---
title:  "1일 1코딩 프로젝트"
excerpt: "코딩 도장에서 수련하기"

categories:
  - projects
tags:
  - coding
  - python
  - projects
  - study
---

매일 코딩하기
-----
코딩을 공부하면서 이걸 다 언제 써먹나 싶을 때가 있다. 간단하게라도 복습할 수 있는 곳이 있으면 좋겠다고 생각을 하게 된다.

내가 공부한 [**점프 투 파이썬**](https://wikidocs.net/book/1)이라는 책(e-book)에선 [**코딩 도장**](http://codingdojang.com/)이라는 것을 추천해주고있다. 하루에 하나씩 간단한 코딩을 하기에 매우 적합한 사이트다.

코딩 도장 3단계 문제까지 푸는 것이 목표이며, 아래에 계속 업데이트 할 예정이다.  
* * *
코딩 목차
-----
1. [lv.1 Multiples of 3 and 5 ]( #multiples-of-3-and-5)



* * *
## multiples of 3 and 5
10미만의 자연수에서 3과 5의 배수를 구하면 3,5,6,9이다. 이들의 총합은 23이다. 1000미만의 자연수에서 3,5의 배수의 총합을 구하라.
>n = 1  
while n < 1000:  
&nbsp;&nbsp;&nbsp;&nbsp;  if n % 3 == 0 or n % 5 == 0:  
&nbsp;&nbsp;&nbsp;&nbsp;    result += n  
  n += 1  
print(result)
