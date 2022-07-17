## 语言E的动态语义
### 简介
> 语言的动态语义描述程序如何执行.

我们把动态语义放在静态语义之前讨论, 是为了让语言更加直观,
首先理解他的句子/ AST /程序是如何执行的.

动态语义有多种定义, 这散布在 PFPL 的第5、7章, 并有以下分类:
- 大步语义 : 求值动态语义、成本动态语义与等式动态语义.
    大步语义可以理解成 AST 中 **运算符 Operator** 的 **消去 Elimination**. bn
- 小步语义 : 结构化动态语义与上下文动态语义
    小步语义指定了每一小步的变换, 通常包括大步语义以及制定求值顺序.

本节按以下顺序进行:
先从直观的求值动态语义引入, 并提出运行时错误这一问题留待静态语义处理;
然后简要讨论两个求值动态的拓展: 成本动态语义与等式动态语义;
之后为了更好地描述小步语义, 我们先描述其元语言, 即转换系统.
基于转换系统, 我们描述结构化动态语义与上下文动态语义;
最后, 我们证明这些动态语义彼此等价, 主要是小步与大步的关联.

注意, 大部分证明仅有四字"归纳即可", 冗长的证明过程示例见转换系统一节.
对归纳法不熟悉的读者, 应当先写出某一归纳定义结构的归纳原理, 如转换系统中示例.

### 求值动态语义
求值动态语义 $e⇓v$ 如下归纳定义, 其中 $v$ 是 $e$ 经过 **求值 evaluation** 后得到的**值 value**:

$$
\tag{7.1a, 自然数}
\dfrac{}
{\text{nat[n]}⇓\text{nat[n]}}
$$
$$
\tag{7.1b, 布尔真}
\dfrac{}
{\text{true}⇓\text{true}}
$$
$$
\tag{7.1c, 布尔假}
\dfrac{}
{\text{false}⇓\text{false}}
$$
上述三个规则表明自然数和布尔值的求值就是本身;

$$
\tag{7.1g, 按名解释}
\dfrac
{[e_1/x]e_2⇓v}
{\text{let}(e_1; x.e_2)⇓v}
$$
$$
\tag{7.1h, 按值解释}
\dfrac
{e_1⇓v_1 \ \ [v_1/x]e_2⇓v}
{\text{let}(e_1; x.e_2)⇓v}
$$

上述两个规则中, $[a/x]b$ 是 AST 的 **代换 Substitution**;
两个规则是 let 运算符的不同求值方式, 按名或按值, 二选一;
差异参见 λ 演算的 **按名调用 Call-By-Name, CBN** 与 **按值调用 Call-By-Value, CBV**;

$$
\tag{7.1d, 自然数加法}
\dfrac
{e_1⇓\text{nat[m]} \ \ e_2⇓\text{nat[n]}}
{\text{plus}(e_1; e_2)⇓\text{nat[m+n]}}
$$
$$
\tag{7.1e, 布尔真否定}
\dfrac
{e⇓\text{true}}
{\text{not}(e)⇓\text{false}}
$$
$$
\tag{7.1f, 布尔假否定}
\dfrac
{e⇓\text{false}}
{\text{not}(e)⇓\text{true}}
$$
上述三个规则定义了 plus 与 not 这两个运算符的求值方式, 即如何**消去 Eliminate**;

需要注意的是, 我们没有定义诸如 $\text{not(nat[0])}, \text{plus(nat[1]; false)}$ 如何求值,
这样的 AST 会导致求值错误, 通过引入零元运算符 error , 可以定义求值错误, 下例:
$$
\tag{7.3a, 自然数否定错误}
\dfrac
{e⇓\text{true}}
{\text{not}(e)⇓\text{error}}
$$
这样的求值错误当然是可以传递的, 如下例:
$$
\tag{7.3b, 错误否定传递}
\dfrac
{e⇓\text{error}}
{\text{not}(e)⇓\text{error}}
$$
其他的运行时错误规则类似, 故省略.

我们当然不希望一段程序有运行时错误, 这不符合语言设计预期;
编译器通过 **类型检查 type checking** 确保无错误, 我们将在[静态语义](./E-static-semantics.md)中看到如何做到.

