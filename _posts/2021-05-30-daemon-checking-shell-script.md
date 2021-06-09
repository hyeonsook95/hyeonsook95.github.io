---
layout: post
title: "Cron으로 주기적으로 데몬 확인하기"
date: 2021-05-30
tag:
 - shell
 - cron
 - linux
 - daemon
---

크롤링 데몬이 죽었다. 사이트 서비스는 죽지 않아서, 데몬이 죽은지 2일이 지난후에 알아차렸다. 메모리 누수와 관련된 문제인데, 원인을 아직 정확히 파악하진 못했고, 우선 데몬이 죽으면 조치를 취하도록 해야했다.

## 대략적인 알고리즘

1. 매 1분마다 데몬이 죽었는지 확인한다.
~~2. 데몬이 죽었을 경우, log files을 이메일로 보낸다(아직 미완).~~
3. 데몬을 재실행한다.

## Shell Script

### 변수
```bash
# 변수선언(여백없이)
daemon=""

# shell 명령어의 결과를 변수로 선언
daemon=`systemctl list-units --state failed | grep 'nginx|celery' | wc -l`

# 변수사용
echo $daemon
```

### 조건문
```bash
# 같을 때| 다를 때
if [ -eq|-ne ];then
  // 로직
fi
```

### 권한 변경

쉘을 실행시키기 위해서는 실행권한을 부여해줘야 한다.

```bash
$ chmod +x daemoncheck.sh
```

## Deamon

### 상태확인

systemd를 관리하는 systemctl 명령어를 사용해 데몬의 상태를 확인할 수 있다.

```bash
# list-units [PATTERN...]         List units currently in memory
$ systemctl list-units
#     --state=STATE    List units with particular LOAD or SUB or ACTIVE state
$ systemctl list-units --state=failed
# egrep: Search for PATTERN in each FILE.
$ systemctl list-units --state=failed | egrep 'celery'
# wc: Print newline, word, and byte counts for each FILE, and a total line if
# more than one FILE is specified.  A word is a non-zero-length sequence of
# characters delimited by white space.
$ systemctl list-units --state=failed | egrep 'celery' | wc
# -l, --lines            print the newline counts
$ systemctl list-units --state=failed | egrep 'celery' | wc -l
```

### 실행, 재실행
```bash
# 실행
$ systemctl start
# 재실행
$ systemctl restart
```

## Cron

cron은 유닉스 계열 운영 체제의 시간 기반 잡 스케줄러이다. crontab을 통해 설정할 수 있다.

### Crontab

crontab으로 주기적으로 실행하도록 설정할 수 있다.

```bash
# crontab 편집하기
$ crontab -e
```

편집기를 열면 보이는 안내대로 `분 시 일 월 요일 사용자 실행명령` 순서대로 선언하고 cron을 재실행시키면 된다.

데몬 검사 작업은 1분마다 검사하도록 할 것이다.

```vim
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command

* * * * * /scripts/daemoncheck.sh
```

저장을 한 후 cron 서비스를 재실행해준다.

```bash
$ systemctl restart cron
```

---
참고
* https://jhnyang.tistory.com/68
* https://ko.wikipedia.org/wiki/Cron
* bash --help