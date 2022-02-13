## Hook

A hook is a Lisp variable which holds a list of functions

简单概念

保存代码 调用 after-save-hook的函数

```lisp
(defun after-save-hook-setup ()
  (message "buffer-file-name = %s" buffer-file-name)
  (message "after-save-hook-setup called"))
(add-hook 'after-save-hook 'after-save-hook-setup)
```



主插件模式 major-mode-hook

在bye.js缩进两个空格，在hello.js中缩进4个空格，怎么实现

```lisp
(defun js2-mode-hook-setup ()
  (cond
    ((string-match "hello.js" buffer-file-name)
     (setq-local js-indent-level 4)
     )
    ((string-match "bye.js" buffer-file-name)
     (setq-local js-indent-level 2))))

(add-hook 'js2-mode-hook 'js2-mode-hook-setup)
```

## Advice

举例：

改变find-file，有点像注入病毒

```lisp
(defun my-find-file-hack (orig-func &rest args)
  (message "args=%s" args)
  (apply orig-func args)
  )

(advice-add 'find-file :around #'my-find-file-hack)
```

做点有意义的事，每打开一个文件，将其文件名放到kill-ring中

```lisp
(defun my-find-file-hack (orig-func &rest args)
  ;;每打开一个文件，将其文件名放到kill-ring中
  (let* ((file (nth 0 args)))
        (when (and file (file-exists-p file))
          (message "file = %s" file)
          (kill-new file)))
  (apply orig-func args)
  )
```

