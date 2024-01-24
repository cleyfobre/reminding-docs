#windows #윈도우 #batch

키움 OpenAPI를 활용한 어플리케이션을 자동으로 켜고 끄는 배치를 만드려고 한다. 장이 종료되면 앱을 종료하고, 장이 시작되기 전에 앱을 실행시키는 것이다.

앱을 실행시키는 파일이 있는 디렉토리로 가서 아래와 같은 bat 파일을 만든다. bat 파일 이름은 **kiwoom_auto_start.bat** 이라고 한다.

```bat
@ECHO ON
title Kiwoom Auto Start

cd C:\Users\pc\Documents\workspace\stockinfo\kiwoom

call activate myenv
python __init__.py

cmd.exe
```

참고로 나의 예제에서 디렉토리는 **C:\Users\pc\Documents\workspace\stockinfo\kiwoom**이며,
32bit python과 pyqt5가 설치된 가상환경의 이름은 **myenv**이다.

**여기까지 어플리케이션 코드와 bat 파일이 완성된 상태다.** 다음은 Windows 레지스트리에 등록하는 단계다.

#### 1) '작업 스케줄러'를 실행한다.

![[jobscheduler.png]]

#### 2) 오른쪽 탭에서 '작업 만들기'를 선택한다.

![[Pasted image 20240124154346.png]]

#### 3) 다음 탭마다 설정한다. 일반, 트리거, 동작, 설정

![[setting.png]]

