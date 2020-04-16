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
코드: [링크 클릭!](https://nbviewer.jupyter.org/gist/Sean-Parkk/7264e35c764a53ef85ef3ba0dc626ed8)  
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
  * 왜 중앙값으로 했는지 궁금하다면? -> [자세히 보기](https://nbviewer.jupyter.org/gist/Sean-Parkk/7264e35c764a53ef85ef3ba0dc626ed8)

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

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 12.png?raw=true)
* 성수동 카페 3대장인 대림창고, 어니언, 블루보틀이 자리매김했다.
* 개인적으로 성수동에서 가장 맛있는 커피집이라고 생각하는 메쉬커피도 6위로 높다. (괜히 뿌듯)

- - - -

* 이 리스트를 보고있자니, 평점 수와 후기 수가 나뉘어있어 보기가 불편하다.
* 이를 위해, 인지도라는 지표를 추가해보자.

### 3-2-1. 인지도 지표 추가
> **인지도 점수(Awareness)** = 평가 수 + (리뷰 수/10)

* 위와 같이 지표를 정의하고자 한다.
  * 평가 수가 많을 때 유명하다고 생각하고, 리뷰는 가산점이라고 기준을 세웠다.
    * 이 기준은 당연히 매우 주관적이다!!
    * 더 적합한 기준이 있다면 알려주세요!!

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 13.png?raw=true)
* 순위 변동이 조금 있었지만 **어니언, 대림창고, 블루보틀 3대장이 여전히 상위권을 지키고 있다.**
* 인지도 지표를 통해 **가장 수혜를 본 카페는 어반소스** 일 것 같다.
  * 8위에서 5위까지 올라왔다.
  * 어반소스는 아기자기한 느낌보다는 넓고, 조명, 간판 등이 트렌디하다.
  * 언급했듯, 성수동은 작은 카페가 많은데, 어반소스는 여러 사람들과 모임을 가지기에도 적당하다.
* **메쉬커피가 순위권에서 사라졌다!**
  * 찾아보니, 16위까지 밀려났다.
  * 아는 사람들만 아는, 아직 많이 알려지지 않은 카페라서 그런가? 라는 생각이 들었다.
  * 여담으로, 산미있는 커피를 좋아한다면, 메쉬커피는 꼭 마셔봐야한다고 생각한다.
* 그러면 이제 인지도와 평점을 시각화해보자.

### 3-2-2. 유명한 카페의 별점은?
![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 14.png?raw=true)
* 인지도가 50 이상으로 **유명한 카페들은 대부분 3-4점에 머무르고 있다.**
* 그런데, 세 번째로 유명함에도 불구하고 **블루보틀은 2점대 후반으로 10위권 중 가장 낮다.**
  * 이유로는 공간 활용, 맛 등 여러가지가 있겠지만, 이 포스트에서는 자세하게 다루진 않겠다.

## 3-3. 은근히 넓은 성수동, 어디로 가야할까?
* 애인이 오늘 '성수동 놀러 가자' 라고 얘기했다고 가정해보자.
* 즐거운 데이트를 위해 카페를 찾아놨는데, 영업 휴일 혹은 만석 등의 이유로 찾아놓은 카페를 못가면 머리가 하얘질 것이다.
  * 이런 경우 정말 많다. 특히 요샌 사람이 굉장히 많아졌다.
  * 근데 이럴 때 그 근처 아무데나 갔다가 낭패를 볼 수도 있다.
* 어떻게 해야 **계획이 틀어져도 근처에 좋은 카페를 발견할 수 있는 확률을 높일 수 있을까?**
* 성수동의 카페를 지도에 시각화하여 알아보자.

### 3-3-1. 카페의 지리 정보 찾기
* googlemaps라이브러리를 통해, 카페들의 지리 좌표를 얻어오자.

