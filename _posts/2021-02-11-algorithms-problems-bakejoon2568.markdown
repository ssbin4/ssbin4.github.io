---  
layout: post  
title: "백준 2568 풀이"  
subtitle: "백준 2568 풀이"  
categories: algorithms
tags: problems
comments: true  
---  
  
> 백준 2568(전깃줄-2) 풀이입니다.


[백준 2568 문제링크](https://www.acmicpc.net/problem/2568)

---

* __문제풀이__

  [전깃줄 1 문제](https://www.acmicpc.net/problem/2565)의 심화 문제이다. A 전봇대와 B 전봇대를 연결하는 전깃줄이 서로 겹치지 않도록 유지하기 위해 잘라내야 하는 전깃줄의 최소 개수와 번호를 출력해야 한다. 기본적으로 LIS 알고리즘을 사용하는 문제이다.
  
  전깃줄 1 문제와의 차이점은 전깃줄 1은 최소 개수만 출력하는 반면, 전깃줄 2에서는 잘라내야 하는 번호까지 출력해야 하며, 전깃줄 1에서는 개수가 100개로 제한되어 있지만 전깃줄 2에서는 10만 개로 대폭 늘어났다. 따라서 이전 문제에서는 O(N<sup>2</sup>)이 걸리는 LIS 알고리즘을 사용하면 해결할 수 있었지만, 본 문제에서는 O(N<sup>2</sup>)의 알고리즘은 시간 초과가 발생하며 대신에 O(NlogN) 알고리즘을 사용하여야 한다. 따라서, [LIS 알고리즘](https://seungkwan.tistory.com/8)의 글을 참고하여 코드를 작성하였다. 해당 링크에서 LIS에 해당하는 수열의 index를 구하는 것까지 자세하게 나와있으므로 참고하면 좋을 것 같다.

  기본적으로 O(NlogN)의 LIS 알고리즘은 LIS를 유지하면서 해당 번호를 삽입할 수 있는 lower_bound를 찾아나가는데, 이렇게 삽입되는 위치를 index 배열에 담아두고 LIS 알고리즘 적용 후 index 배열의 끝부분부터 target number를 찾아나가면 LIS에 담긴 index만을 찾아낼 수 있다. 정답에서 요구하는 것은 LIS에 포함되지 않는 것의 번호이므로 이러한 것들만을 vector에 담아 다시 끝부터 출력하면 오름차순으로 정답을 출력할 수 있다.

---

* __소스코드__ 

  ```c++
  #include <iostream>
  #include <vector>
  #include <algorithm>
  #include <utility>


  using namespace std;

  pair<int,int> line[100000];
  vector<int> lis;
  int index[100000];
    
  int main()
  {
    ios_base :: sync_with_stdio(false);
    cin.tie(NULL);
    cout.tie(NULL);
    int num;
    cin>>num;
    for(int i=0;i<num;i++)
    {
      int left,right;
      cin>>left>>right;
      line[i]=make_pair(left,right);
    }
    sort(line,line+num);
    lis.push_back(line[0].second);
    index[0]=0;
    for(int i=1;i<num;i++)
    {
      if(lis.back()<line[i].second)
      {
        lis.push_back(line[i].second);
        index[i]=lis.size()-1;
      }
      else
      {
        auto it=lower_bound(lis.begin(),lis.end(),line[i].second);
        *it=line[i].second;
        index[i]=it-lis.begin();
      }
    }
    cout<<num-lis.size()<<"\n";
    int target=lis.size()-1;
    vector<int> answer;
    for(int i=num-1;i>=0;i--)
    {
      if(index[i]==target)
      {
        target--;
      }
      else
      {
        answer.push_back(line[i].first);
      }
    }
    for(int i=answer.size()-1;i>=0;i--)
    {
      cout<<answer[i]<<"\n";
    }
  }
  ```