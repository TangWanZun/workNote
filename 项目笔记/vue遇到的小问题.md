> 在编写vue的时候，我遇到的一些小问题，这些问题并不足以写一篇文章，但是对于初学者来说就是一些简单，但是难以
> 发现的坑，这里我将会记录下来，以保证之后不会出现这些问题
# 自定义组件
1. 在自定义组件中我们会出现对子组件传入值时，会改变
此时我们通常会这么写
```
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

```
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
# computed计算属性
```
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
# css
1. absolute 与 relative层级相同;
```
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
```
	<div id="box">
		<div id="con"></div>
		<div id="bg"></div>
	</div>
```
con  relative
bg absolute
# 随笔
## 添加app加载等待页面
使用了router的时候，页面开始会有一个白屏，这个白屏并不是VUE的白屏，而是加载路由时候的
白屏，所以可以在App.vue中添加一个页面作为缓冲页面

可以通过判断router中的matched来实现
```
//page就是加载页面
<div v-if="invalidRoute">
  <page></page>
</div>
<router-view v-else></router-view>
//添加一个计算属性
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
```
if(to.matched.length===0){
	router.replace({path:"/register/404"});
}
```
