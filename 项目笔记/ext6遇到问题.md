# 现代（modern）模式本地化
[github 本地化包](https://github.com/wemersonjanuario/modern-locale)    
首先下载这个包     
将名字更改成为 modern-locale
并将其放入到  ext\modern 文件夹下
最后在app.json 中添加下面代码即可
```
"modern": {
    //以下是新增的添加本地化包的代码
    "requires" : [
        "modern-locale"
    ],
    "locale": "zh_CN"
},
```
# scss的使用方式
1. 在组件同级目录下建立一个同名称的scss文件
2. 在组件中引入cls 方法，添加类名既可以
3. 运行命令
> sencha app watch 
之后就会自动生成对应的css
