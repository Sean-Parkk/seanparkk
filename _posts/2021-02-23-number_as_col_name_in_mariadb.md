---
title:  "MariaDB - 숫자를 컬럼으로 사용하는 방법"
excerpt: "백틱으로 감싸기"

categories:
  - study
tags:
  - sql
  - database
  - sqlite3
  - mariadb

# use_math: True
# toc: True
# toc_sticky: true
---

> 결론부터 말하면, 숫자를 백틱(`)으로 감싸주면 된다.

## 겪은 문제점

최근 sqlite3로 만든 프로토타입 db를 메인 db인 mariaDB로 이전하는 작업을 하고 있다. 기존 db에서 dump파일을 만들어서 .sql파일로 만들고, 이를 maria에 import하는 방식으로 하고 있는데 이 과정에서 문제점이 발생했다. 참고로 나는 python환경에서 sql로 접속하여 작업했다.

일단 오류가 난 코드를 살펴보면,

```sql
CREATE TABLE TABLE_TEST (
		date DATETIME,
		1 FLOAT,
		2 FLOAT,
		3 FLOAT)
```

위 코드에서 오류가 났다.

## 시도해본 것

1. 일단 숫자를 컬럼명으로 사용한 것이 오류의 원인이라고 파악했다.
    - 근데 mariaDB는 숫자로 된 컬럼명을 지원한다. 그럼 뭐가 문제였을까 생각을 해봤다.
    - 컬럼명은 숫자 형식이 아닌 str형식이어야 하는데, 이것 때문에 오류가 났다고 판단하고 일단 아포스트로피(')를 씌웠다.

    ```sql
    CREATE TABLE TABLE_TEST (
    		date DATETIME,
    		'1' FLOAT,
    		'2' FLOAT,
    		'3' FLOAT)
    ```

    - 실패했다.
2. 쌍따옴표로 감싸야하나? 싶어서 다시 쌍따옴표로 시도해봤다.

    ```sql
    CREATE TABLE TABLE_TEST (
    		date DATETIME,
    		"1" FLOAT,
    		"2" FLOAT,
    		"3" FLOAT)
    ```

    - 실패했다.

## 해결!

구글링을 해보니, 따옴표가 아니라 백틱(`)으로 감싸줘야 했다. 예전에 sql공부할 때 어디선가 봤던 것으로 기억하는데, 역시 기억보단 기록이 더 오래 남는 듯... 그래서 별 건 아니지만 기록해둔다.

- 코드

    ```sql
    CREATE TABLE TABLE_TEST (
    		date DATETIME,
    		`1` FLOAT,
    		`2` FLOAT,
    		`3` FLOAT)
    ```


