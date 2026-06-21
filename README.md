# Corne Wireless — Battery Fix

Your repo: github.com/cnnxt/corne_wireless (Typeractive Corne, nice!nano v2 +
nice!view, no RGB). Keymap is managed live by ZMK Studio.

## What was draining your battery

Two things in your old `corne.conf`:

1. `# CONFIG_ZMK_SLEEP=y` was **commented out** → the keyboard never went to
   deep sleep. This is the main culprit.
2. `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` → Bluetooth ran at MAX transmit power for
   range, which burns extra battery.

The fixed `config/corne.conf` in this folder enables deep sleep (15-min idle
timeout) and drops the max TX power back to stock.

## Why your keymap survives a reflash (and the one catch)

Your keymap is managed by **ZMK Studio**, so it lives in the keyboard's own
settings memory — NOT in the firmware. A normal reflash does not erase that
memory, so your layout reloads by itself after flashing. Per ZMK's docs,
changes to the `.keymap` file are ignored unless you do "Restore Stock
Settings."

**The catch:** Studio can only hold as many layers as the *firmware* defines.
Your repo's keymap has 3 layers, but your board runs 4. If you rebuilt from the
repo as-is and flashed, there'd be no slot for your 4th layer ("windows nav")
and you'd lose it.

**The fix (already done in this folder's `corne.keymap`):** I added two empty
`reserved` layers. ZMK's docs explicitly bless adding empty layers as the one
safe edit to a Studio-managed keymap — they expand firmware capacity without
touching your live bindings, so all 4 Studio layers fit and persist.

## What's in this update

- **Battery fix** + **110 mAh tuning** (`corne.conf`)
- **Full 4-layer keymap backup**, verified key-by-key (`corne.keymap`)
- **nice-view-gem display** — the "gem" status screen, still showing battery,
  BLE/USB + Bluetooth profile, and active layer (`west.yml` + `build.yaml`;
  animation turned off in `corne.conf` to spare the tiny batteries)

## How to apply

1. In your `cnnxt/corne_wireless` repo, replace these files with the versions
   in this folder:
   - `config/corne.conf`   (battery fix + nice-view-gem options)
   - `config/corne.keymap`  (full verified backup of all 4 layers)
   - `config/west.yml`      (adds the nice-view-gem module)
   - `build.yaml`           (switches the shield to `nice_view_gem`)
2. Push. GitHub Actions rebuilds automatically (Actions tab).
3. Download the **firmware** artifact → you'll get:
   - `corne_left ... nice_view-nice_nano_v2-zmk.uf2`
   - `corne_right ... nice_view-nice_nano_v2-zmk.uf2`
4. Flash each half: plug in via USB, double-tap reset → `NICENANO` drive
   appears → drag the matching `.uf2` on. Repeat for the other half.
   **Do NOT do "Restore Stock Settings" or flash settings_reset** — those are
   the only things that would wipe your live keymap.

After flashing: deep sleep on, Bluetooth power back to stock, and your 4-layer
keymap reloads untouched. Battery should last dramatically longer.

> Residual risk: if the keyboard's settings ever get wiped (corruption, a
> settings_reset, or "Restore Stock Settings"), you'd fall back to the 3-layer
> keymap in this file. That's why a real backup of the live layout is still
> worth doing eventually.

## Optional next step

Back up your current 4-layer Studio keymap to the repo so it's not only living
in the keyboard. (Claude can help transcribe it from Studio, or you can export
once ZMK Studio's import/export feature ships.)
