# Numpy 기초 가이드

## Numpy란?

Numpy는 행렬이나 대규모 다차원 배열을 쉽게 처리할 수 있도록 지원하는 파이썬 라이브러리입니다. 

일반 파이썬 리스트 대신 `ndarray`(N-dimensional array, N차원 배열) 객체를 사용하여 **벡터화 연산**을 통해 월등히 빠른 속도를 제공합니다. 데이터 분석, 과학 연산, 머신러닝 등 거의 모든 수치 계산 분야에서 필수적으로 사용됩니다.

### 왜 Numpy를 사용해야 할까?

```python
# 일반 파이썬 리스트
python_list = [1, 2, 3, 4, 5]
result = [x * 2 for x in python_list]  # 반복문 필요

# Numpy 배열
import numpy as np
numpy_array = np.array([1, 2, 3, 4, 5])
result = numpy_array * 2  # 한 줄로 끝!
```

### 주요 기능

- **배열 생성**: `np.array()`, `np.zeros()`, `np.ones()` 등으로 다양한 배열 생성
- **속성 확인**: `shape`, `ndim`, `dtype` 등으로 배열 정보 파악
- **수학 연산**: `np.dot()`, `np.sum()`, `np.mean()` 등 다양한 수학/통계 함수
- **타 라이브러리 연동**: Pandas, Matplotlib, Scikit-learn 등과 완벽 호환

---

## 1. 기본 배열의 이해

### 1차원 배열 (Vector)
벡터는 일렬로 나열된 숫자들의 집합입니다.

```python
import numpy as np

# 1차원 배열 생성
vector = np.array([25, 170, 70])
print(f"벡터: {vector}")
print(f"차원: {vector.ndim}")
print(f"크기: {vector.shape}")
```

```bash
벡터: [ 25 170  70]
차원: 1
크기: (3,)
```

### 2차원 배열 (Matrix)
행렬은 행과 열로 구성된 2차원 배열입니다.

```python
# 2차원 배열 생성
matrix = np.array([
    [1, 2, 3],
    [4, 5, 6]
])
print(f"행렬:\n{matrix}")
print(f"차원: {matrix.ndim}")
print(f"크기: {matrix.shape}")  # (행, 열)
```

```bash
행렬:
[[1 2 3]
 [4 5 6]]
차원: 2
크기: (2, 3)
```

---

## 2. Numpy가 빠른 이유: 벡터화 연산

### 벡터화(Vectorization)란?

일반 파이썬 리스트는 각 요소마다 타입을 확인하고 연산을 수행해야 합니다. 하지만 Numpy는 모든 요소가 같은 타입(보통 숫자)임을 미리 알고 있어, **배열 전체에 대해 한 번에 연산**을 수행할 수 있습니다.

### 속도 비교 실험

```python
import numpy as np
import time

# 100만 개 요소로 테스트 (실행 가능한 적절한 크기)
size = 1_000_000
python_list = list(range(size))
numpy_array = np.arange(size)

# Python 리스트 방식
start = time.time()
result_list = [x * 2 for x in python_list]
python_time = time.time() - start

# Numpy 방식
start = time.time()
result_numpy = numpy_array * 2
numpy_time = time.time() - start

print(f"Python 리스트: {python_time:.4f}초")
print(f"Numpy 배열: {numpy_time:.4f}초")
print(f"Numpy가 약 {python_time/numpy_time:.1f}배 빠름!")
```

실행 결과 예시:
```bash
Python 리스트: 0.0823초
Numpy 배열: 0.0012초
Numpy가 약 68.6배 빠름!
```

---

## 3. 배열 연산

### 기본 연산 (Element-wise)

같은 크기의 배열끼리 연산하면 **같은 위치의 요소끼리** 계산됩니다.

```python
import numpy as np

a = np.array([[1, 2], 
              [3, 4]])
b = np.array([[5, 6], 
              [7, 8]])

print("덧셈:\n", a + b)
print("뺄셈:\n", a - b)
print("곱셈:\n", a * b)
print("나눗셈:\n", a / b)
```

```bash
덧셈:
 [[ 6  8]
 [10 12]]
뺄셈:
 [[-4 -4]
 [-4 -4]]
곱셈:
 [[ 5 12]
 [21 32]]
나눗셈:
 [[0.2        0.33333333]
 [0.42857143 0.5       ]]
```

### 브로드캐스팅(Broadcasting)

크기가 다른 배열끼리도 특정 조건에서 연산이 가능합니다. 작은 배열이 큰 배열의 크기에 맞춰 자동으로 확장되는 개념입니다.

