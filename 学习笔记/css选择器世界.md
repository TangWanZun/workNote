# 总结

## 1.不要嵌套选择器
> 在less 或者 sass等预编译工具出现后，嵌套变的很复杂。
### 缺点
1. 性能较弱
2. 优先级混乱
3. 样式布局脆弱

## 2.不要轻视面向属性的类
> 类选择器的目的其实就是为了复用，如果不能复用那么跟用内联样式有什么区别？

一个网站页面基本归纳为
1. 公用结构
2. 公用模块
3. UI组件
4. 精致布局
5. 细枝末节

而细枝末节则可以使用面向属性的类  
例如：简单的flex 布局 、两个按钮的间距、字号等


# 技巧

## 状态属性的使用

当出现了用js操作css的时候 可以这么使用
```html
<div class="box" id="box">
面板
</div>
```

```css
.box{
  display:none;
}
/*这样控制的其实还是box面板 ，注意 两者之间是没有空格的*/
/*注意 aciton在全局中不能使用，必须是空的*/
.box.action{
  display:block;
}
```

```js
box.classList.add('action')
```

>这样做的好处是语义明显。js其实并没有跟css的样式进行耦合，因为action 这个类是空内容的。

## 一行实现类似:first-child效果
我们希望除了列表中的第一个以外，其他的都margin-top:20px;

```css
/*只兼容IE8以上*/
.cs-list:not(:first-child){
  margin-top:20px;
}
```
或者使用相邻兄弟选择器
>如果子元素中第一个不是.cs-list 这个方法也是可以 的
```css
.cs-list + .cs-list{
  margin-top:20px;
}
```

这样比之前使用:first-child要好用的多

## AMCSS开发模式
> 利用了属性选择器中的 [am-el~=btn]属性单词完全匹配选择器·  

解决的问题：当我们使用面向对象的类的时候，我们需要为每个css添加前缀名，并且会和我们通用的css 混合在一起.

```html
<div rab-cl="flex flex-start-center"></div>
```

```css
[rab-cl~="flex"]{
  display:flex;
}

[rab-cl~="flex-start-center"]{
  display:flex;
  justify-content: flex-start;
  align-items: center;
}

```

## 点击div 展示出一个弹框，点击其他位置，弹框消失

之前这个功能我们使用的都是js实现的，其实完全可以只使用css来实现

利用:focus  当获取焦点的时候，执行。
这里有个问题  就是不是全部的html标签支持这个功能
所以说，我们可以为不支持的标签添加一个属性  tabindex 来

例如
```html
<div tabindex="-1"></div> <!-- 表示有获取焦点的效果，但是按tab键是不会选中的 -->
<div tabindex="0"></div><!-- 表示有获取焦点的效果，但是按tab键是可以被响应的-->
<div tabindex="1"></div><!-- 这种情况不需要有-->
```

例子:[focus点击显示二维码图片实例页面](https://demo.cssworld.cn/selector/7/3-1.php) 
![](/assets/2020-04-02-11-17-37.png)
