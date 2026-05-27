# fix-missing-kali
This usually happens because your Kali package lists are outdated or partially synced with the mirror. The repository updated, but your system is still trying to download old package versions that no longer exist.

Run these commands step-by-step:

```bash
sudo rm -rf /var/lib/apt/lists/*
sudo apt clean
sudo apt update --fix-missing
```

Then upgrade again:

```bash
sudo apt full-upgrade -y
```

If it still fails, replace your Kali mirror source with the official rolling repository.

Open sources list:

```bash
sudo nano /etc/apt/sources.list
```

Remove everything inside and add:

```bash
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
```

Save:

* CTRL+O → Enter
* CTRL+X

Then run:

```bash
sudo apt clean
sudo apt update
sudo apt full-upgrade -y
```

If packages are broken, repair them:

```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
```

You can also test another mirror:

```bash
sudo sed -i 's|http://http.kali.org/kali|http://kali.download/kali|g' /etc/apt/sources.list
sudo apt update
```

To check your current Kali version:

```bash
cat /etc/os-release
```

And verify repositories:

```bash
grep -v '^#' /etc/apt/sources.list
```
A black screen after updating Kali Linux usually means one of these happened during the upgrade:

* Broken graphics driver (NVIDIA/AMD/VirtualBox)
* Incomplete upgrade
* Display manager failed (LightDM/GDM)
* Kernel/initramfs issue
* Full disk during update

First check whether the system is actually booting.

## 1. Try switching to TTY console

Press:

```text id="4x4r1g"
CTRL + ALT + F2
```

(or F3/F4/F5)

If login appears:

* your system is alive
* only the graphical interface is broken

Login with your username/password.

---

## 2. Repair broken packages

Run:

```bash id="d6m3u9"
sudo dpkg --configure -a
sudo apt --fix-broken install
sudo apt update
sudo apt full-upgrade -y
```

---

## 3. Reinstall display manager

For Kali XFCE:

```bash id="lb89c8"
sudo apt install --reinstall kali-desktop-xfce lightdm -y
sudo dpkg-reconfigure lightdm
sudo reboot
```

Choose `lightdm` when prompted.

For GNOME:

```bash id="fh0byf"
sudo apt install --reinstall kali-desktop-gnome gdm3 -y
sudo reboot
```

---

## 4. NVIDIA users (very common black screen cause)

If you installed proprietary NVIDIA drivers, boot may fail after kernel update.

Remove broken drivers:

```bash id="vl4r6n"
sudo apt purge '^nvidia'
sudo reboot
```

Then reinstall properly later.

---

## 5. Check disk space

A full disk can corrupt upgrades.

```bash id="0n4zth"
df -h
```

If `/` is 100%, remove cache:

```bash id="lc7fny"
sudo apt clean
```

---

## 6. Rebuild initramfs and GRUB

```bash id="j7l8wo"
sudo update-initramfs -u -k all
sudo update-grub
sudo reboot
```

---

## 7. If system still black screens

At GRUB menu:

1. Hold `Shift` during boot
2. Select:

   ```text id="2ltjlwm"
   Advanced options for Kali GNU/Linux
   ```
3. Boot older kernel

If older kernel works, the latest kernel or driver caused the issue.

---

## 8. Virtual machine users

If Kali runs in:

* VirtualBox
* VMware Workstation

Then reinstall guest tools:

### VirtualBox

```bash id="m4y2c5"
sudo apt install --reinstall virtualbox-guest-x11 -y
```

### VMware

```bash id="s2c4zn"
sudo apt install --reinstall open-vm-tools-desktop -y
```

---

Tell me:

* physical PC or VM?
* NVIDIA or Intel graphics?
* can you access TTY with CTRL+ALT+F2?
* what happens after boot logo?
