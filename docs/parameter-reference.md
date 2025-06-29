# Parameter Reference

This document provides a complete reference for all parameters in OB-Xd, including their ranges, default values, and implementation details.

## Parameter Overview

OB-Xd contains **90+ parameters** organized into logical groups. Each parameter supports:

- **Host automation** via DAW envelopes and control surfaces
- **MIDI learn** for hardware controller mapping
- **Preset storage** in banks and individual patches
- **Real-time modulation** with parameter smoothing

## Oscillator Parameters

### OSC 1 Parameters

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| OSC1 Saw | `OSC1Saw` | On/Off | On | Enable sawtooth waveform |
| OSC1 Pulse | `OSC1Pul` | On/Off | Off | Enable pulse waveform |
| OSC1 Mix | `OSC1MIX` | 0.0 - 1.0 | 1.0 | Oscillator 1 output level |
| OSC1 Pitch | `OSC1P` | -24 - +24 ST | 0 ST | Pitch offset in semitones |
| OSC1 LFO Pitch | `lfoo1` | On/Off | Off | LFO modulation to pitch |
| OSC1 LFO PW | `lfopw1` | On/Off | Off | LFO modulation to pulse width |

### OSC 2 Parameters

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| OSC2 Saw | `OSC2Saw` | On/Off | On | Enable sawtooth waveform |
| OSC2 Pulse | `OSC2Pul` | On/Off | Off | Enable pulse waveform |
| OSC2 Mix | `OSC2MIX` | 0.0 - 1.0 | 1.0 | Oscillator 2 output level |
| OSC2 Pitch | `OSC2P` | -24 - +24 ST | 0 ST | Pitch offset in semitones |
| OSC2 Detune | `OSC2_DET` | 0.0 - 1.0 | 0.4 | Fine detuning amount |
| OSC2 LFO Pitch | `lfoo2` | On/Off | Off | LFO modulation to pitch |
| OSC2 LFO PW | `lfopw2` | On/Off | Off | LFO modulation to pulse width |

### Shared Oscillator Parameters

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Pulse Width | `PW` | 0.0 - 0.95 | 0.5 | Base pulse width (0% - 95%) |
| PW Envelope Mod | `pwenvmod` | 0.0 - 0.85 | 0.0 | Envelope modulation depth |
| PW Offset | `pwOfs` | 0.0 - 0.75 | 0.0 | OSC2 pulse width offset |
| PW Env Both | `pwEnvBoth` | On/Off | Off | Apply PW env to both oscillators |
| Hard Sync | `SYNC` | On/Off | Off | OSC1 hard sync to OSC2 |
| Cross Mod | `XMOD` | 0.0 - 24.0 | 0.0 | OSC1 frequency modulation of OSC2 |
| Noise Mix | `NOISEMIX` | 0.0 - 1.0 | 0.0 | White noise level |

## Filter Parameters

### Filter Core

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Cutoff | `CUTOFF` | 0.0 - 120.0 | 120.0 | Filter cutoff frequency |
| Resonance | `RESONANCE` | 0.0 - 0.991 | 0.0 | Filter resonance amount |
| Filter KB Follow | `KBD_TRK` | 0.0 - 1.0 | 0.0 | Keyboard tracking amount |
| Filter 4-Pole | `FOURPOLE` | On/Off | Off | 24dB vs 12dB filter mode |
| Multimode | `MULTIMODE` | 0.0 - 1.0 | 0.0 | HP ↔ Notch ↔ BP ↔ LP morph |
| Bandpass Blend | `BANDPASS` | On/Off | Off | Bandpass filter mode |

### Filter Modulation

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Filter Env Amt | `FILTER_ENV_AMT` | 0.0 - 140.0 | 0.0 | Filter envelope depth |
| Filter LFO | `lfof` | On/Off | Off | LFO modulation to filter |
| Invert F Env | `FENV_INVERT` | On/Off | Off | Invert filter envelope |

