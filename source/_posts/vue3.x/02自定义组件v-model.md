---
title: Vue3.x 自定义v-model
date: 2023-02-02 17：32：00
tags:
categories:
- [Vue]
---

## 自定义组件的v-model

在下面的代码中，首先我们把属性名修改成 modelValue，然后如果我们想在前端页面进
行点击评级的操作，我们只需要通过 update:modelValue 这个 emit 事件发出通知即可。

```js
let props = defineProps({
  modeValue: Number,
  theme: {
    type: String,
    default: 'orange'
  }
})
let emits = defineEmits(['update:modelValue'])
```

然后我们就可以按如下代码中的方式，使用 Rate 这个组件，也就是直接使用 v-model 绑
定 score 变量。这样，就可以实现 value 和 onRate 两个属性的效果。

```html
<template>
  <h1>你的评分是 {{ score }}</h1>
  <Rate v-model="score"></Rate>
</template>
```
