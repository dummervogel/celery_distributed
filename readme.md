# celery示例
项目集成了django,django-celery-beat,django-celery-results。

Celery使用时，django admin用于通过broker（这里采用redis）多个celery worker，这里有两个worker app1和app2。Celery worker执行完任务后，结果会回写到backend（mysql数据库）中。
注意：

- 所有的celery worker需要套用django，否则celery worker的执行结果无法写入到django-celery-results指定的数据表中
- 同一个queue下的celery worker需要使用同一套代码。不同queue下celery worker可以使用不同的代码。
- 在admin中配置不同的task应该找哪一个worker
- 在admin中也需要配置worker的代码，但是具体的代码实现可以不写，直接用pass
- mysql需要打上时区补丁，以支持和django time_zone里以名字形式的时区对接。见use_tz设置
admin中django启动方式为：
```bash
# 第一次运行时启动，在数据库中创建django admin对应的表格
python manage.py migrate
python manage.py createsuperuser
# 运行django
python manage.py runserver
```
celery worker启动方式为
```bash
# app1目录
celery -A celery_admin worker -l INFO --pool=solo -n worker1
# app2目录
celery -A celery_admin worker -l INFO --pool=solo -n worker2
```
flower启动方式。flower只用于flower在线时的状态(persistent可用于保存上一次在线时的结果)，可以在admin端启动。
```bash
celery -A celery_admin flower --persistent=True 
```

> 创建django工程用

```bash
django-admin startproject demodj .
```

> 在django里创建app用

```bash
python manage.py startapp app1
```

