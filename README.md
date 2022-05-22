# OpenCore EFI for ASRock X570 Pro 4 with AMD Ryzen

## Verified Specification

| **Component**    | **Model**                                  |
| ---------------- | ------------------------------------------ |
| CPU              | AMD Ryzen 9 5900 @ 4.8GHz                  |
| Motherboard      | [ASRock X570 Pro4](https://www.asrock.com/MB/AMD/X570%20Pro4/index.ru.asp)|
| RAM              | 32GB (2 x 16GB) DDR4 @ 2667 MHz            |
| GPU              | AMD Radeon RX 5700 XT (8176 MB)            |
| Audio Chipset    | Realtek ALC1200                            |
| Ethernet         | Intel(R) I211.                             |
| WiFi & Bluetooth | -                                          |
| OS Disk (NVMe)   | Samsung 980 PRO 512GB                      |

**macOS version**: 11.6.6 (20G624) \
**OpenCore version**: 0.8.0

## Software Compatibility

- :x: [NOT WORKING*] Monterey (12.x). 
- :white_check_mark: [WORKING] Big Sur (11.x)

*Monterey is not supported yet due to the Ethernet issue of [SmallTreeIntel82576.kext](https://github.com/khronokernel/SmallTree-I211-AT-patch/issues/3). You can try [AppleIGB kext](https://cdn.discordapp.com/attachments/724618275971137568/879288441278435348/AppleIGB.kext.zip), it works on some systems. If it does not work you have to stay on Big Sur and wait for SmallTree's update.

## Installation

### Bootable USB

1. Follow [this guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/) to create your bootable USB.

2. Clone this repository and copy "BOOT" & "OC" directories to your "EFI" directory on your bootable USB. The structure should look somewhat like this: `EFI -> BOOT, OC`.

### Modifying kernel patches
3. Modify Core Count patches to match your CPU's cores amount.

  - Find three `algrey - Force cpuid_cores_per_package` patches under `Kernel -> Patch` in your config.
  - Modify these patches for your CPU physical cores. Change **first pair** of `00` in `Replace` of these patches to `Hex value` from below table.

    - e. g. for Ryzen 9 5900X with 12 Cores three modified patches should look like:
      - B8 **00** 0000 0000 -> B8 **0C** 0000 0000
      - BA **00** 0000 0000 -> BA **0C** 0000 0000
      - BA **00** 0000 0090 -> BA **0C** 0000 0090

| **Physical CPU cores** | **Hex value** |
| ---------------------- | ------------- |
| 4 Cores                | `04`          |
| 6 Cores                | `06`          |
| 8 Cores                | `08`          |
| 12 Cores               | `0C`          |
| 16 Cores               | `10`          |

### SMBIOS

4. Use [this tool](https://github.com/corpnewt/GenSMBIOS) to generate your unique SMBIOS info.

- SMBIOS has to be unique, you cannot use one present in this repository.

- Run the tool and select `Generate SMBIOS`.
- Select the appropriate model for your hardware (e.g. `iMac20,1`).
- Go to [Apple Coverage](https://checkcoverage.apple.com/) and paste generated _Serial_. You need "Invalid Serial" or "Purchase Date not Validated" message. If you get something another you have to generate SMBIOS data and check it again.
- Open _config.plist_ and search for `PlatformInfo -> Generic` and replace these values:
  - _SystemProductName_ - Model
  - _MLB_ - Board Serial
  - _SystemSerialNumber_ - Serial
  - _SystemUUID_ - SmUUID
- _ROM_ entry should be set to your [network card's MAC address](https://www.wikihow.com/Find-the-MAC-Address-of-Your-Computer), without separators (e. g. `:`, `-`).

## BIOS Settings

| **Option**            | **Status**           |
| --------------------- | -------------------- |
| CSM                   | Disabled             |
| Above 4G Decoding     | Enabled              |
| Resizable BAR Support | Disabled             |
| Secure Boot           | Disabled             |
| Serial Port           | Disabled             |
| Thunderbolt           | Disabled             |
