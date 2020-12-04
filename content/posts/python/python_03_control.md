---
title: "[Python 기초] - 제어문과 데이터 구조"
date: 2020-06-23T15:01:31+09:00
categories: ["python"]
---
# [Python 기초] - 제어문과 데이터 구조

## 데이터 정렬 : sorted() 함수

- 리스트

```python
nums = 4, 2, 5, 7, 1, 3
sorted(nums)
[1, 2, 3, 4, 5, 7]
sorted(nums, reverse = True)
[7, 5 , 4, 3, 2, 1]
nums
(4, 2, 5, 7, 1, 3(
```

- 딕셔너리

```python
programmer_dict = {'Python':5, 'C':2, 'C++':3, 'Java':4, 'Ruby':1}
sorted(programmer_dict.keys())
sorted(programmer_dict.values())
```

## 제어문과 데이터 타입 조합

```python
A = ['blue', 'yellow', 'red']
B = ['red', 'green', 'blue']
pairs = []

for a in A :
	for b in B :
		if a != b :
			pairs.append((a,b))

pairs
[('blue', 'red'), ('blue', 'green'), ... ]

new_pairs = [(a, b) for a in A for b in B if a != b] # (a, b) 소괄호를 안감싸면 컴파일 에러
```

- 세트 타입 구성

```python
a = {x for x in 'abracadabra' if x not in 'abc'}
{'d', 'r'}
```

- 딕셔너리 타입 구성

```python
square_dic = {x: x**2 for x in (2, 4, 6)}
square_dic
{2: 4, 4: 16, 6: 36}
```

- enumerate()

```python
korean_foods_enum = enumerate(korean_foods)

for index, food in korean_foods_enums:
	print(index, food)

## 출력 결과 ##
0 kimchi
1 bibimbab
2 tteok-bokki
```
