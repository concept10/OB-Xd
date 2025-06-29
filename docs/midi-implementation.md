# MIDI Implementation

This document details the MIDI implementation in OB-Xd, covering note handling, continuous controllers, MIDI learn functionality, and integration patterns.

## MIDI Channel Support

OB-Xd responds to MIDI on **all channels** (1-16) simultaneously, making it suitable for:

- **Multi-channel setups** with channel-specific control
- **Omni mode operation** for simple single-channel use
- **MIDI merge scenarios** with multiple input sources

## Note Messages

### Note On/Off Handling

```cpp
// Sample-accurate MIDI processing
void ObxdAudioProcessor::processMidiPerSample(MidiBuffer::Iterator* iter, 
                                              const int samplePos)
{
    while (getNextEvent(iter, samplePos))
    {
        if (midiMsg->isNoteOn())
        {
            int noteNumber = midiMsg->getNoteNumber();     // 0-127
            float velocity = midiMsg->getVelocity() / 127.0f; // 0.0-1.0
            synth.procNoteOn(noteNumber, velocity);
        }
        else if (midiMsg->isNoteOff())
        {
            synth.procNoteOff(midiMsg->getNoteNumber());
        }
    }
}
```

### Velocity Response

| Velocity Range | Behavior |
|----------------|----------|
| **0** | Note Off (velocity 0 = note off) |
| **1-127** | Note On with velocity scaling |

Velocity affects two destinations:

- **Amplitude Envelope**: Controlled by `VELO_AMP_ENV` parameter (0-100%)
- **Filter Envelope**: Controlled by `VELO_FILT_ENV` parameter (0-100%)

### Note Range

- **Full MIDI range**: Notes 0-127 (C-1 to G9)
- **No note filtering**: All notes are processed
- **Octave transpose**: Global octave shift available (±2 octaves)

## Continuous Controllers (CC)

### Standard Controller Mappings

| CC Number | Parameter | Range | Description |
|-----------|-----------|-------|-------------|
| **1** | Mod Wheel | 0-127 | Vibrato amount |
| **7** | Volume | 0-127 | Master volume |
| **10** | Pan | 0-127 | Stereo position (future use) |
| **64** | Sustain Pedal | 0-63/64-127 | Sustain on/off |
| **74** | Filter Cutoff | 0-127 | Real-time cutoff control |
| **120** | All Sound Off | Any | Emergency stop |
| **123** | All Notes Off | Any | Release all notes |

### Pitch Bend

- **Range**: ±8192 (14-bit resolution)
- **Sensitivity**: 2 or 12 semitones (switchable)
- **OSC targeting**: Both oscillators or OSC2 only
- **Smoothing**: Built-in interpolation prevents audio artifacts

```cpp
void SynthEngine::procPitchWheel(float val)
{
    pitchWheelSmoother.setSteep(val); // Smooth parameter changes
}

void SynthEngine::procPitchWheelSmoothed(float val)
{
    for(int i = 0; i < synth.MAX_VOICES; i++)
    {
        synth.voices[i].pitchWheel = val;
    }
}
```

### Modulation Wheel (CC1)

The mod wheel controls vibrato with these characteristics:

- **Vibrato LFO**: Separate from main LFO
- **Frequency range**: 3-10 Hz (adjustable)
- **Depth control**: 0-100% via mod wheel
- **Waveform**: Sine wave only
- **Targets**: Both oscillator pitches simultaneously

## MIDI Learn System

### Learning Process

1. **Activate Learn Mode**: Click "MIDI Learn" button or parameter
2. **Parameter Selection**: Click on any knob, button, or slider
3. **Send MIDI CC**: Move desired hardware controller
4. **Automatic Mapping**: Association is created and stored

### MIDI Learn Implementation

```cpp
class MidiMap
{
private:
    struct CCMapping 
    {
        int parameterIndex;
        int ccNumber;
        bool isLearned;
    };
    
    Array<CCMapping> mappings;
    
public:
    void setCC(int paramIndex, int ccNumber)
    {
        // Create or update mapping
        mappings.add({paramIndex, ccNumber, true});
    }
    
    void processCC(int ccNumber, float value)
    {
        // Find parameter mapped to this CC
        for (auto& mapping : mappings)
        {
            if (mapping.ccNumber == ccNumber && mapping.isLearned)
            {
                processor.setEngineParameterValue(mapping.parameterIndex, value);
            }
        }
    }
};
```

