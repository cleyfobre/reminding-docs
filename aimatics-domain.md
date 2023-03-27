## admatics domain

### 트립 관련
 
- fms db:
  - tb_trip_statuses 1 : N tb_trip_events
  - tb_trip_events 1 : 1 tb_uploaded_video_files
- dca db:
  - dca_log.trip_status_id = tb_trip_statuses.id

### DCA Resolver

- DCA 큐 메시지 처리
  - 리스너(Listener): 큐 메시지 파싱
  - 파서(Parser)
    - S3 파일 다운로드
    - bytes 중에 header 정보만 dto로 변환
    - 워커로 넘어갈 때, last_dca 테이블 수정
  - 워커(Worker)
    - 디바이스DCA
      - 트립 생성 (기존 운행중인 트립은 300으로 변경. 100은 운행중)
      - 트립 생성시 이미 Device테이블로부터 기본 정보를 가져옴
    - 리얼타임DCA
      - 리얼타임DCA 리스트 변환
      - 처음과 마지막 DCA로 트립 기본 정보 구성
      - 자체 이벤트(스쿨존, 과속, 연속운전) 생성하여 S3에 txt 업로드
    - 비히클DCA
    - 이벤트DCA
    - 아다스DCA