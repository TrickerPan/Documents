# JavaScript 中 export 和 export default 的区别，及 import 的使用

## 1. `export` 的使用

编写 `request.js` 文件

```JavaScript
const get = (urlShard) => {
    let request = new XMLHttpRequest();
    request.onreadystate = () => {
        if (request.readyState === XMLHttpRequest.DONE) {
            if (request.status === 200) {
                return JSON.parse(request.response);
            }
        }
    };
    request.open("GET", "https://www.example.com/" + urlShard, false);
    request.setRequestHeader("Content-Type", "application/json");
    request.send()
};

const post = (urlShard, data = null) => {
    let request = new XMLHttpRequest();
    request.onreadystate = () => {
        if (request.readyState === XMLHttpRequest.DONE) {
            if (request.status === 200) {
                return JSON.parse(request.response);
            }
        }
    };
    request.open("POST", "https://www.example.com/" + urlShard, false);
    request.setRequestHeader("Content-Type", "application/json");
    request.send(data)
};

// export get;
export {
    get,
    post
};

```

编写 `index.js` 文件

 ```JavaScript
// import { get } from "/path/to/request.js";
import { get, post } "/path/to/request.js";

const getItem = (itemId = null) => {
    const urlShard = "item" + itemId ? "/" + itemId : "";
    return get(urlShard);
};

const addItem = (item) => {
    const urlShard = "item";
    return post(urlShard, item);
};

```

## 2. `export default` 的使用

编写 `request.js` 文件

```JavaScript
class Request {

    get = (urlShard) => {
        let request = new XMLHttpRequest();
        request.onreadystate = () => {
            if (request.readyState === XMLHttpRequest.DONE) {
                if (request.status === 200) {
                    return JSON.parse(request.response);
                }
            }
        };
        request.open("GET", "https://www.example.com/" + urlShard, false);
        request.setRequestHeader("Content-Type", "application/json");
        request.send()
    };

    post = (urlShard, data = null) => {
        let request = new XMLHttpRequest();
        request.onreadystate = () => {
            if (request.readyState === XMLHttpRequest.DONE) {
                if (request.status === 200) {
                    return JSON.parse(request.response);
                }
            }
        };
        request.open("POST", "https://www.example.com/" + urlShard, false);
        request.setRequestHeader("Content-Type", "application/json");
        request.send(data)
    };
}

export default Request;

```

编写 `index.js` 文件

```JavaScript
import req from "/path/to/request.js";

const getItem = (itemId = null) => {
    const urlShard = "item" + itemId ? "/" + itemId : "";
    return new req().get(urlShard);
};

const addItem = (item) => {
    const urlShard = "item";
    return new req().post(urlShard, item);
};

```

## 3. 总结

`export` 是开放此文件内，某一个`常量`、`变量`、`函数`或`类`的接口，引用时需要知道名称，且要包含在`{}`内，多个要用`,`分隔；
`export default` 是开放此文件，作为一个接口，一个文件最多包含该一个 `export default` 接口，引用时不需要知道名称，可自定义名称。
