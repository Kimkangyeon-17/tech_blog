# Pandas 기초 가이드

## Pandas란?

Pandas는 Python에서 데이터를 조작하고 분석하기 위한 필수 라이브러리입니다. 엑셀이나 CSV 같은 **표 형태의 데이터를 다룰 때** 가장 많이 사용됩니다.

### 왜 Pandas를 사용해야 할까?

```python
# 엑셀처럼 표 형태로 데이터를 다룰 수 있어요!
import pandas as pd

data = {
    '이름': ['철수', '영희', '민수'],
    '나이': [25, 30, 35],
    '도시': ['서울', '부산', '대구']
}

df = pd.DataFrame(data)
print(df)
```

```bash
   이름  나이  도시
0  철수  25  서울
1  영희  30  부산
2  민수  35  대구
```

### 주요 특징

- **다양한 파일 형식 지원**: CSV, Excel, JSON, SQL 등을 쉽게 읽고 쓸 수 있음
- **강력한 데이터 조작**: 필터링, 정렬, 그룹화, 병합 등이 간단함
- **결측치 처리**: 빠진 데이터를 쉽게 찾고 처리 가능
- **통계 분석**: 평균, 합계, 표준편차 등 통계 함수 내장

---

## 1. 핵심 데이터 구조

### Series (1차원)
한 개의 컬럼만 가진 1차원 데이터입니다.

```python
import pandas as pd

ages = pd.Series([25, 30, 35, 40], name='나이')
print(ages)
print(f"\n데이터 타입: {type(ages)}")
```

```bash
0    25
1    30
2    35
3    40
Name: 나이, dtype: int64

데이터 타입: <class 'pandas.core.series.Series'>
```

### DataFrame (2차원)
행과 열로 구성된 2차원 테이블 데이터입니다. 엑셀 시트와 비슷하다고 생각하면 됩니다.

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David'],
    '나이': [25, 30, 35, 40],
    '도시': ['서울', '부산', '대구', '인천'],
    '연봉': [3000, 3500, 4000, 4500]
}

df = pd.DataFrame(data)
print(df)
print(f"\n데이터 타입: {type(df)}")
```

```bash
      이름  나이  도시    연봉
0    Alice  25  서울  3000
1      Bob  30  부산  3500
2  Charlie  35  대구  4000
3    David  40  인천  4500

데이터 타입: <class 'pandas.core.frame.DataFrame'>
```

---

## 2. DataFrame 생성 방법

### 딕셔너리로 생성

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie'],
    '나이': [25, 30, 35],
    '도시': ['서울', '부산', '대구']
}

df = pd.DataFrame(data)
print(df)
```

```bash
      이름  나이  도시
0    Alice  25  서울
1      Bob  30  부산
2  Charlie  35  대구
```

### 리스트로 생성

```python
import pandas as pd

data_list = [
    ['Alice', 25, '서울'],
    ['Bob', 30, '부산'],
    ['Charlie', 35, '대구']
]

df = pd.DataFrame(data_list, columns=['이름', '나이', '도시'])
print(df)
```

```bash
      이름  나이  도시
0    Alice  25  서울
1      Bob  30  부산
2  Charlie  35  대구
```

### CSV 파일에서 읽기

```python
import pandas as pd

# CSV 파일 읽기
df = pd.read_csv('data.csv')
print(df.head())  # 상위 5개 행 출력

# CSV 파일로 저장
df.to_csv('output.csv', index=False)  # index=False: 인덱스 번호 제외
```

**실무 팁**: CSV 파일을 읽을 때 한글 깨짐 현상이 있다면 `encoding='cp949'` 또는 `encoding='utf-8'`을 추가하세요.

```python
df = pd.read_csv('data.csv', encoding='cp949')
```

---

## 3. 데이터 탐색

### 기본 정보 확인

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, 30, 35, 28, 32],
    '도시': ['서울', '부산', '대구', '인천', '광주'],
    '연봉': [3000, 3500, 4000, 3200, 3800]
}

