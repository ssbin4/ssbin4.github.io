---  
layout: post  
title: "백준 20010(악덕 영주 혜유) 풀이"  
subtitle: "백준 20010(악덕 영주 혜유) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 20010(악덕 영주 혜유) 풀이입니다.


[백준 20010 문제링크](https://www.acmicpc.net/problem/20010)

---

* __문제풀이__

  MST+트리의 지름을 계산하는 문제이다.

  MST는 kruskal이나 prim 알고리즘을 적용하면 해결할 수 있는데, 여기서는 [링크](https://m.blog.naver.com/ndb796/221230994142)를 참조하여 kruskal 알고리즘을 적용하여 문제를 해결하였다. union, find 부분에서 오류가 나서 시간을 허비했는데, union에서 parent를 업데이트 해주는 과정에서 node번호가 아닌 getParent로 받아온 parent 값으로 업데이트를 해주어야 정상적으로 업데이트를 하는 것이 된다.(이렇게 해야 이후에 getParent 함수를 호출할 때 재귀적으로 업데이트가 일어날 수 있음)

  MST에 들어가는 edge들을 차례로 저장한 후, MST에서 트리의 지름을 계산해주면 된다. 트리의 지름을 구하는 문제는 [백준 1167](https://www.acmicpc.net/problem/20010)의 문제와 동일하고, 최대 길이 방향으로 dfs를 두 번 진행해주면 구할 수 있다.

  [1167 풀이](https://ssbin4.github.io/algorithms/2021/02/11/algorithms-problems-bakejoon1167/)

  MST의 총 cost 합과 MST의 지름을 차례대로 출력해주면 정답이다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <vector>
  #include <tuple>
  #include <algorithm>
  #include <utility>

  using namespace std;

  int n,k;
  tuple<int,int,int> edge[1000000];
  vector<pair<int,int>> mstEdge[1000];
  bool visited[1000];
  int parent[1000];
  long long mstSum=0;
  int edgeNum=0;
  int maxNode;
  long long maxLength=0;

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
    for(int i=0;i<mstEdge[src].size();i++)
    {
      int dst=mstEdge[src][i].first;
      int dist=mstEdge[src][i].second;
      dfs(dst,cost+dist);
    }
  }

  int getParent(int node)
  {
    if(parent[node]==node)
    {
      return node;
    }
    else
    {
      parent[node]=getParent(parent[node]);
      return parent[node];
    }
  }

  void unionParent(int node1, int node2)
  {
    int parent1=getParent(node1);
    int parent2=getParent(node2);
    if(parent1<parent2)
    {
      parent[parent2]=parent1;
    }
    else
    {
      parent[parent1]=parent2;
    }
  }

  bool findParent(int node1,int node2)
  {
    int parent1=getParent(node1);
    int parent2=getParent(node2);
    return parent1==parent2;
  }

  void makeMST()
  {
    for(int i=0;i<k;i++)
    {
      int node1=get<1>(edge[i]);
      int node2=get<2>(edge[i]);
      int cost=get<0>(edge[i]);
      if(!findParent(node1,node2))
      {
        unionParent(node1,node2);
        mstSum+=cost;
        edgeNum++;
        mstEdge[node1].push_back(make_pair(node2,cost));
        mstEdge[node2].push_back(make_pair(node1,cost));
        if(edgeNum==n-1)
        {
          return;
        }
      }
    }
  }

  int main()
  {
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    cin>>n>>k;
    for(int i=0;i<n;i++)
    {
      parent[i]=i;
    }
    for(int i=0;i<k;i++)
    {
      int a,b,c;
      cin>>a>>b>>c;
      edge[i]=make_tuple(c,a,b);
    }
    sort(edge,edge+k);
    makeMST();
    cout<<mstSum<<endl;
    dfs(0,0);
    for(int i=0;i<n;i++)
    {
      visited[i]=false;
    }
    dfs(maxNode,0);
    cout<<maxLength;
  }
  ```