**브로드캐스팅 규칙:**
1. 두 배열의 차원 수가 다르면, 작은 배열 앞에 크기 1인 차원을 추가
2. 각 차원에서 크기가 같거나, 둘 중 하나가 1이어야 함

```python
import numpy as np

# 예제 1: 행렬 + 스칼라
matrix = np.array([[1, 2, 3],
                   [4, 5, 6]])
print("원본 행렬:\n", matrix)
print("모든 요소에 10 더하기:\n", matrix + 10)

# 예제 2: 행렬 + 벡터
matrix = np.array([[1, 2, 3],
                   [4, 5, 6]])
vector = np.array([10, 20, 30])
print("\n행렬:\n", matrix)
print("벡터:", vector)
print("행렬의 각 행에 벡터 더하기:\n", matrix + vector)

# 예제 3: 열 방향 브로드캐스팅
column_vector = np.array([[100],
                          [200]])
print("\n열 벡터:\n", column_vector)
print("행렬의 각 열에 열 벡터 더하기:\n", matrix + column_vector)
```

```bash
원본 행렬:
 [[1 2 3]
 [4 5 6]]
모든 요소에 10 더하기:
 [[11 12 13]
 [14 15 16]]

행렬:
 [[1 2 3]
 [4 5 6]]
벡터: [10 20 30]
행렬의 각 행에 벡터 더하기:
 [[11 22 33]
 [14 25 36]]

열 벡터:
 [[100]
 [200]]
행렬의 각 열에 열 벡터 더하기:
 [[101 102 103]
 [204 205 206]]
```

---

## 4. 행렬 곱셈과 전치

### 행렬 곱셈 (Matrix Multiplication)

행렬 곱셈은 일반 곱셈(`*`)과 다릅니다. `@` 연산자나 `np.dot()`을 사용합니다.

**실생활 예제: 빵 구매 비용 계산**

```python
import numpy as np

# 철수와 영희가 사려는 빵의 개수
purchase = np.array([
    [1, 2],  # 철수: 소금빵 1개, 소보로 2개
    [3, 4]   # 영희: 소금빵 3개, 소보로 4개
])

# 각 빵집의 가격 (원)
prices = np.array([
    [1000, 2000, 3000],  # 소금빵: A사, B사, C사
    [3000, 2000, 1000]   # 소보로: A사, B사, C사
])

# 행렬 곱셈으로 총 비용 계산
total_cost = purchase @ prices

print("구매 수량:\n", purchase)
print("\n가격표:\n", prices)
print("\n각 빵집에서의 총 비용:")
print("       A사    B사    C사")
print(f"철수: {total_cost[0]}")
print(f"영희: {total_cost[1]}")
```

```bash
구매 수량:
 [[1 2]
 [3 4]]

가격표:
 [[1000 2000 3000]
 [3000 2000 1000]]

각 빵집에서의 총 비용:
       A사    B사    C사
철수: [7000 6000 5000]
영희: [15000 14000 13000]
```

### 전치(Transpose)

행과 열을 바꾸는 연산입니다. `.T` 속성이나 `np.transpose()`를 사용합니다.

```python
import numpy as np

original = np.array([
    [1, 2, 3],
    [4, 5, 6]
])

transposed = original.T

print("원본 행렬 (2×3):\n", original)
print("\n전치 행렬 (3×2):\n", transposed)
```

```bash
원본 행렬 (2×3):
 [[1 2 3]
 [4 5 6]]

전치 행렬 (3×2):
 [[1 4]
 [2 5]
 [3 6]]
```

---

## 5. 데이터 정규화 (Normalization)

AI 모델은 데이터의 스케일에 민감합니다. 서로 다른 범위의 데이터를 비슷한 범위로 맞춰주는 과정이 필요합니다.

가장 기본적인 방법은 **표준화(Standardization)**: 평균 0, 표준편차 1로 변환

```python
import numpy as np

# 키 데이터 (cm)
heights = np.array([160, 165, 170, 175, 180])

# 1단계: 평균과 표준편차 계산
mean = np.mean(heights)
std = np.std(heights)

print(f"원본 데이터: {heights}")
print(f"평균: {mean:.2f}cm, 표준편차: {std:.2f}cm")

# 2단계: 정규화
normalized = (heights - mean) / std

print(f"\n정규화된 데이터: {normalized}")
print(f"정규화 후 평균: {np.mean(normalized):.10f}")
print(f"정규화 후 표준편차: {np.std(normalized):.10f}")
```

