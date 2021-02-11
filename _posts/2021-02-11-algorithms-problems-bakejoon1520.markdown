---  
layout: post  
title: "백준 1520 풀이"  
subtitle: "백준 1520 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 1520(내리막길) 풀이입니다.


[백준 1520 문제링크](https://www.acmicpc.net/problem/1520)

---

* __문제풀이__

  M x N 지도의 왼쪽 위에서 출발하여 오른쪽 아래까지 도착하는 경로 중 내리막길(항상 값이 작은 곳으로만 이동)의 수를 구하는 문제이다.

  문제가 까다로운 부분이 최단경로가 아닌, 모든 경로의 수를 구해야 하므로 단순하게 수를 구할 수 없다는 점이었다. 

  dfs와 dp를 이용하면 문제를 해결할 수가 있는데, 먼저 해당 지역부터의 내리막길 수를 저장할 dp 배열을 두고 모두 -1로 초기화한다. -1은 아직 해당 지역에 방문한 적이 없다는 뜻이며 dfs 함수에서 dp 배열을 업데이트 해나간다.
  
  dfs 함수의 리턴값은 해당 지역에서의 dp 배열 값으로 하여 한 지역마다 상하좌우의 4개 구역을 dfs 하도록 한다. 이 때, 한 번 방문한 지역은 재탐색하지 않고 dp 배열에 저장된 값을 바로 불러올 수 있도록 한다. 상하좌우의 탐색을 용이하게 하기 위해 delta 배열을 두어 for문으로 탐색을 할 수 있도록 하였다. 

  dfs(1,1)을 호출하여 재귀적으로 탐색을 거친 후 이 값을 출력하면 정답이 된다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <algorithm>
  #include <utility>

  using namespace std;

  int cnt=0;
  int m,n;
  int height[501][501];
  int dp[501][501];
  pair <int,int> delta[4]={make_pair(0,1),make_pair(0,-1),make_pair(1,0),make_pair(-1,0)};

  int dfs(int row,int col)
  {
      if(row==m&&col==n)
      {
          return 1;
      }
      if(dp[row][col]==-1)
      {
          dp[row][col]=0;
          for(int i=0;i<4;i++)
          {
              int nextRow=row+delta[i].first;
              int nextCol=col+delta[i].second;
              if(nextRow>=1&&nextRow<=m&&nextCol>=1&&nextCol<=n)
              {
                  if(height[nextRow][nextCol]<height[row][col])
                  {
                      dp[row][col]+=dfs(nextRow,nextCol);
                  }
              }
          }
      }
      return dp[row][col];
  }

  int main()
  {
      cin>>m>>n;
      for(int i=1;i<=m;i++)
      {
          for(int j=1;j<=n;j++)
          {
              cin>>height[i][j];
              dp[i][j]=-1;
          }
      }
      cout<<dfs(1,1);
      return 0;
  }
  ```