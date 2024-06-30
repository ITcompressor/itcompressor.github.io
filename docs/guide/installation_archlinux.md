Гайд по установке archlinux

**tags** `best linux distro`
**time** 2021-11-22 22:16

Мой микрогайд по установке и настройке archlinux. Если вы новичек в linux, то лучше даже не пробовать. Другие гайды:
- С шифрованием [https://github.com/48d90782/archlinux_modern_luks_install](https://github.com/48d90782/archlinux_modern_luks_install)
- Простой [https://www.tecmint.com/arch-linux-installation-and-configuration-guide/](https://www.tecmint.com/arch-linux-installation-and-configuration-guide/)
- Официальный [https://wiki.archlinux.org/index.php/Installation_Guide](https://wiki.archlinux.org/index.php/Installation_Guide)

## 0. Скачиваем iso образ с https://www.archlinux.org/

## 1. Создаем загрузочную флешку

```bash
dd if=archlinux.img of=/dev/sdX bs=16M && sync # on linux
```

также можно через GUI с помощь. [https://www.balena.io/etcher/](https://www.balena.io/etcher/)

## 2. Подключаемся к точке wifi

```bash
# 1
iwctl
# 2 
station list
# 3
station <generally wlan0> connect <wifi network name SSID> 
# -> station wlan0 connect 0xdev
# enter your password and exit (type exit -> enter)
```

## 3. Разбиваем диски (для nvme дисков будет примерно такие же названия из коробки)

```bash
cfdisk /dev/nvme0n1
```  

### 3.1. 512MB EFI partition

`/dev/nvme0n1p1`

### 3.2. Остальное пойдет на данные `/dev/nvme0n1p2`

## 4. Создание EFI partition

```bash
mkfs.fat -F32 /dev/nvme0n1p1
```

## 5. Шифруем раздел диска, где будут храниться данные

```bash
cryptsetup -c aes-xts-plain64 --key-size 512 --hash sha512 --iter-time 3000 -y --use-random luksFormat /dev/nvme0n1p2
cryptsetup luksOpen /dev/nvme0n1p2 luks
```

## 6. Размечаем зашифрованный разде

```bash
pvcreate /dev/mapper/luks
vgcreate vg0 /dev/mapper/luks
lvcreate --size 16G vg0 --name swap # (Если вы планируете использовать гибернацию, то память рассчитываем: Ram * 1.5)
lvcreate -l +100%FREE vg0 --name root
```  

## 7. Форматируем

```bash
mkfs.ext4 /dev/mapper/vg0-root  
mkswap /dev/mapper/vg0-swap
```  

## 8. Монтируем разделы для дальнейшей установки

```bash
mount /dev/mapper/vg0-root /mnt # /mnt - это место куда монитруем
swapon /dev/mapper/vg0-swap # Не обязательно, но так можно проверить что все ок с swap
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```  

## 9. Устанавливаем основные пакеты

```bash
pacstrap /mnt base base-devel zsh vim neovim git sudo efibootmgr iwd dhcpcd lvm2 linux linux-headers linux-firmware
```

## 10. Устанавливаем fstab

```bash
genfstab -pU /mnt >> /mnt/etc/fstab
```  

## 11. Добавлем в /tmp a ramdisk (добавляем эту строчку в /mnt/etc/fstab)

```
tmpfs	/tmp	tmpfs	defaults,noatime,mode=1777	0	0
```

## 12. Инициализуем систему

```bash
arch-chroot /mnt
```  

## 13. Настройка времени

```bash
ln -s /usr/share/zoneinfo/Europe/Minsk /etc/localtime <USE YOUR CITY!>
hwclock --systohc --utc
```

## 14. Установка hostname

```bash
echo <YOU HOST NAME> > /etc/hostname
```  

## 15. Раскоментируем строчку с нужной нам локалью в /etc/locale.gen

```bash
vim /etc/locale.gen
locale-gen
localectl set-locale LANG=en_US.UTF-8
echo LANG=en_US.UTF-8 >> /etc/locale.conf # (важно для GNOME)
```

## 16. Устанавливаем пароль для root и добавляем пользователя

```bash
passwd
useradd -mg users -G wheel,storage,power -s /bin/zsh <MYUSERNAME> (or /bin/bash)
passwd <MYUSERNAME>

visudo -> uncomment the following line --> %wheel ALL=(ALL) ALL

Defaults timestamp_type=global (use sudo globally)
Defaults timestamp_timeout=15 (sudo timeout)
```  

## 17. Конфигурируем mkinitcpio с модулями, которые нам нужны для initrd image  (ПОРЯДОК ВАЖЕН!)

```bash
vim /etc/mkinitcpio.conf
```  

Добавляем `ext4` в MODULES. Также если вы хотите чтобы блокировался экран при закрытия крышки ноутбука добавляем
`i915` (Intel) в модули. Добавляем `encrypt` и `lvm2` в HOOKS до filesystems   
Добавляем `resume` после `lvm2` (также `udev` должно быть до этого)  
Мои **hooks** выгледят так: `HOOKS=(base udev autodetect modconf block encrypt lvm2 resume filesystems keyboard fsck)`  
И **modules** `MODULES=(i915 ext4)`

## 18. Перегенерируем initrd image

```bash
mkinitcpio -P
```

## 19. Устанавливаем systembootd

```bash
bootctl --path=/boot install
```

## 20. Создаем loader.conf

```bash
echo 'default arch' >> /boot/loader/loader.conf
echo 'timeout 5' >> /boot/loader/loader.conf
```

## 21. Создаем arch.conf

```bash
vim /boot/loader/entries/arch.conf
```

## 22. Наполняем arch.conf

UUID - это UUID зашифрованного устройства (/dev/nvme0n1p2). Его также можно найти с помощью `blkid` команды

```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options cryptdevice=UUID=<UUID>:vg0 root=/dev/mapper/vg0-root resume=/dev/mapper/vg0-swap rw zswap.enabled=1
```

## 23. Устанавливаем DE

```bash
pacman -S gnome gnome-extra pulseaudio-bluetooth blueman bluez bluez-utils
```

### 24. Добавляем сервисы в автозагрузку

```bash
systemctl enable gdm bluetooth iwd dhcpcd NetworkManager
```

## 25. Выходим из шела

```bash
exit
```

## 26. Отключаем все разделы

```bash
umount -R /mnt
swapoff -a
```

## 27. Грузимся (не забудьте отключить все левые накопители/загрузочную флешку)

```bash
reboot
```

## 28. Установка yay

```bash
sudo pacman -S base-devel

git clone https://aur.archlinux.org/yay.git

cd yay

makepkg -si
```

## 29. Делаем красивым его в консоли

```bash
vim /etc/pacman.conf

# Misc options
UseSyslog
Color
#NoProgressBar
CheckSpace
VerbosePkgLists
ParallelDownloads = 5
```
