# Schwung — Custom Modules for Ableton Move

Schwung (formerly Move Everything) is an unofficial framework for running custom instruments, effects, and controllers on the **Ableton Move** hardware. It adds a Shadow UI that runs alongside stock Move firmware, allowing additional synths, FX, and tools to run in parallel with the normal Move interface.

> **Disclaimer:** Not endorsed or supported by Ableton. Modifying Move firmware carries risk — back up your sets and know how to use DFU restore mode before installing.

## How it works

Schwung injects itself via `LD_PRELOAD`. The `schwung-shim.so` library intercepts the Move process and launches the Schwung host, which manages the Shadow UI and loads modules. Move itself continues to run normally.

Access the Shadow UI with **Shift+Vol+Track** (and +Menu). Overtake modules use **Shift+Vol+Jog click**.

A web interface is available at `schwung.local` for managing modules, files, and settings.

## Installation

```bash
# Via curl (Move must be on same WiFi network)
curl -L https://raw.githubusercontent.com/charlesvestal/schwung/main/scripts/install.sh | sh

# Or use the desktop installer (macOS/Windows/Linux GUI)
# https://github.com/charlesvestal/schwung-installer/releases/latest
```

Uninstall:

```bash
curl -L https://raw.githubusercontent.com/charlesvestal/schwung/main/scripts/uninstall.sh | sh
```

## Modes

| Mode | Access | Description |
|------|--------|-------------|
| **Shadow UI** | Shift+Vol+Track | Custom signal chains alongside stock Move |
| **Overtake** | Shift+Vol+Jog click | Full-screen modules that take over the UI |
| **Quantized Sampler** | Shift+Sample | Record to `Samples/Schwung/Resampler/` |
| **Skipback** | Shift+Capture | Last 30 seconds to `Samples/Schwung/Skipback/` |
| **Schwung Manager** | `schwung.local` | Web UI for modules, files, settings, mirroring |

## Module types

| Type | Install path | Examples |
|------|-------------|---------|
| `sound_generator` | `modules/sound_generators/<id>/` | Dexed, Surge XT, Braids, OB-Xd |
| `audio_fx` | `modules/audio_fx/<id>/` | CloudSeed, CHOWTape, NAM, Vocoder |
| `midi_fx` | `modules/midi_fx/<id>/` | Super Arp, Eucalypso, Genera |
| `overtake` | `modules/overtake/<id>/` | Performance FX, M8 LPP Emulator |
| `tool` | `modules/tools/<id>/` | AutoSample, Wave Edit, Time Stretch, Stems |

## Hardware target

Ableton Move runs on **ARM64 (aarch64) Linux** with `glibc`. All modules must be cross-compiled for this target.

- Audio: 44.1 kHz, 128-sample blocks (~3ms latency)
- Modules loaded via `dlopen()` as shared `.so` files

## Plugin API v2

All new modules use API v2, which supports multiple instances and is required for Signal Chain integration.

```c
#include "host/plugin_api_v2.h"

typedef struct plugin_api_v2 {
    uint32_t api_version;    // must be 2
    void* (*create_instance)(const char *module_dir, const char *json_defaults);
    void  (*destroy_instance)(void *instance);
    void  (*on_midi)(void *instance, const uint8_t *msg, int len, int source);
    void  (*set_param)(void *instance, const char *key, const char *val);
    int   (*get_param)(void *instance, const char *key, char *buf, int buf_len);
    void  (*render_block)(void *instance, int16_t *out_lr, int frames);
} plugin_api_v2_t;

// Export this from your shared library
extern "C" plugin_api_v2_t* move_plugin_init_v2(const host_api_v1_t *host);
```

`render_block` is called every ~3ms with 128 stereo `int16` frames. `set_param` / `get_param` pass string key-value pairs for parameter communication with the JS UI.

## Building a module

Cross-compilation for ARM64 is done via Docker. Each module has a `scripts/build.sh`:

```bash
./scripts/build.sh       # Docker cross-compile (macOS/Windows/Linux)
./scripts/install.sh     # Deploy to Move over SSH
```

Manual cross-compile (Ubuntu/Debian):

