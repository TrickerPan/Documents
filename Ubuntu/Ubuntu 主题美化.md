# Ubuntu 主题美化

> 记录下自己安装主题的过程，以防重装系统后的不知所措。

## 一、安装 Gnome 设置工具

```bash
sudo apt update
sudo apt install gnemo-tweak-tool gnome-shell-extensions
```

打开 Tweaks 进行如下操作：

+ `Extensions → User Themes → Turn on`。
+ `Desktop → Icons on Desktop → Show Icons → Turn off`。
+ `Windows → Placement → Turn Left`。

## 二、修改主题和图标

> 先推荐一个 Web Site —— [https://www.opendesktop.org](https://www.opendesktop.org)  
> 一个免费的、拥有众多主题或图标等共享平台门户社区。

下载 [McHith Sierra](https://www.opendesktop.org/p/1013714/) 这款主题。

![下载列表](/static/images/ubuntu_theme_download.png)

选择自己喜欢的主题颜色和图标下载就可以了。

> + 主题放到 `/usr/share/themes` 目录下。
> + 图标放到 `/usr/share/icons` 目录下。

复制到相应的目录之后，打开 Tweaks：

+ `Appearance → Themes → Applications → Choose Your Theme`
+ `Appearance → Themes → Icons → Choose Your Icon`
+ `Appearance → Themes → Shell → Choose Your Shell`

## 三、优化 Dock

打开 Ubuntu Software，搜索 `Dash to Dock` 安装插件。
打开 Tweaks，`Extensions → Dash to Dock → Turn on`，点击螺母图标 `Dash to Dock → Appearance → Use built-in theme → Turn on`。

## 四、修改登录界面

下载 [High Ubunterra](https://www.opendesktop.org/p/1207015/) 这个脚本并解压

```bash
sudo chmod -x install.sh
source install.sh
```

然后选中你喜欢的图片 `Right Click → Scripts → SetAsWallpaper` 经过这样的步骤，就设置成功啦。

> **卸载**
>
> ```bash
> sudo chmod -x uninstall.sh
> source uninstall.sh
> ```  
>
> 卸载完成。

## 五、为 Ubuntu 账户选择头像

打开 Settings，进行如下操作：

+ `Details → Users`
+ 单机头像并选择你喜欢的图片。