### MIDI Learn Features

- **Any CC to any parameter**: Complete flexibility
- **Multiple mappings**: One CC can control multiple parameters
- **Persistent storage**: Mappings saved with presets
- **Visual feedback**: Learned assignments highlighted in UI
- **Easy removal**: "MIDI Unlearn" button clears associations

### MIDI Learn File Format

MIDI mappings are stored in JSON format:

```json
{
  "midiMappings": [
    {
      "parameter": "CUTOFF",
      "ccNumber": 74,
      "channel": -1
    },
    {
      "parameter": "RESONANCE", 
      "ccNumber": 71,
      "channel": -1
    }
  ]
}
```

## Advanced MIDI Features

### High-Resolution Controllers (14-bit)

OB-Xd supports high-resolution MIDI for precise control:

```cpp
// 14-bit CC processing (MSB + LSB)
void processMIDI14Bit(int ccMSB, int ccLSB, float value)
{
    int highRes = (ccMSB << 7) | ccLSB;  // Combine for 14-bit resolution
    float normalized = highRes / 16383.0f; // Convert to 0.0-1.0
    // Apply to parameter with full precision
}
```

Supported 14-bit CCs:
- **CC 1/33**: Mod Wheel (high-res vibrato)
- **CC 7/39**: Volume (smooth level changes)
- **CC 74/42**: Filter Cutoff (precise frequency control)

### MPE (MIDI Polyphonic Expression) Support

While not fully MPE-compliant, OB-Xd supports per-voice modulation:

- **Per-voice pitch bend**: Individual voice pitch control
- **Per-voice pressure**: Aftertouch routing (planned)
- **Timbre control**: CC74 per channel (planned)

### MIDI Timing and Sync

#### Sample-Accurate Timing

```cpp
void processBlock(AudioSampleBuffer& buffer, MidiBuffer& midiMessages)
{
    const int numSamples = buffer.getNumSamples();
    MidiBuffer::Iterator midiIterator(midiMessages);
    
    for (int sampleIndex = 0; sampleIndex < numSamples; ++sampleIndex)
    {
        // Process MIDI events at exact sample position
        processMidiPerSample(&midiIterator, sampleIndex);
        
        // Generate audio for this sample
        float leftSample, rightSample;
        synth.processSample(&leftSample, &rightSample);
        
        buffer.setSample(0, sampleIndex, leftSample);
        buffer.setSample(1, sampleIndex, rightSample);
    }
}
```

#### LFO Sync to Host

- **Tempo sync**: LFO can sync to host tempo
- **Beat divisions**: 1/1, 1/2, 1/4, 1/8, 1/16, 1/32
- **Retrigger**: LFO restart on new notes

## MIDI Performance Optimization

### Efficient Event Processing

```cpp
class MidiProcessor
{
private:
    MidiMessage* nextMidi;
    bool hasMidiMessage;
    int midiEventPos;
    
public:
    bool getNextEvent(MidiBuffer::Iterator* iter, const int samplePos)
    {
        while (midiEventPos <= samplePos && hasMidiMessage)
        {
            // Process current MIDI event
            if (midiEventPos == samplePos)
            {
                return true; // Event ready to process
            }
            
            // Advance to next event
            hasMidiMessage = iter->getNextEvent(*nextMidi, midiEventPos);
        }
        return false;
    }
};
```

### Voice Allocation Strategies

#### Round Robin Mode (Default)

```cpp
void Motherboard::setNoteOn(int noteNo, float velocity)
{
    for (int i = 0; i < totalvc && !processed; i++)
    {
        ObxdVoice* voice = voiceQueue.getNext();
        if (!voice->Active)
        {
            voice->NoteOn(noteNo, velocity);
            processed = true;
        }
    }
}
```

#### As-Played Mode

