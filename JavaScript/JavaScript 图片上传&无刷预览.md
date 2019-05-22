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

展示一下效果图：  
![效果图][preview]

####3. 添加 Script 预览

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

[preview]: data:image/png;base64,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
