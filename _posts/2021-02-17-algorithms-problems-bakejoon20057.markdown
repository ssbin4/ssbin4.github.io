---  
layout: post  
title: "백준 20057(마법사 상어와 토네이도) 풀이"  
subtitle: "백준 20057(마법사 상어와 토네이도) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 20057(마법사 상어와 토네이도) 풀이입니다.


[백준 20057 문제링크](https://www.acmicpc.net/problem/20057)

---

* __문제풀이__

  주어진 규칙에 따라 2차원 배열에서 이동할 때마다 각 배열 위치에 해당하는 모래가 이동하게 된다. 이 때, 밖으로 밀려난 모래의 양을 계산하는 문제이다.

  딱히 알고리즘을 적용하는 것은 아니고, 이동하는 규칙만 찾으면 풀 수 있는 문제이다. 이동하는 방향은 왼쪽, 아래, 오른쪽, 위를 계속하여 반복하게 되고, 이동하는 길이는 1, 1, 2, 2, 3, 3, ...과 같은 식으로 이어나가게 된다. 이 때, 마지막 이동에서는 이동하는 길이가 더 짧아질 수도 있으므로 꼭 현재 좌표를 업데이트 해나가면서 현재 좌표가 (0,0)인지를 확인하는 과정이 필요하다. 이 과정이 없을 떄는 (-1,0) 과 같은 곳으로 이동할 수도 있어서 무한루프를 돌 수도 있다.

  move 함수에서는 이동하는 방향을 받아 현재 좌표를 업데이트 하며 calcSand 함수에서는 현재 좌표를 기반으로 이동하는 모래의 양을 업데이트 해준다. 이 때, calcOut 함수를 호출하여 업데이트할 좌표가 valid한 값인 경우는 그대로 업데이트 하고 배열 밖인 경우는 전역변수 out에 값을 계속하여 더해준다.

  이동하는 방향에 따라 업데이트하는 배열의 좌표가 다 다르므로 이는 deltaX, deltaY, ratio 배열에 현재 좌표 기준 x 변화량, y 변화량, 이동할 모래의 비율을 저장해주어 쉽게 계산 가능하도록 한다. 방향은 0, 1, 2, 3이 각각 왼쪽, 아래, 오른쪽, 위를 나타내는 것으로 한다. 이 때, 알파 값은 바로 정의되지 않으므로 ratio 배열에는 계산하지 않고 delta 배열의 마지막 원소로 두어 8번째 원소까지 업데이트를 한 후 남은 만큼을 알파 값에 해당하는 위치로 업데이트한다. 

  현재 좌표가 (0,0)인 경우 반복문을 빠져나와 out에 저장된 값을 출력해주면 된다.

---

* __소스코드__ 

  ```c++
  #include <iostream>

  using namespace std;

  int n;
  int sand[500][500];
  int out=0;

  {% raw %}
  int deltaX[4][10]={{1,-1,2,1,-1,-2,1,-1,0,0},{-1,-1,0,0,0,0,1,1,2,1},  {-1,1,-2,-1,1,2,-1,1,0,0},{1,1,0,0,0,0,-1,-1,-2,-1}};
  int deltaY[4][10]={{1,1,0,0,0,0,-1,-1,-2,-1},{1,-1,2,1,-1,-2,1,-1,0,0},{-1,-1,0,0,0,0,1,1,2,1},  {-1,1,-2,-1,1,2,-1,1,0,0}};
  {% endraw %}
  int ratio[9]={1,1,2,7,7,2,10,10,5};
  pair<int,int> current;

  void calcOut(int row,int col,int delta)
  {
    if(row<0||row>=n||col<0||col>=n)
    {
      out+=delta;
    }
    else
    {
      sand[row][col]+=delta;
    }
  }

  void calcSand(int dir)
  {
    int sandY=sand[current.first][current.second];
    int remain=sandY;
    for(int i=0;i<9;i++)
    {
      int delta=(sandY*ratio[i])/100;
      calcOut(current.first+deltaX[dir][i],current.second+deltaY[dir][i],delta);
      remain-=delta;
    }
    calcOut(current.first+deltaX[dir][9],current.second+deltaY[dir][9],remain);
    sand[current.first][current.second]=0;
  }

  void move(int dir)
  {
    if(dir==0) //left
    {
      current.second-=1;
    }
    else if(dir==1) //down
    {
      current.first+=1;
    }
    else if(dir==2) //right
    {
      current.second+=1;
    }
    else if(dir==3) //up
    {
      current.first-=1;
    }
    calcSand(dir);
  }

  int main()
  {
    cin>>n;
    for(int i=0;i<n;i++)
    {
      for(int j=0;j<n;j++)
      {
        cin>>sand[i][j];
      }
    }
    current=make_pair(n/2,n/2);
    for(int i=0;;i++)
    {
      int dir=i%4;
      int step=(i+2)/2;
      for(int j=1;j<=step;j++)
      {
        move(dir);
        if(current.first==0&&current.second==0)
        {
          break;
        }
      }
      if(current.first==0&&current.second==0)
      {
        break;
      }
    }
    cout<<out;
  }
  ```