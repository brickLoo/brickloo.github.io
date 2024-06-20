---
date: '2024-06-11'
lastmod: '2024-06-11'
title: 'Vue 学习笔记'
prev: '/docs/Vue 学习笔记'
url: '/docs/vue/note'
weight: '1'
draft: true
---

笔记作者：BrickLoo

## Vue 的 API 分类

Vue 的 API 分为组合式（Composition）和选项式（Options）两种，开发者可以在 HMTL 中调用，也可以在单文件组件（SFC）中调用。SFC 是一种可复用的代码组织形式，它将从属于同一个组件的 HTML、CSS 和 JavaScript 封装在使用 `.vue` 后缀的文件中。

对于不同的 API，选项式 API 是在组合式 API 的基础上实现的，因此更抽象。出于更好理解原理的角度考虑，笔记以 HTML 中调用组合式 API 的写法为主，并仅简要记录其他调用方式。因此在阅读本篇笔记的过程中，读者需要首先阅读 Composition & HTML 部分的笔记。

## 声明式渲染与响应式状态

- 声明式渲染：通过 JavaScript 来描述 HTML 应该是什么样子的；
- 响应式状态：能在改变时触发更新的状态，或许可以理解成拥有实时刷新效果的变量；

接下来我们看看如何基于声明式渲染使用响应式状态。

{{< tabs items="Composition & HTML,Composition & SFC,Options & HTML,Options & SFC" >}}

{{< tab >}}

在 `script` 中，我们需要导入 `createApp` 函数，以及 `reactive` 或 `ref` 函数。

```html {filename="index.html"}
<script type="module">
import { createApp, reactive, ref } from 'vue'

...

</script>
```

`createApp` 函数接收一个 Vue 组件。如果令 `createApp` 函数的返回值调用 `mount` 函数，将其挂载到对应 id 的 HTML 元素上，这个 HTML 元素就能够访问到 Vue 组件中被暴露的响应式状态。

```html {filename="index.html"}
<script type="module">
import { createApp, reactive, ref } from 'vue'

createApp({...}).mount('#app')
</script>

<div id="app">
  ...
</div>
```

要暴露响应式状态，我们需要在 Vue 组件内包含 `setup` 函数。在 `setup` 函数中，我们可以调用 `reactive` 或 `ref` 函数得到一些响应式状态，然后使用 `return` 将这些状态返回。这样，状态就会被暴露给外部。

```js {filename="index.html"}
createApp({
  setup() {
    const counter = reactive({ count: 0 })
    const message = ref('Hello World!')

    return {
      counter,
      message
    }
  }
}).mount('#app')
```

要在对应 id 的 HTML 元素中访问被暴露的响应式状态，我们只需要用双大括号 `{{` 和 `}}` 包裹被暴露的状态名，即可在 HTML 元素被渲染时得到相应的状态值。

```html {filename="index.html"}
<div id="app">
  <h1>{{ message }}</h1>
  <p>Count is: {{ counter.count }}</p>
</div>
```

{{< /tab >}}

{{< tab >}}

如果在单文件中调用组合式 API，HTML 的部分则不需要指定 id，但需要使用模板标签。这种写法下，状态的访问方式没有改变。

```html {filename="App.vue"}
<template>
  <h1>{{ message }}</h1>
  <p>Count is: {{ counter.count }}</p>
</template>
```

而对于 `script` 的部分，vue 提供了 `<script setup>` 语法糖来独立地写 `setup` 函数内部的代码，其内部的内容都会被自动地暴露给模板，而不需要我们显式地使用 `return` 将这些状态返回。

```html {filename="App.vue"}
<script setup>
import { reactive, ref } from 'vue'

const counter = reactive({ count: 0 })
const message = ref('Hello World!')
</script>

<template>
  ...
</template>
```

使用语法糖相比于普通的 `script` 是具有优势的，详情请参阅[官方文档](https://cn.vuejs.org/api/sfc-script-setup.html)。

{{< /tab >}}

{{< tab >}}

如果在 HTML 中调用选项式 API，只有 Vue 组件中的写法有所不同。相比起使用 `setup` 函数手动创建响应式状态并返回，我们可以通过 `data` 函数直接返回 JavaScript 对象来让组件自动地根据其中的键值对创建响应式状态并对外暴露。

```js {filename="index.html"}
createApp({
  data() {
    return {
      message: 'Hello World!',
      counter: {
        count: 0
      }
    }
  }
}).mount('#app')
```

除此之外，其余部分并无不同。

{{< /tab >}}

{{< tab >}}

如果在单文件中调用选项式 API，相比起组合式 API，我们需要使用 `export default` 来使得 Vue 组件可以被外部通过默认的名字调用它的实例及其内部函数。

```html {filename="App.vue"}
<script>
export default {
  data() {
    return {
      message: 'Hello World!',
      counter: {
        count: 0
      }
    }
  }
}
</script>
```

除此之外，模板部分代码与 Composition & SFC 中的写法并无不同。

{{< /tab >}}

{{< /tabs >}}

## 属性绑定

要在 HTML 元素的属性中使用响应式变量的属性值，