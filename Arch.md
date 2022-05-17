# Arch邪教全攻略

## 安装系统

首先通过安装介质启动到live环境。选第一个

![img](https://pic1.zhimg.com/80/v2-2681e91b2f946ba746fe78eb30c29c40_720w.jpg)



会以root身份进入一个虚拟控制台中，默认的shell是zsh。

### 验证引导模式

`ls /sys/firmware/efi/efivars` 如果结果显示了目录且没有报告错误，则系统是以 UEFI 模式引导的。



![img](https://pic1.zhimg.com/80/v2-e39547a53ce976be2597ac1a6171a6ac_720w.jpg)



### 连接到因特网

#### 判断无线网卡是否被锁

```text
# rfkill list  
--------------
0: phy0: Wireless LAN
    Soft blocked: yes
    Hard blocked: yes
```

如果出现以上内容，可以调节网卡开关打开它。如果没有开关，那就使用以下命令：

```text
# rfkill unblock wifi
```

#### 连接网络

```text
$ iwctl   //会进入联网模式
[iwd]# help    //可以查看帮助
[iwd]# device list    //列出你的无线设备名称，一般以wlan0命名
[iwd]# station <device> scan    //扫描当前环境下的网络
[iwd]# station <device> get-networks    //会显示你扫描到的所有网络
[iwd]# station <device> connect <network name>
password:输入密码
[iwd]# exit    //退出当前模式，回到安装模式
```

测试网络是否连通：

```text
# ping baidu.com
```

### 更新系统

#### 更新为国内镜像源

```
reflector --country China --age 72 --sort rate --protocol https --save /etc/pacman.d/mirrorlist
```

已将最新的镜像源更新为国内的，保存在/etc/pacman.d/mirrorlist目录下

#### 更新系统时间

`timedatectl set-ntp true` 之后可以使用 `timedatectl status`检查服务状态

#### 通过ssh链接当前主机（可选）

可以通过ssh在另外一台可以正常使用的设备上进行安装，方便命令复制

1.  在当前安装环境下输入`passwd` 为当前环境设置一个密码，不用太长，两三位就可以了。输入时不会显示。
2.  执行`ip -brief address`查看当前ip地址，一般是192.168.<...>.<...> IP地址后面斜杠之后的掩码位不用哦
3.  准备另外一台设备，应当使该设备与安装主机在同一局域网内，就是俩设备都连着同一个WiFi

几种设备的连接方式：

 Windows：在终端输入：`ssh -o StrictHostKeyChecking=no root@<刚刚查看的IP地址>`

 Linux&macOS：在终端输入：`ssh -o StrictHostKeyChecking=no root@<刚刚查看的IP地址>`

 当然也可以使用Android和ios设备，这里不说了，因为那玩意太小了，用来输入命令实在鸡肋

### 磁盘分区

安装archlinux所需分区

```text
EFI分区       300 MB
swap分区      4GB
root分区      剩余空间
```

使用cfdisk工具分区 `cfdisk <install disk name >` 比如我的： `cfdisk /dev/sda` 。之后会进入如下界面，选择gpt分区表：



![img](https://pic2.zhimg.com/80/v2-a215df06456711a851892b0cb5659f6d_720w.jpg)



之后就开始正式分区了，首先EFI分区，点击new新建：



![img](https://pic1.zhimg.com/80/v2-0d48c2252b5398ffeb6d1b1d8da40788_720w.jpg)



这里输入300M，之后回车，就回到上面的界面了。



![img](https://pic2.zhimg.com/80/v2-3d864f6d801a2870bd45fe3b247e9de1_720w.jpg)



在建立下一个分区之前，先对第一个EFI分区的类型做一个修改，选择type选项



![img](https://pic1.zhimg.com/80/v2-5f7420e9fe32635c6e0f87ddb9d5f170_720w.jpg)



重复之前的步骤，建立swap分区和root分区，完成之后如下图：



![img](https://pic3.zhimg.com/80/v2-cf7bb42ede637025071babe516e69b72_720w.jpg)



### 格式化分区

#### EFI分区格式化

```text
mkfs.vfat /dev/sda1
```

#### root分区格式化

```text
mkfs.xfs -f /dev/sda3
# 强制分区为xfs
```

#### 创建swap分区

```text
mkswap /dev/sda2
```

可以使用`lsblk -f` 查看磁盘分区情况

#### 挂载分区

```text
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/vda1 /mnt/boot/efi
swapon /dev/sda2
lsblk -f    ## 查看分区g情况
```

### 安装系统

```text
pacstrap /mnt linux linux-firmware linux-headers base base-devel vim git bash-completion
```

### 生成文件系统的表文件

```text
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

## 进入新系统

### 进入新系统

```text
arch-chroot /mnt
```

### 设置时区

```text
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

### 本地化

#### 设置系统语言

```text
vim /etc/locale.gen
```

将以下两行取消注释(删除前面的井号)

```text
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

#### 生成本地语言信息

```text
locale-gen
```

#### 设置本地语言环境变量

```text
/etc/locale.conf
-------------------------
LANG=en_US.UTF-8
```

### 网络配置

#### 主机设置

```text
vim /etc/hostname
---------------------
archlinux(也可以写成你想要的主机名)
```

#### 生成对应的hosts

```text
vim /etc/hosts
--------------------
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain archlinux   # 这里的archlinux是主机名
```

### 安装相关包

```text
pacman -S grub efibootmgr efivar networkmanager intel-ucode
```

### 配置grub

```text
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 激活启用NetworkManager

```text
systemctl enable NetworkManager
```

### 给root用户建立密码

```text
passwd
输入密码
```

### 重启

```text
exit

umount /mnt/boot/efi
umount /mnt

reboot
```

## 基本配置

### 再次联网

输入`nmtui` 选择 “Activate a connection” 回车进入，选择你需要的网络，连接后back返回即可

### 再次ssh连接

### 安装openssh

```text
pacman -S openssh
```

### 启动ssh服务

```text
systemctl start sshd
```

### 查看当前ip地址

```text
ip -brief address
```

### 修改ssh配置

ssh默认无法连接root用户，需要修改其配置文件使其支持

```text
vim /etc/ssh/sshd_config
--------------------------------------
# 将下列的语句值改为yes
PermitRootLogin yes
```



![img](https://pic3.zhimg.com/80/v2-ef475a0ef5d4f500947cb126784b65da_720w.jpg)



### 在其他端进行连接

```text
ssh -o StrictHostKeyChecking=no root@<刚刚查看的IP地址>
```

### 配置bash shell环境变量

```text
vim /etc/skel/.bashrc
-------------------------------
# 在alias行上面添加
export EDITOR=vim

# 继续添加
alias grep='grep --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'

# 在alias 之后添加脚本
[ ! -e ~/.dircolors ] && eval $(dircolors -p > ~/.dircolors)
[ -e /bin/dircolors ] && eval $(dircolors -b ~/.dircolors)
# 保存退出
```

随后将上述文件复制到home目录下

```text
cp /etc/skel/.bashrc ~
```

### 添加标准用户(以下klelee是我的用户名)

```text
# 添加用户
useradd --create-home klelee
# 设置密码
passwd klelee
```

### 设置用户组

```text
usermod -aG wheel,users,storage,power,lp,adm,optical klelee
```

### 修改当前用户权限

```text
visudo
---------------------------------
# 取消注释以下行
%wheel ALL=(ALL) ALL
```

### 添加ArchLinuxCN 存储库

该仓库是由archlinux中文社区驱动的一个非官方的软件仓库。我们使用的很多软件都需要使用这个库去下载，比如typora。

```text
# 编辑/etc/pacman.conf
vim /etc/pacman.conf
--------------------------------------
# 在最后添加
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch   
# 这是中科大的源，你也可以选择清华、阿里等，当我推荐中科大，因为我喜欢
```

然后更新GPG密钥

```text
pacman -S archlinuxcn-keyring
```

**注** ： 如果以上更新密钥步骤出现错误，就是那种连着一串ERROR的情况，请执行以下步骤

```text
# rm -rf /etc/pacman.c/gnupg
# pacman-key --init
# pacman-key --populate archlinux archlinuxcn
# pacman -Syy
```

### 显卡驱动

```text
pacman -S xf86-video-intel vulkan-intel mesa
```

### 声卡配置

```text
# pacman -S alsa-utils pulseaudio pulseaudio-bluetooth cups
```

## 图形界面

### 显示服务

```text
pacman -S xorg
```

### 安装字体

### 英文字体

```text
pacman -S ttf-dejavu ttf-droid ttf-hack ttf-font-awesome otf-font-awesome ttf-lato ttf-liberation ttf-linux-libertine ttf-opensans ttf-roboto ttf-ubuntu-font-family
```

### 中文字体

```text
pacman -S ttf-hannom noto-fonts noto-fonts-extra noto-fonts-emoji noto-fonts-cjk adobe-source-code-pro-fonts adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-hk-fonts adobe-source-han-sans-tw-fonts adobe-source-han-serif-cn-fonts wqy-zenhei wqy-microhei
```

### 打开字体引擎

```text
vim /etc/profile.d/freetype2.sh
--------------------------------------------
# 取消注释最后一句
export FREETYPE_PROPERTIES="truetype:interpreter-version=40"
```

### 安装桌面环境（xfce4)

#### xfce4

```text
sudo pacman -S xfce4 xfce4-goodies
```

#### 设置sddm登录

```text
systemctl enable sddm
```

## 深层配置

###  安装软件

Manjaro背靠Arch软件仓库，安装软件爽的yp，仓库又全又新，基本上遇不到依赖问题需要手动去搜该怎么安装，这也是我不愿意换回Ubuntu的一个重要原因

```bash
sudo pacman -S yay
```

yay是一个用Go语言写的一个AUR助手，有些时候官方仓库没有你想要的软件，就需要通过yay来安装

有了yay，以后就不用sudo pacman了

除了yay之外还有另外一个现在很流行的aur助手叫做paru（rust编写）

```text
sudo pacman -S paru
```

paru相比于yay的优势在于可以用一行命令清除系统上所有不需要的包依赖项，此外在安装来在aur的包的时候会出现对应的PKGBUILD文件让你查看，具体用法可以在其github页面查看：

[Morganamilo/parugithub.com/Morganamilo/paru![img](https://pic2.zhimg.com/v2-d3f847ff0f7b6e8e179e67eb890e869d_180x120.jpg)](https://link.zhihu.com/?target=https%3A//github.com/Morganamilo/paru)

#### 安装拼音输入法

抛弃fcitx4，拥抱fcitx5吧，btw搜狗、百度、google输入法都是垃圾

安装fcitx5（输入法框架）

```text
yay -S fcitx5-im
```

配置fcitx5的环境变量：

```text
nano ~/.pam_environment
```

内容为：

```text
GTK_IM_MODULE DEFAULT=fcitx
QT_IM_MODULE  DEFAULT=fcitx
XMODIFIERS    DEFAULT=\@im=fcitx
SDL_IM_MODULE DEFAULT=fcitx
```

安装fcitx5-rime（输入法引擎）

```text
yay -S fcitx5-rime
```

安装rime-cloverpinyin（输入方案）

```text
yay -S rime-cloverpinyin
```

如果出现问题可能还需要做下面这步：

```text
yay -S base-devel
```

创建并写入rime-cloverpinyin的输入方案：

```text
nano ~/.local/share/fcitx5/rime/default.custom.yaml
```

内容为：

```text
patch:
  "menu/page_size": 5
  schema_list:
    - schema: clover
```

关于这个输入方案有什么问题，可以去github看对应的wiki

安装中文维基百科词库：

```text
yay -S fcitx5-pinyin-zhwiki-rime
```

#### 配置zsh

安装zsh4human（简称z4h），我用过最快也最好用的zsh框架

![img](https://pic4.zhimg.com/80/v2-f98b1b3eb1840f9224971041d740a703_720w.jpg)

首先安装nerd font，图中的字体是JetBrainsMono Nerd Font

```text
yay -S nerd-fonts-jetbrains-mono
```

安装完成之后修改konsole字体为JetBrainsMono Nerd Font，关闭终端重新启动

修改hosts来确保基本的github访问，如果还是不行那你可能需要先完成科学上网的设置：

```text
# GitHub Host Start

185.199.108.154              github.githubassets.com
140.82.113.21                central.github.com
185.199.108.133              desktop.githubusercontent.com
185.199.108.153              assets-cdn.github.com
185.199.108.133              camo.githubusercontent.com
185.199.108.133              github.map.fastly.net
199.232.69.194               github.global.ssl.fastly.net
140.82.112.3                 gist.github.com
185.199.108.153              github.io
140.82.114.3                 github.com
140.82.114.6                 api.github.com
185.199.108.133              raw.githubusercontent.com
185.199.108.133              user-images.githubusercontent.com
185.199.108.133              favicons.githubusercontent.com
185.199.108.133              avatars5.githubusercontent.com
185.199.108.133              avatars4.githubusercontent.com
185.199.108.133              avatars3.githubusercontent.com
185.199.108.133              avatars2.githubusercontent.com
185.199.108.133              avatars1.githubusercontent.com
185.199.108.133              avatars0.githubusercontent.com
185.199.108.133              avatars.githubusercontent.com
140.82.114.10                codeload.github.com
52.216.114.235               github-cloud.s3.amazonaws.com
52.216.107.20                github-com.s3.amazonaws.com
52.217.94.236                github-production-release-asset-2e65be.s3.amazonaws.com
52.217.133.241               github-production-user-asset-6210df.s3.amazonaws.com
52.216.244.140               github-production-repository-file-5c1aeb.s3.amazonaws.com
185.199.108.153              githubstatus.com
64.71.144.202                github.community
185.199.108.133              media.githubusercontent.com

# Please Star : https://github.com/ineo6/hosts
# Mirror Repo : https://gitee.com/ineo6/hosts
# Update at: 2021-12-06 14:14:14

# GitHub Host End
```

执行命令安装z4h：

```bash
if command -v curl >/dev/null 2>&1; then
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
else
  sh -c "$(wget -O- https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
fi
```

整个安装过程完全交互式，根据自己需要安装即可

安装完成之后打开~/.zshrc

```text
nano ~/.zshrc
```

找到下图所在的行：

![img](https://pic1.zhimg.com/80/v2-db11ef3ccb61cd06a76c307953782090_720w.jpg)

z4h支持安装来自ohmyzsh的插件，只需要按照图中3，4行的格式加上插件，重启终端即可生效

sudo的功能是在你输入的命令的开头添加sudo ，方法是双击Esc

extract能让你不用再去记不同文件的解压命令，解压时只需要extract +你要解压的文件名

![img](https://pic2.zhimg.com/80/v2-a3243776c3192b0f473fe9cecd055dd9_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-3acc04916ba22199f3128efe74d16917_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-4f35571cf59387036ed42d91a40044a2_720w.jpg)

图中logo-ls是带logo的ls，exa是带高亮的ls，需要的话直接yay安装即可

![img](https://pic1.zhimg.com/80/v2-20165d68818794bf25ebc09ef2f9cc48_720w.jpg)

保存退出之后，手动修改konsole的默认bash为zsh

##### 具体配置教程

````markdown
# oh-my-zsh配置全攻略

## 一、 安装zsh\z4h

### 1. zsh

​	(1). Debian系 ： sudo apt install zsh

​	(2). Arch系 ： yay -S zsh \ sudo pacman -S zsh



### 2.z4h

#### (1). 修改hosts来确保基本的github访问：

```text
# GitHub Host Start

185.199.108.154              github.githubassets.com
140.82.113.21                central.github.com
185.199.108.133              desktop.githubusercontent.com
185.199.108.153              assets-cdn.github.com
185.199.108.133              camo.githubusercontent.com
185.199.108.133              github.map.fastly.net
199.232.69.194               github.global.ssl.fastly.net
140.82.112.3                 gist.github.com
185.199.108.153              github.io
140.82.114.3                 github.com
140.82.114.6                 api.github.com
185.199.108.133              raw.githubusercontent.com
185.199.108.133              user-images.githubusercontent.com
185.199.108.133              favicons.githubusercontent.com
185.199.108.133              avatars5.githubusercontent.com
185.199.108.133              avatars4.githubusercontent.com
185.199.108.133              avatars3.githubusercontent.com
185.199.108.133              avatars2.githubusercontent.com
185.199.108.133              avatars1.githubusercontent.com
185.199.108.133              avatars0.githubusercontent.com
185.199.108.133              avatars.githubusercontent.com
140.82.114.10                codeload.github.com
52.216.114.235               github-cloud.s3.amazonaws.com
52.216.107.20                github-com.s3.amazonaws.com
52.217.94.236                github-production-release-asset-2e65be.s3.amazonaws.com
52.217.133.241               github-production-user-asset-6210df.s3.amazonaws.com
52.216.244.140               github-production-repository-file-5c1aeb.s3.amazonaws.com
185.199.108.153              githubstatus.com
64.71.144.202                github.community
185.199.108.133              media.githubusercontent.com

# Please Star : https://github.com/ineo6/hosts
# Mirror Repo : https://gitee.com/ineo6/hosts
# Update at: 2021-12-06 14:14:14

# GitHub Host End
```

 如果还是不行那你可能需要先完成科学上网的设置

#### (2). 执行命令安装z4h

```
if command -v curl >/dev/null 2>&1; then
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
else
  sh -c "$(wget -O- https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
fi
```

整个安装过程完全交互式，根据自己需要安装即可

#### (3). 设置成默认

```bash
chsh -s /usr/bin/zsh
```

## 二、 安装oh-my-zsh

<a src="https://ohmyz.sh/">官网</a>

<a src="https://github.com/ohmyzsh/ohmyzsh/">github</a>

#### 1. curl

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### 2. wget

```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

#### 3. git

```bash
git clone https://gitee.com/mirrors/oh-my-zsh ~/.oh-my-zsh
```

#### 4. Gitee ( 国内镜像 )

```bash
sh -c "$(wget -O- https://gitee.com/pocmon/mirrors/raw/master/tools/install.sh)"
```

## 三、 oh-my-zsh插件安装

#### 1.安装zsh-syntax-highlighting(zsh高亮显示):

```
git clone https://github.91chi.fun//https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
```

#### 2. 安装zsh-autosuggestions(zsh 自动补全):

```3bash
git clone https://github.91chi.fun//https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

#### 3. 安装h插件(zsh cmd检查):

```bash
git clone https://github.91chi.fun//https://github.com/YouDad/h.git ~/.oh-my-zsh/custom/plugins/h
```

## 四、 powerlevel10k主题

#### 1.Powerlevel10k 安装

- 手动安装

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

- 国内用户可以使用 [http://gitee.com](https://link.zhihu.com/?target=http%3A//gitee.com) 上的官方镜像加速下载.

```bash
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

#### 2. Powerlevel10k 配置

- 编辑 .zshrc 文件， 文件路径 ~/.zshrc

```rb
open ~/.zshrc

ZSH_THEME="powerlevel10k/powerlevel10k"
```

- ```
    source .zshrc
    ```

#### 3. 设置Nerd-Font字体	(否则图标会乱码)

##### (1). 首先下载文件

```ruby
wget -c https://github.com/ryanoasis/nerd-fonts/releases/download/v2.0.0/SourceCodePro.zip
```

##### (2). 然后解压到文件夹:

```bash
sudo unzip SourceCodePro -d /usr/share/fonts/SourceCodePro
```

##### (3). 转到/usr/share/fonts/SourceCodePro目录，并安装

```bash
cd /usr/share/fonts/SourceCodePro
sudo mkfontscale # 生成核心字体信息
sudo mkfontdir # 生成字体文件夹
sudo fc-cache -fv # 刷新系统字体缓存
```

#### 4. 配色方案

##### (1). 第一步

```
git clone git://github.com/seebi/dircolors-solarized.git
```

```
cp ~/dircolors-solarized/dircolors.256dark ~/.dircolors
```

```
eval 'dircolors .dircolors'
```

##### (2). 第二步 (先检查 echo $TERM)

```
vim .barshrc
```

```
export TERM=xterm-256color
```

##### (3). 第三步

```
git clone git://github.com/sigurdga/gnome-terminal-colors-solarized.git
```

```
cd gnome-terminal-colors-solarized
```

```
./set_dark.sh  or  ./set_light.sh
```
````



#### 配置Vim/Neovim

现代化的vim插件管理工具，开箱即用，不使用vim的可以略过

```text
git clone https://github.com/chxuan/vimplus.git ~/.vimplus
cd ~/.vimplus
./install.sh
```

[vimplusgithub.com/chxuan/vimplus](https://link.zhihu.com/?target=https%3A//github.com/chxuan/vimplus)

下图为安装效果：

![img](https://pic1.zhimg.com/80/v2-8b4c90ea381791405a5bdedbbb27bea0_720w.jpg)

同样想显示icon，需要将终端字体改为打了nerd补丁的字体

vim并不难，开个终端执行vimtutor命令，进去练30分钟就能掌握80%的有用操作

如果已经对vim有一定的使用经验，可以考虑使用neovim：

[https://github.com/ayamir/nvimdotsgithub.com/ayamir/nvimdots](https://link.zhihu.com/?target=https%3A//github.com/ayamir/nvimdots)

这是我个人的配置，已经完成了很多的定制化功能，而且相当快。

按照readme的提示装好依赖就成了一个全功能的现代化编辑器。

![img](https://pic2.zhimg.com/80/v2-83ebcf2192d225f77dd01bbad3517441_720w.jpg)

````markdown
## 一、基础配置

### 1. 缩进 & 制表符

使 `Vim` 在创建新行的时候使用与上一行同样的缩进：

```
set autoindent
```

创建新行时使用**智能缩进**，主要用于 `C` 语言一类的程序。通常，打开 `smartindent` 时也应该打开 `autoindent`：

```
set smartindent
```

注意：`Vim` 具有语言感知功能，且其默认设置可以基于文件中的编程语言来改变配置以提高效率。有许多默认的配置选项，包括 `axs cindent`，`cinoptions`，`indentexpr` 等，没有在这里说明。 `syn` 是一个非常有用的命令，用于设置文件的语法以更改显示模式。

（LCTT 译注：这里的 `syn` 是指 `syntax`，可用于设置文件所用的编程语言，开启对应的语法高亮，以及执行自动事件 (`autocmd`)。）

**设置文件里的制表符 `(TAB)` 的宽度**（以空格的数量表示）：

```
set tabstop=4
```

设置移位操作 `>>` 或 `<<` 的缩进长度（以空格的数量表示）：

```
set shiftwidth=4
```

如果你更喜欢在编辑文件时使用空格而不是制表符，设置以下选项可以使 `Vim` 在你按下 `Tab` 键时用空格代替制表符。

```
set expandtab
```

注意：这可能会导致依赖于制表符的 `Python` 等编程语言出现问题。这时，你可以根据文件类型设置该选项（请参考 `autocmd`）。

### 2. 显示 & 格式化

要在每行的前面**显示行号**：

```
set number
```



要在文本行超过一定长度时**自动换行**：

```
set textwidth=80
```

要根据从窗口右侧向左数的列数来**自动换行**：

```
set wrapmargin=2
```

（LCTT 译注：如果 `textwidth` 选项不等于零，本选项无效。)

当光标遍历文件时经过括号时，**高亮标识匹配的括号**：

```
set showmatch
```



### 3. 搜索

高亮搜索内容的**所有匹配位置**：

```
set hlsearch
```



搜索过程中**动态显示匹配内容**：

```
set incsearch
```



搜索时**忽略大小写**：

```
set ignorecase
```

在打开 `ignorecase` 选项的条件下，搜索内容包含部分大写字符时，要使搜索大小写敏感：

```
set smartcase
```

例如，如果文件内容是：

```
testTest
```

当打开 `ignorecase` 和 `smartcase` 选项时，搜索 `test` 时的突出显示：

> test
> Test

搜索 `Test` 时的突出显示：

> test
> Test

### 4. 浏览 & 滚动

为获得更好的视觉体验，你可能希望将光标放在窗口中间而不是第一行，以下选项使光标距窗口上下保留 5 行。

```
set scrolloff=5
```

在 `Vim` 窗口底部显示一个永久状态栏，可以显示文件名、行号和列号等内容：

```
set laststatus=2
```



### 5. 拼写

`Vim` 有一个内置的拼写检查器，对于文本编辑和编码非常有用。`Vim` 可以识别文件类型并仅对代码中的注释进行拼写检查。使用下面的选项打开**英语拼写检查**：

```
set spell spelllang=en_us
```

（LCTT 译注：中文、日文或其它东亚语字符通常会在打开拼写检查时被标为拼写错误，因为拼写检查不支持这些语种，可以在 `spelllang` 选项中加入 `cjk` 来忽略这些错误标注。）

### 6. 其他选项

禁止创建备份文件：启用此选项后，`Vim` 将在覆盖文件前创建一个备份，文件成功写入后保留该备份。如果不想保留该备份文件，可以按下面的方式关闭：

```
set nobackup
```

禁止创建交换文件：启用此选项后，`Vim` 将在编辑该文件时创建一个交换文件。 交换文件用于在崩溃或发生使用冲突时恢复文件。交换文件是以 `.` 开头并以 `.swp` 结尾的隐藏文件。

```
set noswapfile
```

如果需要在同一个 `Vim` 窗口中编辑多个文件并进行切换。默认情况下，工作目录是打开的第一个文件的目录。而将工作目录自动切换到正在编辑的文件的目录是非常有用的。要自动切换工作目录：

```
set autochdir
```

`Vim` 自动维护编辑的历史记录，允许撤消更改。默认情况下，该历史记录仅在文件关闭之前有效。`Vim` 包含一个增强功能，使得即使在文件关闭后也可以维护撤消历史记录，这意味着即使在保存、关闭和重新打开文件后，也可以撤消之前的更改。历史记录文件是使用 `.un~` 扩展名保存的隐藏文件。

```
set undofile
```

错误信息响铃，只对错误信息起作用：

```
set errorbells
```

如果你愿意，还可以设置错误视觉提示：

```
set visualbell
```

### 惊喜

`Vim` 提供长格式和短格式命令，两种格式都可用于设置或取消选项配置。

`autoindent` 选项的长格式是：

```
set autoindent
```

`autoindent` 选项的短格式是：

```
set ai
```

要在不更改选项当前值的情况下查看其当前设置，可以在 `Vim` 的命令行上使用在末尾加上 `?` 的命令：

```
set autoindent?
```

在大多数选项前加上 `no` 前缀可以取消或关闭选项：

```
set noautoindent
```

可以为单独的文件配置选项，而不必修改全局配置文件。需要的话，请打开文件并输入 `:`，然后键入 `set`命令。这样的话，配置仅对当前的文件编辑会话有效。

使用命令行获取帮助：

```
:help autoindent
```

```
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"__     ___                   _             
"\ \   / (_)_ __ ___    _ __ | |_   _  __ _ 
" \ \ / /| | '_ ` _ \  | '_ \| | | | |/ _` |
"  \ V / | | | | | | |_| |_) | | |_| | (_| |
"   \_/  |_|_| |_| |_(_) .__/|_|\__,_|\__, |
"                      |_|            |___/ 
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
imap jk <Esc>
nmap <space> :
normap Q :wq<CR>
colorscheme space_vim_theme
" 深色背景
set bg=dark
" 启用 one 配色
"colorscheme one
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" 显示相关
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"set shortmess=atI   " 启动的时候不显示那个援助乌干达儿童的提示
"winpos 5 5          " 设定窗口位置
"set lines=40 columns=155    " 设定窗口大小
"set nu              " 显示行号
set go=             " 不要图形按钮
color asmanian2     " 设置背景主题
set guifont=Courier_New:h10:cANSI   " 设置字体
syntax on           " 语法高亮
autocmd InsertLeave * se nocul  " 用浅色高亮当前行
autocmd InsertEnter * se cul    " 用浅色高亮当前行
"set ruler           " 显示标尺
set showcmd         " 输入的命令显示出来，看的清楚些
"set cmdheight=1     " 命令行（在状态行下）的高度，设置为1
"set whichwrap+=<,>,h,l   " 允许backspace和光标键跨越行边界(不建议)
"set scrolloff=3     " 光标移动到buffer的顶部和底部时保持3行距离
set novisualbell    " 不要闪烁(不明白)
set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}   "状态行显示的内容
set laststatus=1    " 启动显示状态行(1),总是显示状态行(2)
set foldenable      " 允许折叠
set foldmethod=manual   " 手动折叠
"set background=dark "背景使用黑色
set nocompatible  "去掉讨厌的有关vi一致性模式，避免以前版本的一些bug和局限
" 显示中文帮助
if version >= 603
    set helplang=cn
    set encoding=utf-8
endif
" 设置配色方案
"colorscheme NeoSolarized
"字体
if (has("gui_running"))
   set guifont=Bitstream\ Vera\ Sans\ Mono\ 10
endif
set fencs=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936
set termencoding=utf-8
set encoding=utf-8
set fileencodings=ucs-bom,utf-8,cp936
set fileencoding=utf-8
"""""""""""""""""""""""""""""""""""""""""""""""""""""""
""新文件标题
"""""""""""""""""""""""""""""""""""""""""""""""""""""""
"新建.c,.h,.sh,.java文件，自动插入文件头
autocmd BufNewFile *.cpp,*.[ch],*.sh,*.java exec ":call SetTitle()"
""定义函数SetTitle，自动插入文件头
func SetTitle()
    "如果文件类型为.sh文件
    if &filetype == 'sh'
        call setline(1,"\#########################################################################")
        call append(line("."), "\# File Name: ".expand("%"))
        call append(line(".")+1, "\# Author: ma6174")
        call append(line(".")+2, "\# mail: ma6174@163.com")
        call append(line(".")+3, "\# Created Time: ".strftime("%c"))
        call append(line(".")+4, "\#########################################################################")
        call append(line(".")+5, "\#!/bin/bash")
        call append(line(".")+6, "")
    else
        call setline(1, "/*************************************************************************")
        call append(line("."), "    > File Name: ".expand("%"))
        call append(line(".")+1, "    > Author: ma6174")
        call append(line(".")+2, "    > Mail: ma6174@163.com ")
        call append(line(".")+3, "    > Created Time: ".strftime("%c"))
        call append(line(".")+4, " ************************************************************************/")
        call append(line(".")+5, "")
    endif
    if &filetype == 'cpp'
        call append(line(".")+6, "#include<iostream>")
        call append(line(".")+7, "using namespace std;")
        call append(line(".")+8, "")
    endif
    if &filetype == 'c'
        call append(line(".")+6, "#include<stdio.h>")
        call append(line(".")+7, "")
    endif
    "新建文件后，自动定位到文件末尾
    autocmd BufNewFile * normal G
endfunc
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"键盘命令
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
nmap <leader>w :w!<cr>
nmap <leader>f :find<cr>
" 映射全选+复制 ctrl+a
map <C-A> ggVGY
map! <C-A> <Esc>ggVGY
map <F12> gg=G
" 选中状态下 Ctrl+c 复制
vmap <C-c> "+y
"去空行
nnoremap <F2> :g/^\s*$/d<CR>
"比较文件
nnoremap <C-F2> :vert diffsplit
"新建标签
map <M-F2> :tabnew<CR>
"列出当前目录文件
map <F3> :tabnew .<CR>
"打开树状文件目录
map <C-F3> \be
"C，C++ 按F5编译运行
map <F5> :call CompileRunGcc()<CR>
func! CompileRunGcc()
    exec "w"
    if &filetype == 'c'
        exec "!g++ % -o %<"
        exec "! ./%<"
    elseif &filetype == 'cpp'
        exec "!g++ % -o %<"
        exec "! ./%<"
    elseif &filetype == 'java'
        exec "!javac %"
        exec "!java %<"
    elseif &filetype == 'sh'
        :!./%
    endif
endfunc
"C,C++的调试
map <F8> :call Rungdb()<CR>
func! Rungdb()
    exec "w"
    exec "!g++ % -g -o %<"
    exec "!gdb ./%<"
endfunc
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""
""实用设置
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""

" quickfix模式
autocmd FileType c,cpp map <buffer> <leader><space> :w<cr>:make<cr>
"代码补全
set completeopt=preview,menu
"允许插件
filetype plugin on
"共享剪贴板
set clipboard+=unnamed
"从不备份
set nobackup
"make 运行
:set makeprg=g++\ -Wall\ \ %
"自动保存
set autowrite
set ruler                   " 打开状态栏标尺
set cursorline              " 突出显示当前行
set magic                   " 设置魔术
set guioptions-=T           " 隐藏工具栏
set guioptions-=m           " 隐藏菜单栏
"set statusline=\ %<%F[%1*%M%*%n%R%H]%=\ %y\ %0(%{&fileformat}\ %{&encoding}\ %c:%l/%L%)\
" 设置在状态行显示的信息
set foldcolumn=0
set foldmethod=indent
set foldlevel=3
set foldenable              " 开始折叠
" 不要使用vi的键盘模式，而是vim自己的
set nocompatible
" 语法高亮
set syntax=on
" 去掉输入错误的提示声音
set
" 在处理未保存或只读文件的时候，弹出确认
set confirm
" 自动缩进
set autoindent
set cindent
" Tab键的宽度
set tabstop=4
" 统一缩进为4
set softtabstop=4
set shiftwidth=4
" 不要用空格代替制表符
set noexpandtab
" 在行和段开始处使用制表符
set smarttab
" 显示行号
set number
" 历史记录数
set history=1000
"禁止生成临时文件
set nobackup
set noswapfile
"搜索忽略大小写
set ignorecase
"搜索逐字符高亮
set hlsearch
set incsearch
"行内替换
set gdefault
"编码设置
set enc=utf-8
set fencs=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936
"语言设置
set langmenu=zh_CN.UTF-8
set helplang=cn
" 我的状态行显示的内容（包括文件类型和解码）
"set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}
"set statusline=[%F]%y%r%m%*%=[Line:%l/%L,Column:%c][%p%%]
" 总是显示状态行
set laststatus=2
" 命令行（在状态行下）的高度，默认为1，这里是2
set cmdheight=2
" 侦测文件类型
filetype on
" 载入文件类型插件
filetype plugin on
" 为特定文件类型载入相关缩进文件
filetype indent on
" 保存全局变量
set viminfo+=!
" 带有如下符号的单词不要被换行分割
set iskeyword+=_,$,@,%,#,-
" 字符间插入的像素行数目
set linespace=0
" 增强模式中的命令行自动完成操作
set wildmenu
" 使回格键（backspace）正常处理indent, eol, start等
set backspace=2
" 允许backspace和光标键跨越行边界
set whichwrap+=<,>,h,l
" 可以在buffer的任何地方使用鼠标（类似office中在工作区双击鼠标定位）
set mouse=a
set selection=exclusive
set selectmode=mouse,key
" 通过使用: commands命令，告诉我们文件的哪一行被改变过
set report=0
" 在被分割的窗口间显示空白，便于阅读
set fillchars=vert:\ ,stl:\ ,stlnc:\
" 高亮显示匹配的括号
set showmatch
" 匹配括号高亮的时间（单位是十分之一秒）
set matchtime=1
" 光标移动到buffer的顶部和底部时保持3行距离
set scrolloff=3
" 为C程序提供自动缩进
set smartindent
" 高亮显示普通txt文件（需要txt.vim脚本）
au BufRead,BufNewFile *  setfiletype txt
"自动补全
:inoremap ( ()<ESC>i
:inoremap ) <c-r>=ClosePair(')')<CR>
:inoremap { {<CR>}<ESC>O
:inoremap } <c-r>=ClosePair('}')<CR>
:inoremap [ []<ESC>i
:inoremap ] <c-r>=ClosePair(']')<CR>
:inoremap " ""<ESC>i
:inoremap ' ''<ESC>i
function! ClosePair(char)
    if getline('.')[col('.') - 1] == a:char
        return "\<Right>"
    else
        return a:char
    endif
endfunction
filetype plugin indent on
"打开文件类型检测, 加了这句才可以用智能补全
set completeopt=longest,menu
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" CTags的设定
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
let Tlist_Sort_Type = "name"    " 按照名称排序
let Tlist_Use_Right_Window = 1  " 在右侧显示窗口
let Tlist_Compart_Format = 1    " 压缩方式
let Tlist_Exist_OnlyWindow = 1  " 如果只有一个buffer，kill窗口也kill掉buffer
let Tlist_File_Fold_Auto_Close = 0  " 不要关闭其他文件的tags
let Tlist_Enable_Fold_Column = 0    " 不要显示折叠树
autocmd FileType java set tags+=D:\tools\java\tags
"autocmd FileType h,cpp,cc,c set tags+=D:\tools\cpp\tags
"let Tlist_Show_One_File=1            "不同时显示多个文件的tag，只显示当前文件的
"设置tags
set tags=tags
"set autochdir
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"其他东东
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"默认打开Taglist
let Tlist_Auto_Open=1
""""""""""""""""""""""""""""""
" Tag list (ctags)
""""""""""""""""""""""""""""""""
let Tlist_Ctags_Cmd = '/usr/bin/ctags'
let Tlist_Show_One_File = 1 "不同时显示多个文件的tag，只显示当前文件的
let Tlist_Exit_OnlyWindow = 1 "如果taglist窗口是最后一个窗口，则退出vim
let Tlist_Use_Right_Window = 1 "在右侧窗口中显示taglist窗口
" minibufexpl插件的一般设置
let g:miniBufExplMapWindowNavVim = 1
let g:miniBufExplMapWindowNavArrows = 1
let g:miniBufExplMapCTabSwitchBufs = 1
let g:miniBufExplModSelTarget = 1
```

## 二、插件配置

### 1. Nerd tree  -->  Vim树浏览器插件

<a serf="https://vimawesome.com/plugin/nerdtree-red">Vim Awesome主页</a>

修改**按键映射**：

```
map <silent> <C-e> :NERDTreeTpggle<CR>
```

<C-e> 可以替换为你舒适的位置按键

### 2. Vim-Airline --> 状态栏强化

https://vimawesome.com/plugin/vim-airline-superman

#### 1. 安装airline

手动安装：`github`地址：https://github.com/vim-airline/vim-airline.git
使用`Vundle`安装：在`vimrc`配置的`Vundle`插件列表加入 `Plugin ‘bling/vim-airline’` 并在Vim 执行` PluginInstall`。

#### 2. 配置字体

##### 1.安装字体

##### **Linux**: 下载 `powerline fonts`,并按指示安装。

**windows**:下载里面的四种字体并安装` powerline fonts`

> 1. Linux: 下载 [powerline fonts](javascript:void()),并按指示安装。
> 2. windows:下载里面的四种字体并安装[ powerline fonts](javascript:void())

##### 2 .注意

```
安装过字体需要用vim 打开 ./vim-airline/doc/airline.txt 目录中的airline.txt 找到下面的一些语句 将其复制到.vimrc中就可以了
例如 let g:airline_left_sep = ‘’ 这里’ ‘’ 在这里显示不出来 如果正确安装了补丁字体会是实心的箭头符号 有一个比较大的实心箭头 和一个比较小的实心箭头 选大的所在的那条语句复制到.vimrc中就可以正确契合的显示箭头符号了
```

如果仍然无法正常显示，可能是编码设置的问题，我的编码设置为：

```
"----------------------------------------------------------------
"编码设置
"----------------------------------------------------------------
"Vim 在与屏幕/键盘交互时使用的编码(取决于实际的终端的设定)        
set encoding=utf-8
set langmenu=zh_CN.UTF-8
" 设置打开文件的编码格式  
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1 
set fileencoding=utf-8
"解决菜单乱码
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim
"解决consle输出乱码
set termencoding = cp936  
"设置中文提示
language messages zh_CN.utf-8 
"设置中文帮助
set helplang=cn
"设置为双字宽显示，否则无法完整显示如:☆
set ambiwidth=double
```

#### 3. 配置Vim-airline

以下是我的配置：

```
"--------------------------------------------------------------------------
"vim-airline
"--------------------------------------------------------------------------
Plugin 'vim-airline'    
let g:airline_theme="molokai" 

"这个是安装字体后 必须设置此项" 
let g:airline_powerline_fonts = 1   

 "打开tabline功能,方便查看Buffer和切换,省去了minibufexpl插件
 let g:airline#extensions#tabline#enabled = 1
 let g:airline#extensions#tabline#buffer_nr_show = 1

"设置切换Buffer快捷键"
 nnoremap <C-tab> :bn<CR>
 nnoremap <C-s-tab> :bp<CR>
 " 关闭状态显示空白符号计数
 let g:airline#extensions#whitespace#enabled = 0
 let g:airline#extensions#whitespace#symbol = '!'
 " 设置consolas字体"前面已经设置过
 "set guifont=Consolas\ for\ Powerline\ FixedD:h11
  if !exists('g:airline_symbols')
    let g:airline_symbols = {}
  endif
  " old vim-powerline symbols
  let g:airline_left_sep = '⮀'
  let g:airline_left_alt_sep = '⮁'
  let g:airline_right_sep = '⮂'
  let g:airline_right_alt_sep = '⮃'
  let g:airline_symbols.branch = '⭠'
  let g:airline_symbols.readonly = '⭤'
```

#### 4. 底部状态栏不显示图标

##### 1. 正常的：

![在这里插入图片描述](https://img-blog.csdnimg.cn/adfce3dbc1fb4873a2b73e34b6eb8bbd.png)

##### 2. 不正常的：

![在这里插入图片描述](https://img-blog.csdnimg.cn/adfce3dbc1fb4873a2b73e34b6eb8bbd.png)

##### 3. 解决办法

把Plug 'ryanoasis/vim-devicons'插件放到airline下面引用就可以了。

```
" 飞机线美化
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
" 这个图标插件要放在靠后的位置才可以正常显示
Plug 'ryanoasis/vim-devicons'

```

#### 5. theme

```
bubblegum . Molokai
```

### 3. rainbow --> 彩虹括号

https://vimawesome.com/plugin/rainbow-you-belong-with-me

彩虹插件配置生效 

```vim
let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle
```

### 4. emmet --> HTML、CSS代码补全

<a herf="https://vimawesome.com/plugin/emmet-vim">Vim Awesome主页</a>

```
let **g:user_emmet_mode**='n'    "only enable normal mode functions.             

let **g:user_emmet_mode**='inv'  "enable all functions, which is equal to        

let **g:user_emmet_mode**='a'    "enable all function in all mode.               

let **g:user_emmet_leader_key**=''  
```

```
let g:user_emmet_install_global = 0
autocmd FileType html,css EmmetInstall

To remap the default `<C-Y>` leader:
let g:user_emmet_leader_key='<C-Z>'
```

### 5. Tagbar --> 标签导航

https://vimawesome.com/plugin/tagbar

```vim
nmap <F8> :TagbarToggle<CR>
```

### 6. Startify --> 启动标签页

<a herf="https://vimawesome.com/plugin/vim-startify">Vim Awesome</a>

### 7. indentLine --> 可视化缩进

https://vimawesome.com/plugin/indentline

### 8. `Coc.nvim` --> 自动补全

<a herf="https://vimawesome.com/plugin/coc-nvim">Vim Awesome</a>

<a src="https://blog.csdn.net/weixin_44286745/article/details/113729094">教程</a>

<a src="https://www.cnblogs.com/pulsgarney/p/coc-nvim-guideline.html">教程2</a>

#### 一、 安装node.js

需要`nodejs`>=12.12

##### 1. Debian系  安装

###### (1). apt-get安装（不推荐）

```
sudo apt-get install nodejs
sudo apt-get install npm
```

安装的node版本太老旧

###### (2). 源代码安装

```
$ sudo git clone https://github.com/nodejs/node.git
Cloning into 'node'...
```

修改目录权限：

```
$ sudo chmod -R 755 node
```

使用 **./configure** 创建编译文件，并按照：

```
$ cd node
$ sudo ./configure
$ sudo make
$ sudo make install
```

查看 node 版本：

```
$ node --version
v0.10.25
```

###### (3). 代码安装（尝试）

```bash
curl -sL install-node.now.sh/lts | bash
```

##### 2. CentOS 下源码安装 Node.js

1、下载源码，你需要在<a src="https://nodejs.org/en/download/">官网</a>下载最新的Nodejs版本，本文以v0.10.24为例:

```
cd /usr/local/src/
wget http://nodejs.org/dist/v0.10.24/node-v0.10.24.tar.gz
```

2、解压源码

```
tar zxvf node-v0.10.24.tar.gz
```

3、 编译安装

```
cd node-v0.10.24
./configure --prefix=/usr/local/node/0.10.24
make
make install
```

4、 配置NODE_HOME，进入profile编辑环境变量

```
vim /etc/profile
```

设置 nodejs 环境变量，在 ***export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL*** 一行的上面添加如下内容:

```
#set for nodejs
export NODE_HOME=/usr/local/node/0.10.24
export PATH=$NODE_HOME/bin:$PATH
```

:wq保存并退出，编译/etc/profile 使配置生效

```
source /etc/profile
```

验证是否安装配置成功

```
node -v
```

输出 v0.10.24 表示配置成功

npm模块安装路径

```
/usr/local/node/0.10.24/lib/node_modules/
```

**注：**Nodejs 官网提供了编译好的 Linux 二进制包，你也可以下载下来直接应用。

##### 3. 其他方法

```
# wget https://nodejs.org/dist/v10.9.0/node-v10.9.0-linux-x64.tar.xz    // 下载
# tar xf  node-v10.9.0-linux-x64.tar.xz       // 解压
# cd node-v10.9.0-linux-x64/                  // 进入解压目录
# ./bin/node -v                               // 执行node命令 查看版本
v16.9.0
```

#### 二、 安装Coc.nvim

https://github.com/neoclide/coc.nvim

##### 1.测试是否安装成功

```
在`vim`命令行中输入`:CocInfo`，若有类似以下信息弹出表示插件安装成功
```

##### 2.  build/index.js not found

```
sudo apt install nodejs 

sudo npm install -g yarn 

~/.vim/plugged/coc.nvim/  //是我的coc.nvim插件的安装目录 

cd ~/.vim/plugged/coc.nvim/	 

yarn install yarn build
```



#### 三、 配置Coc.nvim

https://github.com/neoclide/coc.nvim/wiki/Language-servers

##### 1. 配置python补全 

-    安装python3  

```shell
apt install python
```

-    安装pip3  

```shell
apt install python3-pip
```

-    安装语言服务器  

```shell
pip install jedi
```

-    安装补全插件  

```vim
:CocInstall coc-python
```

##### 2. 配置javaScript/typescript补全 

-    安装语言服务器  

```shell
npm install typescript
```

-    安装补全插件  

```vim
:CocInstall coc-tsserver
```

#####  3. 配置golang补全 

-    安装golang  

```shell
apt install golang
```

-    安装插件  

```vim
:CocInstall coc-go
```

#####   4. 配置Java补全 

-    安装jdk或openjdk，注意   `    jdk版本   `   >=   `    1.8   `  
-    安装插件  

```vim
:CocInstall coc-java
```

-    进入一个Java文件，下面会有下载jdt的提示，等待下载完成即可使用  

#####   5. C家族 

  由于我并不写C语言，也没安装过C的补全，所以请自行观看官方文档安装，可能会比我上面讲的这些插件难装一些。  
  官方文档地址：  [    coc.nvim-clang   ](https://github.com/clangd/coc-clangd) 

#####   6. 其他的一些补全 

-    html补全  

```vim
:CocInstall coc-html
```

-    css补全  

```vim
:CocInstall coc-css
```

-    文档高亮  

```vim
:CocInstall coc-highlight
```

-    json补全  

```vim
:CocInstall coc-json
```

-    代码片段列表  

```vim
:CocInstall coc-snippets
```

-    基本列表  

```vim
:CocInstall coc-lists
```

-    markdown补全  

```vim
:CocInstall coc-markdownlint
```

-    自动补全括号  

```vim
:CocInstall coc-pairs
```

###  一次性安装上面这些插件 

  大家可以选择自己需要的插件进行安装，没必要全部安装 

  在vim中输入： 

```vim
:CocInstall coc-markdownlint coc-snippets coc-json coc-highlight coc-css coc-html coc-java coc-python coc-tsserver coc-pairs coc-lists coc-go
```

#### 四、 添加插件

因为Coc 本身并不提供具体语言的补全功能，更多的只是提供了一个补全功能的平台，
 所以在安装完成后，我们需要安装具体的语言服务以支持对应的补全功能。
 打开Vim 并使用以下命令即可自动安装子插件及相关依赖。

```ruby
:CocInstall coc-json coc-tsserver
```

其中`coc-json coc-tsserver` 这些是对应的支持JSON, Typescript 的相关子插件。
 要检索都有哪些子插件可以直接[在Npm 上查找coc.nvim](https://www.npmjs.com/search?q=keywords%3Acoc.nvim),
 亦或者使用[coc-marketplace](https://github.com/fannheyward/coc-marketplace) 直接在Vim 里面进行管理，安装命令如下：

```ruby
:CocInstall coc-marketplace
```

安装完后用下面命令可以打开面板，`Tab` 可对高亮的子插件进行安装卸载等操作。

```ruby
# 打开面板
:CocList marketplace

# 搜索python 相关子插件
:CocList marketplace python
```

#### 五、 总结

- 安装命令`:CocInstall 插件名`
- 移除命令`:CocUninstall 插件名`
- 查看已安装`:CocList extensions`
- 更新命令`:CocUpdate

### 9. lederF/FZF --> 模糊搜索

./install即可，无需其他配置（leaderf需要pyhon3.1+）

### 10. markdown/image

#### (1). markdown

##### I. 首先安装插件

```
Plugin 'godlygeek/tabular'

Plugin 'plasticboy/vim-markdown'
```

##### II. 配置插件

```
 
let g:vim_markdown_folding_disabled = 1 #不折叠显示，默认是折叠显示，看个人习惯

let g:vim_markdown_override_foldtext = 0

let g:vim_markdown_folding_level = 6 #可折叠的级数，对应md的标题级别

let g:vim_markdown_no_default_key_mappings = 1

let g:vim_markdown_emphasis_multiline = 0
```

#### (2). image

### 11. ale

### 12. ranger

### 13. 主题

#### 1.`Monokai`

#### 2.`vim-one`

#### 3.`onedark.vim` / `onedark.nvim`

可以根据自己喜好配置

````

####  安装腾讯系软件

5.4.1 安装Tim

```text
yay -S deepin-wine-tim
```

安装过程中出现选择输入n就好

切换deepin-wine环境

```text
sh /opt/deepinwine/apps/Deepin-Tim/run.sh -d
```

https://link.zhihu.com/?target=https%3A//github.com/countstarlight/deepin-wine-tim-arch/issues)

开issue反馈就好了

如果这个版本的卡或者有其他问题，建议安装：

```text
yay -S deepin.com.qq.office
```

如果这个也没办法装，则使用linuxqq

```text
yay -S linuxqq
```

还有个不是很成熟的解决方案：

[Clansty/icalinguagithub.com/Clansty/icalingua](https://link.zhihu.com/?target=https%3A//github.com/Clansty/icalingua)

```text
yay -S icalingua
```

5.4.2 安装微信

deepin-wine版：

```text
yay -S deepin-wine-wechat
```

切换到deepin-wine环境：

```text
/opt/apps/com.qq.weixin.deepin/files/run.sh -d
```

关于字体发虚问题：

在切换到deepin-wine环境后，在terminal输入下面的指令：

```bash
env WINEPREFIX="$HOME/.deepinwine/Deepin-TIM" /usr/bin/deepin-wine winecfg
```

在弹出的窗口中选择windows xp，将DPI调大（默认是96），我调成了120

微信的同样，只需要将命令改为：

```bash
env WINEPREFIX="$HOME/.deepinwine/Deepin-WeChat" /usr/bin/deepin-wine winecfg
```

electron版：

```text
yay -S electron-wechat
```

####  安装其他软件

tldr(Too Long; Don't Read)：帮你更加高效地学习linux命令

```text
pip install --user tldr
```

![img](https://pic1.zhimg.com/80/v2-80bfb8a7b747b19088f4b24f061ab3a0_720w.jpg)

如果下载太慢：

```text
mkdir -p ~/.config/pip
nano ~/.config/pip/pip.conf
```

写入如下内容

```text
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

这样就永久地修改了用户级别的pip下载源

如果安装失败则用：

```text
yay -S tldr
```

GNU/Linux基本命令的rust替代（更好用）：

ranger：终端文件浏览器

```text
yay -S ranger
```

wps：中文版，想要英文版把后面那个包去掉

```text
yay -S wps-office wps-office-mui-zh-cn
```

网易云音乐：

```text
yay -S netease-cloud-music
```

高颜值、开发活跃的第三方客户端：

[YesPlayMusicgithub.com/qier222/YesPlayMusic](https://link.zhihu.com/?target=https%3A//github.com/qier222/YesPlayMusic)

```text
yay -S yesplaymusic
```

一个基本上所有功能都不缺的electron客户端：

[Rocket1184/electron-netease-cloud-musicgithub.com/Rocket1184/electron-netease-cloud-music](https://link.zhihu.com/?target=https%3A//github.com/Rocket1184/electron-netease-cloud-music)

```text
yay -S electron-netease-cloud-music
```

qq音乐：

```text
yay -S qqmusic-bin
```

一个支持全平台听歌的软件：FeelUown

```text
yay -S feeluown
```

根据装完之后的提示装对应平台的依赖

chrome

```text
yay -S google-chrome
```

百度网盘：

```text
yay -S baidunetdisk
```

或者命令行（CLI）的：

```text
yay -S baidupcs-go
```

坚果云：

```text
yay -S nutstore
```

为知笔记：全平台通用、有云端同步、支持md的笔记

```text
yay -S wiznote
```

如果你更喜欢开源软件，这里还有个很好的选择：joplin

```text
yay -S joplin
```

还有个选择：notion

```text
yay -S notion-app
```

思源笔记：所见即所得的笔记管理方案，类似于obsidian

```text
yay -S siyuan-note-bin
```

marktext：typora的开源替代品

```text
yay -S marktext
```

flameshot：最强大的截图工具 当你的tim/微信截图不好用的时候，用这个

```text
yay -S flameshot
```

timeshift：强大好用的备份、回滚系统工具

```text
yay -S timeshift
```

设置快捷键启动的方式：

设置 -> 快捷键 -> 自定义快捷键 -> 编辑 -> 新建 -> 全局快捷键 -> 命令/URL

设置触发器：设置为你习惯的快捷键 -> 动作：命令/URL这填：/usr/bin/flameshot gui

XDM：Linux下最快的下载神器

```text
yay -S xdman
```

建议直接去[官网](https://link.zhihu.com/?target=http%3A//xdman.sourceforge.net/%23downloads)下载压缩包安装，比命令行快很多

calibre：电子书管理神器

```text
yay -S calibre
```

系统托盘那开启夜间颜色控制，不需要安装redshift了

### 科学上网

```text
yay -S clash clash-for-windows-bin
```

[ACL4SSR 在线订阅转换acl4ssr-sub.github.io/](https://link.zhihu.com/?target=https%3A//acl4ssr-sub.github.io/)

### 字体

1.使用Windows/Mac OS字体

https://link.zhihu.com/?target=https%3A//wiki.archlinux.org/index.php/Fonts_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)%23Microsoft_%E5%AD%97%E4%BD%93

这里建议直接拷贝字体文件而非链接

2.渲染效果优化

https://zhuanlan.zhihu.com/p/343934880

### 必须要有的习惯：

1.必须要做timeshift，以防你哪天玩坏只能重装

2.每天要开机执行一遍

```text
sudo pacman -Syu
```

[archlinux wiki](https://link.zhihu.com/?target=https%3A//wiki.archlinux.org/title/Main_page_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

[List of applications](https://link.zhihu.com/?target=https%3A//wiki.archlinux.org/title/List_of_applications_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

### 清理缓存

```text
pacman -Scc
```