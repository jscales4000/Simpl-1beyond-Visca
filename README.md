# Crestron SIMPL+ VISCA Camera Control Module

## Overview
This SIMPL+ module provides comprehensive control of VISCA-compatible PTZ cameras through a Crestron control system. The module supports a wide range of camera functions including pan/tilt, zoom, focus, presets, and tracking.

## Features

### Camera Control
- **Pan/Tilt Control**: Smooth movement with adjustable speed
- **Zoom Control**: Standard, variable speed, direct, and absolute positioning
- **Focus Control**: Manual and auto-focus modes with one-push auto-focus
- **Preset Management**: Support for standard presets (1-5) and extended presets (0-108)

### Advanced Features
- **Camera Tracking**: Start/stop tracking with status feedback
- **Power Management**: Power on/off with status polling
- **Preset Recall Feedback**: Tracks last recalled preset
- **Menu Control**: Access to camera OSD menu

### Feedback
- Power status feedback
- Tracking status feedback
- Last recalled preset feedback

## File Versions
- `VISCA_Camera_v1.usp`: Initial implementation with basic controls
- `VISCA_Camera_v2.usp`: Enhanced with additional features and feedback
- `VISCA_Camera_v3.usp`: Latest version with comprehensive zoom and focus controls

## Usage
1. Import the desired version into your SIMPL Windows program
2. Connect the serial output to your VISCA camera's RS-232 port
3. Map the digital, analog, and serial inputs/outputs as needed
4. Configure the camera ID if using multiple cameras

## Requirements
- Crestron 3-Series or 4-Series control system
- VISCA-compatible PTZ camera
- RS-232 or RS-422 connection (depending on camera model)

## Documentation
- `VISCA Command doc`: Reference for VISCA protocol commands
- `Crestron_SIMPL+_Guide_for_AI.md`: Programming guide for SIMPL+

## License
[Specify your license here]

## Support
For support, please open an issue in the GitHub repository.
