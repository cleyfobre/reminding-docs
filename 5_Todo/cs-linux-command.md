## 리눅스 성능 체크

- ps -a
- ps aux
  - 현재 돌고있는 프로세스들의 cpu, memory 사용량 등을 표시해줌
- df -h
  - 디스크 폴더별 사용량을 보여줌

### 유용함
- root 기준으로해서 제일 많이 디스크 잡아먹는 폴더 파
	- sudo du -h --max-depth=1 .
- 어떤 디렉토리가 용량 많이 차지하는지 파악
	- sudo du -hsx /* | sort -rh | head -n 40