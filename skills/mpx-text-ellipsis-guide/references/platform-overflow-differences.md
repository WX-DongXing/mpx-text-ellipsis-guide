# 跨平台文本溢出能力差异

## 适用范围

用于说明 Mpx 跨端输出到小程序（webview、skyline）、RN 时，文本溢出打点相关能力的不一致点。处理方案应优先基于“是否能形成明确边界、是否能限制行数、是否能拿到真实尺寸”来判断，而不是只依赖单个平台的样式写法。

## 平台枚举

1. 小程序（webview、skyline）
2. RN（ios、android、harmony）

### 平台关注点

- 小程序 webview：通常更接近 CSS 语义，块级、行内块、宽度继承行为较容易对齐。
- 小程序 skyline：部分布局表现与传统 webview 不完全一致，遇到文本宽度、裁剪、测量不稳定时，优先回退到更明确的包裹层方案。
- RN：没有完整 CSS 盒模型语义，`Text`、`View`、`flex` 的配合要单独确认；不要直接照搬 Web 的块级文本心智。

### text-like 节点的布局边界
在微信小程序，尤其 skyline 下，应区分“文本承载节点”和“布局容器节点”。

#### text-like 节点

1. `span` 节点， skyline 下可内嵌 `image` 节点，其始终为 inline 特性，因此不应当承担布局容器职责。
2. `special-text` 和 `rich-text` 节点，是一个在小程序中使用 `span` 包裹，在 RN 中使用 `text` 包裹的副文本节点组件。其文本内容通过 `text` 属性传入；在省略能力、行数控制等文本行为上，可按 `text` 节点理解。

#### span 节点的布局边界问题

- `mpxTagName@wx="span"` 映射出的 `span` 节点，其特性表现上始终为 inline 特性，若存在 `span` 嵌套 `span`、`rich-text`、`special-text` 场景，通常对最外层 `span` 设置为边界容器通常不生效。

排查这类问题时，可优先观察以下信号：

- 某一层是否为 `span` 节点，是否对其尝试声明文本溢出相关能力，诸如 overflow: hidden、text-overflow: ellipsis、white-space: nowrap 等样式或等效样式。
- 其中是否存在 `span`、`rich-text`、`special-text` 节点

处理原则：

- 让承担布局职责的那一层回到真实 `view`，由它负责明确边界、剩余空间分配、收缩和裁剪。
- 将 `max-lines`、`overflow="ellipsis"`、`numberOfLines`、`ellipsizeMode` 等文本省略能力放在内部真实 `text` 或 text-like 节点上。

### 属性条件编译
组件属性添加 `@wx` 表示在小程序（webview、skyline）生效，`@ios|android|harmony` 表示在 RN 下生效。

### 样式条件编译

#### webview && skyline
只对小程序（webview、skyline）生效。

```css
.text {
  /* @mpx-if (!__ISRN__) */
  display: block;
  /* @mpx-endif */
}
```

#### RN
只对 RN（ios、android、harmony）生效。

```css
.text {
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  display: block;
  /* @mpx-endif */
}
```
也可以使用 `__ISRN__` 来适配 RN ios、android、harmony 平台。

```css
.text {
  /* @mpx-if (__ISRN__) */
  display: block;
  /* @mpx-endif */
}
```

#### 多平台适配
以下样式分支分别在对应平台生效。

```css
.text {
  overflow: hidden;
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  display: flex;
  /* @mpx-else */
  display: block;
  text-overflow: ellipsis;
  white-space: nowrap;
  /* @mpx-endif */
}
```

## 文本块级元素差异
接下来将分别根据平台不同，说明文本块级元素的能力差异。

### webview && skyline

#### 单一文本节点
```html
<template>
  <view class="wrapper">
    <text class="text-node">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.text-node
  display block
</style>
```

#### 多文本节点
```html
<template>
  <view class="wrapper">
    <text class="text-node">
      <text>这是一个文本节点</text>
      <text>这是一个文本节点</text>
    </text>
  </view>
</template>

<style lang="stylus">
.text-node
  display block
</style>
```

### RN
在 RN 下 Text 默认具备盒子特性，无需设置其他样式。

### webview && skyline && RN 跨平台适配
适配所有平台

#### 单一文本节点
```html
<template>
  <view class="wrapper">
    <text class="text-node">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  /* @mpx-endif */
</style>
```

#### 多文本节点
```html
<template>
  <view class="wrapper">
    <text class="text-node">
      <text>这是一个文本节点</text>
      <text>这是一个文本节点</text>
    </text>
  </view>
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  /* @mpx-endif */
</style>
```

## 文本设置行数差异
接下来将分别根据平台不同，说明文本设置行数的能力差异。

### webview
根据单行多行分别对 text 设置以下样式。

#### 单行

```css
.text-node {
  white-space: nowrap;
}
```

#### 多行
其中 -webkit-line-clamp 其值便是其指定的最大行数。

```css
.text-node {
  white-space: normal;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}
```

### skyline
对 text 添加 `max-lines` 属性，其值便是其指定的最大行数，设置为 1 则为单行文本，设置为其他值则为多行文本，无需指定其他样式。

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines="1">这是一个文本节点</text>
  </view>
</template>
```

### RN
对 text 添加 `numberOfLines` 属性，其值便是其指定的最大行数，设置为 1 则为单行文本，设置为其他值则为多行文本，无需指定其他样式。

```html
<template>
  <view class="wrapper">
    <text class="text-node" numberOfLines@ios|android|harmony="1">这是一个文本节点</text>
  </view>
</template>
```

## 文本溢出裁剪差异
接下来将分别根据平台不同，说明文本溢出裁剪的能力差异。

### webview
对 text 设置以下样式。

```css
.text-node {
  overflow: hidden;
  text-overflow: ellipsis;
}
```

### skyline
对 text 添加 `overflow` 属性，溢出打点则将其值设置为 ellipsis，无需设置其他样式。

```html
<template>
  <view class="wrapper">
    <text class="text-node" overflow="ellipsis">这是一个文本节点</text>
  </view>
</template>
```

### RN
对 text 添加 `ellipsizeMode` 属性，溢出打点则将其值设置为 tail，无需设置其他样式。
其中 @ios|android|harmony 表示在 RN 下生效。

```html
<template>
  <view class="wrapper">
    <text class="text-node" ellipsizeMode@ios|android|harmony="tail">这是一个文本节点</text>
  </view>
</template>
```

## 跨平台最佳实践
对于单一 text 适配所有平台的最佳实践如下，亦可根据上面内容根据不同平台进行调整。

### 单行文本溢出打点

#### text 节点

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1" ellipsizeMode@ios|android|harmony="tail">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
</style>
```

#### text-like 节点
```html
<template>
  <view class="wrapper">
    <rich-text class="text-node" text="这是一个文本节点" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1" ellipsizeMode@ios|android|harmony="tail" />
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display block
  overflow hidden
  white-space nowrap
  text-overflow ellipsis
  /* @mpx-endif */
</style>
```

### 多行文本溢出打点
以两行为例

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines@wx="2" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="2" ellipsizeMode@ios|android|harmony="tail">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display: -webkit-box
  -webkit-box-orient: vertical
  -webkit-line-clamp: 2
  overflow hidden
  white-space normal
  /* @mpx-endif */
</style>
```
