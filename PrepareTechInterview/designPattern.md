# Design Pattern

- 소프트웨어 설계에 있어 공통적으로 발생하는 문제들에 대한 표준적인 해법과 작명법을 제안한 것

- 효율적인 코드를 만들기 위한 방법론

- 객체간 응집도는 높이고, 결합도는 낮게, 요구사항 변경 시 코드 변경은 최소화해야 하는 객체지향적 설계 방향을 지키기 위함

- 프로젝트 진행 시 의사소통 용도로 사용됨
  - Q. 애플리케이션이 전역적으로 접근하고 관리할 필요가 있는 데이터를 포함한다. 동시에 이러한 데이터는 시스템에 유일하다. 어떤 방식으로 클래스에서 생성되는 객체의 수를 제어하고 클래스의 인터페이스에 접근하는 것을 제어해야 하는가?
  - A1(디자인 패턴을 모를 경우). 클래스의 생성자를 public으로 정의하지 말고 private이나 protected로 선언해 외부에서 생성자를 이용해 객체를 일단 생성할 수 없게... 어쩌고 저쩌고
  - A2(디자인 패턴을 알 경우). 싱글톤 패턴을 사용합시다. 끝 



## GoF(Gang of Fout) Design Pattern



### Creational Pattern (생성 패턴)

객체의 생성과 관련된 패턴



#### 1. Singleton Pattern

인스턴스를 1개로 제한(static)하며, Global하게 어디서든 접근할 수 있도록 하는 디자인 패턴

즉, 애플리케이션이 시작될 때 최초 한 번만 메모리를 할당함

private 생성자를 가지며, 하나의 인스턴스를 반환하는 getInstance() 메서드를 구현한다.

```java
public class Singleton {
    // Eager Initialization
    private static Singleton uniqueInstance = new Singleton();

    private Singleton() {
        
    }

    public static Singleton getInstance() {
      return uniqueInstance; 
    }
    
    public void doAction() {
        System.out.println("Hello, world!");
    }
}

public class User {
    public void use() {
        Singleton.getInstance.doAction();
    }
}
```

Spring Boot에서 Singleton Pattern은 IoC 컨테이너가 관리하는 Bean과 관련된다.



#### 2. Prototype Pattern

