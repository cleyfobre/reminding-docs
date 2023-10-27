## ssh

### ssh 연결이 안될 때

- 이유를 모를 때 체크해볼 사항
  - 접속하려는 서버에서 아래 명령문으로 sshd 로그 확인해볼 것
  - 왜 ssh 연결을 거절했는지 알려준다.
	```
	grep 'sshd' /var/log/auth.log
	```
- 다음과 같은 에러 때문에 ssh 연결이 거절되었다.
  ```
  userauth_pubkey: key type ssh-rsa not in PubkeyAcceptedAlgorithms [preauth]
  ```
  - 아래와 같은 방법으로 일단 해결은 되었으나...
    - vi /etc/ssh/sshd_config 해서 아래 문구 추가
    ```
    PubkeyAcceptedAlgorithms +ssh-rsa
    ```
    - sshd 재시작 sudo systemctl restart sshd
    - 궁극적인 해결 방법은 아니다. 

- 결국 SSH v8.8 이후로 RSA 시그니처를 지원하지 않는다. (보안문제로...)
  - 참고: https://osg.kr/archives/718
  - ssh-keygen를 이용해 키를 ecdsa로 만들면 해결된다.
    ```
    ssh-keygen -t ecdsa -b 521 -m PEM
    ```

### DB SSH Tunneling (dbeaver 기준)

- 기본 db 접속 정보 입력 (host, port, username, password)
- tunneling ec2 ssh 연결
  - 호스트 ip, port(22), username 입력
  - ppk 인증서 불러오기