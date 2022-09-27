## 导入 intro
这是我关于 [PFPL](https://dl.acm.org/doi/book/10.5555/3002812) 的阅读笔记.

我参照阅读中英文书籍, 以[中文](https://item.jd.com/13727350.html)为主, 英文为辅,
但是笔记中不一定使用译本的术语, 这取决于我的口味.

我写得非常随意, 不会解释太多, 
需要深入理解内在逻辑者请自行阅读.

我写 Markdown 惯常使用单个 ```'\n'``` 换行, 即软换行.
请使用合适的渲染器浏览笔记, 本人对排版概不负责.
现有 [html 版本](http://mepy.net/pfpl) 以供预览.

如有疑问, 请提issue.
## 目录
- 元语言 章1~3
  - [语法](./syntax.md) 章1
  - [规则](./rules.md) 章2~3

- 语言E 章4-7
  语言的语义一般可以分成静态语义与动态语义, 本节以语言 E 为例讲述.
  - [语法](./E-syntax.md) 章4
  - [动态语义](./E-dynamic-semantics.md) 章5, 7
    语言的程序如何运行, 程序 **良定义 well-defined**.
  - [静态语义](./E-static-semantics.md) 章4, 6
    语言的类型系统及相关性质, 程序 **良赋型 well-tyed**.
    类型安全 : **良赋型 $⟹$ 良定义**.

- [语言EF](./EF.md) 章8
    语言 E 中无函数抽象, 引入非递归函数.
    注意, 非递归函数显然是全函数.

- 代数类型 章10~11, 14~15
  - [积类型](./product-types.md) 章10
  - [和类型](./sum-types.md) 章11
  - [泛型](./generic-types.md) 章14
  - [(余)归纳类型](./(co)inductive-types.md) 章15

- 可计算性与递归论 章9, 19~20
  以可计算性为依归, 
  任意输入均有输出者称为 **全函数 Total Computable Function, TCF**,
  否则称 **部分可计算函数 Partial Computable Function, PCF**.
  - [系统T](./T.md) 章9
    可计算的递归函数与 Gödel 的不完备定理.
  - [系统PCF](./PCF.md) 章19
    部分可计算函数, 不确定性的魔法.
  - [系统FPC](./FPC.md) 章20
    递归类型.

- $λ$ 立方 章16~18, 拓展
  - [全称类型](./forall-types.md) 章16
    系统 F , 多态类型
  - [存在类型](./exists-types.md) 章17
  - [种类](./kinds.md) 章18
  - [依赖类型](./dependent-types.md) 拓展

- Curry-Howard 对应 章12~13, 拓展
  为了不出现循环论证, 我们必须回到全函数.
  - [构造逻辑](./constructive-logic.md) 章12
  - [经典逻辑](./classical-logic.md) 章13