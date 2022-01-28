# Emacs初体验

## 1. emacs简介

讲解的版本是GNU Emacs

GNU emacs于1984年，Richard Stallman发起并维护

竞争对手vim，vscode等

跨平台，内核编译器c写成，通过lisp语言进行扩展

## 2. emacs安装

```shell
sudo yum install emacs
```

## 3. Emacs版本

- 最新版本（2020年3月）：26.3
- 即将发布版本：27
- 开发中不稳定版本：28

教程以26.3为演示版本，同时兼容27版本。安装前可查看包管理器安装的是否是对应版本。

```bash
# Windows 
scoop info emacs
 # Linux 
apt info emacs 
dnf info emacs 
# macOS 
brew cask info emacs
```

启动Emacs，然后查看版本。
 `M-x emacs-version`

或者在命令行中查看版本。
 `emacs --version`

## 4. 高标基本操作速记

| command | description            |
| :------ | ---------------------- |
| C-n     | 下一行                 |
| C-p     | 上一行                 |
| C-f     | 向前移动一个字符       |
| C-b     | 向后移动一个字符       |
| C-k     | 从光标位置到末尾删掉   |
| C-a     | 回到行首               |
| C-e     | 去往行尾               |
| M-<     | 回到编辑区域最开始位置 |
| M->     | 去到编辑区域最后的位置 |
| C-v     | 向上翻一屏             |
| M-v     | 向下翻一屏             |

## 5. 自带文档

```lisp
C-h t ;速记 Help Tutorial
```

查看快捷键的含义：

```lisp
C-h k ;速记 Help Keybind
```

查看函数的定义以及快捷键绑定：

```lisp
C-h f ;速记 Help Function
```

# Emacs配置初体验

## 6. 认识配置文件

Emacs配置文件的位置，会按照一下顺序去查找：

- `~/.emacs`
- `~/.emacs.d`
- `~/.config/emacs/init.el`

第一个是一个单一文件配置；第二个更符合工程化；第三个仅适用于≥27的版本。教程会从第一个入手，逐渐变为第二种的模式。

## 7. 为什么不用大牛配置

- 大牛配置
  - Spacemacs
  - Centaur Emacs
  - Doom Emacs
  - Steve Purcell
  - redguardtoo（陈斌，一年成为emacs高手作者）
  - ……

- 当然建议用大牛配置

  - 兼容性好
  - 代码质量高
  - 功能齐全
  - 开箱即用

- 为什么不用

  - 体验配置Emacs的乐趣
  - 对Emacs工作多一些了解
  - 体验大牛成长的过程
  - 追求轻量，追求速度，追求新

- 我的配置
   `https://github.com/cabins/.emacs.d`

- 另外一个轻量级的是centaur12 软件源

  什么是软件源

  为什么要用软件源

  为什么要改成国内的软件源

## 8. 软件源

- 什么是软件源
- 为什么要用软件源
- 为什么要改成国内的软件源

```lisp
(setq package-archives '(
    ("melpa" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/") 
    ("gnu" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/")
    ("org" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/org/")))
```

## 9. 安装第一个emacs扩展

安装扩展前，需要先刷新软件源的索引。

```lisp
(setq package-check-signature nil) ;个别时候会出现签名校验失败
 (require 'package) 

;; 初始化包管理器
 (unless (bound-and-true-p package--initialized) 
   (package-initialize)) 
;; 刷新软件源索引
 (unless package-archive-contents
     (package-refresh-contents))

(unless (package-installed-p 'use-package) 
    (package-refresh-contents) 
    (package-install 'use-package))
```

## 10. 使用use-package管理扩展

- 什么是use-package
  - 简单理解，是一个宏
  - 用更简单统一的方式去管理插件
- 怎么用
  - 基本格式，并举个例子

```lisp
;; 最简洁的格式
 (use-package restart-emacs)

;; 常用的格式
 (use-package smooth-scrolling 
    :ensure t ;是否一定要确保已安装
    :defer nil ;是否要延迟加载 
    :init (setq smooth-scrolling-margin 2) ;初始化参数 
    :config (smooth-scrolling-mode t) ;基本配置参数 
    :bind ;快捷键的绑定 
    :hook) ;hook模式的绑定
```

建议添加的配置（部分来自use-package官方建议）：

