---
title: "[Python 기초] - 클래스"
date: 2020-06-23T15:02:09+09:00
categories: ["파이썬"]
---
# [Python 기초] - 클래스

## 클래스의 개념

- id() : 클래스에 의해 생성된 '객체'의 식별자를 반환하는 함수
- '객체'는 '클래스'에 의해 생성된 실체

### 클래스 생성 및 선언

```python
class Car :
    # 속성(변수)
    count = 0 # 클래스 변수

    # 생성자
    def __init__(self, color, speed) :
        # 인스턴스 변수
				self.color = color
        self.speed = speed

				# 클래스 변수
				Car.count += 1

    # 메소드
    def getName(self) :
        return self.color

    def getSpeed(self) :
        return self.speed

    def upSpeed(self, value) :
        self.speed += value

    def downSpeed(self, value) :
        self.speed -= value

car = Car('검정', 0)
print('%s의 현재 속도는 %d입니다.' %(car.getName(), car.getSpeed()))

car.color = "파랑"
car.speed = 5
car.upSpeed(30)

print('%s의 현재 속도는 %d입니다.' %(car.getName(), car.getSpeed()))
```

```
검정의 현재 속도는 0입니다.
파랑의 현재 속도는 35입니다.
```

## 클래스의 상속

### 상속을 구현하는 문법

```python
class SubClass(SuperClass) :
	# ~~
```

### 메서드 오버라이딩 구현 코드

```python
## 부모 클래스 ##
class Car :
	speed = 0
	def upSpeed(self, vlaue) :
		self.speed += value

class Sedan(Car) :
	def upSpeed(self, value) :
		self.speed += value

		if self.speed > 150 :
			self.speed = 150
```

## 클래스 심화

### 클래스의 특별한 메소드

- __del__() 메소드
    - 소멸자, 생성자와 반대로 인스턴스 삭제할 때 자동 호출
- __repr__() 메소드
    - 인스턴스를 print() 문으로 출력할 때 실행
- __add__() 메소드
    - 인스턴스 사이에 덧셈 작업이 일어날 때 실행되는 메소드
- 비교 메소드
    - __lt__(), __le__(), __gt__(), __ge__(), __eq__(), __ne__()
    - 순서대로 <. <=, >. >=, ==, !=

### 추상 메소드

```python
class SuperClass :
	def method(self) : #
		raise NotImplementedError()

class SubClass(SuperClass) :
	def method(self) :
		print('abstract method')
```
