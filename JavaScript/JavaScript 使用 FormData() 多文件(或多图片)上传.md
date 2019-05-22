# JavaScript 使用 FormData() 多文件(或多图片)上传

## 1. `index.html` 页面

```html
<!doctype html>
<html lang="zh">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Multiple Upload</title>
</head>

<body>
    <form enctype="multipart/form-data">
        <!-- 注意 name 元素中的 '[]'，声明 input file 为数组 -->
        <label for="files">文件<input id="files" type="file" name="files[]" multiple></label>
        <input id="upload" type="submit" value="提交">
    </form>
    <script src="./app.js" async></script>
</body>

</html>
```

## 2. `app.js` 页面

```JavaScript
(function() {
    "use strict";

    let app = {
        isLoading: true,
        files: [],
        response: null,
    };

    document.getElementById('files').addEventListener('change', event => {
        app.files = [];
        let files = event.target.files;
        for (let file of files) {
            app.files.push(file);
        }
    });

    document.getElementById('upload').addEventListener('click', event => {
        event.preventDefault();
        let formData = new FormData();
        let request = new XMLHttpRequest();
        let url = "http://www.example.com/upload";
        // 这步很重要，循环添加 file 进 FormData()
        app.files.forEach(file => {
            formData.append("files[]", file);
        });
        request.onreadystatechange = () => {
            if (request.readyState === XMLHttpRequest.DONE) {
                if (request.status === 200) {
                    app.response = JSON.parse(request.response);
                }
            }
        };
        request.open("POST", url);
        request.setRequestHeader("Content-Type", "multipart/form-data")
        request.send(formData);
    });
})();
```
