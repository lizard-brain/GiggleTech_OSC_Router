# Giggletech Haptics Server - README

## Files Included

1. **config.yml**  
   Stores all your device settings and configurations for Giggletech haptics.

2. **config_multiple_devices.yml**  
   An example configuration file for when you have multiple Giggletech devices.

3. **Giggletech_OSCQuery_Installer.exe**  
   The OSCQuery Helper, required for managing OSC communication with VRChat.

4. **giggletech_server_1.3.1.exe**  
   The main Giggletech software that manages communication between VRChat and your device(s).

5. **giggletech_vrc_simulator.exe**  
   A testing tool that allows you to test your devices without needing a VRChat avatar or setup.

---

## Overview

This guide will help you set up and configure your Giggletech device, software, and server for use in VRChat and other compatible platforms. Follow the steps below to get started.

---

## Step 1: Configure Your Giggletech Device

### About Giggletech Devices

**Status LED**:
- **Initial Power-Up**: 3 quick blinks.
- **Normal Operation**: 3 more blinks, 3 buzzes. Light will be off. LED lights up when pat commands are received.
- **Configuration Mode**: Solid LED.

### Enter Configuration Mode
1. Power up the device (3 blinks).
2. Unplug and re-plug it in. The LED will go solid, indicating configuration mode.

### Connect the Device to Wi-Fi
1. Connect to the **"Giggletech_haptics"** Wi-Fi network (password: **giggletech**).
2. Your phone may automatically direct you to the configuration page. If not, open a browser and go to `http://192.168.4.1`.
3. Select your Wi-Fi network from the list and enter your Wi-Fi password.

### IP Setup
- **Automatic (Recommended)**: Uncheck 'Use static IP' and click 'Save'.
- **Static IP (Advanced)**: Set your IP, Gateway, and Subnet manually.

**After saving**: Unplug and re-plug the device to apply settings. The device will blink 3 times and buzz 3 times, indicating a successful connection.

---

## Step 2: Giggletech Software Setup

### Install the Software
1. Download the latest version from the Giggletech GitHub.
2. Run the **Giggletech_OSCQuery_Installer.exe** to install the OSCQuery Helper.

---

## Step 3: Configure the YML File

1. **Find Your Device's IP**:  
   Visit `http://giggletech.local`. If it doesn’t load, ensure the [Bonjour Service](https://developer.apple.com/bonjour/) is installed.

2. **Edit the YML File**:
   - Open **"config.yml"** using Notepad.
   - Replace the default IP (`192.168.1.69`) with the one obtained from `giggletech.local` or your static IP setup.

3. **Multiple Devices**:
   - Add multiple devices to the YML by copying the format and assigning unique IPs.
   - Power up each device separately.

4. **Save Your Changes**:
   After updating, save the file and close the editor.

---

## Step 4: Test Your System

Before entering VRChat:
1. Open the **Giggletech Server** software.
2. Run **giggletech_vrc_simulator.exe** to emulate a VRChat environment.
3. Follow the simulator prompts to confirm your hardware setup.

> **Note**: The Giggletech Server must remain open while using VRChat to receive haptic interactions.

For further assistance, visit our Discord or contact us via email.

---

# Giggletech OSC API Documentation

## Overview
The Giggletech OSC API enables you to control Giggletech hardware, such as GigglePuck and GiggleSpark, by sending Open Sound Control (OSC) messages to the device. This guide explains the OSC paths, how to send messages, and how to interact with the hardware in any programming language that supports OSC libraries.

---

## 1. Requirements

### General Requirements
- **Giggletech Hardware**: GigglePuck, GiggleSpark.
- **OSC-compatible Programming Language**:
  Examples include:
  - Rust
  - Python
  - C++
  - JavaScript
  - Unity (C#)
- **Network Configuration**:  
  Ensure the Giggletech device is on the same local network as the sending device.

---

## 2. OSC Path Definitions
| OSC Address  | Description           | Compatible Devices        |
|--------------|-----------------------|---------------------------|
| `/motor`     | Motor control path    | GigglePuck, GiggleSpark   |

---

## 3. OSC Setup

### How OSC Works
OSC messages are UDP-based and consist of:
- **Target IP**: IP address of the Giggletech device (e.g., `192.168.1.10`).
- **Target Port**: Port to send data to. The default port is **8888**.
- **OSC Path**: The control path `/motor`.
- **Value**: Motor control intensity as an `i32` integer in the range **0-255**.

### Motor Value Details
| Value Range | Behavior         |
|-------------|-----------------|
| `0`         | Motor is off     |
| `255`       | Maximum intensity|

**Scaling Factor**: To ensure motor safety, values are scaled by **0.66**. This accounts for the motor being designed for 3.3V but running on 5V.

**Formula**:
```java
Effective Motor Value = 0.66 * Input Value (0-255)
```
**Example**:
- Input: `255`  
  Effective Output: `168` (scaled).

**Signal Persistence**:
If the OSC signal disconnects, the motor will remain at the last known value until a new signal is received.

> **Recommendation**: Periodically resend values to maintain behavior.

---

## 4. Example Workflow

Follow these steps to send motor values:
1. Identify the target IP address of your Giggletech device (e.g., `192.168.1.10`).
2. Send OSC messages to port **8888** using the path `/motor`.
3. Use values in the range **0-255**, understanding that the output will be scaled by **0.66** for motor safety.

---

## 5. Example Scenarios
| Input Value | Scaled Value (0.66) | Behavior                |
|-------------|---------------------|------------------------|
| `0`         | `0`                 | Motor Off              |
| `128`       | `84`                | Moderate Motor Intensity|
| `255`       | `168`               | Safe Maximum Intensity |

---

## 6. Notes and Best Practices
- **Resend Values**: To maintain motor behavior during connection interruptions, periodically resend motor values.
- **Voltage Scaling**: Do not bypass the **0.66 scaling factor**. It protects motor longevity when running on 5V.
- **Test Values**: Start with lower motor values (e.g., `50`) and gradually increase intensity to identify optimal motor behavior for your setup.

---

## 7. Troubleshooting
| Issue                         | Solution                                                    |
|-------------------------------|------------------------------------------------------------|
| Motor not responding          | Verify IP address, port (8888), and OSC path (`/motor`).    |
| Connection loss or interruptions | Resend values periodically to ensure consistent behavior. |


