---
title:  "판다는 귀여운데 판다스는"
excerpt: "Python Pandas라이브러리 기초 스터디"

categories:
  - study
tags:
  - Study
  - Statistics
  - Dataanalysis
  - Python
  - Pandas
---

# Pandas Study
> 데이터 분석에 기초가 되어줄 판다스 라이브러리 공부하기

- 데이터타입
    - 시리즈
        - 파이썬의 list, 넘파이의 array와 같은 1차원 배열
    - 데이터프레임
        - 2차원 배열
```python
    pd.Series([1,2,3,4])
    pd.Series(np.random.randint(1,3,100))
```

- Series를 DataFrame으로!
```python
    s = s.to_Frame()
```
- csv불러올 때 구분자 설정, 인덱스 설정
    - 길이가 정해지지 않은 공백으로 구분되어있으면,  sep='\s+' 사용
    - 날짜, 시간으로 된 데이터 parse_dates로 파싱하기
```python
    df = pd.read_csv('../aa/aa.csv', sep='\s+', index=1, parse_dates=[[0,1,2]]) #sep='\t', '\n' 등 #index='col3'
```
- 행(0) 혹은 열(1) 세기
```python
    df.shape[0] #[1]은 col
```
- info로 각 피처 별 레코드 수 및 null, 자료형 파악
```python
    df.info()
```
- 각 피처의 데이터 타입
```python
    df.dtypes
```
- groupby 가능! 거기에 sum(), mean() 등도 가능!
```python
    df1 = df.groupby('col1')
    df1 = df1.sum()
    c = chipo.groupby('choice_description').sum().sort_values(by='quantity', ascending=False)
```
- apply 로 함수 적용. lambda와 같은.
```python
    df['col1'] = df['col1'].apply(lambda x: float(x))
```
- 해당 col의 그룹화하여 개수 세기
```python
    df['col1'].value_counts()
    df['col1'].nunique()
```
- 모든 피처 describe()
```python
    df.describe(include='all') # default = only numeric col
```
- len으로 필터링한 레코드 개수 세기
```python
    len(df[df['col'] == 1])  # 10
```
- 특정 문자로 시작하는 레코드 조회
```python
    df[df['col'].str.startswith('A')]
```
- loc (location) 과 iloc
    - 둘다 인덱스 조회, loc[row, col]으로 동일
    - loc
        - col을 이름으로 조회
        - 수식 가능
    - iloc
        - col을 숫자로 조회
    - loc은 문자 입력, iloc은 숫자로 조회
```python
    # loc
    df.loc[:3, :['col']]
    df.loc[df['col'].isin(['a','c']), ['col1', 'col2']]

    # iloc
    df.iloc[:4, 1:3]

    #같이도 됨
    df.loc[['col']].iloc[,3]
```
- 여러개의 값이 있는지 조회(or연산)
```python
    df.isin['a', 'b', 'c']
```
- and와 or필터링
```python
    df[df['col']>20][df['col1']=='d'] # and연산
    df[(df['col']>20) | (df['col']<10)] # or연산
```
- 그룹화 후 여러 통계량 조회
```python
    df.groupby('col')['col2'].agg(['max', 'min', 'median'])
```
- 그룹화 후 agg와 딕셔너리를 활용하기!
```python
    gender_ocup = users.groupby(['occupation', 'gender']).agg({'gender': 'count'})
```
- groupby 연산 후 피벗처럼 보기
```python
    df.groupby(['col1', 'col2'])['col3'].mean().unstack()
```
- groupby 후 레코드 수 세기
```python
    df.groupby(['col1', 'col2']).size()
```
- groupby로 묶으면
    - 그룹화한 (index, 나머지 값들) 로 저장됨.
```python
    for colname, group in df.groupby('col1'):
    	print(colname)  # colname출력
    	print(group)  # 나머지 값들 출력
```
- applymap은 2차원 배열에 함수 적용할 때 씀
```python
    df.applymap(def)
```
- datetime으로 데이터 변환
```python
    df['col'] = pdf.to_datetime(df['col'], format='%Y')
```
- index 설정
    - reset_index로 0부터 n-1까지의 숫자로 초기화시킬 수 있다.
```python
    df.set_index('col', inplace=True)
    df = df.set_index('col', drop=True)
    df.reset_index(drop=True, inplace=True)
```
- col 삭제
    - 여러개 삭제는 df.drop(df.columns[[1,2,3], axis=1)
```python
    del df['col']
    df = df.drop(df.columns[[1,2,3]], axis=1)
```
- 리샘플링
    - 시간 간격을 재조정함
    - [시간 인수 자세히 보기](http://pandas.pydata.org/pandas-docs/stable/timeseries.html#offset-aliases) (Offset aliases로 검색)

    - 업샘플링(up-sampling)
        - 시간 구간이 작아져서 데이터 개수가 증가
    - 다운샘플링
        - 시간 구간이 커지며 데이터 개수가 감소
        - ex. 분단위를 시간단위로 바꾸는 것
        - groupby를 한 것 처럼 그룹화되므로, 측정하려는 통계량 필요
    - 날짜가 아닌 시/분 단위에서는 start ~ end 구간에서 start는 포함, end는 제외
        - start 제외, end 포함하고 싶으면, closed='right' 써줌

```python
    ts.resample('col', closed='right').mean() # 다운샘플링
```
- 날짜인덱스의 장점
    - 다운샘플링을 groupby로 할 수 있다는 점
    - 다른 변수를 선언하지 않고 바로 볼 수 있음
```python
    data.groupby(data.index.to_period('A')).mean() # to_period('D') 등
```
- idxmin, idxmax
```python
    df.idxmin() # 최소값을 가지고 있는 index 반환
    df.idxmax() # 최대값을 가지고 있는 index 반환
```
- df들 병합 가능
```python
    df = df1.append(df2)
```
- df concat
```python
    df = pd.concat([df1, df2], axis=0) # 이건 df1.append(df2)랑 같음
```    

- df merge (DB쿼리와 매우 유사하다!)
```python
    pd.merge(df1, df2, on='key_col', how='outer')
```
- datetime.date(yy,mm,dd)로 선언한 col은 object형으로 저
    - pd.to_datetime('col')로 datetime62로 변환해줘야함.
```python
    df['col'] = pd.to_datetime(df['col'])
```
- 결측값
    - 결측값 계산: isnull (<> notnull)
    - 결측값 없애기: dropna()
```python
    df.isnull().sum()
    df.dropna()
```
- 다 합치기
```python
    df.sum().sum()
```
- 행단위로 계산하기
```python
    df['rawmin'] = df['col'].min(axis=1)
```
- index 가져오기
```python
    df.index.get_level_values(1)
```
- index is duplicated?
```python
    df.index.is_unique
```
- 인덱스 정렬
```python
    df.sort_index()
```
- timedelta를 day로 계산
    - [timedelta 객체 자세한 정보](https://docs.python.org/ko/3/library/datetime.html)
```python
    timedelta.days
```
