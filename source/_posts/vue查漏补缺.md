---
title: vue查漏补缺
date: 2019-02-11 16:59:36
categories: vue
tags:
 - vue
 - 修饰符
---
## Vue **不支持** IE8 及以下版本

## CDN 
```html
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.22/dist/vue.js"></script>
```

## key
确保相同的元素被切换时会重新渲染

## v-for 
- 可以对对象的属性进行迭代
- 提供了 `value`、 `key`、 `index` 三个参数

```html
<ul id="v-for-object" class="demo">
  <li v-for="(value, key, index) in object">
     {{ index }}. {{ key }}: {{ value }}
  </li>
</ul>
```
```javascript
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```

## 数组的更新检测
- push()
- pop()
- shift()
- unshift()
- splice()
- sort()
- reverse()

### 注意事项
由于 JavaScript 的限制，Vue 不能检测以下变动的数组：

- 当你利用索引直接设置一个项时，例如：
```javascript
vm.items[indexOfItem] = newValue

// 通过一下方法替代
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```
- 当你修改数组的长度时，例如：
```javascript
vm.items.length = newLength
```

## 修饰符
### 事件处理
- .stop
- .prevent
- .capture
- .self
- .once
- .passive

### 按键
- .enter
- .tab
- .delete (捕获“删除”和“退格”键)
- .esc
- .space
- .up
- .down
- .left
- .right

### 系统
- .ctrl
- .alt
- .shift
- .meta

### 鼠标
- .left
- .right
- .middle

### 表单
- .lazy
- .trim
- .number

## slot
### 具名slot
> 模板
```html
<header>
  <slot name="header"></slot>
</header>
```
> 使用
```html
// template 可以是任意元素
 <template slot="header">
  <h1>Here might be a page title</h1>
</template>
```

## 动画
### 过渡类名
- `v-enter`：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
- `v-enter-active`：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
- `v-enter-to`: 2.1.8版及以上 定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。
- `v-leave`: 定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
- `v-leave-active`：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
- `v-leave-to`: 2.1.8版及以上 定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。
![png](transition.png)
