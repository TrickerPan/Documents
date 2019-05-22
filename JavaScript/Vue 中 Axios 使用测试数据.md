# Vue 中 Axios 使用测试数据

在前端开发工程中，模拟数据是比较常见的行为。  
在 Vue 中，我们可以通过 json 文件，来制作假的数据。

> **前提条件：**
>
> + 项目使用 Vue CLI 创建。
> + Vue 版本 <sup>^</sup>2.0.0

1. 使用 Vue CLI 创建项目，并添加依赖。

    ```bash
    $ vue create template
    $ cd template
    # use yarn
    $ yarn add axios
    # or use npm
    $ npm install axios
    ```

2. 创建项目配置文件 `./src/configs/global.js`。

    ```javascript
    export const HOST = {
        MAIN: '',     // 主服务的 host
        EXAMPLE: '',  // 样例服务的 host
    };
    ```

    没什么特别的，方便全局统一配置常量。

3. 创建 `example` 的 `axios` 对象 `./src/apis/example.js`。

    ```javascript
    // @/apis/example.js
    import axios from 'axios';

    import {HOST} from '@/configs/global';

    const http = axios.create({
        // 如在全局配置中，配置了 url 则使用全局配置；
        // 如未配置，则使用测试的数据。
        baseURL: HOST.EXAMPLE || '/data/example.json',
        headers: {
            'Content-Type': 'application/json'
        },
    });

    const dataHandler = data => {
        // 可以在这里添加一些数据处理。
        return data;
    };

    export default {
        get(page, callback) {
            http.request({
                method: 'GET',
                params: {
                    page: page
                }
            }).then(response => {
                const result = response.data.data.map(item => dataHandler(item));
                // 因为 axios 的请求是异步请求，所以在这里使用了回调函数。
                callback(result);
            });
        },
        post() {},
        put() {},
        delete() {},
    };
    ```

4. 创建 `./public/data/example.json`。

    ```json
    {
        "errcode": 0,
        "errmsg": "ok",
        "data": [
            {
                "id": "a5e26a40-a060-4e61-89fc-15e61b14695c",
                "title": "Test Data",
                "content": "Text Content."
            }, {
                "id": "7829a49d-300f-4f52-bca9-562bc23750ce",
                "title": "Test Data",
                "content": "Text Content."
            }, {
                "id": "5433a6d9-a244-41a1-adac-6a62420540a5",
                "title": "Test Data",
                "content": "Text Content."
            }, {
                "id": "0bd3b283-be85-476f-a1b1-153a262c2ff8",
                "title": "Test Data",
                "content": "Text Content."
            }, {
                "id": "9e33e619-d599-40e0-95bb-767aef2f88e0",
                "title": "Test Data",
                "content": "Text Content."
            }, 
        ]
    }
    ```

5. 随便制作一个 `Component`，并把它随便插入一个页面即可。

    ```vue
    <template>
        <div class="example">
            <div v-for="(example, index) in examples" :key="example.id">
                <h1>{{example.title}}</h1>
                <p>{{example.content}}</p>
            </div>
        <div>
    </template>

    <script>
        import http from '@/apis/example';

        export default {
            name: 'Example',
            data() {
                examples: []
            },
            mounted() {
                http.get(1, data => {
                    this.examples = data;
                });
            },
        };
    </script>
    ```
