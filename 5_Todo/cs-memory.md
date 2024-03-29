## memory (feat. Java)

### 개요
- webapp에서 mysql로 커넥션 맺을 때, 커넥션도 메모리 상에 존재하는 object이기 때문에, 갑자기 궁금해졌다. 메모리에 대해서.

### object
- 객체라고 하는 것이다. heap으로부터 메모리의 블럭을 할당 받아 존재하는 것을 말한다. 프로그램 상에서 더이상 다른 객체에 의해 참조(referenced)되지 않으면 사라진다. 이 때 가비지컬렉터가 움직인다.

### heap
- JVM에 의해 관리되는 메모리 영역이다. 객체에 동적으로 메모리를 할당하기 위해 존재한다. 객체가 생성되면 heap에서 특정 메모리 블럭을 해당 객체에 allocate한다. 
- java 앱 실행할 때, -Xmx 옵션으로 heap 크기를 정해놓고 실행 가능 하다.
	- 근데 만약 이 옵션을 따로 설정을 안하면, 물리 메모리의 1/4이 디폴트로 잡힌다. 이것도 서버 세팅을 어떻게 하냐에 따라 다르지만 보통 1/4이고, 주로 heap 사이즈를 적절하게 세팅 해놓는 것이 안전하다. 

### garbage collection
- JVM이 하는 역할 중 하나다. 사용되지 않는 객체가 있으면 걔가 점유하고 있는 메모리를 자유롭게 해준다. 즉 객체를 삭제하고 사용가능한 메모리를 확보하게 한다.

### stack
- 스택도 메모리 영역 중 하나다. heap과 다른 점은 heap이 객체나 배열 등을 저장하는 데 사용되는 것에 반해, stack은 method calls와 지역변수 등을 저장하는 데 사용된다.
	- 스택은 LIFO다. 그래서 메소드 호출 정보를 저장한다. 메소드 특징이 메소드 안에 메소드를 호출하는 구조이다보니, 먼저 실행된 메소드는 가장 늦게 종료가 되어야 한다.
	- 스택은 제한된 사이즈의 메모리이고, 스택이 꽉 차면 스택오버플로우 에러를 일으키기 때문에 주의깊게 사용해야 한다. 과도한 재귀함수나 너무 깊은 메소드 설계는 주의해야 한다.


### 물리적 영역으로서 heap과 stack
- 스택은 CPU의 stack pointer register에서 관리되는 영역이다. 어떤 프로그램이 스택에 데이터를 푸시하면 스택포인터가 감소하면서 공간이 줄어들고, 반대로 데이터가 팝업되면 스택 포인터가 증가하면서 공간이 늘어난다.
- 힙은 OS의 memory allocation routines에 의해 관리되는 영역이다. 스택과는 달리 비교적 유연하게 메모리를 할당하고 제거하는 데이터 구조를 갖고 있다.
- 스택이든 힙이든 장치 수준에서 해당 영역의 성능을 향상시키는 작업들도 존재한다. 일부 프로세서는 '스택캐시'나 '스택버퍼'같은 스택 전용 하드웨어 지원을 제공하기도 하고, MMU(메모리 관리 장치)에서 메모리 보호 및 가상 메모리 관리 기능으로 힙의 성능을 높일 수도 있다.

