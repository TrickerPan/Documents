# CSS box-shadow 详解

## 一、简介

|名称|描述|
|---|---|
|缺省值|`none`|
|适用|所有元素|
|继承|不继承|
|`border-radius`|会被影响|
|多重阴影|支持，多重阴影间的 `z-index` 排序——第一个被注册的阴影在最上面。|

### （一）表达式

```css
/* offset-x | offset-y | color */
box-shadow: 64px -64px teal;

/* offset-x | offset-y | blur-radius | color */
box-shadow: 10px 5px 5px black;

/* offset-x | offset-y | blur-radius | spread-radius | color */
box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.2);

/* inset | offset-x | offset-y | color */
box-shadow: inset 5em 1em gold;

/* 任何数量的阴影，用逗号(,)分隔。 */
box-shadow: 3px 3px red, -1em 0 0.4em olive;
```

### （二）参数

1. `inset`

    如未指定，则默认外部阴影。指定则为内部阴影。

2. `<offset-x> <offset-y>`

    这是两个 `<length>` 值，用于设置阴影的偏移量。`<offset-x>` 指定水平偏移距离，**负值将阴影置于元素左侧**。`<offset-u>` 指定垂直偏移距离，**负值将阴影置于元素上方**。如果两个值均为 0，则阴影位于元素正后方。

3. `<blur-radius>`

    这是第三个 `<length>` 值，此值越大，阴影越模糊，且越大。**不允许使用负值**。缺省值为 0.

4. `<spread-radius>`

    这是第四个 `<lenght>` 值，正值使阴影扩大，负值是阴影缩小。缺省值为 0.

5. `<color>`

    颜色值。

## 二、示例

### （一）阴影在元素四周

<div style="width: 256px; height: 256px; margin: 16px auto; background-color: #FF0; box-shadow: 0 0 8px 16px #0FF;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 0 0 8px 16px #0FF;
    }
</style>

<div class="box"></div>
```

### （二）阴影在元素上方

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 0 -16px 8px #0FF;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 0 -16px 8px #0FF;
    }
</style>

<div class="box"></div>
```

### （三）阴影在元素下方

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 0 16px 8px #0FF;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 0 16px 8px #0FF;
    }
</style>

<div class="box"></div>
```

### （四）阴影在元素左侧

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: -16px 0 8px #0FF;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: -16px 0 8px #0FF;
    }
</style>

<div class="box"></div>
```

### （五）阴影在元素右侧

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 16px 0 8px #0FF;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 16px 0 8px #0FF;
    }
</style>

<div class="box"></div>
```

### （六）双层阴影

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 0 0 8px 16px #0FF, 0 0 8px 32px #F0F;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 0 0 8px 16px #0FF,
                    0 0 8px 32px #F0F;
    }
</style>

<div class="box"></div>
```

### （七）左上和右下颜色不同

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: -16px -16px 8px 16px #0FF, 16px 16px 8px 16px #F0F;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: -16px -16px 8px 16px #0FF,
                    16px 16px 8px 16px #F0F;
    }
</style>

<div class="box"></div>
```

### （八）右上和左下颜色不同

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 16px -16px 8px 16px #0FF, -16px 16px 8px 16px #F0F;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 16px -16px 8px 16px #0FF,
                    -16px 16px 8px 16px #F0F;
    }
</style>

<div class="box"></div>
```

### （九）四个边的阴影展示不同颜色

<div style="width: 256px; height: 256px; margin: 64px auto; background-color: #FF0; box-shadow: 0 -16px 8px #0FF, 16px 0 8px #F0F, 0 16px 8px #0F0, -16px 0 8px #00F;"></div>

```html
<style>
    .box {
        width: 256px;
        height: 256px;
        background-color: #FF0;
        box-shadow: 0 -16px 8px #0FF,
                    16px 0 8px #F0F,
                    0 16px 8px #0F0,
                    -16px 0 8px #00F;
    }
</style>

<div class="box"></div>
```
