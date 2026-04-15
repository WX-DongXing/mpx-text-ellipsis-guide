# 常见布局的文本溢出打点最佳实践

## 使用方式

当用户提供的是布局描述而不是现成代码时，优先从以下布局模板中选择最接近的一类，再补齐对应平台的溢出判断与打点方案。

## 模式 1：单行文本布局

### 描述

- 单行文本布局，文本左右 view 可选存在。
- 文本区域占据剩余空间，而不是随着内容无限撑开，文本随长度变化更改最后面 view 的位置，当达到最大宽度时，文本开始省略。

### 结构示意

```text
┌──────────────────────────────┐
│ <view> <text> <view>         │
└──────────────────────────────┘
```

### 实现
在此中布局中, 设置 wrapper 宽度为 100% 给定父层一个明确宽度。如果父层为 flex 布局，则设置文本节点为 `flex-shrink 1` 也为关键，默认 flex item 节点小程序 `flex-shrink 1`，而 RN 为 `flex-shrink 0`，这里需要指定为 1 使得 text 能够在外层 view 下收缩，而使得 text 随文本长度变大撑开最后的 view。

```html
<template>
  <view class="wrapper">
    <view>view</view>
    <text class="text-node" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">这是一个文本节点</text>
    <view>view</view>
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
</style>
```

## 模式 2：主副标题布局

### 描述

- 主副标题布局，使用一个外层 view 纵向承载两行文本，第一行为主标题，第二行为副标题。
- 若副标题存在，则主标题和副标题都按单行超长打点；若副标题不存在，则主标题占用两行并按两行超长打点。

### 结构示意

```text
┌──────────────────────────────┐
│ 主标题主标题主标题              │
│ ---------------------------- │
│ 副标题副标题副标题              │
└──────────────────────────────┘
```

### 实现
在此布局中，设置 wrapper 宽度为 100% 给定父层一个明确边界，并通过 `overflow hidden` 保证内部文本发生真实裁剪。主标题通过副标题是否存在来切换行数：当副标题存在时，主标题单行省略；当副标题不存在时，主标题两行省略。副标题本身单独作为第二个 `text` 节点，在存在时按单行省略即可。

```html
<template>
  <view class="wrapper">
    <text class="title {{subTitle ? 'title-single' : 'title-multi'}}" max-lines@wx="{{subTitle ? 1 : 2}}"
      overflow@wx="ellipsis" numberOfLines@ios|android|harmony="{{subTitle ? 1 : 2}}" >{{ title }}</text>
    <text wx:if="{{subTitle}}" class="sub-title" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">{{ subTitle }}</text>
  </view>
</template>

<style lang="stylus">
.wrapper
  width 100%
  overflow hidden
.title-single,
.sub-title
  font-size 32rpx
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
.title-multi
  font-size 28rpx
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display -webkit-box
  overflow hidden
  text-overflow ellipsis
  white-space normal
  -webkit-box-orient vertical
  -webkit-line-clamp 2
  /* @mpx-endif */
.title
  font-size 32rpx
.sub-title
  margin-top 8rpx
  font-size 24rpx
</style>
```

## 模式 3：image、text 与 text-like 混合布局

### 描述

- 混合布局，左侧可存在 `image`，中间可同时存在普通 `text` 和循环渲染的 `text-like` 节点。
- 整条混排内容作为一个整体文本流省略，而不是让其中某个片段单独承担省略。
- 该模式适用于“图标 + 前缀文本 + 若干文本片段”整体作为一句展示的场景。

### 使用前提

- 目标必须是“整条混排内容统一省略”，而不是某一段独立省略。
- 最外层节点在小程序侧应映射为 `span`，在 RN 侧应映射为 `text`，使内部内容保持同一条文本流。
- 内部 `text`、`image`、`text-like` 节点只承担内容语义，不单独设置省略能力。
- 行数由最外层文本流容器统一控制，可按单行或多行设置。

### 结构示意

```text
┌───────────────────────────────────────────────────┐
│ <image> <rich-text> <rich-text> <rich-text>...    │
└───────────────────────────────────────────────────┘
```

### 前提
需要在 mpx plugin 中对 `text-like` 节点设置 customTextRules 规则，使得 `text-like` 节点外层覆盖一个 `text` 节点满足 text layout 布局。

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

### 实现

在此布局中，内部真正承担省略的是一个统一的文本流容器。该容器在小程序侧映射为 `span`，在 RN 侧映射为 `text`，并由它统一设置行数与省略属性；内部的 `image`、`text`、`text-like` 节点仅作为混排内容存在，不再逐个设置省略。这样可以让整条混排内容作为同一条文本流统一打点，既可用于单行，也可用于多行。

```html
<template>
  <view class="line" mpxTagName@wx="span" mpxTagName@ios|android|harmony="text" numberOfLines@ios|android|harmony="1" max-lines@wx="1" overflow@wx="ellipsis">
    <image class="prefix-image" src="xxx" mode="heightFix" />
    <block wx:for="{{segments}}" wx:key="index">
      <rich-text
        class="text"
        text="{{item.text}}"
      />
    </block>
  </view>
</template>

<style lang="stylus">
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
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display inline-block
  /* @mpx-endif */
</style>
```

### 变体

#### segments 循环嵌套 image + text-like 场景
该场景则只需要将 image 置于循环 block 内即可，不过在 RN 下会出现最后一个 image 依然显示的问题，**不能完美适配 RN 场景**。

#### 单行复杂布局，左边普通单行布局，右边 text-like 混合布局
最外层为 flex 布局 view，其中嵌套模式 1 布局和模式 3 布局即可。
