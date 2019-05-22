# JavaScript 中的迭代遍历循环

## 一、`for...in`

以任意顺序遍历一个对象自有的、继承的、可枚举的、非Symbol的属性。对于每个不同的属性，语句都会被执行。

### （一）语法

```JavaScript
let obj = {a: 1, b: 2, c: 3};

// prop 每次迭代时，将不用的 键值 分配给变量。
// obj 被迭代枚举其 键值 的 对象。
for (let prop in obj) {
    console.log(`obj.${prop} = ${obj[prop]}`);
}

// obj.a = 1
// obj.b = 2
// obj.c = 3
```

### （二）注意事项

1. `添加` 操作

    最好不要在 `for...in` 循环中对迭代遍历的对象执行 `添加` 操作，因为 `for...in` 遍历是无序的，不确定被添加的属性是否会在接下来的迭代遍历过程中被访问到。

2. `修改` / `删除` 操作

    `修改` / `删除` 操作最好只对被迭代遍历的对象中正在被访问的属性执行。

3. 迭代遍历数组 `Array`

    因为 `for...in` 是无序的，所以在迭代遍历的数组中，其顺序具有重要意义时，最好使用 `for`、`Array.prototype.forEach()` 或 `for...of`。

## 二、`for...of`

在可迭代对象（包括 `Array`，`Map`，`Set`，`String`，`TypedArray`，`arguments` 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句。

### （一）语法

```JavaScript
let obj = [1, 2, 3];

// value 在每次迭代中，将不同属性的 值 分配给变量。
// obj 被迭代枚举其属性的对象。
for (let value in obj) {
    console.log(value);
}

// 1
// 2
// 3
```

### （二）注意事项

1. `for...of` 和 `for...in` 的区别

    无论是 `for...of`，还是 `for...in` 都是用来迭代一些东西，它们的主要区别在于它们的迭代方式。

    `for...in` 迭代的是对象的 键。
    `for...of` 迭代的是对象的 数据。
