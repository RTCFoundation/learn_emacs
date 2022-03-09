首先把vim插件上的leader相关的快捷键学习一遍

| 功能                      | 快捷键   |
| ------------------------- | -------- |
| list buffers              | C-x C-b  |
| switch buffer             | C-x b    |
| save some buffers         | C-x s    |
| save file                 | C-x C-s  |
| delete all but one window | C-x 1    |
| delete current buffer     | C-x k    |
| 窗口跳转                  | Meta-number |
| close current window      | C-x 0    |

## keyreq测量并优化工作流

https://gist.github.com/redguardtoo/5b3964f58ef81ed2c787b2a337dbacc3

| 功能                                                    | 快捷键           |
| ------------------------------------------------------- | ---------------- |
| evil-jump-items                                         | %                |
| select-window-2                                         | M-2或 ,2         |
| select-window-1                                         | M-1或 ,1         |
| 将当前emacs放到后台                                     | ,xz              |
| recentf                                                 | ,rr              |
| grep                                                    | ,qq              |
| back-to-previous-buffer                                 | ,bb              |
| select-windows-3                                        | M-3 or ,3        |
| **Hydra-launcher/body**                                 | C-c, C-y         |
| find-tag                                                | C-]              |
| **eval-expression**                                     | ,ee              |
| **comint-interrupt-subjob**                             |                  |
| Evil-visualstar/begin-search-forward                    | *                |
| **copy-to-x-clipboard**                                 | ,aa              |
| pop-tag-mark                                            | C-t              |
| Counsel-grep-or-swiper                                  | C-s/,ss          |
| narrow-or-widen-dwim                                    | ,ww              |
| **hydra-launcher/my-erase-current-buffer-and-exit**     |                  |
| imenu(显示本文件的symbols)                              | ,ii              |
| paste-form-x-clipboard                                  | ,pp              |
| evil-toggle-input-method（切换输入法）                  | C-\              |
| git-add-current-file                                    | ,va              |
| Evilnc-comment-or-uncomment-lines                       | ,ci              |
| dired-next-line                                         | space            |
| dired-previous-line                                     | p                |
| find-function                                           | C-h C-f          |
| find-file-in-project-by-selected                        | ,kk              |
| kill-buffer                                             | ,xk              |
| My-evil-goto-definition                                 | Gt               |
| My-split-window-vertically                              | ,x2              |
| evilmi-select-items                                     | ,si              |
| Find-file-in-project                                    | ,ip              |
| hydra-git/body                                          | C-c C-g          |
| counsel-etags-grep-current-directory                    | ,dd              |
| winnner-undo                                            | C-c <left>       |
| Evil-surround-region                                    | <visual-state>gS |
| beginning-of-defun                                      | gh               |
| indent-for-tab-command                                  |                  |
| find-file-in-current-directory                          | ,tt              |
| Counsel-browse-kill-ring                                | ,yy              |
| Er/expand-region                                        | ,xx              |
| hydra-launcher/shellcop-reset-with-new-command-and-exit |                  |
| evilmr-replace-in-buffer                                | ,rb              |
| my-transient-winner-undo                                | ,uu              |
| Counsel-describe-function                               | C-h f            |
| my-counsel-git-grep                                     | ,gg              |
| find-file-in-project-at-point                           | ,jj              |

indent-rigidly-left-to-tab-stop可以将代码取消缩进

## 视频中的使用方法

1. 先选中区域，（v%）

2. 单独提取到一个buffer中，（，ww)
3. 在要替换的单词上，点击evilmr-replace-in-buffer，（，rb）
4. 填写对应的单词，然后替换即可

## 多文件替换

1. 选中单词或区域（v）

2. 使用grep进行查找 (,qq)

3. counsel-etags-grep (C-c C-o)

4. C-x C-q进入可编写模式
5. 在要替换的单词上，点击evilmr-replace-in-buffer，（，rb）
6. 替换单词完单词，C-c C-c表示结束 （具体命令参考wgrep.el， C-h wgrep）

备注：

| dd删除一出occur，将来就不会替换了 |
| --------------------------------- |
| C-c C-d 删除这一行                |

