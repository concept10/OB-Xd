# Building from Source

This document provides detailed instructions for compiling OB-Xd from source code on all supported platforms.

## Prerequisites

### Required Software

#### All Platforms

- **JUCE Framework 7.0.3** (exactly this version)
  - Download from: <https://github.com/juce-framework/JUCE/releases/tag/7.0.3>
  - Alternative: Use JUCE's Projucer to manage the project

#### Windows

- **Visual Studio 2019/2022** with C++ development tools
- **Windows SDK 10.0** or later
- **Git** for cloning the repository

#### macOS

- **Xcode 12.0** or later
- **macOS 10.13** or later for development
- **Command Line Tools** for Xcode

#### Linux

- **GCC 7.0** or **Clang 6.0** or later
- **Development packages**:
  ```bash
  # Ubuntu/Debian
  sudo apt update
  sudo apt install build-essential git pkg-config
  sudo apt install libasound2-dev libjack-jackd2-dev
  sudo apt install ladspa-sdk
  sudo apt install libcurl4-openssl-dev
  sudo apt install libfreetype6-dev
  sudo apt install libx11-dev libxcomposite-dev libxcursor-dev
  sudo apt install libxext-dev libxinerama-dev libxrandr-dev libxrender-dev
  sudo apt install libwebkit2gtk-4.0-dev
  sudo apt install libglu1-mesa-dev mesa-common-dev
  
  # Fedora/CentOS/RHEL
  sudo dnf groupinstall "Development Tools"
  sudo dnf install alsa-lib-devel jack-audio-connection-kit-devel
  sudo dnf install ladspa-devel
  sudo dnf install libcurl-devel
  sudo dnf install freetype-devel
  sudo dnf install libX11-devel libXcomposite-devel libXcursor-devel
  sudo dnf install libXext-devel libXinerama-devel libXrandr-devel
  sudo dnf install libXrender-devel
  sudo dnf install webkit2gtk3-devel
  sudo dnf install mesa-libGLU-devel
  ```

## Source Code Setup

### 1. Clone Repository

```bash
git clone https://github.com/discoDSP/OB-Xd.git
cd OB-Xd
```

### 2. JUCE Framework Setup

#### Option A: Standalone JUCE Installation

1. Download JUCE 7.0.3 from GitHub releases
2. Extract to a directory (e.g., `C:\JUCE` on Windows, `/opt/juce` on Linux)
3. Set environment variable `JUCE_PATH` to point to JUCE directory

#### Option B: Using Projucer

1. Download and install Projucer from JUCE website
2. Open `OB-Xd.jucer` (Windows/Mac) or `OB-Xd Linux.jucer` (Linux)
3. Configure global paths in Projucer preferences

## Platform-Specific Build Instructions

### Windows Build

#### Using Visual Studio

1. **Open Projucer**:
   ```cmd
   # Navigate to OB-Xd directory
   cd OB-Xd
   # Open with Projucer
   "C:\Program Files\JUCE\Projucer.exe" OB-Xd.jucer
   ```

2. **Configure Build Settings**:
   - Click "Save Project and Open in IDE"
   - This generates Visual Studio solution files

3. **Build in Visual Studio**:
   ```cmd
   # Alternative: Command line build
   "C:\Program Files (x86)\Microsoft Visual Studio\2022\Community\MSBuild\Current\Bin\MSBuild.exe" ^
     Builds\VisualStudio2022\OB-Xd.sln ^
     /p:Configuration=Release /p:Platform=x64
   ```

4. **Plugin Locations**:
   ```
   VST3: Builds\VisualStudio2022\x64\Release\VST3\OB-Xd.vst3
   AAX:  Builds\VisualStudio2022\x64\Release\AAX\OB-Xd.aaxplugin
   ```

#### Using Command Line (Advanced)

```cmd
# Set up Visual Studio environment
call "C:\Program Files (x86)\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars64.bat"

# Generate project files
Projucer.exe --resave OB-Xd.jucer

# Build
msbuild Builds\VisualStudio2022\OB-Xd.sln /p:Configuration=Release
```

### macOS Build

#### Using Xcode

1. **Generate Xcode Project**:
   ```bash
   # Using Projucer GUI
   /Applications/JUCE/Projucer.app/Contents/MacOS/Projucer OB-Xd.jucer
   
   # Or command line
   /Applications/JUCE/Projucer.app/Contents/MacOS/Projucer --resave OB-Xd.jucer
   ```

2. **Build in Xcode**:
   ```bash
   # Open Xcode project
   open Builds/MacOSX/OB-Xd.xcodeproj
   
   # Or command line build
   xcodebuild -project Builds/MacOSX/OB-Xd.xcodeproj \
              -scheme "OB-Xd - All" \
              -configuration Release
   ```

3. **Plugin Locations**:
   ```
   AU:   Builds/MacOSX/build/Release/OB-Xd.component
   VST3: Builds/MacOSX/build/Release/OB-Xd.vst3
   AAX:  Builds/MacOSX/build/Release/OB-Xd.aaxplugin
   ```

#### Code Signing (for Distribution)

```bash
# Sign plugins with your Developer ID
codesign --force --sign "Developer ID Application: Your Name" \
  Builds/MacOSX/build/Release/OB-Xd.component

codesign --force --sign "Developer ID Application: Your Name" \
  Builds/MacOSX/build/Release/OB-Xd.vst3
```

### Linux Build

#### Using Make

1. **Generate Makefile**:
   ```bash
   # Using Projucer
   Projucer --resave "OB-Xd Linux.jucer"
   
   # Navigate to build directory
   cd Builds/LinuxMakefile
   ```

