---
title: Vue 自定义指令，控制元素显示隐藏
date: 2022-06-06 16:20:21
tags: [前端, Vue]
categories: 代码笔记
url: vue-custom-directive-show-or-hide
keywords: [Vue, 自定义指令, directive, bind]
description: Vue 提供了v-show、v-if可以快速的控制元素显示隐藏，但是在实际项目中，控制元素的显示或隐藏需求更加复杂，此时可以封装业务指令，控制元素的显示隐藏。
photos: [/thumbnail/vue.webp]
---

Vue 提供了`v-show`、`v-if`可以快速的控制元素显示隐藏。但在实际项目中，控制元素的显示或隐藏的需求会更加复杂，此时可以封装自定义业务指令，控制元素的显示隐藏。

<!--more-->

例如，有这样一个业务：

* 首先有一个项目模块；其次，根据项目模块，延伸出了工单模块、任务模块，日报模块等等。延伸模块都与项目模块有关联，使用项目id与其它模块关联。
*  当项目模块中的项目关闭后，延伸的其它模块的编辑、删除按钮都需要隐藏。

解决思路：封装`v-project-close`指令，传入项目id，发起请求，查询此项目是否关闭:关闭则隐藏元素，未关闭则不操作。

## 注册指令
使用 Vue.directives() 注册指令。

* bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

```js
import Vue from 'vue'

Vue.directive('project-close', {  
    bind: async (el, binding, vnode) => {
	    // queryProjectId 发起请求
        await queryProjectId(binding.value).then((resp) => {  
          if (resp.data.close) {
	          // 隐藏元素
	          el.parentNode && el.parentNode.removeChild(el) || (el.style.display = 'none')
          } 
        })
    }  
})
```

bind 函数的参数说明：

-   `el`：指令所绑定的元素，可以用来直接操作 DOM。
-   `binding`：一个对象，包含以下 property：
    -   `name`：指令名，不包括 `v-` 前缀。
    -   `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
    -   `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
    -   `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
    -   `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
    -   `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
-   `vnode`：Vue 编译生成的虚拟节点。移步 [VNode API](https://cn.vuejs.org/v2/api/#VNode-%E6%8E%A5%E5%8F%A3) 来了解更多详情。
-   `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。

## 对象字面量
`binding.value` 支持传入一个 JavaScript 对象字面量。

```vue
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
Vue.directive('demo', function (el, binding) {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text)  // => "hello!"
})
```

>官方文档：[自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)