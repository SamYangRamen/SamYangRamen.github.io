# Tibero Procedure



## Tibero 프로시저의 특징

- 변수의 선언은 `IS`절 또는 `DECLARE`절에서만 가능하다.
- DDL (CREATE, DROP, ALTER, TRUNCATE, ...), DCL(GRANT, REVOKE) 명령어는 Dynamic SQL을 이용할 때만 사용 가능하다.
- SELECT문의 결과를 내부 엔진으로 보내는데, 이를 캐치하기 위한 변수를 선언해야 하고, INTO절을 통해 해당 변수에 입력하는 식으로 작성되어야 하며, 반드시 한 개의 값만 검색되어야 한다. 



## 프로시저 생성/호출/삭제 방법

- CREATE : 프로시저 선언 및 파라미터 정의
- IS : 변수 선언
- BEGIN : 기능 구현
- END : 프로시저 종료

```sql
CREATE OR REPLACE PROCEDURE FOO_PROCEDURE (
	param1 IN INTEGER,
	param2 IN INTEGER
)
IS
	result INTEGER := 0;
BEGIN
	result := param1 + param2;
	dbms_output.put_line(result);
EXCEPTION
END;
```

```sql
CALL FOO_PROCEDURE(3, 7);
```

```sql
10 /* 실행 결과 */
```

```sql
DROP PROCEDURE FOO_PROCEDURE;
```



## 프로시저 문법



### 변수 선언 (+ 블록이란?)

- MariaDB와는 다르게, 중간중간에 변수 선언이 불가능하다.

- 반드시 IS 영역에 변수를 선언해두어야 한다.

- 만약 중간중간에 변수를 선언하여 사용하고 싶다면, `DECLARE-BEGIN-EXCEPTION-END` 키워드를 사용하여 아래와 같은 문장을 중간중간에 작성해서 사용하는 방법 밖에 없다.

  - 아래와 같은 문장들을 한 묶음으로 '블록' 이라고 말한다.

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

  ```sql
  -- 프로시저 안에서 블록을 사용하고자 할 때 형식 예시
  
  CREATE OR REPLACE PROCEDURE ... (
    ...
  )
  IS
    ...
  BEGIN
    ...
    DECLARE
      ...
    BEGIN
      ...
    EXCEPTION
      ...
    END;
    ...
    DECLARE
      ...
    BEGIN
      ...
    EXCEPTION
      ...
    END;
    ...
  EXCEPTION
    ...
  END;
  ```

  

#### 변수 선언 방법

- 스칼라 변수 : 원래 하던 방법

  ```sql
  VEMPNO NUMBER(4);
  VENAME VARCHAR2(10);
  ```

- 레퍼런스 변수 : 이전에 선언된 다른 변수나 DB 컬럼에 맞춰서 변수를 선언하는 방법

  ```
  TEMPNO VEMPNO%TYPE;
  TEMPNAME EMP.ENAME%TYPE;
  ```

  

### 변수 입력

```sql
result := param1 + param2;
i := i + 1;
myString := 'Hello, world!';
```



### SELECT문

- MariaDB의 프로시저에서는 `select ? FROM ? ...` 라고 작성하여 그 결과를 출력할 수 있었으나, Tibero의 프로시저에서는 `INTO` 키워드를 이용하여 SELECT 결과를 변수에 대입하는 방식으로 작성하는 수 밖에 없다.

- 이때, SELECT 결과는 반드시 한 Column의 한 Row, 즉 단 하나의 값이어야만 한다.

  ```sql
  IS
    total NUMBER := 0;
  BEGIN
  ...
    SELECT SUM(단가)
    INTO total
    FROM BASIC_DATA;
  
    dbms_output.put_line(total);
  ...
  END;
  ```

  ```sql
  74,950 /* 출력 결과 */
  ```

