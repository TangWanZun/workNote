> 在编写vue的时候，我遇到的一些小问题，这些问题并不足以写一篇文章，但是对于初学者来说就是一些简单，但是难以
> 发现的坑，这里我将会记录下来，以保证之后不会出现这些问题
# 自定义组件
1. 在自定义组件中我们会出现对子组件传入值时，会改变
此时我们通常会这么写
``` javascript
	export default {
		props:['value'],
		data(){
			return {
				thisValue:this.value
			}
		}
	}
```
但是我们会发现，这个thisValue,只有在初始化的时候会变为value的值，当我们外部更改value的值的时候
thisValue并没有进行相应的改变，这个是因为这里的data中，是进行的值传值，只有在初始化时才会有效
解决方法是，采用监听的方式，监听value的变化，从而改变thisValue

```javascript
	export default {
		props:['value'],
		data(){
			return {
				thisValue:this.value
			}
		},
		watch:{
			value(val){
				this.thisValue = val;
			}
		}
	}
	
```
自定义组建中props的属性,是可以用来获取的,但是没法赋值,所以需要将props中的属性移动出来,才可以赋值
> 2019/3/9添加内容
要知道，在通常情况下，
props 中的数据我们是不允许在子组件中修改的，如果修改，则vue会报错，我们要使用$emit的方式用事件来通知父组件
来改变此值		
但是这个存在一个我暂时不知道是为什么的现象（或许这个是一个没有进行控制）
当props中存在数组和对象这种地址传值的时候，我们可以直接对该值进行操作，比如push操作。此时vue不会报错		
而且，父元素中的变量也会相应的变化。
# computed计算属性
```javascript
 nextStepDisabled:{
	   get () {
			if(this.formData.phone && this.formData.verifCode){
				return false;
			}else{
				return true;
			}
		},
		set (newValue) {
		}
	}
```
计算属性是存在get与set的
当计算属性需要赋值的时候，是必须存在set  的，否则会报错
例
	把计算属性当做v-model的值
>说法存在问题,
>计算属性,在被赋值的时候,值是没有真的被赋值到计算属性上的
>他只是会运行set方法
# vuex
> Vue 不能检测到对象属性的添加或删除
这句话将会是一个重点，由此好多问题都将可以解决	
例如	
```javascript
  state: {
		//这个是用来进行penl与其modal页面进行互通的缓存
		//当点击刷新按钮时候，这个变量将会被滞空
    constChche:{
		}
  },
  mutations: {
    setDynamicList(state,val){
			state.constChche.dynamicList = val;
    }
  },
```  
这里可以很明显的看出，当调用commit('setDynamicList')的时候，就是向constChche中添加一个		
属性，但是。我们在组件中通过computed计算属性进行检测的时候，发现，这个属性变化是检测不到的		
但是如果我们在constChche中显示的定义了dynamicList，则可以检测到			
其原因就是上面的那句话。
解决方法就是
```javascript
  state: {
		//这个是用来进行penl与其modal页面进行互通的缓存
		//当点击刷新按钮时候，这个变量将会被滞空
    constChche:{
		}
  },
  mutations: {
    setDynamicList(state,val){
			state.constChche.dynamicList = val;
			//添加这么一句话
			state.constChche = Object.assign({},state.constChche)
    }
  },
```
Object.assign的方法就是拷贝对象，重新赋值，这样computed就可以检测到变化了
#watch
属性监听
```javascript
	props:['value']
  watch:{
    value(){
        console.log(val);
    }
  }
```
当value首次被赋值的时候watch中对应的方法是不会调用的,如果需要调用，则需要开启深度监听
```javascript
  watch:{
    value:{
      immediate: true,			//开启深度监听
      handler(val){
        console.log(111);
        console.log(val);
      }
    }
  }
```
# css
1. absolute 与 relative层级相同;
```html
	<div id="box">
		<div id="bg"></div>
		<div id="con"></div>
	</div>
```
如果想实现
a. box中嵌套两个div且bg需要充当背景图,
b. 而且box必须随着con变大而变大,
c. box也有背景图
可以使用 下面方式
```html
	<div id="box">
		<div id="con"></div>
		<div id="bg"></div>
	</div>
```
con  relative
bg absolute
# 组件
对于vue来说，页面是由一个个的组件构成的，但是对于组件不同的使用方法可以让组件有这不同的效果
```html
<template>
        <div>
            Hello world
        </div>
</template>
<script>
    export default {
        name: 'ele-1',
    }
</script>
```
上面就是一个简单的组件
在使用时有两种方式
1. 局部组件
这个是最常使用的一种方式
首先在这种情况下，ele-1这个组件是嵌套在ele-2这个组件中的
这种最常用的也不需要过多的介绍了
```html
<template>
        <div>
            <ele-1></ele-1>
        </div>
</template>
<script>
	import ele-1 from 'components/ele-1'
	export default {
		name: 'ele-2',
		components:{ele-1}
	}
</script>
```
2. 全局组件
这种组件是需要用的vue的一个方法的
Vue.component( id, [definition] )
# component
Vue用于创建组件的方法
参数：
{string} id
{Function | Object} [definition]
第二个参数需要重点说明
这个参数可以接受三种情况
1. 
> 注册组件，传入一个扩展过的构造器
Vue.component('my-component', Vue.extend({ /* ... */ }))
2.
> 注册组件，传入一个选项对象 (自动调用 Vue.extend)
Vue.component('my-component', { /* ... */ })
3. 普通的方法
>此时Vue会运行fun这个方法
Vue.component('my-component', fun)
# 随笔
## 添加app加载等待页面
使用了router的时候，页面开始会有一个白屏，这个白屏并不是VUE的白屏，而是加载路由时候的
白屏，所以可以在App.vue中添加一个页面作为缓冲页面

