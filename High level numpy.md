### High level numpy

#### 정렬

- 하나 이상의 키를 기준으로 데이터를 정렬하는 상황이 많음 
  - EX) A로 정렬한 다음 B로 정렬 (간접 정렬)

**argsort**

```python
import numpy as np

arr = np.random.randn(3, 5)
arr
# array([[ 1.19150081, -1.39283864, -0.65707704, -0.62087326, -2.04337546],
#        [ 1.78060301, -2.08695138, -0.07934292,  0.4884494 , -0.21521199],
#        [ 0.33917024, -1.96489678, -0.41936347,  0.16890563, -0.74344259]])


# 첫 번째 로우을 기준으로 나머지 로우도 다 같이 정렬
arr2 = arr[:, arr[0].argsort()]
# array([[-2.04337546, -1.39283864, -0.65707704, -0.62087326,  1.19150081],
#        [-0.21521199, -2.08695138, -0.07934292,  0.4884494 ,  1.78060301],
#        [-0.74344259, -1.96489678, -0.41936347,  0.16890563,  0.33917024]])

# 위의 결과를 역순으로 
arr2[:, ::-1]
# array([[ 1.19150081, -0.62087326, -0.65707704, -1.39283864, -2.04337546],
#        [ 1.78060301,  0.4884494 , -0.07934292, -2.08695138, -0.21521199],
#        [ 0.33917024,  0.16890563, -0.41936347, -1.96489678, -0.74344259]])
```

**lexsort**

```python
first_name = np.array(["Bob", "Jane", "Steve", "Bill", "Barbara"])
last_name = np.array(["Jones", "Arnold", "Arnold", "Jones", "Walters"])

sorter = np.lexsort([first_name, last_name])
sorter
# array([1, 2, 3, 0, 4], dtype=int64)

last_name[sorter], first_name[sorter]
# (array(['Arnold', 'Arnold', 'Jones', 'Jones', 'Walters'], dtype='<U7'),
#  array(['Jane', 'Steve', 'Bill', 'Bob', 'Barbara'], dtype='<U7'))
```

- lexsort는 나중에 넘겨준 배열을 우선적으로 정렬을 수행
  - last name으로 정렬 후 동일한 결과에 대해서 first_name으로 정렬

**partition**

```python
np.random.seed(12345)
arr = np.random.randn(20)
arr
# array([-0.20470766,  0.47894334, -0.51943872, -0.5557303 ,  1.96578057,
#         1.39340583,  0.09290788,  0.28174615,  0.76902257,  1.24643474,
#         1.00718936, -1.29622111,  0.27499163,  0.22891288,  1.35291684,
#         0.88642934, -2.00163731, -0.37184254,  1.66902531, -0.43856974])

# 반환된 결과의 첫 세 원소는 해당 배열에서 가장 작은 값
np.partition(arr, 3)
# array([-2.00163731, -1.29622111, -0.5557303 , -0.51943872, -0.37184254,
#        -0.43856974, -0.20470766,  0.28174615,  0.76902257,  0.47894334,
#         1.00718936,  0.09290788,  0.27499163,  0.22891288,  1.35291684,
#         0.88642934,  1.39340583,  1.96578057,  1.66902531,  1.24643474])

# argsort와 유사하게 해당 원소의 위치를 반환
np.argpartition(arr, 3)
# array([16, 11,  3,  2, 17, 19,  0,  7,  8,  1, 10,  6, 12, 13, 14, 15,  5,
#        4, 18,  9], dtype=int64)
```

- partition은 정렬을 하는 배열의 가장 작은 n개의 원소가 반환 결과의 앞에 오도록 정렬

**searchsorted**

```python
arr = np.array([0, 1, 7, 12, 15])
arr.searchsorted(9)
# 3

arr.searchsorted([0, 8, 11, 16])
# array([0, 3, 3, 5], dtype=int64)

arr = np.array([0, 0, 0, 1, 1, 1, 1])
arr.searchsorted([0, 1])
# array([0, 3], dtype=int64)
```

- 정렬된 배열에서 이진 탐색을 수행해 새로운 값을 삽입할 때 정렬된 상태를 유지하는 위치를 반환
  - 동일한 값은 그룹의 왼쪽에서부터 색인을 반환



#### Numba

- CPU, GPU 또는 기타 하드웨어를 이용해서 NumPy와 유사한 데이터를 다루는 빠른 함수를 제공
- 파이썬 코드를 기계 코드를 변환하기 위해 LLVM 프로젝트를 사용
- 모든 파이썬 코드를 컴파일할 수는 없지만 많은 부분을 지원하며 산술 알고리즘을 작성하는 경우 특히 유용

```python
import numpy as np
import numba as nb

# @nb.jit
def mean_distance(x, y):
    nx = len(x)
    result = 0.0
    count = 0
    for i in range(nx):
        result += x[i] - y[i]
        count += 1
    return result / count

x = np.random.randn(10000000)
y = np.random.randn(10000000)

%time mean_distance(x, y)
# Wall time: 5.31 s, 0.00018234538368696598

numba_mean_distance = nb.jit(mean_distance)
%time numba_mean_distance(x, y)
# Wall time: 766 ms, 0.00018234538368696598
```

- 위의 경우에는 거의 6 ~ 7배 만큼 시간을 단축한 것을 볼 수 있음
- numba.jit 함수를 이용해서 컴파일된 Numba 함수로 바꿀 수 있으며 기존의 함수위에 @nb.jit이라는 decorator를 사용해도 동일한 효과를 얻을 수 있음