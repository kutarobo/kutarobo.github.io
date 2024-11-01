---
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

```java
// as - is
/**
 * logistics 라는 이패키지는 물류(logistics)를 위한 클래스를 포함한다.
 * 이패키지의 inventory 클래스는 화물선에 제품을 선적하고,
 * 변질된 제품은 모두 버릴 수 있다.
 * 이 패키지의 클래스:
 * - Iventory
 * - Supply
 * - Hull
 *
 * @author 아무개
 * @version 1.8
 * @since 1.7
 */
 package logistics;
```

- 요약문이나 @version 같이 불피요한 요소를 집어넣지 말자
  - 패키지 내 클래스 사용법이나 전체 클래스 목록을 알 수 없는 추상적인 요약문은 불필요하다.
  - 버전이나 작성자같은 정보는 git 과 같은 형상관리도구로 관리되고 있고, 코드로 관리하면 실제 버전과 매번 동기화 하기 불편하다.

```java
// to - be
/**
 * 제품 재고를 관리하는 클래스
 *
 * <p>
 * 주요 클래스는 {@link logistics Inventory} 로서 아래를 수행한다.
 * <ul>
 * <li> {@link logistics CargoShip} 으로 선적하고,
 * <li> 변질된 {@link logistics Supply}를 모두 버리고,
 * <li> 이름으로 어떤 {@link logistics Supply}든 찾는다.
 * </ul>
 *
 * <p>
 * 이 클래스는 제품을 내리고 변질된 제품은 즉시 모두 버릴 수 있게 해준다
 * <pre>
 * Inventory inventory = new Inventory();
 * inventory.stockUp(cargoShip.unload());
 * inventory.disposeContaminatedSupplies();
 * inventory.getContaminatedSupplies().isEmpty(); // true;
 * </pre>
 */
 package logistics
```

- 영역 세개를 수직 분히라고 불필요한 정보를 없앴다.
  - 형상관리도구에서 알 수 있는 버전, 작성자 생성일과 같은 정보를 제거.
- 소개문은 패키지내 클래스로 무엇을 할 수 있는지 짧은 요약을 제공한다
- 두번쨰 단락은 패키지 내 주요 클래스로 무엇을 할 수 있는지 설명한다.
  - @link 표기를 활용하여 바로 이동할 수 도 있다.
- 개발자가 즉석에서 바로 사용할 수 있도록 구체적인 예시 제공
  > 훌륭한 패키지 설명서는 이해도에 큰 변화를 불러오며 \
  > 패키지내 모든 클래스로의 진입장벽도 낮춘다.

## 3.8 클래스와 인터페이스를 JavaDoc으로 구조화하기

```java
// as - is
/**
 * 이 클래스는 화물선을 나타낸다.
 * 제품의 {@link Stack}를 내릴 수 있고 제품의 {@link Queue}를 실을 수 있으며
 * long타입으로 remainingCapacity를 보여줄 수 있다.
 */
 interface CargoShip {
  Stack<Supply> unload();
  Queue<Supply> load(Queue<Supply> supplies);
  int getRemainingCapacity();
 }
```

- 요약문이 단순히 인터페이스명을 반복할 뿐 별다른 역할을 못하고 있다.
- 구조화가 덜 되어있다.
- 설명에 오류가 있다. class(x) -> interface(O)
- 상세설명은 인터페이스의 메서드 서명을 되풀이하고 있다.

```java
// to - be
/**
 * 화물선은 용량에 따라 제품을 싣고 내릴 수 있다.
 *
 * <p>
 * 제품은 순차적으로 선적되고 LIFO(last-in-first-out)순으로 내려진다.
 * 화물선은 용량만큼만 제품을 저장할 수 있다.
 * 용량은 절대 음수가 아니다.
 */
 interface CargoShip {
  Stack<Supply> unload();
  Queue<Supply> load(Queue<Supply> supplies);
  int getRemainingCapacity();
 }
```

- 최상단 요약문이 단순히 인터페이스명을 반복하지 않고 유용한 조언을 제공한다.
- 이어지는 설명도 후입선출법 사용과 같은 동작을 더 상세히 설명하고 있다.
- 인터페이스를 호출할 때 capacity에 대해 보장하는 두 가지 조건도 명시한다.
  - 항상참. -> 조건불변 (invariant) 이라고 부른다.

## 3.9 메서드를 JavaDoc으로 구조화하기

```java
// as - is
interface CargoShip {
  Stack<Supply> unload();
  /**
  * {@link Supply} 를 싣는다.
  *
  * @param {@link Queue} 타입의 제품제공
  * @return {@link Queue} 타입의 적재되지 않은 제품
  */
  Queue<Supply> load(Queue<Supply> supplies);

  int getRemainingCapacity();
}
```

