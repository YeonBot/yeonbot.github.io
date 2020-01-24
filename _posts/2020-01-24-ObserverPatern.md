---
title: "Java로 구현해보는 Observer Pattern"
excerpt: "Java Observer Pattern Tutorial"

toc: true
toc_sticky: true
categories:
  - Java
tags:
  - Java
  - Pattern
  - Observer
last_modified_at: 2020-01-24T08:06:00-05:00
---

# Observer Pattren

## 목표

- Observer Pattern 이 무엇인지 정리 s
- Java로 코드를 짜보면서 각인
- 어디서 쓰이는지 정리
  <br/><br/>

## 옵저버 패턴은 언제 쓰이는가

> 하나 또는 여러 클래스가 어떤 클래스의 상태 변화에 따라 동작을 해야하는 경우.

하나 예를 들어서 유튜브에 있는 구독, 알림 기능을 옵저버 패턴과 관련 지어서 설명 해보겠습니다. **하나 또는 여러 클래스가 어떤 클래스의 상태 변화에 따라 동작을 해야하는 경우** 에서 앞에 말한 하나 또는 여러 클래스는 구독 버튼을 누르는 사용자가 되고, 어떤 클래스는 영상을 올리거나 스트리밍을 하는 페이지 라고 보면 됩니다. 변화에 따라 동작을 해야하는 경우는 페이지에 영상을 올리면 구독을 누른 사용자에게 알림이 오는 경우로 해석 할 수 있습니다.

여기서 상태를 가지고 있는 페이지는 Observable 객체라 하고, 페이지를 구독 하는 구독자들을 Observer 객체 라고 부릅니다.

## 객체지향 설게

위의 예시를 이용해 옵저버 패턴을 구현해 보자

옵저버 패턴도 인털페이스를 통한 느슨한 결합 관계를 권장한다.

- Publisher : subject 또는 Generator 등 다른 이름으로도 불른다.여기서는 Publisher 이라 부르고,
