# 最终效果

先看预览效果，有必要往下看。无所谓的不耽误时间

1，语义级别代码高亮，关键字，命名空间，枚举，成员变量，字符串，函数，类名，注释，分别用不同的颜色标注出来。增加心里距离，阅读代码更容易

2，基于TAG的标签跳转

3，基于语义的实时编译分析精确跳转

4，clang-tidy大杀器，让编译器来教你写代码，支持编译器快速修复代码缺陷，帮你写出更高效代码

   移动构造函数只是置换资源，为什么要抛出异常？

![noexpect](/Users/menglingfeng/Documents/GitHub/learn_emacs/noexpect.gif)

​      别随便move，你吵到我RVO了

![rvo](/Users/menglingfeng/Documents/GitHub/learn_emacs/rvo.gif)

​     不会还有人不知道范围for循环要用引用吧

![for-ref](/Users/menglingfeng/Documents/GitHub/learn_emacs/for-ref.gif)

5，文件搜索/字符串搜索/，列表展示。支持模糊搜索和正则表达式，实时预览

6，代码自动提示补全，自动触发，这个功能并不卡，图里是我手慢了

7，vim8以前的同步时代，ale，cpplint是很棒的静态检查工具。vim8能异步刷新后，实时编译的动态语法检查是更好的选择

8，代码格式化，LLVM/GNU/Google多种格式化风格供选择，clang-format丰富的配置选项，强大的跨平台支持，简单的使用方式，更容易提升团队代码一致性。选中格式化，不会碰到别人的代码

9，括号彩色匹配，单词高亮

10，像鼠标一样一秒移动光标

11，添加VIM宏，快速插入作者信息



# 环境准备

写代码最常用的功能，包含以下几点

- 文件查找，快速找到需要编辑的文件
- 字符串/关键字搜索，搜索注释，搜索某个字段在哪些地方被使用
- 实时的语义检查，可以提供自动补全，语法检查，语法高亮。
- 代码拷贝
- 代码格式化

