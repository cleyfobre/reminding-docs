#aws #키움 #키움증권 

### AWS Windows 인스턴스 선택

아래 링크를 보면 윈도우에 최적화된 인스턴스 타입을 알 수 있다.

| **인스턴스 크기**          | **vCPU** | **메모리(GiB)** | **인스턴스 스토리지(GB)** | **네트워크 대역폭(Gbps)** | **EBS 대역폭(Gbps)** |
| -------------------- | -------- | ------------ | ----------------- | ------------------ | ----------------- |
| r6i.large(메모리 최적화)   | 2        | 16           | EBS 전용            | 최대 12.5            | 최대 10             |
| c6i.2xlarge(컴퓨트 최적화) | 8        | 16           | EBS 전용            | 최대 12.5            | 최대 10             |

https://aws.amazon.com/ko/blogs/tech/top-10-recommendations-to-optimize-your-windows-server-workloads-on-aws/

### 세팅 순서

### 1. OS 세팅

1. 언어팩 및 시간대 세팅(한국어, 한국시간 세팅시 여러번 리부팅이 필요)
	1. 언어팩
	2. 지역
	3. 시간대
	4. 지역 설정 >> 시스탬 로캘 변경 (beta 어쩌구 체크하지 말기! No No!!)
	5. 언어 >> 키보드 설정 >> 고급 키보드 설정 >> 한국어

### 2. OpenAPI 세팅 및 테스트

3. 키움 증권 
	1. 로그인
	2. 인증서 파일 로컬로 옮기기 (이메일 등으로)
	3. 사이트에서 인증서 가져오기 (로컬에 인증서 설치됨)
	4. 인증서 로그인 테스트
	5. OpenAPI, KOAStudio 다운로드
	6. KOA 로그인 테스트
4. 수집 서버
	1. git clone (or 귀찮으면 download)
	2. vscode (python 팩 플러그인 설치)
	3. conda 설치 및 가상환경 세팅
		1. [[키움 API 사용법 및 자동매매App 코드 리뷰]]
			1. 구버전 말고 최신버전 설치 방법 따라하기
			2. windows build tools 설치 필요할수도 있으니 참고
	4. 코드 수정
		1. 글로벌 파라미터 (로컬 경로)
		2. email_pw 넣기
		3. 레디스 접속 정보(redisQ, alive_manager)
			1. memorydb가 기본 클러스터라 복수의 키를 하나의 execute 명령어로 날리는 코드부분이 에러가 난다. 그래서 elasticache 쪽을 쓰는데, 내가 알기론 elaticache도 cluster로 알고잇는데... 왜 다른지 이건 함 알아보아야 한다.
			2. 에러명은 이러했다. `CROSSSLOT Keys in request dont hash to the same slot`
			3. redis-cache.dayup.co.kr 로 하면 됨

### 3. Redis 연동 테스트

2. 레디스 데스크탑 클라이언트 설치 (https://github.com/qishibo/AnotherRedisDesktopManager)
	1. 굳이 cli로 제대로 하겠다고, wsl 설치부터하지말고 그냥 데스크탑앱 받고 테스트하자.
