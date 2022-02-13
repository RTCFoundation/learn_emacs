## 整体步骤

1. 定义一个my-find-file方法，放在my-find-file.el文件中

2. 将my-find-file方法放到custom.el中
3. 使用M-x就可以调用定义的方法了

## 定义my-find-file

```lisp
(defun my-find-file ()
  (interactive)
  (message "hello world"))
```

### 添加局部变量

```lisp
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name \*.\*"))
        (message "command = %s" cmd)
        ))
```

### 添加shell cmd执行

```lisp
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name \*.\*")
        (output (shell-command-to-string cmd))
        (message "cmd = %s" cmd)
        (message "output = %s" output)
        ))
```

### 按行拼接成list

```lisp
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name \*.\*")
        (output (shell-command-to-string cmd))
        (lines (split-string output "[\n\r]+")))
         
        (message "cmd = %s" cmd)
        (message "output = %s" output)
        (message "lines = %s" lines)
        ))
```

### 提示用户选择某行

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name \*.\*")
        (output (shell-command-to-string cmd))
        (lines (split-string output "[\n\r]+")))
         
        (message "cmd = %s" cmd)
        (message "output = %s" output)
        (message "lines = %s" lines)
        
        (ivy-read "Find file: "lines)
        ))
```

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name \*.\*")
        (output (shell-command-to-string cmd))
        (lines (split-string output "[\n\r]+"))
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read "Find file: "lines))
        (message "selected-line = %s" selected-line)
        ))
```

### 打开选中的文件

M-x ^ + space + test：表示查找所有包含test的命令（若没有space键，查找所有开头为test的命令）

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -name *.*")
        (output (shell-command-to-string cmd))
        (lines (split-string output "[\n\r]+"))
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read "Find file: "lines))
        (message "selected-line = %s" selected-line)
        (find-file selected-line)
        ))
```

### 加条件判断

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . type f -name \*.\*")
        (output (shell-command-to-string cmd))
        (lines (split-string output "[\n\r]+"))
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read "Find file: "lines))
        (message "selected-line = %s" selected-line)
        (when (and selected-line (file-exists-p selected-line))
          (find-file selected-line))
        ))
```

## 扩展my-find-file命令

过滤掉.git等隐藏文件

```lisp
find . -path "*/.git" -prune -o -name \*.\*
```

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -path "*/.git" type f -prune -o -name \*.\*")
        (output (shell-command-to-string cmd))
        (lines (cdr (split-string output "[\n\r]+"));;去掉第一个元素.
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read "Find file: "lines))
        (message "selected-line = %s" selected-line)
        (when (and selected-line (file-exists-p selected-line))
          (find-file selected-line))
        ))
```

## 在项目根目录中搜索

default-directory表示文件当前所在的目录

注意：每个文件都有一个default-directory

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -path "*/.git" type f -prune -o -name \*.\*")
         (default-directory "~/project/linux-master")
        (output (shell-command-to-string cmd))
        (lines (cdr (split-string output "[\n\r]+"));;去掉第一个元素.
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read (format "Find file in %s : " default_directory) lines))
        (message "selected-line = %s" selected-line)
        (when (and selected-line (file-exists-p selected-line))
          (find-file selected-line))
        ))
```

Locate-dominating-file:从当前文件/目录向上定位到项目根目录

```lisp
(locate-dominating-file default-directory ".git")
```

```lisp
(require 'ivy)
(defun my-find-file ()
  (interactive)
  (let* ((cmd "find . -path "*/.git" type f -prune -o -name \*.\*")
         (default-directory (locate-dominating-file default-directory ".git"))
        (output (shell-command-to-string cmd))
        (lines (cdr (split-string output "[\n\r]+"));;去掉第一个元素.
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read (format "Find file in %s : " default_directory) lines))
        (message "selected-line = %s" selected-line)
        (when (and selected-line (file-exists-p selected-line))
          (find-file selected-line))
        ))
```

## 工程化

```lisp
(require 'ivy)
(defun my-find-file-internal (directory)
  "Find file in DIRECTORY."
  (let* ((cmd "find . -path "*/.git" type f -prune -o -name \*.\*")
         (default-directory directory)
        (output (shell-command-to-string cmd))
        (lines (cdr (split-string output "[\n\r]+"));;去掉第一个元素.
         selected-line);;seleted-line刚开始为nil
        
        (setq selected-line (ivy-read (format "Find file in %s : " default_directory) lines))
        (message "selected-line = %s" selected-line)
        (when (and selected-line (file-exists-p selected-line))
          (find-file selected-line))
        ))
```

```lisp
(defun my-find-file-in-project ()
  "Find File in root project."
  (interactive)
  (my-find-file-internal (locate-dominating-file default-directory ".git"))
  )
```

```lisp
(defun my-find-file ()
  "Find File in current directory."
  (interactive)
  (my-find-file-internal default_directory)
  )
```

### 提取my-find-file

```lisp
(defun my-find-file (&optional level)
  "Find File in current directory or level parent directory."
  (interactive "p");将参数转换成数字
  (unless level (setq level 0))
  
  ;;循环找父目录
  (let* ((parent-directory default-directory)
         (i 0))
        (while (< i level)
               (setq parent-directory
                     (file-name-directory (directory-file-name parent-directory)))
               (setq i (+ i 1)))
  
  (my-find-file-internal parent-directory)
  )
```

### 优化my-find-file

使用find实现，这里利用find的过滤功能

1. 使用name中过滤

2. 大小写不敏感

```lisp
find . -path "*/.git" type f -prune -o -iname "*test*" -print
```

修改lisp代码

```lisp
(require 'ivy)
(defun my-find-file-internal (directory)
  "Find file in DIRECTORY."
  (let* ((keyword (read-string "please input keyword")))
        (when (and keyword (not (string= "")))
          (let* ((cmd (format"find . -path "*/.git" type f -prune -o -name \*%s\* -print" keyword))
           (default-directory directory)
          (output (shell-command-to-string cmd))
          (lines (cdr (split-string output "[\n\r]+"));;去掉第一个元素.
           selected-line);;seleted-line刚开始为nil

          (setq selected-line (ivy-read (format "Find file in %s : " default_directory) lines))
          (message "selected-line = %s" selected-line)
          (when (and selected-line (file-exists-p selected-line))
            (find-file selected-line))
          ))
        )
  
```

## 增加搜索文件文本

关键字变成文件内容了，而不是文件名字，本质使用grep搜索

| 选项        | 含义              |
| ----------- | ----------------- |
| s           | 不要error-message |
| r           | 递归              |
| n           | 打印行号          |
| exclude-dir | 忽略目录，如.git  |

## 测试时间工具

```shell
time grep --exclude-dir=".git" -rsn"hello" * >/dev/null
```

vmtouch 加速工具

将当前目录载入到内存

```shell
vmtouch -t .
```

再测试发现速度明显提升