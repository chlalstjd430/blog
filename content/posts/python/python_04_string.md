---
title: "[Python 기초] - 문자열의 개념"
date: 2020-06-23T15:01:46+09:00
categories: ["python"]
---
# [Python 기초] - 문자열의 개념

## 문자열의 개념

```python
ss = '파이썬' * 3

## 출력 결과 ##
'파이썬파이썬파이썬'
```

- 문자열의 모든 글자 뒤에 $를 붙여서 출력하는 코드

```python
ss = '파이썬짱!'

sslen = len(ss)
for i in range(0, sslen) :
	print(ss[i] + '$', end = '')
```

## 문자열 메소드

### 대, 소문자 변환

- upper() : 소문자 → 대문자
- lower() : 대문자 → 소문자
- swapcase() : 대문자 ←→ 소문자
- capitalize(0 : 첫문자를 대문자로 변환
- title() 각 단어의 앞글자만 대문자로 변환

### 검색

- s.count('like') : 'like'라는 문자열이 발생한 횟수 리턴
- s.find('like')
    - 문자열 s에서 'like'의 오프셋 리턴
    - 찾는 문자열 없을 경우 -1 리턴
- s.find('like', 숫자) : 문자열 숫자 번 위치부터 검색
- s.rfind('like') : find와 동일하나, 오른쪽부터 검색
- s.index('like')
    - 문자열 s에서 'like'의 오프셋 리턴
    - 찾는 문자열이 없을 경우 예외 발생
- s.rindex('like') : index와 같지만 오른쪽부터 검색
- s.startswith('i like') : i like로 시작하는 문자열인가? bool 타입 리턴(True, False)
- s.endswith('you') : 'you'로 끝나는 문자열인가? bool 타입 리턴(True, False)
- s.startswith('i like', 7) startswith을 7번쨰 문자열부터 검사
- s.endswith('you', 7) endswith을 7번째 문자열부터 검사

### 문자열 편집/치환

- s.strip() : 좌우 공백 삭제
- s.rstrip() : 오른쪽 공백 삭제
- s.lstrip() : 왼쪽 공백 삭제

```python
ss = ' 파 이 썬 '
ss.strip()
ss.rstrip()
ss.lstrip()

## 출력 결과 ##
'파 이 썬'
' 파 이 썬'
'파 이 썬 '
```

- s.strip('<>') : 앞뒤의 특정 문자 삭제, '<>' 삭제
- s.replace('파이썬', 'Python') :  '파이썬'을 'Python'으로 치환

### 문자열 분리/결합

- s.split() : 공백으로 분리
- s.splitlines() : 라인 단위로 분리
- ':'.join(s) : ':' 문자로 결합
- s.split(':', 2) : ':' 문자로 2번만 분리
- s.rsplit(':', 1) : 오른쪽부터 한번만 분리

### 함수명에 대입 : map() 함수

- map(함수명, 리스트명)

```python
before = ['2019', '12, '31']
after = list(map(int, before))
after

## 출력 결과 ##
[2019, 12, 31]
```

### 문자열 정렬, 채우기

- center() : 문자열 가운데 정렬
- ljust() : 문자열 왼쪽 정렬
- rjust() : 문자열 오른쪽 정렬
- zfill() : 오른쪽 정렬, 왼쪽 빈 공간은 0으로 채움

### 문자열 구성 파악(bool type return)

- isdigit() : 문자열이 숫자인지 아닌지 확인
- isalpha() : 문자열이 문자(한글, 영어)인지 아닌지 확인
- isalnum() : 글자와 숫자가 섞여있는지 아닌지 확인
- islower() : 전체가 소문자로만 구성되었는지 아닌지 확인
- isupper() : 전체가 대문자로만 구성되었는지 아닌지 확인
- isspace() : 공백 문자로만 구성되었는지 아닌지를 확인