```python
from tqdm import tqdm_notebook

# 혹시 모를 사태를 대비하여 원본을 복사하여 사용
df = df_raw.copy()

# 구글맵API를 활용하여 각 카페의 주소에 해당하는 지리 정보를 얻어오기
for row in tqdm_notebook(df.index):
    try:
        geo = gmaps.geocode(str(df.loc[row, 'address']))
        df.loc[row, 'lat'] = geo[0].get('geometry')['location']['lat']
        df.loc[row, 'lng'] = geo[0].get('geometry')['location']['lng']
    except:
        df.loc[row, 'lat'] = np.nan
        df.loc[row, 'lng'] = np.nan
df.head()
```

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 15.png?raw=true)
* 지리 정보(lat, lng)를 잘 얻어왔다.
* 혹시 null값이 있는지 찾아보자.

```python
df.isnull().sum()
```

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 16.png?raw=true)
* null값이 없으므로, 바로 지도 시각화를 진행해보자.

### 3-3-2. 어디가 카페가 많을까?
* 일단 주변에 카페가 많으면 내가 가고싶었던 곳에 못가도 대안이 많아진다.
* 어느 지역에 카페가 많은지 살펴보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 17.png?raw=true)
* 일단, 카페가 많은건 알겠다. 정말 많다.
* 근데, 어디가 밀집되어있는지 한 눈에 파악하기는 힘들다.
* 더 직관적으로 파악할 수 있도록 시각화해보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 18.png?raw=true)
* 지도 스타일을 조금 바꿔봤다.
  * 카페라는 보물을 찾아주는 보물 지도 느낌..?
* **서울숲 위쪽 부근에(왼쪽 핑크 동그라미)** 카페가 밀집되어 있는 것을 볼 수 있다.
  * 저 지역을 가보면 알겠지만, 사람보다 카페가 더 많다.
  * 일단 서울숲에 갈 계획이 있다면, 저쪽 부근을 중심으로 데이터 코스를 짜는 것이 바람직해보인다.
* 두 번째로는, **성수역(가운데 핑크 동그라미)** 근처에 카페가 모여있다.
  * 이 곳을 대표하는 카페는 대림창고가 있다.
  * 유명한 곳을 꼭 가봐야겠어! 라는 생각이 있다면 이 곳을 추천한다.
* 세 번째는, **건대입구역** 근처다.
  * 사실 건대입구를 성수동이라고 칭하지는 않는다.
  * 실제로 카페 분위기도 굉장히 다르지만, 번외편으로 추가해보았다.

### 3-3-3. 어디를 가야 평점이 높은 카페가 많을까?
* 사실, 위 지도에서 보았듯 성수동에는 어딜 가나 카페가 많다.
  * 정말 궁금한 것은 어디를 가야 평점이 높은 카페가 많냐는 것이다!
* **평점이 높은 카페는 주로 어느 지역에 많은지** 알아보자.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/cafeinseongsu/이미지 19.png?raw=true)
* 건대부근을 제외하고 보다 성수동에 집중할 수 있도록 지도를 확대했다.
* 색이 진할수록 평점이 높고, 인지도가 높을수록 원이 크다.
* 1) **좀 더 유명하고 검증된 카페를 가고 싶다면,**
  * 오른쪽 중간, 성수역 근처 대림창고 부근을 가는 것이 좋아보인다.
  * 상대적으로 인지도가 높은(원이 큰) 카페가 많이 보이고, 평점도 높다
* 2) **인지도보다는 남들이 잘 모르는 카페를 찾아보고 싶다면,**
  * 서울숲 위쪽 거리가 좋은 선택이 될 것으로 보인다.
  * 전반적으로 인지도가 있는 카페는 1번의 선택보다는 적어보이지만, 카페가 더 밀집되어있다.
  * 그리고 중간정도의 인지도를 가진, 평점이 좋은 카페들이 모여있는것을 볼 수있다.

# 4. 결론
* 사실 이 분석은 육하나이라는 카페에서, '성수동에 카페가 왜 이렇게 많아!' 라는 생각에서 시작됐다.
* 단순한 질문에서 시작했지만,
  1. 어떤 카페가 가장 평점이 좋은지,
  2. 어떤 카페가 가장 유명한지,
  3. 성수동의 어느 지역을 가야 실패없이 많은 카페를 구경하고 경험할 수 있는지 알 수 있었다.
* 비록 부족한 분석력이었지만, 누군가에게는 흥미로운 스토리로 다가갔으면 좋겠다.
