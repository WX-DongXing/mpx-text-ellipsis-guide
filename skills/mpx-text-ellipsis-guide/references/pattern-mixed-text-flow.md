# 模式 3：image、text 与 text-like 混合布局

## 描述

- 左侧可存在 `image`，中间可同时存在普通 `text` 和循环渲染的 `text-like` 节点。
- 整条混排内容作为一个整体文本流省略，而不是让其中某个片段单独承担省略。

## 使用前提

- 目标必须是整条混排内容统一省略。
- 最外层节点在小程序侧映射为 `span`，在 RN 侧映射为 `text`。
- 内部 `text`、`image`、`text-like` 节点只承担内容语义，不单独设置省略能力。
- 行数由最外层文本流容器统一控制，可按单行或多行设置。

## 前提配置

需要在 mpx plugin 中对 `text-like` 富文本节点设置 `customTextRules`，使其满足 text layout 布局，在 RN 下 `text-like` 没有符合预期时给用户以提示。

。

```js
module.exports = defineConfig({
  pluginOptions: {
    mpx: {
      plugin: {
        srcMode: 'wx',
        customTextRules: {
          include: [
            resolve('node_modules/@didi/mpx-ui/src/basic-components/rich-text/rich-text.mpx'),
            resolve('node_modules/@didi/mpx-ui/src/basic-components/special-text/index.mpx'),
          ],
        }
      },
    }
  }
})
```

## 示例

```html
<template>
  <view class="wrapper">
    <view class="line" mpxTagName@wx="span" mpxTagName@ios|android|harmony="text" numberOfLines@ios|android|harmony="1" max-lines@wx="1" overflow@wx="ellipsis">
      <image class="prefix-image" src="xxx" mode="heightFix" />
      <block wx:for="{{segments}}" wx:key="index">
        <rich-text wx:class="{{textClass}}" text="{{item.text}}" verticalAlign@ali="white-space:nowrap;" />
      </block>
    </view>
  </view>
</template>

<script setup lang="ts">
const isSkyline = useSkyline()

const textClass = computed(() => `text ${isSkyline.value ? 'text-skyline' : ''}`)

defineExpose({
  textClass
})
</script>

<style lang="stylus">
.wrapper
  width 100%
.line
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
.prefix-image
  height 32rpx
  display inline-block
.text
  font-size 24rpx
.text-skyline
  display inline-block
</style>
```