求值动态语义非常直观, 可以很快写成一个递归函数实现, 下面是 C++ 伪代码:
(注: 适合熟悉命令式编程而不熟悉函数式编程特别是代数数据类型的读者)
```C++
AST eval(AST ast)
{
    switch(ast.operator)
    {
    case NAT: case TRUE: case FALSE: return ast;
    case NOT: 
    {
        auto v = eval(ast.e); // 递归调用
        switch(v.operator)
        { 
        case TRUE   : return new AST::FALSE();
        case FALSE  : return new AST::TRUE ();
        default     : return new AST::ERROR();
        }
    }
    /* ... */
    }
}
```
### 成本动态语义
求值动态语义没有显式指出计算复杂性, 成本动态语义正为此而来.
成本动态语义 $e⇓^kv$ 表示 $e$ 经过 $k$ 步计算得到值 $v$:
$$
\tag{7.4a, 自然数}
\dfrac{}
{\text{nat[n]}⇓^0\text{nat[n]}}
$$
$$
\tag{7.4b, 自然数加法}
\dfrac
{e_1⇓^{k_1}\text{nat[m]} \ \ e_2⇓^{k_2}\text{nat[n]}}
{\text{plus}(e_1; e_2)⇓^{k_1+k_2+1}\text{nat[m+n]}}
$$
其余省略, 留作读者自行练习.
我们不考虑运行时错误的成本.

### 等式动态语义
求值动态语义是偏计算的, 对于更代数一些的表述, 我们有等式动态语义 $e≡e'$.
此外, 求值动态语义是单向的, 而等式动态语义是等价关系, 满足自反对称传递性质:
$$
\tag{5.10a, 自反性}
\dfrac{}
{e≡e}
$$
$$
\tag{5.10b, 对称性}
\dfrac{e≡e'}
{e'≡e}
$$
$$
\tag{5.10c, 传递性}
\dfrac{e≡e' \ \ e'≡e''}
{e≡e''}
$$
其余规则不过是求值动态语义的改写, 即把 $⇓$ 改写成 $≡$.
不过, 等式动态语义不需要引入错误, 他并不是一个函数, 只是一个(等价)关系.

等式动态语义又称 **定义等同 Definitional Equality**, 这较弱.
我们无法在规则 5.10 内证明 **推定等价 Putative Equivalence**:
$\text{plus(nat[m]; nat[n])} ≡ \text{plus(nat[n]; nat[m])}$,
左式定义等同于 $\text{nat[m+n]}$, 右式定义等同于$\text{nat[m+n]}$,
证明二者等同依赖于 $∀m,n,m+n=n+m$ 这一外部定理.
尽管对于任何 $m, n$ 的实际数值, 如 $m=1, n=2$,
我们有$\text{plus(nat[1]; nat[2])} ≡ \text{nat[3]} ≡ \text{plus(nat[2]; nat[1])}$成立.
通常, 这两者之间的差异可以通过扩充得到 **语义等价 Semantic Equivalence** 而抹去.

$$
\tag{扩充}
\dfrac
{m=n}
{\text{nat[m]} ≡ \text{nat[n]}}
$$

### 转换系统
转换系统是一个元语言, 描述系统状态间的转换, 在本书中, 系统是语言, 状态是 AST.
这样的转换是有方向性的, 因而由下列判断共同描述:
1. $s$ state, 判断 $s$ 是转换系统的一个状态.
2. $s$ final, 对于 $s$ state, 判断 $s$ 是一个**终结状态**.
3. $s$ initial, 对于 $s$ state, 判断 $s$ 是一个初始状态.
4. $s↦s'$, 对于 $s$ state, $s'$ state,, 判断 $s$ 可以转换到 $s'$.

方便起见, 我们不再强调 $s$ state, 默认以元变量 $s$ 等表示的均满足.

我们当然希望终结状态是终结的, 这个状态不会再转换为其他状态:
即对于 $s$ final, 不存在 $s'$ 使得 $s↦s'$.
PFPL 中, 将无法再转换的状态称为 **停顿的 stuck**, 并说明终结状态总是停顿的.
通常意义上, 我们说终结状态是 **停顿的 stuck**, 而不是 **卡住的 stuck**, 
因为这是我们设计转换系统时就希望的, 而非出乎意料之外的"卡住".
$s$ **卡住 stuck** 当且仅当 $s$ **停顿 stuck**且 $s$ 非终结.

注 : 我们使用卡住和停顿两个不同的翻译, 是想区分一个语言设计上的差异, 
卡住带有错误意味, 是语言设计所不期望的, 这很快就会被我们看见; 而停顿是中性的.

我们说转换有方向性, 是说想找到一条有向路径 $s_0↦s_1↦s_2↦...↦s_n$.
对形式语言与自动机课程熟悉的人会意识到, 
我们定义有穷自动机、下推自动机与图灵机时, 正是这样定义状态转换的:
转换序列 $s_0, ..., s_n$ 满足 $s_0$ 是初始状态, 且 $∀0≤i≤n-1, s_i↦s_{i+1}$.
转换序列 **最大 maximal** 当且仅当 $s_n$ 停顿.
转换序列 **完备 complete** 当且仅当 $s_n$ 终结.
直接推论:完备序列总是最大的.
反过来是不一定的, 因为 $s_n$ 可能卡住.
判断 $s↓$ 当且仅当存在一个从 $s$ 开始的完备转换序列,
这样的 $s$ 性质非常好, 因为状态转换过程中不会卡住.

一般而言, 我们用 $↦^*$ 表示 $↦$ 的自反传递闭包, 即:
$$
\tag{5.1a, 自反性}
\dfrac
{}
{s↦^*s}
$$
$$
\tag{5.1b, 传递性}
\dfrac
{s↦s' \ \ s'↦^*s''}
{s↦^*s''}
$$
上述是一个归纳定义, 对应的归纳原理是:
要证 $s↦^*s'⇒P(s, s')$ 只要证:
1. $P(s,s)$
2. $s↦s', P(s', s'')⇒P(s, s'')$.