Voices are allocated in the order notes were played, with intelligent stealing:

```cpp
if (asPlayedMode)
{
    // Find voice with lowest priority (oldest note)
    int minPriority = INT_MAX;
    ObxdVoice* targetVoice = nullptr;
    
    for (int i = 0; i < totalvc; i++)
    {
        if (priorities[voices[i].midiIndx] < minPriority)
        {
            minPriority = priorities[voices[i].midiIndx];
            targetVoice = &voices[i];
        }
    }
    
    if (targetVoice)
        targetVoice->NoteOn(noteNo, velocity);
}
```

## MIDI Implementation Chart

### Standard MIDI Implementation

| Function | Transmitted | Recognized | Remarks |
|----------|------------|------------|---------|
| **Basic Channel** | - | 1-16 | Omni mode |
| **Mode** | - | Mode 1 (Omni On, Poly) | |
| **Note Number** | - | 0-127 | Full range |
| **Velocity** | - | Note On: 1-127<br>Note Off: 0 | |
| **Aftertouch** | - | No | Planned feature |
| **Pitch Bend** | - | Yes | ±2/12 semitones |
| **Control Change** | - | Yes | See CC table |
| **Program Change** | - | 0-127 | Preset selection |
| **System Exclusive** | - | No | |
| **System Common** | - | No | |
| **System Real Time** | - | Clock only | For LFO sync |
| **Aux Messages** | - | No | |

### Controller Implementation

| CC# | Function | Range | Resolution |
|-----|----------|-------|------------|
| **1** | Modulation | 0-127 | 7-bit |
| **1/33** | Modulation (Hi-Res) | 0-16383 | 14-bit |
| **7** | Volume | 0-127 | 7-bit |
| **7/39** | Volume (Hi-Res) | 0-16383 | 14-bit |
| **10** | Pan | 0-127 | 7-bit |
| **64** | Sustain | 0-63=Off, 64-127=On | Switch |
| **74** | Filter Cutoff | 0-127 | 7-bit |
| **74/42** | Filter Cutoff (Hi-Res) | 0-16383 | 14-bit |
| **120** | All Sound Off | Any | Trigger |
| **123** | All Notes Off | Any | Trigger |

## Integration Examples

### DAW Integration

#### Ableton Live

```javascript
// Max for Live device integration
{
  "parameters": [
    {"name": "Cutoff", "cc": 74, "min": 0, "max": 127},
    {"name": "Resonance", "cc": 71, "min": 0, "max": 127},
    {"name": "LFO Rate", "cc": 76, "min": 0, "max": 127}
  ]
}
```

#### Logic Pro

```xml
<!-- Channel EQ setting for MIDI learn -->
<key>MIDILearnMode</key>
<true/>
<key>DefaultCCMappings</key>
<dict>
    <key>Cutoff</key>
    <integer>74</integer>
    <key>Resonance</key>
    <integer>71</integer>
</dict>
```

### Hardware Controller Templates

#### Arturia MiniLab MkII

| Control | Parameter | CC | Notes |
|---------|-----------|----|----|
| **Knob 1** | Cutoff | 74 | Filter frequency |
| **Knob 2** | Resonance | 71 | Filter resonance |
| **Knob 9** | LFO Rate | 76 | Modulation speed |
| **Mod Wheel** | Vibrato | 1 | Built-in mapping |
| **Pitch Bend** | Pitch | - | Hardware direct |

#### Novation Launchkey

```json
{
  "template": "OB-Xd",
  "mappings": [
    {"control": "Fader1", "cc": 7, "param": "Volume"},
    {"control": "Knob1", "cc": 74, "param": "Cutoff"},
    {"control": "Knob2", "cc": 71, "param": "Resonance"},
    {"control": "Knob3", "cc": 76, "param": "LFO_Rate"}
  ]
}
```

This comprehensive MIDI implementation provides:

- **Complete hardware integration** with any MIDI controller
- **Professional DAW compatibility** across all platforms
- **Flexible routing options** via MIDI learn
- **Sample-accurate timing** for tight musical performance
- **Efficient processing** optimized for real-time use
