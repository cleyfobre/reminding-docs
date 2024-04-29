#cpu #io #OS #cpuboundtask #cs 

CPU bound task란 CPU속도가 곧 작업속도인 작업을 말한다. 아래와 같은 작업들이 이것에 해당된다.

1. 수학 계산: 복잡한 수학문제와 알고리즘부터 단순 산술계산까지의 작업
2. 데이터 가공: Python에서 Dictionary나 Pandas의 Dataframe 가지고 데이터를 필터링할 때의 작업
3. 이미지 작업: 이미지 포맷, 사이즈 변경 등의 작업
4. 머신러닝: 음... 머신러닝

Python에서는 GIL 때문에 threading.Thread를 여러개 사용해도 CPU bound task들의 병렬처리가 힘들다. multiprocessing.Process를 사용해야 병렬로 처리할 수 있다.

근데 궁금한 점은 왜 IO Operation과 분리하는 것일까? IO Operation도 CPU의 리소스를 사용하는데 말이다. 그 이유는 다음과 같다. IO Operation은 CPU의 속도보다 파일이나 네트워크로부터 읽고 쓰는 속도가 중요한 작업이다. 데이터를 읽거나 쓸 때에는 그 작업을 기다리는 동안 CPU는 idle 상태가 된다. 마냥 기다린다.

IO의 속도를 올리려면 대표적으로 SSD나 RAM의 용량이 크면 되고, 인터넷이 빠르면 된다. 하드웨어로 콕찝어 말하면 NICs(Network Interface Cards), Router, Switch and Any other devices related to network 등이 되겠다.