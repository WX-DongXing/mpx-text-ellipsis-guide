# 基础跨平台示例

## 文本块级元素

### webview && skyline

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

### 跨平台适配

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

## 单一 text 跨平台最佳实践

### 单行

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">这是一个文本节点</text>
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

### 多行

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines@wx="2" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="2">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.text-node
  /* @mpx-if (__mpx_mode__ === 'ios' || __mpx_mode__ === 'android' || __mpx_mode__ === 'harmony') */
  /* @mpx-else */
  display -webkit-box
  -webkit-box-orient vertical
  -webkit-line-clamp 2
  overflow hidden
  white-space normal
  /* @mpx-endif */
</style>
```
