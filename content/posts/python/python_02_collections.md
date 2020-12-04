---
title: "[Python 기초] - 튜플,세트 그리고 딕셔너리"
date: 2020-06-23T15:00:58+09:00
categories: ["python"]
---
# [Python 기초] - 튜플,세트 그리고 딕셔너리

# 다양한 데이터 타입

컴퓨터 자원의 효율적 사용을 위해 상황에 맞는 데이터 구조를 선택해야 질 높은 프로그램을 개발할 수 있음

- 튜플 타입 : 값을 변경할 수 없음(←→ 리스트)
- 세트 타입 : 중복 데이터를 허용하지 않음
- 딕셔너리 타입 : 색인이 아닌 특정 키로 값을 불러올 수 있음

## 튜플

### 개념

- immutable 열거 데이터의 집합
- 값을 변경할 수 있는 리스트 타입과 달리, 한 번 값이 정해지면 항목의 값을 변경할 수 없음

### 튜플 타입

- '서로 다른 종류의 데이터 타입'으로 이루어진 항목들을 변수에 바로 풀어서 할당하거나 색인을 매길 때 사용

### 리스트 타입

- '동일한 자료 타입'으로 이루어진 항목들을 리스트 내에서 순차적으로 추출할 때 사용

### 선언 및 사용

- 소괄호 기호(())로 감싸거나, 아예 감싸지 않는 방법으로 선언

```python
card = 'red', 4, '다이아모든', True # 튜플 생성
card
# ('red', 4, '다이아모늗', True) 출력
card[-1] = False # -> 불가능
```

- 빈 튜플 만들기

```python
empty_tuple = ()
empty_tuple2 = tuple()
```

- 항목이 하나 있는 튜플 생ㅇ성

```python
one = ('하나')
type(one)
# <class 'str'> 출력

one_item = '하나',
type(one_item)
# <class 'tuple'> 출력
```

### 튜플 패킹과 튜플 언패킹

- 튜플 패킹 : 문자열, 숫자, 논리 타입으로 이루어진 여러 개의 데이터가 하나의 튜플로 포장된 것
- 튜플 언패킹 : 튜플을 항목별로 각각 풀어서 변수에 저장하는 것

```python
card = 'red', 4, '다이아몬드', True # 튜플 생성(패킹)
a, b, c, d = card # 튜플 항목 개별 할당(언패킹)
a # 'red' 출력
```

### 튜플 ↔ 리스트

```python
card = 'red', 4, '다이아몬드', True

card_list = list(card)
type(card_list)
<class 'list'>
# ['red', 4, '다이아몬드', True]

card_tuple = tuple(card_list)
type(card_tuple)
<class 'tuple'>
card_tuple
# ('red', 4, '다이아몬드', True)
```

튜플은 append, index 삭제, 값 변경이 불가능하다.

## 세트

### 개념

세트 타입은 데이터 중복을 허용하지 않고 순서가 없는 데이터 지합을 위한 데이터 타입

### 선언 및 사용

- 쉼표 기호(,)로 구분된 항목을 중괄호 기호({})로 감싸는 것
- 'in' : 값이 있으면 Ture, 없으면 False 반환

```python
languages = {'C++', 'Python', 'C', 'C', 'C++', 'Python'}
languages
# {'Python', 'C++', 'C'} 출력

'Python' in languages
True
'R' in languages
False
```

- 빈 세트 타입 생성

```python
try_empty_set = {} # -> 이것은 딕셔너리 타입

real_empty_set = set() # -> 올바른 빈 세트 데이터 생성
```

- 사용 예제

```python
a = set('abracadabra')
# {'a', 'r', 'b', 'c', 'd'} 출력
b = set('alacazam')

a-b
{'r', 'd', 'b'} # a와 b의 차집합
a | b
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'} # a와 b의 합집합
a & b
{'a', 'c'} # a와 b의 교집합
a^b
{'r', 'd', 'b', 'm', 'z', 'l'} # a와 b의 여집합(합집합 - 교집합)
```

## 딕셔너리

### 개념

