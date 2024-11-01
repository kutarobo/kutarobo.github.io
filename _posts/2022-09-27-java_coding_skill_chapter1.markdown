---
title: "1장 우선정리부터"
date: 2022-09-27 16:46:35 +0900
categories: 도서 자바코딩의_기술
---

# 1장 우선정리부터

> 바보도 컴퓨터가 이해하는 코드는 작성할 수 있다.\
> 훌륭한 프로그래머는 인간이 이해하는 코드를 작성한다.

## 1.1 쓸모없는 비교 피하기

### Bool원시값이나 반환값은 비교를 할 필요가 없다

```java
// microscope.isInorganic(sample) 의 결과가 Boolean 일 경우
// as-is
if (microscope.isInorganic(sample) = true) {
  return Result.INORGANIC;
} else { /*...*/ }

// to-be
if (microscope.isInorganic(sample)) {
  return Result.INORGANIC;
} else { /*...*/ }
```

## 1.2 부정 피하기

### 긍정표현식이 부정표현식 보다 더 낫다

- 부정적메서드를 긍정적 메서드로 변경한다.
  - 비슷한 메서드가 한쌍이 생기면 부정메서드를 제거한다.

```java
// microscope.isInorganic(sample) 의 결과가 Boolean 일 경우
// as-is
if (microscope.isInorganic(sample)) {
  return Result.INORGANIC;
} else {
  return analyzeOrganic(sample);
}

if (!microscope.isHumanoid(sample) = true) {
  return Result.ALIEN;
} else {
  return Result.HUMAN;
}

// to-be
if (microscope.isOrganic(sample)) { // 부정메서드를 긍정메서드로 리팩토링
  return analyzeOrganic(sample);
} else {
  return Result.INORGANIC;
}

if (microscope.isHumanoid(sample) = true) { // 부정 표현 ! 을 제거하고 if 내용 순서 변경
  return Result.HUMAN;
} else {
  return Result.ALIEN;
}
```

## 1.3 불 표현식 직접 반환

### 불을 반환할때는 전체항목을 if로 감쌀 필요없이 바로 반환가능.

```java
// as-is
boolean isValid() {
  if (mission < 0 || name == null || name.trim().isEmpty)  {
    return false;
  } else {
    return true;
  }
}

// to-be
boolean isValid() {
  return misstion >= 0 && name != null && !name.trim().isEmpty();
}
```

### 반환할 boolean의 비교문의 덩어리가 클경우 의미있는 조건 덩어리로 나눠서 불표현식의 간소화를 하면 더 좋다

```java
boolean isValid() {
  boolean isValidMission = mission >= 0;
  boolean isValidName = name != null && !name.trim().isEmpty();
  return isValidMission && isValidName;
}
```

# 1.4 불표현식 간소화

- 여러 조건문이 합쳐져서 복잡해진 불표현식은 추상화하여 메소드로 뽑아내서 심플하게 바꾸자.
  - 조건이 복잡해서 파악이 힘들어지면 코드변경시 오류를 유발할 수 있음.

```java
// as-is
boolean willCrewSurvive() {
  return 선체.구멍 == 0
    && 연료탱크.연료 > 네비.지구가는데필요한연료
    && 산소탱크.지속가능(크루인원) > 네비.지구까지가는시간();
}
// to-be
boolean willCrewSurvive() {
  boolean 충분한연료 = hasEnoughOxygen() && hasEnoughFuel();
  return 선체.온전한가 && 충분한연료;
}

private boolean hasEnoughOxygen() {
  return 산소탱크.지속가능(크루인원) > 네비.지구까지가는시간();
}

private boolean hasEnoughFuel() {
  return 연료탱크.연료 > 네비.지구가는데필요한연료;
}
```

# 1.5 조건문에서 NullPointerException 피하기

- null 이 올수 있는 값은 우선적으로 null 인지 체크를 하자.

```java
// as-is
if (message.trim().isEmpty || message == null) {} // message가 null 일경우 nullPointerException 발생

// to-bo
if (message == null || message.trim().isEmpty) {} // null 여부를 먼저 확인함으로써 exception 발생을 회피.

```

# 1.6 스위치 실패 피하기

- 관심사는 하나만
  - 서로 다른 관심사는 서로 다른 코드블록에 넣어야한다.
  - 관심사 분리에 유리한 if문을 사용하는 것도 방법
- 예비분기 default와 break 사용을 잊지말자.

# 1.7 항상 괄호 사용하기

- if 문에서 괄호없이 사용할경우 한줄까지만 조건에 해당하기떄문에 의도치 않은 버그가 발생하기 쉽다.

```java
// as-is
if(조건)
  조건결과 // 해당
  조건결과 // if문에 해당하지않음.

// to-be
if(조건) {
  조건결과 // 해당
  조건결과 // 해당
}
```

# 1.8 코드 대칭 이용하기

- 코드의 목적이 명확해진다.
- 코드가 대칭인지 판단
  - 모든 분기가 비슷한 관심사를 표현하는가?
  - 병렬 구조를 띠나?
  - 분기가 모두 대칭인가?

```java
// as-is
if (user.is미확인) {
  // ....
} else if (user.is우주비행사) {
  // ....
} else (user.is사령관) {
  // ...
}

// to-be - 관심사 분리 적용 및 빠른실패처리
if (user.is미확인) {
  return ... // fast fail
}

if (user.is우주비행사) {
  // ....
} else (user.is사령관) {
  // ...
}
```
