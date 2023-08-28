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
```
SELECT
    work_cluster_id,
    COUNT(*) AS point_count,
    ST_Centroid(ST_Collect(geo_point)) AS centroid
FROM (
    SELECT
        *,
        ST_ClusterDBSCAN(geo_point, eps := 0.01, minpoints := 3) OVER () AS work_cluster_id
    FROM yc_target AS subquery
) AS clusters
WHERE work_cluster_id IS NOT NULL
GROUP BY work_cluster_id
ORDER BY point_count DESC
```

### 해당 위경도에 속한 도로 가져오기
```
SELECT *
FROM yc_road
--WHERE ST_Contains(geom, ST_GeomFromText('POINT(126.854716 37.526262)', 4326));
ORDER BY geom <-> ST_GeomFromText('POINT(126.832795 37.530064)', 4326)
LIMIT 1;
```