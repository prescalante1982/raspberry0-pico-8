# 🕹️ PICO-8 Portable Handheld & Desktop Clock

A lightweight, optimized, and fully featured custom OS setup for turning a **Raspberry Pi Zero / Zero W** paired with a **1.44" SPI LCD HAT (128x128)** into a dedicated PICO-8 handheld console and retro desktop clock.

---

## 🌟 Key Features

- **C-based Framebuffer Streamer (`lcd_stream_c`)**: Reads directly from `/dev/fb0` via `mmap` and transfers 32bpp/16bpp frames to the 128x128 ST7735 SPI display using hardware-accelerated `lgpio` at 12 MHz (~6% CPU usage).
- **Zero-Idle-CPU Input Mapping (`pico8_gpio_controller.py`)**: Uses Linux `uinput` and `gpiozero` hardware interrupts to map GPIO directional pad and action buttons directly to virtual keyboard strokes (`Z`, `X`, `ESC`, Arrows) with zero CPU overhead.
- **Custom Retro Launcher (`pico8_launcher.py`)**:
  - **Boot Splash Screen**: Custom loading bar on system boot.
  - **Retro Desktop Clock**: Displays local time (`HH:MM`) in bright PICO-8 yellow with blinking colon and auto NTP synchronization.
  - **Network Manager Integrations**: Scans local Wi-Fi networks and provides an interactive 7x6 on-screen virtual keyboard (with caps, symbols, backspace, and submit) navigated entirely via D-pad.
  - **Interactive PWM Brightness Control**: Dedicated menu allowing 10% to 100% backlight adjustment via D-pad.
  - **Safe Shutdown**: Holding the D-pad Center button for 5 seconds on the main screen cleanly shuts down the Raspberry Pi (`sudo poweroff`) to prevent MicroSD corruption.
- **Power Optimizations**: Automatically disables Bluetooth and HDMI output (`vcgencmd display_power 0`) to maximize battery runtime.

---

## 🛠️ Hardware Requirements & Pinout

### Required Components
- **Raspberry Pi Zero / Zero W / Zero 2 W**
- **1.44-inch SPI LCD Display Module (128x128, ST7735 controller)** (e.g., Waveshare 1.44inch LCD HAT)
- MicroSD Card (8GB+) with **Raspberry Pi OS (32-bit / Lite or Desktop)**
- Official **PICO-8 Raspberry Pi dynamic binary** (`pico8_dyn`) - Not included, buy your license in https://www.lexaloffle.com
  
### GPIO Pin Mapping

| Component | Function | GPIO Pin (BCM) |
| :--- | :--- | :--- |
| **Joystick** | UP | `GPIO 6` |
| **Joystick** | DOWN | `GPIO 19` |
| **Joystick** | LEFT | `GPIO 5` |
| **Joystick** | RIGHT | `GPIO 26` |
| **Joystick** | CENTER (Press) | `GPIO 13` |
| **Button** | KEY 1 (Action O / Select) | `GPIO 21` |
| **Button** | KEY 2 (Action X / Backspace) | `GPIO 20` |
| **Button** | KEY 3 (ESC / Pause) | `GPIO 16` |
| **LCD** | Reset (RST) | `GPIO 27` |
| **LCD** | Data/Command (DC) | `GPIO 25` |
| **LCD** | Backlight (PWM BL) | `GPIO 24` |

---

## 🏗️ Technical Architecture & Workflow

+-----------------------------------------------------------------------+
|                            SYSTEM BOOT                                |
+-----------------------------------------------------------------------+
|
v
+-----------------------------------------------------------------------+
|                     pico8_launcher.py (Python)                       |
|  - Displays Splash / Boot animation                                   |
|  - Runs Desktop Clock, IP indicator & Wi-Fi scanner                  |
|  - Handles PWM Backlight adjustment menu                              |
|  - Detects 5s press on D-Pad Center for safe OS poweroff             |
+-----------------------------------------------------------------------+
|
Press KEY1 (Start)
|
v
+----------------------------------+------------------------------------+
|                                  |                                    |
v                                  v                                    v
+------------------------+ +-------------------------+ +-----------------+
| pico8_gpio_controller  | |      lcd_stream_c       | |   pico8_dyn     |
| (uinput GPIO mapping)  | |  (C / mmap framebuffer) | | (PICO-8 Engine) |
+------------------------+ +-------------------------+ +-----------------+

---

## 🚀 Installation & Setup

### 1. Fresh Raspberry Pi OS Setup
Install Raspberry Pi OS on your MicroSD card and ensure SSH and Wi-Fi are configured.

### 2. Copy the PICO-8 Binary
Obtain the official PICO-8 Raspberry Pi build from [Lexaloffle](https://www.lexaloffle.com/pico-8.php) and copy the `pico8_dyn` binary to your home directory:

/home/pi/pico8_dyn

chmod +x /home/pi/pico8_dyn

3. Run the Automated Master Installer
Download and run the automated setup script over SSH:

Bash
wget -O setup_pico8_master.sh [https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/setup_pico8_master.sh](https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/setup_pico8_master.sh)
sudo bash setup_pico8_master.sh
(Or copy the script directly to /home/pi/setup_pico8_master.sh and execute sudo bash setup_pico8_master.sh).

The script automatically performs:
System updates and dependency installations (lgpio, gpiozero, uinput, network-manager, PIL, xorg).
Timezone setting to America/Guatemala (UTC-6) with active NTP sync.
Power saving optimizations (disabling Bluetooth overhead).
Compilation of lcd_stream.c into an optimized C binary.
Setup of input event listeners and systemd autostart services (pico8.service).

4. Reboot
Bash
sudo reboot

🎮 How to Use
Main Menu / Clock Screen
KEY 1: Launch PICO-8 (SPLORE mode).
KEY 2: Open Wi-Fi scanner & network manager.
KEY 3: Open Backlight Brightness Control menu.
D-Pad CENTER (Hold 5s): Safe System Shutdown (sudo poweroff).
Wi-Fi Menu & Virtual Keyboard
Use the D-Pad to highlight networks or characters on the 7x6 keyboard.
KEY 1: Select / Input letter.
KEY 2: Backspace.
D-Pad CENTER / KEY 3: Cancel / Go Back to Main Screen.
Keyboard Special Keys:
^: Switch layout (Lowercase → Uppercase → Symbols).
<: Backspace.
=: Submit Password & Connect.
In-Game Controls
D-Pad: Directional movement.
KEY 1: Button O (Z).
KEY 2: Button X (X).
KEY 3: Pause Menu / ESC (ESC).
📁 Repository Structure


