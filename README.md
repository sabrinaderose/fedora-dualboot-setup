
# Fedora + GRUB Dual-Boot Customization & Recovery

## Date
2025-07-26

## Author
[@sabrinaderose](https://github.com/sabrinaderose)

## Category
Recovery | Configuration | Documentation | GRUB Customization | Dual-Boot

## Related Certifications
- LPI Linux Essentials (Earned)
- Cisco CCNA (In Progress)
- CompTIA A+ (Earned)

---

## Objective

This project documents the configuration and recovery of a Fedora 42 KDE Plasma installation in a dual-boot environment with Windows 11. The primary goals were to:

- Restore Windows boot visibility after GRUB override
- Customize the GRUB bootloader with the Intervals theme
- Clean up Boot Loader Specification (BLS) entries for visual clarity
- Document the end-to-end troubleshooting and theming process for professional demonstration

---

## Environment & Tooling

### Hardware Used
- **Primary SSD** (`/dev/sda`): Fedora Linux (KDE Plasma)
- **Secondary SSD** (`/dev/sdb`): Windows 11
- **USB Media**: Fedora Live Installer

### Software Environment
- Fedora 42 KDE Plasma Edition
- GRUB2 Bootloader (with EFI support)
- `grub2-mkconfig`, `grub-customizer`, `grub2-theme-preview`
- Intervals GRUB Theme (GitHub: yeyushengfan258/Intervals-grub-theme)
- BLS (Boot Loader Spec) Configs

---

## Step-by-Step Process

### Phase 1: Preparation
- Identified the need to replace Arch Linux with Fedora KDE Plasma
- Created Fedora installation media using official Fedora Media Writer
- Backed up important data and confirmed SSD mappings (`sda` Fedora, `sdb` Windows)

### Phase 2: Configuration
- Installed Fedora on `/dev/sda` and allowed it to overwrite the GRUB bootloader
- Verified Windows partitions remained intact using `lsblk` and GParted
- Repaired Windows boot entry manually with:
  ```bash
  grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
  ```
- Cloned and installed the Intervals GRUB theme:
  ```bash
  git clone https://github.com/yeyushengfan258/Intervals-grub-theme.git
  cd Intervals-grub-theme
  chmod +x install.sh
  ./install.sh
  sudo mv dist /boot/grub2/themes/Intervals
  ```

- Configured theme in `/etc/default/grub`:
  ```bash
  GRUB_THEME="/boot/grub2/themes/Intervals/theme.txt"
  ```

### Phase 3: Testing
- Rebooted into Fedora → GRUB theme was not initially visible
- Verified theme path and file presence, then rebuilt GRUB
- Final GRUB entries listed Fedora and Windows but used long kernel strings

### Phase 4: Troubleshooting & Cleanup

#### GRUB Entry Showing as "."
After installation, a GRUB menu entry appeared as `"."`. This was discovered to be the newest Fedora kernel (6.15.7), but was visually confusing.

**Fix:**
1. Locate the correct `.conf` BLS file:
    ```bash
    /boot/loader/entries/cc32cd06eeaa440faa33f95512551b9c-6.15.7-200.fc42.x86_64.conf
    ```

2. Edit the file:
    ```bash
    sudo nano /boot/loader/entries/cc32cd06eeaa440faa33f95512551b9c-6.15.7-200.fc42.x86_64.conf
    ```

3. Update the `title` line to something meaningful:
    ```ini
    title Fedora KDE Plasma (6.15.7)
    ```

4. Rebuild GRUB:
    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
    ```

5. Reboot and verify.

**Optional**: Remove older kernel entries if no longer needed:
```bash
sudo rm /boot/loader/entries/cc32cd06eeaa440faa33f95512551b9c-6.14.0-63.fc42.x86_64.conf
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg
```

---

## Analysis & Reflection

### What Went Well:
- Successful use of official Fedora tools and GitHub-hosted themes
- Preserved Windows partition integrity
- Cleaned up GRUB with both visual theming and logical entry naming

### What Could Be Improved:
- Better EFI partition documentation from Fedora could prevent user confusion
- Intervals GRUB theme requires a build step — not documented clearly in repo

---

## Final Outcome

- Fedora KDE Plasma now dual-boots cleanly with Windows 11
- GRUB is themed, legible, and branded
- BLS entries are renamed for clarity
- All steps documented for future replicability

---

## Screenshot Gallery (WIP)

- `images/before_grub.png` – Default GRUB with Fedora kernel strings
- `images/after_theme.png` – Intervals GRUB theme active
- `images/windows_detected.png` – Verified Windows entry restored

---

## Key Files and Scripts

| File Path | Description |
|-----------|-------------|
| `/etc/default/grub` | Primary GRUB settings |
| `/boot/loader/entries/*.conf` | BLS entry customization |
| `install.sh` | GRUB theme builder |
| `/boot/grub2/themes/Intervals/` | Final theme folder used by GRUB |

---

## Interview/Resume Summary (STAR Format)

**Situation**: Dual-boot Fedora and Windows system where Fedora GRUB installation failed to detect Windows or apply theme correctly.

**Task**: Restore access to both OSes, apply a custom GRUB theme, and document the process for professional review.

**Action**:
- Manually repaired GRUB to re-detect Windows EFI
- Built and installed Intervals GRUB theme from GitHub
- Customized BLS boot entries for clarity and UX

**Result**: Dual-boot system fully operational with custom GRUB menu; all config steps documented and reusable for future Linux deployments.

---

## References & Resources

- https://github.com/yeyushengfan258/Intervals-grub-theme
- https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/kernel-module-driver-configuration/Working_with_the_GRUB_2_Boot_Loader/
- https://wiki.archlinux.org/title/GRUB
- StackOverflow, ChatGPT, and Fedora forums
