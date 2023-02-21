## github, gradle and jenkins build

### Install
- apt-get update
- apt-get install
  - openjdk-11-jdk
  - sudo
  - wget
  - 등등 필요한거 설치
- sudo wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
- echo deb http://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
- apt-get update
- apt-get install jenkins
- service jenkins start
- service jenkins status

### Configuration
- password: cat /var/lib/jenkins/secrets/initialAdminPassword
- 추천 다운로드
- 유저 정보 입력
- item 등록
- Freestyle project 선택
- 이하 상세 사항 입력
  - git repository 주소
  - git source 주소(~.git)
  - gradle mapper 설정
- 지금 빌드


### Check
- jar 파일 존재: /var/lib/jenkins/workspace/{jenkins item 명}/build/libs

### ref
- https://abbo.tistory.com/184
- https://abbo.tistory.com/185
- https://velog.io/@qudalsrnt3x/Jenkins-%EC%A0%A0%ED%82%A8%EC%8A%A4%EC%99%80-Github%EC%9D%84-%ED%86%B5%ED%95%9C-%EB%B9%8C%EB%93%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95