可以通过判断router中的matched来实现
```html
<!--page就是加载页面 -->
<div v-if="invalidRoute">
  <page></page>
</div>
<router-view v-else></router-view>
```
```javascript
// 添加一个计算属性
computed: {
	invalidRoute () {
	  return !this.$route.matched || this.$route.matched.length === 0;
	}
}
```
>但是这个样子实现会有一个BUG,那就是当页面跳转到不存在的页面时候，这个效果也会触发，就会导致，在访问不存在的页面的时候，会显示加载页面
解决方法也简单，给路由守卫添加一个404页面
详情见下面
## 解决页面不存在跳转404问题 
可以在路由守卫中添加下面的话
 matched 以数组的形式显示出跳转到的页面信息，所以当matched长度为0时，表示跳转的页面不存在
```javascript
if(to.matched.length===0){
	router.replace({path:"/register/404"});
}
```
## 开发模式(development)与生产模式（production）之间配置变化
这里应该善用webpack的打包，而不是进行手动修改  
![](/assets/2.jpg)  
在vue-cli2 中，这两个文件分别就是在开发模式和生产模式中加载时加载的不同的全局变量文件
```javascript 
// 开发模式
'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  DE_BUG:true
})

```

```js
// 生产模式
'use strict'
module.exports = {
  NODE_ENV: '"production"',
  DE_BUG:true
}

```

当我们在文件中使用
```js
process.env.DE_BUG
```
通过使用这个参数 在生产模式和开发模式 这个参数将根据文件不同则不同
## 关于App.vue页面
> 首先APP页面是全部页面的入口，所以，有些请求只需要在app打开请求一次，所以会放在app中进行
这里会有个问题，就是在路由守卫中，即便我们使用了next(false)
app页面也会执行，这里可能会因为在路由鉴权时出现问题

这个时候，可以把获取信息的放置到中		
router.onReady

这个方法有个回调函数，表示当路由守卫成功调用的回调
next(false)		与 next(new Error()) 都不会执行router.onReady