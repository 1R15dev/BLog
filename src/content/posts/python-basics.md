---
title: "Python 기초: 처음 시작하는 프로그래밍"
published: 2026-08-22
description: "Python의 출력, 변수, 입력, 조건문, 반복문, 리스트와 함수를 예제로 배우는 입문 가이드"
tags: [Python, Programming, Beginner, IRIS]
category: IRIS
draft: false
---

# Python 기초: 처음 시작하는 프로그래밍

Python은 문법이 간결하고 활용 범위가 넓어 프로그래밍을 처음 배우기에 좋은 언어다. 웹 개발, 데이터 분석, 인공지능, 자동화, IoT 등 다양한 분야에서 사용된다.

## 1. 출력과 주석

```python
print("Hello, Python!")
print(3 + 5)
```

`print()`는 괄호 안의 값을 화면에 출력한다. `#` 뒤의 내용은 코드 실행에 영향을 주지 않는 주석이다.

```python
# 이름을 출력하는 코드
print("IRIS")
```

## 2. 변수와 기본 자료형

변수는 값을 저장하는 이름이다. Python은 변수의 자료형을 따로 선언하지 않아도 된다.

```python
name = "Jihun"       # 문자열 str
age = 17             # 정수 int
height = 172.5       # 실수 float
is_student = True    # 불리언 bool
```

문자열과 변수를 함께 출력할 때는 f-string이 편리하다.

```python
print(f"이름은 {name}이고 나이는 {age}세입니다.")
```

## 3. 사용자 입력

`input()`으로 입력받은 값은 기본적으로 문자열이다. 계산하려면 `int()`나 `float()`로 변환한다.

```python
name = input("이름을 입력하세요: ")
score = int(input("점수를 입력하세요: "))

print(f"{name}님의 점수는 {score}점입니다.")
```

## 4. 연산자

```python
a = 10
b = 3

print(a + b)   # 13
print(a - b)   # 7
print(a * b)   # 30
print(a / b)   # 3.333...
print(a // b)  # 3, 몫
print(a % b)   # 1, 나머지
print(a ** b)  # 1000, 거듭제곱
```

비교에는 `==`, `!=`, `>`, `<`, `>=`, `<=`를 사용하고, 여러 조건을 묶을 때는 `and`, `or`, `not`을 사용한다.

## 5. 조건문

Python은 중괄호 대신 들여쓰기로 코드 범위를 구분한다.

```python
score = int(input("점수: "))

if score >= 90:
    print("A")
elif score >= 80:
    print("B")
else:
    print("조금 더 연습해 봅시다.")
```

들여쓰기 간격이 섞이면 오류가 생길 수 있다. 일반적으로 공백 4칸을 사용한다.

## 6. 반복문

### for문

```python
for number in range(1, 6):
    print(number)
```

`range(1, 6)`은 1부터 5까지의 숫자를 만든다. 마지막 값 6은 포함하지 않는다.

### while문

```python
count = 3

while count > 0:
    print(count)
    count -= 1

print("START!")
```

### 2중 반복문

```python
for row in range(3):
    for column in range(4):
        print(f"({row}, {column})", end=" ")
    print()
```

구구단도 2중 반복문으로 만들 수 있다.

```python
for dan in range(2, 10):
    print(f"[{dan}단]")
    for number in range(1, 10):
        print(f"{dan} × {number} = {dan * number}")
```

## 7. 리스트와 딕셔너리

리스트는 여러 값을 순서대로 저장한다.

```python
sensors = [23.5, 24.1, 22.8]
sensors.append(25.0)

for temperature in sensors:
    print(temperature)
```

딕셔너리는 키와 값을 한 쌍으로 저장한다.

```python
device = {
    "name": "Arduino UNO",
    "status": "online",
    "temperature": 24.3,
}

print(device["name"])
```

## 8. 함수

함수는 자주 사용하는 코드를 하나의 기능으로 묶는다.

```python
def calculate_average(numbers):
    return sum(numbers) / len(numbers)

scores = [80, 95, 87]
average = calculate_average(scores)
print(f"평균: {average:.1f}")
```

## 9. 예외 처리

잘못된 입력 때문에 프로그램이 바로 종료되지 않도록 처리할 수 있다.

```python
try:
    number = int(input("정수를 입력하세요: "))
    print(100 / number)
except ValueError:
    print("정수만 입력할 수 있습니다.")
except ZeroDivisionError:
    print("0으로 나눌 수 없습니다.")
```

## 종합 예제: 간단한 성적 관리

```python
scores = []

for index in range(3):
    score = int(input(f"{index + 1}번째 점수: "))
    scores.append(score)

average = sum(scores) / len(scores)

print(f"최고 점수: {max(scores)}")
print(f"최저 점수: {min(scores)}")
print(f"평균 점수: {average:.1f}")
```

## 마무리

Python을 공부할 때는 코드를 그대로 복사하는 것보다 값을 바꾸고 출력 결과를 먼저 예상해 보는 것이 중요하다. 기본 문법을 익힌 뒤에는 파일 처리, 웹 API, 데이터 분석, AI 라이브러리 순서로 확장해 보자.

