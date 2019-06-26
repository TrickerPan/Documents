# ssh-server 和 ssh-client 如何上传&下载

## 一、下载文件

```bash
scp username@servername:/path/to/download/file /path/to/local/folder/
```

## 二、上传文件

```bash
scp /path/to/local/file username@servername:/path/to/upload/folder/
```

## 三、下载文件夹

```bash
scp -r username@servername:/path/to/download/folder/ /path/to/local/folder/
```

## 四、上传文件夹

```bash
scp -r /path/to/local/folder/ username@servername:/path/to/upload/folder/
```
