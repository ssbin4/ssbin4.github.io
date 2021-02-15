---  
layout: post  
title: "백준 9019(DSLR) 풀이"  
subtitle: "백준 9019(DSLR) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 9019(DSLR) 풀이입니다.


[백준 9019 문제링크](https://www.acmicpc.net/problem/9019)

---

* __문제풀이__

  두 수 A,B를 입력받아 A를 B로 바꾸기 위한 DSLR의 최소 sequence를 출력하는 문제이다.

  우선 D, S, L, R에 대한 함수를 각각 구현한 후 BFS를 통해 길이가 1, 2, 3, ...인 것을 차례대로 대입해보는 방식을 사용하였다. BFS를 사용하기 위해서 queue를 이용해 답이 나올 때까지 pop한 값에다 D, S, L, R를 붙인 값 4개를 push하도록 하였다. 이 때, 단순한 BFS로는 시간 초과 또는 메모리 초과가 발생하였는데, 이는 visited 배열을 두어 이미 탐색한 수는 queue에 push하지 않도록 하는 것을 추가하여 통과할 수 있었다. 문제의 조건에서 레지스터에는 10000 미만의 수만 저장된다고 명시되어 있으므로, visited 배열을 두는 것이 시간을 훨씬 단축시킬 수 있다.

  queue에는 (레지스터의 값, DSLR string)을 pair로 저장하여 레지스터의 값을 일종의 캐시로 사용하여 D, S, L, R 중 하나의 연산만 덧붙여 값을 저장하도록 하여 빠른 계산이 가능하다. 이렇게 무한루프를 돌다가 캐시에 저장된 값과 B의 값이 일치할 경우 string을 출력하면 정답이다.


---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <queue>
  #include <string>
  #include <utility>

  using namespace std;

  int d(int a)
  {
      return (a*2)%10000;
  }
  int s(int a)
  {
    if(a==0)
    {
      return 9999;
    }
    else
    {
      return a-1;
    }
  }
  int l(int a)
  {
      int thousand=a/1000;
      int remain=a%1000;
      return remain*10+thousand;
  }
  int r(int a)
  {
      int one=a%10;
      int remain=a/10;
      return remain+1000*one;
  }

  int main()
  {
    int t;
    int init,fin;
    cin>>t;
    bool visited[10001];
    for(int i=1;i<=t;i++)
    {
      for(int i=0;i<10001;i++)
      {
        visited[i]=false;
      }
      cin>>init>>fin;
      queue<pair<string,int>> q;
      q.push(make_pair("",init));
      while(1)
      {
        string str=q.front().first;
        int cache=q.front().second;
        if(cache==fin)
        {
          cout<<str<<endl;
          break;
        }
        q.pop();
        int d_cache=d(cache);
        int s_cache=s(cache);
        int l_cache=l(cache);
        int r_cache=r(cache);
        if(!visited[d_cache])
        {
          visited[d_cache]=true;
          q.push(make_pair(str+"D",d_cache));
        }
        if(!visited[s_cache])
        {
          visited[s_cache]=true;
          q.push(make_pair(str+"S",s_cache));
        }
        if(!visited[l_cache])
        {
          visited[l_cache]=true;
          q.push(make_pair(str+"L",l_cache));
        }
        if(!visited[r_cache])
        {
          visited[r_cache]=true;
          q.push(make_pair(str+"R",r_cache));
        }
      }
    }
  }
  ```