# Fix WebHID connection for Keychron M5 on Arch Linux (Chromium)

This is a minimal, step-by-step guide for the exact fix that worked.

Notes:
- Verified on Arch Linux (CachyOS) with Chromium.
- Mouse model: Keychron M5 (USB IDs: `3434:d028` (wireless), `3434:d048` (wired)).
- Replace IDs if your device differs (run `lsusb` to check).

## 1) Enable Web Platform features in Chromium
- Go to: `chrome://flags/#enable-experimental-web-platform-features`
- Set to: Enabled
- Relaunch Chromium

## 2) Add udev rules for the M5
Create a rules file with your device’s vendor/product IDs (this guide uses Q5 HE: `3434:d028` and `3434:d048`):

```bash
sudo tee /etc/udev/rules.d/50-keychron-m5.rules >/dev/null <<'EOF'
# Keychron M5 (WebHID)
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="3434", ATTRS{idProduct}=="d028", MODE="0666", TAG+="uaccess", TAG+="udev-acl"
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="3434", ATTRS{idProduct}=="d048", MODE="0666", TAG+="uaccess", TAG+="udev-acl"
EOF
```

Apply the rules and replug:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
# Unplug and replug the keyboard USB cable
```

## 3) Connect in the [Keychron web tool](https://launcher.keychron.com/)
- Use Chromium (not Firefox).
- Mouse either wireless or wired USB mode.
- Open the Keychron web configurator/launcher, click Connect, select the device.

If your model is different:
- Find IDs with `lsusb` (format `vvvv:pppp`).
- Replace `3434` (vendor) and `d028` (product) in the udev rule accordingly.
- The second udev rule is only needed if you can connect either wired or wirelessly
