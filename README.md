# Alluno Hardware Input Driver (AllunoHInpuD)

Windows KMDF driver for Kernel-level input emulation.

## Requirements

- Windows 10+ (64-bit)

## Features

- Kernel-level keyboard and mouse
- Multi-device support (ALL filter devices)
- PS/2 scan codes with E0 extended key support
- Absolute and relative mouse positioning
- Mouse buttons, vertical wheel, horizontal wheel

## IOCTL Reference

### Control Devices

| Device Path | Type |
|---|---|
| `\\.\AllunoHInpuDKbd` | Keyboard |
| `\\.\AllunoHInpuDMou` | Mouse |

### IOCTL Codes

| IOCTL | Value | Description |
|---|---|---|
| `IOCTL_ALLUNO_INJECT` (Keyboard) | `CTL_CODE(0x0B, 0x820, 0, 0)` = `0x000B2080` | Inject keyboard input |
| `IOCTL_ALLUNO_INJECT` (Mouse) | `CTL_CODE(0x0F, 0x820, 0, 0)` = `0x000F2080` | Inject mouse input |

### Keyboard Input (KEYBOARD_INPUT_DATA, 12 bytes)

| Offset | Size | Field | Description |
|---|---|---|---|
| 0 | 2 | UnitId | Device unit (0) |
| 2 | 2 | MakeCode | PS/2 Set 1 scan code |
| 4 | 2 | Flags | `0x00`=key down, `0x01`=key up, `0x02`=E0 prefix |
| 6 | 2 | Reserved | 0 |
| 8 | 4 | ExtraInformation | 0 |

**Flags** can be combined: `0x03` = E0 + key up (e.g. Right Ctrl release).

### Mouse Input (MOUSE_INPUT_DATA, 24 bytes)

| Offset | Size | Field | Description |
|---|---|---|---|
| 0 | 2 | UnitId | Device unit (0) |
| 2 | 2 | Flags | `0x00`=relative, `0x01`=absolute, `0x03`=absolute+virtual desktop |
| 4 | 2 | ButtonFlags | See button flags below |
| 6 | 2 | ButtonData | Wheel delta (signed, 120 = one notch) |
| 8 | 4 | RawButtons | 0 |
| 12 | 4 | LastX | X delta (relative) or 0-65535 (absolute) |
| 16 | 4 | LastY | Y delta (relative) or 0-65535 (absolute) |
| 20 | 4 | ExtraInformation | 0 |

### Button Flags

| Flag | Value | Description |
|---|---|---|
| `LEFT_BUTTON_DOWN` | `0x0001` | Left button press |
| `LEFT_BUTTON_UP` | `0x0002` | Left button release |
| `RIGHT_BUTTON_DOWN` | `0x0004` | Right button press |
| `RIGHT_BUTTON_UP` | `0x0008` | Right button release |
| `MIDDLE_BUTTON_DOWN` | `0x0010` | Middle button press |
| `MIDDLE_BUTTON_UP` | `0x0020` | Middle button release |
| `WHEEL` | `0x0400` | Vertical scroll (ButtonData = delta) |
| `HWHEEL` | `0x0800` | Horizontal scroll (ButtonData = delta) |

### Common Scan Codes (PS/2 Set 1)

| Key | Code | Key | Code | Key | Code |
|---|---|---|---|---|---|
| Esc | `0x01` | Tab | `0x0F` | Space | `0x39` |
| A-Z | `0x1E`-`0x32` | 1-0 | `0x02`-`0x0B` | F1-F12 | `0x3B`-`0x58` |
| Enter | `0x1C` | Backspace | `0x0E` | CapsLock | `0x3A` |
| LShift | `0x2A` | RShift | `0x36` | LCtrl | `0x1D` |
| LAlt | `0x38` | LWin | E0+`0x5B` | | |

Extended keys (RCtrl, RAlt, arrows, Insert, Delete, Home, End, PageUp, PageDown) use the E0 prefix flag (`0x02`) with the base scan code.

## Rust Bindings

See [alluno-hinpud-sys](https://github.com/alluno-io/alluno-hinpud-sys) for Rust bindings.

## License

MIT
