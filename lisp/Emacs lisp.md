## 变量

### eval-expression

在Eval里输入

(concat "ab" "cd")

(setq a 3)

可以获取语句的返回值

### setq

在scratch中输入(setq c "abc")

使用eval-buffer运行完

调用eval-expression，输入c，可以看到“abc”



C-x C-q将read-only 窗口变成可编辑

erase-buffer就可以清空buffer

## 函数

### 函数定义

```lisp
(defun hello (v)
  (+ v 2)
  )
```



在scratch中，M-x eval-buffer

然后eval-expression Eval输入(hello 3)

得到结果为5

message打印

```lisp
(defun hello (v)
  (message "v=%s" v)
  )
```

### Interactive command

```lisp
(defun bye()
  (interactive)
  (message "bye bye")
  (insert "this is a new line") ;;在当前buffer输出this is a new line
  )
```

## 运算符

+，-，*，/ 这些其实也是函数

```lisp
(defun sum (a b c)
  (+ a b c)
  )
```

^str:表示以str开头的字符串

C-h f describe function

C-h C-f 跳到函数的定义 Find Function

## 数据结构

### list 

a->b->c->list_end 本质也是一个函数

```lisp
(setq my-list (list "a" "b" 1 2))
```

```lisp
;;只能使用常量
(setq my-list '("a" "b" 1))
```

car提取list的第一个元素

```lisp
(car my-list)
```

cdr返回一个除第一个元素外的list

```lisp
(cdr my-list)
```

nth返回第几个元素

```
(nth 0 my-list)
```

listp返回是否是list

### cons

list的特例，只有两个元素

car== cons[0]

cdr== cons[1]

```lisp
(setq my-cons (cons "ab" "cd"))
(message "car=%s cdr=%s" (car my-cons) (cdr my-cons))
```



### array

### hashtable

array和hashtable用的比较少，性能也不太好

## 正则表达式

#### replace-regexp-in-string

过滤不需要的文本

```lisp
(setq str "abc123de")
(setq a (replace-regexp-in-string "[0-9]+" "" str))
```

#### string-match match-string

```lisp
;;提取上面的数字，123
(when (string-match "[a-z]*\\([0-9]*\\)[a-z]*" str)
  (message "num = %s" (match-string 1 str)))
```

## 语句

### if语句

```lisp
(if my-cond (message "true")
    (message "false")
    (message "false"))
```

### when语句

```lisp
(when my-cond
  (message "first")
  (message "second"))
```

### unless语句

相当于when (not my-cond)

```lisp
(unless my-cond
  (message "first")
  (message "second"))
```

### cond语句

类似自带break的switch case语句

```lisp
(cond
  (my-cond1
   (message "cond1"))
  (my-cond2
   (message "cond2")))
```

### while语句

```lisp
(setq i 0)
(while (< i 10)
       (message "i = %s" i))
```

And or

```lisp
(and t (< i 10))
```

```lisp
(or t (< i 10))
```

### dolist语句

```lisp
(setq my-list '("a" "b" 1))
(dolist (elem my-list)
  (message "elem=%s" elem))
```

### let语句

```lisp
(let* ((a 3))
      (message "a=%s" a))
```

### mapcar语句

```lisp
(setq mylist '("a" "b" "c"))
(defun say-hi (elem)
  (format "str=%s" elem))
(message "mapcar = %s" (mapcar 'say-hi mylist))
```

```lisp
(message "mapcar = %s" (mapcar 
                        (lambda (a)
                                (format "str=%s" a))
                        mylist))
```

## lisp与命令行进行交互

```lisp
(setq str (shell-command-to-string "echo hello"))
(message "str=%s" str)
(insert str)
```



https://tldp.org/LDP/GNU-Linux-Tools-Summary/html/book1.htm

GNU Linux Tools Summary

https://tldp.org/LDP/Bash-Beginners-Guide/html/index.html

Bash Guide for beginner

https://tldp.org/LDP/abs/html/index.html

Advanced Bash-Scripting Guide