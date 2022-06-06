## 抽象语法
- 表观语法 surface syntax
- 抽象语法 abstract syntax

### 抽象语法树 Abstract Syntax Tree, AST

可以参考编译原理课本中的那个, 但本章所说的更加抽象一些. 
引入了 **变量 var** 的概念, 这是编译原理中所述之不具有.
熟悉 **λ calculus** 的人, 可以理解为丢掉了 **抽象 abs** 的 **λ calculus**.
所谓 **运算符 operator**, 可以理解成 **λ calculus** 的 **应用 app** 以及其他拓展. 

类别集 $\mathcal{S}$
**类别 Sort** $s\in\mathcal{S}$
**AST** 具有不同类别, 例如:
- 表达式 Expression
- 语句 Statement

运算符集 $\mathcal{O}_s=\{\mathcal{O}_{s,\alpha}\}$
**元数 arity** 为 $(s_1, s_2, ..., s_n)s$ 的运算符 $o\in\mathcal{O}_{s,n}$

变量集 $\mathcal{X}_s$
变量族 $\mathcal{X}=\{\mathcal{X}_s\}_{s\in\mathcal{S}}$
如果 $\exists s\in\mathcal{S}, x\in\mathcal{X}_s$ , 那么称 $x$ 有类别 $s$ .
如果 $\forall s\in\mathcal{S}, x\notin\mathcal{X}_s$ , 那么称 $x$ 对于 $\mathcal{X}$ 而言 **新 fresh** ;
我们可以将 $x$ 加入到 $\mathcal{X}$中, 记作 $\mathcal{X}, x$ ,
其中类别 $s$ 由上下文给定, 因此略去. 


**AST** 具有归纳结构/递归结构, 可以使用 **结构归纳法 structural induction** .
我们需要归纳定义最小 **AST** 族 $\mathcal{A}[\mathcal{X}]:=\{\mathcal{A}[\mathcal{X}]_s\}_{s\in \mathcal{S}}$:
1. 如果 $x\in\mathcal{X}_s$ , 则 $x\in\mathcal{A}[\mathcal{X}]_s$ ;
2. 如果 $o\in\mathcal{O}_{s,n}$ , $a_1, ..., a_n\in\mathcal{A}[\mathcal{X}]_s$ , 则 $o(a_1; ...; a_n)\in\mathcal{A}[\mathcal{X}]_s$ .


变量是抽象的, 仅有被 **代换 substitution** 才有实意.
$[b/x]a$ 表示把 $a$ 中的变量 $x$ 代换成 $b$ .
称 $a$ 为代换 **目标 target**, $x$ 为代换**对象 subject**.
代换递归定义如下:
1. $[b/x]y=\begin{cases}b&,\text{ if } x=y \\ y&,\text{ if } x\not=y\end{cases}$;
2. $[b/x]o(a_1; ...; a_n)=o([b/x]a_1; ...; [b/x]a_n)$.


定理(代换良定义, 代换唯一)
如果 $a\in\mathcal{A}[\mathcal{X}, x]$, 那么 $\forall b\in\mathcal{A}[\mathcal{X}], \exists! c\in\mathcal{A}[\mathcal{X}], s.t. [b/x]a=c$.
证明: 对 $a$ 归纳. 


### 抽象绑定树 Abstract Binding Tree, ABT

在 **AST** 的基础上, 添加了 **抽象 abs** , 即 $x_1, ..., x_n.a$ 的形式.
**let x = a in b** 是一个运算符, 他的作用大抵等同于**应用 app**, 即:
$\text{let}(a; x.b)$ 等同于 $\text{app}(x.b, a)$.

升阶[^zexal]魔法!
变量升阶为 **抽象子 abstractor** , 具有 $x_1, ..., x_n.a$ 的形式, 其中 $n=0$ 的情形可以简写为 $a$ ; 简单起见, 记 $\vec{x}=x_1, ..., x_n$ .
运算符元数升阶为 **泛化元数 generalized arity** , 具有 $(v_1, ..., v_n)$ 的形式, 其中 $v$ 称为 **价 valence** , 具有 $s_1,...,s_n.s$ 的形式.
比如, $\text{let}$ 具有元数 $(e, e.e)e$

由于引入了 **抽象 abs**, 就会涉及到 **作用域 scope**,
有可能出现重名, 我们需要做 **换名 α-reduction** .
(我们不考虑 **De Bruijn Index** )
新的换名双射 $\rho:\vec{x}↔\vec{x}'$ , 其中 $\vec{x}'$ 对于 $\mathcal{X}$ 而言新.
$\hat\rho(a)$ 表示把 $a$ 中的所有变量 $x_i$ 换成 $\rho(x_i)$ 的结果.
换名带来了 **α-等价 α-equivalence** $=_α$.
1. $x=_αx$
2. 如果 $\forall 1\le i\le n, \forall \rho_i:\vec{x}_i↔\vec{z}_i, \rho_i':\vec{x}_i'↔\vec{z}_i', \hat\rho_i(a_i)=_α\rho_i'(a_i')$,
   那么 $o(\vec{x}_1.a_1; ...; \vec{x}_n.a_n)=_αo(\vec{x}_1'.a_1'; ...; \vec{x}_n'.a_n')$ .




**ABT** 族的递归定义
1. 如果 $x\in\mathcal{X}_s$ , 则 $x\in\mathcal{B}[\mathcal{X}]_s$ ;
2. 如果 $o$ 具有泛化元数 $(\vec{s}_1.s_1, ..., \vec{s}_n.s_n)s$, 且 $\forall 1\le i\le n$,
   $a_i\in\mathcal{B}[\mathcal{X, \vec{x}_i}]_{s_i}, \exists \rho_i:\vec{x}_i ↔ \vec{x}_i', s.t.\hat\rho_i(a_i)\in\mathcal{B}[\mathcal{X, \vec{x}_i}]_{s_i}$, 
   则 $o(\vec{x}_1'.a_1; ...; \vec{x}_n'.a_n)\in\mathcal{B}[\mathcal{X}]_s$ .
   
代换 $[b/x]a$ :
1. $[b/x]y=\begin{cases}b&,\text{ if } x=y \\ y&,\text{ if } x\not=y\end{cases}$;
2. $[b/x]o(\vec{x}_1.a_1; ...; \vec{x}_n.a_n)=o(\vec{x}_1.a_1'; ...; \vec{x}_n.a_n')$,
   其中 $a_i'=\begin{cases}a_i&,\text{ if } x\in\vec{x}_i \\ [b/x]a_i&,\text{ if } x\notin\vec{x}_i\end{cases}$

由于存在换名, 代换不能唯一确定,
我们以后会采用**标识约定 identification convention** :
$\mathcal{B}[\mathcal{X}] := \mathcal{B}[\mathcal{X}]/_{=_α}$
在商集和等价类的意味下, 代换结果就是唯一的.

### ~~混沌~~[^zexal]抽象绑定树 ~~Chaos~~ ABT

除了变量, 我们也可为运算符绑定符号.
不过符号没有代换的概念.

符号集 $\mathcal{U}$
符号 $u\in\mathcal{U}$
运算符 $o[u]$
**ABT** 族 $\mathcal{B}[\mathcal{U}, \mathcal{X}]$

[^zexal]: Yu·Gi·Oh ZEXAL.