```lisp
(eval-and-compile 
    (setq use-package-always-ensure t) ;不用每个包都手动添加:ensure t关键字 
    (setq use-package-always-defer t) ;默认都是延迟加载，不用每个包都手动添加:defer t 
    (setq use-package-always-demand nil) 
    (setq use-package-expand-minimally t) 
    (setq use-package-verbose t))
```

顺便配置一个好看一点的Mode Line。

```lisp
(use-package smart-mode-line 
    :init 
    (setq sml/no-confirm-load-theme t) 
    (setq sml/theme 'respectful) 
    (sml/setup))
```

## 11. 工程化管理配置（1）功能拆分

到此为止，在`~/.emacs` 中配置了很多的内容，开始显得很乱：

- 初始化Elpa
- 安装扩展
- 添加主题

是时候分类处理了。就像一个开发项目那样。尝试着把配置按照功能不同拆分成不同的文件。如：

- `lisp/init-elpa.el`用于存放Elpa和Package初始化
- `lisp/init-package.el`用于存放安装的扩展
- `lisp/init-ui.el`用于存放界面主题相关配置

如何在初始化文件中把它们联系在一起（调用）？

## 12. 工程化管理配置（2）文件加载与调用

先设置加载的目标目录到 `load-path` 中。

```lisp
(add-to-list 'load-path 
    (expand-file-name (concat user-emacs-directory "lisp")))
```

各个文件通过 provide 暴露对外调用的名称。如：

```lisp
(provide 'init-ui)
```

然后在 init.el 文件中通过 `require` 调用：

```lisp
require 'init-ui
```

## 13. 关于自定义的配置

建议写入一个单独的文件，对外开源的时候，该文件不被提交到Git中。如，`custom.el`



```lisp
(setq custom-file 
    (expand-file-name "custom.el" user-emacs-directory))
```

这样就会把通过图形界面做的一些配置，写入到这里了。

## 14. 操作系统判断

后面的部分配置会因操作系统不同而产生不同配置代码，所以有必要对操作系统进行判断。

```lisp
(defconst *is-mac* (eq system-type 'darwin)) 
(defconst *is-linux* (eq system-type 'gnu/linux)) 
(defconst *is-windows* (or (eq system-type 'ms-dos) (eq system-type 'windows-nt)))
```

## 15. macOS平台将Command键映射为Meta【可选】

对于Mac键盘来说，Command键距离x的位置与Windows键盘更相近，所以习惯上可以修改这个按键映射为Meta，如果你不需要这个设置，可以不用管。



```lisp
(when *is-mac* 
    (setq mac-command-modifier 'meta)
    (setq mac-option-modifier 'none))
```

## 16. 通过修改字体解决Windows上Emacs的卡顿

```lisp
(use-package emacs 
  :if (display-graphic-p) 
  :config 
  ;; Font settings 
  (if *is-windows* 
    (progn 
      (set-face-attribute 'default nil :font "Microsoft Yahei Mono 9") 
      (dolist (charset '(kana han symbol cjk-misc bopomofo)) 
        (set-fontset-font (frame-parameter nil 'font) charset (font-spec :family "Microsoft Yahei Mono" :size 12)))) 
  (set-face-attribute 'default nil :font "Source Code Pro for Powerline 11")))
```

## 17. 建议增加的一点启动配置

设置系统的编码，避免各处的乱码。

```lisp
(prefer-coding-system 'utf-8)
(set-default-coding-systems 'utf-8) 
(set-terminal-coding-system 'utf-8) 
(set-keyboard-coding-system 'utf-8) 
(setq default-buffer-file-coding-system 'utf-8)
```

设置垃圾回收阈值，加速启动速度。

```lisp
(setq gc-cons-threshold most-positive-fixnum)
```

## 18. 测试启动耗时

通过 `benchmark-init` 包进行启动耗时的测量。

```lisp
(use-package benchmark-init 
  :init (benchmark-init/activate) 
  :hook (after-init . benchmark-init/deactivate))
M-x benchmark-init/show-durations-tree` 或者 `M-x benchmark-init/show-durations-tabulated
```

## 19. 中断命令输入

`C-g`在输入任何命令的时候，可随时通过该快捷键放弃命令的继续键入。

## 20. 用y/n来代替yes/no

```lisp
(defalias 'yes-or-no-p 'y-or-n-p)
```

为了项目管理的统一化，可以用如下的`use-package`写法：

```lisp
(use-package emacs :config (defalias 'yes-or-no-p 'y-or-n-p))
```

## 21. 显示行号