## Envelope Parameters

### Amplitude Envelope (ADSR)

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Attack | `LATK` | 4ms - 60s | 4ms | Attack time |
| Decay | `LDEC` | 4ms - 60s | 1s | Decay time |
| Sustain | `LSUS` | 0.0 - 1.0 | 1.0 | Sustain level |
| Release | `LREL` | 8ms - 60s | 1s | Release time |

### Filter Envelope (ADSR)

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| F Attack | `FATK` | 1ms - 60s | 1ms | Filter attack time |
| F Decay | `FDEC` | 1ms - 60s | 1s | Filter decay time |
| F Sustain | `FSUS` | 0.0 - 1.0 | 0.0 | Filter sustain level |
| F Release | `FREL` | 1ms - 60s | 1s | Filter release time |

## LFO Parameters

### LFO Core

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| LFO Rate | `LFO_RATE` | 0.1 - 50 Hz | 2.0 Hz | LFO frequency |
| LFO Sync | `LFOSYNC` | On/Off | Off | Sync to host tempo |
| LFO Sine | `LFOSIN` | On/Off | On | Enable sine wave |
| LFO Square | `LFOSQR` | On/Off | Off | Enable square wave |
| LFO S&H | `LFOSH` | On/Off | Off | Enable sample & hold |

### LFO Routing

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| LFO Amt 1 | `LFO_AMT` | 0.0 - 60.0 | 0.0 | Pitch modulation depth |
| LFO Amt 2 | `LFO_AMT2` | 0.0 - 0.7 | 0.0 | Pulse width modulation depth |

## Voice Management

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Voice Count | `VOICE_COUNT` | 1 - 32 | 8 | Number of active voices |
| Unison | `UNISON` | On/Off | Off | All voices play same note |
| Legato Mode | `LEGATO` | 0 - 3 | 0 | Legato behavior mode |
| As Played Alloc | `ASPLAYEDALLOCATION` | On/Off | Off | Voice allocation method |

## Global Parameters

### Main Controls

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Volume | `VOLUME` | 0.0 - 0.3 | 0.15 | Master output level |
| Tune | `TUNE` | -1.0 - +1.0 | 0.0 | Global pitch adjustment |
| Octave | `OCTAVE` | -2 - +2 Oct | 0 Oct | Global octave shift |
| Brightness | `BRIGHTNESS` | 7kHz - 26kHz | 26kHz | High frequency emphasis |

### Portamento

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Portamento | `PORTAMENTO` | 0.14 - 250ms | 0.14ms | Glide time between notes |

### Modulation

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Pitch Wheel Range | `BEND_RANGE` | 2/12 ST | 2 ST | Pitch bend sensitivity |
| Pitch Wheel OSC2 | `BEND_OSC2_ONLY` | On/Off | Off | Bend OSC2 only |
| Vibrato Rate | `BENDLFORATE` | 3 - 10 Hz | 6 Hz | Vibrato LFO frequency |
| Env → Pitch | `ENVPITCHMOD` | 0.0 - 36 ST | 0.0 | Envelope pitch modulation |
| Pitch Mod Both | `PITCH_MOD_BOTH` | On/Off | Off | Apply pitch mod to both OSC |

### Velocity Sensitivity

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Velocity → Amp | `VELO_AMP_ENV` | 0.0 - 1.0 | 0.0 | Velocity to amplitude |
| Velocity → Filter | `VELO_FILT_ENV` | 0.0 - 1.0 | 0.0 | Velocity to filter |

## Micro-Detuning Parameters

### Analog Character

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Voice Detune | `UDET` | 0.0 - 0.9 | 0.2 | Voice pitch detuning |
| Envelope Detune | `ENVDER` | 0.0 - 1.0 | 0.3 | Envelope timing variation |
| Filter Detune | `FILTERDER` | 0.0 - 18¢ | 0.3 | Filter frequency variation |
| Portamento Detune | `PORTADER` | 0.0 - 0.75 | 0.3 | Portamento timing variation |
| Level Difference | `LEVEL_DIF` | 0.0 - 0.67 | 0.3 | Voice level variation |

