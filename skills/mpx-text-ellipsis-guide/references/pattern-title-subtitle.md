# 模式 2：主副标题布局

## 描述

- 使用一个外层 `view` 纵向承载两行文本，第一行为主标题，第二行为副标题。
- 若副标题存在，则主标题和副标题都按单行省略。
- 若副标题不存在，则主标题占用两行并按两行省略。

## 关键点

- 外层需要明确宽度并裁剪溢出。
- 主标题按副标题是否存在切换行数。
- 副标题单独作为第二个 `text` 节点处理。
- 如果需要支持副文本，则可直接将 `text` 替换为 `rich-text` 等富文本节点即可。

## 示例

```html
<template>
  <view class="wrapper">
    <text class="title {{subTitle ? 'title-single' : 'title-multi'}}" max-lines@wx="{{subTitle ? 1 : 2}}" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="{{subTitle ? 1 : 2}}">{{ title }}</text>
    <text wx:if="{{subTitle}}" class="sub-title" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">{{ subTitle }}</text>
  </view>
</template>

<style lang="stylus">
.wrapper
  width 100%
  overflow hidden
.title-single,
.sub-title
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
.title-multi
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display -webkit-box
  overflow hidden
  text-overflow ellipsis
  white-space normal
  -webkit-box-orient vertical
  -webkit-line-clamp 2
  /* @mpx-endif */
</style>
```