### 21.1 行号的类型

从Emacs 26开始，自带了行号显示功能，可以通过设置`display-line-numbers-type`变量的值，来改变行号的类型。可取值有：

- relative，相对行号
- visual，可视化行号
- t

### 21.2 行号配置

```lisp
(use-package emacs 
    :config 
    (setq display-line-numbers-type 'relative) 
    (global-display-line-numbers-mode t))
```

> 注：
>  有些时候我发现在Windows上开启了行号会让屏幕滚动的时候闪烁，此时，我们可以让其在Windows上不开启行号。

```lisp
(use-package emacs 
    :unless *is-windows* 
    :config 
    (setq display-line-numbers-type 'relative) 
    (global-display-line-numbers-mode t))
```

# Emacs文本编辑

## 22. 文本编辑之选中/复制/剪切/粘贴

**选中**

- 方法一，使用鼠标拖动选中
- 方法二，标记开始位置，然后通过移动光标到结束位置
  - 默认使用C-SPC或C-@进行开始位置的标记
  - 对于很多场景下，C-SPC快捷键被占用（输入法，Spotlight等）

**复制**
 `M-w`

**剪切**
 `C-w`

**粘贴**
 `C-y ;; 速记 Yank`

## 23. 文本编辑之删除

**场景一：从光标位置到行尾的删除**
 `C-k`

**场景二：删除当前行**

默认没有这样的快捷键，可以安装一个扩展来实现，`crux`，该扩展提供了包含该场景在内的一系列优化快捷命令。



```lisp
(use-package crux 
    :bind ("C-c k" . crux-smart-kill-line))
```

后面补充一节课来介绍这个扩展。

**场景三：一次性删除到前/后面的第一个非空字符（删除连续的空白）**

通过`hungry-delete`扩展实现，该功能。



```lisp
(use-package hungry-delete 
    :bind (("C-c DEL" . hungry-delete-backward)
           ("C-c d" . hungry-delete-forward)))
```

## 24. 文本编辑之行/区域上下移动

在VSCode或Jetbrains家族的编辑器中，上下移动行/块是非常容易的，但原生的Emacs没有提供这样的功能。可通过插件`drag-stuff`来辅助实现。



```lisp
(use-package drag-stuff 
  :bind (("<M-up>". drag-stuff-up) 
         ("<M-down>" . drag-stuff-down)))
```

## 25. 文本编辑之原生搜索/替换

Emacs原生提供了强大的搜索和替换功能。

```lisp
;; 当前位置到文档结尾的搜索 
C-s
;; 当前位置到文档开始的搜索 
C-r 
;; 替换 
M-%
```

替换时按下`y`确认替换，`n`跳过本处的替换，`!`全部替换。

## 26. 文本编辑之强化搜索

著名的`ivy-counsel-swiper`三剑客，就是为了强化搜索（同时优化了一系列Minibuffer的操作）。

```lisp
(use-package ivy 
  :defer 1 
  :demand 
  :hook (after-init . ivy-mode) 
  :config 
  (ivy-mode 1) 
  (setq ivy-use-virtual-buffers t 
        ivy-initial-inputs-alist nil 
        ivy-count-format "%d/%d " 
        enable-recursive-minibuffers t 
        ivy-re-builders-alist '((t . ivy--regex-ignore-order))) 
  (ivy-posframe-mode 1))) 

(use-package counsel 
  :after (ivy) 
  :bind (("M-x" . counsel-M-x) 
         ("C-x C-f" . counsel-find-file) 
         ("C-c f" . counsel-recentf)
         ("C-c g" . counsel-git))) 

(use-package swiper 
  :after ivy 
  :bind (("C-s" . swiper) 
         ("C-r" . swiper-isearch-backward)) 
  :config (setq swiper-action-recenter t 
                swiper-include-line-number-in-search t))
```

## 27. 文本编辑之自动补全

Emacs界补全两大垄断插件，`Auto-Complete`和`Company`（Complete Anything）。时代和特性原因，`Company`呈现压倒性优势。

```lisp
(use-package company 
  :config 
  (setq company-dabbrev-code-everywhere t 
        company-dabbrev-code-modes t 
        company-dabbrev-code-other-buffers 'all 
        company-dabbrev-downcase nil 
        company-dabbrev-ignore-case t 
        company-dabbrev-other-buffers 'all 
        company-require-match nil 
        company-minimum-prefix-length 2 
        company-show-numbers t 
        company-tooltip-limit 20 
        company-idle-delay 0 
        company-echo-delay 0 
        company-tooltip-offset-display 'scrollbar 
        company-begin-commands '(self-insert-command)) 
  (push '(company-semantic :with company-yasnippet) company-backends) 
  :hook ((after-init . global-company-mode)))
```

