# 다차원 집계 
```SQL
CREATE TABLE Sales (
    Region VARCHAR(50),
    Country VARCHAR(50),
    Product VARCHAR(50),
    SalesAmount DECIMAL(10, 2)
);

INSERT INTO Sales (Region, Country, Product, SalesAmount) VALUES
('North', 'USA', 'Product_A', 100),
('North', 'USA', 'Product_B', 150),
('North', 'Canada', 'Product_A', 120),
('North', 'Canada', 'Product_B', 130),
('South', 'Brazil', 'Product_A', 80),
('South', 'Brazil', 'Product_B', 90),
('South', 'Argentina', 'Product_A', 110),
('South', 'Argentina', 'Product_B', 100);
```

## CUBE 
```SQL
SELECT Region, Country, SUM(SalesAmount) AS TotalSales
FROM Sales
GROUP BY CUBE(Region, Country);
```

결과 
``` SQL
Region  | Country   | TotalSales
--------------------------------
North   | USA       | 250
North   | Canada    | 250
North   | NULL      | 500
South   | Brazil    | 170
South   | Argentina | 210
South   | NULL      | 380
NULL    | NULL      | 880
```

## ROLLUP

```SQL
SELECT Region, Country, SUM(SalesAmount) AS TotalSales
FROM Sales
GROUP BY ROLLUP(Region, Country);
```

결과 
``` SQL
Region  | Country   | TotalSales
--------------------------------
North   | USA       | 250
North   | Canada    | 250
North   | NULL      | 500
South   | Brazil    | 170
South   | Argentina | 210
South   | NULL      | 380
NULL    | NULL      | 880
```

##  GROUPING SETS 
```SQL
SELECT Region, Country, SUM(SalesAmount) AS TotalSales
FROM Sales
GROUP BY GROUPING SETS((Region), (Country), ());
```

결과 
``` SQL
Region  | Country   | TotalSales
--------------------------------
North   | NULL      | 500
South   | NULL      | 380
NULL    | USA       | 250
NULL    | Canada    | 250
NULL    | Brazil    | 170
NULL    | Argentina | 210
NULL    | NULL      | 880
```


# 집합 연산자

UNION : 합집합
UNION ALL : 합집한 + 중복 허용 
INTERSECT : 교집합 
EXCEPT (=MINUS): 앞 결과 - 뒤 결과 



# 계층형 쿼리 

START WITH : 트리 구조의 최상위 행을 지정합니다.

CONNECT BY : 부모, 자식의 관계를 지정합니다.

PRIOR : CONNECT BY 절에 사용되며 PRIOR에 지정된 컬럼이 맞은편 컬럼을 찾아갑니다.

CONNECT BY PRIOR 자식 컬럼 = 부모 컬럼 : 부모 → 자식 순방향 전개

CONNECT BY PRIOR 부모 컬럼 = 자식 컬럼 : 자식 → 부모 역방향 전개

ORDER SIBLINGS : 계층형 쿼리에서 정렬을 수행합니다.

LEVEL : ROOT 의 레밸은 1 


```sql
select parent_c as p, child_c as c from t1;
```


```sql
P  C
-- --
a  b
b  c
a  c
c  d
c  e
e  f
```

**[부모→자식 순방향 전개 계층형 쿼리 예제]**

```sql
select parent_c as p, child_c as c, level 
from t1
start with parent_c = 'a'
connect by prior child_c = parent_c;
```

```sql
P  C       LEVEL
-- -- ----------
a  b           1 <- 첫번째 부모행을 시작으로 자식 탐색
b  c           2
c  d           3
c  e           3
e  f           4 <- 첫번째 부모행의 자식 탐색 종료
a  c           1 <- 두번째 부모행의 자식 탐색 시작
c  d           2
c  e           2
e  f           3 <- 두번째 부모행의 자식 탐색 종료
```

**[실행 결과 해석]**

LEVEL을 보면 첫 번째 최상위 부모 행을 찾으면 자식이 없을 때까지 계속 타고 들어갔다가 마지막까지 탐색 후 두 번째 최상위 부모 행의 자식을 탐색합니다.


**[CONNECT BY에 조건절을 넣을 경우 예제]**

```
select parent_c as p, child_c as c, level 
from t1
start with parent_c = 'a'
connect by prior child_c = parent_c and parent_c='c';
```

**[실행 결과]**

```
P  C       LEVEL
-- -- ----------
a  b           1
a  c           1
c  d           2
c  e           2
```

**[실행 결과 해석]**

