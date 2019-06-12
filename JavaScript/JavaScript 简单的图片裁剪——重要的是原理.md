# JavaScript 简单的图片裁剪——重要的是原理

> 关于这个功能，我做了两版。  
> 第一版是把图片交由后台处理裁剪。  
> 第二版是利用 `canvas` 画布功能，直接对图片进行裁剪。

## 一、布局

### 1. 先浅谈一下我的布局思路

+ 我先制作一个盒子，包裹住图片和遮挡阴影，并设置 `position: relative` 做定位封锁用。
+ 把图片铺满盒子放到底层，阴影放到顶层，用以遮盖图片。
+ 制作四个阴影 `div`，把四个 `div` 用绝对定位 `position: absolute` 分别定位到：左上、右上、左下、右下。它们的大小，依据预览区域的四个点来调整。

![布局图片](/static/images/js_cut_image.png)

### 2. 接下来，再用代码实现刚刚的布局思路

#### HTML 代码

```html
<div class="box">
    <img id="box-image" class="box-image" src="" alt="图片" hidden>
    <div id="box-shadow-top" class="box-shadow box-shadow_top"></div>
    <div id="box-shadow-right" class="box-shadow box-shadow_right"></div>
    <div id="box-shadow-bottom" class="box-shadow box-shadow_bottom"></div>
    <div id="box-shadow-left" class="box-shadow box-shadow_left"></div>
    <div id="box-shadow-point_top_left" class="box-shadow-point box-shadow-point_top_left" hidden></div>
    <div id="box-shadow-point_top_right" class="box-shadow-point box-shadow-point_top_right" hidden></div>
    <div id="box-shadow-point_bottom_left" class="box-shadow-point box-shadow-point_bottom_left" hidden></div>
    <div id="box-shadow-point_bottom_right" class="box-shadow-point box-shadow-point_bottom_right" hidden></div>
</div>

<form action="__URL__" method="post">
    <div class="input-wrapper">
        <div class="input-btn">上传图片</div>
        <input id="input-image" class="input-image" name="image" type="file" accept="image/*" value="上传头像" title="上传头像">
    </div>
</form>
```

#### CSS 代码

```css
.box {
    position: relative;
    width: 512px;
    height: 512px;
    border: 5px solid #333;
    background: #fff;
}

.box .box-image {
    position: absolute;
    top: 0;
    teft: 0;
    width: 100%;
    height: 100%;
    z-index: 2;
}

.box .box-shadow {
    position: absolute;
    background: #333;
    opacity: .7;
    z-index: 3;
}

.box .box-shadow.box-shadow_top_left {
    top: 0;
    teft: 0;
}

.box .box-shadow.box-shadow_top_right {
    top: 0;
    right: 0;
}

.box .box-shadow.box-shadow_bottom_left {
    bottom: 0;
    teft: 0;
}

.box .box-shadow.box-shadow_bottom_right {
    right: 0;
    bottom: 0;
}

.box .box-shadow-point {
    position: absolute;
    width: 8px;
    height: 8px;
    background: #fff;
    border: 2px solid #000;
    z-index: 4;
}

.input-wrapper {
    position: relative;
    width: 64px;
    height: 32px;
}

.input-wrapper .input-btn {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    font-size: .8rem;
    line-height: 26px;
    text-align: center;
    color: #fff;
    background: #333;
    border: #000 solid 3px;
    border-radius: 8px;
    box-sizing: border-box;
}

.input-wrapper .input-image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
}
```

## 二、功能实现

### 1. 通过 JavaScript 裁剪图片的思路

+ 通过点击预览区域的四个点，来选择预截取的图片区域。
+ 四个阴影的大小形状，根据四个点来改变。
+ 最后把原图和预览区域左上角与右下角的在图片内的坐标，发送给后台，由后台对图片进行真正的裁剪和储存。
+ 也就是说，前端只让客户选择了他想要的图片的区域。而后台，才真正的对图片进行处理。

