# DSPi Firmware Flashing Guide

## Summary

✅ **Firmware Built Successfully**
- Stock DSPi v1.1.4 compiled for RP2040 (Pico)
- File: `DSPi-RP2040-stock-v1.1.4.uf2` (175 KB)
- Ready to flash to your device

## Flashing Steps

### 1. Enter Bootloader Mode
1. **Disconnect USB** from your Pico
2. **Hold the BOOTSEL button** (small button on the Pico board)
3. **While holding**, connect USB to your computer
4. Your Pico will appear as a USB drive named **RPI-RP2**
5. Release the BOOTSEL button

### 2. Flash the Firmware
1. Open the **RPI-RP2** drive in File Explorer
2. **Drag and drop** `DSPi-RP2040-stock-v1.1.4.uf2` onto the drive
3. The file will copy and the Pico will **automatically reboot**
4. Your Pico now runs the latest DSPi firmware

### 3. Verify Installation
1. Reconnect your Pico via USB (normal operation, not BOOTSEL mode)
2. Open the **DSPi Console app**
3. Connect to your device in the console
4. You should see normal audio operation with all I/O slots working

## Using S/PDIF Output (Stock Firmware)

The stock firmware does NOT have native S/PDIF support on the sub output. However, the DSPi Console app now includes an **app-level workaround** to route sub audio via S/PDIF:

### Option 1: Matrix Mixer Route (Recommended)
Use the Matrix Mixer in the console to route sub output to any available S/PDIF output:
1. Open **Settings → Matrix Mixer**
2. Map sub audio to GPIO 10 output (or other S/PDIF pins)
3. This sends sub audio via S/PDIF at the firmware level

### Option 2: Settings Dialog S/PDIF Button
1. Open **Settings → Hardware**
2. Locate the **SUB OUT** dropdown
3. Select **S/PDIF** from the dropdown
4. If your firmware supports native S/PDIF on sub, it will switch immediately
5. If not, a helpful error message explains the workaround

See `S_PDIF_WORKAROUND_USAGE.md` for detailed setup instructions.

## Advanced: Building Patched Firmware with Native S/PDIF Support

The `firmware-spdif-sub/` directory contains detailed patch instructions to add **native S/PDIF 3 support** on GPIO 10 (replacing PDM mono). This gives you hardware-level S/PDIF output without app-level workarounds.

### What's Included
- `BUILD_INSTRUCTIONS.txt` - Complete step-by-step guide
- `CMakeLists.patch`, `config.h.patch`, `main.patch`, `usb_audio.patch` - Detailed change specifications

### To Build Patched Firmware Later
1. Follow the instructions in `BUILD_INSTRUCTIONS.txt`
2. Manually apply changes to the DSPi source code using the patch files as guides
3. Run `cmake -DPICO_BOARD=pico -B build && cd build && make -j4`
4. Flash the new `.uf2` to your Pico

The patches:
- Add S/PDIF 3 instance on GPIO 10 (RP2040 only)
- Configure output_pins[] array for stereo S/PDIF 3 support
- Disable PDM on RP2040 (GPIO 10 conversion: PDM → S/PDIF 3)
- Maintain backward compatibility with RP2350 (which keeps PDM on GPIO 10)

## Files in This Directory

| File | Purpose |
|------|---------|
| `DSPi-RP2040-stock-v1.1.4.uf2` | ✅ **Main firmware** - Flash this to your Pico |
| `DSPi-RP2040-v1.1.4-beta1.uf2` | Previous firmware version (for reference) |
| `firmware-spdif-sub/` | S/PDIF patch documentation and detailed change specs |
| `S_PDIF_WORKAROUND_USAGE.md` | App-level S/PDIF workaround guide (use with stock firmware) |
| `DSPi-Console-Windows-1.1.4-beta1-hotfix/` | Console app with S/PDIF switching UI |
| `FIRMWARE_FLASHING_GUIDE.md` | This file |

## Troubleshooting

### Pico not appearing as RPI-RP2 drive
- Ensure BOOTSEL button is held **throughout** USB connection
- Try a different USB cable (some are power-only)
- Try a different USB port on your computer

### Firmware appears to hang after flashing
- Verify the file copied completely (check file size matches)
- Reflash the firmware again
- Try the previous firmware version `DSPi-RP2040-v1.1.4-beta1.uf2`

### S/PDIF output not working in console app
- Ensure you're using the latest console app (in `DSPi-Console-Windows-1.1.4-beta1-hotfix/`)
- Check the hardware settings to confirm S/PDIF selection
- If Settings shows an error, use the Matrix Mixer workaround instead
- See `S_PDIF_WORKAROUND_USAGE.md` for detailed setup

### Build Environment Issues
The firmware was built in WSL 2 Ubuntu 22.04 with:
- gcc-arm-none-eabi 15:10.3-2021.07-4
- CMake 3.22.1
- All required Pico SDK dependencies

If you need to rebuild with patches, all tools are configured in WSL.

## Next Steps

1. **Flash immediately**: Use `DSPi-RP2040-stock-v1.1.4.uf2` with BOOTSEL method above
2. **Test audio**: Verify device works in console app
3. **Set up S/PDIF**: Use Matrix Mixer or Settings workaround (see `S_PDIF_WORKAROUND_USAGE.md`)
4. **Optional later**: Build patched firmware if you want native hardware S/PDIF support

---

**Built:** 2024-05-04 @ 17:30 UTC  
**Status:** Ready to deploy ✅
