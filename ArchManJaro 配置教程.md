# Arch/ManJaro 配置教程（安装图形界面之后）

## 1. 换源

- **Arch**

  ```sh
  reflector --country China --age 24 --protocaol https --sort rate --save /etc/pacman.d/mirrorlist
  
  sudo pacman -Syy
  ```

`reflector` 需要使用先添加archlinuxcn源， 请先执行第二步

`--country` 软件源的所在地

`--age` 在多长时间之内更新过 (h)

`--protocol` 以哪种形式开始

`--sort` 镜像源排列方式

`--save` 保存到哪里

- **manjaro**

```sh
sudo pacman-mirrors -i -c China -m rank
```

## 2.添加`archlinuxcn`源

### 2.1 打开`/etc/pacman.conf` 文件

```sh
vim /etc/pacman.conf
```

### 2.2 编写`/etc/pacman.conf` 文件

```sh
# 清华大学源
[archlinuxcn] 
#SigLevel = Optional TrustedOnly 
SigLevel = Optional TrustAll
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

### 2.3 包导入GPG key

```sh
sudo pacman -S archlinuxcn-keyring
```

### 2.4 更新一下

```sh
sudo pacman -Syy
```

## 3. 安装软件

### 3.1 yay

*Arch软件仓库（AUR）*，安装软件爽的yp，仓库又全又新，基本上遇不到依赖问题需要手动去搜该怎么安装，这也是我不愿意换回`Ubuntu`的一个重要原因

**yay**是一个用Go语言写的一个AUR助手，有些时候官方仓库没有你想要的软件，就需要通过**yay**来安装有了**yay**，以后就不用`sudo pacman`了

```sh
sudo pacman -S yay
```

### 3.2 paru

除了yay之外还有另外一个现在很流行的aur助手叫做paru（rust编写）

```sh
sudo pacman -S paru
```

**paru**相比于yay的优势在于可以用一行命令清除系统上所有不需要的包依赖项，此外在安装来在aur的包的时候会出现对应的PKGBUILD文件让你查看，具体用法可以在其github页面查看：

[ Morganamilo/paru ](https://github.com/Morganamilo/paru)

### 3.3 安装**fcitx5**输入法

#### 3.3.1 安装fcitx5（输入法框架）

```text
yay -S fcitx5-im
```

#### 3.3.2 配置fcitx5的环境变量

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

#### 3.3.3 安装fcitx5-rime（输入法引擎）

```text
yay -S fcitx5-rime
```

#### 3.3.4 安装rime-cloverpinyin（输入方案）

```text
yay -S rime-cloverpinyin
```

如果出现问题可能还需要做下面这步：

```text
yay -S base-devel
```

#### 3.3.5 创建并写入rime的输入方案

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

#### 3.3.6 安装中文维基百科词库

```text
yay -S fcitx5-pinyin-zhwiki-rime
```

#### 3.3.7（可选）配置主题

```text
yay -S fcitx5-material-color
```

### 3.4 安装/配置 zsh

[我的配置文件](https://hub.xn--p8jhe.tw/Johnson170/zsh)

#### 3.4.1 安装*zsh*

修改hosts来确保基本的github访问：

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

```sh
# zsh 与 z4h 选一个
# zsh
paru -S zsh 

# z4h
if command -v curl >/dev/null 2>&1; then
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
else
  sh -c "$(wget -O- https://raw.githubusercontent.com/romkatv/zsh4humans/v5/install)"
fi

chsh -s /usr/bin/zsh #设置为默认
```

#### 3.4.2 安装*oh-my-zsh*

##### 3.4.2.1 curl

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

##### 3.4.2.2 wget

```
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

##### 3.4.2.3 git

```bash
git clone https://gitee.com/mirrors/oh-my-zsh ~/.oh-my-zsh

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

chsh -s /bin/zsh
```

##### 3.4.2.4 Gitee ( 国内镜像 )

```bash
sh -c "$(wget -O- https://gitee.com/pocmon/mirrors/raw/master/tools/install.sh)"
```

#### 3.4.3  oh-my-zsh插件安装

##### 3.4.3.1 安装插件

```sh
 # zsh-autosuggestions
 git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

##### 3.4.3.2 配置`.zshrc`文件

```sh
plugins=(
	git
	extract
	autojump
	sudo
	cp
	zsh-autosuggestions
	zsh-syntax-highlighting
	)
