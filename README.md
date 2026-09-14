# DLSS-NR OptiScaler Bridge

Makes [DLSS-NR on AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) work in games that also use
[OptiScaler](https://github.com/optiscaler/OptiScaler).

With both mods installed, one of these usually happens:

- OptiScaler works, but the DLSS-NR overlay never appears.
- The DLSS-NR overlay appears, but keeps asking you to turn on FSR even though FSR is on.

The bridge fixes both. You keep OptiScaler's GPU spoofing and in-game menu, and DLSS-NR runs on top
of OptiScaler's FSR 4.

## Requirements

- Windows 10 or 11, 64-bit
- A Radeon GPU supported by DLSS-NR on AMD
- A DirectX 12 game
- [OptiScaler](https://github.com/optiscaler/OptiScaler) installed in the game (tested with 0.9.4)
- [DLSS-NR on AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) installed in the same game (tested
  with 0.3.0)

The bridge does not include OptiScaler, DLSS-NR on AMD, or any NVIDIA or game files. Install those
yourself first.

## Download

Get `DLSS-NR-OptiScaler-Bridge-<version>.zip` from the [latest release](../../releases/latest).

If Windows or your antivirus blocks the files, right-click the downloaded zip, choose
**Properties**, tick **Unblock**, and unzip it again.

## Install

1. Close the game.
2. Unzip the download anywhere.
3. Double-click **Install Bridge.bat**.
4. Choose the game folder: the one with `OptiScaler.ini` and `dlssnr_on_amd.ini`, usually where the
   game's `.exe` is. You can also drag the game folder onto **Install Bridge.bat**.
5. Read the list of changes and press **Y**, then **Enter**.

Then start the game in DirectX 12 and turn DLSS, FSR or XeSS on in its graphics settings, whichever
it offers. OptiScaler turns it into FSR 4, and the DLSS-NR overlay should appear without asking for
FSR.

## What the installer changes

- Copies `dlssnr_opti_bridge.asi` into the game's `plugins` folder, where OptiScaler loads it.
- Sets three options in `OptiScaler.ini`, after saving your original as `OptiScaler.ini.before-bridge`:

  | setting | why |
  |---|---|
  | `LoadAsiPlugins=true` | OptiScaler loads the bridge |
  | `EnableFfxInputs=false` | otherwise OptiScaler calls FSR in a way DLSS-NR cannot see |
  | `Dx12Upscaler=fsr31` | DLSS-NR needs OptiScaler to use FSR (FSR 4 on Radeon RX 9000) |

- Fixes OptiScaler and DLSS-NR being installed under the same file name (see below).

It does not modify OptiScaler's or DLSS-NR's own files. When the game folder needs administrator rights
(for example under Program Files), Windows asks for them.

## When both mods used the same file name

Both mods are a DLL with a Windows file name such as `dxgi.dll`, `winmm.dll` or `version.dll`. If they
were installed under the same name, the second replaced the first. The installer notices this:

- **OptiScaler replaced DLSS-NR:** it moves OptiScaler to another free name, then asks you to run
  `dlssnr_on_amd_setup.exe` again, and offers to start it.
- **DLSS-NR replaced OptiScaler:** it asks for `OptiScaler.dll` from the OptiScaler download and
  installs it under a free name.

A replaced file cannot be recovered, only installed again.

## Uninstall

Double-click **Uninstall Bridge.bat** and choose the game folder. It removes the bridge and puts the
three `OptiScaler.ini` settings back. OptiScaler and DLSS-NR stay installed.

## If something does not work

Run **Install Bridge.bat** again: it shows what it finds and fixes what is missing. These files in the
game folder show what happened after playing for a minute:

- `plugins\dlssnr_opti_bridge.<game exe name>.log`, for example `dlssnr_opti_bridge.RDR2.log`
- `dlssnr_on_amd.log`
- `OptiScaler.log`, when `LogToFile=true` is set in `OptiScaler.ini`

## Tested with

| game | GPU | OptiScaler | DLSS-NR on AMD |
|---|---|---|---|
| Rise of the Tomb Raider | Radeon RX 9070 | 0.9.4 | 0.3.0 |
| Red Dead Redemption 2 | Radeon RX 9070 | 0.9.4 | 0.3.0 |

Other games and versions may work but have not been tested.

## Credits

- [DLSS-NR on AMD](https://github.com/danielblnc/DLSS-NR-on-AMD) by danielblnc
- [OptiScaler](https://github.com/optiscaler/OptiScaler) by the OptiScaler team

## Disclaimer

This tool is provided as is, without support or warranty of any kind. It is not made by, or affiliated
with, danielblnc or the OptiScaler developers. Use it at your own risk.

## License

Free for personal, non-commercial use. Re-uploading, bundling, selling, modifying and reverse
engineering are not allowed; link to this page instead. Full terms: [LICENSE](LICENSE).
