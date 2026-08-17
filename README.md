# Context

Recently I ordered [this YMDK keyboard](https://ymdkey.com/products/ymdk-sofle-wireless-split-keyboard-2-4ghz-64-keys-ergonomic-hotswappable-60-layout-3d-pla-vial-all-key-programmable-mini-independent-gaming-keyboard-kit), which was advertised as programmable. At the time of the purchase, I didn't know much about this type of keyboards, and the web said the firmware was unavailable to download ***at that moment***, so I assumed there was a temporary problem, but the original files would be published in a short time.

But the original firmware hasn't been released since then, and I believe that's because this keyboard is probably a variation of a [Vial](https://github.com/vial-kb/vial-qmk)-based keyboard whose with a closed-source firmware.
While that is against [QMK's license](https://docs.qmk.fm/hardware_keyboard_guidelines#license), there's nothing they can do about it, so I decided to try and patch my keyboard's firmware myself to customize my keyboard to a deeper extent.

The first thing I wanted to do was to modify the behaviour of Caps Word key.

# About the Keyboard

I gathered some info about the keyboard specifications with Linux tools, in case that can be of help to my research.

- **Normal Mode USB Identification**: ``55d4:0664 MTKB W-SOFLE``
  - `MTKB` (VID: `0x55D4`) shows as the manufacturer and `W-SOFLE` (PID: `0x0664`) as the product.
- **Bootloader Mode USB Identification**: ``0820:0029 Adafruit Industries PINRF Bootloader`` (can be entered for 30 seconds by pressing the Bootloader key)
  - **VID**: ``0x0820``
  - **PID**: ``0x0029``
- The keyboard presents three USB HIDs, first one for boot.

The bootloader exposes a USB Mass Storage device with the following specifications:
```
Model: nRF UF2
Device: /dev/sda
Size: 32.13 MiB
Filesystem: VFAT
Label: PINRF_BOOT
UUID: 0042-0042
```

Inside that filesystem I found what I believe to be the installed firmware: a file named ``CURRENT.UF2``. I copied it and saved it to the repository as [firmware/dumped_firmware.uf2](./firmware/dumped_firmware.uf2).

I also found this ``INFO_UF2.TXT`` file that gave interesting information:
```
UF2 Bootloader 0.8.3-59-g187faf3-dirty
# I assume this is telling us that the keyboard uses a modified version
# of Adafruit's bootloader. A fork 59 commits behind the base.

lib/tinyusb (0.12.0-145-g9775e7691)
lib/uf2 (remotes/origin/configupdate-9-gadbb8c7)
# Libraries used for USB connection and firmware format management.

Model: Adafruit Feather nRF52820 Express
Board-ID: nRF52820-Feather-revD
Date: Aug 11 2024
# Let's not trust this identification. This is a dirty/modified
# keyboard, so this might just be inherited specification.

SoftDevice: not found
# Apparently SoftDevice handles Bluetooth. My keyboard uses
# Bluetooth. But not SoftDevice, apparently.
```


# YMDK Help

I had previously asked YMDK support how I could get the original files for my keyboard firmware. They answered providing a compiled firmware, but I compared it to
the one I had copied from my keyboard's bootloader, and it was less than half its size.

One may think that, given that this is a Vial-based keyboard, the extra data could be EEPROM (persistent memory; my custom mappings).
However, when I compared the two files, they had the same structure up to a certain point, but different data.
Not sure if this means they're different or not. I have little experience in this field. But I won't assume they're the same firmware.

I uploaded the compiled firmware they provided to the repository too, as [firmware/ymdk_firmware.uf2](./firmware/dumped_firmware.uf2).
