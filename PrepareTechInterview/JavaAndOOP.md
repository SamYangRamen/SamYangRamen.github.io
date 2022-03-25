# JAVA



## Wrapper Class

원시 자료형(Primitive Type)을 객체 형태로 사용하기 위해 만든 클래스

| primitive type | wrapper class |
| -------------- | ------------- |
| byte           | Byte          |
| char           | Character     |
| int            | Integer       |
| float          | Float         |
| double         | Double        |
| boolean        | Boolean       |
| long           | Long          |
| short          | Short         |



### Boxing & UnBoxing

- Boxing : 원시 타입의 값을 객체로 만드는 과정

  ```java
  Integer number = new Integer(10);
  ```

- UnBoxing : 객체로부터 원시 타입의 값을 얻어내는 과정

  ```java
  int n = number.intValue();
  ```

※ JDK 1.5부터는 아래와 같은 기능을 제공한다.

- AutoBoxing : 자동화된 Boxing

  ```java
  Integer number = 10;
  ```

- AutoUnBoxing : 자동화된 UnBoxing

  ```
  int n = number;
  ```



### Wrapper Class의 메모리 참조 방식

- 클래스의 필드가 아닌 원시 타입의 지역변수에 할당한 값은 stack 영역을 점유하지만, Wrapper Class로 생성된 객체에 할당한 값은 heap 영역을 점유한다.
- Wrapper Class로 생성된 객체에 값을 더하는 행위는, 해당 객체가 가리키고 있던 원래 값에 더해진 값을 새롭게 heap 영역에 할당하는 것이다.

```java
public class Main {
    public static void main(String[] args) {
        Integer a = 300;
        a += 300;
        Integer b = a;
        b += 300;
        Integer c = 600;
        c = null;
    }
}
```

위 코드의 실행 과정은 아래와 같다.

```java
Integer a = 300;
a += 300;
```

