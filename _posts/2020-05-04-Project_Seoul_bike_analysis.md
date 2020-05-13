---
title:  "빌릴 땐 마음대로지만 반납할 땐 아니란다."
excerpt: "따릉이 데이터 분석하기"

categories:
  - projects
tags:
  - Study
  - Statistics
  - Dataanalysis
  - Python
  - folium
---
> *성수동에는 따릉이가 정말 많다.*  
카페 거리, 서울숲 산책, 한강 자전거길 등 주변에 자전거로 즐길 콘텐츠가 많아서 일 것이다.  
반면, 성수동에서 따릉이를 타고 본가인 쌍문동까지 가면, 따릉이를 반납할 곳이 참 없다고 느꼈었다.  
**지역마다 따릉이 분포가 어떤지** 살펴보고, **어느 구역에 확충하면 좋을 지** 살펴보자.

**목적: 구별 따릉이 분포 분석하기**

코드: [링크 클릭!](https://nbviewer.jupyter.org/gist/Sean-Parkk/0d7d9567b0d8145eaf2f4fec587740f4)  
깃허브에서 보기: [링크 클릭!](https://github.com/Sean-Parkk/Projects/tree/master/Seoul_bike_analysis)

# 1. 데이터 가져오기
* 대여소와 대여소 정보는 공공데이터를 다운받아 사용했다.
  * [대여소 정보 받기](https://data.seoul.go.kr/dataList/OA-13252/F/1/datasetView.do)
    * 19.12.09 기준 데이터를 사용했다.
  * [따릉이 이용 정보 보기](https://data.seoul.go.kr/dataList/OA-15249/F/1/datasetView.do)
    * 18.12~19.11 간 데이터를 사용했다.
* 병합, 결측값 처리 등의 전처리를 해주면 아래와 같은 데이터를 얻을 수 있다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 3.png?raw=true)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 4.png?raw=true)

# 2. 데이터 분석
* 먼저, 어느 지역에 따릉이가 많이 배치되었는지 살펴보자.

## 2-1. 따릉이가 많이 배치된 지역 찾기
* 어느 지역에 따릉이가 많을까?
* 서울 중심부에 비교적 많이 배치되어있지 않을까? (정말 서울스러운 생각..)

### 2-1-1. 가장 따릉이가 많이 배치된 대여소는?
* 먼저, 자전거 배치 수를 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 5.png?raw=true)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 6.png?raw=true)
* 가장 많이 배치된 곳은 무려 40대나 배치되어있다.
  * 40대면 도로 전체에 자전거가 깔려있는건가... 아직 본 적이 없어서 궁금하다.
  * 아마 한강 근처나 자전거도로가 잘 되어있는 곳이 아닐까?
* 10대가 배치된 곳이 가장 많고, 다음으로는 15대, 20대가 많다.
* 가장 적은곳은 5대가 배치되어있다.
* **35대 이상 배치된 곳은 어디인지 살펴보자.**

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 7.png?raw=true)
* 마포구, 영등포구, 광진구 등 한강 근처이거나 자전거도로가 있는 지역이 많다.
* 근데 이렇게 텍스트로만 보니, 어디에 있는지 감이 잘 오지는 않는다.
* 지도에 나타내서 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 8.png?raw=true)
* 예상대로, 따릉이가 많이 배치된 지역은 대부분 한강 근처에 분포되어있는 것을 알 수 있다.
* 다음으로는, 어느 구에 따릉이가 많이 배치되어있는지 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 9.png?raw=true)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 10.png?raw=true)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 11.png?raw=true)

* **강남 3구(강남구, 서초구, 송파구)와 영등포구가 가장 따릉이가 많이 배치되어있다.**
* 반면 **강북은 전반적으로 따릉이 수가 적은 것을 확인할 수 있다.**
  * 그나마 제일 많은 곳은 마포구다.
* 놀랐던 것은, 내가 살고있는 **성동구도 상대적으로 따릉이가 많은 편은 아니었다!**
  * 서울숲 근처에 따릉이가 많아서 가시적으로는 굉장히 많아보였는데, 아마 서울숲 근처에만 많나보다.
  * 음.. 이렇게 끝내면 카더라니까, 실제로 **성동구에는 서울숲 근처에만 따릉이가 많은지 확인해보자!**

