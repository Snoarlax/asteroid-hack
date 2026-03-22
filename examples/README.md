# Example Directory Structure

This directory contains an example home directory structure for the default AsteroidOS user `ceres`.

## Structure

```
ceres/
├── hack_scripts/           # Shell scripts (.sh extension required)
├── keyboard_scripts/       # Keyboard scripts (no extension)
└── utilities/             # Helper utilities for privileged operations
```

## Installation

Copy the entire `ceres/` directory to your watch:

```bash
scp -r ceres/* ceres@<watch-ip>:/home/ceres/
```

## Contents

### hack_scripts/

Shell scripts executed by the HackWatch app. Scripts must have a `.sh` extension.

- `whoami.sh` - Test privileged execution
- `enable_wifi.sh` / `disable_wifi.sh` - WiFi control via connmanctl
- `status_wifi.sh` - WiFi status
- `ip_info.sh` - Network interface info
- `setup_keyboard_device.sh` - Configure USB HID gadget
- `remove_keyboard_device.sh` - Remove USB HID gadget

### keyboard_scripts/

Keyboard scripts for USB HID keyboard emulation. Scripts have **no file extension**.

- `smalltest` - Basic text input
- `whoami` - Terminal + whoami command
- `space_test` - Space character test
- `linux_poc` - Linux system enumeration
- `fulltest` - Comprehensive keyboard test

### utilities/

Helper scripts requiring root privileges (run via `runas`):

- `runas` - Execute scripts/commands as root
- `setup_keyboard_device.sh` - USB gadget setup (root)
- `remove_keyboard_device.sh` - USB gadget teardown (root)
- `whoami.sh` - Test script (root)

## Requirements

The `runas` utility and `keyboard_interface` binary must be compiled for ARM architecture and placed in the `utilities/` directory.

#TODO: INSTRUCTIONS for runas setup

- compile runas using bitbake and the provided runas bitbake script (should be automatically compiled as a dependency for asteroid-hackwatch, but can be manually compiled using bitbake asteroid-runas)
- make sure root owns runas and the setuid bit is set on it
- place it in the ~/utilities folder

#TODO: INSTRUCTIONS for keyboard_interface setup

- compile keyboard_interface using the bitbake script (should be automatically compiled as a dependency for asteroid-hackwatch, but can be manually compiled using bitbake asteroid-keyboard-interface)
- place the binary in the utilities folder

https://github.com/Snoarlax/asteroid-hackwatch
