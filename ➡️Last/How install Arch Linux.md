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