- 또한, SELECT 결과가 존재하지 않아서 INTO 옆의 변수 안에 들어갈 값이 없다면, 에러 메시지를 띄우면서 컴파일이 되지 않는다. 따라서 아래와 같이 DECLARE-BEGIN-EXCEPTION-END로 감싸서 작성해야 한다.

  ```sql
  ...
  	DECLARE
  	BEGIN
  		SELECT 단가
  		INTO myValue
  		FROM BASIC_DATA
  		WHERE 단가 > 1000000;
  	EXCEPTION
  		WHEN NO_DATA_FOUND THEN
  			/* sentences */
  	END;
  ...
  ```

- 단지 변수에 어떤 값만을 입력하는 용도로 사용할 수도 있다. 단, Tibero는 FROM절이 없을 경우 SELECT문이 실행되지 않기 때문에, `DUAL`이라는 Dummy Table을 이용해서 입력해야 한다.

  ```sql
  SELECT 123
  INTO result
  FROM DUAL;
  ```



### if-else

```sql
if (/* 조건문 */) then
	/* sentences */
END if;
```

```sql
if (/* 조건문 */) then
	/* sentences */
ELSE
	/* sentences */
END if;
```



### for

```sql
FOR i IN 1..10 LOOP -- i는 미리 선언된 변수임
	/* sentences */
END LOOP;
```

```sql
FOR i IN START..END LOOP -- i, START, END는 미리 선언된 변수임
	/* sentences */
END LOOP;
```



### while

```sql
while (/* 조건문 */) LOOP
	/* sentences */
END LOOP;
```



### loop

```sql
LOOP
EXIT WHEN /* 조건문 */;
	/* sentences */
END LOOP;
```



### dynamic SQL

- 개발 편의를 위해 한 query string을 IDE에서 여러 줄에 걸쳐 표현하기 위해서는,

  ```sql
  EXECUTE IMMEDIATE '
    CREATE OR REPLACE TABLE BASIC_DATA (
      거래처명 VARCHAR(50),
      품명 VARCHAR(50),
      상품분류 VARCHAR(50),
      수량 INTEGER,
      단가 INTEGER
    );
  ';
  ```

  MariaDB에서 사용했던 위와 같은 방법을 사용할 수 없으므로,

  ```sql
  queryString := ''
    || 'CREATE OR REPLACE TABLE BASIC_DATA ( '
    || '  거래처명 VARCHAR(50), '
    || '  품명 VARCHAR(50), '
    || '  상품분류 VARCHAR(50), '
    || '  수량 INTEGER, '
    || '  단가 INTEGER '
    || ');';
  EXECUTE IMMEDIATE queryString;
  ```

  위와 같이 `||`를 써서 이어붙인 string을 변수에 넣은 다음, 그 변수에 대하여 EXECUTE를 해야 한다.

- `SELECT` 문을 dynamic SQL로 실행하기 위해서는 아래와 같이 `INTO`문을 string 바깥으로 빼서 표현해야 한다.

  ```sql
  EXECUTE IMMEDIATE 'SELECT SUM(단가) FROM BASIC_DATA' INTO total;
  ```

  다만, 아래와 같이 sub query로서 작성된 내부의 `SELECT`문에는 INTO문을 작성할 필요가 없다.

  ```sql
  EXECUTE IMMEDIATE 'SELECT * FROM ( SELECT SUM(단가) FROM BASIC_DATA );' INTO total;
  ```

- query string 안에 변수가 들어갈 수 없다.

  ```sql
  -- 안되는 문장
  DECLARE
    dummy1 INTEGER := 12345;
    dummy2 INTEGER := 0;
  BEGIN
    EXECUTE IMMEDIATE 'SELECT dummy1 FROM DUAL' INTO dummy2;
  EXCEPTION
    WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE('Dynamic SQL ERROR');
  END;
  ```

  ```sql
  -- 되는 문장
  DECLARE
    dummy1 INTEGER := 12345;
    dummy2 INTEGER := 0;
  BEGIN
    EXECUTE IMMEDIATE 'SELECT ' || dummy1 || ' FROM DUAL' INTO dummy2;
  EXCEPTION
    WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE('Dynamic SQL ERROR');
  END;
  ```

  