df = pd.DataFrame(data)

print("데이터 미리보기")
print(df.head(3))  # 상위 3개 행

print("\n 데이터 정보")
print(df.info())

print("\n 통계 요약")
print(df.describe())

print("\n 컬럼 목록")
print(df.columns.tolist())

print("\n 데이터 크기 (행, 열)")
print(df.shape)
```

```bash
 데이터 미리보기
      이름  나이  도시    연봉
0    Alice  25  서울  3000
1      Bob  30  부산  3500
2  Charlie  35  대구  4000

 데이터 정보
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5 entries, 0 to 4
Data columns (total 4 columns):
 #   Column  Non-Null Count  Dtype 
---  ------  --------------  ----- 
 0   이름      5 non-null      object
 1   나이      5 non-null      int64 
 2   도시      5 non-null      object
 3   연봉      5 non-null      int64 
dtypes: int64(2), object(2)
memory usage: 292.0+ bytes
None

 통계 요약
             나이          연봉
count   5.00000     5.00000
mean   30.00000  3500.00000
std     3.80789   374.16574
min    25.00000  3000.00000
25%    28.00000  3200.00000
50%    30.00000  3500.00000
75%    32.00000  3800.00000
max    35.00000  4000.00000

 컬럼 목록
['이름', '나이', '도시', '연봉']

 데이터 크기 (행, 열)
(5, 4)
```

---

## 4. 데이터 선택과 필터링

### 컬럼 선택

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie'],
    '나이': [25, 30, 35],
    '도시': ['서울', '부산', '대구']
}

df = pd.DataFrame(data)

# 단일 컬럼 선택 → Series 반환
print("나이 컬럼 (Series):")
print(df['나이'])
print(type(df['나이']))

# 여러 컬럼 선택 → DataFrame 반환
print("\n이름과 도시 컬럼 (DataFrame):")
print(df[['이름', '도시']])
print(type(df[['이름', '도시']]))
```

```bash
나이 컬럼 (Series):
0    25
1    30
2    35
Name: 나이, dtype: int64
<class 'pandas.core.series.Series'>

이름과 도시 컬럼 (DataFrame):
      이름  도시
0    Alice  서울
1      Bob  부산
2  Charlie  대구
<class 'pandas.core.frame.DataFrame'>
```

### 행 선택: `.loc` vs `.iloc`

**핵심 차이점:**
- `.loc`: **라벨(이름)** 기반 선택
- `.iloc`: **인덱스(위치)** 기반 선택

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie'],
    '나이': [25, 30, 35],
    '도시': ['서울', '부산', '대구']
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# .iloc: 위치(숫자)로 선택
print("\n첫 번째 행 (.iloc[0]):")
print(df.iloc[0])

# .loc: 인덱스 번호로 선택 (이 경우 0, 1, 2가 인덱스)
print("\n인덱스가 0인 행 (.loc[0]):")
print(df.loc[0])

# .loc: 조건으로 선택
print("\n이름이 'Bob'인 행 (.loc):")
print(df.loc[df['이름'] == 'Bob'])
```

```bash
원본 데이터:
      이름  나이  도시
0    Alice  25  서울
1      Bob  30  부산
2  Charlie  35  대구

첫 번째 행 (.iloc[0]):
이름    Alice
나이       25
도시       서울
Name: 0, dtype: object

인덱스가 0인 행 (.loc[0]):
이름    Alice
나이       25
도시       서울
Name: 0, dtype: object

이름이 'Bob'인 행 (.loc):
    이름  나이  도시
