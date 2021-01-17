# 新建一个VUE项目

1.安装node.js（node.js自带npm）（npm更新最新版本 npm install -g npm）

2.可使用淘宝镜像 $ npm install -g cnpm --registry=https://registry.npm.taobao.org

3.安装vue -cli cnpm install vue-cli -g  (vue list 检查vue版本) 、

4.打开放项目的文件夹 （cd）

5.创建项目 (vue init webpack “项目名字”)

6.安装依赖或者运行

# v-on 事件处理

```vue
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发 -->
<!-- 而不会等待 `onScroll` 完成  -->
<!-- 这其中包含 `event.preventDefault()` 的情况 -->
<div v-on:scroll.passive="onScroll">...</div>

<!-- 只有在 `key` 是 `Enter` 时调用 `vm.submit()` -->
<input v-on:keyup.enter="submit">

<!-- 你可以直接将 KeyboardEvent.key 暴露的任意有效按键名转换为 kebab-case 来作为修饰符。 -->
<input v-on:keyup.page-down="onPageDown">

<!-- Alt + C -->
<input v-on:keyup.alt.67="clear">

<!-- Ctrl + Click -->
<div v-on:click.ctrl="doSomething">Do something</div>

<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button v-on:click.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button v-on:click.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button v-on:click.exact="onClick">A</button>

鼠标按钮修饰符
.left
.right
.middle
```



# 生命周期

![image-20201117143339720](C:\Users\saoren\AppData\Roaming\Typora\typora-user-images\image-20201117143339720.png)

![

](C:\Users\saoren\AppData\Roaming\Typora\typora-user-images\image-20201117143356760.png)



- beforecreate : 一般使用场景是在加 loading事件 的时候
- created ：处于loading结束后，还做一些初始化，实现函数自执行（data数据已经初始化，但是DOM结构渲染完成，组件没有加载）
- beforemount：处于组件创建完成，但未开始执行操作
- mounted ：处于发起后端请求，获取数据，配合路由钩子执行操作（DOM渲染完成，组件挂载完成 ）
- beforeupdate、updated：处于数据更新的前后
- beforeDestroy：当前组件还在的时候，想删除组件
- destroyed ：当前组件已被销毁，清空相关内容

**created 与 mounted 的区别**

- created:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
- mounted:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。

**mounted 与 methods 的区别**

- mounted 是生命周期方法之一，会在对应生命周期时执行。
- methods 是Vue实例对象上绑定的方法，供当前Vue组件作用域内使用，未调用不会执行，只执行逻辑，返回值可有可无。

**computed 与 watched 的区别**

- computed 是计算属性，也可以理解为一个方法。其中计算的结果如果不发生改变就不会触发，且必须返回一个值并在DOM中绑定的才能取得值。他可以自动获取数据的改变。
- watched 属性是手动定义的所需监听的值，不同的数据可以在其中多次定义监听值，这时会消耗一定性能，他并不能像computed那样自动改变
- 

# VUE 选项

## name:

```vue
 name: 'XXX',
```

## data:Vue 实例的数据对象

```vue
data() {
      return {
      };
    },
```

## props：可以是数组或对象，用于接收来自父组件的数据

```
// 简单语法
Vue.component('props-demo-simple', {
  props: ['size', 'myMessage']
})

// 对象语法，提供验证
Vue.component('props-demo-advanced', {
  props: {
    // 检测类型
    height: Number,
    // 检测类型 + 其他验证
    age: {
      type: Number,
      default: 0,
      required: true,
      validator: function (value) {
        return value >= 0
      }
    }
  }
})
```



## propsData：创建实例时传递 props。主要作用是方便测试。



## computed:计算属性



## methods:事件处理器



## watch:监测Vue实例上的数据变动。

```vue
<template>
  <div>
    <el-input v-model="demo"></el-input>
    {{value}}
  </div>
</template>
<script>
  export default {
    name: 'index',
    data() {
      return {
        demo: '',
        value: ''
      };
    },
    watch: {
      demo(val) {
        this.value = this.demo;
      }
    }
  };
</script>
```



## components：包含 Vue 实例可用组件的哈希表。



## filters:包含 Vue 实例可用过滤器的哈希表。



## directives:包含 Vue 实例可用指令的哈希表。



# Router

path:路径

component：组件import的名字

```
children[{

	path:

	component:

}]
```

