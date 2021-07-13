---  
layout: post  
title: "백준 5577(RBY팡!) 풀이"  
subtitle: "백준 5577(RBY팡!) 풀이"  
categories: algorithms
tags: ps
comments: true  
---  
  
> 백준 5577(RBY팡!) 풀이입니다.


[백준 5577 문제링크](https://www.acmicpc.net/problem/5577)

---

* __문제풀이__

  연속된 4개 이상의 공의의 색깔이 같으면 문제에서 정의한 '팡!'이 일어난다. 처음에 단 한 개의 색깔만 변경할 때, 일련의 팡! 후 남은 공의 최소 개수를 구하는 문제이다.

  공의 개수가 최대 10000개 이므로 O(N^<sup>2</sup>)의 알고리즘으로 가능하다고 생각해서 모든 케이스에 대해 전부 조사를 해보는 방식으로 구현하였다. pang 함수에서 하단의 공 위치, 상단의 공 위치, 남은 공 개수를 인자로 받아 아래, 위로 연속된 같은 색깔의 공의 개수와 위치를 계산하고, 아래 위를 합친 연속되는 공의 최대 개수를 구한 뒤 이 개수가 4개 이상이라면 다시 pang을 재귀적으로 호출하는 방식이다. 이 때, 예외 경우로 맨 아래 공과 맨 위 공이 팡!에 해당하는 경우는 nextUnder, nextUp 값을 다른 값으로 변경해주어야 한다.

  모든 공에 대해 변경 가능한 2가지 경우를 시험해 본 후 최소 개수를 출력하면 정답이다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <algorithm>

  using namespace std;

  int n;
  int color[10000];

  int pang(int under,int up,int left)
  {
    int underChain=1,upChain=1;
    int nextUnder=under,nextUp=up;
    while(nextUp+1<n&&color[nextUp]==color[nextUp+1])
    {
      nextUp++;
      upChain++;
    }
    while(nextUnder>=1&&color[nextUnder]==color[nextUnder-1])
    {
      nextUnder--;
      underChain++;
    }
    int maxChain;
    if(color[up]==color[under])
    {
      maxChain=underChain+upChain-(under==up);
    }
    else
    {
      maxChain=max(upChain,upChain);
    }
    if(maxChain>=4)
    {
      if(left-maxChain<4)
      {
        return left-maxChain;
      }
      if(nextUnder==0)
      {
        nextUnder=nextUp;
      }
      else
      {
        nextUnder--;
      }
      if(nextUp==n-1)
      {
        nextUp=nextUnder;
      }
      else
      {
        nextUp++;
      }
      return pang(nextUnder,nextUp,left-maxChain);
    }
    else
    {
      return left;
    }
  }

  int makePang(int index)
  {
    int ballColor=color[index];
    int cand1,cand2;
    if(ballColor==1)
    {
      color[index]=2;
      cand1=pang(index,index,n);
      color[index]=3;
      cand2=pang(index,index,n);
    }
    else if(ballColor==2)
    {
      color[index]=1;
      cand1=pang(index,index,n);
      color[index]=3;
      cand2=pang(index,index,n);
    }
    else if(ballColor==3)
    {
      color[index]=1;
      cand1=pang(index,index,n);
      color[index]=2;
      cand2=pang(index,index,n);
    }
    color[index]=ballColor;
    return min(cand1,cand2);
  }

  int main()
  {
    cin>>n;
    for(int i=0;i<n;i++)
    {
      cin>>color[i];
    }
    int minBall=10000;
    for(int i=0;i<n;i++)
    {
      int ball=makePang(i);
      if(ball<minBall)
      {
        minBall=ball;
      }
    }
    cout<<minBall;
  }
  ```