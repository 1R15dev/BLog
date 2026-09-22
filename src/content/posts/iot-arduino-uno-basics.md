---
title: "IoT 입문: Arduino UNO로 시작하는 피지컬 컴퓨팅"
published: 2026-08-24
description: "IoT의 기본 구조와 Arduino UNO의 구성, 디지털·아날로그 입출력, 센서와 LED 제어를 이해하는 입문 가이드"
tags: [IoT, Arduino, UNO, Electronics, IRIS]
category: IRIS
draft: false
---

# IoT 입문: Arduino UNO로 시작하는 피지컬 컴퓨팅

IoT(Internet of Things)는 센서로 현실의 정보를 수집하고, 네트워크를 통해 데이터를 전달하며, 그 결과에 따라 장치를 제어하는 기술이다. Arduino UNO는 이러한 흐름을 가장 단순한 형태로 실습하기 좋은 마이크로컨트롤러 보드다.

## 1. IoT 시스템의 기본 구조

대부분의 IoT 시스템은 다음 네 단계로 구성된다.

1. **감지(Sensing)**: 온도, 밝기, 거리, 움직임 등을 센서로 측정한다.
2. **처리(Processing)**: 마이크로컨트롤러가 센서 값을 읽고 판단한다.
3. **통신(Networking)**: Wi-Fi, Bluetooth 등의 방식으로 데이터를 전송한다.
4. **동작(Actuation)**: LED, 모터, 부저, 릴레이 같은 장치를 제어한다.

예를 들어 스마트 화분은 토양 수분 센서로 상태를 감지하고, 기준보다 건조하면 펌프를 작동시킨다. 측정값을 서버로 전송하면 스마트폰에서도 상태를 확인할 수 있다.

## 2. Arduino UNO란?

Arduino UNO는 ATmega328P 마이크로컨트롤러를 사용하는 오픈소스 개발 보드다. USB 케이블로 컴퓨터와 연결하고 Arduino IDE에서 프로그램을 업로드한다.

주요 구성은 다음과 같다.

- **디지털 핀 0~13**: `HIGH` 또는 `LOW` 두 상태를 입출력한다.
- **PWM 핀**: `~` 표시가 있는 핀으로 LED 밝기나 모터 속도를 단계적으로 제어한다.
- **아날로그 입력 A0~A5**: 연속적인 전압을 0~1023 범위의 숫자로 읽는다.
- **5V, 3.3V, GND**: 부품에 전원을 공급하고 공통 기준 전압을 만든다.
- **USB 포트**: 프로그램 업로드와 시리얼 통신에 사용한다.
- **리셋 버튼**: 업로드된 프로그램을 처음부터 다시 실행한다.

:::caution
Arduino UNO의 입출력 핀에 과도한 전류나 5V를 넘는 전압을 직접 연결하면 보드가 손상될 수 있다. LED에는 반드시 전류 제한 저항을 사용하고, 모터처럼 전류가 큰 장치는 별도 드라이버와 전원을 사용한다.
:::

## 3. Arduino 프로그램의 구조

Arduino 스케치는 두 개의 기본 함수로 시작한다.

```cpp
void setup() {
  // 전원을 켜거나 리셋했을 때 한 번 실행
}

void loop() {
  // 전원이 켜져 있는 동안 계속 반복
}
```

`setup()`에서는 핀의 역할과 통신 속도를 설정한다. `loop()`에는 반복해서 실행할 센서 측정과 장치 제어 코드를 작성한다.

## 4. 첫 실습: 내장 LED 깜빡이기

Arduino UNO의 13번 핀에는 내장 LED가 연결되어 있다.

```cpp
const int LED_PIN = 13;

void setup() {
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_PIN, HIGH);
  delay(1000);
  digitalWrite(LED_PIN, LOW);
  delay(1000);
}
```

- `pinMode(pin, OUTPUT)`: 핀을 출력 모드로 설정한다.
- `digitalWrite(pin, HIGH)`: 핀에 높은 전압을 출력한다.
- `digitalWrite(pin, LOW)`: 핀에 낮은 전압을 출력한다.
- `delay(1000)`: 1000ms, 즉 1초 동안 기다린다.

## 5. 버튼으로 LED 제어하기

버튼을 2번 핀과 GND 사이에 연결하고 내부 풀업 저항을 사용하면 외부 저항을 줄일 수 있다.

```cpp
const int BUTTON_PIN = 2;
const int LED_PIN = 13;

void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  int buttonState = digitalRead(BUTTON_PIN);

  if (buttonState == LOW) {
    digitalWrite(LED_PIN, HIGH);
  } else {
    digitalWrite(LED_PIN, LOW);
  }
}
```

`INPUT_PULLUP`에서는 버튼을 누르지 않았을 때 `HIGH`, 눌렀을 때 `LOW`가 된다. 처음에는 반대로 느껴질 수 있으므로 기억해 두자.

## 6. 아날로그 센서 읽기

가변저항의 가운데 핀을 A0에 연결하면 회전 위치에 따른 전압을 읽을 수 있다.

```cpp
const int SENSOR_PIN = A0;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int sensorValue = analogRead(SENSOR_PIN);
  Serial.println(sensorValue);
  delay(100);
}
```

`analogRead()`는 UNO 기준으로 0V를 0, 5V를 1023에 가깝게 변환한다. `Serial.begin(9600)`으로 통신을 시작하면 Arduino IDE의 시리얼 모니터에서 값을 확인할 수 있다.

## 7. 센서 값으로 LED 밝기 조절하기

PWM을 사용하면 디지털 핀에서도 0~255 단계로 평균 출력 세기를 조절할 수 있다.

```cpp
const int SENSOR_PIN = A0;
const int LED_PIN = 9;

void setup() {
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  int sensorValue = analogRead(SENSOR_PIN);
  int brightness = map(sensorValue, 0, 1023, 0, 255);

  analogWrite(LED_PIN, brightness);
  delay(10);
}
```

`map()`은 센서의 0~1023 범위를 PWM의 0~255 범위로 변환한다.

## 8. UNO를 IoT 장치로 확장하기

UNO만으로는 Wi-Fi가 내장되어 있지 않다. 인터넷에 연결하려면 다음과 같은 방법을 사용할 수 있다.

- ESP8266 또는 ESP32 통신 모듈 연결
- Ethernet Shield 사용
- Wi-Fi 기능이 포함된 Arduino UNO R4 WiFi 사용

처음부터 네트워크까지 한꺼번에 구현하기보다는 아래 순서로 확장하는 것이 좋다.

```text
센서 값 읽기
→ 시리얼 모니터에서 확인하기
→ 조건에 따라 LED·부저 제어하기
→ 통신 모듈 연결하기
→ 서버나 대시보드로 값 전송하기
```

## 9. 입문 프로젝트 아이디어

- 조도 센서로 자동 점등되는 미니 가로등
- 초음파 센서를 이용한 거리 경보기
- 온도 센서와 부저를 이용한 과열 경고 장치
- 토양 수분 센서를 이용한 스마트 화분
- 버튼과 LED로 만드는 반응 속도 게임

## 마무리

Arduino UNO 학습의 핵심은 회로와 코드를 동시에 조금씩 바꾸며 결과를 관찰하는 것이다. 먼저 LED와 버튼으로 디지털 입출력을 익히고, 센서로 아날로그 입력을 배운 뒤, 마지막에 통신 기능을 더하면 IoT 시스템의 전체 흐름을 자연스럽게 이해할 수 있다.