start with 절에서 선택된 부모 데이터는 무조건 포함이 되고 자식 데이터들에 의해 parent_c='c' 조건으로 필터링된 결과가 나타납니다.


**[ORDER SIBLINGS 사용한 계층형 쿼리 예제]**

```sql
select parent_c as p, child_c as c, level 
from t1
start with parent_c = 'a'
connect by prior child_c = parent_c
order siblings by child_c desc;
```

**[실행 결과]**

```
P  C       LEVEL
-- -- ----------
a  c           1 <- LEVEL 1 중에서 child_c desc 정렬했을 때 c가 가장 맨위에 옴
c  e           2 <- LEVEL 2 중에서(위 LEVEL 1의 자식 기준) child_c desc 정렬했을 때 e가 가장 맨위에 옴
e  f           3
c  d           2
a  b           1 <- LEVEL 1 중에서 child_c desc 정렬했을 때 두번째인 b가 옴
b  c           2
c  e           3
e  f           4
c  d           3
```

**[실행 결과 해석]**

LEVEL 1에서 진입하기 전에 정렬을 한 후 정렬한 결과의 첫 번째 행부터 자식 행을 찾습니다. 이는 자식행을 들어가서도 같은 LEVEL 내에서 정렬이 된 후 첫 번째 행부터 타고 들어갑니다.






