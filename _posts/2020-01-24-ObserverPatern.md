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

- Observer Pattern 이 무엇인지 정리
- Java로 코드를 짜보면서 각인
- 어디서 쓰이는지 정리
  <br/><br/>

## 옵저버 패턴은 언제 쓰이는가

> 하나 또는 여러 클래스가 어떤 클래스의 상태 변화에 따라 동작을 해야하는 경우.

예를 들어서 유튜브에 있는 구독, 알림 기능을 옵저버 패턴과 관련 지어서 설명 해보겠습니다.  
**하나 또는 여러 클래스가 어떤 클래스의 상태 변화에 따라 동작을 해야하는 경우** 에서  
앞에 말한 하나 또는 여러 클래스는 구독 버튼을 누르는 사용자가 되고, 어떤 클래스는 영상을 올리거나 스트리밍을 하는 페이지 라고 보면 됩니다.  
변화에 따라 동작을 해야하는 경우는 페이지에 영상을 올리면 구독을 누른 사용자에게 알림이 오는 경우로 해석 할 수 있습니다.

## 옵저버 패턴 개념

위의 예시를 이용해 옵저버 패턴을 구현해 보자

옵저버 패턴은 전략패턴과 같이 인터페이스를 통한 느슨한 결합 관계를 권장한다.

![]({{ site.url }}{{ site.baseurl }}/assets/images/ObserverPatternDiagram.png)
![]({{ site.url }}{{ site.baseurl }}/assets/images/ObserverPatternDiagram2.png)
위의 사진은 Observer Pattern 의 다이어그램이다.

- Subject : Subject 인터페이스는 Observer들을 관리 해주는 역할을 한다. attach, detech, notify는 말그대로 옵저버들을 추가하거나 제거하거나 알림을 해주는 용도라고 생각하면 된다. 위의 예에서는 유저가 유튜브 페이지에 구독을 누르는 경우가 attach, 구독을 취소하는 경우는 detech, notifty 는 유튜버가 실시간 방송을 켰을때 알림이 저절로 구독자들에게 보내지는 경우로 볼수 있다.

- ConctreateSubject : Subject interface를 상속받은 클래스로 subjectState 값을 가지고 있다. 위 다이어 그램에서는 이 State 가 변결 될 떄마다 Observer 의 State 도 같이 변경된다. 여기서 만들 서비스에서는 유튜브가 이 클래스가 되고, 변수로는 Observer list 를 가지고 있고, 알림을 해줄 유튜브 제목, 내용 을 또한 가직 있다.

- Observer interface 의 update 는 Subject interface 에 있는 notify 메서드에서 불리게 된다.

이를 토대로 JAVA로 코딩을 해보자

## 객체지향 Observer Pattern 설계

1. Observer interface, Subject interface 생성

```java
public interface Subject {
  public void add(Observer observer);
  public void delete(Observer observer);
  public void notify(Observer observer);
}
```

```java
public interface Observer {
  public void update(String title, String body);
}
```

두개의 인터페이스를 생성해준다. Observer interface는 update 메서드를 통해 유튜브의 제목과 내용을 받아서 구독자들에게 알림을 해주는 역할을 한다.

2. YoutubeSubject class 생성

```java
import java.util.ArrayList;

public class YoutubeSubject implements Subject {
  private ArrayList<Observer> observers;
  private String title;
  private String body;

  public YoutubeSubject() {
    this.observers = new ArrayList<>();
  }

  //observers리스트에 추가
  @Override
  public void add(Observer observer) {
    observers.add(observer);
  }

  //observers리스트 삭제
  @Override
  public void delete(Observer observer) {
    int index = observers.indexOf(observer);
    observers.remove(index);
  }

  // observers리스트에 있는 모든 구독자들의 update method 호출.
  @Override
  public void notifyObserver() {
    for( Observer observer:observers){
      observer.update(this.title,this.body);
    }
  }

  // get set 메서드
  public String getTitle() {
    return title;
  }

  public void setTitle(String title) {
    this.title = title;
  }

  public String getBody() {
    return body;
  }

  public void setBody(String body) {
    this.body = body;
  }
}
```

