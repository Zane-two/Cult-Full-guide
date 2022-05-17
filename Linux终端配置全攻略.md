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



