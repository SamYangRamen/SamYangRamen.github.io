# Spring Boot 내부 구조 살펴보기



## 라이브러리 관리 및 빌드 자동화 도구



개발에 필요한 각 라이브러리들을 번거롭게 모두 일일히 다운받을 필요 없이, 필요한 라이브러리 종류와 버전, 종속성 정보 등을 명시하여, 자동으로 라이브러리들을 다운로드 해주고 이를 간편하게 관리해주는 도구가 있다.



### Maven vs Gradle

예전에는 Spring을 다루는 프로젝트를 진행할 때 **Maven**을 주로 이용했었다고 한다. Maven에서는 **pom.xml** 이라는 파일에 필요한 라이브러리를 미리 정의해놓으면, 알아서 네트워크를 통해 자동으로 라이브러리를 다운받는다. 그러나 아래와 같은 이유들 때문에 최근에는 **Gradle**을 주로 많이 사용한다고 한다.

- pom.xml의 작성 과정이 복잡하고 가독성이 좋지 않음
- Gradle은 캐시를 허용하여 동일한 입력에 대해서 gradle build를 재사용함. 따라서 빌드 시간을 훨씬 단축시켰기 때문에 Maven에 비해 속도가 매우 빠름
- 그 외 정리된 글 : http://egloos.zum.com/kwon37xi/v/4747016

Gradle에서는 빌드 환경설정을 **build.gradle**에서 작성한다. 이 파일은 Maven에서 pom.xml과 같은 역할을 한다고 보면 된다.



### Gradle의 기본 구조

#### Gradle Wrapper란?

```
각 개발자들이 자기 컴퓨터에 서로 다른 버전의 gradle을 설치하고 있다고 가정하자. 예를 들어, A 개발자는 gradle 5.6 버전을 사용하고, B 개발자는 gradle 3.0을 사용하고 있다. 그런데 A가 진행하던 프로젝트에 B가 합류하게 되었다. B는 빌드 중 알 수 없는 오류가 생겨서 한참을 해멘 뒤에, 해당 오류가 gradle 버전이 달라서 생겼다는 것을 알게 되었다. 만약에 B와 같은 개발자가 많아질수록 버전이 달라서 발생한 오류를 찾기 위해 수많은 시간이 낭비되었을 것이다.
```

- 각기 다른 개발 환경에서도 기본으로 설치된 gradle 버전과 상관없이 해당 프로젝트에 종속된 gradle 버전으로 사용할 수 있게 도와주는 역할을 한다.
- 다시 말해, 이미 존재하는 프로젝트를 새로운 환경에 설치할 때 별도의 환경설정 없이 곧바로 빌드할 수 있게 도와준다.
- 이때, Java나 Gradle을 설치할 필요가 없으며, 로컬에 설치된 Java나 Gradle의 버전도 신경쓸 필요가 없다.
- 따라서, 남이 하던 프로젝트도 빠르게 이어서 작업할 수 있다.

#### Gradle Wrapper 구성요소

- **gradlew** : 리눅스용 실행 파일. 쉘 스크립트로 구성

- **gradlew.bat** : 윈도우용 실행 파일. 배치 스크립트로 구성

- **gradle-wrapper.properties** : Gradle Wrapper 설정 정보 파일

  - gradle의 버전을 특정짓는 url이 포함되어 있음

    `distributionUrl=https\://services.gradle.org/distributions/gradle-7.1.1-bin.zip`

- **gradle-wrapper.jar** : `gradlew` 또는 `gradlew.bat` 실행파일로 프로젝트를 build할 때, 위 url에 작성된 버전의 gradle을 호출하여 build할 수 있도록 연결해주는 역할을 한다. 따라서 로컬 환경의 영향을 받지 않는다.

- **build.gradle** : 빌드 환경설정 파일

- **settings.gradle** : 프로젝트의 구성 정보 파일. 멀티 프로젝트를 구성하여 프로젝트를 모듈화할 경우, 하위 프로젝트의 구성 설정을 여기에서 하게 된다. ※ 당장은 쓸 일이 없으므로 다루지 않도록 한다.



### build.gradle 작성하기

#### build.gradle의 기본 구성요소

```javascript
plugins {
	id 'org.springframework.boot' version '2.5.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.seongbo.school'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.2.0'
	implementation 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4:1.16'
	implementation files("libs/tibero6-jdbc.jar")
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	compileOnly 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	annotationProcessor 'org.projectlombok:lombok'
}

test {
	useJUnitPlatform()
}
```

