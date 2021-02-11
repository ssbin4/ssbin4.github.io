---  
layout: post  
title: "백준 1167 풀이"  
subtitle: "백준 1167 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 1167(트리의 지름) 풀이입니다.


[백준 1167 문제링크](https://www.acmicpc.net/problem/1167)

---

* __문제풀이__

  트리에서 임의의 두 node 사이의 거리 중 최댓값(트리의 지름)을 구하는 문제이다.

  먼저, 각 트리의 node마다 연결된 node가 주어지는데 이러한 edge들에 대한 정보는 각 node마다 vector를 두어 vector에 <연결된 node, edge의 weight>를 쌍으로 저장하도록 하였다.

  처음 생각한 방법은 1번 node에서 연결된 모든 node를 통해 최대 거리를 구한 후 가장 큰 2개의 값을 더하면 된다고 생각했지만, 이 방법에는 반례가 존재하는 것을 확인하였다.
  [반례 링크](https://www.acmicpc.net/board/view/49946)

  정답은 임의의 node에서 최대 거리까지 dfs를 한 후 다시 해당 정점에서 최대 거리로 dfs를 한 번 더 진행하는 방법이다. 이 경우 처음 dfs에서는 최대 거리를 발견하지 못 할 수도 있지만, 2번째의 dfs에서는 항상 최대 거리를 보장할 수 있게 된다.

  dfs를 진행하기 위해서는 dfs 함수에서 src node의 index와 현재까지의 거리 합인 cost를 인자로 받아 이웃한 node들로 dfs를 진행해 나가면 된다. 전역변수 maxLength와 maxNode를 두어 최대 거리와 최대 거리가 되는 node의 index를 저장해 나간다. 이 때, 첫 번째 dfs 종료 후 꼭 visited 배열을 다시 초기화하여 제대로 탐색이 이루어질 수 있도록 해주어야 한다. 두 번째 dfs는 maxNode에서 시작하여 dfs를 마치면 maxLength에 저장된 값을 출력하면 된다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <utility>
  #include <vector>

  using namespace std;

  vector<pair<int,int>> edge[100001];
  bool visited[100001];
  int maxNode;
  int maxLength=0;

  void dfs(int src,int cost)
  {
    if(visited[src])
    {
      return;
    }
    visited[src]=true;
    if(cost>maxLength)
    {
      maxLength=cost;
      maxNode=src;
    }
    for(int i=0;i<edge[src].size();i++)
    {
      int dst=edge[src][i].first;
      int dist=edge[src][i].second;
      dfs(dst,cost+dist);
    }
  }

  int main()
  {
    int vertices;
    cin>>vertices;
    for(int i=1;i<=100000;i++)
    {
      visited[i]=false;
    }
    for(int i=0;i<vertices;i++)
    {
      int src,dst,dist;
      cin>>src;
      while(1)
      {
        cin>>dst;
        if(dst==-1)
        {
          break;
        }
        cin>>dist;
        edge[src].push_back(make_pair(dst,dist));
      }
    }
    dfs(1,0);
    for(int i=1;i<=100000;i++)
    {
      visited[i]=false;
    }
    dfs(maxNode,0);
    cout<<maxLength;
  }
  ```