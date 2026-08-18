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
- Official **PICO-8 Raspberry Pi dynamic binary** (`pico8_dyn`)

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
