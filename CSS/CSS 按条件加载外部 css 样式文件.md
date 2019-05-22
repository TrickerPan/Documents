# CSS 按条件加载外部 css 样式文件

> Mobile 端 和 PC 端，同一套 HTML 构架，加载不同的 css 样式文件。

## 一、在 HTML 中

```HTML
<style href="styles_mobile.css" media="screen and (max-width:700px)" />
<style href="styles_pc.css" media="screen and (min-width:700px)" />
```

## 二、在 CSS 中

```css
@import url("styles_mobile.css") screen and (max-width:700px);
@import url("styles_pc.css") screen and (min-width:700px);
```

## 三、扩展 `CSS at-rule`

`at-rule` 是一个条件声明。已 `@` at 符号开头， `；` 分号结尾。

+ `@charset` —— 定义字符集
+ `@import` —— 应用外部样式文件
+ `@namespace` —— 定义其所有内容皆视为带有 XML 命名空间的前缀。
+ `Nested at-rules` —— 嵌套规则，可用样式语句以及条件规则的内部：
  + `@media` —— 条件规则，如设备满足条件，则应用其内容。
  + `@supports` —— 条件规则，如浏览器满足条件，则应用其内容。
  + `@page` —— 打印文档时样式布局更改内容。
  + `@font-face` —— 外部字体引用内容。
  + `@keyframes` —— CSS 动画步骤应用内容。

### 1. `Media Queries` 媒体查询

根据媒体的类型或特征修改样式。例：

+ 追加条件给 `@media` 和 `@import` ，判断是否应用内容。
+ 追加条件给 `<link>` `<source>` 等。
+ 使用 `JavaScript` 中 `Window.matchMedia()` 方法和 `MediaQueryList.addListener()` 方法，监控设备变化。

### 2. `Media Types` 媒体类型

设备的类型。除了在使用 `not` 或 `only` 逻辑运算符的情况下，媒体类型都是可选的。

+ `all` —— 全部，缺省值，默认类型
+ `print` —— 打印及打印预览
+ `screen` —— 屏幕
+ `speech` —— 语音合成

### 3. `Media Features` 媒体特征

每个媒体特征都必须被 `()` 括号包围。

+ `width` —— 可视宽度
+ `height` —— 可视高度
+ `aspect-ratio` —— 可视宽度/可视高度
+ `orientation` —— 可视方向
+ `resolution` —— 设备的像素密度(我们常说的分辨率)
+ `scan` —— 设备的扫描方式(`progressive` 和 `interlace`)
+ `grid` —— 设备是否支持网格布局
+ `color` —— 设备每单元的颜色位数，非彩色设备则为 0
+ `monochrome` —— 黑白设备的像素数，非黑白设备则为 0

### 4. `Logical Operators` 逻辑运算符

可以通过 `,` 逗号分隔多个媒体查询到单个规则中。

+ `and` —— 与，皆为真，则应用其内容
+ `not` —— 非，条件为假，则应用其内容
+ `only` —— 仅，仅判断媒体类型中的某一个类型，并忽视其它类型。
+ `,` —— 逗号，用于分隔多个媒体查询语句到一个规则中。多个媒体查询中，有一个为真，则应用其内容。(类似于 `or` 或 逻辑运算符)
