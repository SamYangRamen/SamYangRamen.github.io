# SQL Procedure



## 프로시저 생성/호출/삭제 방법

```mysql
CREATE DEFINER=`root`@`%` PROCEDURE `FOO_PROCEDURE`(
	IN `strParameter` VARCHAR(50)
)
LANGUAGE SQL
NOT DETERMINISTIC
CONTAINS SQL
SQL SECURITY DEFINER
COMMENT ''
BEGIN
	DECLARE VAR INT DEFAULT 0;
	SET VAR=12345;
	SELECT `strParameter`, `VAR`;
END;
```

```mysql
CALL `FOO_PROCEDURE`('Hello, world!');
```

| strParameter  |   VAR |
| ------------- | ----: |
| Hello, world! | 12345 |

```mysql
DROP PROCEDURE `FOO_PROCEDURE`;
```





## 프로시저 문법



### 변수 선언

```mysql
DECLARE [변수명] [자료형] [DEFAULT값];
```



### if-else

```mysql
if (/* 조건문 */) then
	# sentences
END if;
```

```mysql
if (/* 조건문 */) then
	# sentences
ELSE
	# sentences
END if;
```



### while

```mysql
while (/* 조건문 */) DO
	# sentences
END while;
```



### Loop

```mysql
loop_name:loop
	if (/* 조건문 */) then
		LEAVE loop_name;
	END if;
END loop;
```



### Cursor

- 어떤 SELECT 결과에 대하여 첫 번째 튜플부터 순차적으로 각 튜플의 데이터 접근할 수 있게 해주는 기능

※ Cursor는 `CREATE TABLE`이 있는 열에서 사용할 수 없다.

```mysql
DECLARE finished INT DEFAULT 0;

DECLARE myCursor CURSOR FOR
SELECT *
FROM BASIC_DATA;

DECLARE CONTINUE handler FOR NOT FOUND SET finished=1;

OPEN myCursor;

myLoop: loop
	fetch myCursor INTO 거래처명, 품명, 상품분류, 수량, 단가;
	if finished=1 then
		leave myLoop;
	END if;
	if (단가 <= 1000) then
		SELECT 거래처명, 품명, 상품분류, 수량, 단가;
	END if;
END loop myLoop;

close myCursor;
```

※ SELECT 결과는 각각 따로따로 나온다.

| 거래처명      | 품명        | 상품분류 | 수량 | 단가 |
| ------------- | ----------- | -------- | ---: | ---: |
| 가양 아트박스 | 모나미 볼펜 | 필기구   |  250 |  100 |

| 거래처명        | 품명       | 상품분류 | 수량 | 단가 |
| --------------- | ---------- | -------- | ---: | ---: |
| 나나문구 대치점 | 스테플러침 | 기타     |   50 |  950 |

| 거래처명        | 품명        | 상품분류 | 수량 | 단가 |
| --------------- | ----------- | -------- | ---: | ---: |
| 나나문구 서현점 | 모나미 볼펜 | 필기구   |  250 |  100 |

