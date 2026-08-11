```
Hexchant Harmonizer
by Spectral Garden
An occult vocal harmonizer, spectral vocoder & text-to-speech chant engine for VST3      
Hexchant Harmonizer turns any voice — live or typed — into layered ritual harmony. It combines an 8-voice harmonizer, a 32-band spectral vocoder, a built-in text-to-speech "spell casting" engine, a Ghost spectral delay, and a Formant Sculptor with tube saturation and cathedral reverb, all wrapped in an animated coven-themed interface.

Table of Contents
For Musicians & Producers
Features
Parameters
Casting Spells (Text-to-Speech)
Installation
System Requirements
Quick Start
For Developers
Architecture Overview
Signal Chain
DSP Internals
UI / Editor
Project Structure
Building from Source
Extending the Plugin
License

For Musicians & Producers
Features
 Text-to-Speech Casting — type any phrase and have it sung/chanted directly into the signal chain
 8-voice Harmonizer — independently pitched, panned, delayed, and vibrato-modulated harmony voices act as the vocoder's carrier
32-band Spectral Vocoder — your voice (or typed text) modulates the harmony carrier for classic-to-alien vocoder textures
 Ghost Engine — a spectral delay that smears and haunts the vocoded signal
 Formant Sculptor — Body/Air/Tilt/Drive controls to reshape vowel character, with selectable 2x/4x/8x oversampling around the drive stage
 Tube Warmth Saturation — analog-style tanh saturation on the master bus
 Cathedral Reverb — a large, dark reverb tail
 6 Voice Types — Male, Female, Androgynous, Whisper, Demonic, Angelic
 4 Speaking Styles — Chant, Whisper, Drone, Sing
 A/B state comparison, Undo/Redo, and a built-in preset browser
 Eco Mode — halves active harmony voices and disables oversampling for lighter CPU use
 Live CPU meter and in/out level meters
 Built-in Help overlay with a full quickstart guide
Parameters
Section
Parameter
UI Name
Range
Description
Voice
voice_type
Voice Type dropdown
Male / Female / Androgynous / Whisper / Demonic / Angelic
Sets the base TTS voice character & pitch
Voice
voice_pitch
PITCH
-12 to +12 st
TTS pitch shift in semitones
Voice
voice_formant
FORMANT
-12 to +12 st
TTS formant/vowel shift
Voice
voice_breath
BREATH
0–100%
Breathiness added to the TTS voice
Voice
voice_whisper
WHISPER
0–100%
Whisper/noise blend on the TTS voice
Voice
voice_style
Speaking Style dropdown
Chant / Whisper / Drone / Sing
Chant & Drone loop continuously; Whisper & Sing are transient
Harmony ×8
harmony_N_active
(voice toggle)
on/off
Enables harmony voice N (voices 1–3 on by default)
Harmony ×8
harmony_N_interval
(voice toggle)
-24 to +24 st
Pitch interval of harmony voice N
Harmony ×8
harmony_N_formant
—
-12 to +12 st
Formant shift of harmony voice N
Harmony ×8
harmony_N_pan
—
-1 to +1
Stereo position of harmony voice N
Harmony ×8
harmony_N_delay
—
0–500 ms
Onset delay of harmony voice N
Harmony ×8
harmony_N_vibrato
—
0–100%
Vibrato depth on harmony voice N
Harmony ×8
harmony_N_ghost
—
0–100%
Ghost engine send amount for harmony voice N
Vocoder
vocoder_smear
SMEAR
0–100%
Spectral smearing across vocoder bands
Vocoder
vocoder_density
DENSITY
0–100%
Vocoder band density/resolution
Vocoder
vocoder_drift
DRIFT
0–100%
Random spectral drift over time
Sculptor
sculpt_body
BODY
-12 to +12 dB
Low/body formant emphasis
Sculptor
sculpt_air
AIR
-12 to +12 dB
High/air formant emphasis
Sculptor
sculpt_tilt
TILT
-1 to +1
Overall spectral tilt
Sculptor
sculpt_drive
DRIVE
0–100%
Saturator drive amount (runs through oversampling)
Master
master_warmth
WARMTH
0–100%
Tube-style tanh saturation on the master bus
Master
master_gate
(internal)
-80 to 0 dB
Noise gate threshold on the live input
Master
master_reverb
REVERB
0–100%
Cathedral reverb send
Master
master_mix
MIX
0–100%
Dry/wet balance
Master
master_gain
GAIN
-24 to +12 dB
Output gain
Global
oversampling
dropdown
Off / 2x / 4x / 8x
Oversampling around the Formant Sculptor/drive stage
Global
eco_mode
Eco Mode
on/off
Reduces active voices to 4 and disables oversampling
Global
bypass
Bypass
on/off
Fully bypasses processing

Casting Spells (Text-to-Speech)
Type a phrase into the scroll box at the top-left (e.g. "HEXCHANT").
Click Cast Vocalization — the built-in synthesizer sings the text using your current Voice Type, Pitch, Formant, Breath, and Whisper settings.
Set Speaking Style to Chant or Drone for a phrase that loops continuously as an ambient background voice; use Whisper or Sing for one-shot phrases.
The typed voice feeds the same vocoder path as live input, so it will be shaped by your Harmony, Vocoder, Sculptor, and Master settings too.
Note: The vocoder always needs a carrier. Your active Harmony voices provide this. If every harmony voice is muted, an automatic fallback blends a bit of the dry source back in so the vocoder never goes silent.
Installation
Download the plugin build for your platform (.vst3 or .component).
Copy it into your system's plugin folder:
Windows (VST3): C:\Program Files\Common Files\VST3\
macOS (VST3): /Library/Audio/Plug-Ins/VST3/
macOS (AU): /Library/Audio/Plug-Ins/Components/
Rescan plugins in your DAW.
Load Hexchant Harmonizer on a vocal or bus track.
System Requirements
A VST3- or AU-compatible DAW (Ableton Live, Logic Pro, Reaper, FL Studio, Cubase, etc.)
macOS 10.13+ or Windows 10+
64-bit host required
Oversampling settings above 2x are CPU-intensive — use Eco Mode on slower machines
Quick Start
Load Hexchant Harmonizer on a vocal track, or route a synth pad into it to serve as the harmony carrier.
Enable 2–3 Harmony voices with different intervals for a chord.
Type a phrase and hit Cast Vocalization to hear it sung through the vocoder.
Turn up Reverb and Warmth for a darker, more atmospheric result.
If CPU usage climbs (see the meter in the top bar), enable Eco Mode.

For Developers
Architecture Overview
Hexchant Harmonizer is a JUCE audio plugin (VST3/AU) with a standard AudioProcessor / AudioProcessorEditor split, plus an AudioProcessorValueTreeState::Listener implementation on the processor itself:
PluginProcessor.h/.cpp — DSP chain, parameter layout, TTS triggering, A/B state management, CPU/level metering
PluginEditor.h/.cpp — Custom LookAndFeel, slider/label layout, help overlay, and branding
Signal Chain
Live Input (L/R)
  │
  ▼
Noise Gate (master_gate threshold)
  │
  ▼
Source Voice = 0.5*(dryL + dryR) + TTS synth output
  │
  ▼
8x Harmony Voices (pitch/formant/pan/delay/vibrato/ghost per voice)
  │  └─ normalized by 1/√(active voice count)
  │
  ▼
Excitation Fallback Blend (harmonies*0.7 + sourceVoice*0.3)
  │
  ▼
32-band Spectral Vocoder
  │  modulator = sourceVoice, carrier = combinedHarmonies
  │  → mono vocoded signal, duplicated to L/R, ×12 makeup gain
  ▼
Ghost Engine (spectral delay; strength halved in Eco Mode)
  │
  ▼
[Optional Oversampling 2x/4x/8x]
  │
  ▼
Formant Sculptor (Body/Air/Tilt/Drive) + Tube Warmth (tanh saturation)
  │
  ▼
[Oversampling downsample back to native rate]
  │
  ▼
Cathedral Reverb (juce::dsp::Reverb, applied only if master_reverb > 0)
  │
  ▼
Dry/Wet Mix (master_mix) × Output Gain (master_gain)
  │
  ▼
Limiter
  │
  ▼
Output (L/R)

DSP Internals
Noise gate — a simple hard gate: if (|L| + |R|) / 2 falls below the master_gate threshold (in linear gain, converted from dB), both channels are zeroed for that sample. This runs on the live input only, before the TTS signal is summed in.
Harmony voices (HarmonyVoice, ×8) — each instance is fed the combined source voice and independently processes pitch interval, formant shift, pan, onset delay, vibrato, and ghost send. Outputs are summed and normalized by 1/√(activeCount) to keep gain roughly constant regardless of how many voices are active.
Excitation fallback — after harmony summing, the code unconditionally blends combinedHarmonies*0.7 + sourceVoice*0.3. This means there is always some carrier energy even with every harmony voice muted, so the vocoder never fully drops out. Worth knowing if you're debugging "why is there sound with all voices off" — this is by design, not a bug.
Vocoder (vocoder.process) — takes the source voice as modulator and the combined harmonies as carrier, producing a single mono channel (duplicated to stereo afterward — the current implementation is not true stereo through the vocoder stage). smear, density, and drift are normalized 0–1 from their 0–100 parameter ranges. Output is boosted ×12 as fixed makeup gain post-vocoding.
Ghost engine (ghost.process) — a spectral delay applied after the vocoder. Its wet amount is hardcoded to 0.65 normally and 0.3 in Eco Mode, independent of any user-facing "ghost mix" parameter (per-voice harmony_N_ghost values feed into the harmony voices themselves, not this stage).
Oversampling — three juce::dsp::Oversampling instances (2x/4x/8x, half-band FIR equiripple) are pre-built in prepareToPlay and selected at runtime based on the oversampling parameter, but only wrap the Formant Sculptor + Warmth saturation stage — the vocoder, harmonizer, and reverb all run at the native sample rate. Eco Mode forces oversamplingSetting to 0 (off) regardless of the stored parameter value.
Formant Sculptor (sculptor.process) — applies Body/Air/Tilt EQ-style shaping and drive; Warmth is applied afterward as tanh(x * (1 + warmth*2)), only if warmth > 0.
Reverb — a standard juce::dsp::Reverb with fixed internal parameters (roomSize=0.85, damping=0.3, wetLevel=0.45, dryLevel=0.55, width=1.0) set once in prepareToPlay. The master_reverb plugin parameter is applied afterward as an additional dry/wet blend against the pre-reverb signal, rather than by adjusting the internal reverb wet/dry — so master_reverb and the reverb object's own wetLevel are two separate blends stacked together. The reverb block is skipped entirely if master_reverb <= 0.
A/B state — stateA/stateB are juce::ValueTree snapshots. toggleAB() swaps the live apvts state with whichever slot isn't currently active; copyAToB()/copyBToA() copy between them without switching.
Metering & CPU tracking — input/output peaks are read via buffer.getMagnitude() before/after processing. CPU usage is computed each block from high-resolution ticks (processing time / block duration * 100), smoothed with a 0.9/0.1 exponential average.
Unused parameter note — master_air ("Exciter Air") is registered in createParameterLayout() but is never read in processBlock(). It's present in the state/automation but currently has no audible effect — worth wiring up or removing.
UI / Editor
HexchantColors — a static palette (voidBlack, moonlitSilver, spectralTeal, bloodRed, ghostGreen) shared across the custom LookAndFeel and editor.
Slider labels are drawn directly in paint() via a drawSliderLabel lambda rather than juce::Label components, positioned relative to each slider's live bounds.
Help overlay — a dedicated component (helpOverlay) toggled by helpButton; when visible it hides the OpenGL visualDisplay to avoid render punch-through, and is brought to front explicitly with toFront(true).
Branding — the plugin name and studio name are drawn in paint() in the unused ~80×60px pocket of the header (to the right of the Bypass/Eco/Help buttons), since no dedicated title Label components exist in this editor.
10Hz timer (startTimer(100)) drives visualDisplay.repaint() for the central visualizer; UI text/meters are otherwise only redrawn on state changes.
Project Structure
HexchantHarmonizer/
├── Source/
│   ├── PluginProcessor.h      # DSP engines, parameter layout, processor interface
│   ├── PluginProcessor.cpp    # processBlock, TTS trigger, A/B state, metering
│   ├── PluginEditor.h         # Editor, LookAndFeel, HexchantColors declarations
│   └── PluginEditor.cpp       # UI painting, layout, help text, branding
├── HexchantHarmonizer.jucer   # (or CMakeLists.txt) JUCE project file
└── README.md

Building from Source
Requirements:
JUCE 7.x or later (uses juce::dsp::Oversampling, juce::dsp::Reverb)
A C++17-compatible compiler (Xcode / MSVC / GCC)
Projucer or CMake, depending on your JUCE project setup
Steps:
Clone or download the repository.
Open the .jucer file in Projucer (or configure via CMakeLists.txt if using CMake).
Set your exporter target (Xcode, Visual Studio, etc.) and generate the project.
Build the VST3 and/or AU target in your IDE.
The built plugin binary will be output to your configured plugin destination folder, or you can manually copy it from the build directory.
Extending the Plugin
Some natural extension points:
Wire up master_air — currently registered but unused in processBlock.
True stereo vocoding — the vocoder currently outputs mono and is duplicated to both channels.
Expose Ghost engine wet amount as a user parameter instead of the hardcoded 0.65/0.3 split.
MIDI-triggered TTS casting or harmony voice control — processBlock currently ignores midiMessages entirely.
Route master_reverb into the juce::dsp::Reverb wet/dry parameters directly rather than stacking a second dry/wet blend on top.

License
Spectral Garden Proprietary License
© 2026 Spectral Garden Studios

You are granted a non-exclusive license to use the Forsaken plugin for personal or commercial audio production.

You may not:
- redistribute the plugin
- modify, decompile, or reverse-engineer the plugin
- sell or repackage the plugin

All rights reserved.

DISCLAIMER — USE AT YOUR OWN RISK  
All VST plugins, software units, and applications provided through Spectral Garden are experimental digital tools. While extreme care and testing have been applied to ensure stable, high‑performance architecture, all downloads and modules are provided “AS IS” without warranty of any kind.

Use of these VST units and software applications is strictly at your own risk. The creator assumes no liability for audio hardware clipping, data corruption, DAW crashes, or unexpected sonic anomalies.


