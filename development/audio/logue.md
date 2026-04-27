# Logue SDK

The Korg logue SDK allows developing custom oscillators and effects for Korg's logue-compatible hardware. Units compile to ARM Cortex-M4/M7 bare-metal code - see [ARM](../../linux/arm.md) for toolchain setup.

## Hardware targets

| Platform | Unit extension | CPU | Notes |
|----------|---------------|-----|-------|
| NTS-1 mkI | `.ntkdigunit` | Cortex-M4F | Original pocket synth |
| NTS-1 mkII | `.nts1mkiiunit` | Cortex-M7 | Current pocket synth |
| NTS-3 Kaoss | `.nts3unit` | Cortex-M7 | Primary target |
| minilogue xd | `.mnlgxdunit` | Cortex-M4F | Polyphonic synth |
| prologue | `.prlgunit` | Cortex-M4F | Polyphonic synth |
| [drumlogue](drumlogue.md) | `.drmlgunit` | Cortex-A7 | Drum machine |

## Unit types

| Type | Header | Description |
|------|--------|-------------|
| `osc` | `unit.h` | Custom oscillator / sound generator |
| `modfx` | `unit_modfx.h` | Modulation effect (chorus, flanger) |
| `delfx` | `unit_delfx.h` | Delay effect |
| `revfx` | `unit_revfx.h` | Reverb effect |
| `genericfx` | `unit_genericfx.h` | NTS-3 background effect (always running) |

`genericfx` is NTS-3 Kaoss exclusive. Unlike other effect types, it can process audio continuously in the background regardless of touch pad state.

## GenericFX - NTS-3 background effects

Normal effect units (`modfx`, `delfx`, `revfx`) receive audio via the `in` parameter in `unit_render`, which is routed through the effect on/off state. When the touch pad is not held (and HOLD/XY Freeze is off), the input is bypassed - the effect stops processing.

`genericfx` units can sidestep this by calling `get_raw_input()` through the runtime context, which returns the raw audio input unaffected by touch pad state. The effect runs continuously in the background.

### Storing the runtime descriptor

Save the descriptor passed to `unit_init` - it carries the hooks needed later in `unit_render`:

```cpp
#include "unit_genericfx.h"

static unit_runtime_desc_t s_runtime_desc;

__unit_callback int8_t unit_init(const unit_runtime_desc_t *desc) {
    if (!desc) return k_unit_err_undef;
    if (desc->target != unit_header.common.target) return k_unit_err_target;
    if (!UNIT_API_IS_COMPAT(desc->api)) return k_unit_err_api_version;

    s_runtime_desc = *desc;   // store for get_raw_input() access

    // optional: SDRAM allocation for delay buffers etc.
    if (s_effect_instance.getBufferSize() > 0) {
        float *buf = (float *)desc->hooks.sdram_alloc(
            s_effect_instance.getBufferSize() * sizeof(float));
        if (!buf) return k_unit_err_memory;
        s_effect_instance.init(buf);
    }
    return k_unit_err_none;
}
```

### Background render

```cpp
__unit_callback void unit_render(const float *in, float *out, uint32_t frames) {
    // Cast runtime context to genericfx type to access get_raw_input()
    const unit_runtime_genericfx_context_t *ctxt =
        static_cast<const unit_runtime_genericfx_context_t *>(
            s_runtime_desc.hooks.runtime_context);

    // get_raw_input() bypasses effect on/off routing - always live audio
    const float *raw_input = ctxt->get_raw_input();
    s_effect_instance.process(raw_input, out, frames);
}
```

Using `raw_input` instead of `in` means the effect processes audio continuously without requiring the HOLD button.

| Approach | Input source | Requires HOLD? |
|----------|-------------|----------------|
| Normal effect | `in` parameter | Yes, when pad not touched |
| Background effect | `ctxt->get_raw_input()` | No - always running |

### NTS-3 extras

`genericfx` also receives XY pad events via `unit_touch_event`, not available on other platforms:

```cpp
__unit_callback void unit_touch_event(uint8_t id, uint8_t phase, uint32_t x, uint32_t y) {
    s_effect_instance.touchEvent(id, phase, x, y);
}
```

Memory available: 32 KB SRAM + 3 MB SDRAM (allocated via `desc->hooks.sdram_alloc`).

Template: `logue-sdk/platform/nts-3_kaoss/dummy-genericfx/`

## Callback architecture

The SDK calls MIDI callbacks (`unit_note_on`, `unit_note_off`) from a **different context** than `unit_render`. Doing DSP state changes inside MIDI callbacks races against the audio thread.

**Rule: MIDI callbacks only enqueue events. All DSP work happens in `unit_render`.**

```
unit_note_on  → push to ring buffer only
unit_note_off → push to ring buffer only
unit_render   → drain ring buffer → process events → render audio
```

### SPSC ring buffer pattern