### 2-1-2. 성동구 내 따릉이 분포 확인해보기
* 정말 서울숲에만 많아서 많아보인건가?
* 나는 정녕 우물 안 개구리 혹은 서울숲 안 사슴이었던것인가

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 12.png?raw=true)

> 히트맵: 어느 곳에 자전거수가 많은지 확인 가능  
클러스터링(지도 내 숫자): 구역 당 대여소의 수를 확인 가능

* 서울숲 근처에는 성동구에서 가장 많은 자전거 대여소 중 하나가 있다.
* 그리고 근처에 다섯개의 대여소가 있다. 적다고 할 수는 없다.
* **하지만!**
  * 서울숲 근처에만 집중적으로 몰려있는 것은 아니다.
  * 전반적으로 고르게 퍼져있음을 확인할 수 있다.

## 2-2. 따릉이를 많이 대여,반납하는 지역 찾기
* 앞에서는  
  * 1)대여소가 어느 구에 많은지 알았고,
  * 2)서울숲 근처에 대여소가 몰려있는지 확인해보았다.
* 이제 대여, 반납 수를 분석하여 실제로 어느 구역에 따릉이가 부족한 지 분석해보자.

### 2-2-1.  따릉이를 많이 대여하는 구역은?
* 먼저, 많이 대여하는 구역은 어디인지 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 13.png?raw=true)

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 14.png?raw=true)

* 역시 강남 3구 + 영등포구가 제일 많이 대여가 되고있다.
* 금천구가 가장 대여가 적고, 의외로 용산구, 중구도 가장 대여가 적다.
* 이번에는 반납수를 볼텐데, 대여와 반납의 상관관계를 파악해보자.
  * 예상하건데, 대여가 많은 구가 당연히 반납도 많지 않을까?

### 2-2-2. 대여가 많으면 반납도 많을까?
* 어쩌면 결과가 뻔한 질문이지만, 대여가 많으면 반납도 많을지 눈으로 확인해보고싶다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 15.png?raw=true)
* 예상대로, 대여와 반납은 선형관계에 있다.
* 그럼 구역 말고 대여소별로는 어떤지 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 16.png?raw=true)
* 대여소별로도 크게 다르지 않게, 선형관계에 있다.
* 하지만, 구별로 보았을 때보다는 선형에서 조금 벗어나는 값들이 몇개 보인다.
* 대여수 / 반납수 를 계산하여 **대여보다 반납이, 혹은 반납보다 대여가 많은 대여소를 찾아보자.**

### 2-2-3. 반납 대비 대여가 높은 대여소는?
* 반납보다 대여가 많다면 따릉이 충원 지역 후보라고 볼 수 있을것이다.

```python
# 대여수를 반납수로 나눈 지표 추가
df_group['rent_return_ratio'] = df_group['rent'] / df_group['return']
```
* 반납 대비 대여가 많은 곳

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 17.png?raw=true)
* 반납 대비 대여가 적은 곳

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 18.png?raw=true)

* rent_return_ratio이라는 지표를 통해, 반납 대비 대여가 얼마나 많은지 알 수 있다.
* 가장 비율이 높은 신당 래미안 앞은, 반납 대비 대여가 5.5배나 높다.
* 반면, 가장 낮은 비율을 가진 곳은 강서구의 곰달래 문화복지센터이다.
* 4번째로 낮은 곳인 곰달래사거리도 0.72로 대여보다 반납이 많다.
* 곰달래라고 하는 곳의 근처에서 사람들이 따릉이 반납을 많이 하나보다.

- - - -
* **반납보다 대여를 2배 이상 많은 곳이 있다면, 수요-공급이 안 맞는 것 아닐까?**
* 계수가 2 이상인 곳을 찾아보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 19.png?raw=true)
* 의도한 것은 아닌데, 정확하게 50개의 대여소가 2보다 높다.
* 어느 구가 가장 많은지 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 20.png?raw=true)

* 강남구가 가장 많음을 확인할 수 있다.
* 이제 이 대여소들이 어디있는지 시각화해서 더 자세히 분석해보자.

