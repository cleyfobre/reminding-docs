#prometheus #grafana

1. build.gradle
    
    ```
    implementation 'io.prometheus:simpleclient:0.16.0'
    implementation 'io.prometheus:simpleclient_httpserver:0.16.0'
    ```
    
2. prometheus.yml
    
    ```
    # Global configuration
    global:
      scrape_interval: 15s  # Default scrape interval
      evaluation_interval: 15s  # Default evaluation interval for recording rules
    scrape_configs:
      - job_name: 'board-metrics'
        static_configs:
          - targets: ['localhost:8087']
        metrics_path: '/management/prometheus'
    ```
    
3. application-local.yml
    
    ```
    management:
      endpoints:
        web:
          exposure:
            include: health,info,prometheus
      endpoint:
        prometheus:
          enabled: true
      metrics:
        export:
          prometheus:
            enabled: true
            step: 30s  # Optional, change scrape interval if needed
    ```
    
4. 프로젝트 폴더에서 아래 명령어 실행
    
    ```
    prometheus --config.file=prometheus.yml --web.listen-address=:9092
    ```
    
5. [http://localhost:9092/targets](http://localhost:9092/targets?search=) 접속하여 프로메테우스 서버 정상작동 확인