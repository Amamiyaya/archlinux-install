# archlinux安装步骤（自用）

**<big><big>参考了并整理了[大佬的视频教程](https://www.bilibili.com/video/BV1fk4y1w7wq/?spm_id_from=333.880.my_history.page.click&vd_source=3c09440c76698200a2fd30438ed4eeaf)</big></big>**

## 1. 分盘并挂载

```shell
fdisk -l
cfdisk /dev/sdb
```

### (1). 挂载根目录

```shell
mkfs.ext4 /dev/sdb1
mount /dev/sdb1 /mnt
```

### (2). 挂载home目录

```shell
mkfs.ext4 /dev/sdb2
mount --mkdir /dev/sdb2 /mnt/home
```
### (3). 挂载交换区

```shell
mkfs.ext4 /dev/sdb3
mkswap /sdv/sdb3
swapon /dev/sdb3
```
### (4). 挂载EFI分区

```shell
mkfs.fat -F32 /dev/sdb4
mount --mkdir /dev/sdb4 /mnt/efi
```

## 2. 配置国内镜像源

```shell
sudo vim etc/pacman.d/mirrorlist
```

<big>**在第一行添加Server = https:/mirrors.tuna.tsinghua.edu.cn/archlinux/\$repo/os/$arch(清华源)，保存退出。也可通过如下命令换源：**</big>	

```shell
reflector --verbose --country 'China' -l 200 -p https --sort rate --save /etc/pacman.d/mirrorlist
```
```shell
sudo pacman -Syy
```

## 3. 重新安装密钥
```shell
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman -S archlinux-keyring
```
## 4. 安装软件包
**<big>根据cpu型号选择intel-ucode和amd-ucode。zen代表高性能内核，lts代表长期支持稳定版</big>**
```shell
sudo pacstrap /mnt base base-devel linux-zen linux-zen-headers linux-firmware networkmanager grub os-prober efibootmgr ntfs-3g intel-ucode bluez bluez-utils nano vim
```
## 5. 创建fstab (自动挂载配置文件)
```shell
genfstab -U /mnt >> /mnt/etc/fstab
```
## 6. chroot进入系统
```shell
arch-chroot /mnt
```
## 7. 设置时区
```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
## 8. 设置硬件时间
```shell
hwclock --systohc
```
## 9. 设置字符集
```shell
sudo vim /etc/locale.gen
```
**<big>删除en_US.UTF-8 UTF-8和zh_CN.UTF-8 UTF-8前的#（用vim可直接搜索）</big>**

```shell
locale-gen
```
```shell
sudo vim /etc/locale.conf
```
**<big>添加：LANG=en_US.UTF-8</big>**

## 10. 设置主机和密码
```shell
sudo vim /etc/hostname
passwd
```
## 11. 创建普通用户
```shell
useradd -m -G wheel yanami(用户名)
passwd yanami(用户名)
```
## 12. 赋予root权限
```shell
sudo EDITOR=vim visudo
```
**<big>删除%wheelALL=(ALL:ALL)ALL前的#</big>**

## 13. 启动服务
```shell
sudo systemctl enable NetworkManager
sudo systemctl enable bluetooth
```
## 14. 编辑grub
```shell
sudo vim /etc/default/grub
```
**<big>去掉GRUB_DISABLE_OS_PROBER=false前的#</big>**

## 15. 安装更新grub服务引导
```shell
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```
## 16. 安装桌面环境KDE
```shell
sudo pacman -S xorg plasma
systemctl enable sddm
sudo pacman -S alacritty thunar ark
```
```shell
exit
reboot
```
## 17. 进入桌面安装中文字体
```shell
grub-mkconfig -o /boot/grub/grub.cfg
```
```shell
sudo pacman -S adobe-source-han-sans-cn-fonts
```
**进入设置，设置语言为简体中文**
```shell
reboot
```

## 18. 配置国内源
```shell
sudo vim /etc/pacman.conf
```
**去掉multlib以及下一行的#，然后在下面添加：**

```shell
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
或
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
或
Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch
```

```shell
sudo pacman -Syy
```

```shell
sudo pacman -S archlinuxcn-keyring
```
## 19.安装应用商店后端程序
```shell
sudo pacman -S archlinux-apstream-data packagekit-qt5 fwupd
```
```shell
sudo pacman -S yay
sudo yay -Syy
```
## 20. 安装中文输入法
```shell
sudo pacman -S fcitx5-im
sudo pacman -S fcitx5-chinese-addons fcitx5-rime
```
```shell
sudo vim /etc/environment
添加：
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibuS
```
```shell
或者：yay -S fcitx5-input-support
```

```shell
reboot
```

**完结撒花~**
