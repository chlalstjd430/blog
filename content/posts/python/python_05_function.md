---
title: "[Python 기초] - 함수와 모듈"
date: 2020-06-23T15:01:53+09:00
categories: ["파이썬"]
---
# [Python 기초] - 함수와 모듈

## 함수 기본

### 함수 만들기

```python
def hello(name) : # 함수 선언
	return name +'님, 안녕하세요!'

hello('홍길동') # 함수 호출
```

### pass 예약어

- 이름만 만들어 놓고 내용은 pass 예약어를 사용하여 비울 수 잇음

```python
# 오류인 상황
if True :
	# True일  때 아무런 할 일이 없다고 빈 줄로 둘 때 오류 발생
else :

	print('거짓')

# 정상 작동
if True :
	pass
else :
	print('거짓')
```

### 함수의 매개변수 전달

1. 매개변수의 개수를 지정해 전달하는 방법

```python
def para_func(v1, v2) :
	return v1 + v2
```

2. 매개변수에 기본값을 설정해 놓고 전달하는 방법

```python
def para_func(v1, v2 = 1)
	return v1 ** v2
```

3. 매개변수의 개수를 지정하지 않고 전달하는 방법

```python
def para_func(*para) :
	result = 0

	for num in para :
		result += num

	return result
```

- 함수 호출할 때 딕셔너리의 형식의 매개변수를 키 = 값 형식으로 사용

```python
def dic_func(**para) :
    for k in para.keys() :
        print('%s --> %d명입니다.' % (k, para[k]))

dic_func(트와이스=9, 소녀시대=7, 걸스데이=4, 블랙핑크=4)
```

## 인수 이해하기

### 위치나 키워드로 인수 설정

```python
def print_student_info(name, birth, major='CS', country='KOR') :
    print('이름 :', name, ', 생년월일 : ', birth, ', 전공 : ', major, ', 국적 : ', country)

print_student_info('홍길동', '2020-06-09')
print_student_info('홍길동', '2020-06-09', 'SERVER')  
print_student_info(birth='2020-06-10', name='길동 홍')
```

출력 결과      

```
이름 : 홍길동 , 생년월일 :  2020-06-09 , 전공 :  CS , 국적 :  KOR
이름 : 홍길동 , 생년월일 :  2020-06-09 , 전공 :  SERVER , 국적 :  KOR
이름 : 길동 홍 , 생년월일 :  2020-06-10 , 전공 :  CS , 국적 :  KOR
```

### 키워드로만 호출하기

```python
def concat(*args, sep='/'):
	return sep.join(args)

concat('a', 'b', 'c')
'a/b/c'
concat('a', 'b', 'c', sep = '.')
'a.b.c'
concat(sep='-', 'a','b', 'c')
# SyntaxError
# 개수가 정해져 있지 않은 인수는 함수 선언 시 가장 뒤쪽에 배치하여야 한다.
```

### 언패킹 인수 리스트 활용하기

```python
args = 5, 10
list(range(*args))
# [5, 6, 7, 8, 9]
```

## 모듈

### 모듈의 생성과 사용

Module1.py

```python
def func1():
	print('func1')

def func2():
	print('func2')
```

Module2.py

```python
import Module1

Module1.func1()
Module2.func2()
```

- 모듈병을 생략하고 사용하고 싶을 때
    - from 모듈명 import 함수1, 함수2
    - from 모듈명 import *

    Module3.py

    ```python
    from Modul1 import func1, func2

    func1()
    func2()
    ```

### 모듈의 종류

- 표준 모듈 : 파이썬에서 제공하는 모듈
- 사용자 정의 모듈 : 사용자가 직접 정의한 모듈
- 서드 파티 모듈 : 파이썬이 아닌 외부 회사나 단체에서 제공하는 모듈
- 모듈의 목록 확인 방법
    - 모듈명.builtin_module_names
    - dir(모듈명)

## 함수의 응용

### 패키지

- 모듈을 주제별로 분리할 때 사용

![Python%2011%207d7cfdc1cb044362b48444b871c40bcf/Untitled.png](Python%2011%207d7cfdc1cb044362b48444b871c40bcf/Untitled.png)

- 임포트 형식

```python
from 패키지명.모듈명 import 함수명
```

### 내부 함수, lambda, map()

- 내부 함수

```python
def outFunc(v1, v2) :
	def inFunc(num1, num2) :
		return num1 + num2
	return inFunc(v1, v2)

print(outFunc(10,20))

## 출력 결과 ##
30
```

outFunc() 함수 밖에서 호출하면 오류

```python
print(inFunc(10,20)) # 불가능
```

- 람다 함수
    - 쓰고 버리는 일시적인 함수로 함수가 생성된 곳에서만 필요
    - 간단한 기능을 일반적인 함수와 같이 정의해두고 쓰는 것이 아닌 필요한 곳에 즉시 사용 후 버릴 수 있음
    - 람다에는 return 문이 포함되어 있지 않으며 반환값을 만드는 표현식이 있음

    ```python
    g = lambda x: x**2
    print(g(8))
    64

    f = lambda x ,y=7: x + y
    print(f(3))
    10

    myList = [1, 2, 3, 4, 5]
    add10 = lambda num : num + 10
    myList = list(map(add10, myList))
    print(myList)

    ## 출력 결과 ##
    [11, 12, 13, 14, 15]

    # 두 리스트의 각 자릿수를 합쳐서 새로운 리스트로 만들기
    list1 = [1, 2, 3, 4]
    list2 = [10, 20, 30, 40]
    hapList = list(map(lambda n1,n2 : n1 + n2, list1, list2))
    print(hapList)

    ## 출력 결과 ##
    [11, 22, 33, 44]
    ```

### 제너레이터와 yield

- yield 문 : 함수를 종결하지 않으면서 값을 계속 반환

```python
def number_generator():
	yield 0
	yield 1
	yield 2

for i in number_generator():
	print(i)
```

```
## 출력 결과 ##
0
1
2
```
