---
layout: post
title: "Celery WorkerLostError"
date: 2021-06-14
tag:
 - celery
 - python
 - pool
---

# 작성중

Celery로 크롤링을 돌려놓았는데, 크롤링이 되지 않았다. 확인해보니 Celery 데몬은 activate 했으나, top으로 확인한 결과 처리하고 있는 task가 평균 1.5였는데, 0.5로 줄어있었다. 거기다 Celery Worker의 로그는 남고 있지 않았고, Celery Beat는 정상동작하여 매 분동안 신호를 보내는 로그가 남아 있었다.

마지막으로 남은 로그는 어제 6시경으로 워커 메인로그는 `WorkerLostError`를 마지막으로 동작하고 있지 않았다.

## 로그 분석

```
[2021-06-13 18:16:39,719: ERROR/MainProcess] Task handler raised error: WorkerLostError('Worker exited prematurely: signal 6 (SIGABRT).',)
Traceback (most recent call last):
  File "~/python3.6/site-packages/billiard/pool.py", line 1267, in mark_as_worker_lost
    human_status(exitcode)),
billiard.exceptions.WorkerLostError: Worker exited prematurely: signal 6 (SIGABRT).
```

우선 billiard/pool.py의 1267 라인을 살펴보았다. `mark_as_worker_lost`라는 함수로 `WorkerLostError` Exception을 발생시키고 `job._set(None, (False, ExceptionInfo()))`을 실행한다.

이 함수는 `_join_exited_workers(self, shutdown=False)`에서 호출되었다.

> Cleanup after any worker processes which have exited due to reaching their specified lifetime. Returns True if any workers were cleaned up.  
> 워커 프로세스가 완료되어 종료할 때 정리한다. 워커가 정리되면 True를 반환한다.

> The worker may have published a result before being terminated, but we have no way to accurately tell if it did.
> So we wait for _lost_worker_timeout seconds before we mark the job with WorkerLostError.  
> 워커가 끝나기 전에 결과를 publish 하면 우리는 이것을 알 방법이 없다. 그래서 _lost_worker_timeout 초 만큼 기다렸다가 job을 WorkerLostError로 표기한다.

```python
# ready가 아니고 _worker_lost가 있으면 job의 리스트로 반복문을 돌린다.
for job in [job for job in list(self._cache.values())
            if not job.ready() and job._worker_lost]:
    now = now or monotonic()
    lost_time, lost_ret = job._worker_lost
    # job._lost_worker_timeout 보다 now - lost_time 이면,
    if now - lost_time > job._lost_worker_timeout:
        # WorkerLostError를 일으킨다.
        self.mark_as_worker_lost(job, lost_ret)

```

job의 _worker_lost의 lost_time이 _lost_worker_timeout보다 커지면, WorkerLostError를 일으킨다고 한다. 그럼 _worker_lost는 언제 설정되는가?

우선 `ApplyResult`가 생성될 때, `None`으로 초기화된다. `ApplyResult`는 `Pool.apply_async()`에 의해 반환되는 class이다. 또한, 함수 `on_job_process_lost(self, job, pid, exitcode)`가 실행되면 현재 시간이 지정된다.

`on_job_process_lost`도 `_join_exited_workers`에 의해 호출된다.

```python
...

# cleaned가 있으면
if cleaned:
    # 현재 pool의 worker의 pid들을 list화 한다.
    all_pids = [w.pid for w in self._pool]
        for job in list(self._cache.values()):
            # 시작된 것들에게 받은 응답
            acked_by_gone = next(
                # cleaned에 있거나 all_pids 안에 없는 job의 worker_pid를 돌려주고 없으면 None
                (pid for pid in job.worker_pids() if pid in cleaned or pid not in all_pids),
                None
            )
            # already accepted by process
            # process에 의해 accept 되었다면
            if acked_by_gone:
                self.on_job_process_down(job, acked_by_gone)
                # job이 ready가 아니면
                if not job.ready():
                    # 응답한 것의 exitcode를 get하거나 없으면 0
                    exitcode = exitcodes.get(acked_by_gone) or 0
                    # cleaned 리스트에서 응답한 것의 proc를 가져옴
                    proc = cleaned.get(acked_by_gone)
                    # proc가 있고, proc의 _job_terminated가 있으면
                    if proc and getattr(proc, '_job_terminated', False):
                        # job을 종료한다.
                        job._set_terminated(exitcode)
                    # proc가 없거나 _job_terminated가 False이면
                    else:
                        # lost_time을 지정하는 on_job_process_lost 함수를 실행시킨다.
                        self.on_job_process_lost(job, acked_by_gone, exitcode)

...
```

그렇다면 이건 뭐가 문제일까, `_job_terminated`가 `True`가 아니었을까? 

`_job_terminated`가 `True`가 아닌 문제라면 `terminate_job`을 호출하면 된다.

https://docs.celeryproject.org/en/3.1/userguide/workers.html

SIGABRT, 현재 프로세스가 abort 함수를 호출할 때 보냄. 비정상적인 종료가 됨. 이것을 받으면 코어 덤프하고 종료.

