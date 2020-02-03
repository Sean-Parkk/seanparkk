---
title:  "생활데이터를 활용한 배고픔 분석입니다."
excerpt: "근데 이제 SQL과 Python을 곁들인.."

categories:
  - projects
tags:
  - projects
  - toyproject
---
##잘 시간인데 배고프다.

나는 지금 꽤 규칙적으로 생활한다. 기상 알람부터, 식사 시간 알람, 심지어 씻는 시간까지 알람 10개 이상을 맞춰놓고 살고 있다. 구직기간에 이렇게라도 안하면 늘어질 것 같다고 판단한 것이 그 이유이다.  
그런데, 나름 규칙적으로 생활하고 먹고 자는데도 **어떤 날은 자기 전 배가 고프고, 어떤 날은 배가 안고팠다.** 배가 고프다고 배달을 시켜먹기엔 돈도 부담이고 혼자 다 먹지도 못하고, 그냥 자자니 뭔가 서럽고.  

자기 전 배고픔을 조금이라도 덜 느껴보기 위해 직접 데이터를 수집하고, 분석해보았다. *(학습과 생존을 목표로?)*  


##데이터 수집하기
>기간: '19.10.23 ~ '20.01.05

1. 데이터 수집 목록
* 세 끼 식사 여부
  * 식사 양은 측정하기 어렵기 때문에, 균일하다고 가정했다.
* 아침 운동 여부
  * 아침에 하는 운동이지만, 배고픔에 영향을 끼치지 않을까 궁금하여 수집했다.
* 외부 활동 종류
  * [없음, 외출, 데이터, 축구, 친구] 다섯가지로 분류하여 수집했다.
* 걸음수
  * 스마트폰 걸음 수 측정기를 활용했다.
* 공부 시간
  * 공부 시 어플을 활용하여 공부 시간을 측정했다.

2. 데이터 수집 방법
* 수기로 직접 기록, 스마트폰 데이터 활용
![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/crawling.png?raw=true)

3. SQL로 데이터 이동
* 데이터의 수가 적어, 오류는 수기 검수
* 여행 기간 중 기록하지 못한 NULL행 제거
* **총 75개의 데이터 획득!**
![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/hungeranalysis(1).png?raw=true)


## 데이터 살펴보기!

>일할 때 다른 사람(고객)의 데이터는 자주 봤어도, 내 데이터를 이렇게 정리한 것은 처음인 것 같다. 바라보자니 궁금한 것들이 와장장 생겼다.

* 아침은 몇 번 먹었을까?
  ``` SQL
  SELECT breakfast, COUNT(breakfast) AS cnt
  FROM project
  GROUP BY breakfast
  ORDER BY breakfast;
  ```
  ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql1.png?raw=true)
  * 원래 아침을 안 먹었었는데, 아침을 먹은 날이 39일이나 된다.
    * 요리를 독학하고있는데, 자연스레 아침 식사 수가 늘어난 것 같다.



* 점심을 먹은 날, 외부 활동 별 평균 공부 시간은? 3시간 이상 한 것만 추려보기!
  ``` SQL
  SELECT activity, AVG(study) AS avg
  FROM project
  WHERE lunch = "먹음"
  GROUP BY activity
  HAVING avg >= 3;
  ```
  ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql2.png?raw=true)


* 어떤 활동을 가장 많이 했을까?
``` SQL
SELECT activity, COUNT(activity) AS cnt
FROM project
GROUP BY activity
ORDER BY 2 DESC
LIMIT 1;
```
![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql3.png?raw=true)
