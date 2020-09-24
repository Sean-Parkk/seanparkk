---
title:  "아이스크림이 잘 팔리면 익사자가 증가한다?!"
excerpt: "밑바닥부터 시작하는 딥러닝 2장"

categories:
  - study
tags:
  - Study
  - Deeplearning
  - Numpy


use_math: True
toc: True
toc_sticky: true
---

# 2. 퍼셉트론

> 신경망의 기원이 되는 알고리즘인 퍼셉트론을 배워보자.

## 2.1 퍼셉트론이란?

- 다수의 신호를 받아 하나의 신호를 출력. (0, 1)

    ![/assets/images/DLscratch/2/1.png](/assets/images/DLscratch/2/1.png)

- $y=\begin{cases} 0\,(w_1x_1 + w_2x_2 \leq \theta) \\ 1\, (w_1x_1+w_2x_2>\theta) \end{cases}$
- $\theta$는 임계값

## 2.2 단순한 논리 회로

### 2.2.1 AND 게이트

- 모두 1일 때 1 출력
- AND 게이트 진리표

    ![/assets/images/DLscratch/2/Untitled.png](/assets/images/DLscratch/2/Untitled.png)

- 위를 만족하는 매개변수 조합의 예
    - $(w_1, w_2, \theta)$
        - $(0.5, 0.5, 0.7)$
        - $(0.5, 0.5, 0.8)$
        - $(0.8, 0.9, 1.0)$

### 2.2.2 NAND 게이트와 OR 게이트

- NAND = Not AND
    - AND 게이트의 출력을 뒤집은 것
- 모두 0일 때 1을 출력
- NAND 진리표

    ![/assets/images/DLscratch/2/Untitled%201.png](/assets/images/DLscratch/2/Untitled%201.png)

- 위를 만족하는 매개변수 조합의 예
    - $(w_1, w_2, \theta)$
        - $(-0.5, -0.5, -0.7)$
        - AND게이트를 구현하는 매개변수의 부호를 모두 반대로 반전하면 NAND 게이트가 됨
- OR 게이트
    - 입력 신호 중 하나 이상이 1이면 출력이 1이 되는 논리 회로
- OR 진리표

    ![/assets/images/DLscratch/2/Untitled%202.png](/assets/images/DLscratch/2/Untitled%202.png)

- 위를 만족하는 매개변수 조합의 예
    - $(w_1, w_2, \theta)$
        - (0.5, 0.4, 0.2)
        - (0.7, 1.0, 0.6)

퍼셉트론의 구조는 AND, NAND, OR 게이트에서 **모두 똑같음**.
바뀌는 것은 매개변수(가중치, 임계값)의 값 뿐임.

## 2.3 퍼셉트론 구현하기

```python
def AND(x1, x2):
    w1, w2, theta = 0.5, 0.5, 0.7
    tmp = x1*w1 + x2*w2
    return (0 if tmp <= theta else 1)
```

### 2.3.2 가중치와 편향 도입

- 위의 AND 게이트는 직관적이지만, 다른 방식으로 수정해보자.
- 앞선 식에서 $\theta$를 $-b$로 치환하면 식이 아래처럼 된다.
- $y=\begin{cases} 0\,(b+w_1x_1 + w_2x_2\leq 0) \\ 1\, (b+w_1x_1+w_2x_2>0) \end{cases}$
- $b$를 편향(bias)라고 한다. ($w$는 그대로 가중치(weight)
    - 위 식에 의하면, 퍼셉트론은
        - 1) 입력 신호에 가중치를 곱한 후,
        - 2) 편향을 합한다.
        - 3-1) 그 값이 0을 넘으면 1을 출력하고,
        - 3-2) 그 값이 그렇지 않으면 0을 출력한다.
- numpy로 가중치, 편향 구현하기

```python
import numpy as np
x = np.array([0, 1])    # 입력값
w = np.array([0.5, 0.5])    # 가중치
b = -0.7    # 편향

np.sum(w*x)+b    # -0.19999999999999996 (대략 -0.2, 부동소수점 수에 의한 연산 오차)
```

### 2.3.3 가중치와 편향 구현하기

```python
def AND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])
    b = -0.7
    tmp = np.sum(w*x) + b
    return (0 if tmp <= 0 else 1)
```

- 가중치 $w_1, w_2$
    - 각 입력 신호가 결과에 주는 **영향력(중요도)를 조절**하는 매개변수
- 편향 $b$
    - 뉴런이 **얼마나 쉽게 활성화하느냐를 조정**하는 매개변수

- NAND와 OR 구현하기

```python
def NAND(x1, x2):
    x = np.array([x1, x2])
    w = np.array([-0.5, -0.5])
    b = 0.7
    tmp = np.sum(w*x)+b
    return 0 if tmp <= 0 else 1

def OR(x1, x2):
    x = np.array([x1, x2])
    w = np.array([0.5, 0.5])
    b = -0.2
    tmp = np.sum(w*x)+b
    return 0 if tmp <= 0 else 1
```

