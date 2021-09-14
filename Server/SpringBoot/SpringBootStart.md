# Spring Boot 일단 개발해보기



## 상황 설계



개발하려는 프로젝트에 우리가 추가할 기능은, 클라이언트로부터 어떤 학생의 학번, 이름 등의 학생정보 및 국어, 수학, 영어 점수를 입력받으면 총합을 계산하여 클라이언트에 반환하고, DB에는 평균 점수를 저장하는 것이다.



| stdId | name | avg  |
| ----- | ---- | ---- |

DB 테이블의 이름은 `AVG_TABLE`이며, 테이블의 구조가 위와 같다고 가정하자.



```
src/
   └main/
      └java/
         └Main.java
         └com.seongbo.school/
            └DTO/
               └BasicDTO.java
            └controller/
               └TotalController.java
            └service/
               └TotalService.java
               └TotalServiceImpl.java
            └repository/
               └TotalRepository.java
               └TotalRepositoryImpl.java
            └mapper/
               └TotalMapper.java
      └resources/
         └mybatis/
            └mybatis-config.xml
            └mapper/
               └TotalMapper.xml
```

개발하려는 프로젝트의 디렉터리 구조를 위와 같이 만들었다고 가정하자.



```react
export interface studentDTO {
  stdID: number;
  name: string;
  kor: number;
  math: number;
  eng: number;
}
```

```react
export interface dto<T> {
    dto: T;
}

...

public getTotal(obj: studentDTO): Promise<number> {
  return API.post<dto<studentDTO>>('/calculate/total', {
    dto: obj,
  }).then(response => {
    return response.data;
  });
}
```

위 예시는 react의 소스코드이다. 클라이언트에서 어떤 조작에 의해 `getTotal` 메서드가 호출이 되면, `/calculate/total`이라는 url을 향해 POST 요청을 보내는 소스코드임을 알 수 있다.

이때, 이 `getTotal()` 메서드가 호출이 되었다고 가정하자.



## Controller



**Controller**는 클라이언트의 요청을 받는 부분이다. 다만 요청에 대한 처리는 Service에게 모두 전담시킨다.

즉, Controller는 클라이언트로부터 요청을 받으면 가장 먼저 호출되는 부분으로서, 수신받은 데이터를 Service에게 넘기고 이런저런 처리 과정을 거친 결과값을 Service로부터 반환받아 클라이언트에 송신하는 기능을 수행한다.

다시 말해, Controller의 주 기능은 **연결통로**이다.



```java
/* BasicDTO.java */

@Getter
@AllArgsConstructor
public static class DTO<T> {
    T dto;
}

@Getter
@AllArgsConstructor
public static class StudentDTO {
    private Integer stdID;
    private String name;
    private Integer kor;
    private Integer math;
    private Integer eng;
}
```

```java
/* totalController.java */

@RestController
public class TotalController {

    private TotalService totalService;
    
    @Autowired
    public TotalController(TotalService totalService) {
        this.totalService = totalService;
    }
    
    @PostMapping("/calculate/total")
    @ResponseBody
    public Integer getTotal(@RequestBody DTO<StudentDTO> dto) {
        return totalService.getTotalService(dto.getDto());
    }
}
```



react의 `getTotal()` 메서드를 확인해봤을 때, 이 메서드의 요청을 받을 메서드는 아래와 같은 특징을 지녀야 하므로,

- POST로 연결되어야 하므로 `@PostMapping` Annotation을 붙여야 한다.
- 요청 url이 `/calculate/total`이므로, 이 url을 명시해주어야 한다.
- '응답'해주어야 하므로 `@ResponseBody` Annotation을 붙여야 한다.

```java
    @PostMapping("/calculate/total")
    @ResponseBody
    public Integer getTotal(@RequestBody DTO<StudentDTO> dto)
```

코드를 이렇게 작성하였다.

그런데 Controller는 연결통로의 역할만을 수행해야 하고, 나머지 모든 비지니스 로직은 Service에서 담당해야 할 것이므로,

```java
    private TotalService totalService;
    
    @Autowired
    public TotalController(TotalService totalService) {
        this.totalService = totalService;
    }
```

이렇게 Service를 담당하는 객체를 생성하였고,

```java
    public Integer getTotal(@RequestBody DTO<StudentDTO> dto) {
        return totalService.getTotalService(dto.getDto());
    }
```

메서드에서는 아무런 세부 작업도 하지 않고 단지 Service에서의 반환 결과를 그대로 받아 클라이언트로 반환하도록 작성하였다.



## Service



**Service**는 **사용자의 요구사항을 처리**하는 부분이다. 즉, 사용자의 요청에 의해 필요로 하는 모든 비지니스 로직을 여기에서 담당한다.

현재 다루고 있는 예시에서는 '국어, 수학, 영어 점수의 총합을 계산하여 반환'하는 것이 사용자의 요구사항이다. 따라서 이 요구사항을 수행하는 코드를 Service에서 작성해야 한다.



```java
/* TotalService.java */

public interface TotalService {
    Integer getTotalService(StudentDTO dto);
}
```

```java
/* TotalServiceImpl.java */

@Service
public class TotalServiceImpl implements TotalService {

    private TotalRepository totalRepository;
    
    @Autowired
    public TotalServiceImpl(TotalRepository totalRepository) {
        this.totalRepository = totalRepository;
    }
    
    @Override
    public Integer getTotalService(StudentDTO dto) {
        Integer total = dto.getKor() + dto.getMath() + dto.getEng();	// 비지니스 로직
        totalRepository.setTotalRepository(dto);	// DB 저장
        return total;	// 반환
    }
}
```



