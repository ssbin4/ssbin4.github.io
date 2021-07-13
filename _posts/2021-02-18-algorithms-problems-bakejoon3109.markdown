---  
layout: post  
title: "백준 3109(빵집) 풀이"  
subtitle: "백준 3109(빵집) 풀이"  
categories: algorithms
tags: ps
comments: true  
---  
  
> 백준 3109(빵집) 풀이입니다.


[백준 3109 문제링크](https://www.acmicpc.net/problem/3109)

---

* __문제풀이__

  2차원 지도에서 왼쪽 열에서 오른쪽 열로 가는 경로의 개수를 구하는 문제이다. 이 때, 각 경로는 겹쳐서는 안되고 지도 상에서 X로 표시된 부분은 지나갈 수 없으며 항상 오른쪽 위, 오른쪽, 오른쪽 아래로만 이동할 수 있다.

  문제의 힌트에서 주어진 경로는 0번째 행과 3번째 행에서 출발한 경로이지만, 이 떄 사실 1번째 행에서 출발하는 경로도 만들 수 있다. 즉, 행 번호가 증가하는 순서대로 가능한 경로가 있다면 이 경로를 우선 택하는 greedy한 방법으로 경로의 최대 개수를 구할 수 있다. 이 때, 항상 가능한 경로는 1) 오른쪽 위, 2) 오른쪽, 3) 오른쪽 아래 순으로 진행해야 겹치는 경로를 만들어내지 않을 것이므로 이 순서를 지켜주어야 한다. 이 순서를 반대로 한다면 아래 행에서 출발하는 경로를 만들어 낼 수 있음에도 불구하고 가능한 경로의 수를 줄여버리는 경우가 발생할 수 있다.

  dfs를 통해 경로를 찾으며, 전역변수 connected를 두어 이 값이 true가 될 경우 오른쪽 열에 도착한 것으로 인식하여 dfs를 중단하도록 한다. 또, visited 배열을 두어 방문점을 표시하도록 하였으며, 오른쪽 열 끝에 도착하면 pipe의 개수를 하나씩 늘리면 된다.

  모든 행에 대해 왼쪽 열 끝에서 가능한 경로를 탐색하고 pipes에 저장된 값을 출력하면 정답이다.

---

* __소스코드__ 

  ```c++
  #include <iostream>

  using namespace std;

  int r,c;
  bool map[10000][500];
  bool visited[10000][500];
  int pipes=0;
  bool connected;

  void dfs(int i,int j)
  {
    if(visited[i][j]==true)
    {
      return;
    }
    visited[i][j]=true;
    if(j==c-1)
    {
      pipes++;
      connected=true;
      return;
    }
    if(i!=0&&map[i-1][j+1])
    {
      dfs(i-1,j+1);
      if(connected)
      {
        return;
      }
    }
    if(map[i][j+1])
    {
      dfs(i,j+1);
      if(connected)
      {
        return;
      }
    }
    if(i!=r-1&&map[i+1][j+1])
    {
      dfs(i+1,j+1);
    }
  }

  int main()
  {
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    cin>>r>>c;
    for(int i=0;i<r;i++)
    {
      for(int j=0;j<c;j++)
      {
        char input;
        cin>>input;
        if(input=='X')
        {
          map[i][j]=false;
        }
        else if(input=='.')
        {
          map[i][j]=true;
        }
        visited[i][j]=false;
      }
    }
    for(int i=0;i<r;i++)
    {
      connected=false;
      dfs(i,0);
    }
    cout<<pipes;
  }
  ```