文件查找并不复杂，我们的工程文件数有限，如果需要上万级别的目录搜索文件，请替换成[FD](https://github.com/chinanf-boy/fd-zh)。否则vim自带的插件已经足够，插件放在后面的插件章节来讲。

代码的复制粘贴，vim本身支持的复制粘贴功能已经足够强大(秒杀windows)，并且使用简单，这里也不多说

实时的语义检查，高效字符串搜索，代码格式化，实现起来都非常复杂。因此vim本身的插件并不能解决。需要一个提供这些功能的外部程序给vim调用，来完成这些工作。这里主要讲这些工具。

先安装所有centos必备软件包

- gcc10，接下来很多程序，在厂内的老旧的开发机系统没有现成的，因此需要从源码编译，而这些软件的编译依赖于GCC版本。厂内自带的gcc版本由于缺少库一系列复杂的情况。折腾这些不如自己重新编一个

gcc所需要的gmp等依赖，这个包里已经打包好，下下来直接安装就可以。编译的时候视情况把你的线程数开大一些。

注意安装路径换成你自己的路径，centos6下编译由于字节对齐问题，可能会遇到assertion_failed __1102这个报错，导致asan功能无法使用。但是没关系，这个不影响后续编译。直接make install。

- cmake>3.4 ,原因同上，很多软件源码编译依赖cmake版本。如果你的yum源安装的版本比较低，这一步建议也从源码编译安装

- python3，很多插件用python3编写，依赖于python3共享库,系统自带的没有

- zlib，编译clangd的时候对zlib的版本有要求，这里自己编译一个高版本的

- ripgrep，rust大杀器，确定一定以及肯定百分之百毫无疑问没有悬念的史上最快grep工具。小文件几乎感受不到区别，工程大了以后和linux grep至少有5倍的速度差距，极限情况比linux grep快出10倍。

- clangd && clang-format

  clang-format，用于代码格式化，包括大小括号，换行，括号对齐，构造函数，赋值对齐等多种功能，具体选项请参考[LLVM官方文档](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)

  clangd，了解clangd之前，简单介绍一下[Language Server Protocol](https://langserver.org/)，详细介绍可以参考韦大的[Vim 8 中 C/C++ 符号索引：LSP 篇](https://zhuanlan.zhihu.com/p/37290578)(看完介绍就回来吧)。

   大概原理是有个前端编译工具在后台实时编译，编译工具需要知道你正在编辑的代码如何构建。然后提供统一的结果查询接口。编辑器里装个前端插件，在你跳转的时候，插件调用接口查询后端实时编译的结果。然后展示在编辑器里。由于是实时编译的结果，所以精准的定义跳转，语义级别的自动补全，代码高亮。都可以顺利实现完成

   C++后端实时构建的工具有三种，[cquery](https://github.com/jacobdufault/cquery)，[ccls](https://github.com/MaskRay/ccls)，[clangd](https://clangd.llvm.org/)。cquery停止维护，bug巨多，不予考虑。ccls这两年综合来看效果最好，还支持语义高亮。但是这篇文章讲clangd，毕竟LLVM官方出品，专业程度有保证，维护更新够及时，制作团队更专业。

   前端的查询插件，VIM上基本有两种产品，[YouCompleteMe](https://github.com/ycm-core/YouCompleteMe) 和 [coc.nvim](https://github.com/neoclide/coc.nvim)。YouCompleteMe在无LSP时就是补全届的扛把子，谷歌出品值得信赖。但是这篇文章我们用COC，唯一的原因是好装。功能够用，少折腾

  编译clangd需要以下条件

  - CMake > = 3.13.4
  - GCC >= 5.1.0
  - Python >= 3.6
  - zlib >= 1.2.3.4
  - Make >= 3.79

  然后开始编译

- gtags，linux-vim上比较出名的是ctags，全局采集符号建立索引。索引建立好以后利用二分遍历查找，来实现函数的浏览以及跳转。可以在跳转时做符号匹配。

  gtags可以理解为ctags的升级版，不仅继承了ctags原有的所有功能，而且还有以下几个主要优势

  

  - 可以建立引用索引，除了支持查找定义外，还支持查找引用
  - 对比ctags全量更新索引，gtags采用增量更新的方式，文件改动时更快的生成索引
  - gtags支持采用sqlite3作为数据库，对比ctags的文本文件，查找速度更快
  - 支持的语言更多c/cpp/java/go/ruby

  gtags的数据库生成有污染目录，手动生成索引一类比较蛋疼的问题，不过借助下文提到的插件，很容易解决这个问题

- nodeJS，LSP的前端查询插件COC.VIM需要node>10.12

- vim，在vim8以前的异步时代，实现类似ide的功能过于困难。调用，就是执行黑盒指令，就是执行时间长短不确定的动作。无论是索引查询还是自动补全，只要是在原地同步等待返回结果，这段时间内都无法操作。对于一个编辑器来说，没有什么比ui被卡住带来的体验更糟糕。vim8增加了异步插件接口支持后，同步问题得以解决，新的插件比如highlight，leaderF依赖于python3，加上打开clipboard，对于复制搜索有质的使用体验提升。所以来编译vim8吧

  注意使用上面python3的路径库，--with-x这个选项至关重要，一定要打开。这个选项用来使用clipboard。安装clipboard之后，在vim中可以用Y复制以后用ctrl+v来粘贴。

  如果你不理解这个选项的实用性在哪里，请看下面这张图，我想全局搜索一下ServerOptions这个单词，只需要复制，然后调出搜索框，ctrl+v就可以立马出现搜索结果。

# 插件教程

这里使用vim-plug安装插件，vim-plug的安装教程网上有很多，自行参考。确保你的vim版本大于8.1.1564

在.vimrc里面配置要安装的插件

```
call plug#begin('~/.vim/plugged')
Plug 'tpope/vim-fugitive'       #配置git一起使用，作用自行百度
Plug 'kien/rainbow_parentheses.vim'   #括号配对
Plug 'morhetz/gruvbox'         #一个vim主题，对于本篇的插件有很好的高亮效果
Plug 'prabirshrestha/vim-lsp'  #需要高亮支持请安装此插件，具体作用自行百度
Plug 'jackguo380/vim-lsp-cxx-highlight'    #同上
Plug 'easymotion/vim-easymotion'          #光标快速移动
Plug 'neoclide/coc.nvim', {'branch': 'release'}   #IDE效果实现的前端，上文中有提到。具体使用方式参考 https://github.com/neoclide/coc.nvim
Plug 'lfv89/vim-interestingwords'      #cpp基于字符串匹配的语法高亮，其他配置自行百度
Plug 'vim-airline/vim-airline'         #窗口效果
Plug 'ludovicchabant/vim-gutentags'    #在vim中使用gtags的插件，用法自行百度
Plug 'Yggdroot/LeaderF', { 'do': './install.sh' }    #组合所有的搜索功能的vim前端，作者在知乎比较活跃，有问题可以问。更多用法自行百度LeaderF
Plug 'scrooloose/nerdtree',{'on':'NERDTreeToggle'}    #vim经典插件，目录树
call plug#end()
```

vimrc配置，下面放上我的配置。根据自己使用习惯酌情修改

```
#LeaderF
let g:Lf_ShortcutF = '<c-p>'     #ctrl+p 搜索文件
let g:Lf_StlSeparator = { 'left': '', 'right': '', 'font': '' }  #搜索框位置
let g:Lf_RootMarkers = ['.project']   #顶层目录的标记名称，文件搜索，字符串搜索，gtags搜搜都从标记位置开始
let g:Lf_WorkingDirectoryMode = 'Ac'  #搜索时显示相对目录
let g:Lf_WindowHeight = 0.30      #搜索窗口高度
let g:Lf_CacheDirectory = expand('~/.vim/cache')  #LeaderF会自动管理GTAGS，自动生成的cache存放目录
let g:Lf_ShowRelativePath = 0     
let g:Lf_HideHelp = 1      #隐藏帮助窗口
let g:Lf_GtagsAutoGenerate = 1   #自动生成GTAGS文件
 
 
#映射
vnoremap <C-K> :py3f /usr/share/clang/clang-format.py<cr>   #格式化选中代码块，后面路径注意自己替换
"nmap s <Plug>(easymotion-s)    
vmap s <Plug>(easymotion-s)
nmap s <Plug>(easymotion-overwin-w)
"vmap k <Plug>(easymotion-overwin-w)     #各种状态下，按s进行光标移动
nnoremap <F12> :RainbowParenthesesToggle<cr>:RainbowParenthesesLoadBraces<cr>   #F12键进行括号匹配高亮
nnoremap <leader>ff <esc>:Leaderf gtags<cr>  #leader+ff 显示所有tag
nnoremap <leader>fr :<C-U><C-R>=printf("Leaderf! gtags -r %s --auto-jump", expand("<cword>"))<CR><CR>  #leader+ff 根据tag查找引用
nnoremap <leader>fd :<C-U><C-R>=printf("Leaderf! gtags -d %s --auto-jump", expand("<cword>"))<CR><CR>  #leader+fd 根据tag查找定义
nnoremap <leader>fo :<C-U><C-R>=printf("Leaderf! gtags --recall %s", "")<CR><CR>   #leader+fo 重新打开上次的搜索
nnoremap <c-b> :Leaderf rg --current-buffer --sort modified<cr>   #文本内搜索字符串，按照修改顺序展示
nnoremap <c-a> :Leaderf rg<CR>   #搜索顶层目录下所有字符串
inoremap <esc> <Nop>   
cnoremap <esc> <Nop>    #屏蔽esc键
tnoremap jk <c-\><c-n>
cnoremap jk <esc>
inoremap jk <esc>       #jk连按替换位esc
nnoremap <c-h> :bp<cr>  #下一个tab页，配合airline插件更舒服
nnoremap <c-l> :bn<cr>
nnoremap <c-f> <esc>:w<cr>:LeaderfFunction<cr>   #显示函数列表
nnoremap <c-x> :w<cr>:bdelete<cr>       #关闭当前buffer
 
let g:airline_powerline_fonts = 1  #支持 powerline 字体
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline#extensions#tabline#enabled = 1 #显示窗口tab和buffer
```

贴一份自己的.vimrc配置，仅供参考

```
set nocp
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1
set t_Co=256
set updatetime=300
autocmd BufEnter *.txt set ft=confluencewiki
autocmd BufNewFile *.txt set ft=confluencewiki
set bg=dark
filetype indent on "根据文件类型进行缩进"
filetype on
filetype plugin indent on
set autochdir "当前目录随着被编辑文件的改变而改变"
set autoindent
set autoread "文件在Vim之外修改过，自动重新读入"
set autowrite "设置自动保存内容"
set background=dark
set cindent
set cmdheight=2
set cursorline
set encoding=utf-8
set expandtab
set fencs=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936
set fileencodings=utf-8,ucs-bom,chinese
set hidden
set incsearch "边输入边搜索(实时搜索)"
set langmenu=zh_CN.UTF-8
set mouse-=a
set nobackup
set nocompatible
set nohlsearch
set noswapfile
set nowritebackup
set nu
set termwinsize=13x0
set pastetoggle=<F11> "F11来支持切换paste和nopaste状态。"
set ruler "显示当前光标位置"
set selection=exclusive
set selectmode=mouse,key
set shiftwidth=4
set shortmess+=c
set showmatch
set smarttab
set softtabstop=4 "4 character as a tab"
set tabstop=4
set updatetime=300
set wildmenu
set backspace=indent,eol,start
let g:molokai_original = 1
let mapleader = "\<space>"
let g:rehash256 = 1
let g:leetcode_browser = 'chrome'
 
 
let g:Lf_ShortcutF = '<c-p>'
let g:Lf_StlSeparator = { 'left': '', 'right': '', 'font': '' }
let g:Lf_RootMarkers = ['.project']
let g:Lf_WorkingDirectoryMode = 'Ac'
let g:Lf_WindowHeight = 0.30
let g:Lf_CacheDirectory = expand('~/.vim/cache')
let g:Lf_ShowRelativePath = 0
let g:Lf_HideHelp = 1
let g:Lf_GtagsAutoGenerate = 1
let g:gutentags_enabled = 0
" gutentags 搜索工程目录的标志，碰到这些文件/目录名就停止向上一级目录递归
" 将自动生成的 tags 文件全部放入 ~/.cache/tags 目录中，避免污染工程目录
" 所生成的数据文件的名称
" 配置 ctags 的参数
let g:gutentags_project_root = ['.gtags']
call plug#begin('~/.vim/plugged')
Plug 'tomasiser/vim-code-dark'
Plug 'ianding1/leetcode.vim'
Plug 'tpope/vim-fugitive'
Plug 'kien/rainbow_parentheses.vim'
Plug 'morhetz/gruvbox'
Plug 'prabirshrestha/vim-lsp'
Plug 'jackguo380/vim-lsp-cxx-highlight'
Plug 'easymotion/vim-easymotion'
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'lfv89/vim-interestingwords'
Plug 'vim-airline/vim-airline'
Plug 'ludovicchabant/vim-gutentags'
Plug 'Yggdroot/LeaderF', { 'do': './install.sh' }
Plug 'scrooloose/nerdtree',{'on':'NERDTreeToggle'}
call plug#end()
colorscheme gruvbox
" hilight
let g:cpp_class_decl_highlight=1
let g:cpp_class_scope_highlight = 1
let g:cpp_concepts_highlight = 1
let g:lsp_cxx_hl_use_text_props = 1
 
 
let g:rbpt_colorpairs = [
            \ ['brown',       'RoyalBlue3'],
            \ ['Darkblue',    'SeaGreen3'],
            \ ['darkgray',    'DarkOrchid3'],
            \ ['darkgreen',   'firebrick3'],
            \ ['darkcyan',    'RoyalBlue3'],
            \ ['darkred',     'SeaGreen3'],
            \ ['darkmagenta', 'DarkOrchid3'],
            \ ['brown',       'firebrick3'],
            \ ['gray',        'RoyalBlue3'],
            \ ['darkmagenta', 'DarkOrchid3'],
            \ ['Darkblue',    'firebrick3'],
            \ ['darkgreen',   'RoyalBlue3'],
            \ ['darkcyan',    'SeaGreen3'],
            \ ['darkred',     'DarkOrchid3'],
            \ ['red',         'firebrick3'],
            \ ]
let g:rbpt_max = 16
let g:rbpt_loadcmd_toggle = 0
syntax enable
syntax on
 
vnoremap <C-K> :py3f /usr/share/clang/clang-format.py<cr>
"nmap s <Plug>(easymotion-s)
vmap s <Plug>(easymotion-s)
nmap s <Plug>(easymotion-overwin-w)
"vmap k <Plug>(easymotion-overwin-w)
nnoremap <F12> :RainbowParenthesesToggle<cr>:RainbowParenthesesLoadBraces<cr>
nnoremap <leader>ff <esc>:Leaderf gtags<cr>
nnoremap <leader>fr :<C-U><C-R>=printf("Leaderf! gtags -r %s --auto-jump", expand("<cword>"))<CR><CR>
nnoremap <leader>fd :<C-U><C-R>=printf("Leaderf! gtags -d %s --auto-jump", expand("<cword>"))<CR><CR>
nnoremap <leader>fo :<C-U><C-R>=printf("Leaderf! gtags --recall %s", "")<CR><CR>
nnoremap <leader>jf $%d0:%!python3 -m json.tool<CR>
nnoremap <c-b> :Leaderf rg --current-buffer --sort modified<cr>
nnoremap <c-a> :Leaderf rg<CR>
nnoremap <c-t> :below terminal<cr>
inoremap <esc> <Nop>
cnoremap <esc> <Nop>
tnoremap jk <c-\><c-n>
cnoremap jk <esc>
inoremap jk <esc>
nnoremap <c-h> :bp<cr>
nnoremap <c-l> :bn<cr>
nnoremap <c-f> <esc>:w<cr>:LeaderfFunction<cr>
nnoremap <c-x> :w<cr>:bdelete<cr>
 
 
let g:airline_powerline_fonts = 1  " 支持 powerline 字体
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline#extensions#tabline#enabled = 1 "显示窗口tab和buffer
 
 
" Always show the signcolumn, otherwise it would shift the text each time
" diagnostics appear/become resolved.
if has("patch-8.1.1564")
  " Recently vim can merge signcolumn and number column into one
  set signcolumn=number
else
  set signcolumn=yes
endif
 
#######以下是我直接从coc的官网上粘过来的。能看懂的自己改改
" Use tab for trigger completion with characters ahead and navigate.
" NOTE: Use command ':verbose imap <tab>' to make sure tab is not mapped by
" other plugin before putting this into your config.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"
 
function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction
 
" Use <c-space> to trigger completion.
if has('nvim')
  inoremap <silent><expr> <c-space> coc#refresh()
else
  inoremap <silent><expr> <c-@> coc#refresh()
endif
 
" Use <cr> to confirm completion, `<C-g>u` means break undo chain at current
" position. Coc only does snippet and additional edit on confirm.
" <cr> could be remapped by other vim plugin, try `:verbose imap <CR>`.
if exists('*complete_info')
  inoremap <expr> <cr> complete_info()["selected"] != "-1" ? "\<C-y>" : "\<C-g>u\<CR>"
else
  inoremap <expr> <cr> pumvisible() ? "\<C-y>" : "\<C-g>u\<CR>"
endif
 
" Use `[g` and `]g` to navigate diagnostics
" Use `:CocDiagnostics` to get all diagnostics of current buffer in location list.
nmap <silent> [g <Plug>(coc-diagnostic-prev)
nmap <silent> ]g <Plug>(coc-diagnostic-next)
 
" GoTo code navigation.
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)
 
" Use K to show documentation in preview window.
nnoremap <silent> K :call <SID>show_documentation()<CR>
 
function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  else
    call CocAction('doHover')
  endif
endfunction
 
" Highlight the symbol and its references when holding the cursor.
autocmd CursorHold * silent call CocActionAsync('highlight')
 
" Symbol renaming.
nmap rn <Plug>(coc-rename)
 
" Formatting selected code.
xmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>qf  <Plug>(coc-fix-current)
 
augroup mygroup
  autocmd!
  " Setup formatexpr specified filetype(s).
  autocmd FileType typescript,json setl formatexpr=CocAction('formatSelected')
  " Update signature help on jump placeholder.
  autocmd User CocJumpPlaceholder call CocActionAsync('showSignatureHelp')
augroup end
 
" Use <C-l> for trigger snippet expand.
imap <C-l> <Plug>(coc-snippets-expand)
 
" Use <C-j> for select text for visual placeholder of snippet.
vmap <C-j> <Plug>(coc-snippets-select)
 
" Use <C-j> for jump to next placeholder, it's default of coc.nvim
let g:coc_snippet_next = '<c-j>'
 
" Use <C-k> for jump to previous placeholder, it's default of coc.nvim
let g:coc_snippet_prev = '<c-k>'
 
" Use <C-j> for both expand and jump (make expand higher priority.)
imap <C-j> <Plug>(coc-snippets-expand-jump)
```