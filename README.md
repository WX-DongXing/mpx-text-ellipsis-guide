# Mpx Text Ellipsis Guide

用于处理 Mpx 跨端文本省略问题的 skill，覆盖微信小程序 `webview`、`skyline`、支付宝小程序与 RN 场景，聚焦单行省略、多行省略、超长打点、文本边界、收缩条件和平台差异。

## 适用问题

- 现有 Mpx 代码在 webview、skyline、支付宝小程序或 RN 上文本省略不生效
- 文本、标题、副文本等节点需要支持单行或多行跨平台省略
- `text`、`rich-text`、`special-text`、`span`、`image` 混排时需要整体省略或判断文本边界
- 同一套布局在不同平台的 `max-lines`、`numberOfLines`、`overflow`、`text-overflow` 表现不一致

## 包含内容

- [SKILL.md](skills/mpx-text-ellipsis-guide/SKILL.md)：技能入口，包含适用场景、工作流、默认决策和输出约束
- [openai.yaml](skills/mpx-text-ellipsis-guide/agents/openai.yaml)：Codex / OpenAI 技能 UI 元数据
- [platform-overflow-differences.md](skills/mpx-text-ellipsis-guide/references/platform-overflow-differences.md)：平台差异索引
- [layout-overflow-best-practices.md](skills/mpx-text-ellipsis-guide/references/layout-overflow-best-practices.md)：布局模式索引

## 参考文件

```text
references/
├── platform-overflow-differences.md      # 平台差异索引
├── platform-line-count-and-clipping.md   # 行数控制、溢出裁剪、平台属性差异
├── platform-basic-single-line.md         # 单个 text / text-like 的基础跨平台单行实现
├── text-flow-and-span.md                 # span、text-like、整体文本流混排
├── layout-overflow-best-practices.md     # 布局模式索引
├── pattern-flex-single-line.md           # view + text + view 的 flex 单行布局
├── pattern-title-subtitle.md             # 主副标题布局
└── pattern-mixed-text-flow.md            # image / text / text-like 整体文本流布局
```

## 使用说明

可显式使用 `$mpx-text-ellipsis-guide`，也可直接按问题描述触发。

推荐用法：

- `使用 $mpx-text-ellipsis-guide 检查这个 Mpx 组件为什么在 RN 上单行省略不生效`
- `使用 $mpx-text-ellipsis-guide 修复这段标题在 skyline 下不打点的问题，按最小改动处理`
- `使用 $mpx-text-ellipsis-guide 设计一个标题和副文本都支持跨平台省略的布局`
- `使用 $mpx-text-ellipsis-guide 判断这个 rich-text / span / image 混排场景应该整体省略还是局部省略`
- `使用 $mpx-text-ellipsis-guide 对比 webview、skyline、支付宝小程序和 RN 的行数控制与省略能力`

## 隐性使用示例

不显式指定 skill 时，也可以直接描述问题：

- `这个 Mpx 组件在 RN 上单行省略不生效，帮我检查原因`
- `帮我修复这段标题在 skyline 下不打点的问题，尽量最小改动`
- `我需要一个标题和副文本都能跨平台打点的布局`
- `为什么这个 rich-text 放在 span 里整体省略不符合预期`
- `这个列表项里文本把右侧按钮撑开了，帮我处理成可收缩并省略`
- `帮我判断这段代码的文本省略链路有没有问题`

## License

MIT