```bash
# Install toolchain
sudo apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu

# Compile shared library
aarch64-linux-gnu-g++ -O3 -shared -fPIC \
    src/dsp/my_plugin.cpp \
    -o build/modules/my_module/dsp.so \
    -lm

# Deploy
scp build/modules/my_module/dsp.so ableton@move.local:~/schwung/modules/audio_fx/my_module/
```

## module.json

Every module has a `src/module.json` that defines its identity and parameters:

```json
{
  "id": "my_effect",
  "name": "My Effect",
  "version": "0.1.0",
  "component_type": "audio_fx",
  "parameters": [
    { "key": "mix",    "name": "Mix",    "min": 0, "max": 100, "default": 50 },
    { "key": "gain",   "name": "Gain",   "min": 0, "max": 200, "default": 100 }
  ]
}
```

`component_type` determines the install path. Parameters defined here are exposed automatically to the Shadow UI knob pages.

## Standalone modules

Standalone modules take full control of Move hardware — Move firmware is suspended while the module runs, then restarted on exit. They talk directly to the SPI interface:

```c
// Open the SPI device
int fd = open("/dev/ablspi0.0", O_RDWR);
void *buf = mmap(NULL, 0x1000, PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0);

// Main loop: ~2.9ms per tick at 44.1kHz/128 frames
while (running) {
    // Write MIDI out, display slice, audio out to buf[0..767]
    // Read MIDI in, audio in from buf[2048..2815]
    ioctl(fd, 0xa, 0x300);   // blocking SPI transfer
}
```

SPI buffer layout:

| Offset | Size | Direction | Content |
|--------|------|-----------|---------|
| 0–79 | 80B | Out | MIDI OUT (20 × 4-byte packets) |
| 80–83 | 4B | Out | Display slice number (0=new frame, 1-6=data) |
| 84–255 | 172B | Out | Display slice pixel data |
| 256–767 | 512B | Out | Audio OUT (128 frames × stereo int16) |
| 2048–2303 | 256B | In | MIDI IN (31 × 8-byte events) |
| 2304–2815 | 512B | In | Audio IN (128 frames × stereo int16) |

Display: 128×64 pixels, 1-bit, packed as 8 vertical bands. Each byte encodes 8 vertical pixels (bit 0 = top). Sent in 6 slices of 172 bytes.

See `schwung-standalone-example` for a working template.

## Releasing a module

```bash
# 1. Bump version in src/module.json
# 2. Commit + tag
git tag v0.2.0
git push origin main --tags
# GitHub Actions builds, creates release, updates release.json automatically
```

The Module Store reads `release.json` from each module's repo to discover new versions. The `download_url` points to the release tarball on GitHub.

## Notable modules

| Module | Type | Description |
|--------|------|-------------|
| [Dexed](https://github.com/charlesvestal/schwung-dx7) | Sound generator | 6-operator FM (DX7 .syx banks) |
| [Surge XT](https://github.com/charlesvestal/schwung-surge) | Sound generator | Hybrid synth — wavetable, FM, subtractive |
| [Braids](https://github.com/charlesvestal/schwung-braids) | Sound generator | Mutable Instruments macro oscillator (47 algorithms) |
| [NAM](https://github.com/charlesvestal/schwung-nam) | Audio FX | Neural Amp Modeler — guitar amp emulation |
| [CHOWTape](https://github.com/charlesvestal/schwung-chowtape) | Audio FX | Analog tape with Jiles-Atherton hysteresis |
| [CloudSeed](https://github.com/charlesvestal/schwung-cloudseed) | Audio FX | Algorithmic reverb |
| [DJ Deck](https://github.com/charlesvestal/schwung-dj) | Overtake/tool | Dual-deck CDJ player with timestretch/stems |

## Related projects

- [schwung](https://github.com/charlesvestal/schwung) - Main framework
- [schwung-installer](https://github.com/charlesvestal/schwung-installer) - Desktop installer (macOS/Windows/Linux)
- [schwung-standalone-example](https://github.com/charlesvestal/schwung-standalone-example) - Standalone module template
- [move-anything-control](https://github.com/bobbydigitales/move-anything) - Base techniques Schwung builds on
- Discord: https://discord.gg/GHWaZCC9bQ
