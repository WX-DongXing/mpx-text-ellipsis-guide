# 基础跨平台最小单行实现

## 单一 text 节点

### 单行

```html
<template>
  <view class="wrapper">
    <text class="text-node" max-lines@wx="1" overflow@wx="ellipsis" numberOfLines@ios|android|harmony="1">这是一个文本节点</text>
  </view>
</template>

<style lang="stylus">
.wrapper
  width 100%
.text-node
  color #333
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
.wrapper
  width 100%
.text-node
  color #333
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

## `text-like` 富文本节点

### 单、多行
如果用户指定单行，则移除 `isSingleLine` 对多行的判断支持，如果要求支持多行则自行更改支持的行数，默认为两行。

```html
<template>
  <view class="wrapper">
    <rich-text
      class="text-node"
      overflow@wx="ellipsis"
      max-lines@wx="{{ isSingleLine ? 1 : 2 }}"
      verticalAlign@ali="{{ isSingleLine ? 'white-space:nowrap;' : 'white-space:normal;'}}"
      wrapperStyle@wx|ali="overflow:hidden;text-overflow:ellipsis;{{ isSingleLine ? 'display: block;white-space:nowrap;' : 'display:-webkit-box;white-space:normal;-webkit-box-orient:vertical;-webkit-line-clamp:2;' }}"
      numberOfLines@ios|android|harmony="{{ isSingleLine ? 1 : 2 }}"
      text="{{text}}"
    />
  </view>
</template>

<script setup>
  import { ref } from '@mpxjs/core'
  const isSingleLine = ref(true)
  const text = ref('这是一个文本节点')
  defineExpose({
    isSingleLine,
    text,
  })
</script>

<style lang="stylus">
.wrapper
  width 100%
.text-node
  color #333
</style>
```