- 요약문과 @link 표기가 유용한 정보를 제공하고 있지 않다.
- @param/@return 은 새로운 내용 없이 메소드 내용만 반복하고 있다.
  - ex) Queue 인스턴스 대신 null 을 전달 할 경우 어떻게 될지 알 수 없다.
  - 메서드가 어떻게 동작할 지 예측하기 어렵다.

```java
// to - be
interface CargoShip {
  Stack<Supply> unload();
  /**
   * 제품을 화물선에 싣는다
   *
   * <p>
   * 남은 용량만큼만 제품을 싣게 해준다
   *
   * 예:
   * <pre>
   * int capacity = cargoShip.getRemainingCapacity(); // 1
   * Queue&lt;Supply> supplies = Arrays.asList(new Supply("Apple"));
   * Queue&lt;Supply> spareSupplies = cargoShip.load(supplies);
   * spareSupplies.isEmpty(); // 참
   * cargoShip.getRemainingCapacity() == 0; // 참
   * </pre>
   *
   * @param 적재할 제품; 널이면 안 된다.
   * @return 용량이 작아 실을 수 없었던 제품;
   *          모두 실었다면 empty
   * @throws 제품이 널이면 NullPointerException
   * @see CargoShip#getRemainingCapacity() 용량을 확인하는 함수
   * @see CargoShip#unload() 제품을 내리는 함수
   */
  Queue<Supply> load(Queue<Supply> supplies);

  int getRemainingCapacity();
}
```

- 입력과 내부 상태가 특정 출력과 상태변경을 어떻게 보장하는지 명시한다.
- `<pre>`는 xml 환경이므로 `<pre>` 내 `<` 문자를 `&lt;`로 탈출시킴.
- null과 같이 유효하지 않은 입력까지도 @param 설명에 명시함.
  - NullPotinterException을 @throws 한다고 언급해줌
- @see 표기로 다른 메서드도 참조.
  - 메서드가 일으킨 효과를 되돌리는 방법이나 메서드 호출로 야기된 상태 변화를 관찰할 수 있는 방법을 설명한다.

## 3.10 생성자를 JavaDoc으로 구조화하기

```java
// as - is
class Inventory {
  List<Supply> supplies;
  /**
   * 새 Inventory의 생성자
   */
   Inventory() {
    this(new ApprayList<>());
   }

   /**
    * 새 Inventory의 또 다른 생성자
    *
    * 제품을 Inventory에 추가할 수 있는 생성자
    */
    Inventory(Collection<Supply> initialSupplies) {
      this.supplies = new ArrayList<>(initialSupplies);
    }
}
```

- 요약문은 새로운 정보를 전달하지 않고 있다.
- 두번쨰 생성자의 두번쨰 요약문도 핵심에서 벗어나 있다
  - supplies를 정확히 어떻게 추가하는지 알 수 없다
- 두 생성자의 관계도 추론 할 수 없다.
  - 일반적으로 외부 클래스를 사용할 때는 소스코드보다 javaDoc에 더 활용하게 된다.
  - 생성자의 javaDoc 주석은 프로그래머가 생성자를 사용하는 데 필요한 모든 요소를 설명해야한다.

```java
// to - be
class Inventory {
  List<Supply> supplies;

  /**
   * 빈 재고를 생성한다
   * @see Inventory#Inventory(Collection) 초기 제품을 초기화하는 함수
   */
   Inventory() {
    this(new ApprayList<>());
   }

   /**
    * 제품을 처음으로 선적한 재고를 생성한다
    *
    * @param initialSupplies 제품을 초기화한다.
    *                         null 이면 안되고 빌 수 있다.
    * @throws NullPointerException initialSupplies 가 null일 때
    * @see Inventory#Inventory() 제품없이 초기화하는 함수
    */
    Inventory(Collection<Supply> initialSupplies) {
      this.supplies = new ArrayList<>(initialSupplies);
    }
}
```

- 무엇보다 중요한정보는 **`생성자를 올바르게 호출하는 방법`**
  - 어떤 전제조건을 충족해야하는지 설명을 해야한다.
  - 예제에서는 null 일경우 @throws 하는 부분을 명시함.
- 두 번쨰 생성자 종료 후 객체 상태 정보를 알아야함.
  - 상태에 따라 그 시점에서 호출할 수 있는 메서드가 결정된다.
    - 사후 조건 **`(postcondition)`**
- @see 표기는 개발자에게 힌트를 준다.
  - 알려주지 않았다면 모를 대안을 알려준다.
  - 예제에서는 두 표기를 통해 두 생성자의 관계를 밝힌다.
