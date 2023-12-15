## Загрузка Linux.
### Цель:
#### Попасть в систему без пароля
#### Переименовать VG
#### Добавить модуль в initrd

### Для выполнения работы используются следующие инструменты:
- VirtualBox - среда виртуализации, позволяет создавать и выполнять виртуальные машины;
- Vagrant - ПО для создания и конфигурирования виртуальной среды. В данном случае в качестве среды виртуализации используется VirtualBox;
- Git - система контроля версий

### Аккаунты:
- GitHub - https://github.com/

## Попасть в систему без пароля
## папка [boot](https://github.com/serjb1973/boot/tree/main/boot)
#### Создание образа
```
vagrant up
```
#### Входим в систему без пароля 
[boot.jpg](https://github.com/serjb1973/boot/blob/main/boot/boot.jpg)
```
virtualbox
на загрузке делаем ctrl+c
в строке с linux
1. меняем ro на rw
2. добавляем init=/bin/sh
и нажимаем сtrl-x 
```
#### Входим в систему без пароля
[boot2.jpg](https://github.com/serjb1973/boot/blob/main/boot/boot2.jpg)

#### Меняем пароль root
```
passwd root
touch /.autorelabel
```
## Переименовать VG
## Добавить модуль в initrd
## папка [lvm](https://github.com/serjb1973/boot/tree/main/lvm)
## [out.txt](https://github.com/serjb1973/boot/blob/main/lvm/out.txt)
#### Создание образа
```
vagrant up
```
#### Переименовать VG, ребутнуться, проверить
```
vagrant ssh
sudo su -
vgs
vgrename VolGroup00 OtusRoot
cat /etc/fstab|grep -E "VolGroup00|OtusRoot"
sed -i 's/VolGroup00/OtusRoot/g' /etc/fstab
cat /etc/default/grub |grep -E "VolGroup00|OtusRoot"
sed -i 's/VolGroup00/OtusRoot/g' /etc/default/grub
cat /boot/grub2/grub.cfg |grep -E "VolGroup00|OtusRoot"
sed -i 's/VolGroup00/OtusRoot/g' /boot/grub2/grub.cfg
mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
vgs
shutdown -h 0
```
#### Добавить модуль в initrd, ребутнуться, проверить
```
vagrant up
vagrant ssh
sudo su -
vgs
mkdir /usr/lib/dracut/modules.d/01test
cp /home/vagrant/*sh /usr/lib/dracut/modules.d/01test/
ls -l /usr/lib/dracut/modules.d/01test
mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
dracut -f -v
lsinitrd -m /boot/initramfs-$(uname -r).img | grep test
cat /boot/grub2/grub.cfg |grep -E "rhgb|quiet"
sed -i 's/quiet//g ; s/rhgb//g' /boot/grub2/grub.cfg
exit
exit
vagrant halt
virtualbox
```
[linux2.jpg](https://github.com/serjb1973/boot/blob/main/lvm/linux2.jpg)

