# Django 使用 MySQL 执行 python manage.py migrate 发生错误

``` python
Traceback (most recent call last):
  File "...\demo\venv\lib\site-packages\django\db\backends\mysql\base.py", line 15, in <module>
    import MySQLdb as Database
ModuleNotFoundError: No module named 'MySQLdb'
```

1. 首先执行 `pip install pymysql`。
2. 然后在 `项目` 目录下 `__init__.py` 中添加如下代码：

``` python
# demo/__init__.py
import pymysql

pymysql.install_as_MySQLdb()
```

1. 再次执行 `py manage.py migrate` 即可。
