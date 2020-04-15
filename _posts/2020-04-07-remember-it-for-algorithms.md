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
    for(int i=0, max=g[from].size; i<max; ++i)
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
        for(int i=0, max=g[from].size(); i<max; ++i){
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

## 탐색

### 이진 탐색

* 이진 탐색은 정렬된 배열을 탐색하는 것을 가정함.
* C++에서는 `lower_bound()`와 `upper_bound()`로 대체할 수 있음.

```c++
int binsearch(const vector<int>& A, int x) {
    int n = A.size();
    int lo = -1, hi = n;
    // 탐색의 시작 인덱스가 끝 인덱스보다 크면 종료.
    while(lo + 1 < hi) {
        int mid = (lo + hi) / 2; //중간 인덱스를 구함.
        // 중간 값이 찾고자 하는 값보다 작으면
        // 찾는 값이 중간 인덱스보다 뒷 부분에 있으므로 시작 인덱스를 중간 인덱스로 함.
        if(A[mid] < x)
            lo = mid;
        // 중간 값이 찾고자 하는 값보다 크면
        // 찾는 값이 중간 인덱스보다 앞 부분에 있으므로 끝 인덱스를 중간 인덱스로 함.
        else
            hi = mid;
    }
    // 값을 찾았을 때, 조건은 A[mid]=x가 되므로 이 값을 포함하고 있는 조건의 실행문
    // hi=mid에서 hi가 탐색 값의 인덱스임.
    return hi;
}
```

## 정렬

* 속도
    * 삽입 < 선택

### 선택 정렬

* 모든 i에 대해 A[i..N-1]에서 가장 작은 원소를 찾은 후, 이것을 A[i]에 넣는 것을 반복.
* 수행 시간 O(N^2)

```c++
void selectionSort(vector<int>& A) {
    for(int i=0; i<A.size(); ++i) {
        int minIndex = i;
        for(int j=i; j<A.size(); ++j) {
            if(A[j] < A[minIndex])
                A[minIndex] = A[j];
        }
        swap(A[i], A[minIndex]);
    }
}
```

### 삽입 정렬

* 전체 배열 중 정렬되어 있는 부분 배열에 새 원소를 끼워넣는 일을 반복.
* 수행 시간 O(N)~O(N^2)

```c++
void insertionSort(vector<int>& A) {
    for(int i=0; i<A.size(); ++i) {
        // i의 앞 부분은 정렬된 부분 배열임.
        // A[0..i-1]에 A[i]를 끼워 넣음.
        int j=i;
        // j가 0과 같거나 작으면 종료.
        // AND 이전 값이 현재 값보다 작으면 올바르게 정렬된 배열이므로 종료.
        while(j > 0 && A[j-1] > A[j]) {
            swap(A[j-1], A[j]);
            --j;
        }
    }
}
```

## ETC

### 빈도수 측정하기

* 주어진 배열에서 가장 많이 등장하는 수 반환.
* 수행 시간 O(N)
    
* 모든 배열을 검사하면서 해야하는 과정이 __숫자 1의 개수 세기__, __숫자 2의 개수 세기__, __숫자 3의 개수 세기__, ... 등으로 굉장히 많았는데 각 과정을 배열의 한 부분으로 만들면서 특정 부분의 수를 세야할 때 이를 인덱스로 구분할 수 있도록 하여 그 인덱스번째에 할당된 과정일 때, 호출하여 값을 올릴 수 있도록 함.
    * 한 연산을 배열의 인덱스로 축약함(수의 빈도를 구하는 문제에서 수를 i로).
    * 여러 연산들에서 공통적으로 해야 하는 범위를 전체 과정으로 빼서 범위를 한 번 훑을 때 끝내도록 함.

```c++
int majority(const vector<int>& A) {
    int N = A.size();
    // count 배열의 순서를 나타내는 i는 숫자를 뜻함.
    // count[i]는 i 의 빈도수를 나타냄.
    vector<int> count(101, 0);
    for(int i=0; i<N; ++i) {
        count[A[i]]++;
    }
    // 확인한 숫자 중 빈도수가 제일 큰 것을 찾아서 majority에 저장.
    int majority = 0;
    for(int i=1; i <= 100; ++i) {
        if(count[i] > majority)
            majority = count[i];
    }
    return majority;

}
```

### 이동 평균

* 이동 평균(moving average), 시간에 따라 변화하는 값들을 관찰할 때 유용하게 사용할 수 있는 통계적 기준.
* M-이동 평균은 마지막 M개의 관찰 값의 평균으로 정의됨.
    * 새 관찰 값이 나오면 새 관찰 값을 포함하도록 바뀜.

* N 길이의 배열이 주어질 때 각 위치에서의 M-이동 평균 값 반환.
* 수행 시간 O(N)

* 측정치가 M개이면 0번 째부터 M-1까지를 더하고 M으로 나눠서 이동 평균을 구함. 이 때 새로운 M+1번 째 값이 들어오면 1번 째부터 M번째까지 더하고 M으로 나눠서 이동 평균을 구함. 그런데 이 과정을 보면 처음에는 __0~M-1까지 더하고__ 뒤에는 __1~M까지 더하는데__ 그럼 __1~M-1__ 은 두 과정 모두에서 필요함.  따라서 M+1번 째 값이 들어왔을 때는 __0~M-1까지의 값__ 에서 0값을 빼고, M값을 더한 후 M으로 나누면 된다는 것을 알 수 있음.
    * 중복된 계산을 없앰.

```c++
vector<double> movingAverage(const vector<double>& A, int M) {
    vector<double> ret; // 각 위치에서의 M-이동 평균 값을 담을 배열.
    int N = A.size();
    double partialSum = 0;
    // 처음 만들 수 있는 이동 평균 값을 구함.
    // 이 반복문에서 M-1번 째 값은 더하지 않음.
    for(int i=0; i<M-1; ++i)
        partialSum += A[i];
    // ret에 평균 값을 저장하기 전에 이동 평균 값을 계산하는 연산이 와야 함.
    // 이를 위해 위의 반복문에서 마지막 과정을 생략함.
    for(int i=M-1; i<N; ++i) {
        partialSum += A[i];
        ret.push_back(partialSum / M);
        partialSum -= A[i-M+1];
    }

    return ret;
}
```

### 소인수 분해

* 자연수 n의 소인수 분해 결과를 담은 정수 배열을 반환.
* 수행 시간 O(N-1), 이 때 N은 수의 크기를 나타냄.

```c++
vector<int> factor(int n) {
    if(n == 1) return vector<int>(1, 1); //n = 1인 경우는 예외로 함.
    vector<int> ret;
    for(int div=2; n>1; ++div) {
        while(n % div == 0) {
            n /= div;
            ret.push_back(div);
        }
    }
    return ret;
}
```