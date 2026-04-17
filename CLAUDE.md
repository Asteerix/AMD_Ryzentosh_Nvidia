# AMD_Ryzentosh_Nvidia

Configuration OpenCore EFI pour faire tourner macOS sur un système AMD Ryzen (Zen 2/3) avec GPU Nvidia désactivé. Le GPU Nvidia n'a pas de driver macOS au-delà de High Sierra ; le dGPU est désactivé via SSDT/boot-args et l'affichage est délégué à un GPU AMD compatible. Cible SMBIOS : `MacPro7,1`.

## Stack

- **OpenCore** 0.7.x+ (bootloader)
- **macOS** Monterey 12.x (testé)
- **SMBIOS** : MacPro7,1 (à régénérer pour chaque install via GenSMBIOS)
- **Plateforme** : AM4 chipset (B450 / B550 / X570)
- Pas de stack logicielle / build : repo de **fichiers binaires + plist**.

## Kexts inclus (`EFI/OC/Kexts/`)

- Lilu (kernel patching framework)
- VirtualSMC (SMC emulation)
- WhateverGreen (GPU patching, désactive Nvidia)
- AppleALC (audio, layout-id 1)
- AMDRyzenCPUPowerManagement (CPU power management AMD)
- RealtekRTL8111 (Ethernet)
- AirportItlwm + AirPortAtheros40 (Wi-Fi Intel / Atheros)
- NullEthernet (iServices)
- RestrictEvents
- CtlnaAHCIPort

## Commands (manipulation EFI)

```bash
# Pas de build. Workflow :
#  1. Monter la partition EFI (USB ou disque interne) avec MountEFI
#  2. Copier le dossier EFI/ de ce repo dans la partition EFI
#  3. Éditer EFI/OC/config.plist avec ProperTree
#  4. Régénérer SMBIOS avec GenSMBIOS (MacPro7,1) et injecter dans config.plist :
#       - MLB
#       - SystemSerialNumber
#       - SystemUUID
#       - ROM
#  5. Booter depuis l'USB et installer macOS
#  6. Post-install : copier EFI/ vers la partition EFI du disque interne
```

Boot args utilisés : `-v alcid=1 npci=0x2000`.

## Project layout

- `EFI/BOOT/` — `BOOTx64.efi` (entry point UEFI)
- `EFI/OC/` — config OpenCore (config.plist, Drivers, Kexts, Resources, Tools, ACPI)
- `EFI/APPLE/` — assets Apple (boot picker, icônes)
- `README.md` — guide hardware + installation
- Pas de code applicatif

## Conventions

- **Toujours régénérer son propre SMBIOS** (MLB, SystemSerialNumber, SystemUUID, ROM) — ne jamais utiliser les valeurs incluses dans le repo (risque de blacklist iServices)
- BIOS : Disable Fast Boot, Secure Boot, Serial/COM, CSM ; Enable Above 4G Decoding, EHCI/XHCI Hand-off, UEFI boot, SVM (optionnel)
- Mettre à jour les kexts via OCAuxiliaryTools / OpenCore Configurator avant d'éditer manuellement le plist
- Conventional commits : `type(scope): description`

## Notes

- Repo **éducatif** uniquement. Hackintosh peut violer l'EULA Apple.
- Nvidia GPU acceleration **non fonctionnelle** au-delà de High Sierra (10.13). Présence d'un GPU AMD secondaire requise pour l'affichage.
- AirDrop / Handoff / Sidecar non supportés (dépendent d'un combo Wi-Fi/BT Broadcom natif).
- Sleep/Wake peut nécessiter du patching SSDT additionnel sur AMD.
- Aucun secret applicatif — mais SMBIOS valide est sensible (à ne pas partager publiquement).