```bash
원본 데이터: [160 165 170 175 180]
평균: 170.00cm, 표준편차: 7.07cm

정규화된 데이터: [-1.41421356 -0.70710678  0.          0.70710678  1.41421356]
정규화 후 평균: 0.0000000000
정규화 후 표준편차: 1.0000000000
```

**정규화가 중요한 이유:**
- 키(cm)와 몸무게(kg)처럼 단위가 다른 데이터를 공정하게 비교 가능
- AI 학습 속도 향상 및 성능 개선

---

## 6. 배열 초기화 방법

### 기본 초기화

```python
import numpy as np

# 0으로 채운 3×4 배열
zeros = np.zeros((3, 4))
print("0 배열:\n", zeros)

# 1로 채운 2×3 배열
ones = np.ones((2, 3))
print("\n1 배열:\n", ones)

# 특정 값(7)으로 채운 2×3 배열
sevens = np.full((2, 3), 7)
print("\n7 배열:\n", sevens)

# 3×3 단위 행렬 (대각선만 1)
identity = np.eye(3)
print("\n단위 행렬:\n", identity)

# 범위 배열
range_array = np.arange(0, 10, 2)  # 0부터 10 미만까지 2씩 증가
print("\n범위 배열:", range_array)

# 균등 분포 배열
linspace_array = np.linspace(0, 1, 5)  # 0부터 1까지 5개로 균등 분할
print("균등 분포:", linspace_array)
```

```bash
0 배열:
 [[0. 0. 0. 0.]
 [0. 0. 0. 0.]
 [0. 0. 0. 0.]]

1 배열:
 [[1. 1. 1.]
 [1. 1. 1.]]

7 배열:
 [[7 7 7]
 [7 7 7]]

단위 행렬:
 [[1. 0. 0.]
 [0. 1. 0.]
 [0. 0. 1.]]

범위 배열: [0 2 4 6 8]
균등 분포: [0.   0.25 0.5  0.75 1.  ]
```

### 랜덤 배열 생성

AI 학습 시 가중치 초기화 등에 사용됩니다.

```python
import numpy as np

# 재현 가능하도록 시드 고정
np.random.seed(42)

# 0~1 사이 균등 분포 랜덤 값
uniform_random = np.random.rand(2, 3)
print("균등 분포 랜덤:\n", uniform_random)

# 표준 정규분포 (평균 0, 표준편차 1)
normal_random = np.random.randn(2, 3)
print("\n정규 분포 랜덤:\n", normal_random)

# 1부터 100 사이 정수 랜덤
int_random = np.random.randint(1, 100, size=(2, 3))
print("\n정수 랜덤:\n", int_random)
```

```bash
균등 분포 랜덤:
 [[0.37454012 0.95071431 0.73199394]
 [0.59865848 0.15601864 0.15599452]]

정규 분포 랜덤:
 [[ 0.64768854  1.52302986 -0.23415337]
 [-0.23413696  1.57921282  0.76743473]]

정수 랜덤:
 [[62 10 94]
 [98 50 14]]
```

**시드(seed)를 고정하는 이유:**
- 실험 재현성 확보
- 디버깅 용이
- 결과 공유 시 같은 결과 보장

---

## 7. 유용한 팁

### 배열 인덱싱과 슬라이싱

```python
import numpy as np

arr = np.array([
    [1, 2, 3, 4],
    [5, 6, 7, 8],
    [9, 10, 11, 12]
])

print("전체 배열:\n", arr)
print("\n첫 번째 행:", arr[0])
print("두 번째 열:", arr[:, 1])
print("\n2×2 부분 배열:\n", arr[1:3, 2:4])
```

```bash
전체 배열:
 [[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]

첫 번째 행: [1 2 3 4]
두 번째 열: [ 2  6 10]

2×2 부분 배열:
 [[ 7  8]
 [11 12]]
```

### 배열 형태 변경

```python
import numpy as np

arr = np.arange(12)
print("원본:", arr)

# 3×4로 변경
reshaped = arr.reshape(3, 4)
print("\n3×4 배열:\n", reshaped)

# 1차원으로 평탄화
flattened = reshaped.flatten()
print("\n다시 1차원:", flattened)
```

```bash
원본: [ 0  1  2  3  4  5  6  7  8  9 10 11]

3×4 배열:
 [[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]

다시 1차원: [ 0  1  2  3  4  5  6  7  8  9 10 11]
```

---

## 정리

Numpy는 파이썬에서 수치 계산을 할 때 필수적인 라이브러리입니다. 특히 AI/머신러닝을 공부할 때는 반드시 익혀야 합니다.