# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AIS-catcher is a multi-platform AIS (Automatic Identification System) receiver supporting various Software Defined Radios (SDRs). It decodes AIS messages transmitted by ships and outputs them in NMEA format or via various output destinations including screen, file, UDP, TCP, HTTP, and PostgreSQL.

Key features:
- Multi-platform (Linux, Windows, macOS)
- Supports many SDR devices (RTL-SDR, AirSpy, HackRF, SDRPlay, etc.)
- Built-in web server for monitoring AIS traffic
- Multiple output formats (NMEA, JSON)
- Network connectivity options (UDP, TCP, HTTP, MQTT)
- Community sharing features via aiscatcher.org

## Build Commands

### Building with Make

```bash
# Basic build with all available SDR support
make

# Build with specific SDR support only
make rtl-only           # RTL-SDR only
make airspyhf-only      # Airspy HF+ only
make airspy-only        # Airspy only
make sdrplay-only       # SDRPlay only
make hackrf-only        # HackRF only
make zmq-only           # ZMQ only
make soapysdr-only      # SoapySDR only

# Clean build artifacts
make clean
```

### Building with CMake

```bash
# Create build directory
mkdir build
cd build

# Configure with CMake (enable/disable specific SDR support)
cmake ..

# Build
make

# Install
sudo make install
```

### Building with the Script

The repository includes a build script that handles dependencies and builds the project:

```bash
# Full build with dependencies
./scripts/build.sh

# Create Debian package (optional arguments)
./scripts/build.sh "additional-deps" create-deb amd64 "version"
```

## Running AIS-catcher

Basic usage:

```bash
# Run with default settings (RTL-SDR, channels A & B)
./AIS-catcher

# Show all options
./AIS-catcher -h

# Select device by index
./AIS-catcher -d:0

# Run with specific sample rate
./AIS-catcher -s 1536000

# Enable web server on port 8080
./AIS-catcher -N 8080

# Write NMEA data to file
./AIS-catcher -f output.nmea

# Send data via UDP
./AIS-catcher -u 192.168.1.10 10110

# Use specific model
./AIS-catcher -m 2
```

## Debugging Options

```bash
# Show supported hardware
./AIS-catcher -L

# List available devices
./AIS-catcher -l

# Enable verbose output, update every 5 seconds
./AIS-catcher -v 5

# Benchmark decoding models
./AIS-catcher -b
```

## Architecture Overview

AIS-catcher is structured around these key components:

1. **Device Interfaces**: Abstraction layer for different SDR hardware
   - Located in `/Device/` directory
   - Each device has its own implementation class (RTLSDR, AIRSPY, etc.)

2. **Signal Processing**: DSP components for demodulation
   - Located in `/DSP/` directory
   - Models for signal processing and demodulation

3. **AIS Protocol**: AIS message decoding and parsing
   - Located in `/Library/` directory
   - Handles NMEA formatting and message interpretation

4. **Output Modules**: Various output formats and destinations
   - Located in `/IO/` directory
   - Support for file, network, database outputs

5. **Web Interface**: Built-in web server for visualization
   - Located in `/Application/WebViewer.cpp`
   - Uses HTML/CSS/JS files from `/HTML/` directory

6. **Configuration**: Command-line and file-based configuration
   - Located in `/Application/Config.cpp`
   - Supports JSON configuration files

## Development Notes

- The codebase uses C++11 features
- Dependencies are handled through pkg-config
- Uses a signal/slot mechanism for connecting components (see Signals.h)
- Device selection is determined at runtime based on command-line args
- Models (0-9) represent different demodulation approaches with varying performance/accuracy tradeoffs