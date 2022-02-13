## 自定义text object

使用lisp定义text object

```lisp
(evil-define-text-object my-evil-a-textobj (count &optional beg end type)
                         "Select a word."
                         (let* (rlt)
                               (setq rlt (list(line-beginning-position) (line-end-position)))
                               rlt))

(evil-define-text-object my-evil-inner-textobj (count &optional beg end type)
                         "Select inner word."
                         (evil-select-inner-object 'evil-word beg end type count))

(define-key evil-outer-text-objects-map "t" 'my-evil-a-textobj)
(define-key evil-inner-text-objects-map "t" 'my-evil-inner-textobj)
;; press "viw" "vaw"
;; 启动v代表动作，还有d，c
```



demo.js

```javascript
function bye() {
  
}

function hell(var1, var2) {
  let v1 = bye();
}
```

定义一个text object

| 定义 | 含义                |
| ---- | ------------------- |
| vit  | 选中bye()           |
| vat  | 选中let v1 = bye(); |

上面的my-evil-a-textobj已经实现选中行了，下面实现去掉当前行let前面的空格

```lisp
(evil-define-text-object my-evil-a-textobj (count &optional beg end type)
                         "Select a word."
                         (let* (b (e (line-end-position)))
                               (save-excusion
                                (goto-char (line-beginning-position))
                                (setq b (point))
                                (while (and (< b e)
                                            (memq (following-char) '(9 32)))
                                       (forward-char))
                                (setq b (point))
                                )
                               (list b e)))
```

vit实现逻辑：

先选中let v1 = bye();，找到=，取后面的内容。

## 优化vim text object

```javascript
// va" (a ==> at)
// vi" (i ==> inner)
console.log("hello world")
```

使用i代替('和')，使用vii代替vi'和vi"

仿照自定义text object写两个函数



技巧点：

counsel-etags-grep-current-directory ： ,dd

