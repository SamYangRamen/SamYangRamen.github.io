# Spring Boot 개념 정리



## Layered Architecture(계층화 아키텍처)

대부분의 중/대규모 애플리케이션은 효율적인 개발 및 유지보수를 위해 계층화하여 개발하는 것이 일반적이다.

계층을 나누어 개발하면 각 계층마다의 역할에 집중할 수 있고, 시스템 분석 및 테스트가 용이하며 재사용성이 높아진다.

### 내가 이해하고자 하는 아키텍처

- **Presentation Layer - View** : 표현과 이벤트 처리, 데이터 포맷 책임

  - 사용자와의 최종 접점에 위치하여 사용자로부터 데이터를 입력 받거나, 요청된 데이터를 출력해 보이는 계층이다. 외부로부터의 애플리케이션에 대한 요구를 받아들이는 동시에 처리 결과를 사용자에게 보여주는 부분이며, 컨트롤러의 구성 요소와 상호작용한다.

- **Control Layer - Controller** : 구성 요소간의 처리 흐름을 제어, 데이터 조작 의뢰, 데이터 변환 및 연산, Exception과 Error 처리

  - 사용자로부터 요청을 받고 응답을 처리하는 계층이다. 하위 계층에서 발생하는 Exception이나 Error에 대한 처리를 맡으며, 최종 프레젠테션 계층에 표현 해야할 도메인 모델을 엮는 기능을 수행한다. 사용자로부터 전달 받은 데이터의 유효성 검증을 처리하고, 전체 시스템의 설정상태를 유지한다. 요청에 해당하는 비지니스 로직을 결정하는 역할을 수행한다.

    요약 : 사용자의 요청을 검증, 로직에 요청을 전달, 로직에서 반환된 응답을 적절한 뷰로 연결

- **Business Layer - Service** : Control Layer와 Persistence Layer를 연결하는 역할, Transaction 처리

  - 애플리케이션의 비지니스 로직 처리와 비지니스와 관련된 도메인 모델의 적합성을 검증하고, 트랜잭션을 처리한다. Control 계층과 Persistence 계층을 연결하는 역할로서 두 계층이 직접 통신하지 않게 하여 애플리케이션의 유연성을 증가시킨다.

- **Persistence Layer - Repository**

  - 영구 데이터를 빼내어 객체화 시키며, 영구 저장소에 데이터를 저장/수정/삭제하는 계층이다. DB나 파일에 접근하여 데이터를 CRUD하는 계층이다.

- **Domain Model Layer - DTO**

  - 데이터 객체를 말한다.

### 다른 형태의 Layered Architecture

계층화 아키텍처는 몇 가지의 Layer가 존재해야하는지에 대한 기준이 없다. 즉, 계층화 아키텍처는 하나의 정형화된 무언가로 특정되어 있지 않다. 개발자의 관점이나 애플리케이션의 규모 등에 따라, 아래와 같이 다른 형태의 아키텍처도 충분히 존재한다.

- Presentation Layer - Business Layer - Persistence Layer - Database Layer
- Presentation Layer - Application Layer - Domain Layer - Infrastructure Layer

- Presentation Layer - Application Layer - Business Layer - Data Access Layer



## MVC 패턴

**Controller** : 사용자가 접근한 URL에 따라 요청을 파악한다. URL에 맞는 Method를 호출하여 비지니스 로직을 처리한다. 최종적으로 나온 결과는 Model에 저장을 하고, View에 던져준다.

**Model** : Controller에서 받은 데이터를 저장하는 역할을 한다.

**View** : Controller로부터 받은 Model 데이터를 바탕으로 사용자에게 시각적으로 표현해준다.



### Model1

