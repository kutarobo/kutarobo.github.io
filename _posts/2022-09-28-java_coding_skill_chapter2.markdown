---
title: 2장 코드스타일 레벨 업
date: 2022-09-28 15:12:35 +0900
categories: 도서 자바코딩의_기술
---

# 2장 코드스타일 레벨 업

> 바보도 컴퓨터가 이해하는 코드는 작성할 수 있다\
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

## 2.3 For 루프 대신 For-Each

- 예시코드

```java
// as - is
for (int i = 0; i < checks.size(); i++ ) {
  // blar blar
}

// to - be
for (String check : checks ) { // 단수명 : 복수명 형태로 지어주면 Good
  // blar blar
}
```

- 개발자가 사용하지 않는 index에 대해 고민할 필요가 없다
  - `indexOutOfBoundsExceptions` 와 같은 휴먼에러 방지 가능.
  - 로직이 좀 더 안전하고 이해하기 쉬워진다.

## 2.4 순회하며 컬렉션 수정하지 않기

- 예시 코드

```java
// as - is
for (Supply supply : supplies) {
  if (supply.isContaminated()) {
    supplies.remove(supply);  // ConcurrentModificationException 발생 위험
  }
}

// to - be
Iterator<Supply> iterator = supplies.iterator();
while (iterator.hasNext()) {
  if (iterator.next().isContamiated()) {
    iterator.remove();
  }
}
```

1. 해결법 1
   - 리스트를 순회하며 변질된 제품을 찾고 그 후 발견한 제품을 제거.
2. 해결법 2
   - iterator , while 사용
   - Iterator는 첫 번쨰 원소 부터 리스트내 원소를 가리키는 포인터처럼 동작함.
     - hasNext로 원소가 남아있는지 묻고 next로 다음원소를 얻어 처리한다.
3. 해결법 3

- 자바 8부터 람다를 이용하여 Collection.removeIf() 같은 메서드를 사용하여 2번과 비슷한 효과를 얻을 수 있다.

## 2.5 순회하며 계산 집약적 연산 하지 않기

- 예시코드

```java
// as - is
List<Supply> result = new LinkedList<>();
for (Supply supply : supplies) {
  // 계산집약적 연산은 성능에 이슈를 초래 할 수 있다
  if (Pattern.matches(regex, supply.toString()) { // 반복할떄마다 컴파일함.
    result.add(supply);
  }
}
return result;

// to - be
List<Supply> result = new LinkedList<>();
// 반복해서 성능저하 시킬요인은 루프 밖으로 뺸다.
Pattern pattern = Pattern.compile(regex); // 컴파일은 한번만함.
for (Supply supply : supplies) {
  // 컴파일된 표현식 실행. 가볍다.
  if (pattern.matcher(supply.toString()).matches()) {
    result.add(supply);
  }
}
return result;
```

- 루프안에서 성능저하가 발생할 요인을 가능하면 루프밖으로 뺴자.

## 2.6 새 줄로 그루핑

- 에시코드

```java
// as - is
enum DistanceUnit {
  MILES, KILOMETERS;

  // 의미와 역할과 상관없이 코드 블럭이 붙어있다.
  static final double MILE_IN_KILOMETERS = 1.60934;
  static final int IDENTITY = 1;
  static final double KILOMETER_IN_MILES = 1 / MILE_IN_KILOMETERS;

  double getConversionRate(DistanceUnit unit) {
    if (this == unit) {
      return IDENTITY;
    } // if 문도 붙어있다. 하나의 블럭 처럼 읽힘.
    if (this == MILES && unit == KILOMETERS) {
      return MILE_IN_KILOMETERS;
    } else {
      return KILOMETER_IN_MILES;
    }
  }
}

// to - be
enum DistanceUnit {
  MILES, KILOMETERS;

  // 연관된 코드끼리 새 줄로 구분함
  static final int IDENTITY = 1;

  static final double MILE_IN_KILOMETERS = 1.60934;
  static final double KILOMETER_IN_MILES = 1 / MILE_IN_KILOMETERS;

  double getConversionRate(DistanceUnit unit) {
    if (this == unit) {
      return IDENTITY;
    }

    if (this == MILES && unit == KILOMETERS) { // 성격이 다른분기를 새줄로 구분함.
      return MILE_IN_KILOMETERS;
    } else {
      return KILOMETER_IN_MILES;
    }
  }
}
```

- 코드블럭이 붙어있으면 한덩어리로 인식이 되기 쉽다.
  - 별개의 블륵을 새 줄로 분리하면 가독성을 향상 시킬 수 있다.

## 2.7 이어붙이기 대신 서식화

- 예시코드

```java
// as - is
String entry = author.toUpperCase() + ": [" + formattedMonth + "-"
  + today.getDayOfMonth() + "-" + today.getYear() + "](Day "
  + (ChronoUnit.DAYS.between(start, today) + 1) + ")> "
  + message + System.lineSeparator();

// to - be
String entry = String.format("$S: [%tm-%<te-%<tY](Day %d)> %s%n", author, today, ChronoUnit.DAYS.between(start, today) + 1, message);
```

- 서식문자열의 활용은 가독성을 높여준다.
  - 어떻게 출력할지와 무엇을 출력할지를 분리하자.

## 2.8 직접만들지 말고 자바 API 사용하기

- 예시코드

```java
// as - is
int getQuantity(Supply supply) {
  if (supply == null) {
    throw new NullPointerException("blar blar");
  }

  int quantity = 0;
  for (Supply supplyInStock : supplies ) {
    if (supply.equals(supplyInStock)){
      quantity++;
    }
  }
  return quantity;
}

// to - be
int getQuantity(Supply supply) {
  Objects.requireNonNull(supply, "blar blar");
  return Collecyions.frequency(supplies, supply);
}
```

- JAVA API 는 예전과 다르게 방대해졌고, 끊임없이 개선되어가고 있다.
  - 새로운 기능을 추가하기보다 기존에 잘 만들어져 있는 API 기능을 잘 활용하는게 가독성 면이나 협업에 도움이 된다.
