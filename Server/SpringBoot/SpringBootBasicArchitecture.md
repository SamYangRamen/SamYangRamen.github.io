# Spring Boot 기본 구조



## Spring Boot 예제 : 학생 성적 관리 서버

- 클라이언트로부터 `학번`, `과목명`을 입력받으면 `시험종류`, `점수`를 DB에서 꺼내 반환하는 아주 기본적인 구조에 대해서 작성하였음



### Directory

```
src/
└main/
   └java/
      └com.seongbo.school/
         └DTO/
            └InputDTO.java
            └OutputDTO.java
         └DAO/
            └mapper/
               └ScoreMapper.java
            └object/
               └InputDAO.java
               └OutputDAO.java
         └controller/
            └ScoreController.java
         └service/
            └ScoreService.java
            └ScoreServiceImpl.java
         └repository/
            └ScoreRepository.java
            └ScoreRepositoryImpl.java
   └resources/
      └mybatis/
         └mapper/
            └ScoreMapper.xml
         └mybatis-config.xml
      └application.properties
```



### DataBase

- `src/main/resources/application.properties` 에서 DB Logging

#### EXAM_TABLE

| stdId | subject | examType | score |
| ----- | ------- | -------- | ----- |
| 1234  | 수학    | 중간     | 10    |
| 1234  | 수학    | 기말     | 20    |
| 1234  | 영어    | 중간     | 30    |
| 1234  | 영어    | 기말     | 40    |
| 5678  | 수학    | 중간     | 50    |
| 5678  | 수학    | 기말     | 60    |
| 5678  | 영어    | 중간     | 70    |
| 5678  | 영어    | 기말     | 80    |



### DTO

#### InputDTO.java

```java
package com.seongbo.school.DTO;

import lombok.Getter;
import lombok.AllArgsConstructor;
import com.fasterxml.jackson.annotation.JsonInclude;

public class InputDTO {
  @Getter
  @AllArgsConstructor
  @JsonInclude(JsonInclude.Include.NON_NULL)
  public static class StdInfoInputDTO {
    private Integer stdId;
    private String subject;
    }
}
```

#### OutputDTO.java

```java
package com.seongbo.school.DTO;

import lombok.Setter;
import lombok.AllArgsConstructor;
import com.fasterxml.jackson.annotation.JsonUnwrapped;

public class OutputDTO {
  @Setter
  @AllArgsConstructor
  public static class ScoreOutputDTO {
    @JsonUnwrapped
    private String examType;
    private Integer score;
    }
}
```



### DAO

#### InputDAO.java

```java
package com.seongbo.school.DAO.object;

import lombok.Getter;
import lombok.AllArgsConstructor;

public class InputDAO {
    @Getter
    @AllArgsConstructor
    public static class StdInfoInputDAO {
        private Integer stdId;
        private String subject;
    }
}
```

#### OutputDAO.java

```java
package com.seongbo.school.DAO.object;

import lombok.Setter;
import lombok.AllArgsConstructor;

public class OutputDAO {
    @Setter
	@AllArgsConstructor
    public static class ScoreOutputDAO {
        private String examType;
        private Integer score;
    }
}
```



### Controller

#### ScoreController.java

```java
package com.seongbo.school.controller;

import com.seongbo.school.DTO.InputDTO.StdInfoInputDTO;
import com.seongbo.school.DTO.OutputDTO.ScoreOutputDTO;

import com.seongbo.school.service.ScoreService;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RequestParam;

import java.util.List;

@Slf4j
@Controller
@RestController
public class ScoreController {
  @Autowired
  ScoreService scoreService;

  @GetMapping("/score/ScoreBehavior/getScore")
  @ResponseBody
  public List<ScoreOutputDTO> getScore(@RequestParam("stdId") int stdId,
                                @RequestParam("subject") String subject) {
    StdInfoInputDTO dto = new StdInfoInputDTO(stdId, subject);
    return scoreService.getScoreService(dto);
  }
}
```



### Service

#### ScoreService.java

```java
package com.seongbo.school.service;

import com.seongbo.school.DTO.InputDTO.StdInfoInputDTO;
import com.seongbo.school.DTO.OutputDTO.ScoreOutputDTO;

import java.util.List;

public interface ScoreService {
  public List<ScoreOutputDTO> getScoreService(StdInfoInputDTO dto);
}
```

#### ScoreServiceImpl.java

```java
package com.seongbo.school.service;

import com.seongbo.school.DTO.InputDTO.StdInfoInputDTO;
import com.seongbo.school.DTO.OutputDTO.ScoreOutputDTO;

import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.List;

@Service
public class ScoreServiceImpl implements ScoreService {

  @Autowired
  ScoreRepository scoreRepository;

  @Override
  public List<ScoreOutputDTO> getScoreService(StdInfoInputDTO dto) {
    return scoreRepository.getScoreRepo(dto)
  }
}
```



### Repository

#### ScoreRepository.java

```java
package com.seongbo.school.repository;

import com.seongbo.school.DTO.InputDTO.StdInfoInputDTO;
import com.seongbo.school.DTO.OutputDTO.ScoreOutputDTO;

import java.util.List;

public interface ScoreRepository {
  public List<ScoreOutputDTO> getScoreRepo(StdInfoInputDTO dto);
}
```

#### ScoreRepositoryImpl.java

```java
package com.seongbo.school.repository;

import com.seongbo.school.DAO.mapper.ScoreMapper;

import com.seongbo.school.DTO.InputDTO.StdInfoInputDTO;
import com.seongbo.school.DTO.OutputDTO.ScoreOutputDTO;
import com.seongbo.school.DAO.object.InputDAO.StdInfoInputDAO;
import com.seongbo.school.DAO.object.OutputDAO.ScoreOutputDAO;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class ScoreRepositoryImpl implements ScoreRepository {

  @Autowired
  private ScoreMapper scoreMapper

  @Override
  public List<ScoreOutputDTO> getScoreRepo(StdInfoInputDTO dto) {
      StdInfoInputDAO inputDAO = new StdInfoInputDAO(dto.stdId, dto.subject);
      List<ScoreOutputDAO> outputDAO = scoreMapper.getScore(inputDAO);
      
      List<ScoreOutputDTO> outputDTO = new ArrayList<>();
      for (ScoreOutputDAO item : outputDAO) {
          outputDTO.add(new ScoreOutputDTO(item.examType, item.score));
      }
      
      return outputDTO;
  }
}
```



### Mapper

#### ScoreMapper.java

```java
package com.seongbo.school.DAO.mapper;

import com.seongbo.school.DAO.object.InputDAO.StdInfoInputDAO;
import com.seongbo.school.DAO.object.OutputDAO.ScoreOutputDAO;
import org.springframework.stereotype.Repository;
import org.apache.ibatis.annotations.Mapper;

import java.util.List;

@Repository
@Mapper
public interface ScoreMapper {
  List<ScoreOutputDAO> getScore(StdInfoInputDAO dao);
}
```

#### ScoreMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.seongbo.school.DAO.mapper.ScoreMapper">
  <select id="getScore" parameterType="com.seongbo.school.DAO.object.InputDAO$StdInfoInputDAO" resultType="com.seongbo.school.DAO.object.OutputDAO$ScoreOutputDAO">
      SELECT examType, score FROM EXAM_TABLE WHERE stdId=${stdId} AND subject=${subject}
  </select>
</mapper>
```

#### mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <mappers>
        <mapper resource="ScoreMapper.xml"/>
    </mappers>
</configuration>
```

