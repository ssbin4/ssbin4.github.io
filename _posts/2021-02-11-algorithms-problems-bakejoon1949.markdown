---  
layout: post  
title: "백준 1949 풀이"  
subtitle: "백준 1949 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 1949(우수마을) 풀이입니다.


[백준 1949 문제링크](https://www.acmicpc.net/problem/1949)

---

* __문제풀이__

  트리에서 각 노드의 value 값의 합을 최대로 선정하되, 이웃한 node를 택할 수 없으면서 자신이 선택되지 않았을 때 이웃한 node 중 적어도 한 개는 선택되어야 하는 제약 조건이 달린 문제이다.

  이 경우 트리에서 dfs와 dp를 적용하여 문제를 해결할 수 있다. 이 때 dp 배열에는 해당 node까지 탐색을 진행했을 때 최대 value 합을 저장해야 하는데, 두 가지 경우(해당 node가 선택된 경우와 그렇지 않은 경우)를 저장하도록 한다. 이를 위해 pair를 이용하여 first 값에는 해당 node가 선택된 경우, second 값에는 해당 node가 선택되지 않은 경우를 저장한다.

  한 정점에서 시작하여 dfs를 시작한다. 첫 번째로, 해당 node가 선택된 경우는 이웃한 node는 모두 선택되지 않아야 하므로 이웃한 node들의 second 값의 합이 된다. 다음으로, 해당 node가 선택되지 않은 경우는 이웃한 node의 선택 여부에 관계받지 않으므로 이웃한 node들의 first, second 값 중 max 값을 취한 누적합을 저장하면 된다.

  마지막으로 처음 dfs를 시작한 정점의 dp 배열에 저장된 first와 second 중 max 값이 최종 결과값이 된다. 

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <vector>
  #include <algorithm>
  #include <utility>

  using namespace std;

  int population[10001];
  vector<int> edge[10001];
  pair<int,int> dp[10001];
  bool visited[10001];

  pair<int,int> dfs(int node)
  {
    int sum1=0,sum2=0;
    vector<int> neighbour=edge[node];
    if(!visited[node])
    {
      visited[node]=true;
      for(int i=0;i<neighbour.size();i++)
      {
        pair<int,int> near=dfs(neighbour[i]);
        sum1+=max(near.first,near.second);
        sum2+=near.second;
      }
      dp[node].first=population[node]+sum2;
      dp[node].second=sum1;
    }
    return dp[node];
  }
    
  int main()
  {
    ios_base :: sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);
    int n;
    cin>>n;
    for(int i=1;i<=n;i++)
    {
      visited[i]=false;
      cin>>population[i];
    }
    for(int i=1;i<n;i++)
    {
      int a,b;
      cin>>a>>b;
      edge[a].push_back(b);
      edge[b].push_back(a);
    }
    pair<int,int> answer=dfs(1);
    cout<<max(answer.first,answer.second);
  }
  ```