#array #arraylist #linkedlist #queue #stack #deque #cs #java 

### **1. 기본 구조**

|**특징**|**Array**|**ArrayList**|**LinkedList**|
|---|---|---|---|
|**구조**|고정 크기의 배열|크기가 동적으로 조정되는 배열|노드(Node)로 연결된 구조|
|**메모리 저장 방식**|연속된 메모리 블록|연속된 메모리 블록|비연속적으로 분산된 메모리|
|**동적 크기 지원**|X (고정 크기)|O (자동으로 크기 조정)|O (동적 크기)|

---

### **2. 성능 비교**

|**작업 유형**|**Array**|**ArrayList**|**LinkedList**|
|---|---|---|---|
|**임의 접근**|O(1)|O(1)|O(n) (순차 탐색 필요)|
|**삽입/삭제 (중간)**|O(n) (요소 이동 필요)|O(n) (요소 이동 필요)|O(1) (노드 참조를 알고 있는 경우)|
|**삽입/삭제 (끝)**|X (불가능)|O(1) (평균적으로 빠름)|O(1)|
|**탐색 (검색)**|O(n) (선형 탐색)|O(n)|O(n)|
|**메모리 사용량**|낮음|낮음|높음 (노드의 참조값 포함)|

---

### **3. 장단점**

#### **Array**

- **장점**:
    - 빠른 임의 접근 (O(1)O(1)).
    - 메모리 효율적 (추가 오버헤드 없음).
- **단점**:
    - 크기가 고정됨.
    - 중간 삽입/삭제가 비효율적 (O(n)O(n)).

#### **ArrayList**

- **장점**:
    - 동적으로 크기 조정 가능.
    - 배열처럼 빠른 임의 접근 (O(1)O(1)).
    - 컬렉션 프레임워크의 다양한 메서드 지원.
- **단점**:
    - 중간 삽입/삭제 시 요소 이동 필요 (O(n)O(n)).
    - 쓰레드 안전하지 않음 (명시적 동기화 필요).

#### **LinkedList**

- **장점**:
    - 삽입/삭제가 빠름 (O(1)O(1), 노드 참조를 알고 있을 때).
    - 크기 제한이 없음.
    - 양방향 연결 리스트로 구현되어 순차적 데이터 처리에 유리.
- **단점**:
    - 임의 접근 속도가 느림 (O(n)O(n)).
    - 메모리 사용량이 높음 (노드의 `next`와 `prev` 참조값 포함).

---

### **4. 적합한 사용 사례**

|**작업 유형**|**적합한 자료구조**|**이유**|
|---|---|---|
|데이터가 고정 크기이고, 빠른 접근이 필요|**Array**|메모리 효율적, 임의 접근이 빠름.|
|동적 크기가 필요하고, 읽기/쓰기 작업 위주|**ArrayList**|크기 조정이 가능하며, 배열처럼 접근 속도가 빠름.|
|삽입/삭제 작업이 자주 발생|**LinkedList**|삽입/삭제가 빠름. 중간 위치에서도 효율적.|
|순차적으로 데이터를 처리해야 함|**LinkedList**|데이터 순서가 중요하고, 삽입/삭제가 많을 때 유리.|

---

### **결론**

- **Array**: 고정 크기, 빠른 임의 접근 필요할 때.
- **ArrayList**: 동적 크기, 읽기/쓰기 위주 작업일 때.
- **LinkedList**: 삽입/삭제가 많거나 순차 데이터 처리가 중요할 때.