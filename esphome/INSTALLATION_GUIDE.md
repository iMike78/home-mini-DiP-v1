# MiciMike Factory Firmware - Complete Guide

## 1. Project Structure Setup

### File placement in GitHub repo:

```
home-mini-DiP-v1/
├── .github/
│   └── workflows/
│       └── build.yml                    # Automatic build
├── esphome/
│   ├── micimike-factory.yml            # Factory firmware
│   └── micimike-voice.yml              # Full firmware (current config)
├── manifests/
│   ├── manifest.json                    # Production manifest
│   └── manifest-beta.json               # Beta manifest
└── README.md
```

## 2. Setup Steps

### 2.1. File Creation

1. **micimike-factory.yml** - Already created ✓
2. **micimike-voice.yml** - Your current full configuration
3. **manifest.json** - GitHub releases manifest
4. **manifest-beta.json** - Beta version manifest
5. **GitHub Actions workflow** - Automatic build

### 2.2. GitHub Repository Setup

```bash
# Add files
git add esphome/micimike-factory.yml
git add esphome/micimike-voice.yml
git add manifests/manifest.json
git add manifests/manifest-beta.json
git add .github/workflows/build.yml

# Commit
git commit -m "Add factory firmware structure"

# Push
git push origin main
```

### 2.3. Create First Release

```bash
# Create tag
git tag v0.9.0

# Push tag
git push origin v0.9.0
```

GitHub Actions will automatically:
- Build factory and full firmware
- Create the release
- Upload binary files and manifests

## 3. Using Factory Firmware

### 3.1. Initial Installation (USB)

```bash
# Flash factory firmware
esphome run esphome/micimike-factory.yml
```

### 3.2. BLE Improv Provisioning

1. **Power on device**
   - LED ring animation indicates BLE Improv mode

2. **ESPHome app or Home Assistant**
   - Bluetooth search
   - "micimike" device appears

3. **Touch center button**
   - Press button for authentication
   - LED feedback

4. **WiFi configuration**
   - Select SSID
   - Enter password
   - Connect

### 3.3. Automatic Update

After connection, automatically:

1. **5 sec delay** - Allow improv results transmission
2. **BLE disable** - Bluetooth turns off
3. **LED refresh** - control_leds script runs
4. **HTTP OTA** - Download manifest from GitHub
5. **Firmware update** - Install full firmware
6. **Reboot** - Full functionality available

## 4. OTA Updates

### 4.1. Production Updates

When creating new release:

```bash
git tag v0.9.1
git push origin v0.9.1
```

Device automatically detects and installs.

### 4.2. Beta Updates

In Home Assistant:

1. **Enable Beta Firmware switch**
2. **Check Update entity**
3. **Install update**

### 4.3. Return to Production

1. **Disable Beta Firmware switch**
2. **Check Update entity**
3. **Install production version**

## 5. LED Feedback

### BLE Improv Mode
- `improv_ble_in_progress = true`
- Special LED animation

### Initialization
- `init_in_progress = true`
- Waiting animation
- Auto-disable after 10 minutes

### WiFi Connection
- Connected: `control_leds` runs
- Disconnected: BLE enable

### Center Button Touch
- LED brightness increase
- Visual feedback

## 6. Manifest URL Structure

### Production
```
https://github.com/iMike78/home-mini-DiP-v1/releases/latest/download/manifest.json
```

### Beta
```
https://github.com/iMike78/home-mini-DiP-v1/releases/latest/download/manifest-beta.json
```

## 7. Troubleshooting

### BLE Improv Not Working
1. Check touch threshold (currently 5000)
2. ESP32 touch calibration may be needed
3. Check setup mode logs

### OTA Update Failed
1. GitHub releases accessibility
2. Manifest.json correctness
3. Binary files accessibility
4. WiFi connection stability

### LED Not Responding
1. `control_leds` script error
2. Global variables initialization
3. Binary sensor states check

## 8. Touch Center Button Calibration

If authentication doesn't work:

```yaml
esp32_touch:
  setup_mode: True  # Enable temporarily
  # ... other settings
```

Based on log values, adjust:

```yaml
binary_sensor:
  - platform: esp32_touch
    id: center_button
    pin: GPIO3
    threshold: XXXX  # Calibrate this value
```

## 9. Factory Reset

Hold touch center button for 22 seconds:
- 10 sec: Warning (sound + LED)
- 22 sec: Confirmation (sound + red LED)
- Release: Execute factory reset

## 10. Special Features

### Easter Egg
H-A morse code: 
- Tick animation
- Tada sound
- Rainbow LED effect

### Volume Tick
- Custom sound during volume change
- LED volume display

### Timer
- Timer ring mode
- Stop wake word active
- Auto-stop after 15 minutes

## 11. Next Steps

1. ✓ Factory firmware file created
2. ✓ Manifest.json files
3. ✓ GitHub Actions workflow
4. □ Push files to GitHub
5. □ Create first release
6. □ Test factory firmware
7. □ Touch button calibration
8. □ Test OTA updates
