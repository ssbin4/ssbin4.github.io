---  
layout: post  
title: "백준 1202(보석 도둑) 풀이"  
subtitle: "백준 1202(보석 도둑) 풀이"  
categories: algorithms
tags: ps
comments: true  
---  
  
> 백준 1202(보석 도둑) 풀이입니다.


[백준 1202 문제링크](https://www.acmicpc.net/problem/1202)

---

* __문제풀이__

  주어진 가방에는 각 가방의 용량보다 무게가 적은 보석 단 한 개만을 담을 수 있을 때, 보석 가치의 총합이 최대가 되도록 하는 문제이다. 처음 이 문제를 얼핏 보았을 때는 knapsack 알고리즘을 이용하는 것이라고 생각했지만, 보석을 여러 개 고르는 것이 아니라 한 개씩만 고르기 때문에 이는 다른 방식으로 더 쉽게 해결할 수 있다.

  처음에, 각 가방의 용량을 오름차순으로, 각 보석은 무게의 오름차순으로 정렬한다. 기본적인 알고리즘은 용량이 증가하는 순서대로 가방을 탐색하면서, 각 가방 용량보다 무게가 적은 보석들 중 가장 가치가 높은 것을 greedy하게 넣으면 된다. 이 때, 보석들의 가치를 내림차순으로 유지하기 위해 우선순위 큐를 사용한다.

  처음에 에러가 난 부분이 두 군데 있었는데, 먼저 우선순위 큐가 비어있을 경우 단순히 break하면 이후의 가방들은 아예 무시해버리는 것이 되므로 이를 continue를 통해 다음 가방도 체크해주어야 한다. 또한, 보석 가치의 총합을 담을 변수는 단순히 int로 선언하면 오버플로가 발생할 수 있으므로 long long과 같은 것으로 선언해주어야 한다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <utility>
  #include <algorithm>
  #include <queue>

  using namespace std;

  pair<int,int> gems[300000];
  int bags[300000];
  priority_queue<int> value;

  int main()
  {
    int n,k;
    cin>>n>>k;
    for(int i=0;i<n;i++)
    {
      cin>>gems[i].first>>gems[i].second;
    }
    for(int i=0;i<k;i++)
    {
      cin>>bags[i];
    }
    sort(bags,bags+k);
    sort(gems,gems+n);
    int ptr=0;
    long long sum=0;
    for(int i=0;i<k;i++)
    {
      int capacity=bags[i];
      while(ptr<n&&gems[ptr].first<=capacity)
      {
        value.push(gems[ptr].second);
        ptr++;
      }
      if(value.empty())
      {
        continue;
      }
      sum+=value.top();
      value.pop();
    }
    cout<<sum;
  }
  ```