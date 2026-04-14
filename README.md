# mpx-text-ellipsis-guide

用于处理 Mpx 跨端文本省略问题的 skill，覆盖小程序 `webview`、`skyline` 与 RN 场景，聚焦单行省略、多行省略、布局边界和平台差异。

## 包含内容

- [SKILL.md](skills/mpx-text-ellipsis-guide/SKILL.md)：技能入口，包含触发条件、工作流、默认决策和输出约束
- [platform-overflow-differences.md](skills/mpx-text-ellipsis-guide/references/platform-overflow-differences.md)：平台能力差异、`text-like` 边界问题、跨平台基础写法
- [layout-overflow-best-practices.md](skills/mpx-text-ellipsis-guide/references/layout-overflow-best-practices.md)：常见布局模板与示例代码
- [openai.yaml](skills/mpx-text-ellipsis-guide/agents/openai.yaml)：Codex / OpenAI 技能 UI 元数据

## 适用问题

- 现有 Mpx 代码在 `webview`、`skyline` 或 RN 上文本省略不生效
- 同一套布局在不同平台的 `text-overflow`、`max-lines`、`numberOfLines`、`ellipsizeMode` 表现不一致
- 需要根据布局描述设计一套可跨端工作的文本省略方案
- 需要排查 `span`、`special-text`、`rich-text` 等 `text-like` 节点引起的边界问题

## 目录结构

```text
skills/mpx-text-ellipsis-guide/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── layout-overflow-best-practices.md
    └── platform-overflow-differences.md
```

## 使用说明

可显式使用 `$mpx-text-ellipsis-guide`，也可直接按问题描述触发。推荐用法：

- 检查现有代码：
  `使用 $mpx-text-ellipsis-guide 检查这个 Mpx 组件为什么在 RN 上单行省略不生效`
- 修复现有问题：
  `使用 $mpx-text-ellipsis-guide 修复这段列表标题在 skyline 下不打点的问题，按最小改动处理`
- 创建新布局：
  `使用 $mpx-text-ellipsis-guide 设计一个左图标右文本的跨端单行省略布局，要求兼容 webview、skyline 和 RN`
- 排查平台差异：
  `使用 $mpx-text-ellipsis-guide 对比 webview、skyline 和 RN 在 max-lines、numberOfLines、ellipsizeMode 上的处理方式`
- 排查 text-like 边界问题：
  `使用 $mpx-text-ellipsis-guide 检查这个 rich-text 外层为什么不能作为省略边界`

## 隐性使用说明

不显式指定 skill 时，也可以直接描述问题。下面这些表述通常会自然命中这个 skill：

- `这个 Mpx 组件在 RN 上单行省略不生效，帮我检查原因`
- `帮我修复这段标题在 skyline 下不打点的问题，尽量最小改动`
- `给我设计一个兼容 webview、skyline 和 RN 的多行文本省略布局`
- `为什么这段 rich-text 放在外层后，省略边界不生效`
- `对比一下 Mpx 在 webview、skyline 和 RN 上的文本省略差异`
- `这个列表项里文本把右侧按钮撑开了，帮我处理成可收缩并省略`
- `帮我判断这段代码的文本省略链路有没有问题`
- `我需要一个左侧图标、右侧标题单行省略的跨端方案`

## License

MIT
