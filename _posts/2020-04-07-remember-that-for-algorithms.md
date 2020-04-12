---
layout: post
title:  "Algorithms을 위해 외워야 할 것들"
date:   2020-04-07
excerpt: "그래프, DFS, BFS"
categories: 
 - Algorithms
tag:
 - algorithms
---

## 그래프
* 그래프는 `인접 행렬`, `인접 리스트`, `간선 리스트`로 나타낼 수 있음
* `인접 행렬`: O(V^2)
* `인접 리스트`: O(E)

```c++
bool a[2000][2000];           // 인접행렬
vector<int> g[2000];          // 인접리스트
vector<pair<int, int>> edges; // 간선리스트
```

## DFS
* 깊이 우선 탐색
* 스택, 재귀함수로 구현 가능.

```c++
#include <vector>
using namespace std;
vector<int> g[1001]; // 인접리스트 형식으로 나타낸 그래프
bool check[1001]={false,}; // 탐색했는지를 나타내는 bool 배열

void dfs(int from)
{
    check[from] = true;
    for(int i=0, max=g[from].size; i<max; i++)
    {
        // from의 i번째 간선의 정점을 탐색하지 않았다면
        if(!check[g[from][i]])
        {    
            // i번째 간선의 정점을 dfs로 넘긴다.
            dfs(g[from][i]);
        }
    }
}
```

## BFS
* 너비 우선 탐색
* 큐로 구현 가능.

```c++
#include <vector>
#include <queue>
using namespace std;
vector<int> g[1001]; // 인접리스트 형식으로 나타낸 그래프
bool check[1001]={false,}; // 탐색했는지를 나타내는 bool 배열

void bfs(int from)
{
    queue<int> to; //탐색을 기다리는 노드를 저장하는 큐
    to.push(from);
    check[from] = true;
    //다음 탐색할 정점이 없을 때 종료
    while(!to.empty())
    {
        //다음 탐색할 정점을 to의 push값인 front로 지정
        from = to.front();
        to.pop();
        for(int i=0, max=g[from].size(); i<max; i++){
            //from의 i번째 간선의 정점을 탐색하지 않았다면
            if(!check[g[from][i]])
            {
                //탐색할 노드에 from의 i번째 간선의 정점을 push
                to.push(g[from][i]);
                //큐에 들어간 값은 이미 탐색한 것이므로 from의 i번째 정점의 check 값을 true로 바꿈
                check[g[from][i]] = true;
            }
        }
    }
}
```