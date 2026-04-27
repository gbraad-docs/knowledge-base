# Drumlogue Units

The Korg drumlogue runs a full ARM Cortex-A7 Linux system, which makes it
fundamentally different from the Cortex-M targets (NTS-1, minilogue xd). Custom
synth units are shared libraries (`.drmlgunit`) loaded at runtime, with access
to the full C standard library, heap allocation, and `libm`. The toolchain is
`arm-linux-gnueabihf-` rather than `arm-none-eabi-`.

See [Logue SDK](logue.md) for a general overview of the SDK and other platforms.

## Unit type

Drumlogue units use `PROJECT_TYPE := synth` and target `k_unit_module_synth`.
Unlike the Cortex-M platforms, which separate oscillators (`osc`), delay effects,
and reverb effects into distinct module types, the drumlogue has a single synth
slot type that handles everything: sound generation, internal state, and MIDI
response.

```makefile
# config.mk
PROJECT := my_drum
PROJECT_TYPE := synth
```

```c
// header.c
const __unit_header unit_header_t unit_header = {
    .target  = UNIT_TARGET_PLATFORM | k_unit_module_synth,
    .api     = UNIT_API_VERSION,
    .dev_id  = 0x4D593130U,   /* 'MY10' — unique per unit */
    .unit_id = 0x01U,
    .version = 0x00010000U,
    .name    = "MyDrum",
    .num_presets = 0,
    .num_params  = 8,
    .params = { ... }
};
```

## File layout

A minimal drumlogue unit project contains five files:

```
my_drum/
├── Makefile      — copy from rs2_drumlogue; references common/ and config.mk
├── config.mk     — PROJECT, PROJECT_TYPE, CSRC, CXXSRC, UINCDIR, ULIBS
├── header.c      — unit_header_t: name, params, dev_id
├── synth.h       — Synth class: Init, Render, NoteOn, GateOn, setParameter, …
└── unit.cc       — SDK callbacks delegating to Synth class instance
```

`unit.cc` is boilerplate that barely changes between projects. Copy it from an
existing unit (e.g. `rs2_drumlogue/unit.cc`) and it delegates every SDK callback
to the `Synth` class defined in `synth.h`.

## Parameters

The drumlogue supports up to 24 parameters in 6 pages of 4. Only `num_params`
of those are active; the rest must be listed as `k_unit_param_type_none`
placeholders to fill the array.

```c
.num_params = 8,
.params = {
    /* Page 1 */
    {0, 100, 0, 80, k_unit_param_type_percent, 0, 0, 0, {"LEVEL"}},
    {0, 5,   0, 0,  k_unit_param_type_none,    0, 0, 0, {"PRESET"}},
    {0, 0,   0, 0,  k_unit_param_type_none,    0, 0, 0, {""}},
    {0, 0,   0, 0,  k_unit_param_type_none,    0, 0, 0, {""}},
    /* Page 2 */
    ...
    /* Pages 3–6: all none placeholders */
}
```

`getParameterStrValue` can return label strings for enum-style parameters (preset
names, drum names, mode names). Return `nullptr` for percent-type parameters
where the runtime displays the integer directly.

## Gate vs Note

This is the key architectural distinction for the drumlogue.

| Source | Callback | Has note number? |
|--------|----------|-----------------|
| Drumlogue internal sequencer | `unit_gate_on(velocity)` | No |
| External MIDI input | `unit_note_on(note, velocity)` | Yes |

The drumlogue's front panel pads and step sequencer drive the **internal analog
drum parts** (BD, SD, LT, HT, CY, HH). When a custom synth unit occupies one
of the four SYN slots, the sequencer fires that slot with a gate signal — no
note number, just velocity. External MIDI can send full note-on messages to the
synth slot via MIDI channel assignment.

Consequences for drum machine units:

- `GateOn(velocity)` should trigger a single, configurable drum sound
- `NoteOn(note, velocity)` can implement full GM note mapping for external MIDI
- A `GATE` parameter (0–N) lets the user select which drum the sequencer fires

```cpp
// In synth.h
inline void GateOn(uint8_t velocity) {
    // trigger whatever drum PARAM_GATE selects
    RS2_Poly* voices[] = { kick_, snare_, ch_, oh_, clap_, tom_ };
    rs2_poly_note_on(voices[gate_drum_], 60, velocity);
}

inline void NoteOn(uint8_t note, uint8_t velocity) {
    // GM drum map for external MIDI
    switch (note) {
    case 36: rs2_poly_note_on(kick_,  60, velocity); break;
    case 38: rs2_poly_note_on(snare_, 60, velocity); break;
    case 39: rs2_poly_note_on(clap_,  60, velocity); break;
    case 42: rs2_poly_note_on(ch_,    60, velocity); break;
    case 46: rs2_poly_note_on(oh_,    60, velocity); break;
    case 50: rs2_poly_note_on(tom_,   60, velocity); break;
    }
}
```

## Memory model

Because drumlogue runs Linux, `malloc`/`free` and C++ `new`/`delete` are fully
available. Heap-allocated engines per voice are normal:

```cpp
RS2_Poly* kick_voice_ = nullptr;

inline int8_t Init(const unit_runtime_desc_t* desc) {
    kick_voice_ = rs2_poly_create((float)desc->samplerate);
    if (!kick_voice_) return k_unit_err_memory;
    ...
}

inline void Teardown() {
    rs2_poly_destroy(kick_voice_);
    kick_voice_ = nullptr;
}
```

