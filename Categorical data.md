## Categorical data

- pandas 메모리 사용량을 줄이고 성능을 개선하기 위한 Categorical data 활용
- 다양한 데이터 시스템은 중복되는 데이터를 얼마나 효율적으로 저장하고 계산할 수 있는가를 중점으로 개발됨
  - 데이터웨어하우스의 경우 **구별되는 값을 담고 있는 차원 테이블**과 그 **테이블을 참조하는 정수키를 사용**하는 것이 일반적

```python
values = pd.Series([0, 1, 0, 0] * 2)
dim = pd.Series(['apple', 'orange'])
print(values)
# 0    0
# 1    1
# 2    0
# 3    0
# 4    0
# 5    1
# 6    0
# 7    0
# dtype: int64
print(dim)
# 0     apple
# 1    orange
# dtype: object

dim.take(values)
# 0     apple
# 1    orange
# 0     apple
# 0     apple
# 0     apple
# 1    orange
# 0     apple
# 0     apple
# dtype: object
```

- take 메서드로 원래 저장된 문자열을 구함
- Categorical data (범주형 데이터)
  - 정수로 표현된 값은 범주형 or 사전형 표기법
  - 별개의 값을 담고 있는 배열은 범주, 사전 or 단계 데이터 

> 범주형 데이터를 가리키는 정수값은 범주 코드 or 코드



<img src="https://user-images.githubusercontent.com/58063806/121372306-255cda00-c979-11eb-9571-536cef115d82.png" width=30%/>

```python
fruit_cat = df['fruit'].astype("category")
fruit_cat
# 0     apple
# 1    orange
# 2     apple
# 		...
Name: fruit, dtype: category
Categories (2, object): [apple, orange]
    
c = fruit_cat.values
type(c)
# pandas.core.arrays.categorical.Categorical

c.categories
# Index(['apple', 'orange'], dtype='object')

c.codes
# array([0, 1, 0, 0, 0, 1, 0, 0], dtype=int8)
```

- 문자열 객체의 배열을 범주형 데이터로 변경
- Categorical 객체는 categories와 codes 속성을 가짐

```python
my_categories = pd.Categorical(['foo', 'bar', 'baz', 'foo', 'bar'])
my_categories
# [foo, bar, baz, foo, bar]
# Categories (3, object): [bar, baz, foo]

categories = ['foo', 'bar', 'baz']
codes = [0, 1, 2, 0, 0, 1]
my_cats_2 = pd.Categorical.from_codes(codes, categories)
my_cats_2
# [foo, bar, baz, foo, foo, bar]
# Categories (3, object): [foo, bar, baz]
```

- 위와 같이 categorical data를 직접 생성하거나 기존에 정의된 범주 코드가 있으면 from_codes 메서드를 이용해 생성하는 것도 가능

```python
ordered_cat = pd.Categorical.from_codes(codes, categories, ordered=True)
ordered_cat
# [foo, bar, baz, foo, foo, bar]
# Categories (3, object): [foo < bar < baz]

my_cats_2.as_ordered()
# [foo, bar, baz, foo, foo, bar]
# Categories (3, object): [foo < bar < baz]
```

- 범주형으로 변경하는 경우 명시적으로 지정하지 않으면 특정 순서 보장X
- ordered=True 옵션을 주고 생성, 순서가 없는 범주형 인스턴스는 as_ordered 메서드 이용
  - foo, bar, baz 순서를 갖도록 생성
- 범주형 데이터는 꼭 문자열일 필요는 없으며, 변경이 불가능한 값이라면 어떤 자료형이어도 무관

