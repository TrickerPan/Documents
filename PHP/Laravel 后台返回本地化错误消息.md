# Laravel 后台返回本地化错误消息

> 前台本地化本地化很简单 `@lang('messages.welcome')` 这种方式快捷获取本地化消息。
>
> **那么后台呢？**

1. 首先，我们随意找一个 `view`，在里面加入 `@lang()`，然后访问这个页面。

    ![laravel_lang_error_1](/static/images/laravel_lang_error_1.png)

    然后，在后端代码写入：

    ```PHP
    <?php

    namespace App\Http\Controllers

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;
    use Illuminate\Translation\Translator;

    class TestController extends Controller
    {
        public function __invoke(Request $request)
        {
            $message = Translator::getFromJson("messages.welcome");

            return view('welcome', $message);
        }
    }
    ```

    然并卵。

2. 突然间，我在错误日志中找到了 `blade` 解析后的错误描述

    ![laravel_lang_error_2](/static/images/laravel_lang_error_2.png)

    然后，把第一步的后端代码替换为：

    ```PHP
    <?php

    namespace App\Http\Controllers

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;
    use Illuminate\Translation\Translator;

    class TestController extends Controller
    {
        public function __invoke(Request $request)
        {
            $message = app('translator')->getFromJson("messages.welcome");

            return view('welcome', $message);
        }
    }
    ```

    奇迹般的生效了！
