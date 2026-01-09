# Headphone HiFi Software Setup (Windows + foobar2000 + WASAPI Exclusive + FIR Convolution EQ + DSP + Hotkeys)

If you own **any headphone that appears in the list below**, welcome to HiFi — you’re one setup away from **reference-grade audio** (for *your* specific headphone).

AutoEQ headphone list:  
https://github.com/jaakkopasanen/AutoEq/tree/master/results

What we are doing:

- Bypassing the Windows audio mixer (no enhancements, no forced resampling)
- Applying **headphone-specific correction** using FIR convolution (`.wav` impulse responses)
- Optionally adding comfort and usability DSP (crossfeed, loudness matching)
- Enabling **instant profile switching** via hotkeys
- Optionally generating **custom FIR profiles** (Reference / Bass / V-shaped)

---

## Step 1) Get real audio files (don’t sabotage the chain)

MP3 is a **lossy** format. With resolving headphones, compression artifacts become obvious:
- smeared transients
- grainy cymbals
- collapsed soundstage

### Recommended options
- Buy/download **FLAC** (Bandcamp, Qobuz Store, HDtracks)
- Rip your own CDs to FLAC (Exact Audio Copy on Windows)
- Streaming (device/driver dependent): Qobuz, TIDAL, Apple Music Lossless
- Searching for freely available FLAC mirrors often surfaces archival/legal sources (use judgement)

Download a few reference tracks you know well — you’ll reuse them during setup.

### What this achieves
You eliminate codec artifacts **before** doing any DSP work. DSP can correct tonality and phase — it cannot restore information removed by lossy compression.

---

## Step 2) Understand the signal chain (important context)

```text
Audio file (native sample rate)
→ foobar2000
→ FIR Convolver (headphone correction profile)
→ (optional) Gain / Amplifier DSP
→ (optional) Crossfeed
→ (optional) Limiter
→ WASAPI Exclusive output
→ DAC / sound card / amp
→ Headphones
```

### Why this achieves “phase-corrected” correction

Most traditional EQs (graphic / parametric) are **IIR / minimum-phase**:
- They alter frequency response
- They also introduce frequency-dependent **phase shift** (timing distortion)

FIR convolution can be **linear-phase**:
- Frequency response is corrected
- Timing coherence across the band is preserved

Subjectively:
- Cleaner transients
- More stable imaging
- Less listening fatigue

**Important nuance**
- FIR corrects what is representable by the measurement/target
- It cannot remove physical driver distortion or mechanical limits

---

# PART A — FOOBAR2000 SETUP

## Step 3) Install foobar2000

Download:  
https://www.foobar2000.org/download

Install normally and launch once to initialise configuration folders.

Optional:
- Set foobar2000 as the default Windows music player for FLAC files

### What this achieves
foobar2000 gives you a **transparent, modular, bit-perfect playback engine** with deterministic DSP ordering — unlike most consumer media players.

---

## Step 4) Configure output: WASAPI (Exclusive)

### 4.1 Install WASAPI output support (if missing)

Component (exclusive-capable):  
https://www.foobar2000.org/components/view/foo_out_wasapi

Install:
1. `File → Preferences → Components → Install…`
2. Select the component file
3. Restart foobar2000

> Shared-mode alternative (not recommended here):  
> https://www.foobar2000.org/components/view/foo_out_wasapis

### What this achieves
Adds low-level Windows audio interfaces that allow bypassing the system mixer.

---

### 4.2 Select WASAPI Exclusive device

1. `File → Preferences → Playback → Output`
2. Device: `WASAPI (exclusive) : <your DAC>`
3. Buffer: default (increase if crackles occur)

### What this achieves
- Windows mixer bypassed
- No system DSP, enhancements, or resampling
- Playback timing controlled entirely by foobar + driver
- Prevents other applications from interfering

---

## Step 5) Install FIR Convolver (Stereo Convolution DSP)

