---
layout: post
title: "Celery tasks 구조 변경하기"
date: 2021-06-01
tag:
 - celery
 - django
 - python
---

앱의 로그인이 잘 되지 않는다는 문의가 있었다. 여러 원인이 있겠지만, 내 디바이스에서도 로그인을 시도한 후에 화면이 바뀌지 않는 문제점이 있었다. 앱을 삭제하고 다시 설치하는 과정을 반복하면서 나중에는 그런 일이 없었기 때문에 대수롭지 않게 넘어갔던게 문제였다.

## 문제 분석

내 디바이스에서 로그인이 안되는 과정은 아래와 같았다.

1. 로그인 시도 완료함.
2. 화면이 변경되지 않음.
3. 2일 뒤에 앱에 접속하자 화면이 바뀌어 있었음.

앱의 로직은 아래와 같았다.

1. 앱에서 카카오에게 소셜 로그인 요청을 보낸다.
2. 카카오에게 요청과 이메일 값을 받아 서버로 보낸다.
3. 서버에서 토큰을 받아와 디바이스에 저장한다.
4. 토큰이 있을 경우, 앱의 화면이 바뀐다.

여기서 추론한 문제점은 **서버에서 로그인을 처리하는 프로세스가 늦게 응답했구나!** 라는 거였다. 카카오 로그인을 요청해놓은 뒤에 아무런 작동도 하지 않았는데, 로그인이 되었다는 건 토큰 값을 받아오는 게 느렸을 것이다라고 생각했기 때문이다.

확인을 위해 서버 상태를 확인하였다.

```bash
$ top
... load average: 4.xx 4.xx, 4.xx

... celery
... celery
... celery
```

Linux에서 로드 애버리지는 프로세스가 처리되기를 기다리는 정도를 말한다. `load average`는 서버의 상태를 확인할 수 있는 지표중 하나인데, 서버의 cpu 개수에 따라 부하를 판단할 수 있다. 일반적으로 cpu 개수보다 load average의 값이 적으면 문제가 없다고 할 수 있고, 많다면 과부하일 가능성이 있다. 현재 팀의 서버는 코어가 2개였고, 메모리 사용률은 낮았으므로 코어 과부하로 판단했다. cpu 업그레이드를 하기 전에 celery의 구조를 변경해보기로 했다.

