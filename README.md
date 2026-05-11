# Razer-Blade-15-2023-Internal-Speakers-Fix-for-Linux
This script fixes the "silent internal speakers" issue on Razer Blade 15 (2023) models (and similar) running Linux.

### The Problem

On many recent Razer laptops, the internal audio amplifier is not properly initialized by the Linux kernel. Even if the sound card is detected, no sound comes out of the speakers because the hardware is "asleep."

### The Solution

This script uses `hda-verb` to send direct commands to the audio codec to wake up the amplifier. It includes **dynamic card detection**, so it works even if you have an external USB sound card or DAC plugged in.

---

## Prerequisites

You need the `alsa-tools` package to use the `hda-verb` command.

**Ubuntu / Debian / Pop!_OS:**

```bash
sudo apt update && sudo apt install alsa-tools

```

**Fedora:**

```bash
sudo dnf install alsa-tools

```

**Arch Linux:**

```bash
sudo pacman -S alsa-utils

```

---

## Setup Instructions

### 1. Install the Script

First, move the script to a system directory and make it executable:

```bash
sudo cp RB15_2023_internal_speakers_fix.sh /usr/local/bin/fix_audio.sh
sudo chmod +x /usr/local/bin/fix_audio.sh

```

### 2. Create the Systemd Service

To make the fix permanent (so it runs automatically at boot and after sleep), create a service file:

```bash
sudo nano /etc/systemd/system/fix-audio.service

```

Paste the following content into the file:

```ini
[Unit]
Description=Fix Audio Razer Blade Internal Speakers
After=multi-user.target suspend.target hibernate.target

[Service]
Type=oneshot
ExecStart=/usr/local/bin/fix_audio.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target suspend.target hibernate.target

```

*Save and exit (Ctrl+O, Enter, Ctrl+X).*

### 3. Enable the Service

Run these commands to activate the fix:

```bash
sudo systemctl daemon-reload
sudo systemctl enable fix-audio.service
sudo systemctl start fix-audio.service

```

---

## Troubleshooting

**How do I check if it's working?**
Run `systemctl status fix-audio.service`. You should see `Active: active (exited)` in green.

**The sound is still not coming out?**

1. Open `alsamixer` in your terminal.
2. Press **F6** and select `sof-hda-dsp`.
3. Ensure the **Master** and **Speaker** channels are not muted (they should show `OO`, not `MM`).

---

## Credits

This fix is based on various community efforts to bring full audio support to Razer laptops on Linux. Feel free to contribute or report issues!