## Pan Parameters

### Per-Voice Panning

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Pan 1 | `PAN1` | L - R | Center | Voice 1 pan position |
| Pan 2 | `PAN2` | L - R | Center | Voice 2 pan position |
| Pan 3 | `PAN3` | L - R | Center | Voice 3 pan position |
| Pan 4 | `PAN4` | L - R | Center | Voice 4 pan position |
| Pan 5 | `PAN5` | L - R | Center | Voice 5 pan position |
| Pan 6 | `PAN6` | L - R | Center | Voice 6 pan position |
| Pan 7 | `PAN7` | L - R | Center | Voice 7 pan position |
| Pan 8 | `PAN8` | L - R | Center | Voice 8 pan position |

## System Parameters

### Performance

| Parameter | ID | Range | Default | Description |
|-----------|----|----|---------|-------------|
| Economy Mode | `ECONOMY_MODE` | On/Off | On | CPU-saving voice management |
| Oversampling | `OVERSAMPLE` | On/Off | Off | 2x internal oversampling |
| Self Osc Push | `SELF_OSC_PUSH` | On/Off | Off | Filter self-oscillation boost |
| Quantize Pitch | `QUANTIZE_PITCH` | On/Off | Off | Quantize pitch wheel to semitones |

## Parameter Implementation Details

### Value Scaling Functions

```cpp
// Linear scaling: 0.0-1.0 to min-max range
float linsc(float param, float min, float max)
{
    return min + param * (max - min);
}

// Logarithmic scaling for frequency parameters  
float logsc(float param, float min, float max, float curve = 30)
{
    return min * pow(max / min, pow(param, curve / 100.0f));
}

// Pitch conversion: parameter to Hz
float getPitch(float pitchParam)
{
    return 440.0f * pow(2.0f, (pitchParam - 69.0f) / 12.0f);
}
```

### Parameter Smoothing

All parameters that affect audio generation use smoothing to prevent clicks:

```cpp
class ParamSmoother
{
public:
    void setSteep(float newTarget);      // Set target value
    float smoothStep();                  // Get next smoothed value
    void setSampleRate(float sr);        // Configure smoothing rate
};
```

### MIDI Learn Implementation

Each parameter can be mapped to MIDI CC:

```cpp
class MidiMap
{
public:
    void setCC(int paramIndex, int ccNumber);
    void processCC(int ccNumber, float value);
    void unlearn(int paramIndex);
};
```

## Parameter Categories Summary

| Category | Count | Description |
|----------|-------|-------------|
| **Oscillators** | 20 | Waveform generation and modulation |
| **Filter** | 15 | Frequency filtering and resonance |
| **Envelopes** | 16 | Amplitude and filter ADSR |
| **LFO/Modulation** | 18 | Low frequency oscillation and routing |
| **Voice Management** | 8 | Polyphony and allocation |
| **Global Controls** | 10 | Master settings and tuning |
| **Micro-Detuning** | 8 | Analog character simulation |
| **Pan Controls** | 8 | Stereo positioning |
| **System** | 5 | Performance and behavior |
| **Total** | **108** | **Complete parameter set** |

## Default Preset Values

The default preset provides a classic analog sound:

- **Oscillators**: Both saw waves enabled, mixed equally
- **Filter**: Fully open lowpass, no resonance  
- **Envelopes**: Fast attack, medium decay/release
- **LFO**: Moderate rate, no initial modulation
- **Voices**: 8-voice polyphony
- **Character**: Moderate detuning for warmth

This parameter reference enables:

- **Complete automation** of all synthesis aspects
- **Hardware controller integration** via MIDI learn
- **Preset programming** with full parameter access
- **Performance control** through real-time modulation
- **Sound design flexibility** across all synthesis parameters