![image](https://user-images.githubusercontent.com/53200166/135371514-f4a6b612-95d2-4db5-9498-493487f59519.png)

기본 원형이 되는 인스턴스를 사용해서 생성할 객체의 종류를 명시하고 이렇게 만들어진 **객체를 복사해서 새로운 객체를 생성하는 패턴**. 즉 DeepCopy를 말함

```java
MyClass a = new MyClass();
MyClass b = new MyClass();
b = a;
```

위 행위는 b가 a의 주솟값을 가리키는 것이 되어, 복제가 아닌 참조가 된다.

따라서 a와 동일한 인스턴스, 즉 a 내부에 있는 필드값과 동일한 필드값을 그대로 가진 복제품을 만들기 위해서는 아래 예시와 같이 Java에서 이미 정의된 `Cloneable` interface를 implement하고 `clone()` 메서드를 구현하면 된다.

```java
public class Shape implements Cloneable {
    private String shape;
    
    public String getShape() {
        return shape;
    }
    public void setShape(String shape) {
        this.shape = shape;
    }
    
    public Shape clone() throws CloneNotSupportedException {
        Shape sp = (Shape)super.clone();
        return sp;
    }
}

public class MainClass{
	Shape sp1 = new Shape();
    sp1.setShape("Circle");
    System.out.println(sp1.getShape()); //Circle
    
    Shape sp2 = sp1.clone();
    System.out.println(sp2.getShape()); //Circle
}
```

Spring Boot에서 Prototype Pattern은 IoC 컨테이너가 관리하는 Bean과 관련된다.



---



### Structural Pattern (구조 패턴)

클래스나 객체를 조합해 더 큰 구조를 만드는 패턴



#### 1. Proxy Pattern

실제 기능을 수행하는 객체(Real Object) 대신 가상의 객체(Proxy Object)를 사용해 로직의 흐름을 제어하는 디자인 패턴

![image](https://user-images.githubusercontent.com/53200166/135368806-e70b5d6f-82e0-4912-af26-236fbd4e1031.png)

- Client가 해당 합수를 직접 호출하지 않고 Proxy를 호출한다.
- Proxy Class에서 실제 Class를 호출한다. 이때, Proxy Class는 실제로 호출할 Class의 객체를 이미 가지고 있다.
- 실제 Class로부터 Client에게 결과값을 반환한다.

```Java
public interface Subject {
    void doAction();
}

public class RealSubject implements Subject {
    
    @Override
    public void doAction() {
        System.out.println("Hello, world!");
    }
}

public class ProxySubject implements Subject {
    
    private realSubject = new RealSubject();
    
    @Override
    public void doAction() {
        // 전처리
        realSubject.doAction();
        // 후처리
    }
}

public class Client {
    public static void main(String[] args) {
        Subject subject = new ProxySubject();
        subject.doAction();
    }
}
```

Spring Boot에서 Proxy Pattern은 AOP와 관련된다.



---



### Behavioral Pattern (행위 패턴)

객체나 클래스 사이의 알고리즘이나 책임 분배에 관련된 패턴



#### 1. Template Method Pattern

일정한 프로세스(알고리즘)를 가진 요구사항을 구현하기 위해 사용하는 패턴

알고리즘의 구조를 메서드에 정의하고, 하위 클래스에서 알고리즘 구조의 변경없이 알고리즘의 구성요소들을 재정의하는 패턴

즉 알고리즘의 흐름이 정해져 있지만, 특정 부분은 필요에 따라 변경해야할 때 사용한다.

슈퍼클래스에 기본적인 로직의 흐름을 작성하고, 일부 변경이 필요한 부분은 서브클래스에서 오버라이딩할 수 있도록 추상 메소드로 두며, 서브클래스에서 필요에 맞게 이를 구현하여 사용한다.

##### 예제 1

```java
public abstract class Template {

    final public void excute() {
        //... 무언가 처리
        method1();
        //... 무언가 처리
        method2();
        //... 무언가 처리
    }

    protected abstract void method1();
    protected abstract void method2();
}

public class Concrete extends Template {

    @Override
    protected void method1() {
        System.out.println("Hello, world! 1");
    }

    @Override
    protected void method2() {
        System.out.println("Hello, world! 2");
    }
}

public class Main {

    public static void main(String[] args) {
        Concrete concrete = new Concrete();
        concrete.excute();
    }
}
```

##### 예제 2

![image](https://user-images.githubusercontent.com/53200166/135373587-f49af68b-ffd2-4703-8db6-084cc1a557d0.png)

사람들의 하루 일과를 나타내는 DailyRoutine 메서드는 'getUp -> eatBreakfast -> goToWork -> **work** -> returnTOHome -> **relax** -> sleep' 라는 flow를 갖는다. 그런데, work와 relax 등 일부만 직업에 따라 다를 수 있다.  따라서 해당(일부) 메서드만 subClass에서 재정의하도록 한다.

```java
public abstract class Worker {

    public void DailyRoutine()
    {
        getUp();
        eatBreakfast();
        goToWork();
        work();
        returnToHome();
        relax();
        sleep();
    }

    private void getUp() {
        System.out.println("getUp!");
    }

    private void eatBreakfast() {
        System.out.println("getBreakfast");
    }

    private void goToWork() {
        System.out.println("goToWork");
    }

    protected abstract void work();

    private void returnToHome() {
        System.out.println("returnToHome");
    }

    protected abstract void relax();

    private void sleep() {
        System.out.println("sleep");
    }
}

public class FireFighter extends Worker {

    @Override
    protected void work() {
        System.out.println("FireFighter`s work");
    }

    @Override
    protected void relax() {
        System.out.println("FireFighter`s relax");
    }
}

public class Postman extends Worker {

    @Override
    protected void work() {
        System.out.println("Postman`s Work");
    }

    @Override
    protected void relax() {
        System.out.println("Postman`s relax");
    }
}

public class Main {

    public static void main(String[] args) {
        Worker fireFighter = new FireFighter();
        Worker postMan = new Postman();

        fireFighter.DailyRoutine();
        System.out.println("--------------------------------");
        postMan.DailyRoutine();
    }
}
```

```
getUp!
getBreakfast
goToWork
FireFighter`s work
returnToHome
FireFighter`s relax
sleep
--------------------------------
getUp!
getBreakfast
goToWork
Postman`s Work
returnToHome
Postman`s relax
sleep
```

Spring Boot에서 Template Method Pattern은 DispatcherServlet과 관련된다.



#### 2. Strategy Pattern

객체들이 할 수 있는 행위 각각에 대해 전략 클래스를 생성하고, 유사한 행위들을 캡슐화 하는 인터페이스를 정의하여, 객체의 행위를 동적으로 바꾸고 싶은 경우 직접 행위를 수정하지 않고 전략을 바꿔주기만 함으로써 행위를 유연하게 확장하는 방법을 말한다.

OOP(객체 지향)의 주요 원칙중 하나로 Open-Closed Principle(개방폐쇄원칙)이 있다. 간략히 말하면 **"소프트웨어의 구성요소들은 확장에는 열려있어야하고, 변경에는 닫혀있어야 한다."** 라는 원칙이다. 즉, 요구사항의 변경이 있을 때 **기존 구성요소의 수정은 최소화 하되 확장에는 적극적이어야 한다는 말**이다. 확장에는 기존 구성요소의 변경이 없다. **결합도가 낮다**는 의미이기도 하다. strategy Pattern은 개방폐쇄원칙에 잘 맞아 떨어지는 패턴이다.

![image](https://user-images.githubusercontent.com/53200166/135376296-fcd6ba1e-3b0f-4686-8623-515f964fe15f.png)

```java
public interface Strategy {
    void strategy();
}

public class BusStrategy implements Strategy {
    @Override
    public void strategy() {
        System.out.println("버스로 갑니다.");
    }
}
public class CarStrategy implements Strategy {
    @Override
    public void strategy() {
        System.out.println("자동차로 갑니다.");
    }
}
public class TaxiStrategy implements Strategy {
    @Override
    public void strategy() {
        System.out.println("택시로 갑니다.");
    }
}

public class TransportationToAirPort {

    Strategy strategy;

    public void setStrategy(Strategy strategy){
        this.strategy = strategy;
    }

    public void printHowToGoAirPort(){
        strategy.strategy();
    }

    public static void main(String[] args) {
        Strategy[] strategies = {new BusStrategy(), new CarStrategy(), new TexiStrategy()};

        for (Strategy strategy : strategies) {
            TransportationToAirPort transportationToAirPort = new TransportationToAirPort();
            transportationToAirPort.setStrategy(strategy);
            transportationToAirPort.printHowToGoAirPort();
        }
    }
}
```

Spring Boot에서 Strategy Pattern는 IoC/DI와 관련된다.

---



## Architecture Pattern

