# Hackintosh — AMD Ryzentosh with Nvidia GPU

![macOS](https://img.shields.io/badge/macOS-Monterey_12.x-292e33?logo=apple&logoColor=white)
![OpenCore](https://img.shields.io/badge/OpenCore-0.7.x+-0060ff?logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIxNiIgaGVpZ2h0PSIxNiI+PHJlY3Qgd2lkdGg9IjE2IiBoZWlnaHQ9IjE2IiByeD0iMyIgZmlsbD0iIzAwNjBmZiIvPjx0ZXh0IHg9IjQiIHk9IjEyIiBmb250LXNpemU9IjEwIiBmaWxsPSJ3aGl0ZSIgZm9udC1mYW1pbHk9IkhlbHZldGljYSI+T0M8L3RleHQ+PC9zdmc+)
![SMBIOS](https://img.shields.io/badge/SMBIOS-MacPro7%2C1-black)
![License](https://img.shields.io/badge/License-MIT-green)

OpenCore EFI configuration for running macOS on an AMD Ryzen system with an Nvidia GPU.

> **Note:** Nvidia GPUs have no native macOS driver support since High Sierra. The dGPU is disabled via SSDT/boot-args and all display output is handled by the iGPU or a compatible AMD GPU. If your only GPU is Nvidia, you are limited to macOS High Sierra (10.13) with web drivers or must use this configuration with an additional AMD GPU for display.

---

## Hardware

| Component       | Details                           |
|-----------------|-----------------------------------|
| **CPU**         | AMD Ryzen (Zen 2/3)              |
| **GPU**         | Nvidia (disabled in macOS)        |
| **Motherboard** | AM4 chipset (B450/B550/X570)     |
| **Audio**       | Realtek ALC (layout-id 1)        |
| **Ethernet**    | Realtek RTL8111                   |
| **Wi-Fi**       | Intel (AirportItlwm) / Atheros   |
| **Storage**     | NVMe SSD                         |
| **SMBIOS**      | MacPro7,1                        |

> Adjust the table above to match your exact hardware.

---

## What Works

- [x] macOS boot and installation
- [x] CPU power management (`AMDRyzenCPUPowerManagement`)
- [x] Audio (`AppleALC`, layout-id `1`)
- [x] Ethernet (`RealtekRTL8111`)
- [x] Wi-Fi (Intel via `AirportItlwm` / Atheros via `AirPortAtheros40`)
- [x] NVMe storage
- [x] USB ports
- [x] iServices (iMessage, FaceTime) with proper SMBIOS + `NullEthernet`
- [x] OpenCore boot picker (`OpenCanopy`)

## What Doesn't Work

- [ ] Nvidia GPU acceleration (no macOS driver beyond High Sierra)
- [ ] AirDrop / Handoff (requires native Broadcom Wi-Fi/BT)
- [ ] Sleep/Wake (may require additional patching on AMD)
- [ ] Sidecar

---

## Kexts

| Kext | Purpose |
|------|---------|
| [Lilu](https://github.com/acidanthera/Lilu) | Kernel patching framework |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC) | SMC emulation |
| [WhateverGreen](https://github.com/acidanthera/WhateverGreen) | GPU patching / Nvidia disable |
| [AppleALC](https://github.com/acidanthera/AppleALC) | Audio codec patching |
| [AMDRyzenCPUPowerManagement](https://github.com/trulyspinach/SMCAMDProcessor) | AMD CPU power management |
| [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X) | Realtek Ethernet |
| [AirportItlwm](https://github.com/OpenIntelWireless/itlwm) | Intel Wi-Fi |
| [AirPortAtheros40](https://github.com/khronokernel/IO80211-Patches) | Atheros Wi-Fi (legacy) |
| [NullEthernet](https://github.com/RehabMan/OS-X-Null-Ethernet) | Null Ethernet for iServices |
| [RestrictEvents](https://github.com/acidanthera/RestrictEvents) | Suppresses unwanted notifications |
| [CtlnaAHCIPort](https://github.com/dortania/OpenCore-Install-Guide) | SATA controller support |

---

## BIOS Settings

### Disable
- Fast Boot
- Secure Boot
- Serial/COM Port
- CSM (Compatibility Support Module)

### Enable
- Above 4G Decoding
- EHCI/XHCI Hand-off
- UEFI boot mode
- SVM (AMD Virtualization) — optional

---

## Installation

1. **Create a macOS installer** using [OpenCore's guide](https://dortania.github.io/OpenCore-Install-Guide/).
2. **Copy the EFI folder** from this repo to the `EFI` partition of the USB installer.
3. **Generate SMBIOS** values using [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) for `MacPro7,1` and update `config.plist`:
   - `MLB`
   - `SystemSerialNumber`
   - `SystemUUID`
   - `ROM`
4. **Boot from USB** and install macOS.
5. **Post-install:** Copy the EFI folder to the internal drive's EFI partition.

> **Important:** Never use the SMBIOS values included in this repo — always generate your own to avoid iServices blacklisting.

---

## Boot Arguments

```
-v alcid=1 npci=0x2000
```

| Argument | Description |
|----------|-------------|
| `-v` | Verbose boot (remove after stable install) |
| `alcid=1` | AppleALC layout ID for audio codec |
| `npci=0x2000` | Fixes PCI configuration for AMD systems |

---

## Credits

- [Acidanthera](https://github.com/acidanthera) — OpenCore, Lilu, VirtualSMC, WhateverGreen, AppleALC, RestrictEvents
- [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) — OpenCore Install Guide
- [trulyspinach](https://github.com/trulyspinach/SMCAMDProcessor) — AMD CPU Power Management
- [OpenIntelWireless](https://github.com/OpenIntelWireless) — Intel Wi-Fi kexts
- [AMD-OSX](https://amd-osx.com/) — AMD Hackintosh community

---

## Disclaimer

This repository is provided for educational purposes only. Running macOS on non-Apple hardware may violate Apple's EULA. Use at your own risk. The author is not responsible for any damage to hardware or data. Always generate your own SMBIOS values — do not use the ones in this repo.

---

## License

[MIT](LICENSE)