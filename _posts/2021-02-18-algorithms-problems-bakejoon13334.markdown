---  
layout: post  
title: "백준 13334(철로) 풀이"  
subtitle: "백준 13334(철로) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 13334(철로) 풀이입니다.


[백준 13334 문제링크](https://www.acmicpc.net/problem/13334)

---

* __문제풀이__

  왼쪽, 오른쪽 구간의 여러 쌍과 길이 d를 입력받았을 때, 길이 d의 철로 중 양쪽 끝이 모두 철로 내에 포함되는 최대 구간의 개수를 구하는 문제이다.

  라인 스위핑 + 우선순위 큐를 사용하는 문제이며, 라인 스위핑 기법을 사용하는 문제는 이전에 접해본 적이 없어서 처음에 풀기가 상당히 어려웠다. [링크](https://chanhuiseok.github.io/posts/baek-28/)를 참조하여 문제에 대한 힌트를 얻었는데, 코드로 구현하는 것은 어렵지 않았다.

  먼저, 주어진 양 구간을 오른쪽 끝의 오름차순으로 정렬해준다. 이후, 정렬된 구간의 첫번째부터 라인 스위핑을 통해 탐색하면서, 우선순위 큐에 왼쪽 끝의 위치를 push해준다. 이후, 우선순위 큐의 top 항목을 꺼내어 가능한 왼쪽 끝 부분까지 커버 가능한지를 보고 그렇지 않다면 해당 항목을 pop한다. 오른쪽 끝의 오름차순으로 정렬되어 있으므로, 현재 line에서 왼쪽 끝을 커버하지 못한다면 다음 line에서도 해당 구간은 커버할 수 없기 때문이다. 이렇게 계속 pop을 하다, 어느 구간에서 왼쪽 끝까지 커버 가능하거나, 우선순위 큐가 비어있다면 탐색을 중단한다. 이 때, 우선순위 큐에 남아있는 원소의 개수가 현재 line의 오른쪽 끝에서 왼쪽 어느 line까지 커버 가능한 구간의 개수이다.

  이런식으로 계속하여 오른쪽 끝을 기준으로 오름차순으로 정렬된 구간을 라인스위핑하면서 line 개수의 최대값을 저장해나가며 마지막에 maxLine의 값을 출력해주면 된다.

  이 때, 주의해야 할 점은
  1) 우선순위 큐에서 greater<int>를 명시하지 않으면 디폴트로 내림차순으로 정렬되므로 꼭 이를 명시해주어야 하고,
  2) 구간의 (왼쪽 끝, 오른쪽 끝) 순서로 입력되지 않고 임의로 입력되므로 입력 시 왼쪽, 오른쪽을 min, max 함수를 통해 다시 찾아주어야 하며,
  3) 한 오른쪽 끝에서 커버 가능한 구간의 개수를 찾을 때, priority queue가 비었는지 확인하는 것을 체크해주어야 
  에러가 발생하지 않는다. 

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <utility>
  #include <algorithm>
  #include <queue>

  using namespace std;

  bool compare(pair<int,int> a, pair<int,int> b)
  {
    if(a.second==b.second)
    {
      return a.first<b.first;
    }
    return a.second<b.second;
  }

  int main()
  {
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    int n, distance;
    cin>>n;
    pair<int,int> location[100000];
    priority_queue<int,vector<int>,greater<int>> left;
    for(int i=0;i<n;i++)
    {
      int house,office;
      cin>>house>>office;
      location[i]=make_pair(min(house,office),max(house,office));
    }
    cin>>distance;
    sort(location,location+n,compare);
    int maxLine=0;
    for(int i=0;i<n;i++)
    {
      left.push(location[i].first);
      int right=location[i].second;
      while(!left.empty()&&left.top()<right-distance)
      {
        left.pop();
      }
      int line=left.size();
      if(line>maxLine)
      {
        maxLine=line;
      }
    }
    cout<<maxLine;
  }
  ```