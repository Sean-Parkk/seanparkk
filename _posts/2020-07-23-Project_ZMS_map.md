---
title:  "오늘 뭐 먹지? 아니, 뭐가 있지?"
excerpt: "남부터미널 부근 음식점 분석하기"

categories:
  - ##projects
tags:
  - Study
  - Dataanalysis
  - Python
  - Folium
  - Plotly
  - Geovisualization
---
>   뭘 먹을지도 고민인데, 근처에 어떤 음식점이 있는지도 모르겠다.

남부터미널에 있는 학원에 다니며 공부하고있는데, 매일 뭘 먹을지 고민한다. 점심시간마다 뭘 먹을지 고민하는 것은 인생의 과제인듯하다.  

과제를 좀 더 수월하게 풀어내보기 위해, **남부터미널 근처 어떤 음식점이 있고, 갈 만한 맛집은 어디가 있는지** 분석해보자.

**목적: 남부터미널 부근 맛집 지도 만들기**

결과물: [링크 클릭!](https://c11.kr/ZMS_map)
깃허브에서 보기: [링크 클릭!](https://github.com/Sean-Parkk/playdata_miniproject)

## 목차
{:.no_toc}

* 목차
{:toc}

# 1. 맛집 지도 만들기
> 결과물을 먼저 공개한다. 자소서를 너무 많이 썼는지, 뭐든지 두괄식으로..

**[결과물 보러 가기(Click!)](https://c11.kr/ZMS_map)**

* 페이지에 들어가면, 음식점들의 점수와 위치를 확인할 수 있다.
  * ![image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f65fd754-5b04-4012-9469-61699e7cefac/2.gif?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20200723%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200723T074533Z&X-Amz-Expires=86400&X-Amz-Signature=c86cbcc75106224360286719d04796500463b4bcd2d9d5834178fe21a9ab07fe&X-Amz-SignedHeaders=host)
* 해당 지도 외, 다양한 컨셉의 지도들을 확인해볼 수 있다.
  * ![image](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/ZMS/0.png?raw=true)

# 2. 데이터 수집
* 음식점의 데이터는 카카오맵을 크롤링하여 수집했다.