Component:  
https://foobar.hyv.fi/?view=foo_dsp_stereoconv

Reference:  
https://wiki.hydrogenaudio.org/index.php?title=Foobar2000%3AComponents%2FStereo_Convolution_DSP_%28foo_dsp_stereoconv%29

Install:
1. Download component
2. `File → Preferences → Components → Install…`
3. Restart foobar2000

### What this achieves
Allows loading **FIR impulse-response WAV files** that encode frequency *and* phase correction in one operation.

---

## Step 6) Obtain FIR correction WAVs (AutoEQ)

AutoEQ results:  
https://github.com/jaakkopasanen/AutoEq/tree/master/results

### 6.1 Locate your headphone

```text
results/<Brand>/<Model>/convolution/
```

### 6.2 Download the correct FIR

- `44.1kHz.wav` → music libraries
- `48kHz.wav` → video / gaming

Store locally, e.g.:

```text
C:\Audio\FIR\<Brand>\<Model>\
```

### What this achieves
You now have a **pre-computed, headphone-specific correction filter** designed to align your headphone with a known reference target.

---

## Step 7) Enable Convolver and load FIR

1. `File → Preferences → Playback → DSP Manager`
2. Move **Stereo Convolution DSP** to *Active DSPs*
3. Configure → load FIR `.wav`
4. Gain: **0 dB**

Verification:
- Toggle DSP on/off → audible tonal change

### What this achieves
Your headphone is now **corrected at the system level** — not per-track, not per-app.

---

## Step 8) Optional: Add post-EQ gain (recommended for bass/V profiles)

### 8.1 Install Amplifier DSP

Component:  
https://foobar.hyv.fi/?view=foo_dsp_amp

Install via Components → Install, restart foobar.

### 8.2 DSP order

```text
Stereo Convolution DSP
→ Amplifier DSP
```

Start at **+6 dB**, loudness-match by ear.

### What this achieves
Restores loudness lost to EQ headroom *without* altering frequency or phase response.

---

## Step 9) Optional: Add Crossfeed (very light)

Component:  
https://www.foobar2000.org/components/view/foo_dsp_meiercf

DSP order:

```text
Convolver
→ Amplifier (optional)
→ Crossfeed
```

### What this achieves
Reduces extreme left/right separation, lowering fatigue during long listening sessions.

---

## Step 10) Optional: Add Limiter (safety)

If boosting gain significantly:

```text
Convolver
→ Amplifier
→ Crossfeed
→ Limiter (last)
```

Components index:  
https://www.foobar2000.org/components

### What this achieves
Prevents digital clipping during aggressive EQ or loud passages.

---

# PART B — PRESETS AND HOTKEYS

## Step 11) Create FIR profile folders

```text
C:\Audio\FIR\Profiles\
  Reference\
  Bass\
  V\
```

### What this achieves
Keeps FIR profiles organised and repeatable across reinstalls.

---

## Step 12) Create DSP preset: Reference

1. Load Reference FIR in Convolver
2. Set Amplifier gain baseline
3. `Save preset…`
4. Name: `Headphone – Reference`

### What this achieves
Locks a known-good **baseline correction** that you can always return to.

---

## Step 13) Create DSP preset: Bass

1. Load Bass FIR (See part C below on how to create this)
2. Increase Amplifier gain until loudness matches Reference
3. Save preset: `Headphone – Bass`

### What this achieves
Adds low-end energy without breaking phase coherence or clipping.

---

## Step 14) Create DSP preset: V

1. Load V-profile FIR or whatever other profile you wish yto create(See part C below on how to create this)
2. Loudness-match
3. Save preset: `Headphone – V`

### What this achieves
Creates an energetic, fun tuning while preserving correction fundamentals.

---

## Step 15) Bind hotkeys

1. `File → Preferences → Keyboard Shortcuts`
2. Add → `Playback → DSP → Load DSP preset`

