# 语言EF
## 函数拓展 Function Extension
在语言 E 中, 我们仅有简单的表达式, 缺乏抽象手段. 在本章中, 我们为其拓展函数, 使其成为语言 EF.
不过, 值得一提的是, 在 PFPL 中, 先行介绍了语言 ED, 提供了顶层函数支持, 类似于 C 语言, D应是 **定义 Definition** 的意思. 在熟悉 λ 演算的基础上, 我们直接进入语言 EF, 这里的 F 应是 **函数 Function** 的含义. 语言 EF 像 λ 演算那样提供了高阶函数支持.
另外, 本章不涉及递归函数, 因其与可计算性有关, 从而后置.

## 语法 Syntax
类型上, 我们仍然支持两种基本类型, 即自然数 nat 与布尔 bool, 增加了函数类型;
词项上, 我们删去了 let-in 运算符, 增加了函数与调用;
所有变化显示在下表中, 并且指出 let $x = e_1$ in $e_2$ 等价于 $(λ(x:τ).e_2)\ e_1$.

||抽象语法|具体语法|
|:-:|:-:|:-:|
|词项$e$|
|变量|x|$x$|
|<s style="color:grey"> 定义|<s style="color:grey">let($e_1; x.e_2$)|<s style="color:grey">let $x = e_1$ in $e_2$|
|<b style="color:blue;">函数|lam{$τ$}($x.e$)|$λ(x:τ).e$|
|<b style="color:blue;">调用|app($e_1; e_2$)|$e_1\ e_2$|
|自然数|nat[0], nat[1], ...|$0,1,...$|
|布尔真|true|`t`|
|布尔假|false|`f`|
|加法|plus($e_1; e_2$)|$e_1+e_2$|
|否定|not($e$)|$!e$|
|类型$τ$|||
|自然数|nat|nat|
|布尔|bool|bool|
|<b style="color:blue;">函数|arr($τ_1; τ_2$)|$τ_1→τ_2$|


## 动态语义 Dynamic Semantics
我们采用 **小步的 small-step** 结构化动态语义, 关于其他动态语义, 我们放到最后简要讨论.
### 值 Value
首先定义值，即转换系统的终止状态, 亦即 λ 演算的 **正规形式 normal form**.
第一部分是来自语言 E 的值, 我们简单带过:
$$
\tag{5.3a-c}
\dfrac
{}
{\text{nat[n] val}} 
;\ 
\dfrac
{}
{\text{true val}}
;\ 
\dfrac
{}
{\text{false val}}
$$
第二部分是函数拓展:
$$
\tag{8.5a, 函数是值}
\dfrac
{}
{\text{lam}\{τ\}(x.e) \text{ val}}
$$

