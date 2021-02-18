---  
layout: post  
title: "백준 9328(열쇠) 풀이"  
subtitle: "백준 9328(열쇠) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 9328(열쇠) 풀이입니다.


[백준 9328 문제링크](https://www.acmicpc.net/problem/9328)

---

* __문제풀이__

  2차원 지도 상에서 빈 곳으로 또는 열쇠로 문을 열어 이동하면서 획득할 수 있는 문서의 개수를 출력하는 문제이다. 처음 문제를 보았을 때는 아주 복잡해 보였지만, BFS를 사용하라는 힌트를 보고 이를 사용하니 생각보다 쉽게 풀 수 있었다.

  먼저 전역변수로 boolean type의 keys 배열을 두어 각 알파벳에 대한 열쇠의 획득 여부를 표시하고, BFS를 위한 queue인 areaToSearch에는 탐색할 지역의 좌표를 저장한다. 또, visited 배열에는 해당 좌표를 방문했는지 여부를 저장하고, vector 배열인 doors에는 각 알파벳에 대한 문의 좌표를 저장하는 것으로 한다.

  BFS의 초기 조건을 위해 테두리의 모든 좌표를 queue에 넣어두고, queue의 front 값을 보면서 하나씩 탐색을 진행해 나가면 된다. 다음과 같은 조건에 따라 탐색을 진행한다.

  1. 좌표의 값이 빈칸인 경우

    현재 좌표는 pop 후 상하좌우의 값을 push한다. 이 때 push 할 때는 항상 배열을 벗어난 값이 아닌지 체크해주는 것이 필요하다.

  2. 좌표의 값이 벽인 경우

    벽은 통과할 수 없으므로 그 즉시 pop한다.

  3. 좌표의 값이 문인 경우

    keys 배열을 체크해서 해당 문에 대한 열쇠를 이미 가지고 있는 경우는 해당 좌표를 pop 후 상하좌우의 좌표를 push한다. 만약, 아직 열쇠를 가지고 있지 않은 경우라면 해당 좌표를 pop하고 doors 배열에 이 좌표를 기록해 두어 나중에 열쇠가 발견되었을 때 문을 다시 체크해 볼 수 있도록 한다.

  4. 좌표의 값이 열쇠인 경우

    해당되는 keys 값을 true로 하고, doors 배열에 저장된 해당 열쇠에 해당되는 문에 대한 좌표를 다시 queue에 넣어준다. 이 과정을 통해 추후에 열쇠가 발견되더라도 모든 탐색을 할 수 있다.

  5. 좌표의 값이 문서인 경우

    문서 개수를 1 늘리고 현재 좌표는 pop 하면서 상하좌우의 좌표를 push한다.

  이러한 5가지 조건에 따라 queue가 비어있을 때까지 계속 탐색을 진행해주면 된다. 이후 BFS를 통해 찾은 문서의 총 개수를 하나씩 출력한다. 이 때, 테스트케이스가 여러 개이므로 전역변수로 설정한 keys, doors나 queue와 같은 것들은 초기화하는 것이 필수적이다. 




---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <queue>

  using namespace std;

  int height,width;
  char map[100][100];
  bool keys[26];
  queue<pair<int,int>> areaToSearch;
  pair<int,int> direction[4]={make_pair(0,-1),make_pair(0,1),make_pair(-1,0),make_pair(1,0)};
  bool visited[100][100];
  vector<pair<int,int>> doors[26];

  void pushLocation(int row,int col)
  {
    for(int i=0;i<4;i++)
    {
      int nextRow=row+direction[i].first;
      int nextCol=col+direction[i].second;
      if(nextRow>=0&&nextRow<height&&nextCol>=0&&nextCol<width)
      {
        if(visited[nextRow][nextCol]==true)
        {
          continue;
        }
        visited[nextRow][nextCol]=true;
        areaToSearch.push(make_pair(nextRow,nextCol));
      }
    }
  }

  void solve()
  {
    int documents=0;
    cin>>height>>width;
    for(int i=0;i<height;i++)
    {
      for(int j=0;j<width;j++)
      {
        cin>>map[i][j];
        visited[i][j]=false;
      }
    }
    string preKey;
    cin>>preKey;
    if(preKey!="0")
    {
      for(int i=0;i<preKey.length();i++)
      {
        keys[preKey[i]-'a']=true;
      }
    }
    for(int i=0;i<height;i++)
    {
      areaToSearch.push(make_pair(i,0));
      visited[i][0]=true;
      areaToSearch.push(make_pair(i,width-1));
      visited[i][width-1]=true;
    }
    for(int i=1;i<width-1;i++)
    {
      areaToSearch.push(make_pair(0,i));
      visited[0][i]=true;
      areaToSearch.push(make_pair(height-1,i));
      visited[height-1][i]=true;
    }
    while(!areaToSearch.empty())
    {
      pair<int,int> location=areaToSearch.front();
      char ch=map[location.first][location.second];
      if(ch=='*')
      {
        areaToSearch.pop();
      }
      else if(ch>='a'&&ch<='z')
      {
        keys[ch-'a']=true;
        for(int i=0;i<doors[ch-'a'].size();i++)
        {
          areaToSearch.push(doors[ch-'a'][i]);
        }
        pushLocation(location.first,location.second);
        areaToSearch.pop();
      }
      else if(ch=='.')
      {
        pushLocation(location.first,location.second);
        areaToSearch.pop();
      }
      else if(ch>='A'&&ch<='Z')
      {
        if(keys[ch-'A'])
        {
          pushLocation(location.first,location.second);
          areaToSearch.pop();
        }
        else
        {
          doors[ch-'A'].push_back(location);
          areaToSearch.pop();
        }
      }
      else if(ch=='$')
      {
        documents++;
        pushLocation(location.first,location.second);
        areaToSearch.pop();
      }
    }
    cout<<documents<<endl;
  }

  int main()
  {
    ios::sync_with_stdio(false);
    cin.tie(NULL);
    int n;
    cin>>n;
    for(int i=0;i<n;i++)
    {
      for(int i=0;i<26;i++)
      {
        keys[i]=false;
        doors[i].clear();
      }
      solve();
      while(!areaToSearch.empty())
      {
        areaToSearch.pop();
      }
    }
  }
  ```