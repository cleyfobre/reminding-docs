#redis #singlethread

보통 레디스는 싱글 스레드로 알고 있으나, redis 서버에서 top을 찍으면 아래와 같이 여러 프로세스를 볼 수 있다.

```sh
PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
498884 redis 20 0 18.9g 6.3g 2176 D 61.1 20.4 0:15.63 redis-server  
672 redis 20 0 18.9g 6.3g 8832 S 2.0 20.4 354:23.77 redis-server
```

그리고 아래는 chatGPT-4 의 결과다.

```
Redis는 기본적으로 single-threaded 구조를 가지고 있습니다. 이는 주요 작업들은 하나의 쓰레드에서 처리된다는 의미입니다. 하지만, 이는 데이터 처리 작업에 한정된 이야기이며, Redis는 비동기 I/O, 복제(replication), 백업(dump) 등의 작업을 위해 내부적으로 추가적인 쓰레드를 생성하고 사용하곤 합니다.

예를 들어, Redis는 저장된 데이터를 디스크에 백업하는 'SAVE' 명령을 수행할 때, 이를 비동기적으로 처리하기 위해 별도의 쓰레드를 생성합니다. 이 쓰레드는 백업 작업을 처리하며, 이 때문에 'top' 명령어를 통해 여러 개의 Redis 프로세스가 동시에 실행되는 것을 확인하실 수 있습니다.

또한, Redis 4.0 이후 버전에서는 'io-threads' 옵션을 통해 Redis가 처리하는 I/O 작업을 여러 쓰레드에서 병렬로 처리하도록 설정할 수 있습니다. 이는 Redis 서버가 멀티 코어 CPU를 보다 효율적으로 활용할 수 있게 해줍니다.

따라서, 여러분이 확인하신 추가적인 Redis 프로세스는 이러한 Redis의 내부적인 동작 때문에 발생한 것으로 생각됩니다. Redis의 주요 데이터 처리 작업은 여전히 single-threaded 방식으로 처리되지만, 특정 작업들은 멀티 쓰레드를 통해 병렬 처리됩니다.
```
