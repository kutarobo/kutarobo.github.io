---
layout: post
title:  "자바코딩의 기술 - 1장 우선정리부터"
date:   2022-09-27 16:46:35 +0900
categories: jekyll update
---
# 1장 우선정리부터
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

# todo 불표현식 간소화