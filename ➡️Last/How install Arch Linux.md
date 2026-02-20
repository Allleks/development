1. Found HDD
```bash
fdisk -l
```
2. create new partition
```bash
fdisk /dev/sdb
g (new gpt partition)
n (create uefi partition)
	1 +550M
n (create user partition)
	all
t (check 1)
	1 (uefi)
t (check 2)
	20 (ext4)
w (save result and exit)
```
3. format partition
```bash
mkfs.fat -F32 /dev/sd1
mkfs.ext4 /dev/sdb2
```
4. Mount /mnt for partition user
```bash
mount /dev/sdb2 /mnt
```
5. install core
```bash
pacstrap /mnt linux base linux-firmware
```
6. *install file system table*
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
7. change root
```bash
arch-chroot /mnt
```
8. install vim sudo
```bash
pacman -S vim sudo
```
9. generate locale
```bash
vim /etc/locale.gen
	and uncomment
ru_UTF8 en_US
	and
locale-gen
```
10. rename PC (create new file)
```bash
vim /etc/hostname

127.0.0.0    localhost
::1          localhost
127.0.0.1    pc_name.localdomain pc_name
```
11. added password for root
```bash
passwd
	next input password
```
12. Add user
```bash
useradd -m user_name
```
13. Add user in group
```bash
usermod -aG wheel, audio, video, storage user_name
```
14. edit SUDO
```bash
//pacman -S sudo
EDITOR=vim visudo
	uncomment "% wheel ALL=(ALL)ALL"
```
15. Install packege for work internet
```bash
sudo pacman -S networkmanager iwd
systemctl enable NetworkManager или iwd.service
```
guide for iwd
```bash
iwctl
device list
station device_name scan
station device_name get-networks
station d_n connect wifi_name
input password
station d_n show
exit
```
16. Input loader
```bash
pacman -S refind gdisk
refind-install
```
add boot settings
```bash
vim /boot/efi/EFI/refind/refind.conf
	search line "Arch Linux"
		edit "PARTUUD=/dev/sdb2"
		
vim /boot/refind_linux.conf
	edit "delete 1 and 2 line"
```
17. Exit which root and umount /mnt
```bash
exit
umount /mnt -l
```
___
### If there are installation problems
1. Step loaded live-cd iso arch linux
2. In command line `mount /dev/sda2 /mnt`
3. Access root `arch-chroot /mnt`
4. Fix problems -> install network packeges ...
5. `exit umount /mnt -l`
___
# Guide for install Hyprland

1. install packages (*firts install yay*)
```bash
git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si //need base-devel!!!
```
2. Install Hyprland
```bash
sudo pacman -S kitty or yay -S kitty
yay -S hyprland
yay -S nautilus
yay -S google-chrome
yay -S ntfs -3g
yay -S gtk3 gtk4 //for paint theme //usr/share/themes
```
3. copy git config 
```bash
~/.config/hypr/hyprland.conf
```