但，注意下认识到`Company`的不足：`文档`。其文档都是写在源码中的。

## 28. 文本编辑之语法检查

默认Emacs自带了`Flymake`，但`Flycheck`是一个更优的方案。

建议全局启用：



```lisp
(use-package flycheck 
  :hook (after-init . global-flycheck-mode))
```

如果你只想在编程语言的模式下启用：



```lisp
(use-package flycheck 
  :hook (prog-mode . flycheck-mode))
```

## 29. 文本编辑之行排序

```lisp
M-x sort-lines
```

## 30. 文本编辑之顺序更换

光标前后两个字符互换
 `C-t`

光标前后两个单词互换
 `M-t`

## 31. 文本编辑之字数统计

整个Buffer统计

```lisp
M-= 
;; 或者 
M-x count-words-region
```

选中区域统计

```lisp
;; 先选中 
M-x count-words
```

# Emacs常用插件

## 32. crux优化Emacs常用操作

- 优化版的回到行首
- 快速打开Emacs配置文件
- 快速连接两行等

```lisp
(use-package crux 
  :bind (("C-a" . crux-move-beginning-of-line) 
         ("C-c ^" . crux-top-join-line)
         ("C-x ," . crux-find-user-init-file)
         ("C-c k" . crux-smart-kill-line)))
```

## 33. 如何记忆越来越长的快捷键

> 答案：不用记忆。

如果你不喜欢用鼠标在菜单栏点击的化，你其实可以用Which-Key这款插件来辅助你“记忆”。

```lisp
(use-package which-key 
  :defer nil 
  :config (which-key-mode))
```

当你按下一个按键时，它会提示你下一个按键的含义。并等待你去按下。

# Emacs窗口管理

## 34. 窗口管理之Buffer管理

Buffer切换

```lisp
C-x b
```

杀死当前Buffer

```lisp
C-x k
```

批量管理Buffer

```lisp
C-x C-b ;; 进入Buffer列表 
d ;; 标记删除 
u ;; 取消当前行标记 
U ;; 取消全部标记 
x ;; 执行操作 
? ;; 查看按键帮助
```

## 35. 窗口管理之MiniBuffer交互优化

显示在左下角的MiniBuffer移动视线范围大，移动到中央位置，更合适一些。



```lisp
(use-package ivy-posframe
  :init
  (setq ivy-posframe-display-functions-alist 
    '((swiper . ivy-posframe-display-at-frame-center)
      (complete-symbol . ivy-posframe-display-at-point)
      (counsel-M-x . ivy-posframe-display-at-frame-center)
      (counsel-find-file . ivy-posframe-display-at-frame-center)
      (ivy-switch-buffer . ivy-posframe-display-at-frame-center)
      (t . ivy-posframe-display-at-frame-center)))
```

## 36. 窗口管理之分屏

开启横向分屏
 `C-x 3`

开启纵向分屏
 `C-x 2`

只保留当前分屏
 `C-x 1`

关闭当前分屏
 `C-x 0`

## 37. 窗口管理之分屏宽度调整

增加高度
 `C-x ^`

增加/减少宽度
 `C-x {`
 `C-x }`

## 38. 窗口管理之快速切换分屏

默认可通过以下快捷键进行窗口的循环跳转：
 `C-x o`

当分屏很多的时候效率非常低。通过ace-window可快速进行窗口间的跳转。类似的插件较多，但该款为最佳方案。



```lisp
(use-package ace-window 
    :bind (("M-o" . 'ace-window)))
```

![img](https:////upload-images.jianshu.io/upload_images/551195-8fc38247c672fc5f.png?imageMogr2/auto-orient/strip|imageView2/2/w/761/format/webp)

image.png

## 39. 窗口管理之Tab标签页管理

```lisp
C-x t 2 ;; 新建Tab 
      1 ;; 关闭其它Tab 
      0 ;; 关闭当前Tab 
      b ;; 在新Tab中打开Buffer
```

参考链接：https://www.jianshu.com/p/42ef1b18d959



TODO

拷贝选择一串字符然后拷贝



tab键好像只能按一次