1  Bob  30  부산
```

### 조건 필터링

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, 30, 35, 28, 32],
    '도시': ['서울', '부산', '서울', '대구', '서울'],
    '연봉': [3000, 3500, 4000, 3200, 3800]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# 단일 조건
print("\n나이가 30 이상인 사람:")
print(df[df['나이'] >= 30])

# 여러 조건 (AND: &)
print("\n나이가 30 이상이고 도시가 서울인 사람:")
print(df[(df['나이'] >= 30) & (df['도시'] == '서울')])

# 여러 조건 (OR: |)
print("\n나이가 25이거나 35인 사람:")
print(df[(df['나이'] == 25) | (df['나이'] == 35)])

# isin: 여러 값 중 하나에 해당
print("\n도시가 서울 또는 부산인 사람:")
print(df[df['도시'].isin(['서울', '부산'])])
```

```bash
원본 데이터:
      이름  나이  도시    연봉
0    Alice  25  서울  3000
1      Bob  30  부산  3500
2  Charlie  35  서울  4000
3    David  28  대구  3200
4      Eva  32  서울  3800

나이가 30 이상인 사람:
      이름  나이  도시    연봉
1      Bob  30  부산  3500
2  Charlie  35  서울  4000
4      Eva  32  서울  3800

나이가 30 이상이고 도시가 서울인 사람:
      이름  나이  도시    연봉
2  Charlie  35  서울  4000
4      Eva  32  서울  3800

나이가 25이거나 35인 사람:
      이름  나이  도시    연봉
0    Alice  25  서울  3000
2  Charlie  35  서울  4000

도시가 서울 또는 부산인 사람:
      이름  나이  도시    연봉
0    Alice  25  서울  3000
1      Bob  30  부산  3500
2  Charlie  35  서울  4000
4      Eva  32  서울  3800
```

---

## 5. 결측치 처리

결측치(Missing Value)는 데이터가 없는 경우를 말합니다. Pandas에서는 `NaN`으로 표시됩니다.

### 결측치 찾기

```python
import pandas as pd
import numpy as np

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, np.nan, 35, 28, np.nan],
    '도시': ['서울', '부산', np.nan, '대구', '광주'],
    '연봉': [3000, 3500, 4000, np.nan, 3800]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

print("\n결측치 확인 (True = 결측치):")
print(df.isnull())

print("\n각 컬럼별 결측치 개수:")
print(df.isnull().sum())

print("\n결측치가 있는 행:")
print(df[df.isnull().any(axis=1)])
```

```bash
원본 데이터:
      이름    나이  도시      연봉
0    Alice  25.0  서울  3000.0
1      Bob   NaN  부산  3500.0
2  Charlie  35.0 NaN  4000.0
3    David  28.0  대구     NaN
4      Eva   NaN  광주  3800.0

결측치 확인 (True = 결측치):
      이름     나이     도시     연봉
0  False  False  False  False
1  False   True  False  False
2  False  False   True  False
3  False  False  False   True
4  False   True  False  False

각 컬럼별 결측치 개수:
이름    0
나이    2
도시    1
연봉    1
dtype: int64

결측치가 있는 행:
      이름    나이  도시      연봉
1      Bob   NaN  부산  3500.0
2  Charlie  35.0 NaN  4000.0
3    David  28.0  대구     NaN
4      Eva   NaN  광주  3800.0
```

### 결측치 처리

```python
import pandas as pd
import numpy as np

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, np.nan, 35, 28, np.nan],
    '연봉': [3000, 3500, 4000, np.nan, 3800]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# 방법 1: 특정 값으로 채우기
df_filled = df.copy()
df_filled['나이'] = df_filled['나이'].fillna(0)
print("\n결측치를 0으로 채우기:")
print(df_filled)

# 방법 2: 평균값으로 채우기 (가장 많이 사용)
df_mean = df.copy()
age_mean = df_mean['나이'].mean()
df_mean['나이'] = df_mean['나이'].fillna(age_mean)
print(f"\n나이 평균: {age_mean:.1f}")
print("결측치를 평균값으로 채우기:")
print(df_mean)

# 방법 3: 결측치가 있는 행 삭제
df_dropped = df.dropna()
print("\n결측치가 있는 행 삭제:")
print(df_dropped)

# 방법 4: 특정 컬럼에 결측치가 있는 행만 삭제
df_dropped_subset = df.dropna(subset=['나이'])
print("\n'나이' 컬럼에 결측치가 있는 행만 삭제:")
print(df_dropped_subset)
```

