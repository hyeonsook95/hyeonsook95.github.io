---
layout: post
title: "게시글 점수 계산 코드 수정하기"
date: 2021-06-18
tag:
 - python
---


그동안 게시글의 점수를 계산하는 함수가 지저분하다는 생각을 자주 했었다. 그런데 마땅히 어떻게 공부해야할지 몰랐었는데, 최근 builtin 함수를 공부하기도 하고 알고리즘 공부를 하면서, 코드를 수정할 방안을 생각해내고 드디어 수정했다.

수정하고 나니 굉장히 코드를 멍청하게 짰구나.. 싶은 생각이 든다. 파이썬 공부 좀 열심히 하자...


## 수정 전

```python

def top_posts(self, request):
    ...

    try:
        posts = Post.objects.filter(created__range=[startdate, enddate])

        posts_dict = {}

        for post in posts:
            grade = (
                post.specific_views * TOP_VIEW
                + (post.specific_likes - post.specific_dislikes) * TOP_LIKE
                + post.specific_comments * TOP_CMTS
                + post.hitcounts.count() * TOP_HAIL
            )
            posts_dict[post] = grade

        posts = [
            key
            for (key, value) in sorted(
                posts_dict.items(), reverse=True, key=lambda item: item[1]
            )
        ]
    except ValueError:
        ...

    serializer = PostSerializer(posts[:10], many=True)
    return Response(serializer.data)
```

## 수정 후

```python
def top_posts(self, request):
    def get_score(post):
        # post들의 Top 스코어를 계산하는 함수
        return (
            post.specific_views * TOP_VIEW
            + (post.specific_likes - post.specific_dislikes) * TOP_LIKE
            + post.specific_comments * TOP_CMTS
            + post.hitcounts.count() * TOP_HAIL
        )
        
    ...

    try:
        posts = Post.objects.filter(created__range=[startdate, enddate])

        results = sorted(posts, reverse=True, key=get_score)[:10]

    except ValueError:
        ...
        
    serializer = PostSerializer(results, many=True)
    return Response(serializer.data)
```
