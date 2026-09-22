---
title: "C 언어 기초: 출력부터 2중 반복문까지"
published: 2026-08-25
description: "C 언어의 프로그램 구조, 변수, 입력과 출력, 조건문, 반복문, 2중 반복문을 예제로 익히는 입문 가이드"
tags: [C, Programming, Beginner, IRIS]
category: IRIS
draft: false
---

# C 언어 기초: 출력부터 2중 반복문까지

C 언어는 컴퓨터가 데이터를 저장하고 명령을 실행하는 방식을 비교적 가까이에서 배울 수 있는 언어다. 처음에는 기호가 많아 보이지만, **입력 → 처리 → 출력**이라는 흐름부터 이해하면 어렵지 않다.

## 1. 첫 번째 프로그램

```c
#include <stdio.h>

int main(void) {
    printf("Hello, C!\n");
    return 0;
}
```

- `#include <stdio.h>`: `printf`, `scanf` 같은 표준 입출력 기능을 사용한다.
- `int main(void)`: 프로그램이 시작되는 함수다.
- `{ }`: 하나의 코드 영역을 나타낸다.
- `;`: 명령 한 줄이 끝났음을 나타낸다.
- `\n`: 출력 후 줄을 바꾼다.
- `return 0`: 프로그램이 정상 종료됐음을 운영체제에 알린다.

## 2. 변수와 자료형

변수는 값을 담아 두는 이름 있는 공간이다. C 언어에서는 어떤 종류의 값을 저장할지 먼저 정한다.

```c
int age = 17;          // 정수
float height = 172.5f; // 실수
double pi = 3.141592;  // 더 정밀한 실수
char grade = 'A';      // 문자 하나
```

자주 사용하는 자료형은 다음과 같다.

| 자료형 | 저장하는 값 | 출력 형식 |
| --- | --- | --- |
| `int` | 정수 | `%d` |
| `float` | 실수 | `%f` |
| `double` | 정밀한 실수 | `%lf` |
| `char` | 문자 하나 | `%c` |

```c
int score = 95;
printf("점수: %d점\n", score);
```

변수 이름은 숫자로 시작할 수 없고, 띄어쓰기를 사용할 수 없다. `student_score` 또는 `studentScore`처럼 의미가 드러나는 이름이 좋다.

## 3. 입력받기

`scanf`를 사용하면 키보드로 값을 입력받을 수 있다. 변수 앞의 `&`는 값을 저장할 메모리 위치를 전달한다는 뜻이다.

```c
#include <stdio.h>

int main(void) {
    int number;

    printf("정수를 입력하세요: ");
    scanf("%d", &number);

    printf("입력한 수는 %d입니다.\n", number);
    return 0;
}
```

두 값을 한 번에 입력받을 수도 있다.

```c
int a, b;
scanf("%d %d", &a, &b);
printf("합계: %d\n", a + b);
```

## 4. 연산자

```c
int a = 10;
int b = 3;

printf("%d\n", a + b); // 13
printf("%d\n", a - b); // 7
printf("%d\n", a * b); // 30
printf("%d\n", a / b); // 3: 정수끼리 나누면 소수점 버림
printf("%d\n", a % b); // 1: 나머지
```

비교 연산의 결과는 참이면 `1`, 거짓이면 `0`이다.

- `a == b`: 같다
- `a != b`: 다르다
- `a > b`, `a < b`: 크다, 작다
- `a >= b`, `a <= b`: 크거나 같다, 작거나 같다

논리 연산자 `&&`는 두 조건이 모두 참일 때, `||`는 하나라도 참일 때, `!`는 조건을 반대로 바꿀 때 사용한다.

## 5. 조건문

### if문

```c
int score;
scanf("%d", &score);

if (score >= 90) {
    printf("A\n");
} else if (score >= 80) {
    printf("B\n");
} else {
    printf("조금 더 연습해 봅시다.\n");
}
```

