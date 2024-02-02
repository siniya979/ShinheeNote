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

