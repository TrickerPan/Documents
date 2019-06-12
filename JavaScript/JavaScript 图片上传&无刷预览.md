# JavaScript 图片上传&无刷预览

> 利用 `window.FileReader` 实现图片上传&无刷预览功能。

## 1. 创建一个大容器，包裹三个元素

```html
<div class="box">
  <input id="upload-input" class="upload-input invisible" name="image" type="file" accept="image/* title="上传图片">
  <!-- accept 使之仅接受图片 -->
  <!-- title 改变 'no file chosen' 变为中文的 “上传图片” -->
  <img id="upload-image" class="upload-image invisible" src="" >
  <div class="upload-background">上传图片</div>
</div>
```

## 2. 添加 CSS 样式

>我们要让 `input.upload-input`, `img.upload-image`, `div.upload-background` 三个元素重叠。`input.upload-input` 置于最上层，用于触发文件事件；`img.upload-image` 置于中层，显示图片，覆盖文字层；`div.upload-background` 置于最下层，当没有图片的时候，展示提示信息。

首先，给 `div.box` 添加样式

```css
.box {
  position: relative;    /* 给三个子元素一个限制 */
  width: 200px;
  height: 200px;
  padding: 0;
  margin: 100px auto;
}
```

接下来编辑 `input.upload-input` 的样式

```css
.upload-input {
  position: absolute;    /* 绝对定位，定位在父元素内左上角 */
  top: 0;
  left: 0;
  cursor: pointer;
  width: 200px;
  height: 200px;
  z-index: 4;          /* 使 input 处于最上层 */
}
```

再继续给 `img.image.image-ctrl` 添加样式

```css
.upload-image {
  position: absolute;    /* 绝对定位，定位在父元素内左上角 */
  top: 0;
  left: 0;
  width: 200px;
  height: 200px;
  border: 1px solid #333;
  box-sizing: border-box;
  z-index: 3;          /* 使 img 处于中层 */
}
```

把透明样式，单独写一个 `class` ，方便消除透明

```css
.invisible {
  opacity: 0;
}
```

最后给 `div.upload-background` 添加样式

```css
.upload-background {
  position: absolute;    /* 绝对定位，定位在父元素内左上角 */
  top: 0;
  left: 0;
  width: 200px;
  height: 200px;
  color: gray;
  font-size: 40px;
  text-align: center;
  line-height: 200px;
  border: 1px solid #333;
  box-sizing: border-box;
  z-index: 2;          /* 使 div 处于最下层 */
}
```

## 3. 添加 Script 预览

```JavaScript
//首先，给 input 添加监听事件
document.getElementById("upload-input").addEventListener("change", (event) => {
  //获取上传的图片
  let file = event.target.files[0];
  let image = document.getElementById("upload-image");
  //创建FileReader对象
  let fileReader = new FileReader();
  //创建一个onload回调函数，当文件读取完成后触发
  fileReader.onload = (res) => {
    //设置 img 的 src 并移除 image-ctrl 类，使其不再透明
    image.src = res.target.result;
    image.classList.remove("invisible");
  };
  读取上传的图片，生成 Data Url 字符串
  fileReader.readAsDataUrl(file);
});
```