Suggested:
- `Ctrl + 1` → Reference
- `Ctrl + 2` → Bass
- `Ctrl + 3` → V

### What this achieves
Instant, blind A/B switching without breaking listening flow.

---

# PART C — OPTIONAL: FIR GENERATION (Python)

## Step 16) Folder structure

```text
C:\Audio\AutoEQ\
  measurements\
  targets\
  output\
```

### What this achieves
Separates raw data, targets, and generated filters cleanly.

---

## Step 17) Get measurement CSV

From AutoEQ:
```text
results/<Brand>/<Model>/measurements/
```

Place into:
```text
C:\Audio\AutoEQ\measurements\
```

### What this achieves
Provides the raw frequency response data needed to compute correction.

---

## Step 18) Target CSV (Harman Over-Ear 2018)

Place into:
```text
C:\Audio\AutoEQ\targets\Harman over-ear 2018.csv
```

### What this achieves
Defines the *reference tonal balance* your headphone will be aligned to.

---

## Step 19) Python environment

```powershell
cd C:\Audio\AutoEQ
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip setuptools wheel
pip install autoeq
python -m autoeq --help
```

### What this achieves
Creates an isolated, reproducible environment for FIR generation.

---

## Step 20) Generate FIR profiles

### Reference
```powershell
python -m autoeq `
 --input-file measurements\YourHeadphone.csv `
 --output-dir output\Reference `
 --target targets\Harman over-ear 2018.csv `
 --convolution-eq `
 --fs 44100 `
 --preamp -6
```

### Bass
```powershell
python -m autoeq `
 --input-file measurements\YourHeadphone.csv `
 --output-dir output\Bass `
 --target targets\Harman over-ear 2018.csv `
 --convolution-eq `
 --fs 44100 `
 --preamp -10 `
 --bass-boost 7
```

### V
```powershell
python -m autoeq `
 --input-file measurements\YourHeadphone.csv `
 --output-dir output\V `
 --target targets\Harman over-ear 2018.csv `
 --convolution-eq `
 --fs 44100 `
 --preamp -8 `
 --bass-boost 4 `
 --treble-boost 4
```

Copy generated WAVs into:

```text
C:\Audio\FIR\Profiles\
```

### What this achieves
Produces **bespoke FIR correction filters** tailored exactly to your headphone and preferences.

---

## Step 21) Sample rate rules

- Do not force 192/384 kHz
- Keep native rates
- Match FIR to content sample rate

### What this achieves
Avoids unnecessary resampling and preserves signal integrity.

---


## Step 22) Double check your analogue setup.

Assuming you're running high-grade headphones
Ensure your DAC + AMP are clean-powered and isolated
  -This means ideally a dedicated sound card or hardware setup

Note: Your amp, depending on build can add flavour that isn't corrected in this setup.
I would recommend getting a "linear classA/B" amp if you want to experience purity (I'll tell you its euphoric!)
But people enjoy many of the flavours amps can add (vaccume tubes!)

Also general tips:
- Keep digital wires long and analogue cables shorter.
- If you keep software and analogue volumes seperate, explore what such gain staging does to the response.
- I recomend keep your software volume max/high and your analogue as the primary controller if you want purity.
- Or the other way around, if you like a little 'smeared bass' effect.

## Step23) ENJOY!

You are now listening to true reference-grade audio.
Everything from here is preference.

---

## Links summary

- foobar2000: https://www.foobar2000.org/download
- Components: https://www.foobar2000.org/components
- WASAPI exclusive: https://www.foobar2000.org/components/view/foo_out_wasapi
- Convolver: https://foobar.hyv.fi/?view=foo_dsp_stereoconv
- Amplifier: https://foobar.hyv.fi/?view=foo_dsp_amp
- Crossfeed: https://www.foobar2000.org/components/view/foo_dsp_meiercf
- AutoEQ: https://github.com/jaakkopasanen/AutoEq/tree/master/results
