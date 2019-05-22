# JavaScript 判断 Web 页面是在手机端打开，还是PC端打开

## 一行代码，就足够了

```JavaScript
const mobile_url = "https://wap.baidu.com";
const pc_url = "https://www.baidu.com";

window.location.href = new RegExp("/Android|webOS|iPhone|iPod|BlackBerry/i").test(navigator.userAgent) ? mobile_url : pc_url;
```

以上代码利用了`正则表达式`和`三目运算`，换算成 `if` 条件从句就是：

```JavaScript
const mobile_url = "https://wap.baidu.com";
const pc_url = "https://www.baidu.com";

if (new RegExp("/Android|webOS|iPhone|iPod|BlackBerry/i").test(navigator.userAgent)) {
    window.location.href = mobile_url;
} else {
    window.location.href = pc_url;
}
```

> **划重点**  
> 原理就是利用`正则表达式`去判断 `UserAgent` 中是否含有 Android / webOS / iPhone 等字样，并且用 `i` 做了不区分大小写的修饰，最后再用 `test` 方法去判断 `UserAgent` 是否可以和`正则表达式`进行匹配。