![image](https://user-images.githubusercontent.com/53200166/158560476-f5a6f2a1-1ed9-43fc-91d3-ec4de1c0883f.png)

```java
Integer b = a;
b += 300;
```

![image](https://user-images.githubusercontent.com/53200166/158560441-97fcc115-34f9-45e9-8110-898debc25535.png)

```java
Integer c = 600;
c = null;
```

![image](https://user-images.githubusercontent.com/53200166/158560578-f5d5d21f-a568-4681-975d-da2c50182494.png)



한편, Wrapper Class로 생성한 변수는 객체이기 때문에 아래 코드의 `a == b`는 `a`가 참조하는 heap 영역의 주솟값과 `b`가 참조하는 heap 영역의 주솟값을 비교하는 것이다. 따라서 Wrapper Class로 생성한 객체가 참조하는 원래 값을 비교하고자 할 때는 `equals` 메서드를 사용해야 한다. 

```java
public class Main {
    public static void main(String[] args) {
        Integer a = 300;
        Integer b = 300;
        System.out.println(a == b); // false
        System.out.println(a.equals(b)); // true
    }
}
```



---



## GC (Garbage Collection)

JAVA에서 객체가 생성되면 JVM의 Heap 영역의 메모리를 점유한다. 이때, 한정된 메모리에서 현재 사용하지 않는 객체가 점유한 공간을 제때 정리해주지 않으면 메모리 공간이 부족해질 것이다.

GC는 JVM 상에서 더 이상 사용되지 않는 데이터가 할당되어 있는 Heap 메모리를 해제시켜주는 장치로, 자동으로 동작하기 때문에 특별한 경우가 아니면 메모리 관리를 개발자가 직접 해줄 필요가 없다.



### Weak generational hypothesis

GC를 도입하기 위해 전제된 것

- 대부분의 객체는 금방 접근 불가능한 상태가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.



### Mark & Sweep & Compact

- GC가 스택의 모든 변수(정확히는 Root Space)를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아내는 과정. Mark 되지 않은 객체는 접근할 수 없는(Unreachable) 객체로 판단하고 메모리를 돌며 제거(Sweep)한다. Sweep 과정에 의해 삭제되면 메모리 단편화가 발생하는데, Compact를 통해 빈자리들을 채워준다.
- GC의 Root가 될 수 있는 대상은 stack 영역에 존재하는 참조변수 및 Method 영역의 static 데이터 등이다.

![image](https://user-images.githubusercontent.com/53200166/158566064-81140cc8-25b7-499b-a2ba-1f3592b8cc5e.png)

![image](https://user-images.githubusercontent.com/53200166/158566996-5c52656f-3bc6-40a0-835b-1de3d732c1e5.png)



### GC 발생 예제

```java
public class Main {
    public static void main(String[] args) {
        Person person = new Person("a", "곧 참조되지 않음");
        person = new Person("b", "참조가 유지됨");
        //GC 발생
    }
}
```

값 `a`를 갖는 객체는 참조가 유지된 상태가 아니게 되었을 때 GC의 대상이 된다.

```java
for (int i = 0; i < 10000; i++) {
    SampleObj obj = new SampleObj();
    obj.doTest(i);
    // 매 회전마다 GC 발생
}
```

매 회전마다 새롭게 생성되는 객체는 회전이 끝날 때 접근 불가능한 상태가 되므로 GC의 대상이 된다.

```java
Integer a = 300; // Integer a = new Integer(300); 과 동일하다.
a = 600; // a = new Integer(600); 과 동일하다.
```

300을 담은 객체는 600을 담은 객체가 생성되고 a가 그것을 가리켰을 때 접근 불가능한 상태가 되므로 GC의 대상이 된다.



### Stop-The-World

GC를 실행하기 위해 JVM이 프로그램의 실행을 멈추는 것

GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈추고, GC 실행을 완료한 이후에야 중단했던 작업을 다시 시작한다.

따라서 GC가 자주 발생하면, 즉 Stop-The-World 현상이 자주 발생하면 성능이 저하되므로, Stop-The-World 시간을 줄이는 것은 중요하다.



### Heap의 영역 구분

![image](https://user-images.githubusercontent.com/53200166/158535016-ff1b523a-fad9-46ef-bbb3-c60815b3d73c.png)

- Young 영역 :새롭게 생성된 객체가 할당되는 영역
  - Young 영역에서 Eden 또는 Survivor가 꽉 차서 일어나는 GC를 Minor GC라 한다.
- Old 영역 : Young 영역에서 오래 살아남은 객체가 복사되는 영역
  - Old 영역이 꽉 차서 일어나는 GC를 Major GC라 한다.
- ~~Permanent : Java 8 이후로는 해당되지 않는다.~~



### GC의 동작 과정

- 새로 생성된 객체는 Eden 영역에 위치한다.
- Eden 영역이 가득차면 Minor GC가 발생하여 참조가 없는 객체가 삭제된다. 이때, 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
  - 이 과정이 반복될 때마다(Eden 영역이 가득찰 때마다) 살아남은 객체가 Survivor 영역으로 계속 쌓인다.
- 하나의 Survivor 영역이 가득차면 Minor GC가 발생하여 참조가 없는 객체가 삭제된다. 이때, 살아남은 객체를 반대쪽 Survivor 영역으로 이동한다. 그리고 가득 찼던 원래 Survivor 영역은 아무 데이터도 없이 비어있는 상태가 된다.
  - 이 과정을 반복하다가 계속해서 살아남은 객체는 Old 영역으로 이동한다.
  - 각 객체는 Minor GC에서 살아남은 횟수를 기록하는 age bit를 가지고 있으며, Minor GC가 발생할 때마다 하나씩 증가한다. age bit 값이 `MaxTenuringThreshold`라는 설정값을 초과할 때, 해당 객체는 Old 영역으로 이동한다. `MaxTenuringThreshold`의 기본값은 JVM에 동적으로 정해지며, 사용자가 직접 설정할 수도 있다.
- Old 영역이 가득차면 Major GC가 발생하여 참조가 없는 객체가 삭제된다.

![image](https://user-images.githubusercontent.com/53200166/158541023-e4587e3d-b138-4f23-8ead-10ed9a7c74b3.png)



### GC의 종류

개발자는 옵션 설정을 통해 아래의 GC들 중 하나를 선택해 사용할 수 있다.



![image](https://user-images.githubusercontent.com/53200166/158569418-2547a3df-3c04-4645-a3e5-51ffd6c2bbc3.png)

- Serial GC
  - 하나의 쓰레드로 GC를 수행하며, STW 시간이 길다.
  - 싱글 프로세서 환경에 적합하다.



![image](https://user-images.githubusercontent.com/53200166/158569457-9c7578a5-3ec7-4367-abfb-54bb374ca52d.png)

- Parallel GC
  - 여러 개의 쓰레드로 GC를 수행하며, STW 시간이 더 짧다.
  - 멀티 프로세서 환경에 적합하다.



![image](https://user-images.githubusercontent.com/53200166/158569493-179ec583-8e29-4b39-80e8-0e0ac30c7665.png)

- CMS (Concurrent Mark Sweep) GC

  STW 시간을 줄이고자 만든 것으로, Mark & Sweep으로 접근 가능한 객체를 한번에 찾지 않고 나눠서 찾는 방식을 사용한다. CPU 자원을 많이 사용하며, Compaction을 하지 않기 때문에 메모리 단편화가 일어난다는 단점이 있다.

  - `Initial Mark`: GC Root가 참조하는 객체 중 가장 가까운 객체만 탐색하며, 살아있는 객체만 찾는다. (STW가 잠깐 발생)
  - `Concurrent Mark`: Initial Mark 과정에서 살아있다고 확인한 객체에서 참조하는 객체들을 따라가면서 확인한다. (STW 없이 이루어짐)
  - `Remark`: concurrent mark 과정에서 새로 추가되거나 참조가 끊긴 객체를 확인한다. (STW 발생)
  - `Concurrent Sweep`: 접근할 수 없다고 판단한 객체를 제거한다. (STW 없이 이루어짐)



![image](https://user-images.githubusercontent.com/53200166/158572455-13b7132d-3a16-4e7e-a857-3599b9a55141.png)

![image](https://user-images.githubusercontent.com/53200166/158573770-07d03b9a-6b2c-425c-9b4e-fae38d983654.png)

- G1 GC
  - CMS GC를 대체하기 위해 개발되었으며, 성능이 매우 좋다.
  - 기존처럼 Eden, Survivor, Old 영역을 물리적으로 딱딱 나누지 않는다. 그 대신 Region이라는 개념을 도입하여 Heap을 균등하게 여러 개의 지역으로 나누고, 각 지역을 역할과 함께 논리적으로 구분하여 객체를 할당한다.
  - Old Region의 살아있는 객체들을 Compaction하는 과정이 존재한다.



## OOP의 4가지 특징



### 추상화 (Abstraction)

객체의 공통적인 특징을 파악하여 하나로 묶는 것

- 추상 클래스 : 상속받을 자식 클래스들의 공통된 부분(필드, 메서드)을 뽑아 만든 클래스



### 캡슐화 (Encapsulation)

1) 관련이 있는 변수와 함수를 하나의 클래스로 묶고,
2) 그 중 일부를 외부에서 쉽게 접근하지 못하도록 은닉하는 것

- 유지보수 또는 확장 시 오류를 최소화할 수 있다.



#### 접근 제어자 (Access Modifier)

| 접근 제어자 | 같은 클래스 | 같은 패키지 | 자손 클래스 | 전체 |
| :---------: | :---------: | :---------: | :---------: | :--: |
|   public    |      O      |      O      |      O      |  O   |
|  protected  |      O      |      O      |      O      |      |
|   default   |      O      |      O      |             |      |
|   private   |      O      |             |             |      |

- `public` : 제한 없음
- `protected` : 같은 패키지의 클래스이거나 해당 클래스를 상속받은 다른 패키지의 클래스에서만 접근 가능
- `default` : 별도의 접근 제어자를 붙이지 않은 경우로, 같은 패키지 내에서만 접근 가능
- `private` : 같은 클래스 내에서만 접근 가능



### 상속 (Inheritance)

자식 클래스가 부모 클래스의 특성과 기능을 물려받는 것



### 다형성 (Polymorphism)

어떤 변수, 메서드, 객체가 상황에 따라 다른 결과를 내는 것

- 오버라이딩 vs 오버로딩
- 업캐스팅 vs 다운캐스팅
- 인터페이스



#### 오버라이딩 vs 오버로딩

- 오버라이딩 : 부모 클래스의 메서드를 자식 클래스의 용도에 맞게 **재정의**하는 것
- 오버로딩 : 하나의 클래스에서 메서드의 이름이 같지만, 파라미터가 다른 것



#### 업캐스팅 vs 다운캐스팅

- 업캐스팅

  - 서브 클래스의 객체가 슈퍼 클래스의 타입으로 형변환 되는 것
  - 즉, 슈퍼 클래스의 참조변수가 서브 클래스의 인스턴스를 가리키는 것

  ```java
  class Person {
      String name;
  }
  
  class Student extends Person {
      String check;
      
      public Student(String name) {
          super(name);
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          Person p = new Student("김성보");
          p.name = "전지현";
          p.check = "전지현"; // 컴파일 에러
      }
  }
  ```

  - 업캐스팅 하는 이유

    ```java
    ArrayList<Integer> arr = new ArrayList<>();
    ```

    라고 선언하게 되면, 나중에 데이터의 용도가 바뀌어 `LinkedList`로 변경해야 할 때 `ArrayList`로 선언된 모든 부분을 `LinkedList`로 바꿔야 한다. 또한, `ArrayList`에서는 지원하지만 `LinkedList`에서는 지원하지 않는 메서드를 사용했었다면 그 메서드를 더 이상 사용할 수 없게 되어 오류가 발생한다. 반면에

    ```java
    List<Integer> arr = new ArrayList<>(); // 업캐스팅
    ```

    라고 선언하게 되면, 같은 상황이 오더라도 바꿔야 할 부분이 적어진다.



- 다운캐스팅

  - 필요에 따라 업캐스팅된 것을 다시 원상태로 되돌리는 것

  ```java
  class Person {
      String name;
  }
  
  class Student extends Person {
      String check;
      
      public Student(String name) {
          super(name);
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          Person p = new Student("김성보");
          Student s = (Student)p; // 다운캐스팅
      }
  }
  ```

  - `instanceof`

    다운캐스팅은 반드시 업캐스팅이 선행되어야 하는데, 그러지 않는 실수를 범하면 런타임 에러가 발생한다. 왜냐하면 서브 클래스의 데이터 중 슈퍼 클래스에 포함되지 않는 부분이 정해지지 않았기 때문이다.

    ```java
    Person p = new Person("김성보"); // 업캐스팅 X
    Student s = (Student)p; // 런타임 에러
    ```

    위 예제에서는 슈퍼 클래스의 참조변수 p가 가리키는 인스턴스에 `String check`에 대한 메모리 할당이 없었고, 그 상태에서 형변환을 하려 하니 없는 부분에 대한 접근이 이루어져 오류가 발생하는 것이다.

    따라서 혹시 모를 해당 런타임 에러를 막을 때 사용한다.

    ```java
    if(p instanceof Student) {
    	Student s = (Student)p
    }
    ```

    

#### 추상 클래스 vs 인터페이스

- 공통점

  - 선언만 할 수 있고, 상속받은 자식만이 구현할 수 있다.
  - new를 통해 객체를 생성할 수 없고, 상속받은 자식만이 객체를 생성할 수 있다.

- 차이점

  |             | 추상 클래스                                                  | 인터페이스                                                   |
  | ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 관련 특징   | 추상화                                                       | 다형성                                                       |
  | 형태        | 일반 클래스에 추상 메서드를 1개 이상 가지는 형태             | 추상 메서드와 상수만으로 구성되고 생성자와 일반변수를 가질 수 없는 형태 |
  | 사용 의도   | 필수적으로 공통된 기능이 존재해야 함                         | 구현 객체들의 같은 동작을 보장해야 함                        |
  | 예시        | TV, 냉장고, 스마트폰 등은 '전자기기'라는 공통된 특성으로 묶을 수 있다. | 자전거, 자동차, 비행기 등은 ride(), speedUp(), speedDown() 이라는 동일한 동작을 가진다. |
  | 선언 키워드 | abstract                                                     | interface                                                    |
  | 구현 키워드 | extends                                                      | implements                                                   |
  | 다중 상속   | X                                                            | O                                                            |



## OOP의 5대 원칙

유지보수와 확장이 쉽도록 도와주는 5가지 원칙



### S : 단일 책임 원칙 (SRP, Single Responsibility Principle)

모든 클래스는 단 하나의 책임만을 가진다.

그 책임이란, 완전히 **캡슐화**되어야 함을 일컫는다.

즉, 각각의 클래스가 제공하는 모든 서비스는 하나의 책임을 수행하는 데 집중되어야 하며, 클래스가 변경되는 이유 또한 하나의 책임과 연관되어야 한다.



### O : 개방 폐쇄 원칙 (OCP, Open Closed Principle)

확장에 대해 열려있어야 하고, 수정에 대해 닫혀있어야 한다.

즉, 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계가 되어야 한다.

**추상화**와 **다형성**과 관련이 있다.



### L : 리스코프 치환 원칙 (LSP, Liskov Substitution Principle)

자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행할 수 있어야 한다.

즉, 부모 클래스가 들어갈 자리에 자식 클래스를 넣어도 잘 구동되어야 한다.

**상속**과 관련이 있다.



### I : 인터페이스 분리 원칙 (ISP, Interface Segregation Principle)

목적과 관심이 각각 다른 클라이언트들은 인터페이스를 통해 적절하게 분리시켜야 한다.

클라이언트의 목적과 용도에 적합한 인터페이스만을 제공(implements)해야 한다.

사용하지 않는 것에는 의존하지 말아야 한다. 즉, 사용하는 것에만 컴팩트하게 의존해야 한다.

```java
// ISP를 만족하지 않는다.
interface Printer {
    copyDocument();
    printDocument(document: Document);
    stapleDocument(document: Document, tray: Number);
}

// 사용되지 않는 메서드를 구현하고 있다.
class SimplePrinter implements Printer {
    public copyDocument() {
        
    } // 사용 하지 않음
    
    public printDocument(document: Document) {
        console.log("simple copy", document);
    }
    
    public stapleDocument(document: Document, tray: Number) {
        
    } // 사용 하지 않음
}
```

위와 같이 ISP에 위배되는 코드들에 대해서, 아래와 같이 인터페이스 분리를 통해 동작을 나눠야 한다.

```java
// ISP를 만족한다.
interface Copier {
    copyDocument();
}

interface Printer {
    printDocument(document: Document);
}

interface Stapler {
    stapleDocument(document: Document, tray: number);
}

class SimplePrinter implements Printer {
    public printDocument(document: Document) {
        console.log("simple copy", document);
    }
}
```



### D : 의존 관계 역전 원칙 (DIP, Dependency Inversion Principle)

상위 모듈은 하위 모듈에 종속되어서는 안 된다.

즉, **추상화**에 의존하며 설계해야 한다.

![image](https://user-images.githubusercontent.com/53200166/159937996-c72e64a7-a2db-4568-b5f3-e23d40ecc8ed.png)

사용자가 추상화된 `PasswordEncoder`에 의존하므로, 비밀번호 암호화 정책이 변경되더라도 다른 곳들로 변경이 전파되지 않으며 유연한 애플리케이션이 될 수 있다.

```java
// DIP를 만족하지 않는다.
class SimplePasswordEncoder {
    public void encoding() {
        // ...
    }
}

class UserService {
    SimplePasswordEncoder simplePasswordEncoder;
    
    public UserService(SimplePasswordEncoder simplePasswordEncoder) {
        this.simplePasswordEncoder = simplePasswordEncoder;
    }
    
    public void process() {
        simplePasswordEncoder.encoding();
    }
}
```

```java
// DIP를 만족한다.
interface PasswordEncoder {
    void encoding();
}

class SimplePasswordEncoder implements PasswordEncoder {
    public void encoding() {
        // ...
    }
}

class UserService {
    PasswordEncoder passwordEncoder;
    
    public UserService(PasswordEncoder passwordEncoder) {
        this.passwordEncoder = passwordEncoder;
    }
    
    public void process() {
        passwordEncoder.encoding();
    }
}
```



## Collections Framework

