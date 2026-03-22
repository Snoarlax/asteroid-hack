# AsteroidOS-HackWatch

A collection of tools to turn an AsteroidOS-compatible smartwatch into a hacking PDA.

## Currently Implemented Modules

- **Shell Script Runner** - Execute shell scripts from your watch
- **RubberDucky Keyboard Emulator** - Execute RubberDucky scripts that emulate keyboard input via USB HID

## Future Modules

- Radio Hacking
- NFC Hacking

---

## Installation

### Prerequisites

This application is designed to run on [AsteroidOS](https://asteroidos.org/), an open-source Linux distribution for smartwatches.

### Building

1. Set up the AsteroidOS SDK environment following the [official documentation](https://asteroidos.org/wiki/devel/)
2. Clone this repository
3. Build using CMake within the SDK:

```bash
mkdir build && cd build
cmake ..
make
```

### Installing the Application

Transfer the built IPK package to your watch and install it:

```bash
adb push asteroid-hack_1.0.0_armv7vehf-neon.ipk /tmp/
ssh root@192.168.2.15 "opkg install /tmp/asteroid-hack_1.0.0_armv7vehf-neon.ipk"
```

### Installing Example Scripts

The default user on AsteroidOS is `ceres`. Copy the example directory structure to your watch:

```bash
scp -r examples/ceres/* ceres@192.168.2.15:/home/ceres/
```

---

## Usage

### Shell Scripts

The shell script runner executes scripts from the `~/hack_scripts/` directory on your watch.

#### Setup

1. Create the scripts directory on your watch:
   ```bash
   ssh ceres@192.168.2.15 "mkdir -p ~/hack_scripts"
   ```

2. Copy shell scripts to the directory:
   ```bash
   scp your_script.sh ceres@192.168.2.15:~/hack_scripts/
   ```

3. Open the **HackWatch** app and select **Scripts** to run your scripts.

#### Requirements

- Scripts **must have a `.sh` extension** to be recognized
- Scripts are executed using `/bin/sh` - they do **not** need to be marked as executable
- Scripts run with user permissions (the `ceres` user by default)
- To run commands as root, use `sudo`
- To use the Ducky module, use the `keyboard_interface` binary (see `examples/` and read the README for installation instructions for `keyboard_interface`)

#### Example Scripts

See the `examples/ceres/hack_scripts/` directory for reference scripts:

| Script | Purpose |
|--------|---------|
| `whoami.sh` | Tests privileged execution via `sudo` |
| `enable_wifi.sh` / `disable_wifi.sh` | Control WiFi via connmanctl |
| `status_wifi.sh` | Display WiFi status |
| `ip_info.sh` | Show network interface information |
| `setup_keyboard_device.sh` | Configure USB HID gadget for keyboard emulation |
| `remove_keyboard_device.sh` | Remove USB HID gadget configuration |

### Keyboard Scripts (RubberDucky)

The keyboard script runner executes keyboard scripts from `~/keyboard_scripts/` to emulate input via a USB HID gadget device.

#### Setup

1. Ensure you have the required directory structure on your watch:
   ```bash
   ssh ceres@192.168.2.15 "mkdir -p ~/keyboard_scripts ~/utilities"
   ```

2. Copy keyboard scripts:
   ```bash
   scp examples/ceres/keyboard_scripts/* ceres@192.168.2.15:~/keyboard_scripts/
   ```

3. Ensure dependencies are installed:
   - `keyboard_interface` - Processes keyboard scripts and writes to HID device
   - `sudo` - Runs commands as root

4. Open the **HackWatch** app and select **Keyboard** to run your scripts.

#### Kernel Configuration Requirements

The keyboard emulation feature requires USB Gadget subsystem support in the kernel. When building AsteroidOS, add these options to your device's defconfig:

```
CONFIG_USB_CONFIGFS_F_HID=y
```

It is recommended to edit the defconfig directly - I could not get it working by adding a patch. Enabling `CONFIG_USB_CONFIGFS_F_HID` allows userspace to emulate HID devices. This is needed for the Ducky module of asteroid-hackwatch.

#### Keyboard Script Format

Keyboard scripts use a simple text format with lowercase commands. Commands are processed line-by-line:

```
command
command
command
```

**Supported Commands:**

| Command | Description |
|---------|-------------|
| `<text>` | Type the specified text |
| `enter` | Press Enter key |
| `space` | Press Space key |
| `tab` | Press Tab key |
| `escape` | Press Escape key |
| `backspace` | Press Backspace key |
| `delete` | Press Delete key |
| `up` / `down` / `left` / `right` | Arrow keys |
| `home` / `end` | Home/End keys |
| `pageup` / `pagedown` | Page navigation |
| `ctrl+<key>` | Control modifier (e.g., `ctrl+c`) |
| `alt+<key>` | Alt modifier |
| `shift+<key>` | Shift modifier |
| `leftmeta` | Left GUI/Meta/Windows key |
| `rightmeta` | Right GUI/Meta key |
| `ctrl+alt+<key>` | Multiple modifiers |
| `f1` through `f12` | Function keys |
| `delay/sleep/wait <duration>` | Wait (e.g., `sleep 1` or `sleep 100ms`) |

#### Example Keyboard Scripts

See `examples/ceres/keyboard_scripts/`:

| Script | Purpose |
|--------|---------|
| `smalltest` | Basic text input test |
| `whoami` | Opens terminal and runs `whoami` |
| `space_test` | Test spaces in commands |
| `linux_poc` | Linux system enumeration proof-of-concept |
| `fulltest` | Comprehensive keyboard test with all key types |

---

## Directory Structure

### Application Source

```
asteroid-hack/
├── src/                    # Source code
│   ├── main.cpp           # Application entry point
│   ├── CommandRunner.cpp   # Script execution backend
│   └── *.qml              # UI components
├── README.md              # This documentation
└── CMakeLists.txt         # Build configuration
```

### Example Home Directory (on watch)

The `examples/ceres/` directory shows the expected structure for the default AsteroidOS user `ceres`:

```
ceres/                      # User home directory on watch
├── hack_scripts/           # Shell scripts (.sh files)
│   ├── whoami.sh
│   ├── enable_wifi.sh
│   ├── disable_wifi.sh
│   ├── status_wifi.sh
│   ├── ip_info.sh
│   ├── setup_keyboard_device.sh
│   └── remove_keyboard_device.sh
├── keyboard_scripts/       # Keyboard scripts (no extension)
│   ├── smalltest
│   ├── whoami
│   ├── space_test
│   ├── linux_poc
│   └── fulltest
└── utilities/              # Helper utilities
    ├── setup_keyboard_device.sh
    ├── remove_keyboard_device.sh
    └── whoami.sh
```

---

## Limitations

- **Shell Scripts**
  - Scripts must have a `.sh` extension
  - Scripts do not need to be executable (run via `/bin/sh`)
  - Python scripts are **not supported** directly - wrap them in a shell script and place the python file in the ~/utilities directory:
    ```sh
    #!/bin/sh
    python3 your_script.py
    ```
  - Scripts run with application user permissions
  - Output is limited to what the app can display

- **Keyboard Scripts**
  - Require kernel with USB Gadget subsystem and HID support enabled
  - Scripts do **not** use file extensions
  - Commands are lowercase in the script format
  - Require USB OTG cable connected to target device
  - HID device appears as `/dev/hidg0` (USB gadget interface)

---

## USB Gadget Setup

The keyboard scripts rely on Linux USB Gadget subsystem. Before running keyboard scripts, you may need to configure the USB gadget:

```bash
# Setup USB HID keyboard gadget
sudo ~/utilities/setup_keyboard_device.sh

# After use, remove the gadget
sudo ~/utilities/remove_keyboard_device.sh
```

The `setup_keyboard_device.sh` script:
1. Creates a USB HID function at `/sys/kernel/config/usb_gadget/g1/functions/hid.usb0`
2. Writes the HID report descriptor for a standard keyboard
3. Links the function to the USB configuration
4. Binds to the USB Device Controller (UDC)

---

## Troubleshooting

### Scripts not appearing

- Ensure the directory exists at `~/hack_scripts/` or `~/keyboard_scripts/`
- For shell scripts: ensure files have the `.sh` extension
- For keyboard scripts: ensure files have **no extension**
- Check file permissions (read access required)

### Keyboard scripts fail to run

- Verify `/usr/bin/keyboard_interface` exists
- Check that kernel has required USB Gadget options enabled
- Ensure USB cable is connected to target device

### "Error: Failed to start script"

- Check that the script file exists and is readable
- Verify the script syntax is valid

---

## Companion Repository

Additional utilities, build scripts, and pre-compiled binaries can be found in the companion repository:
https://github.com/Snoarlax/asteroid-hackwatch

---

## License

GNU General Public License v3.0
