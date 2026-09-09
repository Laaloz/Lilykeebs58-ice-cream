![AKR04273-Edit](https://github.com/Laaloz/Lilykeebs58-ice-cream/assets/77687900/afd97250-8864-4ad5-bcea-6f5298999e48)

# Lilykeebs58 Ice Cream — ZMK firmware

Wireless Lily58 split (58 keys, nice!nano v2, ZMK v0.3) built on ZMK's stock
`lily58` shield. ZMK Studio is enabled on the left half.

## Structure
```
config/
  lily58.keymap             # default / lower / raise
  lily58.conf               # OLED off, BLE tweaks, TX power, RC oscillator
  west.yml                  # ZMK dependency manifest (pinned to v0.3)
build.yaml                  # build both halves for nice_nano_v2; Studio on the left half
.github/workflows/build.yml # runs ZMK's build-user-config workflow (v0.3)
```

## Getting started
1. Push → GitHub Actions builds both halves → download the `firmware` artifact
   from the workflow run. The zip contains one file per half:
   - `lily58_left-nice_nano_v2-zmk.uf2`
   - `lily58_right-nice_nano_v2-zmk.uf2`
2. Put each half into the bootloader (double-tap RST quickly) → drag the
   matching `.uf2` onto the NICENANO drive. Flash the left half first.
3. Pairing: the left half is central and advertises — pair the left half to
   your computer. The right half connects to the left automatically. If the
   halves don't find each other: add a `settings_reset` entry to `build.yaml`
   (`board: nice_nano_v2`, `shield: settings_reset`), flash that firmware to
   both halves, then flash the normal firmware again and re-pair the host.
   `&bt BT_CLR` (lower layer, top-left) only clears the bond of the active profile.

## Keymap
- **default** — QWERTY with a number row; `[` `]` on the inner bottom keys,
  `BSPC` `-` `'` `` ` `` down the outer right column.
- **lower** (hold LOWER, left thumb) — Bluetooth profiles 0–4 and `BT_CLR` on
  the top row, F1–F12, shifted symbols on the home row, `- + { } |` bottom
  right, ext_power on/off/toggle bottom left.
- **raise** (hold RAISE, right thumb) — number row, F1–F12 on the left, arrows
  on the right home row, `+ - = [ ] \` on the bottom row, Studio unlock
  top-right.
- Thumbs (outer → inner | inner → outer):
  `ALT | GUI | LOWER | SPACE` — `ENTER | RAISE | GUI | ALT`
- Encoder: volume up/down on every layer. Only active if `CONFIG_EC11=y` is
  uncommented in `lily58.conf`.

Keycodes are US positions. The OS layout decides what they print: on a Finnish
layout SEMI/SQT/LBKT produce ö ä å (`;` `'` `[` on US), and GRAVE prints `<`
(labelled that way in the keymap).

## ZMK Studio
Studio is enabled on the left (central) half: `build.yaml` adds the
`studio-rpc-usb-uart` snippet and `-DCONFIG_ZMK_STUDIO=y` for `lily58_left`.
Connect the left half over USB, press `&studio_unlock` (raise layer, top-right)
and edit the keymap in Studio without reflashing. Changes to `lily58.keymap`
made afterwards only take effect after "Restore Stock Settings" in Studio.

## Config notes (`lily58.conf`)
- `CONFIG_ZMK_DISPLAY=n` — OLED off
- `CONFIG_BT_CTLR_PHY_2M=n` — 2M PHY disabled for connection stability
- `CONFIG_BT_GATT_AUTO_SEC_REQ=n` — no automatic security request on connect
- `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` — +8 dBm TX power; helps both the host link
  and the split link, negligible power cost
- `CONFIG_CLOCK_CONTROL_NRF_K32SRC_RC=y` — internal 32 kHz RC oscillator instead
  of the external crystal (works around a faulty crystal; diagnostics)
- Encoder support (`CONFIG_EC11`) is commented out
