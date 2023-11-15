#postgresql 

다음 두 Postgresql 쿼리를 보자. 하나는 _LIMIT_ 을 썼고, 다른 하나는 _FETCH FIRST_
를 썼다. 그리고 _ORDER BY_ 뒤에 _nulls last_ 를 붙였다.

```sql
-- LIMIT 사용
select
	max(dong) as dong,
	count(id) as cnt
FROM yc_task yt
WHERE deleted = false
	AND yt.status = 0
	AND yt.serial NOT IN ('young', 'will')
GROUP BY gu_dong
ORDER BY cnt desc
LIMIT 3;

-- FETCH FIRST 사용
select
	max(t1_0.dong) as dong,
	count(t1_0.id) as cnt
from public.yc_task t1_0
where t1_0.deleted=0
	and t1_0.status=0
	and t1_0.serial not in ('young', 'will')
group by t1_0.gu_dong
order by count(t1_0.id) desc nulls last
fetch first 3 rows only;
```

#### nulls last
먼저 _order by count(t1_0.id)_ 뒤에 _nulls last_ 는 null인 값을 그냥 맨 나중에 정렬한다는 의미다.

#### fetch first 3 rows only
일반적으로 Postgresql이나 Mysql에서의 _LIMIT 3_ 과 같은 역할을 한다.