### 2-2-3. 대여가 많은 지역 시각화하기
![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/SeoulBike/이미지 21.png?raw=true)
* 어느 구역에 따릉이를 확충해야할까?
  * 내가 담당자라면, 검은색 자전거 마크가 있는 곳을 우선적으로 검토할 것이다.
    * 검은색 마크가 있는 곳은 반납 대비 대여가 가장 높은 10곳이다.
    * 다음으로는, 대여가 많은 구 중 반납 대비 대여가 높은 곳들을 후보에 넣을 것 같다.
* 추가로, 특이점이 보인 곳들을 아래에 정리해보았다.

- - - -

* 반납 대비 대여가 많은 대여소는 **강남구에 가장 많이 밀집되어있다.**
  * 특히, **회사가 밀집된 강남구청-학동사거리 부근에 몰려있다.**
  * **출퇴근 시에 이용자가 몰려서 나타난 현상이 아닐까 추측된다.**
  * 해당 지역에 따릉이 대여소를 추가로 설치하면, 출퇴근 따릉이 전쟁이 줄어들지 않을까 싶다.
    * 어쩌면 이런 현상때문에 킥고잉과 같은 전동킥보드 공유 서비스가 강남구를 일대로 서비스를 시작했을 가능성이 있다.
* **서초구에는 가장 따릉이 부족이 심한 대여소 10곳 중 4곳이 존재한다.**
  * 특히 남부순환로에 이어져있는 두 곳의 대여소는 모두 아파트 앞이라는 공통점이 있다.
  * **주거민들이 이용하는 비율이 높을 것으로 예상된다.**
    * 추측이지만, 래미안에 살면서 강남에 회사가 있는 직장인은 웬만하면 저 대여소의 따릉이를 타고자 할 것이다.
    * 아침에 따릉이 쟁탈전에서 패하고 터벅터벅 버스를 타러 가는데, 전동 자전거 대여 서비스가 집앞에서 시작했다면?! (이렇게 충성고객이 만들어지나요)
* **영등포와 송파구는 따릉이 이용이 가장 많은 구이지만, 따릉이 부족 현상을 보이는 대여소는 단 한 곳에 그친다.**
  * 특히 영등포구는 여의도를 중심으로 자전거길이 잘 나있는데도 부족 현상이 적다는 것이 신기하다.
  * 다른 구에서 이런 구들의 따릉이 이용 행태를 벤치마킹하면 어떨까?
* **강북구와 도봉구는 서울에서 가장 따릉이 대여가 적은 곳들이고, 반납 대비 대여가 많은 대여소도 두 곳에 불과하다.**
  * 내가 성수동에서 쌍문동으로 갔을 때 반납할 대여소가 적다고 느낀것은,
    * **쌍문동은 대여량 자체가 적다보니 대여소 자체가 적었기 때문일 것이다.**
  * 아무래도 두 구역 모두 회사가 몰려있지도 않고, 한강, 공원보다는 산이 많기때문에 따릉이 이용이 적을 것 같기는 하다.

  # 3. 결론
> 이 분석은 서울숲에서 쌍문동까지 따릉이를 타고갔을때,  
'성수동엔 따릉이 대여소가 많은데 왜 쌍문동엔 별로 없을까?'라는 질문에서 시작됐다.

* 서울에서 따릉이 대여소가 어느 구역에 밀집되어있는지 살펴보았다.
  * 더 나아가서, 현재 거주하고있는 서울숲 근처에 대여소가 밀집되어있는지 확인해보았다.
* 그리고 반납 대비 대여가 높은 대여소가 어디에 있는지 찾아보았다.
  * 이 데이터를 바탕으로 어느 구역에 따릉이를 확충하면 좋을지도 분석해보았다.
* 요즘에는 모빌리티 공유 서비스가 많이 늘어나고있다.
  * 자전거, 자동차, 킥보드 등 공유되는 이동 수단도 다양해지고있다.
  * 나는 맛보기로 따릉이 데이터를 분석해보았지만,
    * 추후 모빌리티에서 나오는 다양한 데이터들을 통해 재미있는 인사이트가 많이 나올 것 같다.

*끝!!*

[자세한 코드 보러 가기 >](https://nbviewer.jupyter.org/gist/Sean-Parkk/0d7d9567b0d8145eaf2f4fec587740f4)