위 코드에서 `TotalService` 인터페이스의 구현체인 `TotalServiceImpl`에 `@Service` Annotation이 붙어 있으므로

```java
/* TotalController.java */
    
    ...
    private TotalService totalService;
    
    @Autowired
    public TotalController(TotalService totalService) {	// 자동 생성
        this.totalService = totalService;	// 전달
    }
    ...
```

`TotalController`의 생성자에 붙어 있던 `@Autowired`와 서로 연결된다.

따라서 파라미터인 `totalService`에는 `TotalServiceImpl` 객체가 자동으로 생성되어 들어가진다.

```java
/* TotalController.java */

    ...
    public Integer getTotal(@RequestBody DTO<StudentDTO> dto) {
        return totalService.getTotalService(dto.getDto());	// 이거
    }
    ...
```

그러므로 `TotalController`의 메서드인 `getTotal()`에서 호출되는 `getTotalService()`는 사실, `TotalServiceImpl` 에서 작성된 `getTotalService()`인 것이다.

Service에서는 사용자의 요청에 의해 필요로 하는 모든 비지니스 로직를 담당한다고 앞서 이야기하였다. 현재 예시에서는 `TotalServiceImpl`의 `getTotalService()` 메서드가 이에 해당한다.



(그런데 왜 이렇게 인터페이스와 구현체를 따로 두느냐 하면, 일단은 개발 방법론적으로 유지보수를 용이하게 하기 위해서라고 이해하고 넘어가도록 하자.)



한편, 우리가 개발하고자 하는 기능은 평균 점수를 DB에 저장하는 것까지 포함되어야 한다. 따라서 평균 점수를 DB에 저장하는 과정을 수행하기 위해 `totalRepository`의 `setTotal()` 메서드를 호출하였다.



## Repository



**Repository**는 **DB에 관련된 모든 작업을 처리**하는 부분이다. 즉, DB query (SQL) 수행을 위한 연산 작업 및 자료구조 연결 작업을 모두 포함한다. 단, DB query를 수행하기 직전까지의 부분을 담당하며, **DB query 자체는 Mapper에서 담당**한다.



```java
/* BasicDTO.java */

@Getter
@AllArgsConstructor
public static class StudentDTO {
    private Integer stdID;
    private String name;
    private Integer kor;
    private Integer math;
    private Integer eng;
}

@Getter
@AllArgsConstructor
public static class AvgDTO {
    private Integer stdID;
    private String name;
    private Double avg;
}
```

```java
/* TotalRepository.java */

public interface TotalRepository {
    void setTotalRepository(StudentDTO dto);
}
```

```java
/* TotalRepositoryImpl.java */

@Repository
public class TotalRepositoryImpl implements TotalRepository {

    private TotalMapper totalMapper;
    
    @Autowired
    public TotalRepositoryImpl(TotalMapper totalMapper) {
        this.totalMapper = totalMapper;
    }
    
    @Override
    public void setTotalRepository(StudentDTO dto) {
        // DB query 수행을 위한 연산 작업
        Double avg = (dto.getKor() + dto.getMath() + dto.getEng()) / 3.0;
        
        // DB query 수행을 위한 자료구조 연결 작업
        final AvgDTO input = new AvgDTO(
        	dto.getStdID(),
            dto.getName(),
            avg
        );
        
        // DB query 수행
        totalMapper.setTotalMapper(input);
    }
}
```



## Mapper



**Mapper**는 **DB Query** 자체라고 보면 된다. **Mybatis**라는 라이브러리를 등록함으로써 사용할 수 있는 기능이다.

xml 파일에는 각 메서드 별로 수행하고자 하는 Query를 작성해두고, `@Mapper` Annotation을 클래스에 붙이면 해당 클래스가 xml 파일이 서로 연결되며, 해당 메서드를 호출하면 연결된 Query를 수행하게 된다.



```java
/* BasicDTO.java */

@Getter
@AllArgsConstructor
public static class AvgDTO {
    private Integer stdID;
    private String name;
    private Double avg;
}
```

```java
/* TotalMapper.java */

@Mapper
public interface TotalMapper {
    void setTotalMapper(AvgDTO input);
}
```

```xml
<!-- TotalMapper.xml -->

<mapper namespace="com.seongbo.school.mapper.TotalMapper">
    <insert id="setTotalMapper" parameterType="com.seongbo.school.DTO.BasicDTO$AvgDTO">
        INSERT INTO AVG_TABLE
        VALUES (${stdID}, ${name}, ${avg});
    </insert>
</mapper>
```



예제에서는

- xml에 `namespace`를 입력함으로써 `TotalMapper` 클래스와 연결시켰다.
- `insert` 태그를 만들고 id를 `setTotalMapper`로 정함으로써, `setTotalMapper()` 메서드가 호출되면 INSERT문이 DB 내에서 수행되도록 만들었다.
- `parameterType`을 `AvgDTO`로 정함으로써, 해당 파라미터값을 `AvgDTO`의 각 변수명을 따서 `${stdID}`, `${name}`, `${avg}` 라고 작성하여 값을 받아올 수 있도록 하였다.



## 결론



```java
/* Main.java */

@SpringBootApplication
public class Main {

  public static void main(String[] args) {
    SpringApplication.run(Main.class, args);
  }
}
```



Spring으로 개발한 프로그램의 구동 시작부는 위와 같이 생겼다. 개발자는 이 `Main` 클래스부터 소스코드의 구조가 어떻게 되어 있고, data-flow가 어떻게 일어나는지를 생각할 필요 없이 **Controller**, **Service**, **Repository**, **Mapper**만 개발하면 된다. 나머지 것들은 Spring 내에서 자체적으로 모두 개발되었고, 자동화되었기 때문이다.