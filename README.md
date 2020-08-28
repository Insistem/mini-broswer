# 项目介绍

### html解析

解析请求回来的html代码
字节流-->状态机-->词token-->栈-->DOM树

浏览器
url--(http)->html--(parse)->dom--(css computing)->dom with css --(layout)-->dom with position--(render)-->bitmap

HTML Parse
第一步
- parser接受HTML文本作为参数，返回一颗DOM树
第二步——创建状态机
用FSM来实现HTML的分析
- 在HTML标准中，已经规定了HTML的状态
- mini-Browser只挑选其中一部分状态，完成一个最简版本
第三步——解析标签
主要的标签有：开始标签，结束标签和自封闭标签
- 在这一步我们暂时忽略属性
第四步——创建元素
- 在状态机中，除了状态迁移，我们还会要加入业务逻辑
- 我们在标签结束状态提交标签token
第五步——处理属性
- 属性值分为单引号、双引号、无引号三种写法，因此需要较多状态处理
- 处理属性的方式跟标签类似
- 属性结束时，我们把属性加到标签Token上
第六步——构建DOM树
- 从标签构建DOM树的基本技巧是使用栈
- 遇到开始标签时创建元素并入栈，遇到结束标签时出栈
- 自封闭节点可视为入栈后立刻出栈
- 任何元素的父元素是它入栈前的栈顶
第七步——文本节点
- 文本节点与自封闭标签处理类似
- 多个文本节点需要合并

### css computing

第一步 收集CSS规则
- 遇到style标签时，我们把CSS规则保存起来
- 这里我们调用CSS Parser来分析CSS规则
- 这里我们必须要仔细研究此库分析CSS规则的格式
第二步 添加调用
- 当我们创建一个元素后，立即计算CSS
- 理论上，当我们分析一个元素时，所有CSS规则已经收集完毕
- 在真实浏览器中，可能遇到写在body的style标签，需要重新
CSS计算的情况，这里我们忽略
第三步 获取父元素序列
- 在computeCSS函数中，我们必须知道元素的所有父元素才能判断元素与规则是否匹配
- 我们从上一步骤的stack，可以获取本元素所有的父元素
- 因为我们首先获取的是“当前元素”，所以我们获得和计算父元素匹配的顺序是从内向外
第四步 拆分选择器
- 选择器也要从当前元素向外排列
- 复杂选择器拆成针对单个元素的选择器，用循环匹配父元素队列
第五步 计算选择器与元素匹配
- 根据选择器的类型和元素属性，计算是否与当前元素匹配
- 这里仅仅实现了三种基本选择器，实际的浏览器中要处理复合选择器
第六步 生成computed属性
- 一旦选择匹配，就应用选择器到元素上，形成computedStyle
第七步 确定规则覆盖关系
- CSS规则根据specificity和后来优先规则覆盖
- specificity是个四元组，越左边权重越高
- 一个CSS规则的specificity根据包含的简单选择器相加而成

### 待实现

- 根据CSSOM构建layoutTree
- 绘制到浏览器中