## 연결연산자 ( || )

```sql
SELECT ENAME || SAL 
FROM EMP; 
```

두 컬럼 데이터가 서로 연결되어 출력 
```SQL
SELECT ENAME || ' s SALARY IS ' || SAL || '!' AS 월급정보
FROM EMP; 
```





중복제거 DISTICT / UNIQUE

IS NULL 

!= / ^= / <> 같지 않다. 

> order by는 select 절에 선언한 별칭으로 정렬이 가능
> but where 절은 불가능 . 오라클 sql 실행순서가 아래와 같기 때문
> ![](https://i.imgur.com/Pfia9PL.png)


## 현재 세션의 날짜 형식 조회 
```
select *
from NLS_SESSION_PARAMETERS
WHERE PARAMETER = 'NLS_DATE_FORMAT'
```



# 2

## UPPER, LOWER, INITCAP
각 대문자 / 소문자/ 첫번째 철자 대문자 

## SUBSTR

```sql
SUBSTR('문자열', 시작위치, 철자 개수)
```

# LENGTH
문자열 길이 출력 
```SQL
LENGTH(컬럼명)
```

LENGTHB : 바이트 길이 반환 

# INSTR
```SQL
INSTR('SMITH' ,'T')
```
츨략 ; 4

## REPLACE 
```SQL
REPLACE(컬럼명, 철자, 교체될 철자)
```


```SQL
SELECT REPLACE(ENAME, SUBSTR(ENAME, 2, 1 ), '*')
FROM TEST;
```

이름의 두 번째 자리의 한글을 *로 변경 

# LAPD / RPAD 

문자 출력시 특정 철자를 N개 만큼 채워서 출력 
```SQL
LPAD(컬럼명, 자릿수, 대체 문자)
RPAD(컬럼명, 자릿수, 대체 문자)
```
![](https://i.imgur.com/xh5BWWB.png)


## TRIM / RTRIM / LTRIM

문자 출력시 특정 철자를 잘라내어 출력

## ROUND 반올림 

```SQL
ROUND(123.4567, 1) -- 소수점 두 번째 자리에서 반올림
ROUND(123.4567, -2) -- 십의자리에서 반올림
```


## TRUNC 버림 

```SQL
TRUNC(123.4567, 1) -- 소수점 두 번째 자리부터 버림, 결과 : 123.4
```SQL
TRUNC(123.4567, -2) -- 십의 자리에서 버림, 결과 : 100
```

## MOD 나머지 값 출력 


## MONTH-BETWEEN /ADD-MONTHS



![](https://i.imgur.com/c0gYK1N.png)


## RANK() OVER ( ORDER BY 'colNm' DESC)
RANK() OVER ( PARTITION BY JOB ORDER BY 'colNm' DESC)
![](https://i.imgur.com/pPWR0GN.png)

DENSE_RANK -> 순위가 중복되는 경우 다음 순위는 중복 수만큼 + 됨 즉 1 위가 2명이면 2위가 없고 3위 


select 직업, 이름 , 월급, rank() over(patition by job order by sal desc)
where 입사년도 = 1981 

# NTILE(등급 개수) OVER( ORDER BY 'colNm' DESC NULLS LAST)
NULL 을 맨 아래에 출력, 해당 컬럼명을 기준으로  내림차순, 데이터를 등급 개수에 맞게 등급 정해줌 



## PIVOT