```bash
원본 데이터:
      이름    나이      연봉
0    Alice  25.0  3000.0
1      Bob   NaN  3500.0
2  Charlie  35.0  4000.0
3    David  28.0     NaN
4      Eva   NaN  3800.0

결측치를 0으로 채우기:
      이름    나이      연봉
0    Alice  25.0  3000.0
1      Bob   0.0  3500.0
2  Charlie  35.0  4000.0
3    David  28.0     NaN
4      Eva   0.0  3800.0

나이 평균: 29.3
결측치를 평균값으로 채우기:
      이름        나이      연봉
0    Alice  25.000000  3000.0
1      Bob  29.333333  3500.0
2  Charlie  35.000000  4000.0
3    David  28.000000     NaN
4      Eva  29.333333  3800.0

결측치가 있는 행 삭제:
      이름    나이      연봉
0    Alice  25.0  3000.0
2  Charlie  35.0  4000.0

'나이' 컬럼에 결측치가 있는 행만 삭제:
      이름    나이      연봉
0    Alice  25.0  3000.0
2  Charlie  35.0  4000.0
3    David  28.0     NaN
```

---

## 6. 데이터 정렬

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, 30, 35, 28, 32],
    '연봉': [3000, 3500, 4000, 3200, 3800]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# 나이 기준 오름차순 정렬
print("\n나이 오름차순:")
print(df.sort_values(by='나이'))

# 나이 기준 내림차순 정렬
print("\n나이 내림차순:")
print(df.sort_values(by='나이', ascending=False))

# 여러 컬럼 기준 정렬
data2 = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '부서': ['개발', '개발', '마케팅', '개발', '마케팅'],
    '나이': [25, 30, 35, 30, 32]
}

df2 = pd.DataFrame(data2)
print("\n여러 컬럼 정렬 (부서 오름차순, 나이 내림차순):")
print(df2.sort_values(by=['부서', '나이'], ascending=[True, False]))
```

```bash
원본 데이터:
      이름  나이    연봉
0    Alice  25  3000
1      Bob  30  3500
2  Charlie  35  4000
3    David  28  3200
4      Eva  32  3800

나이 오름차순:
      이름  나이    연봉
0    Alice  25  3000
3    David  28  3200
1      Bob  30  3500
4      Eva  32  3800
2  Charlie  35  4000

나이 내림차순:
      이름  나이    연봉
2  Charlie  35  4000
4      Eva  32  3800
1      Bob  30  3500
3    David  28  3200
0    Alice  25  3000

여러 컬럼 정렬 (부서 오름차순, 나이 내림차순):
      이름   부서  나이
1      Bob   개발  30
3    David   개발  30
0    Alice   개발  25
2  Charlie  마케팅  35
4      Eva  마케팅  32
```

---

## 7. 그룹화와 집계

### 기본 그룹화

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva', 'Frank'],
    '부서': ['개발', '개발', '마케팅', '개발', '마케팅', '인사'],
    '나이': [25, 30, 35, 28, 32, 40],
    '연봉': [3000, 3500, 4000, 3200, 3800, 4500]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# 부서별 평균 연봉
print("\n부서별 평균 연봉:")
print(df.groupby('부서')['연봉'].mean())

# 부서별 인원 수
print("\n부서별 인원 수:")
print(df.groupby('부서').size())

# 부서별 여러 통계값
print("\n부서별 연봉 통계:")
print(df.groupby('부서')['연봉'].agg(['mean', 'max', 'min', 'count']))
```

