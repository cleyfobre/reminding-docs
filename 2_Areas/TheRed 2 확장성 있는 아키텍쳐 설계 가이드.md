#fastcampus #mau #msa #architecture #data #thered #디자인패턴 #architecture 

> [!note]
> 이 문서는 [[TheRed 1 대규모 MAU를 지탱하는 서비스 설계 가이드]] 문서에서 참조된다.

#### 확장성
웹 서버는 확장이 용이하다. 이유는 Stateless 하기 때문이다. 콜센터로 예를 들면, 누가 전화를 받던 상관이 없다. 웹 서버도 마찬가지다. 데이터베이스는 확장이 어렵다. 이것은 공유 자원이며, 데이터 관계는 복잡성을 가지고 있다. 그래서 확장이 어렵다.

데이터베이스는 보통 샤딩을 통해 분산 처리를 해놓는다. 액티브-스탠바이 패턴을 적용하는 것도 필수다. 웹 서버도 L4 등으로 분산 처리가 가능하다. 하지만 유튜브나 스푼라디오와 같은 스트리밍 서비스들은 분산 처리를 해도 해당 커넥션이 얼마나 유지될 지 알 수가 없기 때문에 이러한 분산 처리는 크게 의미가 없을 수 있다. 

> 즉,  ==(1) 문제 정의, (2) 그를 해결하는 알고리즘 개발 그리고 (3) 확장성 있는 디자인==을 통해
> 장애와 성능 문제를 최소화 할 수 있다.

#### 대규모 서비스에서 알고리즘 사례

> 내 주변 객실을 검색하라

유클리드 거리 계산을 이용한다. 나의 위치가 p이고, 임의의 객실 위치가 q라고 한다면, 나와 객실들과의 거리들을 구하여 가까운 것을 가려낸다. 피타고라스의 정리와 같다. 

| 수식 | 표 |
| -------- | -------- |
| $$d(p,q) = \sqrt[]{(x_2-x_1)^2+(y_2-y_1)^2}$$ | ![[Pasted image 20231208103314.png]] |

그리고 객실을 구하는 샘플 코드가 아래와 같다면, 시간복잡도는 **O(NlogN)** 이 된다.

- 코드 :
```java
int[][] point = {{3, 3}, {5, -1}, {2, 4}};
Map map = new HashMap<Integer, Integer>();

for(int i = 0; i < point.length; i++) {
    map.put(i, (int) (Math.pow(point[i][0], 2) + Math.pow(point[i][1], 2)));
}

map.entrySet()
   .stream()
   .sorted(Map.Entry.<Integer, Integer>comparingByValue())
   .forEach(System.out::println);
```

- 시간 복잡도 : 전체 코드의 시간 복잡도는 주로 `.sorted()` 메소드에 의해 결정되며, 이는 **O(N log N)** 이 된다. 나머지 부분은 O(N) 또는 그보다 낮기 때문에, 전체 코드의 시간 복잡도는 O(N log N) 이라고 할 수 있다. 이와 관련해서는 [[Mergesort와 Quicksort 정리하기]] 를 참조한다.

- 구면 코사인 법칙 : 참고할 것. 해당 기능은 아래의 쿼리로도 가능하다. 이 쿼리 역시 시간 복잡도는 O(N log N) 이다. 그리고 DB를 사용했을 때 N 만큼 데이터베이스에 부하가 몰리게 된다.

```mysql
SELECT 
	id, 
	(3959 * acos(cos(radians(37)) * cos(radians(lat))
	* cos(radians(long) - radians(-122)) + sin(radians(37)) * sin(radians(lat)))) AS distance
FROM myTable
HAVING distance < 10
ORDER BY distance;
```

> [!danger] 인덱스를 걸면 어떨까?
> 해당 쿼리에서 호출하는 컬럼들에게 인덱스를 걸어도 상관없다. 어찌됐건 전체 데이터를 스캔해서 메모리 상에 올려야 하기 때문에 인덱스와는 상관없다.

#todo 
그래서 MySQL에는 없지만 공간 색인(Spatial Indexing)을 지원하는 DBMS도 있으니 찾아보자. Quad-Tree나 R-Tree같은 공간 색인에 대해서도 알아보자. (나중에)

#### 동시에 같은 테이블에 접근하는 문제
웹 서버는 분산 처리가 용이하나, 데이터베이스는 그렇지 않다. 여러 서비스로부터 같은 데이터에 대한 접근이 있을 수 있다. 누가 먼저 조회를 하거나, 누가 먼저 수정을 하거나 말이다. 

> [!tip] Lock, Mutex and Semaphore
> #todo 
> 공유 자원에 대한 동시 접근 제어 처리에 대한 지식이 있어야 해결이 가능하다. 아래는 분산 환경에서의 락 관리에 대한 논문이다. 
> 
> [Google: The Chubby lock service for loosely - coupled distributed systems (2014)](https://static.googleusercontent.com/media/research.google.com/ko//archive/chubby-osdi06.pdf)
> 
> 아래처럼 lock를 걸어두면 두 서비스간의 Race Condition을 피할 수 있다.
> 
> ![[Pasted image 20231208124355.png]]

위와 같은 문제는 단순 비즈니스 로직에서도 발생할 수 있는 문제다. 즉 간단한 기능 요구사항을 하나 구현하더라도, 알고리즘을 잘 알고 모르고에 따라서 구현의 성숙도, 품질, 생산성이 크게 달라질 수 있다. 

이처럼 서버 단에서 확장성있는 설계에 대해 알아 봤다. 다음은 코드 단에서 확장성있는 설계에 대해 알아보자. [[TheRed 3 확장성 있는 코드 가이드]]