VLAs inside `Render()` are available (GCC extension) and used for temporary
audio buffers, but a fixed-size array is safer if the block size is known:

```cpp
fast_inline void Render(float* out, size_t frames) {
    float mix[frames], l[frames], r[frames];   // VLA, GCC only — fine on Linux
    ...
}
```

The Cortex-M SPSC ring buffer pattern for MIDI/render thread safety (see
[Logue SDK](logue.md)) is **not required** on drumlogue — MIDI callbacks and
`unit_render` run in the same Linux process context. Direct state updates in
`NoteOn` are safe.

## Render output format

`unit_render` receives a stereo interleaved `float*` buffer:

```cpp
__unit_callback void unit_render(const float* in, float* out, uint32_t frames) {
    for (uint32_t i = 0; i < frames; i++) {
        float s = generate_sample();
        out[i * 2]     = s;  // left
        out[i * 2 + 1] = s;  // right
    }
}
```

`RS2_Poly::process` outputs split left/right buffers, not interleaved — mix
voices into a mono accumulator, then write to both channels:

```cpp
float mix[frames], l[frames], r[frames];
rs2_poly_process(voice_, l, r, (int)frames);
for (size_t i = 0; i < frames; i++) mix[i] += l[i] * level_;
// ... accumulate other voices ...
for (size_t i = 0; i < frames; i++) {
    out[i * 2] = out[i * 2 + 1] = mix[i];
}
```

## Building

Units are cross-compiled inside a container. The project directory is mounted
into the SDK platform tree at build time:

```bash
podman run --rm \
    -v "$PLATFORM_PATH:/workspace:rw" \
    -v "$PROJECT_DIR:/workspace/drumlogue/my_drum:rw" \
    -v "$RFX_ROOT:/rfx:ro" \
    ghcr.io/gbraad-logue/logue-sdk/dev-env:latest \
    /app/cmd_entry bash -c "
        source /app/drumlogue/environment &&
        cd /workspace/drumlogue/my_drum &&
        make clean SYNTH_PATH=/rfx/synth &&
        make install SYNTH_PATH=/rfx/synth
    "
```

The `SYNTH_PATH` override redirects the default `../../../synth` relative path
to the absolute container path. The resulting `my_drum.drmlgunit` is installed
back into the project directory on the host via the container mount.

A build orchestration script (`build-drum.sh`) wraps this loop for a list of
units and copies outputs to `bin/`:

```bash
bash logue/build-drum.sh rs1_drumlogue rd1_drumlogue rd2_drumlogue
```

## Loading onto the device

Power on the drumlogue while holding the WRITE button to enter USB mass storage
mode. The device mounts as a drive with a `Units/Synths/` directory. Copy
`.drmlgunit` files there, then restart the device. Units load in alphabetical
order and appear in the SYN1–4 slot selection.

## Unit patterns

### Single-voice drum (rg909 pattern)

One synth engine per unit. The unit sounds like one instrument. `GateOn` fires
it; `NoteOn` can optionally vary pitch. Used by `rg909_drum` (TR-909 bass drum).

```cpp
inline void GateOn(uint8_t velocity) {
    rs1_poly_static_note_on(&voice_, 60, velocity);
}
```

### Multi-voice drum machine (rd1/rd2 pattern)

Six independent voices: kick, snare, closed hat, open hat, clap, tom. Each has
a level parameter. `NoteOn` uses GM note mapping; `GateOn` fires whichever drum
`PARAM_GATE` selects.

```
PARAM_KICK_LEVEL  PARAM_SNARE_LEVEL  PARAM_CH_LEVEL  PARAM_OH_LEVEL
PARAM_CLAP_LEVEL  PARAM_TOM_LEVEL    PARAM_MASTER    PARAM_GATE (0-5)
```

`rd1_drumlogue` uses the RS1 engine (`RS1_PolyStatic` per voice).  
`rd2_drumlogue` uses the RS2 engine (`RS2_Poly` per voice), enabling PCM sample
operators alongside the algorithmic resonator/noise operators.

### Polyphonic synth (rs1/rs2 pattern)

One polyphonic engine (4–8 voices internally). `NoteOn`/`NoteOff` handle melody
playing; `GateOn` triggers at a fixed pitch. `PARAM_PRESET` selects from
built-in factory presets.

`rs1_drumlogue` uses `RS1_PolyStatic` with 6 melodic presets (Default, Bell,
Lead, Supersaw, Bass, Pad). `rs2_drumlogue` uses `RS2_Poly` with an algorithmic
resonator preset.

## Ecosystem examples

Several third-party ports demonstrate what the drumlogue runtime can host:

| Project | Engine | Notes |
|---------|--------|-------|
| [Lillian](https://github.com/boochow/eurorack_drumlogue) | Mutable Instruments Braids | 47 wavetable shapes, dual envelopes, FM, VCA — full eurorack port |
| [maxisynth](https://github.com/boochow/maxisynth) | Maximilian audio library | Band-limited oscillator, LP filter, full ADSR — demonstrates C++ library integration |
| [libpd-test](https://github.com/boochow/libpd_drumlogue_test_synth) | libpd (Pure Data) | Runs a `.pd` patch directly on hardware — demonstrates scripting runtime integration |

These show that the Linux runtime allows porting substantial audio codebases
without the severe size and performance constraints of bare-metal Cortex-M.

## Resources

- [Korg logue SDK — drumlogue platform](https://github.com/korginc/logue-sdk/tree/master/platform/drumlogue)
- [Logue SDK overview](logue.md)
- [ARM cross-compilation toolchain](../../linux/arm.md)
