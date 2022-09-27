## 和类型 Sum Types

### 序幕 Prelude
和类型最易理解的例子恰好是AST, AST的相同类别的不同 **运算符 operator** 可以组成和类型.
和类型可以理解为不交并, 如下列 C 语言代码所示:
```c
struct A_add_B {
    enum { _A, _B } label;
    union {
        A a;
        B b;
    } body;
};
```
不过, 这里只是简单的和, 不涉及[递归类型](./FPC.md)与[(余)归纳类型](./(co)inductive-types.md).

更多例子参考本节末之杂言.

### 语法 Syntax
与积类型类似地, 一般来说, 和类型是某个语言的拓展, 我们仅考虑和类型涉及的语法.

||抽象语法|具体语法|
|:-:|:-:|:-:|
|词项$e$|
|左注入|in\[l]{$τ_1; τ_2$}($e$)|$\text{L}_{τ_1; τ_2} \ e$|
|右注入|in\[r]{$τ_1; τ_2$}($e$)|$\text{R}_{τ_1; τ_2} \ e$|
|分类讨论|case($e;x_1.e_1;x_2.e_2$)|$\text{case } e \text{ with } \begin{cases} \text{L}_{τ_1; τ_2} x_1→ e_1 \\ \text{R}_{τ_1; τ_2} x_2→ e_2 \\\end{cases}$|
|类型$τ$|||
|乘积类型|sum($τ_1;τ_2$)|$τ_1 + τ_2$|

相比 PFPL , 此处少了空和类型 void 与 abort. 在实际编程中, 不会出现空和类型, C语言等中的`void` 实际上是 unit, 因为 void 类型的词项, 即该类型的 **居留元 inhabitant**, 是不存在的, 见 PFPL 11.3.1.

### 动态语义 Dynamic Semantics
首先是值判断:

$$
\tag{10.2b, c}
\dfrac
{[e\text{ val}]}
{\text{L}_{τ_1; τ_2} \ e \text{ val}}
; \
\dfrac
{[e\text{ val}]}
{\text{R}_{τ_1; τ_2} \ e \text{ val}}
$$
关于[ ]部分, 若包含, 则是急迫语义; 若不包括, 则是惰性语义.
这关键是在判断形如 $\text{L}_{τ_1; τ_2} \  1+1$ 的注入(假设为语言 E 拓展和类型)是否为值:惰性语义认为其为值, $1+1$的求值应当留至注入值分类讨论时进行; 急迫语义认为其不是值, 要求立即将其求值为 $\text{L}_{τ_1; τ_2} \ 2$.

接下来是小步语义.
下列两条是急迫语义下对注入值求值:
$$
\tag{11.2c, d}
\left[
\dfrac
{e↦e'}
{\text{L}_{τ_1; τ_2} \ e↦\text{L}_{τ_1; τ_2}\ e'}
\right]
; \
\left[
\dfrac
{e↦e'}
{\text{R}_{τ_1; τ_2} \ e↦\text{R}_{τ_1; τ_2}\ e'}
\right]
$$
下列三条规则处理注入值的分类讨论:
$$
\tag{11.2f}
\dfrac
{e↦e'}
{
\text{case } e \text{ with } 
\begin{cases} 
    \text{L}_{τ_1; τ_2} x_1→ e_1 \\
    \text{R}_{τ_1; τ_2} x_2→ e_2 \\
\end{cases}
↦
\text{case } e' \text{ with } 
\begin{cases} 
    \text{L}_{τ_1; τ_2} x_1→ e_1 \\
    \text{R}_{τ_1; τ_2} x_2→ e_2 \\
\end{cases}
}
$$

$$
\tag{11.2g}
\dfrac
{[e\text{ val}]}
{
\text{case } \text{L}_{τ_1; τ_2} e \text{ with } 
\begin{cases} 
    \text{L}_{τ_1; τ_2} x_1→ e_1 \\
    \text{R}_{τ_1; τ_2} x_2→ e_2 \\
\end{cases}
↦
[e/x_1]e_1
}
$$

$$
\tag{11.2h}
\dfrac
{[e\text{ val}]}
{
\text{case } \text{R}_{τ_1; τ_2} e \text{ with } 
\begin{cases} 
    \text{L}_{τ_1; τ_2} x_1→ e_1 \\
    \text{R}_{τ_1; τ_2} x_2→ e_2 \\
\end{cases}
↦
[e/x_2]e_2
}
$$

### 静态语义 Static Semantics
$$
\tag{11.1b, c}
\dfrac
{Γ⊢e:τ_1}
{Γ⊢\text{L}_{τ_1; τ_2} \ e:τ_1+τ_2}
; \
\dfrac
{Γ⊢e:τ_2}
{Γ⊢\text{R}_{τ_1; τ_2} \ e:τ_1+τ_2}
$$

$$
\tag{11.1d}
\dfrac
{Γ⊢e:τ_1+τ_2 \ \ \ Γ, x_1:τ_1⊢e_1:τ \ \ \ Γ, x_2:τ_2⊢e_2:τ}
{Γ⊢
\left(
\text{case } e \text{ with } 
\begin{cases} 
    \text{L}_{τ_1; τ_2} x_1→ e_1 \\
    \text{R}_{τ_1; τ_2} x_2→ e_2 \\
\end{cases}
\right)
:τ
}
$$

引理 (**保持 Preservation**) : 如果 $e:τ$ 且 $e↦e'$, 那么 $e':τ$.
引理 (**进展 Progress**) : 如果 $e:τ$, 要么 $e \text{ val}$, 要么 $∃e', e↦e'$.

### 杂言 Miscs
1. PFPL 11.2 中所述的有限和是二元和的推广, 其形式上更像AST, 只是缺乏嵌套. 索引的引入使得可读性更高, 这些索引有时候也被称为 **标签 label** .
2. PFPL 11.3.2~4 举了3个例子:
    - 布尔类型可以通过和类型定义, 即 $\text{bool} = \text{unit} + \text{unit}$; 
    - 枚举类型亦然, 以扑克牌四种花色为例, $♢♣♡♠$ 是 **标签 label**,
        花色 $\text{suit} = [♢↪\text{unit}, ♣↪\text{unit}, ♡↪\text{unit}, ♠↪\text{unit}]$;
    - option 类型 $τ\text{ opt} = τ+\text{unit}$, 可以建模空指针问题, 从类型系统的层面检查空指针. 