## 2.4 퍼셉트론의 한계

- AND, NAND, OR은 구현 가능했다, 하지만 XOR 게이트도 구현이 가능할까?

### 2.4.1 XOR게이트

- 배타적 논리합
    - $x_1$과 $x_2$중 한쪽이 1일 때만 1을 출력
- XOR 진리표

    ![/assets/images/DLscratch/2/Untitled%203.png](/assets/images/DLscratch/2/Untitled%203.png)

- 지금까지의 퍼셉트론으로는 XOR 게이트를 구현할 수 없는데, 이를 시각적으로 살펴보자.
- 먼저 OR 게이트를 구현한 퍼셉트론을 시각적으로 나타내면,

    ![/assets/images/DLscratch/2/Untitled%204.png](/assets/images/DLscratch/2/Untitled%204.png)

    - 위처럼 직선으로 나뉜 두 영역을 만들어낸다.
    - AND일 떄에도 직선으로 영역을 구분해낼 수 있다.
- 그럼, XOR 게이트의 출력을 살펴보자.

    ![/assets/images/DLscratch/2/Untitled%205.png](/assets/images/DLscratch/2/Untitled%205.png)

    - 위의 그래프에서 삼각형과 원을 나누는 직선은 존재하지 않는다.

### 2.4.2 선형과 비선형

- 위의 그래프에서 삼각형과 원을 어떻게 나눌 수 있을까?
- 나누는 영역이 꼭 직선일 필요는 없다. 곡선을 그어 영역을 나눠보자.

    ![/assets/images/DLscratch/2/Untitled%206.png](/assets/images/DLscratch/2/Untitled%206.png)

    - 요렇게 나눌 수 있다!
    - 곡선의 영역을 **비선형** 영역, 직선의 영역을 **선형** 영역 이라고도 한다.
- 퍼셉트론은 직선 하나로 나눈 영역만 표현할 수 있다는 한계가 있다.

## 2.5 다층 퍼셉트론이 충돌한다면

- 하나의 퍼셉트론으로는 (그래프에서 하나의 직선으로는) XOR 게이트를 표현할 수 없지만,
    - 사실 퍼셉트론은 '층을 쌓아' 다층 퍼셉트론을 만들 수 있다는 것에 그 의의가 있다.
- 층을 하나 더 쌓아서 XOR게이트를 표현해보자.

### 2.5.1 기존 게이트 조합하기

- [참고] AND, NAND, OR 게이트 기호

    ![/assets/images/DLscratch/2/Untitled%207.png](/assets/images/DLscratch/2/Untitled%207.png)

- 위의 세 게이트를 어떻게 조합해야 XOR게이트를 만들 수 있을까?

    ![/assets/images/DLscratch/2/Untitled%208.png](/assets/images/DLscratch/2/Untitled%208.png)

    - 위와 같이 각 게이트를 대입하면, XOR 게이트가 만들어진다.
    - 진리표를 살펴보면 이해가 쉽다.
- XOR 게이트 진리표

    ![/assets/images/DLscratch/2/Untitled%209.png](/assets/images/DLscratch/2/Untitled%209.png)

### 2.5.2 XOR 게이트 구현하기

```python
def XOR(x1, x2):
    s1 = NAND(x1, x2)
    s2 = OR(x1, x2)
    y = AND(s1, s2)
    return y

# XOR(0,0): 0
# XOR(1,0): 1
# XOR(0,1): 1
# XOR(1,1): 0
```

- XOR을 뉴런을 이용한 퍼셉트론으로 표현하면 아래와 같다.

    ![/assets/images/DLscratch/2/Untitled%2010.png](/assets/images/DLscratch/2/Untitled%2010.png)

- 맨 처음의 퍼셉트론과는 모양이 다르다.
    - AND, OR가 단층 퍼셉트론이라면, XOR은 다층(2층) 퍼셉트론이다.
    - 경우에 따라서는 위 퍼셉트론을 3층 퍼셉트론이라고도 불릴 수 있는데,
        - 2층 퍼셉트론: 가중치를 갖는 층
        - 3층 퍼셉트론: 구성된 층의 수
        - 일단 이 책에선 가중치를 갖는 층을 기준으로 층 수를 부른다.
- 다층 퍼셉트론은, 단층 퍼셉트론으로는 표현하지 못하던 것을 층을 하나 늘려 구현할 수 있게 된다.
    - 이처럼 층을 쌓는 것들 통해(깊게 하여) 더 다양한 것들을 표현해낼 수 있다.

---

## 요약

- 퍼셉트론은 입출력을 가진 알고리즘이며, 입력을 주면 정해진 규칙에 따라 값을 출력
- 퍼셉트론에서는 가중치($w$)와 편향($b$)을 매개변수로 설정
- 단층 퍼셉트론으로 AND, OR 게이트 등의 논리 회로를 구현 가능하며,
    - (선형 영역 표현 가능)
- 다층 퍼셉트론으로는 XOR 게이트도 표현할 수 있다.
    - (비선형 영역 표현 가능)
