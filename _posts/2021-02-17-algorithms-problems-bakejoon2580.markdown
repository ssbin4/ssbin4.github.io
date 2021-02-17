---  
layout: post  
title: "백준 2580(스도쿠) 풀이"  
subtitle: "백준 2580(스도쿠) 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 2580(스도쿠) 풀이입니다.


[백준 2580 문제링크](https://www.acmicpc.net/problem/2580)

---

* __문제풀이__

  문제 이름 그대로 스도쿠 판을 입력받아 빈칸을 채우는 문제이다. 모든 경우의 수를 시도할 경우 시간이 너무 많이 소요되기 때문에 문제에서 알려준 것과 같이 백트래킹을 사용하여야 한다.

  먼저 빈칸의 좌표를 vector로 저장하여 vector의 원소를 하나씩 백트래킹하는 과정으로 문제를 해결한다. backTrack 함수에서 index를 인자로 받아 각 index 번째의 빈칸에 대해 1~9까지 원소를 채우고 해당 원소가 타당할 경우 다음 원소를 backTrack하는 과정을 반복한다. 모든 원소에 대해 타당한 값이 들어갔다면 success 전역변수 값을 true로 하여 빈칸을 채우는 것을 성공했음을 알 수 있도록 한다. 만약 도중에 실패한다면 다시 값을 0으로 되돌려 주어야 한다. 

  isValid 함수에서는 row, col 번쨰 좌표에 num 값을 넣었을 때 스도쿠의 조건을 만족하는지를 검사한다. 스도쿠의 정의에 따라 각 행, 열에 num 값이 중복되지 않는지, 각 3x3 block에서 num 값이 중복되지 않는지를 체크한다. 

  이렇게 backTrack 함수를 실행시켜 빈칸을 채우고 나면 printBoard 함수를 호출하여 채워진 스도쿠 판을 출력하면 된다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <vector>
  #include <utility>

  using namespace std;

  int board[9][9];
  vector<pair<int,int>> blank;
  bool success=false;

  bool isValid(int row,int col,int num)
  {
    for(int i=0;i<9;i++)
    {
      if(board[row][i]==num||board[i][col]==num)
      {
        return false;
      }
    }
    int rowBlock=(row/3)*3;
    int colBlock=(col/3)*3;
    for(int i=rowBlock;i<rowBlock+3;i++)
    {
      for(int j=colBlock;j<colBlock+3;j++)
      {
        if(board[i][j]==num)
        {
          return false;
        }
      }
    }
    return true;
  }

  void backTrack(int index)
  {
    if(index==blank.size())
    {
      success=true;
      return;
    }
    int row=blank[index].first;
    int col=blank[index].second;
    for(int i=1;i<=9;i++)
    {
      if(isValid(row,col,i))
      {
        board[row][col]=i;
        backTrack(index+1);
        if(success)
        {
          return;
        }
        else
        {
          board[row][col]=0;	
        }
      }
    }
  }

  void printBoard()
  {
    for(int i=0;i<9;i++)
    {
      for(int j=0;j<9;j++)
      {
        cout<<board[i][j]<<" ";
      }
      cout<<endl;
    }
  }

  int main()
  {
    for(int i=0;i<9;i++)
    {
      for(int j=0;j<9;j++)
      {
        cin>>board[i][j];
        if(board[i][j]==0)
        {
          blank.push_back(make_pair(i,j));
        }
      }
    }
    backTrack(0);
    printBoard();
  }
  ```