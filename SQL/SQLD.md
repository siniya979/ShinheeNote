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