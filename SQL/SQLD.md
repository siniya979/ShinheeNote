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



계층형 쿼리 

START WITH : 트리 구조의 최상위 행을 지정합니다.

CONNECT BY : 부모, 자식의 관계를 지정합니다.

PRIOR : CONNECT BY 절에 사용되며 PRIOR에 지정된 컬럼이 맞은편 컬럼을 찾아갑니다.

CONNECT BY PRIOR 자식 컬럼 = 부모 컬럼 : 부모 → 자식 순방향 전개

CONNECT BY PRIOR 부모 컬럼 = 자식 컬럼 : 자식 → 부모 역방향 전개

ORDER SIBLINGS : 계층형 쿼리에서 정렬을 수행합니다.


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


