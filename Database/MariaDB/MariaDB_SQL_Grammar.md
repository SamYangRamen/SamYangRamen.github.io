# MariaDB SQL 각종 문법



## 일반 Query



### 변수 선언

```mysql
SET @V = 1;
```



### 변수 선언 및 초기화

```mysql
SET @myID = (SELECT myID FROM myTABLE WHERE myID < 3);
```



### ROWNUM

각 튜플에 번호를 매겨주는 기능

Oracle에서는 `ROWNUM` 기능이 별도로 존재하지만, MariaDB는 그렇지 않기 때문에 아래와 같은 방법으로 우회해서 사용해야 한다.

```mysql
SELECT @idx := @idx + 1 as idx, T.*
FROM BASIC_DATA T, (SELECT @idx := 0) D
;
```

```mysql
SET @idx := 0;
SELECT @idx := @idx + 1 as idx, T.*
FROM BASIC_DATA T
;
```

```mysql
SELECT @idx := @idx + 1 as idx, T.*
FROM BASIC_DATA T
WHERE (@idx := 0) = 0
;
```

|  idx | 거래처명        | 품명              | 상품분류 | 수량 |  단가 |
| ---: | --------------- | ----------------- | -------- | ---: | ----: |
|    1 | 가양 아트박스   | 합지 스프링노트   | 노트     |   20 |  2500 |
|    2 | 가양 아트박스   | A4용지            | 복사용지 |   30 | 20000 |
|    3 | 가양 아트박스   | 모나미 볼펜       | 필기구   |  250 |   100 |
|    4 | 가양 아트박스   | 네임펜 흑색       | 기타     |   10 |  6000 |
|    5 | 나나문구 대치점 | 스테플러침        | 기타     |   50 |   950 |
|    6 | 나나문구 대치점 | 네임펜 흑색       | 필기구   |   10 |  6000 |
|    7 | 나나문구 대치점 | 보드마카 청색     | 필기구   |   15 |  4300 |
|    8 | 나나문구 서현점 | 데스크 오거나이저 | 기타     |    5 | 15000 |
|    9 | 나나문구 서현점 | A4용지            | 복사용지 |   30 | 20000 |
|   10 | 나나문구 서현점 | 모나미 볼펜       | 필기구   |  250 |   100 |



### Nested Query

```mysql
SELECT @idx := @idx + 1 as idx, T.*
FROM
(
	(SELECT @idx := 0) D,
	(
		SELECT *
		FROM BASIC_DATA
		WHERE 상품분류 != '필기구'
	) as T
)
;
```

|  idx | 거래처명        | 품명              | 상품분류 | 수량 |  단가 |
| ---: | --------------- | ----------------- | -------- | ---: | ----: |
|    1 | 가양 아트박스   | 합지 스프링노트   | 노트     |   20 |  2500 |
|    2 | 가양 아트박스   | A4용지            | 복사용지 |   30 | 20000 |
|    3 | 가양 아트박스   | 네임펜 흑색       | 기타     |   10 |  6000 |
|    4 | 나나문구 대치점 | 스테플러침        | 기타     |   50 |   950 |
|    5 | 나나문구 서현점 | 데스크 오거나이저 | 기타     |    5 | 15000 |
|    6 | 나나문구 서현점 | A4용지            | 복사용지 |   30 | 20000 |



### case-when

```mysql
UPDATE BASIC_DATA
SET 단가=
	case
		when 상품분류='필기구' then
			case
				when 단가<500 then 단가*2
				when 단가>5000 then 단가/2
				ELSE 단가-300
			END
		ELSE 0
	END
WHERE 거래처명 LIKE '나나문구%'
;
```

| 거래처명        | 품명              | 상품분류 | 수량 |  단가 |
| --------------- | ----------------- | -------- | ---: | ----: |
| 가양 아트박스   | 합지 스프링노트   | 노트     |   20 |  2500 |
| 가양 아트박스   | A4용지            | 복사용지 |   30 | 20000 |
| 가양 아트박스   | 모나미 볼펜       | 필기구   |  250 |   100 |
| 가양 아트박스   | 네임펜 흑색       | 기타     |   10 |  6000 |
| 나나문구 대치점 | 스테플러침        | 기타     |   50 |     0 |
| 나나문구 대치점 | 네임펜 흑색       | 필기구   |   10 |  3000 |
| 나나문구 대치점 | 보드마카 청색     | 필기구   |   15 |  4000 |
| 나나문구 서현점 | 데스크 오거나이저 | 기타     |    5 |     0 |
| 나나문구 서현점 | A4용지            | 복사용지 |   30 |     0 |
| 나나문구 서현점 | 모나미 볼펜       | 필기구   |  250 |   200 |



