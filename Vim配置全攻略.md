# Vim配置全攻略
<img src="/home/john/Wallpaper/j827jp.png" alt="这是一个图片">
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
