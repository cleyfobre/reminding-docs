#promtail #grafanaloki #loki 

1. 프로젝트 root 디렉토리에 **promtail.yml** 작성
```yml
server:
  http_listen_port: 9080  # Promtail's web server port
  grpc_listen_port: 0    # Disable gRPC server (set to a specific port if needed)

positions:
  filename: /tmp/positions.yaml  # Track the last read positions for log files

clients:
  - url: "http://localhost:3100/loki/api/v1/push"  # URL of your Loki instance (replace with the actual Loki URL)
    tenant_id: "default"  # If you use multi-tenancy in Loki, replace with the appropriate tenant ID
    batchwait: 1s  # Batch wait time before sending logs
    batchsize: 102400 # Maximum size of the batch in bytes before sending logs
    timeout: 10s      # Timeout for sending logs

scrape_configs:
  - job_name: 'app-logs'
    static_configs:
      - targets:
          - localhost  # This can be replaced with the actual host of the logs (if different)
        labels:
          job: "board-log"
          __path__: /Users/youngmin/logs/board.log  # Path to your log files (adjust as needed)
```

2. brew로 promtail을 설치하고 실행한다.
	1. Install: `brew install promtail`
	2. Run: `promtail -config.file=promtail.yml`
	3. 9080포트(디폴트)로 웹서버 접속이 가능하다.
3. grafana와 loki 서버를 docker로 실행한다.
	1. grafana: `docker run`