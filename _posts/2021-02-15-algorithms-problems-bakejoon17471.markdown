---  
layout: post  
title: "백준 17471(게리맨더링) 풀이"  
subtitle: "백준 17471(게리맨더링) 풀이"  
categories: algorithms
tags: ps
comments: true  
---  
  
> 백준 17471(게리맨더링) 풀이입니다.


[백준 17471 문제링크](https://www.acmicpc.net/problem/17471)

---

* __문제풀이__

  그래프를 두 부분으로 나눌 때, 각 부분에는 최소 한 개의 node를 가지게 하면서 각 부분은 서로 연결되도록 만드는 쌍들 중 node의 key 값의 차가 최소일 때, 그 차를 출력하는 문제이다.

  node 수가 최대 10개로 비교적 작으므로 brute force를 이용하여 그래프를 나눌 수 있는 모든 쌍에 대해 node 합의 차를 구한다.

  그래프의 edge는 vector 배열의 형태로 구현하였으며, 각 그래프 양분을 탐색하는 것은 비트마스크를 이용하여 for문으로 해결한다. 양분한 각 집합 내에서 모든 원소들을 방문할 수 있는지를 dfs를 통해 탐색하는 과정이 필요하며, 모든 원소를 방문하지 못하거나 어느 집합의 원소 개수가 0이라면 그 차를 임의의 큰 값 10000으로 두어 업데이트가 일어나지 않도록 하였다. 

  visited 배열에서는 각 dfs마다 방문 여부를 저장하고, section 배열에서는 각 집합마다 들어있는 node의 index를, secMask 배열에서는 i번째 node가 0, 1 어느 집합에 속하는지를, popSum 배열에서는 각 0, 1 집합의 node의 key 값의 합을 저장해 나간다. dfs 함수에서는 인자로 secNum을 받아 secMask 배열을 통해 같은 집합에 속하는 원소만을 탐색하도록 하며, 전역변수를 초기화하는 것을 꼭 해주어야 한다.

  minDiff에 최소 차를 업데이트 해주면서 minDiff가 10000이면 -1을, 그렇지 않으면 minDiff를 출력해주면 된다. 

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <vector>

  #define INF 10000

  using namespace std;

  int n;
  int population[11];
  vector<int> edge[11];
  bool visited[11];
  vector<int> section[2];
  int secMask[11];
  int popSum[2];

  void dfs(int node,int secNum)
  {
    if(visited[node]==true)
    {
      return;
    }
    visited[node]=true;
    popSum[secNum]+=population[node];
    for(int i=0;i<edge[node].size();i++)
    {
      if(secMask[edge[node][i]]==secNum)
      {
        dfs(edge[node][i],secNum);
      }
    }
  }

  int check(int set)
  {
    for(int i=1;i<=n;i++)
    {
      visited[i]=false;
    }
    section[0].clear();
    section[1].clear();
    for(int i=n;i>=1;i--)
    {
      int bit=set&1;
      section[bit].push_back(i);
      secMask[i]=bit;
      set>>=1;
    }
    if(section[0].size()==0||section[1].size()==0)
    {
      return INF;
    }
    popSum[0]=popSum[1]=0;
    dfs(section[0].front(),0);
    dfs(section[1].front(),1);
    for(int i=1;i<=n;i++)
    {
      if(!visited[i])
      {
        return INF;
      }
    }
    if(popSum[0]==0||popSum[1]==0)
    {
      return INF;
    }
    else
    {
      return abs(popSum[0]-popSum[1]);
    }
  }

  int main()
  {
    cin>>n;
    for(int i=1;i<=n;i++)
    {
      cin>>population[i];
    }
    for(int i=1;i<=n;i++)
    {
      int cnt;
      cin>>cnt;
      for(int j=0;j<cnt;j++)
      {
        int neighbour;
        cin>>neighbour;
        edge[i].push_back(neighbour);
      }
    }
    int minDiff=INF;
    for(int i=1;i<=(1<<n)-1;i++)
    {
      int diff=check(i);
      if(diff<minDiff)
      {
        minDiff=diff;
      }
    }
    if(minDiff==INF)
    {
      minDiff=-1;
    }
    cout<<minDiff;
  }
  ```