3. youtubeUser class 생성

```java
public class YoutubeUser implements Observer{

  private String userName;
  private String title;
  private String body;

  //생성 될떄 사용자 이름을 변수로 저장
  public YoutubeUser(String userName) {
    this.userName = userName;
  }

  @Override
  public void update(String title, String body) {
    this.title = title;
    this.body = body;
    pushAlarm();
  }

  private void pushAlarm() {
    System.out.println("========================================");
    System.out.println(userName+"의 수신함");
    System.out.println(title+" : "+body);
    System.out.println("========================================");
  }
}
```

4. 마지막으로 지금까지 만든 interface 와 class들을 Main 문에서 확인 해보겠다.

```java
public class Main {

  public static void main(String[] args) {
    //생활코딩 유튜브 채널 생성
    YoutubeSubject youtubeSubject = new YoutubeSubject();

    //유저 철수 유튜브 가입
    Observer observer1 = new YoutubeUser("철수");
    //유저 영희 유튜브 가입
    Observer observer2 = new YoutubeUser("영희");

    //철수 유튜브 구독 시작
    youtubeSubject.add(observer1);
    //영희 유튜브 구독시작
    youtubeSubject.add(observer2);

    //생황 코딩 유튜브 라이브 방송 시작
    youtubeSubject.setTitle("생활코딩 실시간 스트리밍 시작");
    youtubeSubject.setBody("코딩야학 7기 라이브 시작합니다.");
    youtubeSubject.notifyObserver();

    //철수 생활코딩 구독 취소
    youtubeSubject.delete(observer1);

    //생황 코딩 유튜브 라이브 방송 시작
    youtubeSubject.setTitle("생활코딩 실시간 스트리밍 시작");
    youtubeSubject.setBody("개발자가 되기 위한 Q&A");
    youtubeSubject.notifyObserver();

  }
}
```

![]({{ site.url }}{{ site.baseurl }}/assets/images/ObserverOutput.png)
main 문을 돌리면 다음과 같이 철수, 영희가 구독을 하고 있는 경우에는 둘 모드에게 알림이 오지만, 철수가 구독을 취소한 경우에는 영희에게만 알림이 오는 경우이다

## 추가로

##### java Observable class

JAVA 에서는 이 옵저버 패턴을 적용한 것을을 기본적으로 제공해준다.
바로 Observable 클래스와 Observer 인터페이스 이다. 이를 이용하면 직접 구현 하지 않더라도 쉽게 옵저버 패턴을 적용 할 수 있다. 하지만 Observable이 인터페이스가 아닌 클래스로 구현 되어 있어서 상속에 제한이 있다는 문제가 있다. 때문에 상황을 봐서 적절히 이용하는게 좋다.

##### pull vs push 방식

옵저버 패턴에는 pull 방식과 push 방식 두가지가 있습니다.  
위에서 구현한 방법은 notift함수를 이용해 push 해주는 방식이였습니다. Subject 쪽에서 Observer 들을 update 해주는 방식이였습니다.

pull 방식은 setChaged() 함수를 이용하여 데이터가 변했음을 알려주고 옵저버 측에서 상황에 맞게 데이터를 get 함수를 이용해 가져오는 방식입니다.

push 방식의 구현방법은 모든 옵저버들의 값을 강제로 바꾸게 되지만, pull 방식을 이용해 구현하면 상황에 따라 Observer 의 State를 바뀌지 않게 구현 할 수 있다는 장점이 있습니다.

### 마무리:옵저버 패턴 이란 무엇인가 ?

- 옵저버 패턴은 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들에게 연락이 가고 자동으로 정보가 갱신되는 1:N 의 관계를 정의한다.
- 연결은 인터페이스를 이용하여 느슨한 결합성을 유지한다. 주체, 옵저버 인터페이스를 적용한다.
- JAVA에서 기본으로 Observable 클래스와 Observer 인터페이스를 제공한다.
- Observer Pattern 에는 Push pull 두가지 방식이 있다.