```JavaScript
(function () {
    "use strict";

    // 定义和储存全局变量
    let app = {
        // 定义所有需要使用的元素
        box: document.getElementById("box"),
        image: document.getElementById("box-image"),
        shadowTopLeft: document.getElementById("box-shadow_top_left"),
        shadowTopRight: document.getElementById("box-shadow_top_right"),
        shadowBottomLeft: document.getElementById("box-shadow_bottom_left"),
        shadowBottomRight: document.getElementById("box-shadow_bottom_right"),
        pointTopLeft: document.getElementById("box-point_top_left"),
        pointTopRight: document.getElementById("box-point_top_right"),
        pointBottomLeft: document.getElementById("box-point_bottom_left"),
        pointBottomRight: document.getElementById("box-point_bottom_right"),
        input: document.getElementById("input-image"),
        // 裁剪的坐标点 pointX: {left, top}, pointY: {right, bottom}
        point: {
            top: 128,
            right: 256,
            bottom: 256,
            left: 128
        },
        // 图片原始数据记录
        imageArea: {
            width: null,
            height: null
        },
        // 缩放数据记录
        // zoomArea.ratio = imageArea.width / zoomArea.width;
        // zoomArea.height = imageArea.height / zoomArea.ratio;
        zoomArea: {
            width: 512,
            height: null,
            ratio: null
        },
        // 截取区域数据记录
        shotArea: {
            width: null,
            height: null
        },
        clickPoint: 0
    }

    // 监听鼠标按下四个点的事件，开始监听鼠标移动事件
    app.pointTopLeft.addEventListener("mousedown", () => {
        app.clickPoint = 1;
    })
    app.pointTopRight.addEventListener("mousedown", () => {
        app.clickPoint = 2;
    })
    app.pointBottomLeft.addEventListener("mousedown", () => {
        app.clickPoint = 3;
    })
    app.pointBottomRight.addEventListener("mousedown", () => {
        app.clickPoint = 4;
    })

    // 鼠标移动监听事件
    app.box.addEventListener("mousemove", (event) => {
        if (app.clickPoint === 0) return;

        let point = {
            x: event.clientX - app.box.offsetLeft,
            y: event.clientY - app.box.offsetTop
        }

        if (app.clickPoint === 1 || app.clickPoint === 2) {
            app.point.top = point.y < 0 ? 0 : point.y;
        } else if (app.clickPoint === 3 || app.clickPoint === 4) {
            app.point.bottom = point.y > app.zoomArea.height ? app.zoomArea.height : point.y;
        }

        if (app.clickPoint === 1 || app.clickPoint === 3) {
            app.point.left = point.x < 0 ? 0 : point.x;
        } else if (app.clickPoint === 2 || app.clickPoint === 4) {
            app.point.right = point.x > app.zoomArea.width ? app.zoomArea.width : point.x;
        }

        app.changeShadow();
        app.changePoint();
    });

    // 监听鼠标抬起和离开事件，让预览区域不再变化
    app.box.addEventListener("mouseup", () => { app.clickPoint = 0 });
    app.box.addEventListener("mouseleave", () => { app.clickPoint = 0 });

    app.input.addEventListener("change", (event) => {
        let image = event.target.files[0];
        let fileReader = new FileReader();

        fileReader.onload = (event) => {
            let image = new Image();

            // 当图片加载完成后执行
            image.src = event.target.result;
            image.onload = (event) => {

                app.imageArea.width = event.path[0].width;
                app.imageArea.height = event.path[0].height;

                app.zoomArea.ratio = app.imageArea.width / app.zoomArea.width;
                app.zoomArea.height = app.imageArea.height / app.zoomArea.ratio;

                app.shotArea.width = app.point.right - app.point.left;
                app.shotArea.height = app.point.bottom - app.point.top;

                app.box.style.width = app.zoomArea.width.toString + "px";
                app.box.style.height = app.zoomArea.height.toString + "px";

                app.changeShadow();
                app.initPoint();
                app.changePoint();
            };

            app.image.src = event.target.result;
            app.image.hidden = false;
        }

        fileReader.readAsDataURL(image);
    })

    // 改变遮挡阴影的面积
    app.changeShadow = function () {
        app.shadowTopLeft.style.width = app.point.right.toString() + "px";
        app.shadowTopLeft.style.height = app.point.top.toString() + "px";

        app.shadowTopRight.style.width = (app.zoomArea.width - app.point.right).toString() + "px";
        app.shadowTopRight.style.height = app.point.bottom.toString() + "px";

        app.shadowBottomLeft.style.width = app.point.left.toString() + "px";
        app.shadowBottomLeft.style.height = (app.zoomArea.height - app.point.top).toString() + "px";

        app.shadowBottomRight.style.width = (app.zoomArea.width - app.point.left).toString() + "px";
        app.shadowBottomRight.style.height = (app.zoomArea.height - app.point.bottom).toString() + "px";
    };

    // 初始化四个点
    app.initPoint = function () {
        app.pointTopLeft.hidden = false;
        app.pointTopRight.hidden = false;
        app.pointBottomLeft.hidden = false;
        app.pointBottomRight.hidden = false;
    };

    // 改变四个点的位置
    app.changePoint = function () {
        app.pointTopLeft.style.top = (app.point.top - 6).toString() + "px";
        app.pointTopLeft.style.left = (app.point.left - 6).toString() + "px";

        app.pointTopRight.style.top = (app.point.top - 6).toString() + "px";
        app.pointTopRight.style.left = (app.point.right - 6).toString() + "px";

        app.pointBottomLeft.style.top = (app.point.bottom - 6).toString() + "px";
        app.pointBottomLeft.style.left = (app.point.left - 6).toString() + "px";

        app.pointBottomRight.style.top = (app.point.bottom - 6).toString() + "px";
        app.pointBottomRight.style.left = (app.point.right - 6).toString() + "px";
    };

    // 计算真是的坐标点
    app.calculationTurePoint = function () {
        return {
            top: app.point.top * app.zoomArea.ratio,
            right: app.point.right * app.zoomArea.ratio,
            bottom: app.point.bottom * app.zoomArea.ratio,
            left: app.point.left * app.zoomArea.ratio
        }
    };

})();
```

> 这个时候，就可以把原始图片和区域坐标发给后台，让后台对图片进行处理及储存了。