기타
![](https://i.imgur.com/JX7oQtW.png)

DML ; select, delete, update, insert
DDL : create, drop, alter, rename
DCL : grant(권한 부여), revoke (권한 취소)
TCL : rollback, commit

```SQL
REVOKE 권한 ON 객체 FROM 사용자 또는 롤; 
REVOKE SELECT ON TABLE_NAME FROM USERNAME;

GRANT 권한 ON 객체 TO 사용자;
GRANT SELECT ON EMPLOYEE TO USER1;
GRANT ALL PRIVILEGES ON EMPLOYEES TO USER1; -- 모든 권한 부여 
```


```SQL
CREATE TABLE Employees (
    Employee_ID INT,
    Name VARCHAR(50),
    Department VARCHAR(50)
);

ALTER OBJECT_TYPE OBJECT_NAME ADD COLUMN COLUMN_NAME COLUMN_TYPE;
ALTER OBJECT_TYPE OBJECT_NAME MODIFY COLUMN COLUMN_NAME NEW_COLUMN_TYPE; 
ALTER OBJECT_TYPE OBJECT_NAME DROP COLUMN COLUMN_NAME;

ALTER TABLE Employees ADD COLUMN Age INT; 
ALTER TABLE Employees MODIFY COLUMN Name VARCHAR(100) DEFAULT 'SAMPLE'; 
ALTER TABLE Employees DROP COLUMN Department;

ALTER TABLE Employees DROP CONSTRAINT PK_Employees; -- 제약 조건 삭제 
ALTER TABLE Employees ADD CONSTRAINT PK_Employees PRIMARY KEY (Employee_ID); -- 제약 조건 추가 

DROP OBJECT_TYPE OBJECT_NAME;
DROP TABLE Employees;

TRUNCATE TABLE TABLE_NAME;
TRUNCATE TABLE Employees;

RENAME COLUMN TEAM_ID TO T_ID;
RENAME PLAYER TO PLAYER_BACKUP;
```

`DROP` `TRUNCATE` 의 차이는 DROP 은 테이블(객체)를 삭제하고, TRUNCATE 는 테이블의 모든 데이터를 삭제하고 구조는 유지된다. 

DDL 명령어의 경우 실행시 AUTO COMMIT, DML 은 COMMIT 입력 해야 한다. 

```SQL
INSERT INTO 테이블명(컬럼명) VALUES('입력값');
UPDATE 테이블명 SET 컬럼명 = '입력값';
DELETE FROM 테이블명;
```


테이블 
- 가로 = 행 = 로우 = 튜플 = 인스턴스 
- 세로 = 열 = 컬럼 

숫자형 함수 
```SQL
ABS : 절대값
SIGN(NUM) : 양수면 1 음수면 -1 , 0이면 0
MOD(NUM1, NUM2) : NUM % NUM2
CEIL(NUM)  : 올림
FLOOR(NUM) : 내림
ROUND(38.5235, 3 ) : 38.524
TRUNC(38.5235, 3 ) : 38.523 , 3번째 자리에서 잘라 버림 
```

날짜형

```SQL
EXTRACT : 날짜에서 데이터 출력
TO_NUMBER(TO_CHAR(D,'YYYY'))  연도를 숫자로 
```

```
1 = 하루 
1/24 = 1 시간 ( 24시간을 1로 나눔)
1/24/60 = 1분 ( 1시간을 60으로 나눔 )
```

연산자 우선순위 

( ) -> NOT -> 비교연산자 -> AND -> OR 


NULL 관련 함수 

- NVL ( 표현식1, 표현식 2 ) : 표현식1 의 결과값이 NULL 이면 표현식2 출력
- ISNULL(표현식1, 표현식2) : 위와 같음 
- NULLIF(표현식1, 표현식2) : 표현식1 == 표현식2 면, NULL 아니면 표현식1 리턴 
- COALESCE(표현식1, 표현식2, ....) : NULL 이 아닌 최초 표현식 나타낸다. 모든 표현식이 NULL 이면 NULL 리턴

다중행 집계 함수
- COUNT(*) : NULL 집계
- COUNT(표현식) : NULL 제외 


CASE WHEN LOC = 'A' THEN 'B'

CASE LOC WHEN 'A' THEN 'B'
= DECODE(LOC, 'A', 'B')


ORDER BY 
- ORACLE 에서는 NULL을 가장 큰 값으로 
- SQL SERVER 에서는 NULL 을 가장 작은 값으로 취급 


TOP(NUM) WITH TIES
TOP(N) : 정렬된 컬럼 기준으로 N 번째(등급)까지 출력 
WITH TIES : 랭킹이 같은 경우도 포함 


# 조인 종류 

- EQUI JOIN : INNER JOIN / NATURAL JOIN / JOIN USING 
- NON EQUI JOIN : 2개의 테이블 간에 칼럼 값들이 서로 정확하게 일치하지 않는 경우에 사용 ‘=’ BETWEEN, >, <= 연산자가 아닌 등 연산자 사용

- Natural join  : 두 테이블 간 동일한 이름을 갖는 모든 칼럼들에 대해 EQUI JOIN 수행
  BUT WEHRE 절에서 JOIN 조건을 정의 못함. (USING, ON 사용 X ), SQL SERVER 에서 지원 X 
- USING 조건절 : ALIAS 사용 불가, SQL SERVER 지원 X 


OUTER JOIN 
- (+) 안 붙은 쪽으로 JOIN ,즉 안 붙은 쪽이 기준 

일반 집합 연산자
- UNION : 합집합 (중복행 1개로)
- UNION ALL  : 합집합 (중복 허용)
- INTERSECT : 교집합 (중복행 1개로)
- MINUS : 차집합 (중복행은 1개로 )
- CROSS JOIN : 곱집합 ( M * N ),  카타시안 곱

#### MINUS : 차집합

MINUS는 첫 번째 SELECT문에 의해 반환되는 행 중에서 두 번째 SELECT문에 의해 반환되는 행에 존재하지 않는 행들을 반환 한다.

```SQL
-- 사원이 없는 부서를 조회하는 MINUS 예제.
SELECT deptNo FROM dept
MINUS
SELECT deptNo FROM emp;
```






뷰 사용 장점 
독립성, 편리성, 보안성 

```SQL
CREATE VIEW V_EMPLOYEE AS 쿼리 ...;

DROP VIEW V_EMPLOYEE;
```

# PIVOT ,UNPIVOT 
TOP N 쿼리 
정규 표현식 
윈도우 함수 



![](https://i.imgur.com/XESDg2u.png)



  
제1정규형 : 아래의 조건을 만족하는 정규형

-> 모든 속성의 도메인이 원자 값(atomic value)으로만 (다중값이 아닌) 구성되어 있으면 제1정규형에 속한다.


제2정규형 : 아래의 조건을 만족하는 정규형

-> 제1정규형에 속하고, 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속되면 제2정규형에 속한다.


제3정규형 : 아래의 조건을 만족하는 정규형

-> 제2정규형에 속하고, 기본키가 아닌 모든 속성이 기본키에 이행적 함수 종속이 되지 않으면 제3정규형에 속한다.
-> 속성간의 함수 종속성이 발생하지 않는 경우

​

제1정규화 대상 : 제1정규형의 조건을 만족하지 못하는 상태

제2정규화 대상 : 제1정규형이지만 제2정규형의 조건을 만족하지 못하는 상태

제3정규화 대상 : 제2정규형이지만 제3정규형의 조건을 만족하지 못하는 상태


![](https://i.imgur.com/oEMoX6F.png)


cube, rollup, grouping sets 공부하기 

