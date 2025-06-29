# OB-Xd Documentation

Welcome to the OB-Xd synthesizer documentation. This directory contains comprehensive technical documentation for developers and users interested in understanding the architecture and implementation of this Oberheim OB-X inspired synthesizer.

## Documentation Structure

### Core Documentation
- **[Plugin Lifecycle](plugin-lifecycle.md)** - Complete overview of the JUCE audio plugin lifecycle
- **[Plugin Flow Diagram](plugin-flow.md)** - Visual representation of audio processing flow
- **[DSP Implementation](dsp-implementation.md)** - Deep dive into synthesizer DSP algorithms
- **[File Layout](file-layout.md)** - Project structure and organization

### Technical References
- **[Parameter Reference](parameter-reference.md)** - Complete list of all synthesizer parameters
- **[MIDI Implementation](midi-implementation.md)** - MIDI mapping and control specifications
- **[Preset System](preset-system.md)** - Bank and preset management architecture

### Development Guides
- **[Building from Source](building.md)** - Compilation instructions and requirements
- **[Contributing](contributing.md)** - Guidelines for code contributions
- **[Testing](testing.md)** - Test procedures and validation

## Quick Start

1. **For Users**: Start with the [Plugin Flow Diagram](plugin-flow.md) to understand signal flow
2. **For Developers**: Begin with [File Layout](file-layout.md) and [Plugin Lifecycle](plugin-lifecycle.md)
3. **For DSP Engineers**: Jump to [DSP Implementation](dsp-implementation.md)

## About OB-Xd

OB-Xd is a faithful recreation of the classic Oberheim OB-X synthesizer, implementing:

- **Dual Oscillator Architecture** with saw, pulse, and noise sources
- **Multimode Filter** with 12dB/24dB slopes and continuous morphing
- **Dual ADSR Envelopes** for amplitude and filter modulation
- **LFO System** with multiple waveforms and routing options
- **Voice Management** supporting up to 32 voices with unison mode
- **Micro-detuning** for analog warmth and character

The plugin is built using the JUCE framework and maintains compatibility with all major DAWs supporting VST, AU, and AAX formats.

## Architecture Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   MIDI Input    │────│  Voice Manager  │────│   DSP Engine    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                               │                       │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Parameter UI   │────│  Plugin Host    │────│  Audio Output   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

For detailed diagrams and implementation details, see the individual documentation files.
