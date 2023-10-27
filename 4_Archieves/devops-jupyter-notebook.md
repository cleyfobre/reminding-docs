## jupyter notebook

### why

- ec2를 웹브라우져 환경에서 편하게 관리하고 싶다.

### todo

- 주피터노트북을 설치 및 실행한다.
- 접근에 패스워드를 설정한다.
- https 세팅을 한다.
- ec2가 재부팅되더라도 항상 실행될 수 있도록 service로 등록해놓는다.

### process

1. jupyter notebook 설치
  - sudo apt-get update
  - sudo apt-get install python3-pip -y
  - sudo pip3 install notebook

2. jupyter password 설정
  - python3 치고 아래부턴 python 명령어
  - from notebook.auth import passwd
  - Enter password: 1234
  - Verify password: 1234
  - 비밀번호 해시값 나옴.
  
3. jupyter 환경설정에 비밀번호 넣어둠
  - 환경설정 파일 생성하기
    - jupyter notebook --generate-config
    - 위 명령어 치면 생성된 파일 경로 나옴
      - /home/ubuntu/.jupyter/jupyter_notebook_config.py
  - 환경설정 파일 수정하기
    - sudo vi /home/ubuntu/.jupyter/jupyter_notebook_config.py
    - 맨 밑에 아래 설정 추가해줌(생성된 패스워드, ec2 주소 등)
      - ```
		c = get_config()
		c.NotebookApp.password = u'argon2:$argon2id$v=19$m=10240,t=10,p=8$S9DmXPeRDnLSohJnjGad0A$+GOgwYJgoXSJbFZuRk+8KA+VbCHHjtwl1iVfsPvY6GI'
		c.NotebookApp.ip = '172.31.30.198'
		c.NotebookApp.notebook_dir = '/'
		```

4. jupyter 실행
  - jupyter-notebook --allow-root (앞에 sudo 붙이지 마)
    - 삽질: vpc 문젠줄알고 졸라 찾았는데.. apache2 존나 80에서 접근 잘되길래 vpc 문제는 아니였다는 걸 알게 되었고.. 실행하는 쿼리 다시 좀 찾아봤더니 그냥 jupyter-notebook로 실행하니까 로그가 확실히 다르게 뜨더라... 로컬에서 접근하니까 이제야 접근 가능해짐. 후훗
  - 퍼블릭 IP로 접근: http://54.180.142.137:8888
  - 패스워드는 위에서 설정한 1234

5. 백그라운드에서 돌아갈 수 있게 설정
  - Ctrl + z 눌러 잠시 멈춤
  - bg : 백그라운드에서 돌아갈 수 있도록 하는 명령어
  - disown -h : 소유권 포기
  - 위에 명령어 치면 백그라운드에서 잘 돌아간다.

6. https 붙이기
  - 기존 프로세스 죽이기
    - sudo netstat -nap | grep 8888 (없으면 sudo apt-get install net-tools)
    - sudo kill -9 {PID}
  - 사설 인증서 만들기
    - cd ~
    - mkdir ssl
    - cd ssl
    - 9999일 짜리 인증서 만듦. (sudo 빼샘. 나중에 읽을 때 퍼미션 에러뜸)
      - openssl req -x509 -nodes -days 9999 -newkey rsa:2048 -keyout "cert.key" -out "cert.pem" -batch
  - 환경설정 파일에 아래 추가
    - c.NotebookApp.certfile = u'/home/ubuntu/ssl/cert.pem'
    - c.NotebookApp.keyfile = u'/home/ubuntu/ssl/cert.key'
  - jupyter 다시 실행하면 https로 실행 가능해짐

7. 서비스로 만들기 (systemctl)
  - jupyter-notebook 실행 파일 위치 확인
    - which jupyter-notebook
    - 다음과 같이 뜸. /usr/local/bin/jupyter-notebook
  - sudo vi /etc/systemd/system/jupyter.service
    ```
	[Unit]
	Description=Jupyter notebook server

	[Service]
	Type=simple
	User=ubuntu
	ExecStart=/usr/bin/sudo /usr/local/bin/jupyter-notebook --allow-root --config=/home/ubuntu/.jupyter/jupyter_notebook_
	config.py

	[Install]
	WantedBy=multi-user.target
    ```
  - sudo systemctl daemon-reload
  - sudo systemctl enable jupyter
  - sudo systemctl start jupyter
  - 이제 ec2 재부팅하더라도 서비스로 등록이 되었기 때문에 알아서 주피터가 실행됨