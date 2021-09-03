# Tibero SQL 각종 문법

※ MariaDB 문법과 겹치는 부분들은 제외하였음



##  일반 Query



### 변수 선언

- Tibero에서는 일반 Query에서 변수 선언을 그냥 할 수 없다.

- 일반 Query에서 변수를 사용하고자 할 때, `DECLARE-BEGIN-EXCEPTION-END` 키워드를 사용하여 아래와 같은 문장을 작성해서 사용하는 방법 밖에 없다. 아래 문장들을 한 묶음으로 '블록' 이라고 말한다.

  ```sql
  -- '블록'의 기본 구조
  DECLARE
    /* 변수 선언, 필수 */
  BEGIN
    /* 기능 정의, 필수 */
  EXCEPTION
    /* 예외 처리, 선택 */
    WHEN NO_DATA_FOUND THEN
      /* sentences */
    WHEN ...
      /* sentences */
    WHEN OTHERS THEN
      /* sentences */
  END;
    /* 종료, 필수 */
  ```



### ROWNUM

- 각 N번째 튜플을 확인하는데 사용한다.
- 의사 컬럼으로 참조만 될 뿐 데이터베이스에 저장되지 않으므로, DESC 명령으로 보이지 않는다.
- 또한, `ROWNUM`을 변경하기 위해 DML(`SELECT`, `INSERT`, `UPDATE`, `DELETE`)을 사용할 수 없다.
- `ROWNUM`은 `ORDER BY` 전에 부여되며, `ORDER BY`는 맨 나중에 실행된다.
- 주로 <, <= 를 사용하며, >, >= 인 경우 `ROWNUM`은 동작하지 않는다.
- (단일 Query에서) 등호를 사용하는 경우,`ROWNUM = 1` 은 사용 가능하지만 `ROWNUM = 2`인 경우는 데이터가 추출되지 않는다. 
  - https://m.blog.naver.com/roropoly1/221219162711

```sql
SELECT ROWNUM, T.* FROM BASIC_DATA T WHERE ROWNUM <= 4;
```

| ROWNUM | 거래처명      | 품명            | 상품분류 | 수량 | 단가  |
| ------ | ------------- | --------------- | -------- | ---- | ----- |
| 1      | 가양 아트박스 | 합지 스프링노트 | 노트     | 20   | 2500  |
| 2      | 가양 아트박스 | A4용지          | 복사용지 | 30   | 20000 |
| 3      | 가양 아트박스 | 모나미 볼펜     | 필기구   | 250  | 100   |
| 4      | 가양 아트박스 | 네임펜 흑색     | 기타     | 10   | 6000  |

```sql
SELECT ROWNUM, T.* FROM BASIC_DATA T WHERE ROWNUM <= 4 ORDER BY 단가;
```

| ROWNUM | 거래처명      | 품명            | 상품분류 | 수량 | 단가  |
| ------ | ------------- | --------------- | -------- | ---- | ----- |
| 3      | 가양 아트박스 | 모나미 볼펜     | 필기구   | 250  | 100   |
| 1      | 가양 아트박스 | 합지 스프링노트 | 노트     | 20   | 2500  |
| 4      | 가양 아트박스 | 네임펜 흑색     | 기타     | 10   | 6000  |
| 2      | 가양 아트박스 | A4용지          | 복사용지 | 30   | 20000 |

#### ROWNUM으로 특정 행을 뽑아내는 방법

- 아래와 같이 Nested Query를 이용하면 `ROWNUM`으로 특정 행을 뽑아낼 수 있다.

```sql
SELECT R.*
FROM
(
  SELECT ROWNUM AS NUM, T.*
  FROM SC_PIVOT_TESTDATA T
) R
WHERE R.NUM=3;
```

| ROWNUM | 거래처명      | 품명        | 상품분류 | 수량 | 단가 |
| ------ | ------------- | ----------- | -------- | ---- | ---- |
| 3      | 가양 아트박스 | 모나미 볼펜 | 필기구   | 250  | 100  |



### SELECT

- 단지 값 하나를 SELECT하고자 할 때

  - Tibero는 FROM절이 없을 경우 SELECT문이 실행되지 않기 때문에, `DUAL`이라는 Dummy Table을 이용해야 한다.

  ```sql
  SELECT 12345 AS RESULT FROM DUAL;
  ```

  | RESULT |
  | ------ |
  | 12345  |

