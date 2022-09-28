---
layout: post
title:  2장 코드스타일 레벨 업
date:   2022-09-28 15:12:35 +0900
categories: 도서 자바코딩의_기술
---
# 2장 코드스타일 레벨 업
> 바보도 컴퓨터가 이해하는 코드는 작성할 수 있다
> 훌륭한 프로그래머는 인간이 이해하는 코드를 작성한다.

## 2.1 매직 넘버를 상수로 대체
### 매직넘버가 난무하면 코드를 이해하기 어려워 지고 휴먼에러를 발생시킬 소지가 생긴다.
```java
class CruiseControl {
  private double targetSpeedKmh;

  void setPreset(int speedPreset) {
    if (speedPreset == 2) { // 표면상의 의미는 없지만 프로그램을 제어하는 숫자.
      setTargetSpeedKmh(16944);
    }  else if (speedPreset == 1) { // 일명 '매직 넘버'
      setTargetSpeedKmh(7667);
    } else if (speedPreset == 0) {
      setTargetSpeedKmh(0);
    }
  }

  void setTargetSpeedKmh(double speed) {
    targetSpeedKmh = speed;
  }
}
```

```java
static final  int STOP_PRESET = 0;
static final  int PLANETARY_SPEED_PRESET = 1;
static final  int CURISE_SPEED_PRESET = 2;

static final  int CRUISE_SPEED_KMH = 16944;
static final  int PLANET_SPEED_KMH = 7667;
static final  int STOP_SPEED_KMH = 0;

private double targetSpeedKmh;

void setPreset(int speedPreset) {
  if (speedPreset == CURISE_SPEED_PRESET) { // 매직넘버를 제거후 유의미한 이름을 적용한 상수사용
    setTargetSpeedKmh(CRUISE_SPEED_KMH);
  } else if (speedPreset == PLANETARY_SPEED_PRESET) {
    setTargetSpeedKmh(PLANET_SPEED_KMH);
  } else .....
  ...
}
```


## 2.2 정수 상수 대신 열거형
### 정수상수를 매직넘버로 대체했어도, 유효하지않은 값을 거절하도록 강제할 수는 없다.
  - 
```java
class CruiseControl {
  private double targetSpeedKmh;

  void setPreset(int speedPreset) {
    Objects.requierNonNull(speedPreset);

    // enunm을 사용함으로써 분기가 사라지고 코드가 더 간결해짐.
    // 유효하지 않은 값이 들어오는것을 차단함.
    setTargetSpeedKmh(speedPreset.speedKmh);
  }

  void setTargetSpeedKmh(double speedKmh) {
    targetSpeedKmh = speedKmh;
  }
}

// enum 객체를 만들어 상수를 대체.
enum SpeedPreset {
  STOP(0), PLANETARY_SPEED(7667), CRUISE_SPEED(16944)

  final double speedKmh;

  SpeedPreset(double speedkmh) {
    this.speedKmh = speedKmh;
  }
}
```

# TODO

## 2.3 For 루프 대신 For-Each

## 2.4 순회하며 컬렉션 수정하지 않기

## 2.5 순회하며 계산 집약적 연산 하지 않기

## 2.6 새줄로 그루핑

## 2.7 이어붙이기 대신 서식화

## 2.8 직접만들지 말고 자바 API 사용하기
