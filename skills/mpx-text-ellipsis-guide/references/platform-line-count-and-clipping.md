# 行数控制与溢出裁剪

## 行数控制差异

### webview

- 单行：对 `text` 设置 `white-space: nowrap;`
- 多行：对 `text` 设置 `display: -webkit-box;`、`-webkit-box-orient: vertical;`、`-webkit-line-clamp`

### skyline

- 对 `text` 添加 `max-lines` 属性。
- `1` 表示单行，其他值表示多行。

### RN

- 对 `text` 添加 `numberOfLines` 属性。
- `1` 表示单行，其他值表示多行。

## 溢出裁剪差异

### webview

- 对 `text` 设置 `overflow: hidden;` 与 `text-overflow: ellipsis;`

### skyline

- 对 `text` 添加 `overflow="ellipsis"` 即可。

### RN

- `ellipsizeMode` 默认为 `tail`，单行或多行省略通常无需额外设置该属性。

## 属性条件编译

- `@wx` 表示小程序（webview、skyline）生效。
- `@ios|android|harmony` 表示 RN 生效。