如果我们想强调转换序列的长度, 即 $s_n$ 的下标 $n$, 我们有如下判断 $s↦^ns'$:
(回想成本动态语义, 这刻画了转换系统中状态转换的成本)
$$
\tag{5.2a, 自反性}
\dfrac
{}
{s↦^0s}
$$
$$
\tag{5.2b, 传递性}
\dfrac
{s↦s' \ \ s'↦^ns''}
{s↦^{n+1}s''}
$$

定理(5.1) : $∀s,s',s↦^*s'⟺∃n∈N,s↦^ns'$.
证明: 用归纳法. 仅以一边为例($⇒$), 令$P(s, s')=∃n∈N,s↦^ns'$:
1.  $P(s,s)$ 满足, 取$n=0$, 由规则 5.2a 立有 $s↦^0s$.
2.  现假设 $s↦s', P(s', s'')$, 即 $∃n∈N,s'↦^ns''$,
    取 $n+1$, 由规则 5.2b 有 $s↦^{n+1}s''$, 满足 $P(s, s'')$.

### 结构化动态语义
此动态语义又称 **小步的 small-step**, 他描述的是每一步的动态语义.
之所以称结构化, 是因为这与 AST 的结构有紧密的关系.

语言 $E$ 的状态与初始状态是 AST; 终结状态称为 **封闭值 (closed) value**,
封闭是因为有绑定变量的存在, 即自由变量 $FV=\varnothing$.
$$
\tag{5.3a, 自然数是值}
\dfrac
{}
{\text{nat[n] val}}
$$
$$
\tag{5.3b, 布尔真是值}
\dfrac
{}
{\text{true val}}
$$
$$
\tag{5.3c, 布尔假是值}
\dfrac
{}
{\text{false val}}
$$

