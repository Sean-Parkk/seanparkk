---
title:  "버튼 한 번 누르면, 내 코드를 pep8 스타일로 바꿔주는 그런게 어디있겠어"
excerpt: "주피터 노트북 확장기능 알아보기"

categories:
tags:
- study
  - Jupyter
  - Jupyter Notebook
  - python
  - extensions
---
> (참고)이 글은 주피터 노트북을 쓰시는 분들 중, 추가 기능이 없는지 알아보는 분들에게 도움이 됩니다.

* TOC
{:toc}

나는 **추가/확장 기능과 커스텀을 좋아한다.**  
마우스도 추가 기능을 덧붙여 쓰고있고, 크롬에서도 다양한 확장 기능을 사용하고있다.  
슬랙도 다운받자마자 기능이 아니라 커스텀부터 했다...  
이번에는 그 대상이 **주피터 노트북이다!**

# 1. 설치하기
* 터미널에 아래와 같이 입력해서 설치한다.

```
pip install jupyter_contrib_nbextensions && jupyter contrib nbextension install
```

* 정상적으로 설치되었다면, 아래와 같이 Nbextension이 생성된다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/이미지 2.png?raw=true)
* 탭을 눌러보면 많은 추가 기능들이 있는것을 확인할 수 있다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/image 4.png?raw=true)

# 2. 추가 기능 살펴보기
* 자동 저장 시간 주기를 조정하거나,
* input창을 숨겨서 output창만 보이게 하는 등 다양한 추가 기능이 있다.
* 하나씩 보면 기능을 확인해볼 수 있는데, 그 중 추천하는 몇 가지 기능들을 소개한다.

## 2-1. Table of Contents
* 마크다운 내에서 # 헤더를 줘서 작성하면, 자동으로 **목차를 생성해준다.**
* 아래와 이미지와 같이!

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/image 5.png?raw=true)

* nbviewer로 저장을 하거나, 파일을 그대로 github에 업로드해도 목차 링크가 유지된다.
* Table of Contents의 이름을 바꾸거나, ## 헤더부터(h2) 적용을 한다거나 등의 설정을 할 수 있다.

## 2-2. ExecuteTime
* 실행 시간을 자동으로 표시해준다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/image 6.png?raw=true)
* 효율적인 코드를 찾기 위해 실행 시간을 체크하는 경우에 유용하다.
* 출력 영역을 크게 차지하지도 않기 떄문에 추천한다.

## 2-3. Autopep8
* 자동으로 입력한 코드를 autopep8 스타일로 변경해준다.
* 코드를 아래와 같이 치고,

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/image 8.png?raw=true)

* autopep8을 실행하면 아래와 같이 바뀐다.

![이미지](https://github.com/Sean-Parkk/seanparkk/blob/master/assets/images/nbextensions/image 9.png?raw=true)
* 다만, 가끔 내가 원하는대로 코드가 예쁘게 수정이 안되는 경우도 있긴 하다.
* 비슷한 기능을 가진 Code prettify도 존재한다.
  * 아직 안 써봐서, 뭐가 더 좋은지는 모름(ㅠㅠ)

- - - - -
* [nbextension 내용 자세히 보기](https://towardsdatascience.com/jupyter-notebook-extensions-517fa69d2231)
