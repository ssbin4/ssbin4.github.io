---  
layout: post  
title: "백준 1238 풀이"  
subtitle: "백준 1238 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 1238(파티) 풀이입니다.


[백준 1238 문제링크](https://www.acmicpc.net/problem/1238)

---

* __문제풀이__

  임의의 directed graph에서 주어진 node를 대상으로 나머지 node에서 해당 node까지의 왕복 거리 합이 최대가 되는 node를 구하는 문제이다. 이 때 왕복 거리는 최단 거리를 기준으로 한다.

  이 문제는 두 가지 거리,
  
  1. 해당 node로부터 다른 모든 node까지의 최단 거리
  2. 다른 모든 node로부터 해당 node까지의 최단 거리

  2가지를 구하여 그 합이 최대가 되는 index를 출력하여야 한다. 

  1번은 single source shortest path, 2번은 single destination shortest path에 해당한다. 1번과 같은 경우는 여러 가지 알고리즘이 존재하지만, 여기서는 min-heap 기반의 dijkstra를 이용하였다. dijkstra에 대한 코드는 다음 링크를 참조하였다.

  [dijkstra algorithm](https://m.blog.naver.com/ndb796/221234424646)

  min-heap을 표현하기 위해 STL의 priority_queue를 이용했는데 이 때, compare 구조체를 따로 선언하여 원하는 정렬 상태를 따로 명시해주었다.

  2번과 같은 경우는 사실상 1번과 같다고 할 수 있는데, 원래의 그래프에서 edge의 방향만 반대로 된 reversed graph를 구한 후 해당 그래프에서 1번과 동일하게 dijkstra를 적용하면 되기 떄문이다. 

  이렇게 두 번 dijkstra algorithm을 적용하여 오고 가는 최단 거리를 구한 후 이 거리의 합이 최대가 되는 index를 출력하면 정답이다.


---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <utility>
  #include <vector>
  #include <queue>

  using namespace std;

  vector<pair<int,int>> original[10001];
  vector<pair<int,int>> reversed[10001];
  int timeToX[10001];
  int timeFromX[10001];

  struct compare
  {
    bool operator()(const pair<int,int> &x,const pair<int,int> &y)
    {
      return x.second > y.second;	
    }
  };

  void dijkstra(int src,string direction)
  {
    priority_queue<pair<int,int>,vector<pair<int,int>>,compare> pq;
    int *time;
    vector<pair<int,int>> *edge;
    if(direction=="to")
    {
      time=timeToX;
      edge=reversed;
    }
    else if(direction=="from")
    {
      time=timeFromX;
      edge=original;
    }
    time[src]=0;
    pq.push(make_pair(src,0));
    while(!pq.empty())
    {
      int node=pq.top().first;
      int curTime=pq.top().second;
      pq.pop();
      if(time[node]<curTime)
      {
        continue;
      }
      for(int i=0;i<edge[node].size();i++)
      {
        int next=edge[node][i].first;
        int nextTime=curTime+edge[node][i].second;
        if(nextTime<time[next])
        {
          time[next]=nextTime;
          pq.push(make_pair(next,nextTime));
        }
      }
    }
  }

  int main()
  {
    int n,m,x;
    cin>>n>>m>>x;
    for(int i=0;i<m;i++)
    {
      int src,dst,time;
      cin>>src>>dst>>time;
      original[src].push_back(make_pair(dst,time));
      reversed[dst].push_back(make_pair(src,time));
    }
    for(int i=1;i<=n;i++)
    {
      timeToX[i]=1000000000;
      timeFromX[i]=1000000000;
    }
    dijkstra(x,"to");
    dijkstra(x,"from");
    int maxTime=0;
    for(int i=1;i<=n;i++)
    {
      int sum=timeToX[i]+timeFromX[i];
      if(sum>maxTime)
      {
        maxTime=sum;
      }
    }
    cout<<maxTime;
  }
  ```