- '딕셔너리 타입'은 전체 항목이 정렬되지 않은 키와 값의 쌍으로 구성된 데이터 모음
- 변경할 수 있는 데이터 타입
- '키:값'의 형태로 표기하며 각 쌍은 쉼표 기호(,)로 구분하고 전체 집합은 중괄호 기호({})로 감사는 방식
- 딕셔너리에는 순서가 없어 생성한 순서대로 딕셔너리가 구성
- 중복된 키가 있을 경우 마지막에 있는 키를 적용

```python
student1 = {'학번' : 1000, '이름' :  '홍길동', '학번' : 2000}
# {'학번' : 2000, '이름' :  '홍길동'} 출력
```

### 선언 및 사용

```python
programmer_dict = {'Python' : 5, 'C': 2, 'C++' : 3, 'JAVA' : 4}
programmer_dict['Python']
# 5 출력
```

- 값 변경 및 값 추가

```python
programmer_dict['Python'] = 7
programmer_dict['Python']
# 7 출력

programmer_dict['JvavScript'] = 1
	 # {'Python' : 5, 'C': 2, 'C++' : 3, 'JAVA' : 4, 'JavaScript' : 1} 출력
```

- 키 값 확인 : 내장 함수 keys(), values 사용
- 반환 값은 dict_keys() 타입으로 반환되며 익숙한 리스트로 활용할 경우 lsit() 함수 활용

```python
programmer_dict.keys()
dict_keys(['Python', 'C', 'C++', 'Java', 'Ruby'])
programmer_dict.values()
dict_values([5, 2, 3, 4])

list(programmer_dict.keys())
['Python', 'C', 'C++', 'Java', 'Ruby']
```

- 특정 키워드를 확인할 경우 'in' 사용

```python
'Python' in programmer_dict
True
'R' not in programmer_dict
True
```

- 특정 항목 삭제 : del / 전체 항목 삭제 : 내장 함수 clear()

```python
del programmer_dict['Ruby']
del(programmer_dict['C++'], programmer_dict['C'])
programmer_dict.clear()
```

- 없는 키를 찾을 때 '딕셔너리명[키]'를 사용하면 오류, 하지만 **'딕셔너리.get(키)'** 를 사용하면 아무것도 반환하지 않고 정상 실행
- **딕셔너리.items()** 하면를 사용하면 튜플 형태로도 구할 수 있음

### 정렬

키로 정렬한 후 딕셔너리 추출

```python
import operator # operator.itemgetter() 함수 사용을 위해 import

trainDic, trainList = {}, []

trainDic = {'Thomas' : '토마스', 'Edward' : '에드워드', 'Henry' : '헨리'}
trainList = sorted(trainDic.items(), key = operator.itemgetter(0))
# 값으로 정렬을 원할시 0이아닌 1 입력
```


## 컴프리헨션

- 값이 순차적인 리스트를 한 줄로 만드는 간단한 방법
- 리스트 = [수식 for 항목 in range() if 조건식]

```python
numList = [num for num in range(1, 6)]
numList # [1, 2, 3, 4, 5] 출력

numList = [num for num in range(1, 21) if num % 3 == 0]
numList # [3, 6, 9, 12, 15, 18] 출력
```

## 동시에 여러 리스트에 접근

- zip() 함수를 사용해 동시에 여러 리스트에 접근

```python
foods = ['떡볶이', '짜장면', '라면', '피자', '맥주', '치킨', '삼겹살']
sides = ['오뎅', '단무지', '김치']
for food, side in zip(foods, sides) :
	print(food, ' --> ', side)

## 출력 결과 ##
떡볶이 --> 오뎅
짜장면 --> 단무지
라면 --> 김치
```

- 두 리스트를 튜플이나 딕셔너리로 짝지을 때 zip() 함수 사용

```python
tupList = list(zip(foods, sides))
dic = dict(zip(foods, sides))
tupList
dic

## 출력 결과 ##
[('떡볶이', '오뎅'), ('짜장면', '단무지'), ('라면', '김치')]
{'떡볶이': '오뎅', '짜장면': '단무지', '라면': '김치'}
```

- 얕은 복사의 방지

```python
newList = oldList[:]
```
