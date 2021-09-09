# Spring Boot 개념 정리



## Spring Framework의 핵심 개념



### IoC/DI

#### 의존성(Dependency)이란?

의존성이 무슨 뜻이냐면, 그냥 **한 객체에서 다른 객체를 가져다 쓰면 의존성이 생긴다**고 표현한다.

클래스 간에 의존 관계가 있다는 것은 한 클래스가 바뀔 때 다른 클래스가 영향을 받는다는 것을 뜻한다. 즉, **한 클래스를 수정하였을 때, 다른 클래스도 수정해야 하는 상황이 발생**한다. 따라서, 코드에 변경이 있을 경우 해당 변경에 의존성을 갖는 코드 중에 수정을 하지 않은 곳이 하나라도 있다면 오류를 발생시킬 것이기 때문에 코드의 품질과 안정성이 떨어진다.

```java
class A {
    public void aFoo() {
        ...;
    }
}

class B {
  public void bFoo() {
    A aVar = new A();
    aVar.aFoo();
  }
}
```

위 코드에서는 `B` 클래스가 `A` 클래스의 의존성을 가진다. 즉, 두 클래스 사이에 의존 관계가 생기게 된다.

`B` 클래스의 `bFoo` 메서드에선 내부적으로 `A`라는 클래스를 사용하며, `A` 클래스에서 정의된 `aFoo`라는 메서드를 호출해서 사용하고 있다.

향후, `A` 클래스에 새로운 메서드가 추가되거나 기존 메서드(여기서는 `aFoo`)의 형식 또는 기능이 바뀌면, `B` 클래스도 그에 따라 수정되거나 추가되어야 할 것이다.



#### IoC(Inversion of Control, 제어 역행)란?

일반적으로 지금까지 프로그램은 아래와 같은 작업을 반복해왔다. 무언가 필요한 쪽에서 필요한 객체를 만들고, 만들어진 객체의 메서드를 직접 호출해서 사용한다. 여기에서 각 객체는 프로그램의 흐름에 능동적으로 참여하고 있다. 즉, 모든 작업을 사용자가 제어하는 구조이다.

- 객체 결정 및 생성 -> 의존성 객체 생성 -> 객체 내의 메서드 호출

```java
class SoccerPlayer {
  private NikeSoccerBall nikeBall;

  public SoccerPlayer() {
    this.nikeBall = new NikeSoccerBall();	// 4)
  }

  public void playSoccer() {	// 2)
    System.out.println("축구선수가 공을 찼다!");
    this.nikeBall.touchBall();	// 3)
  }
}
```

```java
class AdidasSoccerBall {
  public void touchBall() {
    System.out.println("아디다스 축구공이 굴러간다!");
  }
}

class NikeSoccerBall {
  public void touchBall() {
    System.out.println("나이키 축구공이 굴러간다!");
  }
}
```

```java
public class Driver {
  public static void main(String[] args) {
    SoccerPlayer sp = new SoccerPlayer();	// 1)
    sp.playSoccer();	// 2)
  }
}
```

위 예제에서 `1) SoccerPlayer`가 `2) PlaySoccer()`를 실행하기 위해서는 `3) NikeSoccerBall`이 필요하므로, `SoccerPlayer` 자신이 직접 `4) NikeSoccerBall`을 만들어서 사용하고 있다. 이 상태를 `SoccerPlayer`가 `NikeSoccerBall`에 **의존하고 있다**고 표현할 수 있다. 만약 축구선수가 다른 축구공을 사용해야 할 경우, `SoccerPlayer`의 많은 부분을 수정해야 할 것이다.



이러한 의존 관계를 아래와 같이 역전시켜보자.

```java
interface SoccerBall {
  void touchBall();
}

class AdidasSoccerBall implements SoccerBall {
  public void touchBall() {
    System.out.println("아디다스 축구공이 굴러간다!");
  }
}

class NikeSoccerBall implements SoccerBall {
  public void touchBall() {
    System.out.println("나이키 축구공이 굴러간다!");
  }
}
```

