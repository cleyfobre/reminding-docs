## postgresql

### 같은 클러스터 있는지 체크 (1 km = 1000m)
- 2m안에 있는 클러스터 체크

```
select cluster_id 
from yc_target yt 
where public.st_distancespheroid(yt.geo_point, st_setsrid(st_point(126.94380071738566, 37.513454135402945), 4326)) < 2;
```

### get nextval sequence
```
SELECT nextval('yc_cluster_id_seq');
```

### 가장 많이 분포해 있는 작업
- eps := 0.00005
  - 대략 5미터
- minpoints := 2
  - 묶음 안에 2개 이상
- ('목4동' is null OR dong = '목4동')
  - '' 값이 null이면 where절 무시, 있으면 dong 조건으로 검색
```
SELECT
    task_cluster,
    COUNT(*) AS point_count,
    ST_Centroid(ST_Collect(geo_point)) AS centroid
FROM (
    SELECT
        geo_point,
        ST_ClusterDBSCAN(geo_point, eps := 0.00005, minpoints := 2) OVER () AS task_cluster
    FROM yc_task
    where ('목4동' is null OR dong = '목4동')
) AS clusters
WHERE task_cluster IS NOT NULL
GROUP BY task_cluster
ORDER BY point_count desc;
```

### 해당 위경도에 속한 도로 가져오기
```
SELECT *
FROM yc_road
--WHERE ST_Contains(geom, ST_GeomFromText('POINT(126.854716 37.526262)', 4326));
ORDER BY geom <-> ST_GeomFromText('POINT(126.832795 37.530064)', 4326)
LIMIT 1;
```