- **plugins** : 
  - `org.springframework.boot` : 스프링부트를 사용하기 위한 플러그인(필수)
  - `io.spring.dependency-management` : 라이브러리 의존성을 관리하는 플러그인(필수)
  - `java` : 자바를 사용하기 위한 플러그인(필수)
- **group** : 자신의 프로젝트를 식별해주는 고유 ID로, 인터넷 주소를 뒤집어 놓은 형태
- **version** : 해당 애플리케이션의 버전. 'SNAPSHOT'이 붙으면 아직 개발 단계라는 것을 의미한다.
- **sourceCompatibility** : 자바 컴파일러 준수 레벨을 디폴트값(현재 사용 중인 JVM의 버전)에서 11로 변경
- **repositories** : gradle이 필요한 라이브러리를 자동으로 다운로드하기 위해 사용하는 저장소
  - 대표적으로 jcenter()와 mavenCentral()이 있다. 이 둘은 gradle의 메서드이다. 최근에는 jcenter()를 주로 사용하는 추세라고 한다.
  - ※ gradle에서 왜 mavenCentral()? maven이랑 다르다며?
    - 빌드 도구는 **Ant** -> **Maven** -> **Gradle** 순으로 생겨났는데, Gradle은 Ant와 Maven같은 기존 빌드 도구의 기능과 시스템을 활용할 수 있다. 이러한 측면에서 MavenCentral()을 사용하는 것이다. 한편, Maven 등을 사용하던 프로젝트는 기존 자원을 최대한 활용하면서 단계적으로 Gradle로 이전할 수도 있다.
- **dependencies** : 의존성 라이브러리를 정의. 의존성 라이브러리란, 개발 도중 필요할 때 가져다 쓸 라이브러리라고 일단 이해하고 넘어가자.
  - implementation : 개발할 때에 내가 필요한 라이브러리를 적어 이 라이브러리를 가져와달라고 하는 부분
  - testImplementation : 테스트 코드를 작성할 때 필요한 라이브러리를 적어 이 라이브러리를 가져와달라고 하는 부분
  - compileOnly : 컴파일할 때에만 사용되고 런타임 때에는 사용되지 않는 라이브러리
- **test** :
  - `useJUnitPlatform()` : JUnit Test를 가능하게 해주는 메서드



### 의존성 라이브러리 종류

#### Lombok

Annotation 기반으로 Java에서 자주 사용되는 코드를 자동완성 해주는 라이브러리

- **@Getter/@Setter** : 필드값에 대한 `get`, `set` 메서드가 자동으로 생성됨

  - `get`, `set` 메서드를 직접 명시한 클래스 (Lombok X)

    ```java
    public class Book {
        private String name;
        private int price;
        private String releaseDate;
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public int getPrice() {
            return price;
        }
    
        public void setPrice(int price) {
            this.price = price;
        }
    
        public String getReleaseDate() {
            return releaseDate;
        }
    
        public void setReleaseDate(String releaseDate) {
            this.releaseDate = releaseDate;
        }
    }
    ```

  - Lombok의 `@Getter`, `@Setter`를 사용한 클래스

    ```java
    @Getter
    @Setter
    public class Book {
        private String name;
        private int price;
        private String releaseDate;
    }
    ```

- **@NoArgsConstructor/@AllArgsConstuctor** : Argument가 없는 기본 생성자/Argument에 필드를 모두 포함한 생성자가 자동으로 생성됨

  - 기본 생성자와, 모든 필드값을 받는 생성자를 직접 명시한 클래스 (Lombok X)

    ```java
    public class Book {
        private String name;
        private int price;
        private String releaseDate;
    
        public Book() {
        }
    
        public Book(String name, int price, String releaseDate) {
            this.name = name;
            this.price = price;
            this.releaseDate = releaseDate;
        }
    }
    ```

  - 롬복의 `@NoArgsConstructor`, `@AllArgsConstructor`를 사용한 클래스

    ```java
    @NoArgsConstructor
    @AllArgsConstructor
    public class Book {
        private String name;
        private int price;
        private String releaseDate;
    }
    ```

- **@Data** : 여러 Annotation의 조합. 즉, `@data` 하나를 명시하는 것은 Lombok에 소속되어 있는 `@Getter`, `@Setter`, `@NoArgsConstructor`, `@AllArgsConstructor`, ... 를 한번에 명시하는 것과 같은 효과를 가진다.

- 그 외에도 여러가지 Annotation 및 기능이 존재함



#### JDBC

자바 프로그램을 데이터베이스에 연결하기 위한 라이브러리



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