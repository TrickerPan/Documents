# JavaScript 短路求值(short-circuit evaluation)

> **短路求值** 是使用 三元运算、`&&` 和 `||` 快速求值的方法。  
> 使用短路求值，在一行代码内表达出简单的条件赋值 (`if...else if...else` 或 `switch...case...default`)。  
> 这样做，可以让代码更清晰，更简洁，更高大上。

## 1. 为变量设置默认值

假设有这样一种状况，要导入某项配置项的值，如果此配置项为空，则应用缺省值。

```js
import axios from 'axios';
import Config from './config';

const http = axios.create({
    baseURL: Config.HOST || 'http://localhost:8080',
});
```

这样处理过后，如果 `Config.HOST` 不为 `0`、`null`、`undefined`、`false` 的话，应用 `Config.HOST` 的值，反之应用 `http://localhost:8080`。

## 2. `if...else` 的简写

判断一次，产生两种结果。

```js
let x = true, y;

if (x) {
    y = 12;
} else {
    y = 21;
}

console.log(y); // Result: 12

// 以上语法，完全可以用 三元运算 解决，使代码更简洁。

let x = true;
let y = x ? 12 : 21;

console.log(y); // Result: 12

// 还有另外一种写法

let x = true;
let y = (x && 12) || 21;
```

## 3. `if...else if[...else]` 和 `switch...case[...default]`

多次判断，产生多个结果。

```js
let x = 110, y;

if (0 < x < 100) {
    console.log('Value below 100.');
} else if (x < 200) {
    console.log('Value between 100-200.');
} else {
    console.log('Value above 200.');
} // Result: Value between 100-200.

// 这个简化起来，就比较复杂了，但是还是很简洁的。

let x = 110;
let y = (0 < x < 100 && 'Value below 100.') || (x < 200 && 'Value between 100-200.') || 'Value above 200.'

console.log(y); // Result: Value between 100-200.
```

另一种情况，和上一个差不多，无甚区别。

```js
let x = 'John';

switch (x) {
    case 'World':
        console.log('Hello World!');
        break;
    case 'Admin':
        console.log('Welcome, my master.');
        break;
    default:
        console.log(`Hello ${x}.`);
} // Result: Hello John.

// 这个简化起来，和刚刚的差不多

let x = 'John';

let y = (x === 'World' && 'Hello World!') || (x === 'Admin' && 'Welcome, my master.') || `Hello ${x}.`;

console.log(y); // Result: Hello John.
```

## 4. 原理

上面写了几个例子，接下来我们来说一说原理。

1. 三元运算 `condition ？ true expression : false expression`：

    + `判断条件 ? 条件为真的处理方式 : 条件为假的处理方式`。
    + 三个元素缺一不可。
    + 条件一定要为 `boolean` 值。

2. 或 `||` 运算操作符：

    + 在短路求值中，`||` 会返回第一个为真的值。
    + 通常可以在最后一个 `||` 后面写上缺省值。

3. 与 `&&` 运算操作符：

    + 在短路求值中，`&&` 会返回第一个为假的值，如果皆为真，则返回最后一个为真的值。
    + 可以用来判断函数中，必填参数是否齐全且符合条件。

> 原理说完了，我们再重新看一下 `switch` 的简写方式。
>
> ```js
> (x === 'World' && 'Hello World!') || (x === 'Admin' && 'Welcome, my master.') || `Hello ${x}`
> ```
>
> + 我们先看第一个括号内的内容。
>   + 当 `x === 'World'` 为真的时候，第二个赋值运算也会为真，则会返回第二个值。
>   + 当 `x === 'World'` 为假的时候，则会直接返回假。
> + 第一个括号和第二个括号之间是 `||` 运算操作符。
>   + 当一个括号返回值为真的时候，则会直接返回第一个括号的返回值。
>   + 当一个括号返回值为假的时候，则要去判断第二个括号内的内容。
> + 依次类推...