2. **Compile**:
   ```bash
   # Build release version
   make CONFIG=Release
   
   # Or with specific number of jobs
   make CONFIG=Release -j$(nproc)
   
   # Debug build
   make CONFIG=Debug
   ```

3. **Plugin Locations**:
   ```
   VST3: Builds/LinuxMakefile/build/OB-Xd.vst3
   LV2:  Builds/LinuxMakefile/build/OB-Xd.lv2
   ```

#### Using CMake (Alternative)

```bash
# Create build directory
mkdir build && cd build

# Configure
cmake .. -DCMAKE_BUILD_TYPE=Release

# Build
make -j$(nproc)
```

## Build Configuration Options

### Plugin Formats

Edit `.jucer` file to enable/disable formats:

- **VST3**: Modern cross-platform format
- **AU**: Audio Units (macOS only)
- **AAX**: Pro Tools format
- **LV2**: Linux native format

### Optimization Settings

#### Release Build Optimizations

```cpp
// Preprocessor definitions for release
#define NDEBUG 1
#define JUCE_DISPLAY_SPLASH_SCREEN 0
#define JUCE_USE_DARK_SPLASH_SCREEN 1
```

#### Debug Build Settings

```cpp
// Preprocessor definitions for debug
#define DEBUG 1
#define _DEBUG 1
#define JUCE_CHECK_MEMORY_LEAKS 1
```

## Installation

### Windows Installation

```cmd
# Copy to system plugin directories
copy "Builds\VisualStudio2022\x64\Release\VST3\OB-Xd.vst3" ^
     "%COMMONPROGRAMFILES%\VST3\"

copy "Builds\VisualStudio2022\x64\Release\AAX\OB-Xd.aaxplugin" ^
     "%COMMONPROGRAMFILES%\Avid\Audio\Plug-Ins\"
```

### macOS Installation

```bash
# Copy to system plugin directories
sudo cp -R "Builds/MacOSX/build/Release/OB-Xd.component" \
  "/Library/Audio/Plug-Ins/Components/"

sudo cp -R "Builds/MacOSX/build/Release/OB-Xd.vst3" \
  "/Library/Audio/Plug-Ins/VST3/"

sudo cp -R "Builds/MacOSX/build/Release/OB-Xd.aaxplugin" \
  "/Library/Application Support/Avid/Audio/Plug-Ins/"
```

### Linux Installation

```bash
# Copy to user plugin directories
mkdir -p ~/.vst3
cp -R "Builds/LinuxMakefile/build/OB-Xd.vst3" ~/.vst3/

mkdir -p ~/.lv2  
cp -R "Builds/LinuxMakefile/build/OB-Xd.lv2" ~/.lv2/

# System-wide installation (requires sudo)
sudo cp -R "Builds/LinuxMakefile/build/OB-Xd.vst3" \
  "/usr/lib/vst3/"
```

## Troubleshooting

### Common Build Issues

#### JUCE Module Not Found

```
Error: Couldn't find JUCE modules folder
```

**Solution**: Set correct JUCE path in Projucer global settings or environment variable.

#### Missing Dependencies (Linux)

```
fatal error: alsa/asoundlib.h: No such file or directory
```

**Solution**: Install ALSA development packages:
```bash
sudo apt install libasound2-dev  # Ubuntu/Debian
sudo dnf install alsa-lib-devel  # Fedora
```

#### Visual Studio Version Mismatch

```
MSB8020: The build tools for v142 cannot be found
```

**Solution**: Update Projucer project to match your Visual Studio version.

#### macOS SDK Issues

```
error: unknown type name 'NSAppearanceName'
```

**Solution**: Update to newer macOS SDK or adjust deployment target.

### Performance Build Tips

#### Optimize for Size

```cpp
// Add to preprocessor definitions
#define JUCE_STRICT_REFCOUNTEDPOINTER 1
#define JUCE_USE_SMALL_SPLASH_SCREEN 1
```

#### CPU Optimizations

```bash
# GCC/Clang optimization flags
-O3 -march=native -mtune=native -ffast-math
```

#### Link-Time Optimization

```bash
# Enable LTO for smaller, faster builds
-flto
```

## Continuous Integration

### GitHub Actions Example

```yaml
name: Build OB-Xd

on: [push, pull_request]

jobs:
  build:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macOS-latest]
    
    runs-on: ${{ matrix.os }}
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup JUCE
      run: |
        wget https://github.com/juce-framework/JUCE/releases/download/7.0.3/juce-7.0.3-linux.zip
        unzip juce-7.0.3-linux.zip
        
    - name: Build
      run: |
        Projucer --resave OB-Xd.jucer
        cd Builds/LinuxMakefile && make CONFIG=Release
```

## Package Creation

### Windows Installer (NSIS)

```nsis
; OB-Xd installer script
!include "MUI2.nsh"

OutFile "OB-Xd-Installer.exe"
InstallDir "$PROGRAMFILES64\VST3"

Section "Install"
  SetOutPath "$INSTDIR"
  File "OB-Xd.vst3"
SectionEnd
```

### macOS Package

```bash
# Create installer package
pkgbuild --root install_root \
         --identifier com.discodsp.obxd \
         --version 2.10 \
         OB-Xd.pkg
```

### Linux Package (Debian)

```bash
# Create .deb package
mkdir -p obxd_2.10/usr/lib/vst3
cp -R OB-Xd.vst3 obxd_2.10/usr/lib/vst3/
dpkg-deb --build obxd_2.10
```

This comprehensive build guide ensures successful compilation across all supported platforms while providing optimization options for different use cases.