### 집계함수

```mysql
SELECT SUM(단가) FROM BASIC_DATA; #합계
```

```mysql
SELECT AVG(단가) FROM BASIC_DATA; #평균
```

```mysql
SELECT COUNT(수량) FROM BASIC_DATA; #총 개수
```

```mysql
SELECT MAX(단가) FROM BASIC_DATA; #최댓값
```

```mysql
SELECT MIN(단가) FROM BASIC_DATA; #최솟값
```

※ MAX(), MIN()은 SELECT 결과가 존재하지 않을 경우 NULL을 반환하기 때문에, case-when 등으로 아래와 같이 예외 처리를 해주어야 할 때가 있었다.

```mysql
SET @단가_최대 = (SELECT MAX(단가) FROM BASIC_DATA);
SELECT (case when @단가_최대 IS NULL then 0 ELSE @단가_최대 END) AS 단가;
```



### 기존의 테이블을 대체하는 테이블 생성

```mysql
CREATE OR REPLACE TABLE BASIC_DATA
(
	학교명 VARCHAR(50) NULL,
	학과 VARCHAR(50) NULL,
	성별 VARCHAR(50) NULL,
	학생수 INT NULL
)
;
```

| 학교명 | 학과 | 성별 | 학생수 |
| ------ | ---- | ---- | ------ |
|        |      |      |        |



### SELECT문으로 테이블 생성

```mysql
CREATE OR REPLACE TABLE BASIC_DATA
SELECT '없음' as 거래처명, '없음' as 품명, '없음' as 상품분류, NULL as 수량, NULL as 단가
```

| 거래처명 | 품명 | 상품분류 | 수량 | 단가 |
| -------- | ---- | -------- | ---- | ---- |
| 없음     | 없음 | 없음     | \N   | \N   |



### 문자열 함수

BIT_LENGTH(문자열) : 할당된 비트 크기 반환

CHAR_LENGTH(문자열) : 문자 개수 반환

LENGTH(문자열) : 할당된 바이트 수 반환

영문 : 문자당 1바이트, 한글: 문자당 3바이트

```mysql
SELECT BIT_LENGTH('ABC'), CHAR_LENGTH('ABC'), LENGTH('ABC') # 24, 3, 3 반환
```

```mysql
SELECT BIT_LENGTH('가나다'), CHAR_LENGTH('가나다'), LENGTH('가나다') # 72, 3, 9 반환
```

CONCAT(문자열들) : 문자열을 이어줌

CONCAT_WS(구분자, 문자열들) : 구분자와 함께 문자열을 이어줌

```mysql
SELECT CONCAT('ABCD', '1234', '가나다라'); # 'ABCD1234가나다라' 반환
```

```mysql
SELECT CONCAT_WS(',', 'ABCD', '1234', '가나다라'); # 'ABCD,1234,가나다라' 반환
```

SUBSTRING(문자열, 시작위치, 길이) : 시작위치부터 길이만큼 문자를 반환, 길이를 생략하면 문자열 끝까지 반환

SUBSTRING_INDEX(문자열, 구분자, 횟수) : 문자열에서 구분자가 왼쪽부터 횟수 번째 나오면 그 이후 문자열을 버리고 앞 문자열을 반환. 횟수가 음수면 오른쪽부터 셈

```mysql
SELECT SUBSTRING('ABCDEFGH', 3, 4) # CDEF
SELECT SUBSTRING('ABCDEFGH', 3) # CDEFGH
```

```mysql
SELECT SUBSTRING_INDEX('A,B,C,D,E', ',', 3) # A,B,C
SELECT SUBSTRING_INDEX('A,B,C,D,E', ',', -3) # C,D,E
```



### EXECUTE IMMEDITATE

query string을 만들어 실행하는 Dynamic SQL

```mysql
SET @selectItem = '*';
SET @tableName = 'BASIC_DATA'
EXECUTE IMMEDIATE CONCAT('SELECT ', @selectItem, ' FROM ', @tableName, ';');
```

