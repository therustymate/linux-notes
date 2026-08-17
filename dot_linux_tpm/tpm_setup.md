# Fedora TPM2 Setup

[https://fedoramagazine.org/use-systemd-cryptenroll-with-fido-u2f-or-tpm2-to-decrypt-your-disk/](https://fedoramagazine.org/use-systemd-cryptenroll-with-fido-u2f-or-tpm2-to-decrypt-your-disk/)

```bash
$ lsblk -o NAME
NAME
zram0
nvme0n1
├─nvme0n1p1
├─nvme0n1p2
└─nvme0n1p3                                     <- TARGET
  └─luks-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx   <- LUKS

$ echo "add_dracutmodules+=\" tpm2-tss \"" | sudo tee /etc/dracut.conf.d/tpm2.conf
add_dracutmodules+=" tpm2-tss "
add_dracutmodules+=" tpm2-tss "

$ sudo systemd-cryptenroll --wipe-slot tpm2 --tpm2-device auto --tpm2-pcrs "0+7" /dev/nvme0n1p3
🔐 Please enter current passphrase for disk /dev/nvme0n1p3: ••••••                  
New TPM2 token enrolled as key slot 1.

$ sudo cat /etc/crypttab 
luks-... UUID=... none discard,x-initrd.attach

$ sudo nano /etc/crypttab
Write:
luks-... UUID=... none discard,x-initrd.attach,tpm2-device=auto

$ sudo dracut -f
```

### Platform Configuration Register Table (Simple)
| PCR No.   | Affecting Components     | Potential Changes                  |
|:----------|:-------------------------|:-----------------------------------|
| **0**     | UEFI/BIOS firmware       | Framework BIOS update              |
| **1**     | Platform configuration   | BIOS settings, Platform components |
| **2**     | Option ROM / EFI driver  | PCIe/GPU/NIC/storage firmware      |
| **3**     | Option ROM configuration | Device configuration and topology  |
| **4**     | Boot executable          | shim/GRUB/EFI loader update        |
| **5**     | Boot configuration       | BootOrder, GPT, boot state/config  |
| **7**     | Secure Boot policy       | Secure Boot, PK/KEK/db/dbx         |
| **9**     | Kernel/initrd files      | Fedora kernel update, `dracut -f`  |

* **Recommended PCR**: 7
* **Possible Strict Policy**: 0+7

### References
* [https://fedoramagazine.org/use-systemd-cryptenroll-with-fido-u2f-or-tpm2-to-decrypt-your-disk/](https://fedoramagazine.org/use-systemd-cryptenroll-with-fido-u2f-or-tpm2-to-decrypt-your-disk/)
* [https://help.zededa.com/hc/en-us/articles/43295940828827-TPM-PCR-Index-Security-Implications](https://help.zededa.com/hc/en-us/articles/43295940828827-TPM-PCR-Index-Security-Implications)
* [https://fedoramagazine.org/automatically-decrypt-your-disk-using-tpm2/](https://fedoramagazine.org/automatically-decrypt-your-disk-using-tpm2/)