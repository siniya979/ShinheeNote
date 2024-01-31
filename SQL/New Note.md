```SQL
select case
		when 'columnNm' = 'value' then 'value'
		when 'columnNm1' = 'value1' and 'columnNm2' = 'value2' then 'value3'
		else 'value4'
	end as 'nm'
from table
group by;
```