```bash
원본 데이터:
      이름   부서  나이    연봉
0    Alice   개발  25  3000
1      Bob   개발  30  3500
2  Charlie  마케팅  35  4000
3    David   개발  28  3200
4      Eva  마케팅  32  3800
5    Frank   인사  40  4500

부서별 평균 연봉:
부서
개발         3233.333333
마케팅        3900.000000
인사         4500.000000
Name: 연봉, dtype: float64

부서별 인원 수:
부서
개발     3
마케팅    2
인사     1
dtype: int64

부서별 연봉 통계:
            mean   max   min  count
부서                                
개발    3233.333333  3500  3000      3
마케팅   3900.000000  4000  3800      2
인사    4500.000000  4500  4500      1
```

### 복잡한 집계

```python
import pandas as pd

data = {
    '부서': ['개발', '개발', '마케팅', '개발', '마케팅'],
    '이름': ['Alice', 'Bob', 'Charlie', 'David', 'Eva'],
    '나이': [25, 30, 35, 28, 32],
    '연봉': [3000, 3500, 4000, 3200, 3800]
}

df = pd.DataFrame(data)

# 부서별 여러 컬럼의 통계
print("부서별 나이와 연봉 평균:")
print(df.groupby('부서')[['나이', '연봉']].mean())

# 다른 집계 함수 적용
print("\n부서별 다양한 통계:")
result = df.groupby('부서').agg({
    '나이': ['mean', 'max'],
    '연봉': ['mean', 'sum']
})
print(result)
```

```bash
부서별 나이와 연봉 평균:
             나이          연봉
부서                        
개발    27.666667  3233.333333
마케팅   33.500000  3900.000000

부서별 다양한 통계:
           나이           연봉         
         mean max       mean   sum
부서                                
개발    27.666667  30  3233.333333  9700
마케팅   33.500000  35  3900.000000  7800
```

---

## 8. 컬럼 추가와 수정

```python
import pandas as pd

data = {
    '이름': ['Alice', 'Bob', 'Charlie'],
    '나이': [25, 30, 35],
    '연봉': [3000, 3500, 4000]
}

df = pd.DataFrame(data)

print("원본 데이터:")
print(df)

# 새로운 컬럼 추가
df['보너스'] = df['연봉'] * 0.1
print("\n보너스 컬럼 추가:")
print(df)

# 조건부 컬럼 추가
df['연령대'] = df['나이'].apply(lambda x: '20대' if x < 30 else '30대')
print("\n연령대 컬럼 추가:")
print(df)

# 기존 컬럼 수정
df['연봉'] = df['연봉'] * 1.1  # 10% 인상
print("\n연봉 10% 인상:")
print(df)

# 컬럼 삭제
df_dropped = df.drop(columns=['보너스'])
print("\n보너스 컬럼 삭제:")
print(df_dropped)
```

```bash
원본 데이터:
      이름  나이    연봉
0    Alice  25  3000
1      Bob  30  3500
2  Charlie  35  4000

보너스 컬럼 추가:
      이름  나이    연봉   보너스
0    Alice  25  3000  300.0
1      Bob  30  3500  350.0
2  Charlie  35  4000  400.0

연령대 컬럼 추가:
      이름  나이    연봉   보너스 연령대
0    Alice  25  3000  300.0  20대
1      Bob  30  3500  350.0  30대
2  Charlie  35  4000  400.0  30대

연봉 10% 인상:
      이름  나이      연봉   보너스 연령대
0    Alice  25  3300.0  300.0  20대
1      Bob  30  3850.0  350.0  30대
2  Charlie  35  4400.0  400.0  30대

보너스 컬럼 삭제:
      이름  나이      연봉 연령대
0    Alice  25  3300.0  20대
1      Bob  30  3850.0  30대
2  Charlie  35  4400.0  30대
```
---

## 정리

Pandas는 데이터 분석의 핵심 라이브러리입니다. 