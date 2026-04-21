---
name: mpx-text-ellipsis-guide
description: 当用户要求诊断、修复或创建 Mpx 在微信小程序 webview、skyline、支付宝小程序 或 RN 上的文本省略问题，且涉及单行省略、多行省略场景时使用。
---

# 跨平台文本溢出打点指引

## 适用场景
当用户提到以下任一类问题时使用：

1. 现有 Mpx 跨端文本在微信小程序 webview、skyline、支付宝小程序 或 RN 上出现超长、省略、打点不生效的问题时。
2. 需要创建文本、标题、副文本等文本类节点，并要求支持单行或多行跨平台省略时。

## 文本溢出打点原理

文本省略只有在以下四项同时成立时才可靠生效：

1. 明确边界：文本所在盒子必须有真实可计算的宽度。
2. 允许收缩：文本所在盒子不能被内容无限撑开。
3. 指定行数：必须明确是单行还是多行省略。
4. 真实裁剪：承担省略的节点或其容器必须具备真实裁剪与省略能力。

## 按需读取的参考

- 行数控制、溢出裁剪、平台属性差异：读 [platform-line-count-and-clipping.md](references/platform-line-count-and-clipping.md)
- 基础跨平台最小单行实现：读 [platform-basic-single-line.md](references/platform-basic-single-line.md)
- 布局模式索引：读 [layout-overflow-best-practices.md](references/layout-overflow-best-practices.md)
- `span`、`text-like` 副文本、整体文本流混排：读 [text-flow-and-span.md](references/text-flow-and-span.md)

## 行为约束

- 遵循最小化改动原则。
- 不进行与溢出无关的样式调整。
- 不进行较大布局的调整。
- 信息不足或结论存疑时，明确缺失条件和当前假设。

## Gotchas

- RN 下涉及 flex 布局时，不要默认沿用小程序的收缩行为；文本节点常常需要显式设置 `flex-shrink 1`。
- `span`、`text-like`、`image` 混排时，若目标是整条内容统一省略，优先判断是否应由最外层文本流容器统一承担省略，而不是让内部片段分别省略。

## 工作流

1. 先判断任务类型：修复现有代码，还是设计新布局。
2. 对现有代码先按“文本溢出打点原理”的四项检查省略链路是否成立。
3. 涉及平台差异、属性差异或 `text-like` 节点时，只读取当前问题需要的那一份参考，不要一次性读取全部 references。
4. 如果省略链路涉及 `span`、`special-text`、`rich-text` 或 `text-like` 富文本节点，优先读取 [text-flow-and-span.md](references/text-flow-and-span.md) 判断是否属于“整体文本流、省略挂在最外层 `span`”的混编场景。
5. 如果是新布局，先读取 [layout-overflow-best-practices.md](references/layout-overflow-best-practices.md) 选择最接近的模式，再按需展开对应模式文件，如果不能匹配任何模式则根据用户要求参考最佳实践组合出合理方案。

## 默认决策

- 用户未明确指定时，默认处理第一个 `text` 或 `text-like` 富文本节点。
- 用户未明确指定平台时，默认按全平台适配处理。
- 用户未明确指定行数时，默认按单行省略处理。
- 用户未明确指定节点类型时，默认使用 `text` 节点；只有用户明确要求或现有代码已使用时，才采用 `text-like` 富文本节点。

## 输出

- 以用户要求为准。
- 修复现有代码时，优先给最小可应用改动；只有在用户要求完整文件或局部片段会造成歧义时，才输出完整组件代码。
- 设计新布局时，优先给出指定平台或全平台可直接使用的方案。
- 回答中应明确指出关键边界条件、平台假设和省略生效的原因。
