# Csound

Csound is a sound and music computing system with a long lineage: it descends from Music III/IV/V written by Max Mathews at Bell Labs in the 1950s–60s, and was developed by Barry Vercoe at MIT in 1985. It is one of the most powerful and flexible tools for sound synthesis and signal processing, used in music composition, sound design, research, and live performance.

Unlike Pure Data's visual approach, Csound is a **text-based language** with a rich library of over 1500 opcodes.

## Installation

```bash
sudo apt install csound csoundqt        # Debian/Ubuntu
brew install csound                     # macOS
```

Or download from [csound.com](https://csound.com/download.html).

## The unified file format (`.csd`)

A Csound program is typically a single `.csd` file combining all three sections:

```xml
<CsoundSynthesizer>

<CsOptions>
-odac          ; output to DAC (real-time audio)
-d             ; suppress displays
</CsOptions>

<CsInstruments>
; Orchestra - defines instruments and signal flow
sr     = 44100   ; sample rate
ksmps  = 64      ; samples per control block
nchnls = 2       ; number of output channels
0dbfs  = 1       ; full-scale amplitude = 1.0

instr 1
  ifreq = p4     ; pitch from score (4th p-field)
  iamp  = p5     ; amplitude from score (5th p-field)

  aenv  linen iamp, 0.01, p3, 0.1    ; linear envelope
  asig  poscil  aenv, ifreq           ; sine oscillator
        outs    asig, asig            ; stereo output
endin
</CsInstruments>

<CsScore>
; Score - schedules instrument events
;  i  start  dur  freq   amp
   i1  0      1    440    0.5
   i1  1      1    550    0.5
   i1  2      2    660    0.3
e                             ; end of score
</CsScore>

</CsoundSynthesizer>
```

```bash
csound mypatch.csd
```

## Rate system

Csound processes audio at three rates, and opcodes are rate-specific:

| Rate | Prefix | Period | Used for |
|------|--------|--------|---------|
| **i-rate** (init) | `i` | Once at note start | Pitch, duration, fixed values |
| **k-rate** (control) | `k` | Every `ksmps` samples | Envelopes, LFOs, MIDI, slowly-varying |
| **a-rate** (audio) | `a` | Every sample | Audio signals |

A variable's rate is determined by its prefix letter. An opcode that outputs at a-rate has the output variable prefixed `a`.

## p-fields

Each score event passes parameters to the instrument via numbered p-fields:

| p-field | Meaning |
|---------|---------|
| `p1` | Instrument number |
| `p2` | Start time (seconds) |
| `p3` | Duration (seconds) |
| `p4`+ | User-defined (pitch, amplitude, etc.) |

Inside the instrument, `p4`, `p5`, etc. read these values at i-rate.

## Essential opcodes

### Oscillators

```csound
asig  poscil  iamp, ifreq           ; precise sine (table interpolation)
asig  vco2    iamp, ifreq           ; anti-aliased oscillator (saw, square, etc.)
asig  oscil   iamp, ifreq, ifn      ; sine from a function table
asig  noise   iamp, ibandwidth      ; band-limited noise
```

### Envelopes

```csound
aenv  linen   iamp, iatt, idur, idec      ; linear attack/sustain/decay
aenv  adsr    iatt, idec, isus, irel      ; ADSR (0..1, multiply by amp yourself)
aenv  madsr   iatt, idec, isus, irel      ; ADSR with note-off handling
kenv  linseg  0, 0.01, 1, 0.1, 0.7, p3-0.2, 0  ; arbitrary line segments
```

### Filters

```csound
afilt  moogladder  asig, kcutoff, kresonance   ; Moog ladder filter
afilt  butterlp    asig, kcutoff               ; Butterworth low-pass
afilt  butterhp    asig, kcutoff               ; Butterworth high-pass
afilt  tone        asig, kcutoff               ; one-pole low-pass
afilt  bqrez       asig, kcutoff, kQ           ; biquad resonant filter
```

### Effects

```csound
; Delay
adel  vdelay  asig, kdelaytime_ms, imaxdelay_ms

; Reverb
aL, aR  reverbsc  aL, aR, kfeedback, kcutoff

; Chorus / flanger
achorus  chorus  asig, krate, kdepth

; Distortion / waveshaping
adist  distort1  asig, kpregain, kpostgain, kshape1, kshape2
```

### Synthesis techniques

```csound
; FM synthesis
amod  poscil  imodamp, imodfreq
acar  poscil  iamp, icarfreq + amod

; Subtractive (noise through filter)
anoise  noise 1, 0
afilt   moogladder anoise, 800, 0.5

; Additive (sum of partials)
a1    poscil  0.3, ifreq
a2    poscil  0.2, ifreq * 2
a3    poscil  0.1, ifreq * 3
aout  =  a1 + a2 + a3
```

### MIDI input

```csound
; Read MIDI note and velocity
  imidi_note  notnum               ; MIDI note number (0–127)
  imidi_vel   veloc                ; velocity (0–127)
  ifreq       cpsmidi              ; convert note to Hz
  iamp        ampmidi 0dbfs * 0.5  ; scale velocity to amplitude
```

### Real-time control

```csound
; Read a MIDI control change (CC)
kval  ctrl7  1, 7, 0, 1       ; channel 1, CC 7 (volume), range 0..1

; Read from a named software channel (set from host or API)
kval  chnget  "cutoff"

; Output to a channel
       chnset  kval, "output_level"
```

## Running Csound

```bash
# Real-time audio output
csound -odac mypatch.csd

# Render to file
csound -o output.wav mypatch.csd

# Specify sample rate and bit depth
csound -odac -r 48000 --format=float mypatch.csd

# Real-time MIDI input
csound -odac -M0 mypatch.csd          # device 0
csound -odac -Ma mypatch.csd          # all MIDI devices

# Verbose output (useful for debugging)
csound -v mypatch.csd
```

## CsoundQt (IDE)

CsoundQt provides a GUI with a code editor, widgets (knobs, sliders), and a real-time scope. Useful for interactive patches.

```bash
csoundqt
```

## Cabbage

[Cabbage](https://cabbageaudio.com/) wraps Csound instruments as VST/AU plugins, enabling use inside a DAW. Add a `<Cabbage>` section to the `.csd` for widget layout.

## Csound in the browser (WebAssembly)

```html
<script src="https://unpkg.com/@csound/browser/dist/csound.js"></script>
<script>
  const { Csound } = window;
  async function run() {
    const cs = await Csound();
    await cs.setOption("-odac");
    await cs.compileCsdText(`
      <CsoundSynthesizer>
      <CsInstruments>
        sr=44100
        ksmps=128
        nchnls=2
        0dbfs=1
        instr 1
          asig poscil 0.3, 440
          outs asig, asig
        endin
      </CsInstruments>
      <CsScore>
        i1 0 2
      </CsScore>
      </CsoundSynthesizer>
    `);
    await cs.start();
  }
  run();
</script>
```

## Csound vs Pure Data

| | Csound | Pure Data |
|--|--------|-----------|
| **Interface** | Text (code) | Visual (dataflow) |
| **Paradigm** | Orchestra + score | Message passing |
| **Opcode library** | 1500+ built-in | Smaller core, externals |
| **Live patching** | Limited | Yes (rewire while running) |
| **DAW integration** | Via Cabbage (VST) | Via Camomile (VST) |
| **Scripting** | Python API, Lua | Limited |
| **Learning curve** | Steeper | More visual/intuitive |
| **Strength** | Complex synthesis, research | Interactive, generative, live |

## Resources

- [Csound documentation](https://csound.com/docs/)
- [The Canonical Csound Reference Manual](https://csound.com/docs/manual/)
- [Csound FLOSS Manual](https://flossmanual.csound.com/)
- [Csound on GitHub](https://github.com/csound/csound)
- [CsoundQt](https://csoundqt.github.io/)
- [Cabbage](https://cabbageaudio.com/)