- COLUMN명을 SELECT하고자 할 때

  ```sql
  SELECT COLUMN_NAME
  FROM ALL_TAB_COLUMNS
  WHERE TABLE_NAME = /* 테이블명 */;
  ```

  ```sql
  -- 예제
  SELECT COLUMN_NAME
  FROM ALL_TAB_COLUMNS
  WHERE TABLE_NAME = BASIC_DATA;
  ```

  | COLUMN_NAME |
  | ----------- |
  | 거래처명    |
  | 품명        |
  | 상품분류    |
  | 수량        |
  | 단가        |

- 특정 순서의 행에 있는 COLUMN명을 SELECT하고자 할 때

  ```sql
  SELECT R.COLUMN_NAME
  FROM
  (
  	SELECT ROWNUM AS NUM, COLUMN_NAME
  	FROM ALL_TAB_COLUMNS
  	WHERE TABLE_NAME = 'SC_PIVOT_TESTDATA'
  ) R
  WHERE R.NUM=2
  ;
  ```

  | COLUMN_NAME |
  | ----------- |
  | 품명        |

- `NVL([컬럼명], [NULL일 경우 반환값])` : SELECT 결과가 NULL인 부분은 특정 값으로 치환해서 반환

  ```
  SELECT NVL(D, 1234)
  FROM
  (
    SELECT 'ABC' AS D FROM DUAL
    UNION ALL
    SELECT NULL FROM DUAL
    UNION ALL
    SELECT 'GHI' FROM DUAL
  )
  ;
  ```

  | NVL(D, 1234) |
  | ------------ |
  | ABC          |
  | 1234         |
  | GHI          |

- `NVL2([컬럼명], [NULL이 아닐 경우 반환값], [NULL일 경우 반환값])` : SELECT 결과가 NULL이 아닌 경우도 특정 값으로 치환해서 반환



### 주석

```sql
-- 한 줄 주석
/*
	여러 줄 주석
*/
```



### 문자열

#### 문자열 합성

- `||`를 사용하여 이어 붙인다.

  - 만약 숫자가 포함되어 있다면, 자동으로 문자열화 되어서 이어 붙여진다.

  ```sql
  DECLARE
    myString VARCHAR(50);
  BEGIN
    myString := 'ABC' || 'DE1' || 234;
    DBMS_OUTPUT.PUT_LINE('myString: ' || myString);
  END;
  ```

  ```sql
  ABCDE1234 -- 실행 결과
  ```

  - 단, 연산자를 포함하면 오류가 발생한다. 따라서 `TO_CHAR()` 메서드를 사용해야 한다.

    ```sql
    DECLARE
      myString VARCHAR(50);
      myNum INTEGER := 0;
    BEGIN
      myString := 'ABC' || myNum+12 || 34 || 'DE';	-- 불가능한 문장
      myString := 'ABC' || TO_CHAR(myNum+12) || 34 || 'DE';	-- 가능한 문장
    END;
    ```

- `CONCAT()` 메서드로 이어 붙인다.

  - MariaDB와는 다르게, 아래와 같이 3개 이상의 값을 Argument로 입력할 수 없다.

    ```sql
    myString := CONCAT('ABC', 'DEF', 123, 'GHI');	-- 불가능한 문장
    ```

  - 단 2개의 Argument만 입력할 수 있다. 만약 3개 이상을 Argument로 입력하고 싶다면, `CONCAT()` 메서드를 중첩시켜야 한다.

    ```sql
    myString := CONCAT('ABC', 123);	-- 가능한 문장
    myString := CONCAT('ABC', CONCAT('DEF', CONCAT(123, 'GHI')));	-- 가능한 문장
    ```

#### Column 추출 문자열 생성

- `WM_CONCAT([컬럼명])` : 해당 Column에 속한 Row들을 `,`으로 이어붙인 문자열을 반환

  ```sql
  SELECT WM_CONCAT(DISTINCT 거래처명) AS 거래처명
  FROM SC_PIVOT_TESTDATA;
  ```

  | 거래처명                                        |
  | ----------------------------------------------- |
  | 가양 아트박스, 나나문구 서현점, 나나문구 대치점 |

- `LISTAGG([컬럼명], [구분자]) WITHIN GROUP (ORDER BY [컬럼명]) `
  : 해당 Column에 속한 Row들을 정렬한 뒤, 구분자로 이어붙인 문자열을 반환

  ```sql
  SELECT LISTAGG(거래처명, ', ') WITHIN GROUP (ORDER BY 거래처명 DESC) AS 거래처명
  FROM
  (
    SELECT DISTINCT 거래처명
    FROM SC_PIVOT_TESTDATA
  )
  ;
  ```

  | 거래처명                                        |
  | ----------------------------------------------- |
  | 나나문구 서현점, 나나문구 대치점, 가양 아트박스 |

#### 자료형 변환

