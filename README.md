# AutoVent 2.1 Remix for the SUNLU AMS Heater

Firmware for the AutoVent 2.1 remix: a rotary vent cover driven by a 107-tooth sector gear off the original 24T servo gear (about 4.5:1), with a self-calibrating FEETECH FS90-FB feedback servo.

This is a fork of [Kevin G's AutoVent 2.0](https://github.com/kgregg384/SUNLU_AMS_Heater_AutoVent). The heater and fan detection, learning mode, 3-minute fan delay, standby and button behavior are his and are unchanged. Only the servo control was rewritten for the rotary cover.

- **Printable files and build guide:** MakerWorld, AutoVent 2.1 Remix by Huntington Builds
- **Original design:** [AutoVent 2.0 on MakerWorld](https://makerworld.com/en/models/2344707-autovent-2-0-for-sunlu-ams-heater)
- **Electronics, PCBs and DIY kits:** [3dcreator.shop](https://3dcreator.shop)

## Files

| File | What it is |
|---|---|
| `firmware/AutoVent_v4_RP2040_2.2.1.uf2` | Ready-to-flash firmware for the Seeed XIAO RP2040 |
| `AutoVent_v4/AutoVent_v4.ino` | Source for v2.2.1 (open the `AutoVent_v4` folder in the Arduino IDE) |

Kevin's original files are left in place. The `.uf2` files without `v4` in the name are for his slider version, not this remix.

## Hardware

- Seeed XIAO RP2040
- AutoVent PCB with ACS758 current sensor and ADS1115 ADC ([3dcreator.shop](https://3dcreator.shop))
- FEETECH FS90-FB feedback servo (must be the 4-wire feedback version)
- Illuminated momentary push button
- 5 V, 2 A power supply

Pins are unchanged from the original: D0 servo, D3 button, D10 LED, SDA/SCL to the ADS1115 (A0 current sensor, A1 servo feedback).

## Flashing the firmware

You need a computer and a USB-C data cable. No software to install.

1. Unplug the 5 V barrel jack so the board is only powered from USB.
2. Remove the lid so you can reach the XIAO.
3. Hold the small **B** (BOOT) button on the XIAO, plug the USB-C cable into your computer, then release **B**. If the cable is already plugged in: hold **B**, tap **R** (RESET), then release **B**.
4. A drive named **RPI-RP2** appears on your computer.
5. Drag `AutoVent_v4_RP2040_2.2.1.uf2` onto the RPI-RP2 drive. The board reboots on its own and the drive disappears. That means it worked.
6. Unplug USB, reinstall the lid, and plug the 5 V supply back in.

If RPI-RP2 doesn't show up, try a different cable. Many USB-C cables are charge-only.

## Servo self-calibration (no computer needed)

Runs automatically on the first boot after flashing. To run it again later, hold the button while plugging in the 5 V supply.

1. The servo moves to its midpoint and the light flashes fast. Turn the vent cover halfway between its two stops, press the 24T gear on (or leave it if it is already installed), then press the button.
2. The light flashes slowly while the servo finds both slot stops using the feedback wire. Keep hands clear.
3. The servo moves to the end it thinks is OPEN and the light double-blinks.
   - Vent is open: short press to accept.
   - Vent is closed: hold the button 2 s until the light goes solid, then release. It swaps ends and asks again.
4. The vent closes, the light gives 2 quick flashes, and the endpoints are saved.

If the light flashes fast for about 5 seconds, calibration failed. Reinstall the gear with the cover halfway between the stops and run it again.

## Button (normal use, unchanged from the original)

| Press | Action |
|---|---|
| Short press | Recalibrate |
| Hold 2 s | Standby on / off |
| Hold 5 s | Current-threshold learning mode |

## What changed from the original firmware (v4 servo layer)

- Servo pulses limited to the FS90 rating (900–2100 µs); nothing outside is ever sent.
- Open and closed are stored as pulse widths and saved to flash.
- Moves ramp to the target, settle, then detach (no holding torque, like the original).
- Calibration moves only between the configured endpoints instead of driving to 0/180°, which stalled against the mechanism.
- After each move the feedback wire is checked; a miss is reported as a possible bind.
- New serial commands: `M` (mid), `[` `]` (trim open), `{` `}` (trim closed), `X` (swap), `W` (save endpoints), `P` (print servo settings), `A` (self-calibrate), `H` or `?` (help).

## License

MIT, same as the original. Original work by Kevin G; v4 servo changes by Huntington Builds.
