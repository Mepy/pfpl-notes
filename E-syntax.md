## 语言E的语法
本文所述语言 E 略有改动, 但不影响理解.

语言 E 有两个类别 Sort $\mathcal{S}=\{ e, \tau \}$ 
其中 $e$ 是表达式 expression , $\tau$ 是类型 type .

下表列出具体语法与抽象语法:
||抽象语法|具体语法|
|:-:|:-:|:-:|
|$e$|
|变量|x|$x$|
|定义|let($e_1; x.e_2$)|let $x = e_1$ in $e_2$|
|自然数|nat[0], nat[1], ...|$0,1,...$|
|布尔真|true|`t`|
|布尔假|false|`f`|
|加法|plus($e_1; e_2$)|$e_1+e_2$|
|否定|not($e$)|$!e$|
|$τ$|||
|自然数|nat|nat|
|布尔|bool|bool|