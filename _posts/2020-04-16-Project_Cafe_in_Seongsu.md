---
title:  "성수동에서 핫한 카페는?"
excerpt: "성수동 카페 별점 분석하기"

categories:
  - projects
tags:
  - Study
  - Statistics
  - Dataanalysis
  - Python
  - folium
---
> 내가 거주하고있는 성수동에는 카페가 참 많다.  
> 재생 인테리어의 매력이 깃든 어니언부터, 오픈 당일 인스타그래머들을 몇 시간 씩 줄세운 블루보틀 1호점까지.  
> 힙스터와 커피성애자들이 모이는 성수동에서, 가장 핫한 카페는 어디일까?

**프로젝트 목적: 성수동에서 별점이 높은 카페 찾아보기**
코드: [링크 클릭!](https://nbviewer.jupyter.org/gist/Sean-Parkk/91bdac286735e8c86e2d473251aa4f7a#1.-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%88%98%EC%A7%91%ED%95%98%EA%B8%B0)  
깃허브에서 보기: [링크 클릭!](https://github.com/Sean-Parkk/Projects/blob/master/cafe_in_seongsu/cafe_in_seongsu.ipynb)
# 1. 데이터 수집하기
* 데이터는 카카오맵을 기준으로 수집한다.
  * 구글 맵을 안 쓴 이유는 카페의 절대적인 수가 많기 때문이다.
  * 네이버맵은 평점이 조회하기 불편하게 제공된다.
* 일단 지도에 카페를 검색해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 1.png?raw=true)
* 한 눈에 봐도 엄청 많은 카페들.. 조금 과장하면, 한 걸음 걸을 때마다 새로운 카페가 나타난다.


![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 2.png?raw=true)
* 위 이미지는 하나의 카페에 들어있는 정보들인데, 여기서 필요한 정보만 선택하여 데이터를 수집하자.
* 이름, 주소, 평점, 평점 수, 리뷰 수 총 5개의 정보를 수집해오겠다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 3.png?raw=true)
* 필요한 정보를 모두 크롤링하여 가져왔다.
  * BeautifulSoup, Folium라이브러리를 활용했다.
* 이제 분석하기 좋은 상태로 데이터를 가공해보자.

# 2. 데이터 전처리
## 2-1. 결측값 처리
* 먼저, 몇 개의 데이터가 수집되었는지 확인해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 4.png?raw=true)
* 총 505개의 카페가 수집되었다.
* 그런데, address를 보면, 505개가 아닌 504로, 결측값이 있는 것을 볼 수 있다.
* 어떤 카페인지 확인해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 5.png?raw=true)
* 조회해보니, 코지카페라는 곳이고 평점, 리뷰 등이 0으로 분석에 영향을 주지 않을 것 같다.
* 이 카페는 삭제하고 다음 단계로 넘어가자.

## 2-2. 텍스트 처리
* 데이터를 다시 한 번 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 6.png?raw=true)
* 총 3개의 컬럼의 추가 처리를 해줘야할 것 같다.
  1. address
    * 텍스트의 앞부분 (지번) 을 삭제해주자.
  2. score_cnt, review_cnt
    * 숫자형 데이터인데, 문자가 섞여서 숫자로 인식하지 못한다.
    * 문자를 제거하고, 숫자로 변환하자.
      * 현업에서도 문자와 숫자가 섞여있어, 이를 처리해야 하는 경우가 많다.
      * 이런 경우, 정규식을 활용하면 매우 쉽고 빠르게 처리할 수 있다.
```python
# 1. address에서 (지번) 삭제
for row in df_raw.index:
    df_raw.address[row] = df_raw.address[row][5:]
df_raw.head()

# 2. score_cnt, review_cnt에서 숫자만 유지
import re
p = re.compile('\D')
for row in df_raw.index:
    df_raw.loc[row, 'score_cnt'] = p.sub('', df_raw.loc[row, 'score_cnt'])
    df_raw.loc[row, 'review_cnt'] = p.sub('', df_raw.loc[row, 'review_cnt'])
df_raw.head()
```

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 8.png?raw=true)
* 원했던 대로 데이터가 처리된 것을 확인할 수 있다.

