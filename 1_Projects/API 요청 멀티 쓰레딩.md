#multithreading #multithread #멀티쓰레드 #멀티스레드

---
### 내 App이 현재 사용하고 있는 쓰레드 갯수?

#### 1. GUI로 확인하기

jdk가 설치되어 있고, 앱에서 properties 설정 파일에 jmx에 관한 false 처리를 따로 하지 않았다면, 아래 명령어가 정상적으로 실행될 것이다.

```
/jconsole
```

jconsole을 실행하면 내가 보고자 하는 App를 선택할 수 있고, 그것이 사용중인 리소스의 현황을 볼 수 있다.

#### 2. 코드로 확인하기

```java
ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean(); 
int threadCount = threadMXBean.getThreadCount(); System.out.println("Total threads: " + threadCount);
```

#### 3. Whatap에서 확인하기

인스턴스 성능 관리(대메뉴)에서 스레드 목록/덤프(소메뉴)로 들어가면 볼 수 있다.