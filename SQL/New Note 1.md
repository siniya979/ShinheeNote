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

