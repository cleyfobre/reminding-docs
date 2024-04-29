- 쉘에서 다음과 같이 치면 현재 열린 포트 목록을 확인할 수 있다.  
    `sudo lsof -PiTCP -sTCP:LISTEN`

- 특정 포트를 찾아 포트를 닫고 싶으면 다음과 같이 쳐서 PID를 알아낸다.  
    `sudo lsof -i :3000`  
    여기서 3000이 포트번호이다.
    
- 위에서 나온 PID를 다음 명령어에 넣으면 포트가 닫힌다.  
    `sudo kill -9 PID`