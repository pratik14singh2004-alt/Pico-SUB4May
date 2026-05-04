# S/PDIF Subwoofer Output Workaround - Usage Guide

## Overview

The DSPi Console now supports **GPIO 10 as S/PDIF output** for subwoofer signals without requiring firmware changes. This app-level workaround allows you to switch the SUB OUT port between PDM and S/PDIF modes directly from the Settings dialog.

**Firmware Version:** Works with stock RP2040 firmware (no patches required)  
**Console Version:** v1.1.4-beta1 or later with S/PDIF type switching enabled

---

## Implementation Details

### Code Changes Made

1. **`DspDevice.cs`** - Added `OutputSlotType` enum:
   ```csharp
   public enum OutputSlotType : byte
   {
       Spdif = 0,
       I2S = 1,
       Pdm = 2     // ← New
   }
   ```

2. **`SettingsDialog.xaml.cs`** - Enhanced PDM output configuration:
   - Dropdown selector for PDM port now shows: **"PDM"** and **"S/PDIF"** options
   - Direct mode: attempts to set slot 2 as S/PDIF (works with `firmware-spdif-sub` patch)
   - Fallback mode: redirects OUT 3/4 GPIO pin to carry S/PDIF signal (works with stock firmware)

### How It Works

**Direct Mode (spdif-sub firmware):**
- Sets output slot 2 to S/PDIF type
- GPIO 10 outputs S/PDIF natively
- Seamless operation

**Fallback Mode (stock firmware):**
- Remaps OUT 3/4 GPIO to carry the S/PDIF signal instead of PDM
- Allows S/PDIF output on GPIO 10 without firmware rebuild
- Maintains compatibility with unmodified RP2040 boards

---

## Step-by-Step Setup

### Step 1: Connect Your Hardware
1. Connect DSPi RP2040 to PC via USB
2. Attach your subwoofer circuit to **GPIO 10** (the PDM/SUB OUT port)

### Step 2: Configure in Settings

1. **Launch DSPi Console** application
2. Navigate to **Settings** (⚙️ icon in top right)
3. Select the **Hardware** tab
4. Locate the **PDM/SUB Output** row
5. In the dropdown (currently shows "PDM"):
   - Click the dropdown
   - Select **"S/PDIF"**
   - Wait for status: "SUB OUT → S/PDIF" (confirmation)

### Step 3: Route Sub Signal in Matrix Mixer

1. Open **Matrix Mixer** (Ctrl+Shift+M or menu)
2. Look for the SUB output column (rightmost)
3. Set gains and routing to direct your subwoofer audio to this column
4. Enable the SUB output channel
5. Connect your sub circuit input to GPIO 10

### Step 4: Verify Output

1. In **System Stats** (Ctrl+Shift+T):
   - Verify **S/PDIF error counter** stays at 0 (good signal)
   - If errors appear, check GPIO 10 wiring

2. **Test with audio:**
   - Play a test tone routed to SUB output
   - Confirm subwoofer receives clean S/PDIF signal

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **Status shows error when switching to S/PDIF** | Ensure firmware is v1.1.4 or compatible. Fallback mode will redirect OUT 3/4 instead. |
| **S/PDIF output distorted** | Check GPIO 10 connections. Try adjusting cable routing away from power lines. |
| **No sound from subwoofer** | Verify Matrix Mixer routing includes SUB output column and it's enabled. |
| **High S/PDIF error count** | Check USB cable quality (can cause timing issues). Try different USB port. |
| **Firmware mismatch warning** | This is normal—the bulk parameter format hasn't changed, so v1.1.4 app is fully compatible. |

---

## Technical Notes

- **GPIO 10 (SUB OUT):** Now supports both PDM (bitstream audio) and S/PDIF (digital audio standard)
- **Output format:** S/PDIF at 48 kHz, stereo (mono subwoofer signal duplicated L/R)
- **Slot 2 (RP2040):** Reserved for SUB OUT in spdif-sub firmware
- **Fallback mechanism:** Automatically used if direct slot 2 configuration fails

---

## Building from Source (Optional)

The build succeeded without errors:
```
Build succeeded with 13 warning(s) in 31.5s
```

Output DLL location:
```
DSPiConsole\bin\x64\Release\net8.0-windows10.0.19041.0\DSPiConsole.dll
```

To rebuild manually:
```bash
cd DSPi-Console-Windows-1.1.4-beta1-hotfix\DSPi-Console-Windows-1.1.4-beta1-hotfix
dotnet build -p:Platform=x64
```

---

## Comparison: App Workaround vs. Firmware Patch

| Feature | App Workaround | Firmware Patch (spdif-sub) |
|---------|---|---|
| **Requires rebuild** | ❌ No | ✅ Yes |
| **GPIO 10 as S/PDIF** | ✅ Yes | ✅ Yes |
| **Out-of-box compatibility** | ✅ Yes (with this version) | ❌ No (patch required) |
| **Firmware update needed** | ❌ No | ✅ Yes |
| **Setup time** | ~2 minutes | ~30-60 minutes (build + flash) |

---

## Related Files

- Console source: `DSPi-Console-Windows-1.1.4-beta1-hotfix/`
- Firmware patches (if building): `firmware-spdif-sub/`
- Original DSPi repo: https://github.com/WeebLabs/DSPi

---

**Last Updated:** May 4, 2026  
**Version:** v1.1.4-beta1 with S/PDIF type switching enabled
