# Flask 与 Celery 的应用工厂模式

Flask 的官网，提供了 Celery 的简单配置。
安装 Celery 和 Redis 的包 `pip install celery['redis']`
当然，你也可以换成别的

```Python
from flask import Flask
from celery import Celery


def make_celery(app):
    celery = Celery(
        app.import_name,
        backend=app.config['CELERY_RESULT_BACKEND'],
        broker=app.config['CELERY_BROKER_URL']
    )
    celery.conf.update(app.config)

    class ContextTask(celery.Task):
        def __call__(self, *args, **kwargs):
            with app.app_context():
                return self.run(*args, **kwargs)

    celery.Task = ContextTask

    return celery


flask_app = Flask(__name__)
flask_app.config.update(
    CELERY_BROKER_URL='redis://localhost:6379/0',
    CELERY_RESULT_BACKEND='redis://localhost:6379/0'
)
celery = make_celery(flask_app)


@celery.task()
def add_together(a, b):
    return a + b
```

然后执行 `celery -A your_application.celery worker` 命令，即可运行 Celery 。

但是，我们一般使用应用工厂模式，这种基础的方法，就不太试用了。
笔者研究了一下，搞出了如下方法。

## `Flask` 应用工厂下 `Celery`

目录结构如下

```folder
/home/user/Projects/flask-and-celery
├── app/
│   ├── __init__.py
│   ├── main/
│   │   └── __init__.py
│   ├── models/
│   │   └── __init__.py
│   ├── tasks/
│   │   └── __init__.py
│   ├── templates/
│   └── static/
├── tests/
│   ├── __init__.py
│   └── test_basics.py
├── migrations/
├── venv/
├── config.py
├── celery_app.py
└── requirements.txt
```

编写 `~/app/tasks/__init__.py` 文件

```Python
from celery import Celery


def make_celery(app):
    celery = Celery(
        app.import_name,
        backend=app.config['CELERY_RESULT_BACKEND'],
        broker=app.config['CELERY_BROKER_URL']
    )
    celery.conf.update(app.config)

    class ContextTask(celery.Task):
        def __call__(self, *args, **kwargs):
            with app.app_context():
                return self.run(*args, **kwargs)

    celery.Task = ContextTask

    return celery


```

编写 `~/app/__init__.py` 文件

```Python
from flask import Flask

from config import config
from .models import db
from .tasks import make_celery

celery = None


def create_app(config_name):
    app = Flask(__name__)
    app.config.from_object(config[config_name])

    config[config_name].init_app(app=app)
    db.init_app(app=app)

    global celery
    celery = make_celery(app=app)

    return app

```

最后编写 `~/celery_app.py` 文件

```Python
import os

from flask_migrate import Migrate

from app import create_app, db

app = create_app(os.getenv('FLASK_CONFIG'))
migrate = Migrate(app=app, db=db)

from app import celery  # 注意了，这是重点，一定要在初始化 app 后，再引用 celery 不然，celery 找不到配置文件，也找不到 app


@app.shell_context_processor
def make_shell_context():
    return dict(
        db=db
    )


@app.cli.command()
def test():
    import unittest
    test = unittest.TestLoader().discover('tests')
    unittest.TextTestRunner(verbosity=2).run(tests)

```

> **注意:**  
> 一定要在初始化 app 之后再引用 celery

接下来执行 `celery -A celery_app.celery worker` 命令就 OK 啦！
