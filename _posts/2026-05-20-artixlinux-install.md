---
layout: post
title: Artix Linux安装
date: 2026-05-20 00:00:00 +8000
---

# Artix Linux安装

520当然要陪电脑过啦\(\).本教程将说明如何给单硬盘，UEFI启动的电脑去掉Windows并安装Artix Linux。

>## The Art of Linux - Simple. Fast. Systemd-free.
>
>### Artix Linux is a rolling-release distribution, based on Arch Linux.
>### It uses real init systems, because PID1 must be simple, secure and stable. 
>
> -[Artix Linux官网首页](https://artixlinux.org/)

Artix Linux是一个基于ArchLinux的发行版。正如官网所说，Artix Linux是滚动发行版，软件包会持续得到更新，而不是固定在某个版本。Artix Linux不使用systemd这种极其复杂，极其庞大，耦合度高的init系统，而是使用OpenRC、s6、runit、dinit这些轻量，配置简洁，低耦合的init系统。

>继续本篇之前，需要明确一点，Artix Linux所使用的几个init系统，普及度远不及systemd。如果使用过程中出现了问题，可能无法通过systemd的方法去解决。如果不太了解其中的区别，建议优先使用中文社区更庞大的ArchLinux。如果想尝试Artix Linux，或者摆脱systemd的"大手"，应当多查阅资料。

>善用pacman -Ss命令去搜索软件包，并配合各种AI工具和浏览器，可提升大量系统使用体验。

---

##安装前的准备

>本篇介绍通过tty界面手动安装OpenRC的Artix Linux，其他init系统(s6、runit、dinit)请查阅其他资料。图形化的安装在本篇也不赘述，但可以通过图形化里面的终端来安装\(本教程也适用\)，通过图形化启动可以通过图形化界面连接WiFi，省掉复杂的命令行。

1. 以自己喜欢的方法将数据备份到其他设备或者云盘，并且准备一个U盘
> <span style="color:red;">警告：任何操作均强烈建议备份数据，以防止数据丢失。</span>
>没有U盘也可以将ventoy烧录到另一个硬盘<span style="color:red;">但是会清除数据</span>。本篇不介绍这个方法。

>如果你更硬核，并且没有U盘和多硬盘，你可以直接将Ventoy作为系统引导而不使用GRUB\(或者wimboot\)。这种操作一旦翻车将无法进入操作系统而变成大号板砖，只能通过烧录了镜像的U盘来救系统。本篇不介绍此方法。

2. 确保网络环境

如果你可以使用路由器分接出来的网线，以 dhcp 的方式直接上网，那么不用准备什么。如果你的环境只能使用无线网络安装，需要事先把自己所用的 wifi 名称改成自己能记住的英文名称。因为安装时无法显示和输入中文名的 wifi，你会看到一堆不知道是什么的方块，并且在安装过程中你将没有办法输入中文的无线名称进行连接。

>注意：一般情况下，tty无法显示中文，不论是安装还是安装完毕，包括安装了中文字体与给GUI配置了中文。

3. 下载并烧录Ventoy

在[Ventoy官网](https://www.ventoy.net/cn/download.html)下载适合你当前操作系统的文件。并通过该网站的`文档手册-使用说明`页面，将Ventoy安装到U盘或者另一个硬盘。

>Ventoy是一个制作可启动U盘的开源工具。有了Ventoy你就无需反复地格式化U盘，只需要把文件直接拖到U盘里面就可以启动了，无需其他操作。你可以一次性拷贝多个镜像文件并创建子目录分类存放，Ventoy 会在启动时显示一个菜单来供你进行选择，同时还不影响U盘的日常使用。
>
> <span style="color:red;">但是，首次安装Ventoy会格式化U盘。</span>

4. 下载Artix Linux的OpenRC安装镜像

在[镜像站](https://mirrors.tuna.tsinghua.edu.cn/artixlinux-iso)\(推荐\)或[官网](https://artixlinux.org/download.php)下载`artix-base-openrc-yyyymmdd-x86_64.iso`文件。下载完毕后放到U盘。如果烧录的是另一块硬盘则放到提前准备的fat32文件系统的分区。

>yyyymmdd是日期，yyyy是年，mm是月，dd是日。截至本文编写，安装镜像的名称是`artix-base-openrc-20260402-x86_64.iso`。意思是这个iso镜像是artix发行版，含有最基础的启动套件，使用openrc作为init系统，于2026年4月2日发布\(实际下载时日期会改变，以实际为准\)，用于x86_64指令集。现代的Intel与AMD都是x86_64。

5. 了解你的主板启动模式

通过磁盘工具\(例如Windows安装DiskGenius\)查看是否有几百mb的`efi`或者`esp`分区。如果没有则进入到主板设置，尝试找到启动模式，将其设置为UEFI。如果没有efi且无法设置则只能使用Legacy引导。本文暂不讲述Legacy引导方式安装。
如果你需要保留Windows操作系统，使用DiskGenius提前分区。本文不介绍多系统共存，全盘安装一个Aritix Linux。

6. 关闭主板安全启动

查询你的主板品牌如何关闭安全启动\(secure boot\)。安全启动会影响到Artix Linux的安装与启动。

>或者在接下来进入Artix Linux的Live环境来确认。


##开始安装

1. 启动到Artix Linux的Live环境

查询你的主板品牌如何进入选择硬盘引导界面。插入U盘，重启/开机电脑，电脑屏幕点亮后立即连点按键，直到出现选择硬盘界面。

>如果不知道具体哪个设备，可以一个一个尝试。当屏幕出现Ventoy字样的图片时则说明选择正确。

通过键盘的上下键，移动到artix-base一栏回车，normal mode再按一下回车，向下移动到From CD/DVD/ISO: artix.x86_64然后回车，稍等片刻，直到出现大量文字，最下面一行是`artix-live login: _`，说明Live环境启动完毕。

2. 登陆

提示：在tty界面，使用TAB或者上下切换历史命令或者左右移动光标失败时，将会听到很大的蜂鸣器的声音。请做好心理准备。
使用root账户登陆:输入`root`。然后输入密码`artix`。

>在输入密码时候，默认是不给提示的/(星号都没有/)。这在Live与正式使用，tty与终端模拟器都是这样的。

如果登陆成功，tty最后一行将显示`artix-live:\[root\]:~# _`

3. 查询电脑是否为UEFI启动

>如果不确定是否为UEFI启动，输入
>```
>ls /sys/firmware/efi/efivars
>```
>如果输出了很多行，则电脑是UEFI启动，否则为Legacy启动。

4. 连接网络

如果电脑连接到有线网络，它会自动配置。
如果需要连接WiFi，则使用`[wpa_supplicant](https://wiki.archlinuxcn.org/wiki/Wpa_supplicant)`工具

>运行以下命令进入wpa_cli交互界面
>```
>wpa_cli
>```
>运行后行首变为`>`
>wpa_cli会自动寻找无线网卡接口，通常为wlan0
>
>查看可用WiFi
>```
>scan
>scan_results
>```
>注意每一条最后`SSID`的信息，这个是网络的名称。
>
>添加网络，配置凭证并启用
>```
>add_network
>set_network 0 ssid "mywifi"
>set_network 0 psk "mywifipasswd"
>enable_network 0
>```
>这里的`mywifi`是WiFi名称，`mywifipasswd`是WiFi的密码
>如果你不明白意思，将WiFi名称设置为`mywifi`，密码设置为`mywifipasswd`，然后直接抄命令。
>
>连接成功后，退出wpa_cli回到tty
>```
>quit
>```
>回到tty将显示`artix-live:\[root\]:~# _`
>
>运行命令获取IP地址
>```
>dhclient
>```
>
>获取IP地址后，运行命令检查连接
>```
>ping m.bilibili.com
>```
>
>确认连接无误后，运行以下命令同步时间
>```
>rc-service ntpd start
>```
>
\(可选\)连接[SSH](https://wiki.archlinuxcn.org/wiki/OpenSSH)

如果使用WiFi等局域网，可以用SSH连接，使用其他设备\(包括各个Linux发行版、Windows、Mac、BSD与Android\)连接到Artix Linux Live进行安装。可以复制粘贴命令，也更容易截图求助。

如果Artix Linux连接到了公网，那么就不会有局域网限制。甚至可以让其他人使用SSH帮你安装\(但这样就没有折腾的意义了，真要这样建议使用图形化更方便\)。
<span style="color:red;">警告：如果能连接到公网，那么别人除了能为你安装，还能对你进行一些"别有用心"的操作。不使用密钥的SSH暴露到公网是危险的。</span>
>局域网与公网的详细概念在本篇不做具体描述。只需要知道两个设备连接一个WiFi一定能用局域网连接。
>
>先确认你手上除了Artix Linux Live,还有什么设备连接了同一个Wifi。设备可以是各个Linux发行版、Windows、Mac、BSD与Android。
>Windows使用[PuTTY](https://www.puttyi.com/tutorial.html)连接。各个Linux发行版、Mac、BSD在终端使用`openssh`连接。Android使用termux并且在termux安装`openssh`连接。
>
>如何安装工具网上教程极多，在此不赘述。但在这里会说一下Artix Linux Live\(就是那些教程所说的"服务器"或者"服务端"\)的配置。
>
>本处只介绍IPv4密码连接的方法。如果想用IPv6或者密钥连接请查询网络教程。
>
>使用[Vim](https://wiki.archlinuxcn.org/wiki/Vim)编辑sshd\(即SSH的服务器守护进程\)配置文件
>```
>vim /etc/ssh/sshd_config
>```
>运行后会变为很多行首为`#`的语句。
>
>按下键盘的`/`，你可以看到屏幕左下角出现了`/`后面跟着一个光标，这就是搜索模式。
>输入`PermitRootLogin`，然后回车搜索。可以按`n`搜索下一个或者`N`搜索上一个。找到`#PermitRootLogin prohibit-password`这一行，将光标移动到`#`后面。按一下`i`，左下变为`INSERT`，即插入模式。点一下`BACKSPACE`删除前面的`#`。然后向后移动，将`prohibit-password`删掉，改为`yes`。
>
>如果删除错了，将被删除的字符再输入。如果忘记或者不确定删除的东西，不要紧张，按一下`ESC`，然后多按几下`u`来撤销操作。
>
>编辑完毕后，按下`ESC`，然后按下`:`，左下角会出现`:`字符，即末行模式，再输入`wq`来保存并退出文件。
>
>回到tty将显示`artix-live:\[root\]:~# _`
>然后运行
>```
>rc-service sshd restart
>```
>sshd将按照配置文件重新启动\(如果未启动则会按照配置文件启动\)。然后你可以通过其他设备使用SSH连接到Artix Linux Live了。

5. 换镜像源
更换镜像源可以加快下载速度。这里使用南京大学的镜像源。
>使用vim编辑Artix Linux的镜像源文件
>```
>vim /etc/pacman.d/mirrorlist
>```
>然后按下`:%d`再回车清除全部内容
>
>按`i`进入编辑模式，输入以下内容
>```
>Server = https://mirror.nju.edu.cn/artixlinux/$repo/os/$arch
>```
>
>输入完毕后按下`ESC`退出编辑模式，然后按下`:wq`再回车保存并退出。
>
>回到tty将显示`artix-live:\[root\]:~# _`

6. 硬盘分区

>使用以下命令确定你的存储设备
>```
>lsblk
>```
>
>这个命令会输出电脑能读取的[块设备](https://wiki.archlinuxcn.org/wiki/Device_file)。判断一个硬盘是不是你想要安装Artix Linux的硬盘，只需要看硬盘各个分区大小与格式。
>需要关注两个地方
>`NAME`列是这个硬盘的块设备名称，他是一个列表结构。`SIZE`列是这个硬盘或者分区的大小。
>机械硬盘与SATA固态一般为`sda`，如果有多个硬盘则最后一个字母顺延，例如`sdb` `sdc`以此类推。而各个硬盘的分区则是后面加一个数字，多个分区则数字顺延，例如`sda1` `sda2` `sdb1` `sdb2`以此类推。
>
>NVME硬盘名称以`nvme`开头。之后是一个从`0`开始的数字，表示设备控制器号。`nvme0`表示第一个发现的 NVME 控制器，`nvme1`表示第二个发现的，以此类推。然后是字母`n`和一个从`1`开始的数字，表示控制器上的设备号。比如`nvme0n1`表示第一个控制器上第一个发现的设备，`nvme0n2`表示第一个控制器上第二个发现的设备，以此类推。 而各个硬盘的分区则是后面加字母`p`和一个数字，多个分区则数字顺延，例如`nvme0n1p1` `nvme0n1p2` `nvme0n1p3`。
>
> <span style="color:red;">警告：接下来的行为会清除数据，务必备份好再继续。并且千万不要选错硬盘。如果没备份好，可以输入`poweroff`来关机。</span>
>使用以下命令进入分区工具
>```
>cfdisk /dev/nvme0n1
>```
>/dev/后面是你硬盘的块设备名称，你想要安装到哪个硬盘就选择哪个。示例是`nvme0n1`，实际操作以自己的`lsblk`反馈为准。<span style="color:red;">注意：这里必须选择硬盘而不是分区，如果不小心写成了分区，按下`Ctrl+c`退出分区工具。</span>
>
>键盘的上下键可以选择这个硬盘的分区，左右键可以选择下方的操作，高亮的就是当前选择。或者按下每个选项的首字母进行快速操作，但不建议使用这个，因为按下即触发。
>
>首先删除全部硬盘分区，只留下`Free space`。
>删除完毕后，对着`Free space`选择`New`，下方会提示你`Partition size`，即分区大小，输入`50M`，回车。对着刚刚创建的50mb的分区\(顶部的Size列，下面会有一个`50M`\)，选择`Type`，即更改分区类型，在子菜单按键盘上下选择`EFI System`。然后对着这个分区选择`Bootable`，按一下回车。
>
>[Swap](https://wiki.archlinuxcn.org/wiki/Swap)分区是可选的，本教程所展示的不会运行这一步。用于在硬盘上暂存暂时用不到内存页面的地方，以为更加常用的数据腾出更多空间。可以先不设置Swap分区，之后使用操作系统时建立Swap文件，他们的效果是一样的。如果设置了Swap分区，请自行根据实际情况判断并挂载。
>
>最后是创建系统分区，同样的对着`Free space`选择`New`，但是这次在`Partition size`不输入任何东西直接回车，即创建剩余空间。然后选择`Type`，在子菜单按键盘上下选择`Linux filesystem`。
>
>完成以上操作后，<span style="color:yellow;">务必选择`Write`来保存</span>，直接`Quit`或者按下`Ctrl+c`退出**不会**保存这些更改。
>写入后再选择`Quit`或者按下`Ctrl+c`
>回到tty将显示`artix-live:\[root\]:~# _`

6. 创建[文件系统](https://wiki.archlinuxcn.org/title/File_systems)

本篇将使用[Ext4](https://wiki.archlinuxcn.org/wiki/Ext4)文件系统，这是一种古老的文件系统，虽然没有例如[Btrfs](https://wiki.archlinuxcn.org/wiki/Btrfs)等文件系统的高级特性，但它久经考验，极其稳定。
>将efi分区格式化为fat32
>```
>mkfs.fat -F 32 /dev/nvme0n1p1
>fatlabel /dev/nvme0n1p1 ESP
>```
>
>将系统分区格式化为ext4
>```
>mkfs.ext4 -L ROOT /dev/nvme0n1p2
>```

7. 挂载并安装Artix Linux

之前极其繁琐的操作，就是为了这一刻。
>将系统分区挂载到/mnt
>```
>mount /dev/nvme0n1p2 /nmt
>```
>
>在系统分区中创建文件夹并且挂载efi分区
>```
>mkdir /mnt/boot/efi
>mount /dev/nvme0n1p1 /mnt/boot/efi
>```
>
>安装基础系统
>```
>basestrap /mnt base base-devel openrc elogind-openrc linux linux-firmware vim sudo bash bash-completion firefox
>```
>这将会开始从网络下载软件包，为你挂载的硬盘安装Artix Linux操作系统。这需要一定的时间。
>
>运行结束后显示`artix-live:\[root\]:~# _`
>
>生成[fstab](https://wiki.archlinuxcn.org/wiki/Fstab)文件
>```
>fstabgen -U /mnt >> /mnt/etc/fstab
>```
>
>[chroot](https://wiki.archlinuxcn.org/wiki/Chroot)到硬盘上的Artix Linux
>chroot可以让你不退出原操作系统的情况下进入目标操作系统，这个操作在无法开机时维修很常见。
>```
>artix-chroot /mnt # formerly artools-chroot
>```
>此时tty的最后一行会改变
>
>安装微码 
>根据使用的CPU安装对应的微码.注意，使用`pacman -S`命令时，会提醒你是否确认安装，直接回车即可。以下不再赘述。
>```
>pacman -S intel-ucode
>```
>
>```
>pacman -S amd-ucode
>```
>
>安装引导程序 
>到现在为止，电脑只有一个空的efi，需要安装[GRUB](https://wiki.archlinuxcn.org/wiki/GRUB)来让电脑可以启动。
>```
>pacman -S grub efibootmgr
>grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Artix
>grub-mkconfig -o /boot/grub/grub.cfg
>```
>
>设置主机名
>设置一个你喜欢的主机名，不要含有空格或者特殊字符
>```
>vim /etc/hostname
>```
>进入后直接输入主机名，输入完毕按下`ESC`然后`:wq`
>
>接下来在设置与其匹配的条目
>```
>vim /etc/hosts
>```
>输入以下内容\(如果有重复的就不要输入重复内容\，注意换行。`HOSTNAME`是刚刚设置的主机名。
>```
>127.0.0.1   localhost
>::1         localhost
>127.0.1.1   HOSTNAME.localdomain HOSTNAME
>```
>
>设置时区，时区只有上海，没有北京。
>```
>ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
>```
>
>将系统时间同步到硬件时间
>```
>hwclock --systohc
>```
>
>设置Locale
>`Locale`决定了软件使用的语言。
>```
>vim /etc/locale.gen
>```
>利用`/`来搜索，去掉`en_US.UTF-8 UTF-8`以及`zh_CN.UTF-8 UTF-8`这俩行前面的`#`
>然后按下`ESC`和`:wq`退出
>然后生成Locale
>```
>locale-gen
>```
>创建locale.conf
>```
>vim /etc/locale.conf
>```
>输入以下内容，注意换行
>```
>LANG=en_US.UTF-8
>LANG=zh_CN.UTF-8
>```
>
>为root用户设置密码
>```
>passwd root
>```
>根据提示输入密码，千万不要忘记。输入过程不会显示，包括星号。
>
>创建平时使用的用户。使用你喜欢的名字代替下文的`USERNAME`
>```
>useradd -m USERNAME
>```
>
>为创建的用户设置密码，千万不要忘记
>```
>passwd USERNAME
>```
>
>将创建的用户加入[sudo](https://wiki.archlinuxcn.org/wiki/Sudo)组
>```
>usermod -aG wheel USERNAME
>```
>
>检查sudo文件是否正确
>```
>visudo
>```
>利用`/`搜索找到`%wheel ALL=(ALL:ALL) ALL`这一行，并且删掉行首的`#`，最后使用`:wq`保存退出。如果没有`#`就直接退出。
>
>安装网络服务
>```
>pacman -S networkmanager networkmanager-openrc
>rc-update add NetworkManager default
>```
>
>安装一个桌面环境
>运行以下命令安装xfce4桌面环境。这是一个轻量的桌面环境。
>这一步你可以选择其他桌面环境，但是[除了GNOME](https://ostechnix.com/artix-linux-drops-gnome-desktop-environment/)，因为它强依赖systemd。
>```
>pacman -S xfce4
>```
>运行后会先让你选择安装哪些东西，直接回车全部安装
>
>安装中文字体
>```
>pacman -S noto-fonts-cjk noto-fonts-emoji
>```
>
>安装一个sddm登陆管理器
>```
>pacman -S sddm sddm-openrc
>rc-update add sddm default
>```
>
>退出chroot
>```
>exit
>```
>回到tty将显示`artix-live:\[root\]:~# _`
>
8. 完成安装，并享受你的新操作系统
>输入`poweroff`关闭电脑，然后拔掉U盘，再开机。过一会电脑将会显示登陆管理器，和你的用户名，输入密码，登陆，你将会看到一个很简约的xfce4桌面。

---

以上就是安装Artxi Linux的全部内容了。这里只安装了最精简的xfce4。在后面的篇章将会讲如何去安装其他的桌面环境或者窗口管理器。
