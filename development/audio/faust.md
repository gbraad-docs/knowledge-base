# Faust

Faust (Functional Audio Stream) is a domain-specific language for audio signal processing developed at GRAME (Centre National de Création Musicale) in Lyon, starting around 2002. It occupies a unique position in the audio programming landscape: like Csound it is text-based and mathematically expressive, but like hvcc/Pure Data it compiles to self-contained C/C++ with no runtime - making it deployable on everything from web browsers to ARM Cortex-M4 microcontrollers.

The central idea is **block diagram algebra**: you describe a signal processor as a composition of simpler processors using a small set of operators, and the compiler works out the efficient C implementation automatically.

## Installation

```bash
sudo apt install faust                  # Debian/Ubuntu
brew install faust                      # macOS

# Or get the latest with all tools
git clone https://github.com/grame-cncm/faust.git
cd faust && make && sudo make install
```

The online IDE requires no installation: [faustide.grame.fr](https://faustide.grame.fr)

## Core concept: block diagram algebra

In Faust, everything is a **block** - a function that takes N input signals and produces M output signals. Blocks are composed with five operators:

| Operator | Symbol | Meaning | Example |
|----------|--------|---------|---------|
| Sequential | `:` | Output of A feeds input of B | `A : B` |
| Parallel | `,` | A and B run side by side | `A , B` |
| Split | `<:` | One output fans out to many inputs | `A <: B , C` |
| Merge | `:>` | Many outputs sum into fewer inputs | `A , B :> C` |
| Recursive | `~` | Feed output of B back to input of A | `A ~ B` |

The entire language is built from these five operators plus a set of primitives. There are no loops, no mutable state - feedback is expressed explicitly with `~`.

## Minimal example

```faust
process = _;   // identity: one input, one output (wire)
process = !;   // cut: discard input, produce nothing
process = 0;   // constant: produce the value 0
```

A sine oscillator at 440 Hz:

```faust
import("stdfaust.lib");

process = os.osc(440);
```

Stereo output (duplicate the mono signal):

```faust
import("stdfaust.lib");

process = os.osc(440) <: _, _;   // split to left and right
```

## Primitives

### Math

```faust
+, -, *, /          // arithmetic (infix: two inputs → one output)
%                   // modulo
^                   // power
&, |, xor          // bitwise
<<, >>              // bit shift
<, <=, >, >=, ==, !=  // comparison (output: 0 or 1)
abs, floor, ceil, round
sin, cos, tan, asin, acos, atan, atan2
exp, log, log10, sqrt, pow
min, max
```

### Signal primitives

```faust
_           // identity (wire)
!           // cut (terminate a signal)
mem         // one-sample delay
@           // variable delay: x @ d  reads x delayed by d samples
rdtable     // read a table
rwtable     // read/write table
select2     // if/else: select2(cond, a, b)
select3     // three-way selector
```

### Conversions

```faust
ba.samp2sec(n)      // samples to seconds
ba.sec2samp(s)      // seconds to samples
ma.SR               // current sample rate
ma.T                // sample period (1/SR)
```

## UI elements

UI elements define parameters that the host (DAW, hardware, web app) can control. They produce a signal - a stream of values reflecting the current setting.

```faust
hslider("name", default, min, max, step)   // horizontal slider
vslider("name", default, min, max, step)   // vertical slider
nentry("name", default, min, max, step)    // numeric entry
button("name")                             // momentary (0 or 1)
checkbox("name")                           // toggle (0 or 1)
```

Grouping (for UI layout):

```faust
hgroup("Group Name", ...)    // horizontal group
vgroup("Group Name", ...)    // vertical group
tgroup("Tab Name", ...)      // tabbed group
```

Example - a tunable oscillator with volume:

```faust
import("stdfaust.lib");

freq = hslider("freq [unit:Hz]", 440, 20, 20000, 0.1);
gain = hslider("gain", 0.5, 0, 1, 0.01);

process = os.osc(freq) * gain <: _, _;
```

## The standard library

`stdfaust.lib` imports all standard libraries. Key namespaces:

| Namespace | Contents |
|-----------|---------|
| `os` | Oscillators: `osc`, `sawtooth`, `square`, `triangle`, `phasor` |
| `fi` | Filters: `lowpass`, `highpass`, `bandpass`, `peak_eq`, `resonlp` |
| `ef` | Effects: `echo`, `chorus`, `flanger`, `freeverb`, `zita_rev1` |
| `en` | Envelopes: `adsr`, `asr`, `ar`, `smoothEnvelope` |
| `no` | Noise: `noise`, `pink` |
| `ba` | Basic utilities: `if`, `sAndH`, `impulsify`, `db2linear` |
| `ma` | Math constants and functions: `SR`, `PI`, `EPSILON` |
| `ro` | Routing: `interleave`, `cross`, `hadamard` |
| `si` | Signal utils: `smoo`, `smooth`, `bus`, `block` |
| `pm` | Physical models: strings, waveguides |
| `sp` | Spatialization |
| `dm` | Demo versions of effects with built-in UI |

## Common patterns

### Envelope

```faust
import("stdfaust.lib");

gate   = button("gate");
attack = hslider("attack",  0.01, 0.001, 2, 0.001);
decay  = hslider("decay",   0.1,  0.001, 2, 0.001);
sustain= hslider("sustain", 0.8,  0,     1, 0.01);
release= hslider("release", 0.2,  0.001, 4, 0.001);

env    = en.adsr(attack, decay, sustain, release, gate);
process = os.osc(440) * env <: _, _;
```

### Filter

```faust
import("stdfaust.lib");

cutoff = hslider("cutoff [unit:Hz]", 1000, 20, 20000, 1);
res    = hslider("resonance", 0.5, 0, 1, 0.01);

// Noise through a resonant low-pass filter
process = no.noise : fi.resonlp(cutoff, res, 1) <: _, _;
```

### Feedback delay (recursive)

```faust
import("stdfaust.lib");

// feedback: output is delayed and added back to input
// ~ is the recursive operator
delay_samples = int(hslider("delay [unit:ms]", 250, 1, 2000, 1) * ma.SR / 1000);
feedback      = hslider("feedback", 0.5, 0, 0.99, 0.01);

echo = _ <: _, (@ (delay_samples) * feedback) :> _;
process = echo <: _, _;
```

### FM synthesis

```faust
import("stdfaust.lib");

freq  = hslider("freq",  440, 20, 8000, 0.1);
ratio = hslider("ratio", 2,   0.1, 10,  0.01);
depth = hslider("depth", 200, 0,   5000, 1);

modulator = os.osc(freq * ratio) * depth;
carrier   = os.osc(freq + modulator);

process = carrier * 0.5 <: _, _;
```

### Polyphony

Faust supports built-in polyphony via special parameter names. Declare `freq`, `gain`, and `gate` and compile with `-nvoices N`:

```faust
import("stdfaust.lib");

freq = hslider("freq",   440, 20, 20000, 0.1);
gain = hslider("gain",   0.5, 0,  1,     0.01);
gate = button("gate");

env  = en.adsr(0.01, 0.1, 0.8, 0.2, gate);
process = os.sawtooth(freq) * env * gain <: _, _;
```

```bash
faust2juce -nvoices 8 mysynth.dsp   # 8-voice polyphonic VST/AU
```

## Compilation targets

Faust compiles via **architecture files** - wrappers that connect the generated DSP code to a specific host API. The `faust2*` scripts bundle generation + compilation:

| Command | Output |
|---------|--------|
| `faust2c myfile.dsp` | Standalone C file |
| `faust2juce myfile.dsp` | JUCE project (VST/AU/standalone) |
| `faust2vst myfile.dsp` | VST2 plugin |
| `faust2lv2 myfile.dsp` | LV2 plugin (Linux) |
| `faust2jack myfile.dsp` | JACK audio application |
| `faust2alsa myfile.dsp` | ALSA application |
| `faust2webaudio myfile.dsp` | Web Audio API (JavaScript) |
| `faust2wasm myfile.dsp` | WebAssembly module |
| `faust2pd myfile.dsp` | Pure Data external |
| `faust2logue myfile.dsp` | KORG Logue SDK unit |
| `faust2teensy myfile.dsp` | Teensy audio library |

## Logue SDK (NTS-1 / minilogue xd / prologue)

Faust compiles directly to Logue oscillator units - no intermediate step required (unlike the Pd→hvcc→Logue pipeline):

```bash
# Install dependencies
sudo apt install faust gcc-arm-none-eabi

# Clone logue-sdk alongside your Faust file
git clone https://github.com/korginc/logue-sdk.git

# Compile for NTS-1 (nutekt-digital)
faust2logue -nvoices 1 myosc.dsp

# Output: myosc.ntkdigunit  (or .prlgunit / .mnlgxdunit)
```

The `faust2logue` script handles:
- ARM Cortex-M4 compilation (`-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard`)
- Memory layout within the 32 KB SRAM constraint
- Mapping Faust UI sliders → Logue knob parameters
- Wrapping the DSP code in `OSC_INIT`, `OSC_CYCLE`, `OSC_PARAM`

Parameter mapping: Faust sliders named `freq`, `gain`, `gate` are automatically bound to the corresponding Logue signals; other sliders become assignable parameters.

```faust
import("stdfaust.lib");

// These names are special - bound automatically by faust2logue
freq = nentry("freq", 440, 20, 20000, 1);
gain = nentry("gain", 0.5, 0, 1, 0.01);

// Custom parameters appear as knobs in the Logue menu
shape = hslider("shape", 0, 0, 1, 0.01);

osc = os.sawtooth(freq) * (1 - shape) + os.osc(freq) * shape;

process = osc * gain;
```

## Comparing Faust, Csound, and Pure Data

| | Faust | Csound | Pure Data |
|--|-------|--------|-----------|
| **Paradigm** | Functional / algebraic | Orchestra + score | Visual dataflow |
| **Interface** | Text | Text | Graphical |
| **Output** | Compiled C - no runtime | Interpreter + runtime | Interpreted (or via hvcc) |
| **Embedded targets** | Yes (Logue, Teensy, bare-metal) | No (runtime too large) | Yes (via hvcc) |
| **Rate system** | Implicit (compiler infers) | Explicit (i/k/a-rate) | Implicit (control vs `~`) |
| **Feedback** | Explicit with `~` | Implicit in instrument | Patch cords with `~` |
| **Polyphony** | Built-in (`-nvoices N`) | Instruments + score | Manual (via `poly~`) |
| **Library size** | Large standard library | 1500+ opcodes | Small core + externals |
| **Live patching** | No (recompile) | Limited | Yes |
| **Learning curve** | Algebraic thinking required | Steep | Visual, intuitive |

Faust and Csound share a mathematical, declarative approach - if you are comfortable with Csound's signal flow thinking, Faust will feel familiar. The key difference is that Faust's block diagram model is more restrictive (everything must be statically composable) but that restriction is what allows the compiler to produce efficient, allocation-free code.

## Resources

- [Faust website](https://faust.grame.fr/)
- [Faust online IDE](https://faustide.grame.fr)
- [Faust documentation](https://faustdoc.grame.fr/)
- [Faust standard libraries](https://faustlibraries.grame.fr/)
- [Faust on GitHub](https://github.com/grame-cncm/faust)
- [faust2logue](https://github.com/grame-cncm/faust/tree/master-dev/architecture/logue)
- [Faust examples](https://faustdoc.grame.fr/examples/)
