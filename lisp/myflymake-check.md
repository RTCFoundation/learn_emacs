## 语法检查

#### 1.基本思路

使用第三方命令行检查，如luac

通过emacs lisp API将检查结果显示出来

变量：

flymake-allowed-file-name-masks

Flymake-err-line-patterns

#### 2.实现

my-syntax-checker.el

```lisp
(message "hello world")
```

简单执行脚本语言

```shell
emacs -Q -batch -l my-syntax-checker.el
```

```lisp
(let* ((file (nth 0 command-line-args-left))
       (i 0)
       all-content)
      (when (file-exists-p file)
        ;;将文件内容加载到all-content
        (setq all-content (with-temp-buffer
                            (insert-file-contents file)
                            (buffer-string)))
        (setq lines (split-string all-content "\n"))
        (while (< i (length lines))
               (when (string-match " +$" (nth i lines))
                 (message "my-syntax-checker:%s:%s:space at the end of line" file (1+ i))
                 (setq i (1+ i))
                 )
          )
        )
      )
```

#### 3.完善

```lisp
(defun my-flymake-txt-init ()
  (list "emacs"
        (list "-Q"
              "-batch"
              "-l"
              "my-syntax-checker-el"
              buffer-file-name))
  )

(push (list "\\.txt$" 'my-filymake-txt-init) flymake-allowed-file0-name-masks)

(push ("^my-syntax-checker:\\([^:]+\\):\\([0-9]\\):\\(.*\\)$") 1 2 nil 3) flymake-err-line-patterns)
```

## 单词检查

#### 1.基本思路

类似flyspell插件，使用flyspell api插件

GNU Aspell支持驼峰代码

```shell
# aspell-en英语词典
yum install aspell aspell-en
```

aspell使用

```shell
echo "helle wold" |aspell pipe --lang en --run-together
```

#### 2.实现

```lisp
;; use 'flyspell-buffer'
(setq ispell-program-name "aspell")
(setq ispell-extra-args '("--lang=en" "--run-together"))

(defun my-spell-check-start ()
  (interactive)
  (setq flyspell-generic-check-word-predicate nil)
  (flyspell-buffer)
  )
```



实现flyspell-generic-check-word-predicate

```lisp
(defun my-predicate ()
  ;; 获取当前type的font face at point
  ;; 检查string、comment和fucntion name
  (let* ((font-face-at-point (get-text-property (point) 'face)))
        (message "font-face-at-point = %s position = %s" font-face-at-point (point))
        (memq font-face-at-point '(font-lock-string-face
                                   font-lock-comment-face
                                   font-lock-function-name-face))
   )
  )
```

在保存文件时自动调用

```lisp
(add-hook 'after-save-hook 'my-spell-chek-start nil t)
```

#### 3.优化

当文件很大时，spellcheck会很慢

检查window的可见区域

