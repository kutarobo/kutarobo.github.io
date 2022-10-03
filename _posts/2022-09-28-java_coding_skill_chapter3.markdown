---
layout: post
title: 3장 슬기롭게 주석 사용하기
date: 2022-10-03 16:35:35 +0900
categories: 도서 자바코딩의_기술
---

# 3장 슬기롭게 주석 사용하기

> 훌륭한 코드는 그자체로 최고의 설명서다.\
> 주석을 추가하기전에 "주석이 필요없더록 코드를 향상시킬 방법이 없을까?" 라고 자문해보자.

## 3.1 지나치게 많은 주석 없애기

- 코드가 전하는 내용을 반복할 뿐인 주석은 모두 제거
  - 코드에 뭔가 덧붙여 설명하지 않는 주석은 의미리스하다.
  - ex) 코드만 보아서는 드러나지 않는 정보가 들어간 주석은 살려둔다.

## 3.2 주석 처리된 코드 제거

- 혼란만 가중시킴.
- 버전관리, 형상관리 툴을 신뢰하고 지우자.

## 3.3 주석을 상수로 대체

- 예시코드

```java
// as - is
enum SmallDistanceUnit{
  CENTIMETER, INCH;

  double getConversionRate(SmallDistanceUnit unit) {
    if (this == unit) {
      return 1; // 동등 변환율
    }
    // ... 이하 생략
  }
}

// to - be
enum SmallDistanceUnit{
  CENTIMETER, INCH;

  static final int IDENTITY = 1;

  double getConversionRate(SmallDistanceUnit unit) {
    if (this == unit) {
      return IDENTITY;
    }
    // ... 이하 생략
  }
}
```

- 주석대신 코드 자체로 매직 넘버에 의미를 부여할 수 있다.
- 주석은 시간이 지나도 변하지 않을 위험성을 내포한다.
  - 주석에 코드만큼 엄격하지 않기 떄문에 시간이 흘러 코드내용과 주석이 달라질 수 있다.
  - 코드에 의미를 부여하자.

## 3.4 주석을 유틸리티 메서드로 대체

- 예시코드

```java
// as - is
// ... blar blar
// 정수 백분율로 반올림
return Math.toIntExact(Math.round(averageFuel  * 100));

// to - be

int getAverageTankFillingPercent() {
// ... blar blar
  return roundToIntegerPecent(averageFuel);
}

static int roundToIntegerPecent(double value) {
  return Math.toIntExact(Math.round(value  * 100));
}
```

- 주석이 필요한 코드를 의미가 분명한 이름을 가진 유틸리티 메서드로 만들어 사용하자.
  - 코드가 무엇을 하는지 이름만으로 설명할 수 있어 주석을 제거 할 수 있다.
  - 본 메서드에 줄을 추가 하지 않아도 된다.
    - 대신 메서드가 늘었지만 각 메서드가 짧아져 이해하기 더 쉬워진다. (가독석 향상)
  - 다른 메서드에서 재사용할 수 있다.
  - 메서드에 계층구조가 생긴다.
    - 상위 계층 메서드의 이해도가 개선된다.
- ** 이상적으로 각 메서드는 비슷한 추상화 정도를 갖는, 명명된 명령문의 나열이다.**
  - 예시코드에선 이러한 맥락에서 평균을 계산하는 코드도 새로운 유틸리티 메서드로 추출할 수 있다.

## 3.5 구현 결정 설명하기

- 코딩을 하다보면 여러 구현방법에서 결정(선택)을 해야할 떄가 있다.
- 그러할 경우 결정을 하게된 사유에 대한 주석을 아래와 같이 남겨두면 해당 내용의 의사결정에 대해 동료개발자가 이해하기 쉬워진다

```java
/**
 * In the context of [USE CASE],  [사용 사례]의 맥락에서
 * facing [CONCERN]               직면하는 [우려사항]과
 * we decided for [OPTION]        우리가 선택한 [해법]으로
 * to achieve [QUALITY]           얻게 되는 [품질]과
 * accepting [DOWNSIDE]           받아들여야 하는 [단점]
 */
```

## 3.6 예제로 설명하기

- 주석만으로 이해하기 어려운 코드의 경우 (가령 복잡한 정규식) 간단한 예제 코드를 작성해 준다

```java
// as - is
class Supply {
  /**
   * 아래 코드는 어디서든 재고를 식별한다
   * S로 시작해 숫자 다섯자리 재고 번호가 나오고
   * 뒤이어 앞의 재고 번호화 구분하기 위한 역 슬래시가 나오고
   * 국가 코드가 나오는 엄격한 형식을 따른다.
   * 국가 코드는 반드시 참여 국가인 (US, EU, RU, CN) 중
   * 하나를 뜻하는 대문자 두 개 여야한다.
   * 이어서 마침표와 실제 재고명이 소문자로 나온다
   */
   static final Pattern CODE = Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
// to - be
class Supply {
  /**
   * 아래 코드는 어디서든 재고를 식별한다
   *
   * 형식: "S<inventory-number>\<COUNTRY-CODE>.<name>"
   *
   * 유효한 예: "S12345\US.pasta", "S08342\CN.wrench",.....
   *
   * 유효하지 않은 예:
   * "R12345\RU.fuel"   (재고가 아닌 자원)
   * "S1234\US.light"   (숫자가 다섯 개여야 함)
   * "S01234\AI.coconut" (잘못된 국가 코드. US, EU, RU, CN 중 하나를 사용한다.)
   */
   static final Pattern CODE = Pattern.compile("^S\\d{5}\\\\(US|EU|RU|CN)\\.[a-z]+$");
}
```

- 주석이 전보다 더 구조적이고 정보도 더 많이 제공한다.
- 유효한 예제는 일반적으로 표현식을 한번에 이해할 수 있게 해준다.

## 3.7 패키지를 JavaDoc으로 구조화하기

## 3.8 클래스와 인터페이스를 JavaDoc으로 구조화하기

## 3.9 메서드를 JavaDoc으로 구조화하기

## 3.10 생성자를 JavaDoc으로 구조화하기
