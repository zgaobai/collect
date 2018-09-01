# Vue 的继承
> 混合 mixins 和继承 extends

mixins 接收对象数组（可理解为多继承），extends 接收的是对象或函数（可理解为单继承）

## 继承钩子函数
```js
const extend = {
 created () {
  console.log('extends created')
 }
}
const mixin1 = {
 created () {
  console.log('mixin1 created')
 }
}
const mixin2 = {
 created () {
  console.log('mixin2 created')
 }
}
export default {
 extends: extend,
 mixins: [mixin1, mixin2],
 name: 'app',
 created () {
  console.log('created')
 }
}
```
控制台输出
```js
extends created
mixin1 created
mixin2 created
created
```
* 结论：优先调用 mixins 和 extends 继承的父类，extends 触发的优先级更高，相对当于队列
* push(extend, mixin1, minxin2) => 本身的**钩子函数**、**watch的值** 

## 继承 methods
```js
const extend = {
 data () {
  return {
   name: 'extend name'
  }
 }
}
const mixin1 = {
 data () {
  return {
   name: 'mixin1 name'
  }
 }
}
const mixin2 = {
 data () {
  return {
   name: 'mixin2 name'
  }
 }
}
// name = 'name'
export default {
 mixins: [mixin1, mixin2],
 extends: extend,
 name: 'app',
 data () {
  return {
   name: 'name'
  }
 }
}
// 只写出子类，name = 'mixin2 name'，extends优先级高会被mixins覆盖
export default {
 mixins: [mixin1, mixin2],
 extends: extend,
 name: 'app'
}
// 只写出子类，name = 'mixin1 name'，mixins后面继承会覆盖前面的
export default {
 mixins: [mixin2, mixin1],
 extends: extend,
 name: 'app'
}
```
* 结论：子类再次声明，data 中的变量都会被**重写**，以子类的为准。
* 如果子类不声明，data 中的变量将会最后继承的父类为准。
* 经过测试， **props 中属性** 、 **methods 中的方法** 和 **computed 的值** 继承规则一样。

## mixins
* 类型：Array<Object>
* 调用方式: mixins: [mixin1, mixin2]
* 详细：
mixins 选项接受一个混入对象的数组。这些混入实例对象可以像正常的实例对象一样包含选项，他们将在 Vue.extend() 里最终选择使用相同的选项合并逻辑合并。举例：如果你的混入包含一个钩子而创建组件本身也有一个，两个函数将被调用。
Mixin 钩子按照传入顺序依次调用，并在调用组件自身的钩子之前被调用。
* 栗子：
```js
var mixin = {
  created: function () { console.log(1) }
}
var vm = new Vue({
  created: function () { console.log(2) },
  mixins: [mixin]
})
// => 1
// => 2
```

是对父组件的扩充，包括methods、components、directive等。。。

触发钩子函数时，先调用mixins的函数，再调用父组件的函数。

虽然也能在创建mixin时添加data、template属性，但当父组件也拥有此属性时以父为准，从这一点也能看出制作者的用心（扩充）。

data、methods内函数、components和directives等键值对格式的对象均以父组件/实例为准

## extends
* 类型：Object | Function
* 调用方式: extends: CompA
* 详细：
允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 Vue.extend。这主要是为了便于扩展单文件组件。

这和 mixins 类似。
* 栗子：
```js
var CompA = { ... }

// 在没有调用 `Vue.extend` 时候继承 CompA
var CompB = {
  extends: CompA,
  ...
}
```
同样是对父组件的扩充，优先级均次于父组件

## extend
* 参数：{Object} options
* 用法：
使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数
```html
<div id="mount-point"></div>
```
```js
// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
```
```html
// 结果如下
<p>Walter White aka Heisenberg</p>
```
扩展组件的构造器

当我们调用vue.component('a', {...})时自动调用

值得注意的是extend内的data为一个函数