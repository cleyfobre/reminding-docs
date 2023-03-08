## admatics domain



### 트립 관련
 - fms db:
 	- tb_trip_statuses 1 : N tb_trip_events
 	- tb_trip_events 1 : 1 tb_uploaded_video_files
 - dca db:
 	- dca_log.trip_status_id = tb_trip_statuses.id