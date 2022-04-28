# Arch Linux Notes

## XMonad

Shift + Alt + C - close window.

## Sound

cat /proc/asound/cards

/etc/modprobe.d/alsa-base.conf:

```filecontents
options snd-hda-intel index=1,0
```

`alsamixer` to tweak volume

`speaker-test -t wav c 6` to test audio

## Networking

`ip link` list intefaces

`ip address add 10.0.1.19/255.255.255.0 10.0.1.255 dev ens9` configure a static ip

`ip route add default via 192.168.1.254` add a default route

`ip route show` show ipv4 routes

`ip -6 route` show ipv6 routes

`pacman -S dhclient` to install DHCP Client

`systemctl start dhclient@ens9.service` to start for interface `ens9`

`dhclient ens9` to get an IP fron dhcp.

`useradd --create-home a2f0`

`usermod -a -G wheel a2f0`

`passwd a2f0`

```bash
pacman -S `ansible git openssh`
git clone https://github.com/a2f0/dotfiles.git
```

```bash
git clone https://github.com/blackbox/blackbox.git
Username: a2f0 
Password: <your_personal_access_token>
```

## Package Management

`pacman -F xlsfonts` equivalent of whatprovides

## Fonts

`pacman -S terminus-font` install terminus

`fc-list` to list fonts
`xterm -fa "DejaVu Sans Mono" -fs 20`
`xterm -fa "Terminus" -fs 20`

https://archlinux.org/groups/ - package groups

1. Even after hosing the EFI partition, you can hold down `alt` to boot from a secondary device.
2. Lightning based, Broadcom doesn't work.

Base OS - MacOS Big Sur

1. Disable Filevault.

Reboot into recover mode by holding `cmd + r`.

Inside of recovery mode, go to `Utilities` -> `Terminal`

Inside of `Terminal` type `csrutil disable` and then restart the machine from the Apple menu.

Download [rEFInd](https://sourceforge.net/projects/refind/) from SourceForge, for example [0.13.3.1/refind-bin-gnuefi-0.13.3.1.zip](https://sourceforge.net/projects/refind/files/0.13.3.1/refind-bin-gnuefi-0.13.3.1.zip/download)

Note about reEFInd

Open Disk Utility in MacOS, select the Disk (not the volume) and click the `Partition` button.  The utility will require the partition to be formatted with APFS.

put in a USB    `diskutil list`

`sudo diskutil unmount /dev/disk2s1`

Note: the `r` in front of the disk identifier.

md5 archlinux-2022.04.05-x86_64.iso

sudo dd bs=4M if=./archlinux-2022.04.05-x86_64.iso of=/dev/disk2 conv=fsync status=progress

boot from the LiveCD

Make sure the system is booted in efi mode. This can be determined by successfully listing the contents of this directory `ls /sys/firmware/efi/efivars`

## Partitioning

`fdisk -l` to list disks

`fdisk /dev/sda`

`d` and delete partition 3 (APFS formatted)

mkswap /dev/sda3
mkfs.ext4 /dev/sda4

swapon /dev/sda3

mount /dev/sda4 /mnt

`pacstrap /mnt base linux linux-firmware base-devel`

genfstab -U /mnt >> /mnt/etc/fstab

systemctl daemon-reload

Chroot the system `arch-chroot /mnt`

set the root password with `root`.

## Network Interfaces

pacman -Q list installed packages

pacman -S `dhclient`

ip link

ip link set dev ens9 up

## Boot loader

`pacman -S grub efibootmgr`

Mount the EFI partition

`mount --mkdir /dev/sda1 /mnt/boot`

`grub-install --target=x86_64-efi --efi-directory=/mnt/boot --bootloader-id=GRUB`

`grub-mkconfig -o /boot/grub/grub.cfg`

## Grub v2.06

Booting from a USB

`ls` to list devices (look for msdos)

set root(hd6,msdos2)

`ls (hd6,msdos2)/` to list files (note: trailing `/`)

`ls (hd6,msdos2)/efi`

`chainloader /efi/boot/BOOTx64.EFI`

## Wireless

`lspci | grep Network` should show the integrated BCM4360

Download the tarball of the Broadcom-wl driver

https://archlinux.org/packages/community/x86_64/broadcom-wl/

mkdir /mnt/usb

plug in usb key get device id from `dmesg`

mount -t exfat /dev/sdd1 /mnt/usb

pacman -U broadcom-wl-6.30.223.271-378-x86_64.pkg.tar.zst

`pacman -Ql broadcom-wl` to list files owned by the package.

pacman -S broadcom-wl-dkms

rmmod b43
rmmod ssb
modprobe wl


iw list

iw wlp2s0 link

iw wlan0 scan

network={
    ssid="AlphaBravo 5GHz"
    psk=BBBCCCDDD........
}

wpa_supplicant -i wlp2s0 -c /etc/wpa_supplicant/home.conf 

BLACKLIST
