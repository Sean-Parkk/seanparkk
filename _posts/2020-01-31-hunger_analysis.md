---
title:  "생활데이터를 활용한 배고픔 분석입니다."
excerpt: "근데 이제 SQL과 Python을 곁들인.."

categories:
  - projects
tags:
  - projects
  - toyproject
---
## 잘 시간인데 배고프다.

나는 지금 꽤 규칙적으로 생활한다. 기상 알람부터, 식사 시간 알람, 심지어 씻는 시간까지 알람 10개 이상을 맞춰놓고 살고 있다. 구직기간에 이렇게라도 안하면 늘어질 것 같다고 판단한 것이 그 이유이다.  
그런데, 나름 규칙적으로 생활하고 먹고 자는데도 **어떤 날은 자기 전 배가 고프고, 어떤 날은 배가 안고팠다.** 배가 고프다고 배달을 시켜먹기엔 돈도 부담이고 혼자 다 먹지도 못하고, 그냥 자자니 뭔가 서럽고.  

자기 전 배고픔을 조금이라도 덜 느껴보기 위해 직접 데이터를 수집하고, 분석해보았다. *(학습과 생존을 목표로?)*  


## 데이터 수집하기
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


## 데이터 살펴보기

>일할 때 다른 사람(고객)의 데이터는 자주 봤어도, 내 데이터를 이렇게 정리한 것은 처음인 것 같다. 바라보자니 궁금한 것들이 와장장 생겼다.

* 아침은 몇 번 먹었을까?
  ```
  SELECT breakfast, COUNT(breakfast) AS cnt
  FROM project
  GROUP BY breakfast
  ORDER BY breakfast;
  ```
  ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql1.png?raw=true)
  * 원래 아침을 안 먹었었는데, 아침을 먹은 날이 39일이나 된다.
    * 요리를 독학하고있는데, 자연스레 아침 식사 수가 늘어난 것 같다.



* 점심을 먹은 날, 외부 활동 별 평균 공부 시간은? 3시간 이상 한 것만 추려보기!
  ```
  SELECT activity, AVG(study) AS avg
  FROM project
  WHERE lunch = "먹음"
  GROUP BY activity
  HAVING avg >= 3;
  ```
  ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql2.png?raw=true)
  * 당연하게도, 집에만 있었을 때에 평균 공부 시간이 8시간으로 길었다.
  * 외출을 하거나 데이터를 하는 날에는 평균 3시간 정도 공부를 한다.
    * 외부 활동이 있는 날에는 3시간 동안 할 수 있는 짧은 공부거리를 하는게 좋겠다.  (파이썬 문제 풀기와 같은?)
  * 병원에 간 날은 공부를 11시간으로 꽤나 오래했다.
    * 목이 아파서 도수치료를 받았는데, 보험이 된다 한들 돈이 꽤 지출된다. *(지갑이 마르는 소리때문에 공부를 더 하게되는건 아닌지...)*



* 어떤 활동을 가장 많이 했을까?
  ```
  SELECT activity, COUNT(activity) AS cnt
  FROM project
  GROUP BY activity
  ORDER BY 2 DESC
  LIMIT 1;
  ```
  ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/sql3.png?raw=true)
  * 역시 집에 있던 날이 많았다.
  * 집에서 공부, 식사 모두 해결할 수 있어서 콩박혀있는 시간이 많았다.

## 배고픔 분석하기
> 어떤 활동이 배고픔에 영향을 끼쳤을까

**1) 식사 데이터 분석**

  * 식사 여부 분석
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph1.png?raw=true)
    * 아침 식사는 먹은날과 안먹은날의 빈도가 비슷했다.
    * 점심과 저녁은 거른 날이 거의 드물었다.

  * 식사 여부에 따른 배고픔 유무
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph2.png?raw=true)
    * 아침을 거른다고 배고픔에 큰 영향을 끼치지 않는 듯 하다.
    * 점심, 저녁 식사는 거른 날이 거의 없기 떄문에 유의미하지는 않다.
    * 저녁을 안 먹은 빈도는 낮았지만, 안 먹으면 확실히 자기 전 배가 고팠다.
    * 식사가 직접적인 원인일 것이라 생각했지만, 다른 요소가 배고픔에 더 영향을 많이 끼칠 것으로 보여진다.

**2) 아침 운동 데이터 분석**
* 아침 운동에 따른 배고픔 분석
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph3.png?raw=true)
  * 아침 운동을 한 날과 안 한 날의 빈도는 큰 차이가 없다.
  * 하지만 아침 운동을 한 날은 상대적으로 배고플 확률이 높았다.

**3) 외부 활동 데이터 분석**
* 외부 활동 수 비교 / 활동 별 배고픔 유무
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph4.png?raw=true)
    * 집에만 있던 날이 제일 많았다.
      * 외부 활동이 없음에도 절반 이상의 날들이 배가 고팠다.
      * 집에서 어떤 활동을 했는지 같이 분석할 필요가 있어보인다.
    * 외출, 데이트, 친구 등의 활동이 있는 날엔 배가 고픈 날이 상대적으로 적었다.
      * 바깥에 나가서 저녁을 먹고 들어오는 경우가 많았기 때문으로 해석된다.
      * 특히 친구를 만난 날은 100% 배가 안 고팠다. *약속은 좋은 영양소입니다*
    * 축구를 한 날은 모두 배가 고팠다.
      * 매주 목요일 밤 10시부터 12시까지 하고 있다.
      * 집에 돌아오면 새벽이어서 매번 배고픔에 허덕인 기억이 있다.
    * 병원에 갔던 날은 배가 고팠지만, 빈도도 적고 배고픔과의 큰 관계성이 없어보인다.

**4) 걸음 수 데이터 분석**
* 날짜 별 걸음 수
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph5.png?raw=true)
    * 날짜 별 데이터로는 패턴을 확인할 수 없었다.
    * 요일 별 데이터로 다시 나눠보니, 월요일에는 걸음 수가 평균 1,000걸음으로 제일 적다.
* 평균 걸음 수와 배고픔 유무
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph6.png?raw=true)
    * 많이 걸은 날이 오히려 배가 안 고팠음
      * 집에 있으면 걸을 일이 없으니, 활동 별로 나눠보면 다르게 나오지 않을까?
    * 외부 활동 별로 나누니, 외부 활동이 있던 날 많이 걸었음

**5) 공부 시간 데이터 분석**
* 공부 시간과 배고픔
    ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/graph7.png?raw=true)
    * 배가 고픈 날은 평균적으로 공부를 많이 했다.
    * 활동별로 나눠보니, 외부 활동이 없던 날 평균 공부 시간이 높았다.
