# 模式 1：flex 单行文本布局

## 描述

- 父层为 flex 单行布局。
- 文本左右存在可选 `view` 节点。
- 文本区域占据剩余空间，而不是随着内容无限撑开。

## 关键点

- 外层需要明确宽度。
- 若父层是 flex，文本节点在 RN 下需要显式 `flex-shrink 1`，其他环境默认即是如此。
- 省略能力由文本节点承担。

## 示例

```html
<template>
  <view class="wrapper">
    <view class="prefix">prefix</view>
    <text class="text-node" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">这是一个文本节点</text>
    <view class="suffix">suffix</view>
  </view>
</template>

<style lang="stylus">
.wrapper
  display flex
  width 100%
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  flex-shrink 1
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
.prefix,
.suffix
  flex none
</style>
```
