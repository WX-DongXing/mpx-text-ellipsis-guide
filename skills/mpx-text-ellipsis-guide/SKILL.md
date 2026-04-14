---
name: mpx-text-ellipsis-guide
description: 当用户要求诊断、修复或创建 Mpx 在小程序 webview、skyline 或 RN 上的文本省略问题，且涉及单行省略、多行省略场景时使用。
---

# 跨平台文本溢出打点指引

用于两类任务：

1. 检查并修复现有 Mpx 跨端文本省略问题。
2. 根据布局描述设计可跨端工作的文本省略方案。

## 文本溢出打点原理

文本省略只有在以下四项同时成立时才可靠生效：

1. 明确边界：文本所在盒子必须有真实可计算的宽度。
2. 允许收缩：文本所在盒子不能被内容无限撑开，必要时应具备 `flex-shrink: 1`、`max-width` 等收缩条件。
3. 指定行数：必须明确是单行还是多行省略。
4. 真实裁剪：承担省略的节点或其容器必须具备真实裁剪与省略能力。

## 按需读取的参考

- 平台能力、属性差异、`text-like` 边界问题、单一文本最佳实践：读 [platform-overflow-differences.md](references/platform-overflow-differences.md)
- 常见布局模板与示例代码：读 [layout-overflow-best-practices.md](references/layout-overflow-best-practices.md)

## 行为约束

- 遵循最小化改动原则。
- 不进行与溢出无关的样式调整。
- 不进行较大布局的调整。
- 信息不足或结论存疑时，明确缺失条件和当前假设。

## 工作流

1. 先判断任务类型：修复现有代码，还是设计新布局。
2. 对现有代码先按“文本溢出打点原理”的四项检查省略链路是否成立。
3. 涉及平台差异、属性差异或 `text-like` 节点时，再读取对应参考章节，不要把全部参考内容都搬进回答。
4. 如果省略链路涉及 `span`、`special-text`、`rich-text` 或 `text-like` 节点承担边界职责，先读取 [platform-overflow-differences.md](references/platform-overflow-differences.md) 中“text-like 节点的布局边界”章节，再决定是否需要回退为真实 `view`。
5. 如果是新布局，优先从 [layout-overflow-best-practices.md](references/layout-overflow-best-practices.md) 选择最接近的模式，再按用户指定平台补齐实现。

## 默认决策

- 用户未明确指定时，默认处理第一个 `text` 或 `text-like` 节点。
- 用户未明确指定平台时，默认按全平台适配处理。
- 用户未明确指定行数时，默认按单行省略处理。
- 用户未明确指定节点类型时，默认使用真实 `text`；只有用户明确要求或现有代码已使用时，才采用 `text-like`。

## 输出

- 以用户要求为准。
- 修复现有代码时，优先给最小可应用改动；只有在用户要求完整文件或局部片段会造成歧义时，才输出完整组件代码。
- 设计新布局时，优先给出指定平台或全平台可直接使用的方案。
- 回答中应明确指出关键边界条件、平台假设和省略生效的原因。
