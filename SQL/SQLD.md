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