조건은 위에서 아래로 검사한다. 하나의 조건이 참이 되면 나머지 `else if`와 `else`는 실행하지 않는다.

### switch문

값이 정해진 여러 경우 중 하나를 선택할 때 편리하다.

```c
int menu;
scanf("%d", &menu);

switch (menu) {
    case 1:
        printf("센서 확인\n");
        break;
    case 2:
        printf("LED 제어\n");
        break;
    default:
        printf("없는 메뉴입니다.\n");
}
```

`break`를 빼면 다음 `case`까지 계속 실행되므로 주의한다.

## 6. 반복문

### for문

반복 횟수를 알고 있을 때 자주 사용한다.

```c
for (int i = 1; i <= 5; i++) {
    printf("%d번째 실행\n", i);
}
```

`for (초기식; 조건식; 증감식)` 순서로 작성한다. 위 코드는 `i`가 1에서 시작해 5가 될 때까지 1씩 증가한다.

### while문

조건이 참인 동안 계속 반복한다.

```c
int count = 3;

while (count > 0) {
    printf("%d\n", count);
    count--;
}
printf("START!\n");
```

조건이 계속 참이면 무한 반복이 된다. 반복문 안에서 조건을 바꿀 코드가 있는지 확인해야 한다.

### break와 continue

```c
for (int i = 1; i <= 10; i++) {
    if (i == 7) break;       // 반복문 종료
    if (i % 2 == 0) continue; // 아래 코드를 건너뛰고 다음 반복
    printf("%d ", i);
}
```

## 7. 2중 반복문

반복문 안에 또 다른 반복문을 넣은 구조다. 바깥 반복문이 한 번 실행될 때마다 안쪽 반복문은 처음부터 끝까지 실행된다.

```c
for (int row = 1; row <= 3; row++) {
    for (int column = 1; column <= 4; column++) {
        printf("(%d, %d) ", row, column);
    }
    printf("\n");
}
```

위 코드는 3개의 행과 4개의 열, 총 `3 × 4 = 12`번 좌표를 출력한다.

### 별 사각형 출력

```c
for (int row = 0; row < 4; row++) {
    for (int column = 0; column < 6; column++) {
        printf("*");
    }
    printf("\n");
}
```

결과:

```text
******
******
******
******
```

### 직각삼각형 출력

```c
for (int row = 1; row <= 5; row++) {
    for (int column = 1; column <= row; column++) {
        printf("*");
    }
    printf("\n");
}
```

안쪽 반복문의 종료 조건이 `column <= row`이므로 행 번호만큼 별이 출력된다.

### 구구단 표 만들기

```c
for (int dan = 2; dan <= 9; dan++) {
    printf("[%d단]\n", dan);

    for (int number = 1; number <= 9; number++) {
        printf("%d × %d = %d\n", dan, number, dan * number);
    }

    printf("\n");
}
```

2중 반복문을 볼 때는 다음 두 질문을 차례로 생각하면 좋다.

1. 바깥 반복문은 무엇을 한 단위로 반복하는가?
2. 그 한 단위 안에서 안쪽 반복문은 무엇을 반복하는가?

## 8. 종합 예제: 좌석 번호 출력

```c
#include <stdio.h>

int main(void) {
    int rows, columns;

    printf("행과 열의 수를 입력하세요: ");
    scanf("%d %d", &rows, &columns);

    for (int row = 1; row <= rows; row++) {
        for (int column = 1; column <= columns; column++) {
            printf("%c-%02d ", 'A' + row - 1, column);
        }
        printf("\n");
    }

    return 0;
}
```

## 마무리

C 언어 기초에서 가장 중요한 것은 코드를 외우는 것이 아니라 **변수의 값이 한 줄씩 어떻게 변하는지 추적하는 습관**이다. 짧은 예제를 직접 수정해 보고, 출력 결과를 예상한 뒤 실행하면서 익혀 보자.

