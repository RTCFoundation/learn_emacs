## 查看版本

```
emacs --version
```

MacBook修改 item2 teminal支持ESC+ ，这样终端的Option键就是Meta键了



## 如此简单的 Emacs：Meta键

Emacs的简单，一言以蔽之，只是一个Meta键而已。 Meta键(键盘上的option/alter键) Meta的词源含义是higher，beyond,没有最高,只有更高。 中文译为”元”，发端处，源头处。因此Meta是 Source，Meta关联Source-Code。而在Source-Code中，Function又是的一等公民。 Emacs的简单策略便是将Meta键绑定到Function这项source-code上，即触发按键M-x (x for execucte) 调用函数。在此之后，便可以天马行空的查询要做的事情, 比如插入当前的日期:



### Emacs的首要策略: 引入Ctrl键

当从目录中打开一个文件，可以M-x find-file,

这项操作需要键入11个字符 Ctrl策略. 倘若按键 C-x C-f. 只需要键入4个字符。

于是作为Emacs实现高效的核心策略，用按键的“字符调用函数”取代“函数名调用”。



字符f则是函数forward-character的首字母。

以上用Control调用functions的方式，称之为Command。Command=Contrl，由此也能反过来看到选择Ctrl键也是语义绑定。





