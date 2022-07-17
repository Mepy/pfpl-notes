## 规则

|逻辑|规则|
|:-:|:-:|
|命题逻辑|[基本规则](#基本规则)|
|命题逻辑|[假言规则](#假言规则)|
|高阶逻辑|[泛型规则](#泛型规则)|
|高阶逻辑|[参化规则](#参化规则)|
    

### 基本规则
**主词 subject** $a\in\mathcal{B}[\mathcal{X}]$
**谓词 predicate** $p$ 或说 **判断形式 judgement form** $\mathsf{J}$
**判断** $a$ 具有性质 $p$ : $p$ $a$ 或说 $a$ $\mathsf{J}$
当不强调 $a$ 时, 我们使用 $J$ 来记 $a$ $\mathsf{J}$ .

基本规则形如 $\dfrac{J_1 ... J_n}{J} (r)$ ,
横线上方的 $J_1, ..., J_n$ 称为 **前提 premise** .
横线下方的 $J$ 称为 **结论 conclusion** .
特别地, $n=0$ 时, $r$ 称为 **公理 axiom** ;
否则 $r$ 称为 **真规则 proper rule** .
规则集 $\mathcal{R}$


**归纳定义 inductive definition**
我们使用有限规则的 **归纳闭包 inductive closure** 来构建整个谓词 $\mathsf{J}$ .
举例(自然数):
$$
\tag{2.2a, nat.O} \dfrac{}{\text{zero nat}}
$$
$$
\tag{2.2b, nat.S} \dfrac{a \text{ nat}}{\text{succ}(a)\text{ nat}}
$$
举例(自然数等价):
$$
\tag{2.4a, is.nat.O} \dfrac{}{\text{zero is zero}}
$$
$$
\tag{2.4b, is.nat.S} \dfrac{a\text{ is }b}{\text{succ}(a)\text{ is }\text{succ}(b)}
$$

归纳定义会导出归纳原理, 而 $\text{nat}$ 所导出的就是我们常用的数学归纳法:
要证 $a \text{ nat }⟹P(a)$, 只要证:
1. $P(\text{zero})$
2. $P(a)⟹P(\text{succ}(a))$

$\text{is}$ 导出的归纳原理如下:
要证 $a\text{ is }b⟹P(a, b)$, 只要证:
1. $P(\text{zero},\text{zero})$
2. $P(a,b)⟹P(\text{succ}(a), \text{succ}(b))$

我们把每个规则中的判断换成 $P$ 即可对应归纳原理中的一点.
下面, 我们示例如何使用归纳原理证明命题.
引理 (2.1) 如果 $\text{succ}(a) \text{ nat}$, 那么 $a \text{ nat}$.
证明: 令 $P(a)=a \text{ nat}∧(∃b, a=\text{succ}(b)⟹b\text{ nat})$, 对 $a \text{ nat}$ 归纳:
1. $P(\text{zero})$ 成立, $∧$ 左侧 $\text{zero nat}$ 由 2.2a 得出; 右侧的前件假, 因而真.
2. 现假设 $P(a)$ 成立, 即 $a \text{ nat}∧(...)$,
   由于证明中不需要用到 $P(a)$ 的 $∧$ 右侧, 我们用$(...)$来表示忘记它.
   要证 $P(\text{succ}(a))$ 即 $\text{succ}(a)\text{ nat}∧(∃b, \text{succ}(a)=\text{succ}(b)⟹b\text{ nat})$,
   $∧$ 左侧结合 $a \text{ nat}$ 与 2.2b 可得; 至于 $∧$ 右侧, 取 $b=a$, 又 $a \text{ nat}$, 故有 $b \text{ nat}$.

引理 (2.2) 如果 $a \text{ nat}$, 那么 $a \text{ is } a$.
证明: 令 $P(a) = a \text{ is } a$, 对 $a \text{ nat}$ 归纳即可.
引理 (2.3) 如果 $\text{succ}(a_1) \text{ is } \text{succ}(a_2)$, 那么 $a_1 \text{ is } a_2$.
证明: 归纳即可.



**推导 derivation**
**推导过程** $∇$ 通常以树状形式给出, 即形如
$$
\dfrac{∇_1, ..., ∇_n}{J}
$$
举例(2是自然数)
$$
\dfrac{\dfrac{\text{zero nat}}{\text{succ(zero) nat}}}{\text{succ(succ(zero)) nat}}
$$
推导有两种方向
- **前向 forward** / **自底向上 bottom-up**
- **后向 backward** / **自顶向下 top-down**


**迭代归纳定义 iterated inductive definition** 使用了以前定义的谓词
$$
\tag{2.7a} \dfrac{}{\text{nil list}}
$$
$$
\tag{2.7b} \dfrac{a\text{ nat }b\text{ list}}{\text{con}(a;b) \text{list}}
$$

**联立归纳定义 simultaneous inductive definition** 交错式
$$
\tag{2.8a} \dfrac{}{\text{zero even}}
$$
$$
\tag{2.8b} \dfrac{b\text{ odd}}{\text{succ}(b) \text{ even}}
$$
$$
\tag{2.8c} \dfrac{a\text{ even}}{\text{succ}(a) \text{ odd}}
$$

同样地, 我们有归纳法.
此处省略.


规则(关系)→函数 : 存在性+唯一性
举例(自然数求和)
$$
\tag{2.9a} \dfrac{b\text{ nat}}{\text{sum}(\text{zero}; b; b)}
$$
$$
\tag{2.9b} \dfrac{\text{sum}(a; b; c)}{\text{sum}(\text{succ}(a); b; \text{succ}(c))}
$$
定理(自然数求和是函数)
1. (存在性) 如果 $a \text{ nat}$ 且 $b \text{ nat}$, 则存在 $c \text{ nat}$, 使得 $\text{sum}(a; b; c)$.
   对 $a \text{ nat}$ 归纳.
2. (唯一性) 如果 $\text{sum}(a; b; c)$ 且 $\text{sum}(a; b; c')$, 则 $c \text{ is } c'$.
   对 $\text{sum}(a; b; c)$ 归纳.

### 假言规则
假言判断形式有两种
**可推性 derivability** 稳定.
**可纳性 admissibility** 逻辑蕴含, 即→, 可以视作函数类型.

#### **可推性 derivability**
如果能在 $\mathcal{R}'=\mathcal{R}\cup\{\overline{J_1}, ..., \overline{J_n}\}$ 下推导出 $K$ , 记作 $J_1,...,J_n⊢_\mathcal{R}K$ .
$Γ=J_i,...,J_n$ 有时也称临时公理.
$Γ$ 称为 **假言 hypothesis** 或说 **前件 antecedent**,
$K$ 称为 **结论 conclusion** 或说 **后件 consequnet**.

定理3.1(**稳定性 stability**): 如果 $Γ⊢_{\mathcal{R}}J$ , 那么 $Γ⊢_{\mathcal{R}\cup\mathcal{R}'}J$.
证明: $\forall r\in\mathcal{R}, r\in\mathcal{R}\cup\mathcal{R}'$.

**自反 reflexivity** : $Γ, J⊢_{\mathcal{R}}J$;
证明: 定义显然.
**减弱 weakning** : 如果 $Γ⊢_{\mathcal{R}}J$ , 那么 $Γ, K⊢_{\mathcal{R}}J$ ;
证明: 定义显然.
**传递 transitivity** : 如果 $Γ, K⊢_{\mathcal{R}}J$ 且 $Γ⊢_{\mathcal{R}}K$, 那么 $Γ⊢_{\mathcal{R}}J$
证明: 对 $Γ, K⊢_{\mathcal{R}}J$ 归纳.

#### **可纳性 admissibility**
如果 $⊢_{\mathcal{R}}Γ$ 蕴含 $⊢_{\mathcal{R}}J$, 那么记 $Γ⊨_{\mathcal{R}}J$.
定理3.2(可推必然可纳): 如果 $Γ⊢_{\mathcal{R}}J $, 那么 $Γ⊨_{\mathcal{R}}J$ .
证明: 使用可推的传递性.
反过来不对, 见下例:
$$
\text{succ(zero) even}    ⊨_{(2.8)}\text{zero odd} \\
\text{succ(zero) even}\not⊢_{(2.8)}\text{zero odd} \\
$$

**自反 reflexivity** : $Γ, J⊨_{\mathcal{R}}J$;
**减弱 weakning** : 如果 $Γ⊨_{\mathcal{R}}J$ , 那么 $Γ, K⊨_{\mathcal{R}}J$ ;
**传递 transitivity** : 如果 $Γ, K⊨_{\mathcal{R}}J$ 且 $Γ⊨_{\mathcal{R}}K$, 那么 $Γ⊨_{\mathcal{R}}J$

定理3.3(可纳即蕴含): $Γ⊨_{\mathcal{R}}J$ 相当于逻辑蕴含.

**相对可纳**
如果 $Γ⊨_{\mathcal{R}}J$ , 那么称 $\dfrac{Γ}{J}$ 相对 $\mathcal{R}$ 可纳.
定理(相对可纳可消) 如果 $r$ 相对 $\mathcal{R}$ 可纳, 那么 $⊢_{\mathcal{R, r}}J$ 与 $⊢_{\mathcal{R}}J$ 等价.
证明: 只需证一边, 记 $r=\dfrac{Γ}{J}$ , 由于 $r$ 相对 $\mathcal{R}$ 可纳,
    将推导过程中使用 $r$ 的情形均换成 $Γ⊨_{\mathcal{R}}J$ 的推导过程即可.


#### 假言归纳定义
我们引入 **全局假设 global hypothesis** $Δ$ 以占位.
假言规则形如 $\dfrac{ΔΓ_1⊢J_1...ΔΓ_n⊢J_n}{Δ⊢J}(3.9)$.

实际上, 此处的 $⊢$ 是形式化或者说公理化的,
**形式可推性判断 formal derivability judgement** 
$Δ⊢J$ 由 $Δ$ 与 $J$ 组成, 且满足三条性质:
$$
\tag{3.11a} \dfrac{}{Δ, J⊢J}
$$
$$
\tag{3.11b} \dfrac{Δ⊢J}{Δ, K⊢J}
$$
$$
\tag{3.11c} \dfrac{Δ⊢K\ \ \  Δ, K⊢J}{Δ⊢J}
$$
实际上定义的谓词是 $\mathcal{R}$ 的上述性质闭包.

一般来说, 某些规则 $r\in\mathcal{R}$ 必须有特定全局假设 $Δ$ 才能使用.
如果 $\forall Δ, \mathcal{R}$ 均可以使用, 那么称 $\mathcal{R}$ **一致 uniform**, 
此时省略 $Δ$ , 记为 $\dfrac{Γ_1⊢J_1...Γ_n⊢J_n}{J}(3.10)$;
并且结构规则 $(3.11b), (3.11c)$ 是可纳的, 因而可消去;
至于 $(3.11a)$ , 我们总假定满足.

### 泛型规则
泛型规则对 **变量** 进行泛化.

$Γ⊢_{\mathcal{R}}^{\mathcal{U}; \mathcal{X}}J$ 表示 $J$ 根据 $\mathcal{R}\cupΓ$ 可推出, 且 $J$ 包含 $\mathcal{U}$ 与 $\mathcal{X}$ 上的 ABT.

**泛型可推性 generic derivability** 
如果 $Γ⊢_{\mathcal{R}}^{\mathcal{X}\mathcal{Y}}J$, 那么称 $\mathcal{Y}|Γ⊢_{\mathcal{R}}^{\mathcal{X}}J$, 其中 $\mathcal{X}\cap\mathcal{Y}=\varnothing$.

**增强 peoliferation** : 如果 $\mathcal{Y}|Γ⊢_{\mathcal{R}}^{\mathcal{X}}J$ , 那么 $\mathcal{Y}, y|Γ⊢_{\mathcal{R}}^{\mathcal{X}}J$.
**换名 renaming** 如果 $\mathcal{Y}, y|Γ⊢_{\mathcal{R}}^{\mathcal{X}}J$ , 那么 $\forall y'$ 相对于 $\mathcal{X}, \mathcal{Y}$ 而言新, 都有 $\mathcal{Y}, y'|[y→y']Γ⊢_{\mathcal{R}}^{\mathcal{X}}[y→y']J$
**代换 substitution** 如果 $\mathcal{Y}, y|Γ⊢_{\mathcal{R}}^{\mathcal{X}}J$ 且 $a\in\mathcal{B}[\mathcal{X}\mathcal{Y}]$ , 那么 $\mathcal{Y}|[a/y]Γ⊢_{\mathcal{R}}^{\mathcal{X}}[y/a]J$ .

同样引入 **全局变量集 global variables set** $\mathcal{Z}$.
泛型规则形如 $\dfrac{\mathcal{Z}\mathcal{Y}_1|ΔΓ_1⊢J_1...\mathcal{Z}\mathcal{Y}_n|ΔΓ_n⊢J_n}{\mathcal{Z}|Δ⊢J}(3.12)$.

**形式泛型可推性判断 formal generic derivability judgement**
上述判断 $\mathcal{Y}|Δ⊢J$ 是形式化的, 应满足:
$$
\tag{3.14a} \dfrac{}{\mathcal{Y}|Δ, J⊢J}
$$
$$
\tag{3.14b} \dfrac{\mathcal{Y}|Δ⊢J}{\mathcal{Y}|Δ, K⊢J}
$$
$$
\tag{3.14c} \dfrac{\mathcal{Y}|Δ⊢J}{\mathcal{Y}, y|Δ⊢J}
$$
$$
\tag{3.14d} \dfrac{\mathcal{Y}, y'|[y→y']Δ⊢[y→y']J}{\mathcal{Y}, y|Δ⊢J}
$$
$$
\tag{3.14e} \dfrac{\mathcal{Y}|Δ⊢K\ \ \  \mathcal{Y}|Δ, K⊢J}{\mathcal{Y}|Δ⊢J}
$$
$$
\tag{3.14f} \dfrac{\mathcal{Y}, y|Δ⊢J\ \ \  a\in\mathcal{B}[\mathcal{Y}]}{\mathcal{Y}|[a/y]Δ⊢[a/y]J}
$$

如果 $\forall\mathcal{Z}, \mathcal{R}$ 均适用, 那么称 $\mathcal{R}$ **一致 uniform**.
我们同时假设对 $Δ$ 一致, 记为 $\dfrac{\mathcal{Y}_1|Γ_1⊢J_1...\mathcal{Y}_nΓ_n⊢J_n}{J}(3.13)$.
$(3.14a)$ 类似于 $(3.11a)$ , 我们总假设满足.
$(3.14b), (3.14c), (3,14e)$ 在泛型一致性的情况下可纳.
$(3.14d)$ 是换名相关的规则, 由 **标识约定 identification convention** 确保.
$(3.14f)$ 须在列出归纳定义后，用归纳法验证.

### 参化规则
参化规则对 **符号** 进行泛化.
我猜测 **变量** 与 **符号** 是对偶的.

**参化可推性 parametric derivability** 
如果 $Γ⊢_{\mathcal{R}}^{\mathcal{U}\mathcal{V}; \mathcal{X}\mathcal{Y}}J$, 那么称 $\mathcal{V}∥\mathcal{Y}|Γ⊢_{\mathcal{R}}^{\mathcal{U}; \mathcal{X}}J$,
其中 $\mathcal{U}\cap\mathcal{V}=\varnothing, \mathcal{X}\cap\mathcal{Y}=\varnothing$.

形式化系统, 相关规则, 一致性等是类似的, $\mathcal{V}∥\mathcal{Y}|Δ⊢J$, 懒写.