```java
class SoccerPlayer {
  private SoccerBall ball;

  public void setSoccerBall(SoccerBall ball) {
    this.ball = ball;
  }

  public void playSoccer() {
    System.out.println("축구선수가 공을 찼다!");
    this.ball.touchBall();
  }
}
```

```java
public class Driver {
  public static void main(String[] args) {
    SoccerPlayer sp = new SoccerPlayer();

    // NikeSoccerBall
    SoccerBall nikeBall = new NikeSoccerBall();
    sp.setSoccerBall(nikeBall);
    sp.playSoccer();

    // AdidasSoccerBall
    SoccerBall adidasBall = new AdidasSoccerBall();
    sp.setSoccerBall(adidasBall);
    sp.playSoccer();
  }
}
```

이번에는 `SoccerBall`을 인터페이스로 만들었고 `SoccerBall`을 implements하는 각각의 축구공 클래스를 만들었다. 축구선수는 축구공을 자신이 만들어서 사용하는 것이 아니라. 외부에서 만들어 진 것을 받아서 사용하고 있다. 또한 타입을 인터페이스로 바꾸었기에 어떤 공이든 코드에 변경없이 사용할 수 있다. 하지만 아직도 코드를 실행하는 부분에서 `SoccerBall`의 종류를 선택하여 `new` 키워드를 통해 직접 생성하고 `SoccerPlayer`에게 set해주는 작업을 해야한다.



#### 의존성 주입(DI, Dependency Injection)이란?

의존성이 무슨 뜻이냐면, 그냥 **한 객체에서 다른 객체를 가져다 쓰면 의존성이 생긴다**고 표현한다.

클래스 간에 의존 관계가 있다는 것은 한 클래스가 바뀔 때 다른 클래스가 영향을 받는다는 것을 뜻한다. 즉, **한 클래스를 수정하였을 때, 다른 클래스도 수정해야 하는 상황이 발생**한다. 따라서, 코드에 변경이 있을 경우 해당 변경에 의존성을 갖는 코드 중에 수정을 하지 않은 곳이 하나라도 있다면 오류를 발생시킬 것이기 때문에 코드의 품질과 안정성이 떨어진다.

예시를 들어보자.

```java
class A {
    public void aFoo() {
        ...;
    }
}

class B {
  public void bFoo() {
    A aVar = new A();
    aVar.aFoo();
  }
}
```

위 코드에서는 `B` 클래스가 `A` 클래스의 의존성을 가진다. 즉, 두 클래스 사이에 의존 관계가 생기게 된다.

`B` 클래스의 `bFoo` 메서드에선 내부적으로 `A`라는 클래스를 사용하며, `A` 클래스에서 정의된 `aFoo`라는 메서드를 호출해서 사용하고 있다.

향후, `A` 클래스에 새로운 메서드가 추가되거나 기존 메서드(여기서는 `aFoo`)의 형식 또는 기능이 바뀌면, `B` 클래스도 그에 따라 수정되거나 추가되어야 할 것이다.



### AOP



### PSA



### JavaBean

```java
import java.io.Serializable;

public class ContactInfo {
    
    public ContactInfo() {
        
    }
    
    private String name;
    private String phoneNumber;
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getPhoneNumber() {
        return phoneNumber;
    }
    
    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

위 예제와 같이 아래 3가지 규칙을 지키는 클래스

- 모든 field는 `private`이며, `getter/setter ` 메서드를 통해서만 접근이 가능하다.
  - `getter/setter`의 접근 제한자는 `public`이어야 한다.
- Argument가 없는 생성자가 존재한다.
  - Argument가 있는 생성자가 존재해도 되지만, 그것만 있어서는 안된다는 뜻
- `java.io.Serializable` interface를 `implement`한다.