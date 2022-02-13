## 代码补全

company-mytags.el仿照company-gtags

```lisp
(defun company-gtags (command &optional arg &rest ignored)
  (interactive (list 'interactive))
  (cl-case command
    (interactive (company-begin-backend 'company-gtags))
    (prefix (...))
    (candidates (...))
    )
  )
```

### 一、基本框架

主要使用prefix和candidates

```lisp
(defun company-mytags-candidates (arg)
  (message "arg=%s" arg)
  '("hello"
    "bye"))

(defun company-mytags-prefix ()
  (company-grab-symbol))
```

```lisp
(defun company-mytags (command &optional arg &rest ignored)
  (interactive (list 'interactive))
  (cl-case command
    (interactive (company-begin-backend 'company-mytags))
    (prefix (company-mytags-prefix))
    (candidates (company-mytags-candidates arg))
    )
  )

(setq company-backends '(ompany-mytags))
```

### 二、完善candidates

```lisp
(defun company-mytags-candidates (prefix)
  (let* ((all-item '("hello"
                     "bye"
                     ))
                       rlt);;rlt是一个nil对象
        (dolist (item all-items)
          (when (string-match prefix item)
            (push item rlt)))
        rlt))
```

```lisp
(setq company-mininum-prefix-length 3)
```

### 三、添加fuzzy模糊算法

```lisp
(defun company-mytags-candidates (prefix)
  (let* ((all-item '("hello"
                     "bye"
                     ))
         (i 0)
         (pattern "^")
                       rlt);;rlt是一个nil对象
        (while (< i (length prefix))
               (setq pattern (concat pattern (substring prefix i (1+ i)) ".*"))
               (setq i (1+ i)))
        (dolist (item all-items)
          (when (string-match pattern item)
            (push item rlt)))
        rlt)
  )
```

在vscode上的应用，command+p，查找文件就是fuzzy算法

### 四、扫描项目代码

```lisp
(defun company-mytags-scan-project-root ()
  (let* ((default-directory (locate-dominating-file default-directory ".git"))
         (find-cmd (format "find. -path=\"*/.git\" -prune -o -type f -iname \"*.html\" -print"))
         (output (shell-command-to-string find-cmd))
         (files (split-string output "[\n\r]+"))
         (all-file-content "")
         rlt)
        ;; scan root dir
        (dolist (file files)
          (when (and (not (string= file "") 
                          (file-exists-p file))
                 (setq all-file-content (concat all-file-content
                                (with-temp-buffer
            											(insert-file-contents file)
            											(buffer-string))))))
          )
        (setq rlt (split-string all-content "[^a-zA-Z_-]"))
        )
  )
```

排序

```lisp
(setq rlt (sort (split-string all-content "[^a-zA-Z_-]") string<))
```

去重

```lisp
(setq rlt (delq nil (delete-dups 
                 (sort (split-string all-content "[^a-zA-Z_-]") string<))))
```

```lisp
(defun company-mytags-candidates (prefix)
  (let* ((all-item company-mytags-scan-project-root)
         (i 0)
         (pattern "^")
                       rlt);;rlt是一个nil对象
        (while (< i (length prefix))
               (setq pattern (concat pattern (substring prefix i (1+ i)) ".*"))
               (setq i (1+ i)))
        (dolist (item all-items)
          (when (string-match pattern item)
            (push item rlt)))
        rlt)
  )
```

### 五、利用ctags优化

```shell
find dirname -type f -name \"*.c\" -o -name \"*.h\" -o -name \"*.cpp\" -o -name \"*.hpp\" | etags -R -
```

每隔固定的时间，运行etags，生成tags文件

```lisp
(defun company-mytags-scan-project-root ()
  (let* ((root-dir (locate-dominating-file default-directory "TAGS"))
         (tag-file (concat root-dir "TAGS"))  
         (all-file-content "")
         (regx "^[^\177\001]+\177\\([^177\001]+\\)\001[0-9]+,[0-9]+$")
         (start 0)
         rlt)
        (setq all-file-content (concat all-file-content
                                (with-temp-buffer
            											(insert-file-contents tag-file)
            											(buffer-string))))
        (while (string-match regx all-file-content start)
               (setq tag-name (match-string 1 all-file-content))
               (setq start (+ start (length (match-string 0 all-file-content))))
               (push tag-name rlt))
        (setq rlt (split-string all-content "[^a-zA-Z_-]"))
        )
  )
```

```lisp
(defvar company-mytags-cache nil
  "Cache of tags file")
;;如果变量为nil，读取一下内容，否则使用之前的cache
(defun company-mytags-candidates (prefix)
  (let* (
         ;; fill cache
         (unless company-mytags-cache
           (setq company-mytags-cache (company-mytags-scan-project-root)))
         (i 0)
         (pattern "^")
                       rlt);;rlt是一个nil对象
        ;; matching by prefix
        (while (< i (length prefix))
               (setq pattern (concat pattern (substring prefix i (1+ i)) ".*"))
               (setq i (1+ i)))
        ;;finalize candidates
        (dolist (item company-mytags-cache)
          (when (string-match pattern item)
            (push item rlt)))
        rlt)
  )
```

### 六、实时载入tags文件

