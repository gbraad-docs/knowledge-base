# Pure Data

Pure Data (Pd) is an open-source visual dataflow programming environment for audio, video, and interactive media. It was created by Miller Puckette - the same person who wrote Max/MSP - and is the free, open-source counterpart to that commercial tool.

## Flavours

| Distribution | Notes |
|-------------|-------|
| **Pd-vanilla** | Reference implementation by Miller Puckette. Minimal, stable. |
| **Pd-extended** | Older bundle with many externals pre-installed. No longer maintained. |
| **Pd-l2ork / Purr Data** | Fork with updated UI, actively maintained. |

For most purposes start with **Pd-vanilla** and add externals as needed.

```bash
# Install
sudo apt install puredata          # Debian/Ubuntu
brew install pd                    # macOS
```

## Core concepts

A Pd program is called a **patch** (`.pd` file). Everything in a patch is one of:

| Element | Description |
|---------|-------------|
| **Object** `[name arg]` | Does computation - has inlets (top) and outlets (bottom) |
| **Message** `[value(` | Sends a value when clicked or triggered |
| **Number** `[0]` | Displays and sends a number |
| **Symbol** `[symbol]` | Displays and sends a symbol (text) |
| **Comment** | Documentation only |

**Patch cords** connect an outlet of one object to an inlet of another. Data flows downward.

### Control vs signal domain

Pd has two parallel domains:

| Domain | Objects | Rate | Used for |
|--------|---------|------|---------|
| Control | `metro`, `counter`, `select` | Event-driven | Sequencing, logic, parameters |
| Signal | `osc~`, `dac~`, `*~` | Audio rate (e.g. 44100 Hz) | Audio generation and processing |

Signal objects are suffixed with `~`. You cannot connect a signal outlet to a control inlet directly - use `snapshot~` or `sig~` to cross between them.

## Essential objects

### Audio generation

```
[osc~ 440]      - sine oscillator at 440 Hz
[phasor~ 440]   - sawtooth wave (0..1 ramp)
[noise~]        - white noise
[sig~ 0.5]      - convert control value to signal (DC)
```

### Audio processing

```
[*~ 0.5]        - multiply signal (amplitude scaling)
[+~ ]           - add two signals
[bp~ 1000 10]   - bandpass filter (freq, Q)
[lop~ 500]      - low-pass filter (one-pole)
[hip~ 100]      - high-pass filter (one-pole)
[vcf~ ]         - voltage-controlled filter
[delwrite~ buf 1000]  - write to delay buffer (name, max ms)
[delread~ buf 100]    - read from delay buffer (name, delay ms)
[rev3~]         - reverb
```

### Envelopes

```
[line~]         - ramp generator: send [target time( message
[vline~]        - high-precision line~
[env~]          - amplitude follower (RMS envelope)
[adsr~ 10 100 0.7 200]  - ADSR envelope (attack/decay/sustain/release ms)
```

### Control flow

```
[metro 500]     - send bang every 500 ms
[delay 200]     - delay a bang by 200 ms
[timer]         - measure time between bangs
[counter 0 7]   - count bangs from 0 to 7, wrap
[select 1 2 3]  - route bang to outlet matching value
[route float symbol]  - route by type
[trigger b f]   - fan out in right-to-left order (alias: [t b f])
[pack f f]      - combine values into a list
[unpack f f]    - split a list into values
```

### I/O

```
[dac~]          - audio output (left, right inlets)
[adc~]          - audio input (left, right outlets)
[print]         - print to console
[receive name]  / [r name]  - receive messages by name (like a bus)
[send name]     / [s name]  - send messages by name
```

## A minimal synthesizer patch

```
[keyboard]              ← MIDI note input (requires extra)

[notein]                ← MIDI note in (note, velocity, channel)
|         |
[mtof]    [/ 127]       ← MIDI to Hz, velocity 0..1
|         |
[osc~ ]   |
|         |
[*~      ]              ← scale amplitude by velocity
|
[dac~]                  ← to speakers
```

In text form (how the .pd file looks):

```
#X obj 100 100 notein;
#X obj 100 150 mtof;
#X obj 200 150 / 127;
#X obj 100 200 osc~;
#X obj 100 250 *~;
#X obj 100 300 dac~;
#X connect 0 0 1 0;
#X connect 0 1 2 0;
#X connect 1 0 3 0;
#X connect 3 0 4 0;
#X connect 2 0 4 1;
#X connect 4 0 5 0;
#X connect 4 0 5 1;
```

## Running from the command line

```bash
# Open a patch with GUI
pd mypatch.pd

# Run headless (no GUI) - useful for servers or embedded
pd -nogui -open mypatch.pd

# Specify audio device and sample rate
pd -audiodev 2 -r 48000 mypatch.pd

# List available audio devices
pd -listdev
```

## Subpatches and abstractions

```
[pd subpatch-name]  - inline subpatch (double-click to open)
[myabstraction]     - load myabstraction.pd as a reusable object
```

Abstractions use `[inlet]`/`[outlet]` (control) or `[inlet~]`/`[outlet~]` (signal) to define their ports. Store them in the same directory as the main patch or in Pd's search path.

## Externals

The ecosystem of third-party objects extends Pd significantly:

| Library | Contents |
|---------|---------|
| **ELSE** | Large collection of modern objects (recommended) |
| **Cyclone** | Max/MSP compatibility objects |
| **Zexy** | Utility and DSP objects |
| **GEM** | Graphics and video |
| **Faust** | Compile Faust DSP code into Pd objects |

Install via Pd's built-in package manager: `Help → Find externals`.

## Resources

- [Pd documentation](https://puredata.info/docs)
- [The Programming Language PURE DATA (Kreidler)](http://www.pd-tutorial.com/)
- [Pd vanilla releases](http://msp.ucsd.edu/software.html)
- [ELSE library](https://github.com/porres/pd-else)
