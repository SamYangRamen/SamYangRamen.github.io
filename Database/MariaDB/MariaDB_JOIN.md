# MariaDB SQL JOIN



## 예제 테이블

```mysql
CREATE TABLE STD_NAME (
	`ID` INT,
	`NAME` VARCHAR(50),
	PRIMARY KEY (`ID`)
);

INSERT INTO STD_NAME
VALUES
	(1, '가가가'),
	(2, '나나나'),
	(3, '다다다')
;
```

|   ID | NAME   |
| ---: | ------ |
|    1 | 가가가 |
|    2 | 나나나 |
|    3 | 다다다 |

```mysql
CREATE TABLE STD_GRADE (
	`ID` INT,
	`GRADE` VARCHAR(50),
	PRIMARY KEY (`ID`)
);

INSERT INTO STD_GRADE
VALUES
	(1, 'A'),
	(2, 'B'),
	(4, 'A'),
	(5, 'C')
;
```

|   ID | NAME |
| ---: | ---- |
|    1 | A    |
|    2 | B    |
|    4 | A    |
|    5 | C    |





## JOIN의 종류



### CROSS JOIN : A×B

```mysql
SELECT *
FROM STD_NAME JOIN STD_GRADE
;

SELECT *
FROM STD_NAME CROSS JOIN STD_GRADE
;

SELECT *
FROM STD_NAME, STD_GRADE
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    1 | 가가가 |    1 | A    |
|    2 | 나나나 |    1 | A    |
|    3 | 다다다 |    1 | A    |
|    1 | 가가가 |    2 | B    |
|    2 | 나나나 |    2 | B    |
|    3 | 다다다 |    2 | B    |
|    1 | 가가가 |    4 | A    |
|    2 | 나나나 |    4 | A    |
|    3 | 다다다 |    4 | A    |
|    1 | 가가가 |    5 | C    |
|    2 | 나나나 |    5 | C    |
|    3 | 다다다 |    5 | C    |



### INNER JOIN : A∩B

```mysql
SELECT *
FROM
	STD_NAME A
	INNER JOIN
	STD_GRADE B
	ON A.ID=B.ID
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    1 | 가가가 |    1 | A    |
|    2 | 나나나 |    2 | B    |



### LEFT JOIN : A

```mysql
SELECT *
FROM STD_NAME A
     LEFT JOIN
     STD_GRADE B
     ON A.ID=B.ID
;

SELECT *
FROM STD_NAME A
     LEFT OUTER JOIN
     STD_GRADE B
     ON A.ID=B.ID
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    1 | 가가가 |    1 | A    |
|    2 | 나나나 |    2 | B    |
|    3 | 다다다 |   \N | \N   |



### LEFT JOIN with exclusion : A－B

```mysql
SELECT *
FROM STD_NAME A
     LEFT JOIN
     STD_GRADE B
     ON A.ID=B.ID
WHERE B.ID IS NULL
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    3 | 다다다 |   \N | \N   |



### RIGHT JOIN : B

```mysql
SELECT *
FROM STD_NAME A
     RIGHT JOIN
     STD_GRADE B
     ON A.ID=B.ID
;

SELECT *
FROM STD_NAME A
     RIGHT OUTER JOIN
     STD_GRADE B
     ON A.ID=B.ID
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    1 | 가가가 |    1 | A    |
|    2 | 나나나 |    2 | B    |
|   \N | \N     |    4 | A    |
|   \N | \N     |    5 | C    |



### RIGHT JOIN with exclusion : B－A

```mysql
SELECT *
FROM STD_NAME A
     RIGHT JOIN
     STD_GRADE B
     ON A.ID=B.ID
WHERE A.ID IS NULL
;
```

|   ID | NAME |   ID | NAME |
| ---: | ---- | ---: | ---- |
|   \N | \N   |    4 | A    |
|   \N | \N   |    5 | C    |



### FULL OUTER JOIN : A∪B

MariaDB에서는 이 기능을 제공하지 않기 때문에, ```UNION```을 사용하여 같은 기능을 하는 Query를 작성해야 한다.

```mysql
SELECT *
FROM STD_NAME A
     LEFT JOIN
     STD_GRADE B
     ON A.ID=B.ID
UNION
SELECT *
FROM STD_NAME A
     RIGHT JOIN
     STD_GRADE B
     ON A.ID=B.ID
;
```

|   ID | NAME   |   ID | NAME |
| ---: | ------ | ---: | ---- |
|    1 | 가가가 |    1 | A    |
|    2 | 나나나 |    2 | B    |
|    3 | 다다다 |   \N | \N   |
|   \N | \N     |    4 | A    |
|   \N | \N     |    5 | C    |



------



### 정해진 ID 범위 내에서 모든 데이터를 편하게 보기 위한 방법

```mysql
SELECT *
FROM
(
	WITH RECURSIVE CTE AS
	(
		SELECT 1 AS ID
		UNION ALL
		SELECT ID + 1 FROM CTE WHERE ID < 10
	) SELECT * FROM CTE
) AS T
LEFT JOIN STD_NAME A ON T.ID=A.ID
LEFT JOIN STD_GRADE B ON T.ID=B.ID
;
```

|   ID |   ID | NAME   |   ID | GRADE |
| ---: | ---: | ------ | ---: | ----- |
|    1 |    1 | 가가가 |    1 | A     |
|    2 |    2 | 나나나 |    2 | B     |
|    3 |    3 | 다다다 |   \N | \N    |
|    4 |   \N | \N     |    4 | A     |
|    5 |   \N | \N     |    5 | C     |
|    6 |   \N | \N     |   \N | \N    |
|    7 |   \N | \N     |   \N | \N    |
|    8 |   \N | \N     |   \N | \N    |
|    9 |   \N | \N     |   \N | \N    |
|   10 |   \N | \N     |   \N | \N    |

```mysql
SELECT T.ID, A.NAME, B.GRADE
FROM
(
	WITH RECURSIVE CTE AS
	(
		SELECT 1 AS ID
		UNION ALL
		SELECT ID + 1 FROM CTE WHERE ID < 10
	) SELECT * FROM CTE
) AS T
LEFT JOIN STD_NAME A ON T.ID=A.ID
LEFT JOIN STD_GRADE B ON T.ID=B.ID
;
```

|   ID | NAME   | GRADE |
| ---: | ------ | ----- |
|    1 | 가가가 | A     |
|    2 | 나나나 | B     |
|    3 | 다다다 | \N    |
|    4 | \N     | A     |
|    5 | \N     | C     |
|    6 | \N     | \N    |
|    7 | \N     | \N    |
|    8 | \N     | \N    |
|    9 | \N     | \N    |
|   10 | \N     | \N    |