```lisp
(defun company-mytags-update-p (tag-file)
  (let* (rlt 
         old-modification-time
         file-modification-time)
        (cond
          ((not company-mytags-cache)
           (setq rlt t))
          (t
           (setq old-modification-time (plist-get company-mytags-cache
                                                   'modification-time))
           (setq file-modification-time
                 (float-time (nth 5 (file-attributes tags-file))))
           (when (> (- file-modification-time old-modification-time) 4)
             (setq rlt t)))
          rlt)
  )

(defun company-mytags-scan-project-root ()
  (let* ((root-dir (locate-dominating-file default-directory "TAGS"))
         (tag-file (concat root-dir "TAGS"))  
         (all-file-content "")
         (regx "^[^\177\001]+\177\\([^177\001]+\\)\001[0-9]+,[0-9]+$")
         (start 0)
         rlt)
        (setq all-file-content (concat all-file-content
                                (with-temp-buffer
            											(insert-file-contents tag-file)
            											(buffer-string))))
        (when (company-mytags-update-p tag-file)
          (plist-put company-mytags-cache
                     'modification-time
                     (float-time (nth 5 (file-atrributes tags-file))))
          ;; extrace tag name
          (while (string-match regx all-file-content start)
               (setq tag-name (match-string 1 all-file-content))
               (setq start (+ start (length (match-string 0 all-file-content))))
               (push tag-name rlt))
          )
        (setq rlt (split-string all-content "[^a-zA-Z_-]"))
        ;; update cache
        (plist-put company-mytags-cache
                   'candidates 
                   rlt)
        )
  )
```

```lisp
(defvar company-mytags-cache nil
  "Cache of tags file")
;;如果变量为nil，读取一下内容，否则使用之前的cache
(defun company-mytags-candidates (prefix)
  (let* (
         ;; fill cache
         (unless company-mytags-cache
           (setq company-mytags-cache (company-mytags-scan-project-root)))
         (i 0)
         (pattern "^")
                       rlt);;rlt是一个nil对象
        ;; matching by prefix
        (while (< i (length prefix))
               (setq pattern (concat pattern (substring prefix i (1+ i)) ".*"))
               (setq i (1+ i)))
        ;;finalize candidates
        (dolist (item (plist-get company-mytags-cache 'candidates))
          (when (string-match pattern item)
            (push item rlt)))
        rlt)
  )
```

大项目性能优化

之前已经有tags文件，而项目比较大，tags正在生成，这时不应该载入

实现逻辑：比较文件大小



加快速度的方法：

不要直接丢弃，使用diff

直接把+的内容放到company-mytags-cache的candidates



### 技巧点：

比较好用的插件函数：

counsel-esh-history (M-n)

counsel-browse-kill-ring (, y y)

## 代码导航

### 一、基本思路

```lisp
(defun my-codenav()
  (interactive)
  (message "my-codenav called"))
```

找到tags文件，并设置根目录

```lisp
(defun my-codenav()
  (interactive)
  ;;查找tags文件
  (let* ((root-dir (locate-dominating-file default-directory "TAGS"))
         (tags-file (concat root-dir "TAGS"))
         (default-directory root-dir)))
  
  (message "my-codenav called"))
```

读取tags-file内容

```lisp
(setq all-content (with-temp-buffer
                    (insert-file-contents tags-file)
                    (buffer-string))
```

先搜到函数名，然后反向找到文件名和行号

```lisp
(with-temp-buffer
  ;;把内容读到temp-buffer里
  (insert all-content)
  (goto-char (point-min))
  (search-forward "bye1")
  (message "file = %s" (etags-file-of-tag t))
  )
```

### 二、完善逻辑

#### 2.1 读取当前光标下的单词

```lisp
(defun my-codenav()
  (interactive)
  ;;查找tags文件
  (let* ((root-dir (locate-dominating-file default-directory "TAGS"))
         (tags-file (concat root-dir "TAGS"))
         (symbol (thing-at-point 'symbol))
         all-content
         (default-directory root-dir)
         (setq all-content (with-temp-buffer
                    (insert-file-contents tags-file)
                    (buffer-string)))
         (when (and symbol
                    (not (string= "")))
                    (with-temp-buffer
                          ;;把内容读到temp-buffer里
                          (insert all-content)
                          (goto-char (point-min))
                          (search-forward symbol)
                          (message "file = %s" (etags-file-of-tag t))
                        ) )))
  (message "my-codenav called"))
```

```lisp
;;解析行号
(with-temp-buffer
  ;;把内容读到temp-buffer里
  (insert all-content)
  (goto-char (point-min))
  (search-forward symbol)
  (setq car-line (buffer-substring (line-begining-position) (line-end-position)))
  ;;对car-line使用正则表达式处理，取出code line和行号
  )
```

#### 2.2 使用循环，将所有的tags都找到

```lisp
;; 定义一个candidates，保存搜索结果
(with-temp-buffer
  ;;把内容读到temp-buffer里
  (insert all-content)
  (goto-char (point-min))
  ;; 这里添加循环
  (while (search-forward symbol (point-max) t)
         ;;添加之前的查找代码
         (setq car-line (buffer-substring (line-begining-position) (line-end-position)))
        ;;对car-line使用正则表达式处理，取出code line和行号
         (push (list code-line line-num file-name) candidates)
         (forward-line))
  )
;; select from candidates
;; 使用ivy-read
```

#### 2.3 跳转回去

使用xref，emacs官方的导航插件

```lisp
;; 导航前保存当前的位置
(xref-push-marker-stack (point-marker)
```

然后在导航后的位置，使用M-x

```lisp
pop-tag-marker
```



跳转到导航位置，显示瞬间高亮效果

```
xref-pulse-momentarily
```

待完善的点：

1.动态载入tags

2.如果没有candidates，使用search-file，读取文件的内容