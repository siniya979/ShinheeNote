```SQL
select case
		when 'columnNm' = 'value' then 'value'
		when 'columnNm1' = 'value1' and 'columnNm2' = 'value2' then 'value3'
		else 'value4'
	end as 'nm'
from table
group by;



select case 
            when a = b and b = c then 'Equilateral'
            when (a+b) <= c or (a+c) <= b or (b+c) <=a then 'Not A Triangle'
            when a = b or b = c or a = c then 'Isosceles'
            else 'Scalene'
        end
from triangles;
```



```sql

select case when categoryid = 1 then price 
	else null end
from products;


```