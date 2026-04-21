# 常见布局的文本溢出打点最佳实践

根据用户描述选择最接近的模式，再读取对应参考：

- flex 单行文本布局：读 [pattern-flex-single-line.md](pattern-flex-single-line.md)
- 主副标题切换单行/多行：读 [pattern-title-subtitle.md](pattern-title-subtitle.md)
- `image`、`text`、`text-like` 作为整体文本流统一省略：读 [pattern-mixed-text-flow.md](pattern-mixed-text-flow.md)

不要一次性读取所有模式，只加载当前布局需要的那一份。
如果任一布局需要支持副文本，需要把普通 `text` 换成 `rich-text`、`special-text` 等副文本节点，并参考 [platform-basic-single-line.md](platform-basic-single-line.md) 中的 `text-like` 富文本实现。