而状态转换如下归纳定义:
$$
\tag{5.4a, PLUS-V}
\dfrac
{}
{\text{plus(nat[m]; nat[n])}↦\text{nat[m+n]}}
$$
$$
\tag{5.4b, PLUS-L}
\dfrac
{e_1↦e_1'}
{\text{plus}(e_1; e_2)↦\text{plus}(e_1'; e_2)}
$$
$$
\tag{5.4c, PLUS-R}
\dfrac
{e_1 \text{val}\ \ e_2↦e_2'}
{\text{plus}(e_1; e_2)↦\text{plus}(e_1; e_2')}
$$
$$
\tag{5.4d, NOT-T}
\dfrac
{}
{\text{not(true)}↦\text{false}}
$$
$$
\tag{5.4e, NOT-F}
\dfrac
{}
{\text{not(false)}↦\text{true}}
$$
$$
\tag{5.4f, NOT-E}
\dfrac
{e↦e'}
{\text{not}(e)↦\text{not}(e')}
$$
$$
\tag{5.4g, LET-X}
\dfrac
{[e_1 \text{val}]}
{\text{let}(e_1; x.e_2)↦[e_1/x]e_2}
$$
$$
\tag{5.4h, LET-E}
\left[
\dfrac
{e_1↦e_1'}
{\text{let}(e_1; x.e_2)↦\text{let}(e_1'; x.e_2)}
\right]
$$

其中, 我们可以明显地将规则分为两类:
1. PLUS-V, NOT-T, NOT-F, LET-X 称为 **指令转换 instruction transition**.
    **运算符 Operator** 的参数均是 **值**, 如 plus(nat[n], nat[m]) 中 nat[n], nat[m] 均是值.
    负责 **消去 eliminate** 由运算符 **引入 introduce** 的结构, 即大步语义的部分.
2. 其余, 称为 **搜索转换 search transition**.
    用于确定求值顺序, 即确定优先被求值的 **主要参数 principal arguments**,
    比如 PLUS-L 与 PLUS-R 确定了从左到右的求值顺序.

当 let 这个运算符是按名解释时, LET-E 可以省去, LET-X 的前提也一并省去;
当按值解释时, LET-X 是 **搜索转换 search transition**, 不得省去.
差异参见 λ 演算的 **按名调用 Call-By-Name, CBN** 与 **按值调用 Call-By-Value, CBV**.

下举一例(方便起见, 用表面语法): $\text{let } x = 1+2 \text{ in } x+3↦^*6$
$$
\dfrac
{
    \dfrac{}{1+2↦3}\text{PLUS-V}
}
{
    \text{let } x = 1+2 \text{ in } x+3↦\text{let } x = 3 \text{ in } x+3
}\text{LET-E}
\\
\dfrac
{}
{
    \text{let } x = 3 \text{ in } x+3↦[3/x]x+3=3+3
}\text{LET-X}
\\
\dfrac{}{3+3↦6}\text{PLUS-V}
\\
$$

引理 (5.2, 值终结) 不存在 $e$ 既满足 $e$ val, 又满足 $\exist e', e↦e'$.
引理 (5.3, 确定性) $e↦e'$ 且 $e↦e''$, 则 $e', e''$ **α-等价 α-equivalence**.
注意, **α-等价 α-equivalence** 而非直接相等, 因为 let 运算符引入了绑定变量.
证明: 归纳即可.

### 上下文动态语义
> 一个单子不过是自函子范畴上的幺半群罢了.
> A monad is just a monoid in the endofunctor category.

上下文动态语义很简单, 他是结构化动态语义的一个等价变种:
只需分离 **指令转换 instruction transition** 与 **搜索转换 search transition**.
我们知道, 指令转换才是真正的求值规则, 搜索转换不过是确定顺序.
通过定义 **求值上下文 evaluation context**, 我们确定求值顺序.
此略.

为避免歧义, 记结构化动态语义为 $e↦_se'$, 上下文动态语义为 $e↦_ce'$.
(注:s是结构化 structural 的首字母, c是上下文 context 的首字母)
定理 (5.4, 上下文动态语义等价于结构化动态语义) : $e↦_se'⟺e↦_ce'$.
证明: 归纳即可.

### 汇总
前文我们已得知, 求值动态语义与等式动态语义基本相同, 只是等式是等价关系.
成本动态语义是求值动态语义的简单拓展, 等价性证明类似于转换系统中的那个.
上一节中, 我们也证明了结构化动态语义与上下文动态语义的等价性.
接下来我们只需要讨论小步语义与大步语义的等价性, 即:
引理 (7.1 大步求值) : 如果 $e⇓v$, 那么 $v \text{ val}$.
定理 (7.2 小步大步等价) : $∀e,v$, $e↦^*v⟺e⇓v$, 且$e↦^nv⟺e⇓^nv$.
证明: 归纳即可.