### 小步语义 Small-step Semantics
$$
\tag{5.4a-b, PLUS}
\dfrac
{}
{\text{plus(nat[m]; nat[n])}↦\text{nat[m+n]}}
; \\
\dfrac
{e_1↦e_1'}
{\text{plus}(e_1; e_2)↦\text{plus}(e_1'; e_2)}
; \ 
\dfrac
{e_1 \text{val}\ \ e_2↦e_2'}
{\text{plus}(e_1; e_2)↦\text{plus}(e_1; e_2')}
$$

$$
\tag{5.4d-f, NOT}
\dfrac
{}
{\text{not(true)}↦\text{false}}
; \
\dfrac
{}
{\text{not(false)}↦\text{true}}
; \
\dfrac
{e↦e'}
{\text{not}(e)↦\text{not}(e')}
$$
函数拓展的小步语义为:
$$
\tag{8.5b, APP-L}
\dfrac
{e_1↦e_1'}
{\text{app}(e_1; e_2)↦\text{app}(e_1'; e_2)}
$$
$$
\tag{8.5b, APP-R}
\left [
\dfrac
{e_1 \text{val}\ \ e_2↦e_2'}
{\text{app}(e_1; e_2)↦\text{app}(e_1; e_2')}
\right ]
$$
$$
\tag{8.5b, APP-V}
\dfrac
{\left [e_2 \text{ val}\right ]}
{\text{app}(\text{lam}\{τ\}(x.e_1); e_2)↦[e_2/x]\ e_1}
$$

在此, 我们再次强调按名解释与按值解释. 若小步语义包括由[ ]括起部分, 则是按值解释, 或称**按值调用 Call-By-Value, CBV**; 若不包括, 即小步语义不含有规则 APP-R, 以及规则 APP-V 不需要 $e_2$ val 作为前提, 则是按名解释, 或称**按名调用 Call-By-Name, CBN**. 这与 λ 演算中的是一致的.

在这之后, 我们还会看到定义动态语义时使用[ ]分述两种情形, 那时候, 我们会将包括[ ]的称为急迫求值, 不包括[ ]的称为惰性求值. 这是语言求值策略上的分歧, 可以注意到[ ]要求某些 AST 必须是值才能继续进行语义.

### 杂言 Miscs
1. 我们当然可以定义求值语义, 并且证明(按名、按值)求值语义与(按名、按值)小步语义等价. 但那显得过于繁琐, 毕竟我们的证明方法有且仅有(结构)归纳法, 即便是 PFPL  也仅是列出求值语义以及定理, 证明简略带过, 见 8.3. 为保证主线清晰, 我们不再赘述证明细节. 感兴趣的读者可以自行手写证明, 也可以自学定理证明器进行形式化证明, 如 Coq, Agda, 并参见 Software Foundation, Volume I & II. 关于定义等同(等式动态语义), 亦是如此.

2. PFPL 8.4 还讨论了动态作用域与静态作用域的问题. 为方便起见, 我们总考虑静态作用域, 动态作用域类似于添加了一个(全局)变量集合, 这在实现上是极容易理解的, 但静态性质不那么好, 容易带来运行错误, 我们会在后续的章节(如 PFPL 章35)中了解到.

## 静态语义 Static Semantics
### 赋型规则 Typing Rules
$$
\tag{4.1a}
\dfrac
{}
{Γ, x:τ⊢x:τ}
$$
$$
\tag{4.1b,e}
\dfrac
{}
{Γ⊢\text{nat[n]}:\text{nat}}
; \
\dfrac
{Γ⊢e_1:\text{nat} \ \ Γ⊢e_2:\text{nat}}
{Γ⊢\text{plus}(e_1; e_2):\text{nat}}
$$
$$
\tag{4.1c,d,f}
\dfrac
{}
{Γ⊢\text{true}:\text{bool}}
; \
\dfrac
{}
{Γ⊢\text{false}:\text{bool}}
; \
\dfrac
{Γ⊢e:\text{bool}}
{Γ⊢\text{not}(e):\text{bool}}
$$
函数拓展:
$$
\tag{8.4a}
\dfrac
{Γ, x:τ_1⊢e:τ_2}
{Γ⊢\text{lam}\{τ_1\}(x.e):\text{arr}(τ_1; τ_2)}
$$
$$
\tag{8.4b}
\dfrac
{Γ⊢e_1:\text{arr}(τ_2; τ)\ Γ⊢e_2:τ_2}
{Γ⊢\text{app}(e_1; e_2):τ}
$$

### 类型安全 Type Safety
仿照语言 E, 通过归纳法, 我们也能证明如下性质:

引理 (唯一 Uniqueness) : $∀Γ,e, (∃ τ, τ', s.t. Γ⊢e:τ ∧ Γ⊢e:τ'⟹τ=τ')$.
引理 (8.2 **反转 Inversion**) : 
- 如果 $Γ⊢\text{plus}(e_1, e_2):τ$, 那么 $τ=\text{nat}, Γ⊢e_1:\text{nat}, Γ⊢e_1:\text{nat}$;
- 如果 $Γ⊢\text{not}(e):τ$, 那么 $τ=\text{bool}, Γ⊢e:\text{bool}$;
- 如果 $Γ⊢\text{lam}\{τ_1\}(x.e_2):τ$, 那么存在$τ_2$, 使得$τ=\text{arr}(τ_1;τ_2), Γ, x:τ_1⊢e_2:τ_2$;
- 如果 $Γ⊢\text{app}(e_1;e_2):τ$, 那么存在$τ_2$, 使得$Γ⊢e_1:\text{arr}(τ_2;τ), Γ⊢e_2:τ_2$.

引理 (弱化 Weakening) : 如果 $Γ⊢e':τ'$, 那么 $Γ, x:τ⊢e':τ'$.
证明: 对 $Γ⊢e':τ'$ 归纳.
引理 (8.3 **代换 Substitution**) : 如果 $Γ, x:τ⊢e':τ'$, 那么 $Γ⊢[e/x]e':τ'$.
证明: 对 $Γ, x:τ⊢e':τ'$ 归纳.

弱化与代换引理以及 **标识约定 identification convention** 保证了赋型判断是泛型归纳定义的判断.


引理 (8.4 **保持 Preservation**) : 如果 $e:τ$ 且 $e↦e'$, 那么 $e':τ$.
证明: 命题等价于 $e↦e'⟹e:τ⟹e':τ$,
令 $P(e,e')=e:τ⟹e':τ$对 $e↦e'$ 归纳即可.

引理 (8.5 **范式 Canonical Form**) : 如果 $e \text{ val}$ 且 $e:τ$, 那么
- $τ=\text{nat}⟹∃n, e=\text{nat[n]}$;
- $τ=\text{bool}⟹e=\text{true} ∨ e=\text{false}$;
- $τ=τ_1→τ_2⟹∃ x, ∃ e_2, x:τ_1⊢ e_2:τ_2, e=λ(x:τ_1).e_2$.
 
证明: 对 $e \text{ val}$ 与 $e:τ$ 进行归纳.

引理 (8.6 **进展 Progress**) : 如果 $e:τ$, 要么 $e \text{ val}$, 要么 $∃e', e↦e'$.
证明: 对 $e:τ$ 进行归纳.

我们可以证明赋型的**有效性 Soundness**, 正如我们在语言 E 中所做那样.

### 张本 Foreshadowing
此时, 通过归纳法, 我们可以证明语言 EF 的一个极佳性质, 即 **良赋型 well-typed** 的词项经过有限个小步化简之后, 便会停止, 即停机性质. 这正是 Simply Typed λ Calculus, STLC 中的 normalization 性质. 根据计算理论的知识, 我们知道 STLC 不是图灵完备的. 尽管 λ 演算是图灵完备的, 但是缺乏类型检查, 动态语义可能出错或者不停机. 停机的性质非常美妙, 这正是可计算性理论的显现, 我们会在递归函数与可计算性理论中看到更多的讨论. 

与停机或说可终止性相关的, 还有一对概念, 即全函数与部分函数. 可以将全函数理解为任意输入, 总有停机输出的函数, 而部分函数则对部分输入出错或者不停机.