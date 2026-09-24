# AMD-NR-bridge

Makes [DLSS-NR-on-AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) work in games that also use
OptiScaler, including OptiScaler forks with built-in neural rendering (NR) controls. For AMD
Radeon GPUs only.

Tested OptiScaler builds:

- the regular [OptiScaler](https://github.com/optiscaler/OptiScaler)
- Dagherbou's OptiScaler fork, [OptiScaler_DLSSNR](https://github.com/Dagherbou/OptiScaler_DLSSNR),
  which adds DLSS neural rendering to OptiScaler's menu

When OptiScaler and DLSS-NR-on-AMD are installed in the same game, one of these usually happens:

- OptiScaler works, but the DLSS-NR overlay never appears.
- The DLSS-NR overlay appears, but keeps asking you to turn on FSR even though FSR is on.

The bridge fixes both. You keep OptiScaler's GPU spoofing and in-game menu, and DLSS-NR runs on top
of OptiScaler's FSR 4.

With an OptiScaler fork that supports neural rendering, the fork's neural rendering menu and sliders
keep working: they control DLSS-NR-on-AMD and add the fork's extra neural rendering effects.

## Requirements

- Windows 10 or 11, 64-bit
- An AMD Radeon GPU supported by DLSS-NR-on-AMD (Radeon RX 9000 or RX 7000 series). NVIDIA GPUs are
  not supported and do not need it.
- A DirectX 12 game
- OptiScaler installed in the game: the regular [OptiScaler](https://github.com/optiscaler/OptiScaler)
  (tested with 0.9.4) or Dagherbou's [OptiScaler_DLSSNR](https://github.com/Dagherbou/OptiScaler_DLSSNR)
- [DLSS-NR-on-AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) installed in the same game (tested
  with 0.3.0)

The bridge does not include OptiScaler, DLSS-NR-on-AMD, or any NVIDIA or game files. Install those
yourself first.

## Download

Get `AMD-NR-bridge-<version>.zip` from the [latest release](../../releases/latest).


## Install

1. Close the game.
2. Copy OptiScaler's files (the regular OptiScaler or Dagherbou's fork) into the game folder and run
   `setup_windows.bat`.
3. Install DLSS-NR-on-AMD with its setup, `dlssnr_on_amd_setup.exe`.
4. Unzip the bridge download anywhere.
5. Double-click **Install AMD-NR-bridge.bat**.
6. Choose the game folder: the one with `OptiScaler.ini` and `dlssnr_on_amd.ini`, usually where the
   game's `.exe` is. In Unreal Engine games (for example Clair Obscur: Expedition 33) that is
   `<game>\<name>\Binaries\Win64`, next to the `...-Win64-Shipping.exe`. You can also drag the game
   folder onto **Install AMD-NR-bridge.bat**.
7. Read the list of changes and press **Y**, then **Enter**.

Then start the game in DirectX 12 and turn DLSS, FSR or XeSS on in its graphics settings, whichever
it offers. OptiScaler turns it into FSR 4, and the DLSS-NR overlay should appear without asking for
FSR. If the installer says the game has its own FSR 3.1 or FSR 4, choose FSR, not DLSS.

## Frame generation

OptiScaler's own frame generation (FSR FG) works with the bridge.
Turn it on in OptiScaler's menu, or in `OptiScaler.ini` before starting the game:

```
[FrameGen]
FGInput=upscaler
FGOutput=fsrfg
```

Choosing **FG Input: OptiFG (Upscaler)** and **FG Output: FSR FG** in OptiScaler's menu writes the same
two lines, but frame generation only starts the **next** time you launch the game, not straight away.

- With frame generation on, only one of the two overlays is drawn at a time, and the bridge takes care of
  it: DLSS-NR's overlay hides itself while OptiScaler's menu is open and comes back when you close the menu.
  If you open OptiScaler's menu first, the End key does nothing until you close it again - that is the same
  rule, not a fault. The two drawing into the same frames crashes the game, which is why they are kept apart.

## What the installer changes

- Copies `AMD-NR-bridge.asi` into the game's `plugins` folder, where OptiScaler loads it.
- Sets these options in `OptiScaler.ini`, after saving your original as `OptiScaler.ini.before-AMD-NR-bridge`:

  | setting | why |
  |---|---|
  | `LoadAsiPlugins=true` | OptiScaler loads the bridge |
  | `Path=<the plugins folder>` | OptiScaler looks for the bridge where it is. Newer OptiScaler versions otherwise look in `OptiScaler\plugins` |
  | `EnableFfxInputs=false` | otherwise OptiScaler calls FSR in a way DLSS-NR cannot see |
  | `Dx12Upscaler=fsr31` | DLSS-NR needs OptiScaler to use FSR (FSR 4 on Radeon RX 9000). Newer OptiScaler versions call it `ffx`, which is fine too |
  | `DxgiFactoryWrapping=true` | otherwise OptiScaler's menu is sometimes missing, or the game crashes, when DLSS-NR and the game start at the same moment |
  | `FGShortcutKey=-1` | so the End key opens DLSS-NR's overlay without also switching frame generation on and off: OptiScaler uses that key too |

  In games with their own FSR 3.1 or FSR 4 it sets these instead:

  | setting | why |
  |---|---|
  | `EnableFfxInputs=true` | OptiScaler sees the game's FSR |
  | `ColorResourceBarrier=64` | otherwise the game crashes as soon as it starts |

- With Dagherbou's OptiScaler fork, also adds the files and settings the fork needs. Uninstall removes
  them again.
- Fixes OptiScaler and DLSS-NR being installed under the same file name (see below).

It does not modify OptiScaler's or DLSS-NR's own files. When the game folder needs administrator rights
(for example under Program Files), Windows asks for them.

## When OptiScaler and DLSS-NR used the same file name

OptiScaler and DLSS-NR are each a DLL with a Windows file name such as `dxgi.dll`, `winmm.dll` or
`version.dll`. If they were installed under the same name, the second replaced the first. The
installer notices this:

- **OptiScaler replaced DLSS-NR:** it moves OptiScaler to another free name, then asks you to run
  `dlssnr_on_amd_setup.exe` again, and offers to start it.
- **DLSS-NR replaced OptiScaler:** it asks for `OptiScaler.dll` from the OptiScaler download and
  installs it under a free name.

A replaced file cannot be recovered, only installed again.

## Uninstall

Double-click **Uninstall AMD-NR-bridge.bat** and choose the game folder. It removes the bridge and puts the
`OptiScaler.ini` settings back. OptiScaler and DLSS-NR stay installed.

## Credits

- [DLSS-NR-on-AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) by danielblnc
- [OptiScaler](https://github.com/optiscaler/OptiScaler) by the OptiScaler team

## Support

AMD-NR-bridge is free. Please support the project:

[![Support me on Ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/goldennights)

## Disclaimer

This tool is provided as is, without support or warranty of any kind. It is not made by, or affiliated
with, danielblnc, the OptiScaler developers, NVIDIA or AMD. Use it at your own risk. NVIDIA and DLSS
are trademarks of NVIDIA Corporation; AMD, Radeon and FSR are trademarks of Advanced Micro Devices, Inc.

## License

Free for personal, non-commercial use. Re-uploading, bundling, selling, modifying and reverse
engineering are not allowed; link to this page instead. Full terms: [LICENSE](LICENSE).
