#multithread #undertow #tomcat #jboss #스레드 #thread

> [!note] Undertow가 쓰는 쓰레드에 대해 알아 보기 위해 작성
> 회사 app에서 사용하는 웹서버는 톰캣이 아니라 언더토우였다. jhipster에서 자동 생성해서 쓰다보니 당연히 스프링 기본 값인 톰캣으로 되어있겠지. 라고 생각했었던게 실수였다. 하지만 쓰레드 리스트를 확인했을 때, 스레드 이름이 XNIO로 시작하는 것을 보고 톰캣이 아니구나. 라는 걸 알게되었고. 언더토우에서 쓰는 스레드에 대해 알고 싶어 정리했다.


In a Spring Boot application, the default names of threads in the pool depend on the embedded server you’re using (Tomcat, Jetty, or Undertow). Here’s how threads are typically named for each server:

### 1. **Tomcat (Default in Spring Boot)**
   - **Main Request Handling Threads**:  
     Threads are named like `http-nio-8080-exec-<number>`.
     - `http-nio-8080` indicates the HTTP connector (using NIO) on port 8080.
     - `exec-<number>` represents individual worker threads in the pool, with `<number>` incrementing as more threads are created to handle incoming requests.

   - **Example**: `http-nio-8080-exec-1`, `http-nio-8080-exec-2`, etc.

### 2. **Undertow**

XNIO is an I/O library created by JBoss (Red Hat) specifically designed to be lightweight and efficient for non-blocking I/O operations. Undertow’s worker threads and I/O threads are managed through this custom system, which has finer control over thread allocation, scheduling, and interaction with non-blocking I/O

   - **IO Threads**:  
     These threads handle I/O operations (like accepting requests) and are named `XNIO-<instance>-I/O-<number>`.
     - `<instance>` is an identifier for the XNIO instance (typically `1`).
     - `<number>` increments for each I/O thread.

   - **Worker Threads**:  
     Worker threads that handle request processing are named `XNIO-<instance> task-<number>`.
     - `task-<number>` increments as new worker threads are created to handle requests.

   - **Example**: `XNIO-1 I/O-1`, `XNIO-1 task-1`, etc.

### 3. **Jetty**
   - **Threads**:  
     Jetty threads are named `qtp<instance>-<thread-id>-<name>`.
     - `qtp<instance>` refers to the QueuedThreadPool instance.
     - `<thread-id>` is the unique ID of the thread within Jetty.
     - `<name>` is the context of the server.

   - **Example**: `qtp123456789-21-acceptor-0@<address>`, `qtp123456789-22-selector-0@<address>`, etc.

### 4. **Async Executor Threads (If @Async is Used)**
   - If you’ve configured a custom `ThreadPoolTaskExecutor` for asynchronous tasks using `@Async`, you can set a custom thread prefix.
   - For example, in the configuration:
     ```java
     executor.setThreadNamePrefix("AsyncThread-");
     ```
   - Threads will then be named `AsyncThread-<number>`, where `<number>` increments for each new async task.

### Checking Thread Names in JConsole or VisualVM
You can observe these thread names in tools like JConsole, VisualVM, or similar monitoring tools, which display the current threads running in the JVM along with their names. This is helpful for verifying the server type and configuration. 

Would you like guidance on setting custom thread names?

---
### Undertow 설정

jhipster로 프로젝트 생성시 SpringBoot 기본 WAS인 Tomcat말고 Undertow로 설정하도록 되어 있습니다.

```groovy
...
configurations {
	...
  implementation.exclude module: "spring-boot-starter-tomcat"
  ...
}
...
dependencies {
	...
	implementation "org.springframework.boot:spring-boot-starter-undertow"
	...
}
```

### 스레드

Undertow의 스레드는 두 유형으로 나뉩니다.

1. I/O 스레드: http 리퀘스트를 받는 스레드
2. 워커 스레드: 비즈니스 로직을 수행하는 스레드

### 스레드 기본값

1. I/O 스레드: 서버의 core 갯수로 세팅됨
    1. 예를 들어, t3.medium 서버에서는 2개의 vcpu를 사용하기 때문에 2개의 I/O 스레드가 기본 생성됩니다.
2. 워커 스레드: I/O 스레드의 8배까지 최댓값으로 생성됨
    1. 예를 들어, I/O 스레드가 2개면 최대 16개까지 생성됨(2 X 8)

### 스레드 라이프사이클

1. I/O 스레드: 부팅시 생성되며 App의 생명주기를 같이 합니다.
2. 워커 스레드: 부팅시 0개이며, 요청이 들어올 때 생성이 됩니다. 부하가 되면 성능을 유지하기 위해 추가적으로 생성됩니다. 설정상 최댓값까지만 생성됩니다. 생성된 스레드는 사라지지 않고, idle 상태로 남습니다.

### 기본값 대신 커스터마이징하기

```yaml
server:
  undertow:
    threads:
      io: 4
      worker: 50
```

### WAS마다 존재하는 built-in 스레드풀

- **Tomcat**: Uses `org.apache.tomcat.util.threads.ThreadPoolExecutor`, which is similar to Java’s `ThreadPoolExecutor`.
- **Undertow**: Uses its own thread management system based on XNIO with I/O threads and worker threads.
- **Jetty**: Uses `QueuedThreadPool` to manage threads.

> [!warning] WAS들은 각각의 노하우로 Thread Pool을 구현했다.
> WAS들은 Java에서 제공하는 `java.util.concurrent.ExecutorService`를 직접적으로 사용하지 않았다. 하지만 컨샙은 매우 비슷하게 자신들의 코드로 자신들만의 Thread Pool을 만들었다.
> Each web application server (WAS) typically implements its **own custom thread pool** mechanism rather than directly using `java.util.concurrent.ExecutorService`. However, most of these implementations are conceptually similar to `ExecutorService` and often inspired by `java.util.concurrent` classes, especially `ThreadPoolExecutor`.

#todo
### 쓰레드는 다다익선? 너무 많을 때의 부작용

### 컨텍스트 스위칭 비용