![image](https://user-images.githubusercontent.com/53200166/132649955-f510d82b-228f-4f57-a210-5a415232378b.png)

- View와 Controller가 같이 있는 형태
- JSP가 Controller와 View의 역할을 모두 담당한다.
- 규모가 작고 빨리 개발할 필요가 있는 프로젝트에 사용되나, 유지보수가 힘들다.

### Model2

![image](https://user-images.githubusercontent.com/53200166/132650069-b65006ce-8e70-4518-9ad9-820a71324c12.png)

- M, V, C 모두 모듈화되어 있는 형태
- 프로젝트의 규모가 커질수록 코드가 복잡해져 유지보수가 힘들어지는 단점을 갖고 있는 Model1 방식을 보완하여 탄생했다.
- 현재 대부분의 Spring 프로젝트들은 Model2의 구조를 따르되, C와 M 사이에 비즈니스 로직만을 담당하는 Service층을 둔 구조이다.



### Controller-Service-Model 수행 과정

- Controller
  - 클라이언트의 요청을 받음
  - 요청에 대한 처리는 Service에게 전담하고, Service에서의 처리 결과를 클라이언트에게 응답
- Service
  - 사용자의 요구사항 처리
  - DB 정보가 필요할 때는 Repository에게 전담
- Repository (Model)
  - DB 관리 및 DB CRUD 작업 처리



### Spring MVC Framework 동작 순서

![image](https://user-images.githubusercontent.com/53200166/132650631-f329febc-0df4-47f8-9945-a7bf1169854e.png)

※ 1번부터 8번까지의 항목을 읽지 않고 알아두어야 할 사항 부분만 읽어도 충분함

1. Client로부터 요청이 들어오면 `DispatcherServlet`이 호출된다.
2. `DispatcherServlet`은 받은 요청을 `HandlerMapping`에게 던져준다. 요청받은 URL을 분석하여 `HandlerMapping`에 적합한 `Controller`를 선택하여 반환한다.
3. `DispatcherServlet`는 다음으로 `HandlerAdapter`를 호출한다. `HandlerApdater`는 해당하는 `Controller` 중 요청한 URL에 맞는 적합한 Method를 찾아준다.
4. `Controller`는 Business Logic을 처리하고, 해당하는 결과를 `View`에 전달할 객체를 `Model`에 저장한다.
5. `Controller`는 `View` name을 `DispatcherServlet`에게 리턴한다.
6. `DispatcherServlet`은 `ViewResolver`를 호출하여 `Controller`가 리턴한 `View` name을 기반으로 적합한 `View`를 찾아준다.
7. `DispatcherServlet`은 `View` 객체에 처리결과를 넘겨 최종 결과를 보여주도록 요청한다.
8. `View` 객체는 해당하는 `View`를 호출하며, `View`는 `Model` 객체에서 화면 표시에 필요한 객체를 가져와 화면 표시를 처리하고 Client에게 넘겨준다.

- 알아두어야 할 사항
  - 요약하면, 클라이언트의 요청과 `Controller` 사이의 Mapping은 모두 `DispatcherServlet`이 담당한다. 
  - 위 그림에서 개발자가 건드려야 하는 부분은 `View`, `Controller`, `Service`, `Repository` 이며 나머지 기능들은 Spring에서 모두 제공해주므로 개발자가 신경 쓸 필요가 없다.
  - `DispatcherServlet`은 자체적으로 `ApplicationContext`를 생성하고 사용한다. 이를 `ServletContext`라고도 부른다.



------



## IoC/DI



### 의존성(Dependency)이란?

의존성이 무슨 뜻이냐면, 그냥 **한 객체에서 다른 객체를 가져다 쓰면 의존성이 생긴다**고 표현한다.

(즉 build.gradle 파일의 dependencies를 명시하는 것은, 개발 중인 클래스에서 다른 어떤 라이브러리가 필요하다고 판단될 때 바로 가져다 쓸 수 있도록 해당 라이브러리를 프로젝트에 등록하는 것이다.)

클래스 간에 의존 관계가 있다는 것은 한 클래스가 바뀔 때 다른 클래스가 영향을 받는다는 것을 뜻한다. 즉, **한 클래스를 수정하였을 때, 다른 클래스도 수정해야 하는 상황이 발생**한다. 따라서, 코드에 변경이 있을 경우 해당 변경에 의존성을 갖는 코드 중에 수정을 하지 않은 곳이 하나라도 있다면 오류를 발생시킬 것이기 때문에 코드의 품질과 안정성이 떨어진다.

```java
public class A {
    public void aFoo() {
        ...;
    }
}

public class B {
  public void bFoo() {
    A aVar = new A();
    aVar.aFoo();
  }
}
```

위 코드에서는 `B` 클래스가 `A` 클래스에 의존성이 있다고 한다. 즉, 두 클래스 사이에 의존 관계가 생기게 된다.

왜냐하면, `B` 클래스의 `bFoo` 메서드에선 내부적으로 `A`라는 클래스를 사용하며, `A` 클래스에서 정의된 `aFoo`라는 메서드를 호출해서 사용하고 있기 때문이다.

향후, `A` 클래스에 새로운 메서드가 추가되거나 기존 메서드(여기서는 `aFoo`)의 형식 또는 기능이 바뀌면, `B` 클래스도 그에 따라 수정되거나 추가되어야 할 것이다.



### IoC를 적용하는 과정 - 소스 코드를 중심으로 우선 훑어보기

위와 같은 문제를 해결하는 방법은 다음과 같다. 먼저, 필요한 객체를 (`new` 키워드를 사용하는 등) 직접 생성하지 말고 외부에서 생성해서 가져오도록 해야 한다.

```java
public class Store {
    
    private Pencil pencil;
    
    public Store() {
        this.pencil = new Pencil();
    }
    
    public void use() {
      this.pencil.say();
    }
}
```

```java
public class Pencil {
    public void say() {
        System.out.println("pencil");
    }
}
```

위 코드에서는 `Store`가 `new` 키워드를 써서 `Pencil` 객체를 생성했기 때문에 `Store`가 `Pencil`에 의존성이 있다.

```java
public class Store {
    
    private Pencil pencil;
    
    public Store(Pencil newPencil) {
        this.pencil = newPencil;
    }
    
    public void use() {
      this.pencil.say();
    }
}
```

```java
public class Pencil {
    public void say() {
        System.out.println("pencil");
    }
}
```

위 코드에서는 Store가 직접 `Pencil` 객체를 생성하지 않고, 외부로부터 생성자를 통해 전달(주입)받기 때문에 의존성이 존재하지 않는다.

그러나 `Pencil`을 `Eraser`로(즉, Pencil 관련 이름들을 Eraser 관련 이름들로) 변경하거나, 나 말고 다른 개발자가 `Pencil`과 비슷한 의의를 가졌고 `Pencil`을 대신하는 `Eraser`라는 클래스로 개발하려고 할 때, 모든 명칭들을 일일히 다 고쳐야 하는 수고를 겪어야 한다는 문제점이 아직 존재한다.

  ```java
public class Store {
    
    private Product product;
    
    public Store(Product newProduct) {
        this.product = newProduct;
    }
    
    public void use() {
      this.product.say();
    }
}
  ```

```java
public interface Product {
    public void say();
}
```

```java
public class Pencil implements Product {
    public void say() {
        System.out.println("pencil");
    }
}
```

```java
public class Eraser implements Product {
    public void say() {
        System.out.println("eraser");
    }
}
```

위와 같이 작성하면, `Store`에 대한 수정을 할 필요 없이 `Pencil` 개발자는 `Pencil` 클래스에만 집중하고, `Eraser` 개발자는 `Eraser` 클래스에만 집중할 수 있다.

그런데, 프로그램 실행 시 `Store` 클래스가 호출되었을 때, `newProduct` 인스턴스 안에 들어갈 `Product` 객체는 `Pencil`과 `Eraser` 둘 중 어떤 종류로, 또 어디에서 생성해서 가져와야 할까?

  ```java
public class Store {
    
    private Product product;
    
    @Autowired
    @Qualifier("pencil")
    public Store(Product newProduct) {
        this.product = newProduct;
    }
    
    public void use() {
      this.product.say();
    }
}
  ```

```java
public interface Product {
    public void say();
}
```

```java
@Component("pencil")
public class Pencil implements Product {
    public void say() {
        System.out.println("pencil");
    }
}
```

```java
@Component("eraser")
public class Eraser implements Product {
    public void say() {
        System.out.println("eraser");
    }
}
```

위와 같이 Annotation을 작성하면, 프로그램 실행 시 `Store` 클래스가 호출되었을 때 Spring이 알아서 `Pencil` 객체를 생성해서  `newProduct` 인스턴스 안에 주입하므로 "pencil"이 출력된다. 만약 "eraser"를 출력하고 싶다면`@Qualifier`의 괄호 안의 값을 "eraser"로 변경하면 된다.

이 코드가 IoC를 모두 적용한 최종 형태이다.



### 컨테이너(Container)란?

개발자가 작성한 코드의 처리 과정을 위임받은 독립적인 존재라고 생각하면 된다. 컨테이너는 적절한 설정만 되었다면 누구의 도움 없이도 개발자가 작성한 코드를 스스로 참조한 뒤 **알아서 객체의 생성과 소멸을 컨트롤**해준다.

개발자가 작성한 코드는 컨테이너를 사용하게 됨으로써 개발자의 손을 떠나 컨테이너의 영역으로 떠나버리게 된다. 정확히 말하자면, 컨테이너가 맘대로 객체를 생성하는게 아니라, 프로그램을 이용하는 이용자의 호출이 있을 때 비로소 컨테이너가 동작하여 이용자가 필요로 하는 객체를 생성하여 가져다주는 구조이다.

Spring에는 위와 같이 객체를 관리하는 컨테이너가 존재하는데, 바로 `ApplicationContext`이다. 즉, 클래스에 `@Component`가 붙으면 **Spring 컨테이너**인 `ApplicationContext`에 해당 클래스를 등록하는 것이고, 생성자에 `@Autowired`가 붙으면 앞서 등록되었던 클래스의 객체를 생성하여 해당 생성자의 파라미터에 주입하는 것이다.

예전에는 `ApplicationContext`를 개발자가 직접 다룸으로써 컨테이너 설정을 해줘야 했지만, 현재는 Spring Framework의 `DispatcherServlet`이 자체적으로 `ApplicationContext`를 생성하고 사용하기 때문에, 개발자가 신경쓸 필요가 없다.



### IoC(Inversion of Control, 제어 역행)란?

일반적으로 지금까지 프로그램은 아래와 같은 작업을 반복해왔다. 무언가 필요한 쪽(B)에서 필요한 객체(A)를 생성하고, 생성된 객체의 메서드(aFoo)를 직접 호출해서 사용한다. 여기에서 각 객체는 프로그램의 흐름에 능동적으로 참여하고 있으며, 어떤 객체는 다른 객체에 의존성을 가진다. 즉, 모든 작업을 개발자가 직접 제어하는 구조이다.

- 객체 결정 및 생성 -> 의존성 객체 생성 -> 의존성 객체 내의 메서드 호출

이러한 구조를 뒤집어서, **객체를 생성 및 전달하는 작업을** 개발자가 직접 결정하는 것이 아닌 **외부에서 결정하는 매커니즘**을 IoC라 한다.

다시 말해, 개발자는 필요한 부품을 만들어 조립하는 방식의 개발을 하게 되며, 이렇게 조립된 코드의 생성 및 전달은 개발자에 의해서 제어되는 것이 아닌, 프레임워크의 내부에서 결정된 대로 이루어지게 되는 것을 말한다.

이때, 프레임워크 내부에서 객체의 생성 및 전달을 도맡아 하는 것이 **컨테이너**이고, Spring에서의 컨테이너는 `ApplicationContext`라 하며, `ApplicationContext`에서 관리하는 이러한 객체들을 **Bean**이라고 한다.

즉, 클래스에 `@Component`가 붙으면 Spring 컨테이너에 Bean을 등록하는 것이고, 생성자에 `@Autowired`가 붙으면 앞서 등록되었던 Bean의 객체를 생성하여 해당 생성자의 파라미터에 주입하는 것이다.



### 의존성 주입(Dependancy Injection, DI)이란?

특정 객체에 필요한 객체를 외부에서 생성하여 주입하도록 두 객체간의 관계를 연결시키는 것을 의미한다.

즉, DI를 통해 두 객체간의 관계가 성립되면, 이후 프로그램이 실행될 때 IoC를 통해 Bean을 생성하여 전달하는 것이다.

생성자에 `@Autowired`를 붙이면 DI가 수행된 것이며, 이후 프로그램이 실행될 때 생성자의 파라미터인 인스턴스에 IoC를 통해 생성된 Bean이 입력된다.



### 의존성 분리란?

**의존 관계 역전 원칙(Dependency Inversion Principle)**에 의해 의존 관계를 분리시키는 것이다.

'의존 관계 역전 원칙'의 사전적인 정의로는, 객체 지향 설계 방법론 중 하나로서, 상위 계층이 하위 계층에 의존하는 전통적인 의존 관계를 역전시킴으로써, 상위 계층이 하위 계층의 구현으로부터 독립되게 할 수 있다고 나와 있다.

앞선 예시를 통해 쉽게 설명하자면, `Store` 클래스가 `Pencil` 클래스에 직접적으로 영향을 받는 관계보다는, 그 사이에 `Product` 인터페이스를 둠으로써 `Pencil` 클래스의 개발이 `Store` 클래스에 직접적으로 영향을 미치지 않는 관계로 두는 것이 더 낫다는 말이다.

즉, 개발자는 클래스의 기능을 추상적으로 묶어둔 `interface`를 호출하도록 코드를 짜고, 이 `interface`를 `implement`하여 구현한 구현체를 따로 두면 된다.

이로써 객체들은 의존성이 생기지 않게 된다.



#### 이제 IoC의 과정을 다음과 같이 요약할 수 있다.

의존성 주입 -> 의존성 분리 -> 객체 생성 및 전달



### IoC를 사용하는 목적 - 호출 방식을 중심으로 설명

#### 클래스 호출 방식

클래스 내에 선언과 구현이 같이 있기 때문에, 다양한 형태로 변화가 불가능하다.

![image](https://user-images.githubusercontent.com/53200166/132811502-dc595a71-2d43-4224-a1c0-fa04d0b9bc1a.png)

#### 인터페이스 호출 방식

호출되는 클래스를 인터페이스와, 인터페이스를 상속받아 구현하는 클래스로 분리하였다. 구현 클래스 교체가 용이하여 다양한 변화가 가능하다. 그러나 아직 구현 클래스 교체 시 호출하는 클래스의 코드에서 수정이 필요하다. 따라서, 부분적으로 종속적이다.

![image](https://user-images.githubusercontent.com/53200166/132811510-67447444-5b26-4e98-aaa7-9330f73e2dc1.png)

#### 팩토리 호출 방식

팩토리가 구현 클래스를 생성하기 때문에 호출하는 클래스는 팩토리를 호출하는 코드로 충분하다. 구현 클래스 변경 시 팩토리만 수정하면 되기 때문에 호출 클래스에는 영향을 미치지 않는다. 그러나 호출하는 클래스에서 팩토리를 호출하는 코드가 들어가야 하는 것 또한 팩토리에 의존한다는 것을 의미한다.

![image](https://user-images.githubusercontent.com/53200166/132811515-e085d994-fe0b-474f-ab0d-5c6b09e13109.png)

#### IoC

이제 어떠한 것에도 의존하지 않는 형태가 되었다. 실행 시점에 클래스 간의 관계가 현성된다. 즉, 의존성이 삽입된다.

![image](https://user-images.githubusercontent.com/53200166/132811521-6583aad1-c38e-4a60-b3f1-9f66111c0f01.png)



### Spring MVC Framework에서의 DI 예제

![image](https://user-images.githubusercontent.com/53200166/132779625-191d40a9-4dbf-4022-9be1-ad490fb50dec.png)

프로젝트 구조가 위와 같을 때, controller 패키지 밑에 `MemberController.java`, service 패키지 밑에 `MemberService.java`, repository 패키지 밑에 `MemberRepository.java` 및 `MemberMemberRepository.java`를 생성한다. 이때, `MemberController`와 `MemberService`는 클래스, `MemberRepository`는 인터페이며, `MemberMemberRepository`는 이를 implement하는 클래스라고 가정하자.

※ 원래라면

- MemberService 또한 interface여야 하고, 이를 implement하여 구현한 구현체가 있어야 함
- 구현체의 명명 규칙은 `[interface명]Impl` 이므로 MemberRepository 인터페이스의 구현체 이름은 `MemberRepositoryImpl`로 정해야 함

이어야 하지만, 이렇게 할 수도 있다는 예시를 보여주는 것이라고 생각하고 넘어가도록 하자.

```java
public class MemberController {

}
```

```java
public class MemberService {

}
```

```java
public interface MemberRepository {

}
```

```java
public class MemberMemberRepository implements MemberRepository {

}
```

이제 `MemberController` 클래스 안에 아래와 같이 내용을 작성하고 `@Controller` annotation을 추가하면, Spring 컨테이너에 `MemberController`라는 객체를 등록하고 관리하게 된다. 하지만 `memberService`에서 오류가 생겨 빨간 줄이 생기는데, 그것은 아직 `MemberService`가 순수 자바 클래스여서 Spring 컨테이너에 등록되지 않았기 때문이다. 따라서 `@Autowired` annotation을 써도 빨간 줄이 사라지지 않는다.

![image](https://user-images.githubusercontent.com/53200166/132779634-c3536cea-0822-4d99-9163-6880fa7a13a9.png)

이때, 아래와 같이 MemberService에 `@Service` annotation을 붙여주면 Spring 컨테이너에 `MemberService`라는 객체를 등록하고 관리하게 되어, 빨간 줄이 사라진다.

![img](https://blog.kakaocdn.net/dn/baledF/btqI6Oo52xq/8mJbjXbvCKGAbFqXJSzsDK/img.png)

이제 `MemberService` 클래스 안에 아래와 같이 내용을 작성하면 `memberRepository`에서 오류가 생겨 빨간 줄이 생기는데, 그것은 아직 `MemberRepository`가 순수 자바 클래스여서 Spring 컨테이너에 등록되지 않았기 때문이다. 따라서 `@Autowired` annotation을 써도 빨간 줄이 사라지지 않는다.

![image](https://user-images.githubusercontent.com/53200166/132779643-91bb85ea-7935-40c4-b97c-b3d8c6521bc1.png)

그러면 `MemberRepository`를 Spring 컨테이너에 등록해야 한다는 생각이 들텐데, 이것은 `interface`이기 때문에 이를 구현한 구현체로 가야 한다. 따라서 구현체인 `MemberMemberRepository`에 아래와 같이 `@Repository` annotation을 붙여주면 Spring 컨테이너에 `MemberRepository` 라는 객체를 등록하고 관리하게 되어, 빨간 줄이 사라진다. 즉, Spring 컨테이너에 등록된 객체는 `MemberRepository` 이지만, 프로그램 이용자의 호출에 따라 객체를 생성해야 할 경우 `MemberMemberRepository`를 생성해서 전달하게 된다.

![image](https://user-images.githubusercontent.com/53200166/132779653-8cee52e2-6fb3-48e7-bb5b-28591da3702f.png)

순서를 정리하면 다음과 같다.

1. MemberController와 MemberService는 @Autowired를 통해 연결된다.
2. 그러면 MemberController가 생성될 때, Spring 컨테이너에 등록된 MemberService 객체를 넣어주게 된다. 이 과정을 DI라고 한다.
3. 그 다음, MemberService와 MemberRepository는 @Autowired를 통해 연결된다.
4. 그러면 MemberService가 생성될 때 Spring 컨테이너에 등록된 MemberRepository를 객체를 넣어줘야겠지만, 이는 인터페이스이기 때문에 구현체인 MemberMemberRepository를 대신 넣어주게 된다.
5. 이를 통해 MemberController, MemberService, MemberRepository가 서로 연결된다.



한편, `@Controller`, `@Service`, `@Repository`에 대해서 알아보자. 소스코드를 뒤져보면, 이 Annotation들은 각각 `@Component` Annotation을 가지고 있는 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/53200166/132779673-907599e3-ffea-480f-a492-122234fbde00.png)

![image](https://user-images.githubusercontent.com/53200166/132779679-d199f3e7-f35a-4ada-88f5-9b9cde217eb5.png)

![image](https://user-images.githubusercontent.com/53200166/132779685-8d4cbe9d-a39e-4f6f-8ea7-8d3a5db631bd.png)

그런데, `@Component` Annotation이 붙어 있는 객체는 Spring 컨테이너에 등록된다.

따라서, `@Component` Annotation을 가지고 있는 `@Controller`, `@Service`, `@Repository`가 붙어있는 객체는 컨테이너에 등록된다.



### Spring에서의 DI 방식

지금까지 DI의 방식으로 `@Autowired` Annotation을 붙이는 방법 하나로만 설명해왔으나, 사실은 다른 방식들도 존재한다.

- ~~XML 구성 파일을 생성하여 주입하는 방식~~

  - XML 구성 파일을 생성하여 설정 값을 입력하고 기능을 사용하고자 하는 클래스에서 불러 사용하는 방식
  - XML 구성 파일을 수정하면 자바 코드를 최대한 수정하지 않기 때문에 재컴파일하지 않더라도 변경사항을 쉽게 적용한다.
  - 모델 클래스, 서버 클래스, XML 설정 파일이 분산되어 있어, 데이터 처리정보를 확인하기 위해서는 3파일 모두 봐야한다는 귀찮음이 있다.

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans
  	... 생략
      >
      <bean id="userRepository" class="com.example.demo.UserRepositoryImpl" />
      <bean id="userService" class="com.example.demo.UserServiceImpl">
      	<constructor-arg ref="userRepository" />
      </bean>
  </beans>
  ```

  위 예시(Application.xml)에서 각각의 Bean을 등록하고, 의존성을 주입받는 클래스에서는 constuctor-arg를 통해 다른 Bean을 주입받는 것을 볼 수 있다.

- **Annotation을 이용하여 주입하는 방식**

  - ~~@Configuration 어노테이션으로 주입하고자 하는 클래스 이름 설정하고, @Bean 어노테이션으로 Bean 객체를 주입한다.~~
  - 데이터에 대한 유효성을 모델클래스에 직접 명시하기 때문에 컴파일 시 바로 확인이 가능하다.
  - 자바 클래스로 생성된 구성파일은 컴파일 될 시, 내부적으로 XML 파일 형식으로 변경되어 주입하게 된다.

... 작성 중



------



## AOP (Aspect Oriented Programming, 관점 지향 프로그래밍)



각 비지니스 로직의 앞/뒤에서 공통적으로 처리되어야 하는 **로그 처리, 보안 처리, 예외 처리, 트랜잭션 처리**와 같은 코드를 **별도로 분리**해 하나의 단위로 묶어 관리하는 것을 AOP라 한다.

```java
class A {
 
    method a1() {
        작업 P
        method a1이 하는 일들
        작업 Q
    }
 
    method a2() {
        작업 P
        method a2가 하는 일들
        작업 Q
    }
}
 
class B {
    method b1() {
        작업 P
        method b1이 하는 일들
        작업 Q
    }
}
```

위와 같이 동일한 작업을 하는 코드 P, Q가 여기저기에서 흩어져서 사용되고 있으면, 코드 변경이 필요한 경우 일일이 다 찾아서 바꿔야 한다는 문제가 있다.

AOP는 그렇게 하지 않고 여기저기에서 사용되는 중복되는(위 예시에서는 작업 P, Q와 같은) 코드를 떼어내서 분리하고, 각 메서드들은(위 예시에서는 method a1, a2, b1) 자신이 해야할 작업만 가지고 있자는 개념이다.



비지니스 로직을 핵심적인 기능이라고 한다면,  로그 처리, 보안 처리, 예외 처리, 트랜잭션 처리 등의 작업을 부가적인 기능이라고 할 수 있다. 객체지향 프로그래밍(OOP)에서 **핵심 기능으로부터 부가 기능을 분리해서 모듈화**하는 것은 어려운 일인데, 관점지향 프로그래밍(AOP)은 그것을 가능하게 한다. 즉, OOP의 의의가 비지니스 로직의 모듈화라면, AOP의 의의는 부가 기능의 모듈화인 것이다. 단, AOP는 OOP와 상반되는 개념이 아니라, OOP를 OOP답게 프로그래밍하기 위한 기법이다.

![image](https://user-images.githubusercontent.com/53200166/133382246-d5174f81-36af-4ca0-9ed5-576d37b52a7b.png)

각각의 Service는 핵심 기능에서 바라보았을 때는 Board, User, XXX 등에서 공통된 요소가 없다. 이런 관점에서는 각각의 Service는 각자 코드를 구현하고 있다. 하지만 부가 기능이란 관점에서 바라보면 상황이 달라진다.

 ![image](https://user-images.githubusercontent.com/53200166/133382266-19c6c15e-70b4-46f5-96d3-08635b9daf62.png)

부가 기능의 관점에서 바라보면, 각각의 Service는 before() 메서드와 after() 메서드라는 공통된 부분이 있는 것을 알 수 있다.

이러한 공통된 부분을 추출해서 한데 모아 관리하는 것이 AOP이다.

이때, 이 공통된 부분(부가 기능)을 **Advice**, 이 공통된 부분들의 집합을 **Aspect**라고 한다.

AOP는 디자인 패턴 중 프록시 패턴을 기반으로 한다.



### 예제를 보기 전, 알아두어야 할 AOP 관련 용어들

처음엔 예제를 먼저 보고 용어를 암기하고 싶었으나, 그게 불가능하다는 것을 깨달았다. 아래 용어들을 숙지하고 예제를 보는 것이 더 낫다.

- **Aspect** : 흩어져 있는 관심사인 부가 기능들이 모듈화한 것. 즉, **부가 기능**을 수행하는 메서드들이 모인 **클래스**
- **Target** : **핵심 기능**. 즉, 모듈화된 부가 기능(Aspect)을 수행해줘야 하는 주요 비지니스 로직(클래스, 메서드)
- **Advice** : Aspect에 속한, **부가 기능**을 수행하는 **메서드**
  - 모든 Advice들은 아래와 같은 Annotation을 붙여야 하는데, **언제** 부가 기능을 Target에 적용할지를 정의한다.
    - `@Before` : 메서드 실행 전
    - `@After` : 메서드 실행 후
    - `@AfterReturning` : 반환된 후
    - `@AfterThrowing` : 예외가 던져지는 시점
    - `@Around` : 메서드 실행 전, 후
- **JoinPoint** : Advice를 적용할 수 있는 모든 지점을 말한다.
  - 다른 AOP 프레임워크와 달리, Spring에서는 메서드 JoinPoint만 제공한다.
    따라서 Spring 프레임워크 내에서 JoinPoint라고 하면 **메서드**를 가리킨다고 생각해도 좋다.

- **Pointcut** : JoinPoint의 부분 집합으로서, JoinPoint들 중 실제로 Advice가 적용되는 지점

- **Weaving** : Advice를 Target에 적용하는 행위



### 일단 AOP 개발해보기(예제)

- AOP를 적용하기 위해서는 다음 작업들이 수반되어야 한다.
  - 메인 클래스에 `@EnableAspectJAutoProxy`가 붙어야 한다.
  - Aspect가 되는 클래스에 `@Aspect` Annotation이 붙어야 한다.
  - Aspect가 되는 클래스(이하 Aspect)와 Target이 되는 클래스(이하 Target) 모두 Bean으로 등록되어야 한다.
    따라서 Aspect와 Target, 두 클래스 모두에 `@Component` Annotation이 붙어야 한다.
  - Aspect 내의 각 Advice(즉, 메서드)들에는 `@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around` 중 하나가 붙어있어야 하며,
    해당 Annotation에는 Pointcut이 명시되어야 한다.

- Target Class에는 `@Component` Annotation 이외에는 AOP에 관련한 어떤 코드도 추가되지 않는다.
  AOP와 관련된 모든 코드는 Aspect 내에서 모두 작성된다.

```java
/* 디렉터리 구조 */

src/
   └main/
      └java/
         └Main.java
         └com.seongbo.school/
            ...
            └service/
               └ExampleService1.java
               └ExampleService2.java
            └aspect/
               └TimeCheckAspect.java
            ...
```

```java
/* Target Class */

@Component
public class ExampleService1 {
    public void target1() {
       System.out.println("Hello, world!");
    }
}
```

```java
/* Target Class */

@Component
public class ExampleService2 {
    public String target2() {
       return "Hello, world!";
    }
}
```

```java
/* Aspect */

@Aspect
@Component
public class TimeCheckAspect {
    
    // Advice를 적용할 지점을 명시함
    @Pointcut("execution(public void com.seongbo.school.service.ExampleService1.target1(..))")
    private void pointcutMethod1() {
        
    }
    
    // Advice를 적용할 지점을 명시함
    @Pointcut("execution(public String com.seongbo.school.service.ExampleService2.target2(..))")
    private void pointcutMethod2() {
        
    }
    
    // 명시된 Pointcut 중 사용하고 싶은 것을 입력
    /* 아래 예시에서는 "pointcutMethod1()"을 입력하였으므로, 앞으로는 pointcutMethod1()에서 정의된 지점,
       즉 ExampleService1의 target1() 메서드가 호출될 때마다 calculateExecutionTime()이 수행된다.
       만약 "pointcutMethod2"를 입력한다면
       ExampleService2의 target2() 메서드가 호출될 때마다 calculateExecutionTime()이 수행될 것이다.
    */
    @Around("pointcutMethod1()")
    public Object calculateExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
        // 해당 클래스 처리 전의 시간
        StopWatch sw = new StopWatch();
        sw.start();
        
        // 해당 클래스의 메서드 실행
        /* 다른 Annotation들은 proceed() 메서드가 호출되지 않아도 되지만,
           @Around의 경우 반드시 proceed() 메서드가 호출되어야 한다.
        */
        Object result = pjp.proceed();
        
        // 해당 클래스 처리 후의 시간
        sw.stop();
        long executionTime = sw.getTotalTimeMillis();
        
        String className = pjp.getTarget().getClass().getName();
        String methodName = pjp.getSignature().getName();
        String task = className + "." + methodName;
        
        log.debug("[ExecutionTime] " + task + "-->" + executionTime + "(ms)");
        
        return result;
    }
}
```



### Pointcut 설정 방법

- **Pointcut의 기본 형식** : `"execution([접근제한자] [리턴타입] [경로].[클래스명].[메서드명]([파라미터명]))"`

  - Pointcut 지정자로는 여러 종류가 있는데, `execution()`을 가장 많이 사용한다.
    - `execution()` : 접근제한자, 리턴타입, 클래스/인터페이스, 메서드명, 파라미터타입 등을 전부 조합할 수 있는 가장 세심한 지정자
    - `within()` : execution 지정자에서 클래스/인터페이스까지만 적용된 경우. 즉, 적용된 클래스/인터페이스에 속한 모든 메서드가 Pointcut이 된다.
    - `@annotation()` : 타겟 메서드에 특정 Annotation이 지정된 경우
    - `bean()` : 특정 bean에 속해 있는 메서드들을 타겟으로 지정하고 싶은 경우
  - 지정자의 괄호 안에 들어가는 부분을 타겟 명세라고 한다.
  - private, public, protected 등 접근제어자는 생략 가능하다.

- **Pointcut의 다양한 설정 방법**

  - `@Pointcut` Annotation을 사용하여 메서드를 생성한 후 Advice에 간접 연결

    ```java
        @Pointcut("execution(public void com.seongbo.school.service.ExampleService1.target1(..))")
        private void pointcutMethod1() {
            
        }
    
        @Around("pointcutMethod1()")
        public Object calculateExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
            ...
        }
    ```

  - Advice에 직접 연결

    ```java
        @Around("execution(public void com.seongbo.school.service.ExampleService1.target1(..))")
        public Object calculateExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
            ...
        }
    ```

  - Annotation을 생성하여 Target에 간접 연결

    1. Annotation을 생성한다.

       ```java
       @Target(ElementType.METHOD)
       @Retention(RetentionPolicy.RUNTIME) // 적용할 수 있는 RetentionPolicy에는 여러 가지가 있음
       public @interface Example {
           
       }
       ```

    2. Pointcut의 지정자로 `@annotation()`을 사용하여, 괄호 안에는 생성한 Annotation의 이름을 작성한다.

       ```java
           @Around("@annotation(Example)")
           public Object calculateExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
               ...
           }
       ```

    3. Target 메서드에 생성한 Annotation을 붙인다.

       ```java
       @Component
       public class ExampleService1 {
       
       	@Example
           public void target1() {
              System.out.println("Hello, world!");
           }
       }
       ```

    

- **Pointcut 표현식**

  - '*'는 '모든'을 나타낸다.
  - '..'는 '0개 이상' 또는 '모든 하위 경로'의 의미를 나타낸다.
  - '&&', '||', '!'을 사용하여 조합할 수 있다.

  | Pointcut                                                     | JoinPoints                                                   |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | "execution(public void set\*(..))"                           | 접근제한자가 public, 리턴타입이 void, 메서드 이름이 set으로 시작하고<br />파라미터가 0개 이상인 모든 메서드 |
  | "execution(!private \* \*(..))"                              | 접근제한자가 private가 아닌 모든 메서드                      |
  | "execution(\* get\*(\*, \*))"                                | 메서드 이름이 get으로 시작하고 파라미터가 2개인 모든 메서드  |
  | "execution(* com.seongbo.school.\*.\*(..))"                  | com.seongbo.school 경로의 모든 메서드                        |
  | "execution(* com.seongbo.school..\*.\*(..))"                 | com.seongbo.school 경로와 하위 경로의 모든 메서드            |
  | "execution(* *Impl(Integer, ..))"                            | 메서드 이름이 Impl로 끝나고 첫 번째 파라미터의 타입이 Integer이며<br />파라미터의 총 개수가 1개 이상인 모든 메서드 |
  | "within(com.seongbo.school.service.Pivot*)"                  | com.seongbo.school.service 경로 내에서 클래스 이름이 Pivot으로 시작하는<br />모든 클래스들에 속해 있는 모든 메서드 |
  | "@annotation(org.springframework.transaction.annotation.Transactional)" | @Transactional Annotation을 갖는 모든 메서드                 |
  | "bean(*Repository)"                                          | 이름이 Repository로 끝나는 모든 Bean들에 속해 있는 모든 메서드 |

  

### Advice 설정 방법

- **메서드 형식**

  ※ 아래에 작성한 형식 이외에도 몇 가지가 더 있는 것 같은데, 아직 다 찾아보지 못했음

  - `@Around`를 사용하는 경우

    ```java
    @Around("[Pointcut]")public Object [메서드명](ProceedingJoinPoint [변수명1]) throws Throwable {    ...	// 타겟의 실행 전에 수행할 작업    Object [변수명2] = [변수명1].proceed();	// 타겟의 실행    ...	// 타겟의 실행 후에 수행할 작업    return [변수명2];}
    ```

  - `@Before`, `@After`를 사용하는 경우

    ```java
    @Before("[Pointcut]")public void [메서드명](JoinPoint [변수명]) {    ...}
    ```

  - `@AfterReturning`을 사용하는 경우

    ```java
    @AfterReturning(pointcut="[Pointcut]", returning=["[타겟의반환값을받는변수명]")public void [메서드명](JoinPoint [변수명], [타겟의반환값의자료형] [타겟의반환값을받는변수명]) {    ...}
    ```

    ```java
    @AfterReturning(pointcut="[Pointcut]", returning=["[타겟의반환값을받는변수명]")
    public void [메서드명](JoinPoint [변수명], Object [타겟의반환값을받는변수명]) {
        ...
    }
    ```

  - `@AfterThrowing`을 사용하는 경우

    ```java
    @AfterThrowing(pointcut="[Pointcut]", throwing=["[예외명]")
    public void [메서드명](JoinPoint [변수명], Exception [예외명]) throws Exception {
        ...
    }
    ```

    ```java
    @AfterThrowing(pointcut="[Pointcut]", throwing=["[예외명]")
    public void [메서드명](JoinPoint [변수명], Throwable [예외명]) throws Throwable {
        ...
    }
    ```

    

### AOP 구현 방법 비교

- java proxy, cglib, aspectJ

#### Java proxy (InvocationHandler)

- `런타임`시에 Target method가 호출될 때 Advice(프록시 할 기능)을 적용
- JDK Proxy는 `인터페이스`에 대한 Proxy만을 지원
- 리플렉션을 사용하여 구현한 기술이기에 상대적으로 퍼포먼스가 떨어짐

#### CGLIB (MethodInterceptor)

- CGLIB(Code Generator Library) - 코드 생성 라이브러리
- java proxy와 동일하게 `런타임`시에 Advice 적용
- `클래스`에 대한 Proxy가 가능
- 메서드가 처음 호출 되었을때 동적으로 bytecode를 생성하여 이후 호출에서는 재사용

#### AspectJ

- Runtime이 아닌 `Compile` 시점에 Aspect를 적용

...작성 중



## PSA



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