- `TO_CHAR()` : 문자열로 변경

  ```sql
  SELECT TO_CHAR(12345) FROM DUAL;
  ```

- `TO_NUMBER()` : 숫자로 변경

  ```sql
  SELECT TO_NUMBER('12345') FROM DUAL;
  ```

- `TO_DATE()` : 날짜로 변경

  ```sql
  SELECT TO_DATE('2019-01-25', 'YYYY-MM-DD') FROM DUAL;
  ```

#### 기타 문자열 함수들

- `LENGTH()` : 문자열의 길이 반환. 한글의 경우도 한 글자를 1로 쳐서 계산함

  ```sql
  SELECT LENGTH('ABCDE'), LENGTH('가나다라'), LENGTH('123') FROM DUAL;
  ```

  | LENGTH('ABCDE') | LENGTH('가나다') | LENGTH('123') |
  | --------------- | ---------------- | ------------- |
  | 5               | 4                | 3             |

- `SUBSTR([문자열], [시작위치], [길이])` : 시작위치부터 길이만큼 문자를 반환

  ```sql
  SELECT
    SUBSTR('ABCDEFG', 5),	-- 맨 왼쪽부터 시작 위치를 정한 다음 끝까지
    SUBSTR('ABCDEFG', 3, 4),	-- 맨 왼쪽부터 시작 위치를 정한 다음 정해진 길이만큼
    SUBSTR('ABCDEFG', -6, 3)	-- 맨 오른쪽부터 시작 위치를 정한 다음 끝까지
  FROM DUAL;
  ```

  | SUBSTR('ABCDEFG', 5) | SUBSTR('ABCDEFG', 3, 4) | SUBSTR('ABCDEFG', -6, 3) |
  | -------------------- | ----------------------- | ------------------------ |
  | EFG                  | CDEF                    | BCD                      |

- `INSTR([문자열], [구분자], [시작위치], [횟수])` : 문자열에서 시작위치부터 구분자가 횟수 번째에 나오는 인덱스를 반환

  ```sql
  SELECT
    INSTR('AB-CD-EF-GH-IJ-KL-MN', '-') AS R1,	-- A부터 세서 제일 먼저 나오는 것
    INSTR('AB-CD-EF-GH-IJ-KL-MN', '-', 7) AS R2,	-- E부터 세서 제일 먼저 나오는 것
    INSTR('AB-CD-EF-GH-IJ-KL-MN', '-', 4, 3) AS R3,	-- C부터 세서 3번째
    INSTR('AB-CD-EF-GH-IJ-KL-MN', '-', -5, 1) AS R4	-- K부터 세서 1번째
  FROM DUAL;
  ```

  | R1   | R2   | R3   | R4   |
  | ---- | ---- | ---- | ---- |
  | 3    | 9    | 12   | 15   |





##  주의사항

- 어떤 Object를 표현할 때 ````` ```로 감싸서 작성할 수 없다.

  - 가능한 표현

    ```sql
    CREATE TABLE BASIC_DATA (
    	거래처명 VARCHAR(50),
    	품명 VARCHAR(50),
    	상품분류 VARCHAR(50),
    	수량 INT(8),
    	단가 INT(8)
    );
    
    INSERT INTO BASIC_DATA
    VALUES ('가게1', '물품1', '상품1', 1, 1000)
    ;
    ```

  - 불가능한 표현

    ```sql
    CREATE TABLE `BASIC_DATA` (
    	`거래처명` VARCHAR(50),
    	`품명` VARCHAR(50),
    	`상품분류` VARCHAR(50),
    	`수량` INT(8),
    	`단가` INT(8)
    );
    
    INSERT INTO BASIC_DATA
    VALUES ('가게1', '물품1', '상품1', 1, 1000)
    ;
    ```

- `IF`문 안에 `EXISTS`를 사용할 수 없다.

  - 따라서 `DELETE` 또는 `DROP`문으로 데이터를 삭제하고자 할 때, 아래와 같이 예외처리하는 Block을 작성해야 한다. 그런데, 접근하려는 Table이 존재하지 않는 경우 실행 자체가 안되기 때문에, Table이 존재하지 않더라도 실행이 되는 Dynamic SQL을 이용해야 한다.

    ```sql
    BEGIN
      EXECUTE IMMEDIATE 'DELETE FROM BASIC_DATA';
    EXCEPTION
      WHEN OTHERS THEN NULL;
    END;
    ```

    ```sql
    BEGIN
      EXECUTE IMMEDIATE 'DROP TABLE BASIC_DATA';
    EXCEPTION
      WHEN OTHERS THEN NULL;
    END;
    ```