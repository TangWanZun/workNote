# IOS13.4 
## 二维码长按只会出现弹框 并不识别
![](assets/2020-05-27-18-01-31.png)

为二维码图片添加一段代码，阻止IOS13.4 的原生弹窗行为
``` css
img{
-webkit-touch-callout: none;
}
```