# 3. 카페 분석하기
* 준비는 끝났다. 이제 친구에게 자신있게 추천해줄 만한 카페를 찾아보자.

## 3-1. 별점이 높은 카페 찾기
* 일단, 별점이 가장 높은 카페를 조회해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 9.png?raw=true)
* 단순히 별점 순으로 조회하니, 평가가 적게 된 카페들이 상위에 랭크되어있다.
  * 이러면 신뢰성이 낮아지니, 랭킹 기준을 정해서 다시 조회해보자.
* 평가 수가(score_cnt)가 평균 이상인 데이터들을 기준으로 정렬해보면 어떨까?

### 3-1-1. 필터 1) 평가 수(score_cnt)
* 평가 수가 0개인 카페를 제외한, 평균 평가 수는 7건이었다.
* 평균보다 많은 평가를 받은 카페를 다시 정렬해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 10.png?raw=true)
* 나는 처음들어보는 **스텀필즈커피**가 1위이다. 찾아보니, 오피스 1층에 있는 카페이다.
* **브루잉세레모니**는 이번에 처음 알았는데, 찾아보니 감성이 장난이 아니다..
* 전 직장 다닐 때 자주 다녔던 **카페라잌유**도 평점이 높다.
  * 참고로 카페라잌유 시그니처 음료(라떼였던것같은데!) 참 맛있었던 기억이 있다!
- - - - -
* 근데 자세히 보니, 스텀필즈커피는 평가는 16건인데, 리뷰(블로그 후기 등)은 0건이다.
  * 이러면 친구한테 자신있게 추천을 못할 것 같다.
  * 우리 모두 카페나 맛집을 갈 때 블로그 글은 한 번씩 찾아보지 않는가?
* 좀 더 자신감을 얻기 위해, **리뷰 수로 추가 필터링을 해보자.**

### 3-1-2. 필터 2) 리뷰 수(review_cnt)
* 리뷰 수가 중앙값인 94개 이상인 카페들로 기준을 정했고, 기준을 토대로 필터링하여 정렬해보았다.
  * 왜 중앙값으로 했는지 궁금하다면? -> [자세히 보기](https://nbviewer.jupyter.org/gist/Sean-Parkk/91bdac286735e8c86e2d473251aa4f7a#1.-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%88%98%EC%A7%91%ED%95%98%EA%B8%B0)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 11.png?raw=true)
* (짝짝짝) **커피오스**가 2차 필터링 결과 가장 평이 높은 카페였다.
  * 몰랐던 카페였는데 코로나가 잠잠해지면 가봐야겠다 ㅋㅋ
* **치카치카**는 찾아보니 사실 성수동 메인 거리(서울숲, 카페거리 등)에 있는 곳은 아니었다.
  * 하지만 마카롱의 엄청난 비주얼이 날 반겨줬다.
* **리도 엘리펀트**는 내부 인테리어도 예쁘지만, 바람이 불 때 루프탑에 앉아있으면 세상을 가진 기분이다.
* **로우스탠드**는 친한 전 직장 동료분이 가장 좋아하는 커피집.
  * 성수동은 지역 특성상 공간이 좁은 카페가 많은데, 이 곳은 맛과 공간, 감성을 모두 담은 카페
* **커피식탁**은 전 직장 동료분께서 운영하고 계신데, 모든 음료가 맛있다!!
  * 아기자기한 분위기는 뽀나스

## 3-2. 가장 유명한 카페 찾기
* 이제 친구한테 어떤 카페를 추천해야하는지는 알아냈다.
* 그러면 **성수동에서 제일 유명한 카페는 어디일까?**
  * 후기가 많으면 그만큼 유명하다는 것을 반증하는 것 아닐까?
* 후기가 가장 많은 카페를 알아보고, 그 카페의 별점을 알아보자.