## 打开文件技巧

在工程目录下输入emacs

1. （,kk），输入关键字就可以查找文件了
2. 在一个文件中的头文件地方，(,jj)，可以跳转到头文件里。然后通过（,bb)跳转回去
3. (,tt)只在当前目录下查找

## 子窗口操作技巧

1. 使用winnumber

子窗口跳转，（，+number）

2. ace-window

C-x o

## 开发环境使用

没有workspace的概念，完全自由

| 常用快捷键 |
| ---------- |
| ,jj        |
| ,bb        |
| Find-tag   |
| ,ii        |
|            |

只剩下当前窗口：,x1

恢复上次的窗口布局: uu



‘find-tag’ is an obsolete command (as of 25.1); use ‘xref-find-definitions’ instead.

xref-find-definitions (M-.)

xref-find-references (M-?)



## 基于tags的代码导航和补全

https://zhuanlan.zhihu.com/p/125524093

代码补全：company-ctags

代码导航：counsel-etags

其实忽略tags，company（complete any)

Counsel 基于ivy实现的

https://github.com/redguardtoo/counsel-etags#counsel-etags

Please install `counsel-etags` from [MELPA](https://melpa.org/#/counsel-etags).

If [Exuberant Ctags](http://ctags.sourceforge.net/) or [Universal Ctags](https://ctags.io/) exists, this program works out of box.

Universal Ctags is actively maintained and strongly recommended.

Or else, customize `counsel-etags-update-tags-backend` to create tags file with your own CLI. Please note [etags](https://www.gnu.org/software/emacs/manual/html_node/emacs/Create-Tags-Table.html#Create-Tags-Table) bundled with Emacs is not supported anymore.

[It’s reported](https://github.com/redguardtoo/emacs.d/issues/697#issuecomment-394141015) “Exuberant Ctags” v5.8.5 is buggy.

注意：[etags](https://www.gnu.org/software/emacs/manual/html_node/emacs/Create-Tags-Table.html#Create-Tags-Table) bundled with Emacs is not supported anymore.

$ ctags --version
ctags (GNU Emacs 27.2)

这个ctags其实就是emacs下的etags，不被`counsel-etags`支持



Q&A专区

Q：查看模式

A: M-x describe-mode



Q: 怎么支持搜索驼峰单词

A: superword-mode



Q：-_这些都是单词的分隔符

A：elpa/evil-20220202.1351/evil.info给出了解释，采用了emacs的单词分词。

(setq evil-symbol-word-search t)可以解决这个问题，同时也可以解决驼峰问题



Q：Blocking call to accept-process-output with quit inhibited!

A：这The example below runs the spell checker on a timer



Q: Company backend ’company-clang’ could not be initialized:
Company found no clang executable

A: M-x customize-option 选择company-backends，去掉company-clang就可以了

自动完成是由`company-mode`提供UI的。自动完成候选项由对应特定命令行工具的`backend`提供，见`company-backends`.
我只用ctags。所以对应的`backend`是`company-ctags`. 它很稳定，如果没有你想要的候选项，可能是因为我把ctags扫描代码生成TAGS文件的时间间隔设置的比你预期的长。`counsel-etags-update-interval`的默认值是300秒。`company-ctags`定期载入的TAGS文件的时间间隔定义在`company-ctags-check-tags-file-interval`，默认是30秒。

这样设置的目的是为了获得最佳编辑体验。减少内存和CPU消耗。对自动完成的实时性做了一点妥协。但我同时用`hippie-expand`和`eacl`两个插件做补充。

如果你使用其他命令行工具特别是LSP系列的自动完成`backend`。实时性很高，语义分析更准确，内存和CPU消耗就差很多了。



Q：

1. company提示框没出来

2. Company: An error occurred in auto-begin

   Wrong type argument: hash-table-p, nil 

A：在.emacs.d目录下有company-statistics-cache.el，删掉后就变得正常了

https://github.com/syl20bnr/spacemacs/issues/7171

猜测，company-statistics-cache.el空文件什么都没有，所以才一直报错的

