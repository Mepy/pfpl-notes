## 语言E
本文所述语言 E 略有改动, 但不影响理解.

语言 E 有两个类别 Sort $\mathcal{S}=\{ e, \tau \}$ 
其中 $e$ 是表达式 expression , $\tau$ 是类型 type .

下表列出具体语法与抽象语法:
||具体语法|抽象语法|
|:-:|:-:|:-:|
|$e$|
|变量|$x$|$x$|
|数零|zero|$0$|
|布尔真|true|`t`|
|布尔假|false|`f`|
|后继|succ($e$)|$+e$|
|否定|not($e$)|$!e$|
|$\tau$|||
|自然数|nat|nat|
|布尔|bool|bool|