어떻게 구조를 변경해야할지 고민하던 중 [[Celery] Django 프로젝트에서 Celery Worker 제한하기](https://qwlake.github.io/etc/2020/08/04/using-routing-tasks-in-celery-on-django/)와 [[Django] Celery,Redis로 Scrapy 크롤링 주기적으로 하기](https://qwlake.github.io/django/2020/05/15/django-scrapy-with-celery-redis/) 글을 보고 수정하게 되었다.

## Task 변경

나도 원래는 하나의 Celery task에 여러 사이트를 크롤링하도록 하였다. 크롤링 자체만으로는 부담이 아니었을지 모르지만, 새롭게 크롤링되는 게시글마다 푸쉬 알림까지 task 하도록 하였으니 문제가 있었던 것 같다.

위 게시글대로 각각의 task에 사이트를 하나씩 크롤링하도록 변경했다.
### 크롤링 Task

```python
def crawling_start():
    spiders = [
        ...
    ]
    process = CrawlerProcess(settings)
    for spider in spiders:
        process.crawl(spider)
    process.start()


@app.task
def crawling():
    proc = Process(target=crawling_start)
    proc.start()
    proc.join()
```

위의 코드를 아래처럼 수정했다.

```
def crawl_start(**kwargs):
    site = kwargs.get("site")
    spiders = {
        ...
    }

    # 스파이더 돌리기
    process = CrawlerProcess(settings)
    process.crawl(spiders[site])
    process.start()

    return process.join()


@app.task
def crawl_site(site):
    proc = Process(target=crawl_start, kwargs={"site": site})
    proc.start()
    return proc.join()


@app.task
def crawl_sites():
    sites = [...]

    for site in sites:
        crawl_site.apply_async((site,), queue="crawl_tasks")
```

### 푸쉬 알림 Task

```python
def send_new_push_notification(**kwargs):
    post = Post.objects.get(id=kwargs.get("post_id"))
    user = User.objects.get(id=kwargs.get("user_id"))
    try:
        return MobileNotification.objects.get(
            post=post, recipient=user, deleted_at=None
        )
    except MobileNotification.DoesNotExist:
        return MobileNotification.objects.create(
            recipient=user, title=post.title, post=post
        )


@app.task
def send_new_push_notifications(**kwargs):
    post_id = kwargs.get("post_id")
    post_title = kwargs.get("post_title")
    users = get_users(post_title)
    for user in users:
        notification = send_new_push_notification(
            post_id=post_id, user_id=user.id
        )
        recipient = notification.recipient
        if recipient.has_android_device():

            push_service = FCMNotification(api_key=settings.FCM_API_KEY)

            # 보낼 내용
            registration_ids = push_service.clean_registration_ids(
                recipient.get_android_devices()
            )

            ...

            results = push_service.notify_multiple_devices(
                registration_ids=registration_ids,
                badge=badge,
                message_title=message_title,
                message_body=message_body,
                android_channel_id=android_channel_id,
            )

```

푸쉬 알림도 개별로 task가 생기도록 수정했다.

```python

@app.task
def send_new_push_notification(post_id, user_id):
    post = Post.objects.get(id=post_id)
    user = User.objects.get(id=user_id)
    try:
        notification = MobileNotification.objects.get(
            post=post, recipient=user, deleted_at=None
        )

        return None
    except MobileNotification.DoesNotExist:
        notification = MobileNotification.objects.create(
            recipient=user, title=post.title, post=post
        )

        recipient = notification.recipient

        if recipient.has_android_device():
            push_service = FCMNotification(api_key=settings.FCM_API_KEY)

            # 보낼 내용
            registration_ids = push_service.clean_registration_ids(
                recipient.get_android_devices()
            )

            ...

            results = push_service.notify_multiple_devices(
                registration_ids=registration_ids,
                badge=badge,
                message_title=message_title,
                message_body=message_body,
                android_channel_id=android_channel_id,
            )

        if recipient.has_ios_device():
            pass

        return results


@app.task
def send_new_push_notifications(post_id, post_title):
    users = get_users(post_title)
    for user in users:
        send_new_push_notification.apply_async((post_id, user.id), queue="notify_tasks")

```

## Worker, Queue 변경

Celery는 작업 시간이 비슷한 Queue 별로 Worker를 나눠주는 것이 효율적이라고 한다. 그래서 Worker와 Queue를 변경해주었다.

### Queue 생성

```python
# celery.py
from __future__ import absolute_import, unicode_literals

import os
from celery import Celery

# Defined dafault django setting file for 'celery' program
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings.deploy")

app = Celery("hailing", broker="redis://localhost:6379")
app.config_from_object("django.conf:settings", namespace="CELERY")
```
위의 코드에서 Queue를 생성하는 부분을 추가하였다.
```python
# celery
from __future__ import absolute_import, unicode_literals

import os
from celery import Celery
from kombu import Queue

# Defined dafault django setting file for 'celery' program
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings.deploy")

app = Celery(
    "config",
    broker="redis://localhost:6379",
    include=["posts.tasks", "notifications.tasks"],
)
app.config_from_object("django.conf:settings", namespace="CELERY")
# 작업 큐 생성
app.conf.task_queues = (
    Queue("crawl_tasks", routing_key="crawl.#"),
    Queue("notify_tasks", routing_key="notify.#"),
)

app.autodiscover_tasks(["posts", "notifications"])

# celery 터미널 실행내용을 정의한다
@app.task(bind=True)
def debug_task(self):
    print(f"Post Request: {self.request!r}")
```

`app.autodiscover_tasks(["posts", "notifications"])` 이 부분은 이 프로젝트의 `celery.py`가 주로 배치하는 장소가 아니라 task가 잘 인식되지 않는다는 문제점 때문에 celery lib의 `autodiscover_tasks()`의 주석인 `Then calling ``app.autodiscover_tasks(['foo', 'bar', 'baz'])`` will
        result in the modules ``foo.tasks`` and ``bar.tasks`` being imported.`을 보고 추가한 부분이다.


### Task 호출시 Queue 지정

Task를 실행할 때, Queue를 지정하여 실행하면 된다.

```python
crawl_site.apply_async((site,), queue="crawl_tasks")
```

### Worker에 Queue 지정

Worker를 Queue 별로 지정하려면 celery를 실행할 때, `-Q` 옵션으로 Queue를 할당해주면 된다.

```bash
$ celery multi start worker1 worker2 -A config -Q:worker1 crawl_tasks -Q:worker2 notify_tasks
```

### Celery Daemon 수정

나는 이 프로젝트를 서버에 데몬으로 돌리고 있었으므로 데몬 파일에 수정해야 했다.

```conf
//.service
[Unit]
Description=Celery Service
After=redis-server.service

[Service]
Type=forking
User=user
Group=user
EnvironmentFile=/etc/conf.d/celery
WorkingDirectory=/home/user/src
ExecStart=/bin/bash -c '${CELERY_BIN} multi start ${CELERYD_NODES} \
  -A ${CELERY_APP} --pidfile=${CELERYD_PID_FILE} \
  --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'
ExecStop=/bin/bash -c '${CELERY_BIN} multi stopwait ${CELERYD_NODES} \
  --pidfile=${CELERYD_PID_FILE}'
ExecReload=/bin/bash -c '${CELERY_BIN} multi restart ${CELERYD_NODES} \
  -A ${CELERY_APP} --pidfile=${CELERYD_PID_FILE} \
  --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'

[Install]
WantedBy=multi-user.target
```

```conf
//.conf
CELERYD_NODES="w1"
CELERY_BIN="/home/user/.virtualenvs/api/bin/celery"
CELERY_APP="config"
CELERY_CHDIR="/home/user/src/"
# How to call manage.py
CELERYD_MULTI="multi"
CELERYD_OPTS="--time-limit=300 --concurrency=3"

# - %n will be replaced with the first part of the nodename.
# - %I will be replaced with the current child process index
#   and is important when using the prefork pool to avoid race conditions.
CELERYD_PID_FILE="/var/run/celery/%n.pid"
CELERYD_LOG_FILE="/var/log/celery/%n%I.log"
CELERYD_LOG_LEVEL="INFO"

# you may wish to add these options for Celery Beat
CELERYBEAT_PID_FILE="/var/run/celery/beat.pid"
CELERYBEAT_LOG_FILE="/var/log/celery/beat.log"
```

위의 파일들을 아래와 같이 수정했다.

```conf
//.service
[Unit]
Description=Celery Service
After=redis-server.service

[Service]
Type=forking
User=user
Group=user
EnvironmentFile=/etc/conf.d/celery
WorkingDirectory=/home/user/src
ExecStart=/bin/bash -c '${CELERY_BIN} multi start ${CELERYD_NODES} \
  -A ${CELERY_APP} ${CELERYD_QUEUE} ${CELERYD_OPTS} \
  --pidfile=${CELERYD_PID_FILE} --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL}'
ExecStop=/bin/bash -c '${CELERY_BIN} multi stopwait ${CELERYD_NODES} \
  --pidfile=${CELERYD_PID_FILE}'
ExecReload=/bin/bash -c '${CELERY_BIN} multi restart ${CELERYD_NODES} \
  -A ${CELERY_APP} --pidfile=${CELERYD_PID_FILE} \
  --logfile=${CELERYD_LOG_FILE} --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'

[Install]
WantedBy=multi-user.target
```

```conf
//.conf
CELERYD_NODES="crawler notificator"
CELERY_BIN="/home/user/.virtualenvs/api/bin/celery"
CELERY_APP="config"
CELERY_CHDIR="/home/user/src/"
# How to call manage.py
CELERYD_MULTI="multi"
CELERYD_QUEUE="-Q:crawler crawl_tasks -Q:notificator notify_tasks"
CELERYD_OPTS="--time-limit=300"

# - %n will be replaced with the first part of the nodename.
# - %I will be replaced with the current child process index
#   and is important when using the prefork pool to avoid race conditions.
CELERYD_PID_FILE="/var/run/celery/%n.pid"
CELERYD_LOG_FILE="/var/log/celery/%n%I.log"
CELERYD_LOG_LEVEL="INFO"

# you may wish to add these options for Celery Beat
CELERYBEAT_PID_FILE="/var/run/celery/beat.pid"
CELERYBEAT_LOG_FILE="/var/log/celery/beat.log"
```

---
* [[YouTube] 정민영: Celery의 빛과 그림자 - PyCon Korea 2015](https://www.youtube.com/watch?v=3C8gBRhtkHk)
* [[YouTube] 셀러리 핵심과 커스터마이제이션 - 이지훈 - PyCon.KR 2019](https://www.youtube.com/watch?v=vGPyjJ1jWUs)
* [[Celery] Django 프로젝트에서 Celery Worker 제한하기](https://qwlake.github.io/etc/2020/08/04/using-routing-tasks-in-celery-on-django/)
* [[Django] Celery,Redis로 Scrapy 크롤링 주기적으로 하기](https://qwlake.github.io/django/2020/05/15/django-scrapy-with-celery-redis/)
* [Celery 에서 worker별로 task를 부여하는 방법](https://iam.namjun.kim/celery/2018/09/09/celery-routing/)
* [Celery 5.1.0 documentation - Routing Tasks](https://docs.celeryproject.org/en/master/userguide/routing.html)