# Coc.nvim配置

## 一、 安装node.js

需要`nodejs`>=12.12

### 1. Debian系  安装

#### (1). apt-get安装（不推荐）

```
sudo apt-get install nodejs
sudo apt-get install npm
```

安装的node版本太老旧

#### (2). 源代码安装

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

#### (3). 代码安装（尝试）

```bash
curl -sL install-node.now.sh/lts | bash
```

### 2. CentOS 下源码安装 Node.js

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

### 3. 其他方法

```
# wget https://nodejs.org/dist/v10.9.0/node-v10.9.0-linux-x64.tar.xz    // 下载
# tar xf  node-v10.9.0-linux-x64.tar.xz       // 解压
# cd node-v10.9.0-linux-x64/                  // 进入解压目录
# ./bin/node -v                               // 执行node命令 查看版本
v16.9.0
```

## 二、 安装Coc.nvim

https://github.com/neoclide/coc.nvim

### 1.测试是否安装成功

```
在`vim`命令行中输入`:CocInfo`，若有类似以下信息弹出表示插件安装成功
```

### 2.  build/index.js not found

```
sudo apt install nodejs 

sudo npm install -g yarn 

~/.vim/plugged/coc.nvim/  //是我的coc.nvim插件的安装目录 

cd ~/.vim/plugged/coc.nvim/	 

yarn install yarn build
```



## 三、 配置Coc.nvim

https://github.com/neoclide/coc.nvim/wiki/Language-servers

### 1. 配置python补全 

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

### 2. 配置javaScript/typescript补全 

-    安装语言服务器  

```shell
npm install typescript
```

-    安装补全插件  

```vim
:CocInstall coc-tsserver
```

###  3. 配置golang补全 

-    安装golang  

```shell
apt install golang
```

-    安装插件  

```vim
:CocInstall coc-go
```

###   4. 配置Java补全 

-    安装jdk或openjdk，注意   `    jdk版本   `   >=   `    1.8   `  
-    安装插件  

```vim
:CocInstall coc-java
```

-    进入一个Java文件，下面会有下载jdt的提示，等待下载完成即可使用  

###   5. C家族 

  由于我并不写C语言，也没安装过C的补全，所以请自行观看官方文档安装，可能会比我上面讲的这些插件难装一些。  
  官方文档地址：  [    coc.nvim-clang   ](https://github.com/clangd/coc-clangd) 

###   6. 其他的一些补全 

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

## 四、 添加插件

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

## 五、 总结

- 安装命令`:CocInstall 插件名`
- 移除命令`:CocUninstall 插件名`
- 查看已安装`:CocList extensions`
- 更新命令`:CocUpdate