source "$ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh"
```

##### 3.4.3.3 powerlevel10k主题

###### 3.4.3.3.1 Powerlevel10k 安装

- 手动安装

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

- 国内用户可以使用 [http://gitee.com](https://link.zhihu.com/?target=http%3A//gitee.com) 上的官方镜像加速下载.

```bash
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

###### 3.4.3.3.2 Powerlevel10k 配置

编辑 .zshrc 文件， 文件路径 ~/.zshrc

```
open ~/.zshrc

ZSH_THEME="powerlevel10k/powerlevel10k"

source .zshrc
```

### 3.5 安装/配置`Vim`

```sh
paru -S vim
paru -S neovim
```

[我的vim配置](https://github.com/Johnson170/Nvim_2)

### 3.6 安装`debtap`

```sh
# 安装
paru -S debtap
sudo debtap -u

#使用
sudo debtap (-q) *.deb
sudo pacman -U *.tar.zst
```

### 3.7 安装社交软件

##### 3.7.1 安装Tim

```text
paru -S deepin-wine-tim
```

安装过程中出现选择输入n就好

切换`deepin-wine`境

```text
sh /opt/deepinwine/apps/Deepin-Tim/run.sh -d
```

有问题直接去

[ 作者GitHub项目地址 ](https://link.zhihu.com/?target=https%3A//github.com/countstarlight/deepin-wine-tim-arch/issues)

开`issue`反馈就好了

如果这个版本的卡或者有其他问题，建议安装：

```text
yay -S deepin.com.qq.office
```

如果这个也没办法装，则使用linuxqq

```text
paru -S linuxqq
```

还有个不是很成熟的解决方案：

[icalingua](https://link.zhihu.com/?target=https%3A//github.com/Clansty/icalingua)

```text
paru -S icalingua
```

##### 3.7.2 安装微信

deepin-wine版：

```text
paru -S deepin-wine-wechat
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
paru -S electron-wechat

paru -S wechat-uos
```

### 3.8 安装其他软件

##### wps

wps：中文版，想要英文版把后面那个包去掉

```text
yay -S wps-office wps-office-mui-zh-cn
```

##### yesplaymusic

```sh
paru -S yesplaymusic
```

##### chrome

```text
paru -S google-chrome
```

[chrome](https://github.com/Johnson170/Chrome_Crx)

##### 百度网盘

```text
paru -S baidunetdisk
```

或者命令行（CLI）的：

```text
paru -S baidupcs-go
```

##### flameshot

最强大的截图工具 当你的tim/微信截图不好用的时候，用这个

```text
paru -S flameshot
```

设置快捷键启动的方式：

设置 -> 快捷键 -> 自定义快捷键 -> 编辑 -> 新建 -> 全局快捷键 -> 命令/URL

设置触发器：设置为你习惯的快捷键 -> 动作：命令/URL这填：/usr/bin/flameshot gui

![img](https://pic1.zhimg.com/80/v2-316b039a03508ed727fd95bd5ebaeb40_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-df9bd21b536019e168bc76fdb958d6d2_720w.jpg)

##### timeshift

强大好用的备份、回滚系统工具

```text
paru -S timeshift
```

##### XDM

Linux下最快的下载神器

```text
paru -S xdman
```

建议直接去[官网](https://link.zhihu.com/?target=http%3A//xdman.sourceforge.net/%23downloads)下载压缩包安装，比命令行快很多

##### clash

```text
paru -S clash clash-for-windows-bin
```

[ACL4SSR](https://acl4ssr-sub.github.io/)

##### sublime text4

```sh
paru -S sublime-text-4 
```

##### steam++

[steam++](https://steampp.net/)

##### 有道云笔记

[ynote](https://note.youdao.com/)

##### vscode

```sh
paru -S visual-studio-code-bin
```

##### typora

```sh
paru -S typora
```

##### ranger

```sh
paru -S ranger
```

[我的配置文件](https://hub.xn--p8jhe.tw/Johnson170/ranger)

##### fzf

```sh
paru -S fzf
```

##### neofetch

```sh
paru -S nerofetch
```

[neofetch](https://github.com/Johnson170/neofetch)

##### pip

```sh
paru -S python(3)-pip
```

##### snapd

```sh
paru -S snapd
```

##### ctags

```sh
paru -S ctags
```

##### bleachbit

```sh
paru -S bleachbit
```

## 4. 美化

### 字体

#### Jetbrains Mono

```sh
paru -S nerd-fonts-jetbrains-mono
```

#### Monaco

paru -S nonaco

#### source code pro

```sh
paru -S ttf-source-code-pro-ibx 
```

### 主题

layan

### latte

edna