```cpp
enum NoteEventType : uint8_t { EVT_NOTE_ON = 0, EVT_NOTE_OFF = 1, EVT_ALL_NOTE_OFF = 2 };
struct NoteEvent { NoteEventType type; uint8_t note; uint8_t velocity; };

static constexpr int NOTE_QUEUE_SIZE = 16;
NoteEvent note_queue_[NOTE_QUEUE_SIZE];
std::atomic<uint8_t> queue_write_;  // producer: MIDI callbacks
std::atomic<uint8_t> queue_read_;   // consumer: Render()

// Producer (MIDI callback context)
inline void enqueue(NoteEventType type, uint8_t note, uint8_t vel) {
    uint8_t w    = queue_write_.load(std::memory_order_relaxed);
    uint8_t next = (w + 1) % NOTE_QUEUE_SIZE;
    if (next != queue_read_.load(std::memory_order_acquire)) {
        note_queue_[w] = {type, note, vel};
        queue_write_.store(next, std::memory_order_release);
    }
}

// Consumer (render context)
inline void drainNoteQueue() {
    uint8_t w = queue_write_.load(std::memory_order_acquire);
    uint8_t r = queue_read_.load(std::memory_order_relaxed);
    while (r != w) {
        NoteEvent evt = note_queue_[r];
        queue_read_.store((r + 1) % NOTE_QUEUE_SIZE, std::memory_order_release);
        switch (evt.type) {
            case EVT_NOTE_ON:      processNoteOn(evt.note, evt.velocity); break;
            case EVT_NOTE_OFF:     processNoteOff(evt.note);              break;
            case EVT_ALL_NOTE_OFF: processAllNoteOff();                   break;
        }
        r = queue_read_.load(std::memory_order_relaxed);
        w = queue_write_.load(std::memory_order_acquire);
    }
}

fast_inline void Render(float* out, size_t frames) {
    drainNoteQueue();   // always first
    for (size_t i = 0; i < frames; i++) {
        // DSP processing
    }
}
```

### VLA pitfall

`unit_render` receives `frames` as a runtime value. Stack-allocating with a runtime size is a C99 VLA - not valid C++ and dangerous on Cortex-M with limited stack:

```cpp
// WRONG: VLA, not valid C++
float buf[frames * 2];

// CORRECT: fixed max (NTS-1 mkII block size is always ≤ 256 frames)
float buf[256 * 2];
```

## Minimal unit structure

```cpp
#include "unit.h"   // from logue-sdk platform headers

static UnitHeader unit_header = {
    .header_size  = sizeof(UnitHeader),
    .target       = UNIT_TARGET_PLATFORM | k_unit_module_osc,
    .api          = UNIT_API_VERSION,
    .dev_id       = 0x00000000,
    .unit_id      = 0x00000000,
    .version      = 0x00010000,
    .name         = "MyUnit",
    .num_params   = 2,
    .params       = {{ "Param1", 0, 0, 100, k_unit_param_type_none },
                     { "Param2", 0, 0, 100, k_unit_param_type_none }},
};

__unit_callback void unit_init(const UnitRuntimeDesc* desc) { }
__unit_callback void unit_teardown() { }
__unit_callback void unit_reset() { }
__unit_callback void unit_resume() { }
__unit_callback void unit_suspend() { }
__unit_callback void unit_note_on(uint8_t note, uint8_t vel) { /* enqueue only */ }
__unit_callback void unit_note_off(uint8_t note) { /* enqueue only */ }
__unit_callback void unit_all_note_off() { /* enqueue only */ }
__unit_callback void unit_set_param_value(uint8_t id, int32_t val) { }
__unit_callback void unit_render(float* in, float* out, uint32_t frames) {
    drainNoteQueue();
    // process audio
}
```

## Building

```bash
# From an SDK platform directory, e.g. nts-3_kaoss/
cd ~/Projects/logue-sdk/platform/nts-3_kaoss/myunit
make

# Output: myunit.nts3unit
# Install via logue-tool or USB drag-and-drop
```

Toolchain: `arm-none-eabi-` for Cortex-M targets, `arm-linux-gnueabihf-` for drumlogue. See [ARM](../../linux/arm.md) for installation.

## Deployment paths

The same DSP core can target multiple runtimes beyond bare-metal hardware:

| Path | Format | Tool | Use case |
|------|--------|------|----------|
| Hardware | `.nts3unit` / `.nts1mkiiunit` | logue-tool / USB | Production on device |
| ARM Linux host | `.nts3unit` | Regroovelizer | Desktop / Raspberry Pi / Android |
| WebAssembly | `.wasmunit` | Rehost | Browser, cross-platform testing |
| Pure Data → C | `.nts3unit` | hvcc | Pd patches compiled to Logue units |
| Faust → C | `.nts3unit` | faust2logue | Faust DSP compiled to Logue units |

The Regroovelizer loads `.nts3unit` ELF files on ARM Linux by parsing relocations and resolving logue SDK symbols at runtime - allowing the same unit binary to run on desktop for development before flashing to hardware.

## Resources

- [Korg logue SDK](https://github.com/korginc/logue-sdk)
- [logue-tool](https://github.com/korginc/logue-tool) - CLI for flashing units
- [Faust → Logue](https://github.com/grame-cncm/faust/tree/master-dev/architecture/logue) - `faust2logue`
- See [ARM](../../linux/arm.md) for cross-compilation toolchain setup
- See [Faust](faust.md) for generating Logue units from Faust DSP code
