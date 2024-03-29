## 코딩테스트 유형

### 그리디

- 부분적인 최적해가 전체적인 최적해가 되는 문제에 사용
- 정렬, 우선순위 큐를 활용하는 경우가 많음

### 브루트 포스(완전 탐색)

- 코딩테스트의 필수
- DFS, BFS, 백트래킹(완탐이 아니긴 함)
- 이름 그대로 모든 경우를 탐색해야 할 경우에 사용

### 다이나믹 프로그래밍

- 복잡한 문제를 한 번에가 아니라 조금씩 점차적으로 풀이하는 유형
- DP 유형
- bottom up, top down으로 풀이

### 해시 테이블

- key-value

### 스택, 큐

- 선입선출, 후입선출 관련된 문제에 사용
- 예: 스택은 DFS, 큐는 BFS

### 힙

- 우선 순위를 고려하는 문제에 사용(큰 수 부터, 작은 수 부터)
- java의 경우 PriorityQueue를 사용(그렇다고 힙 = PQ는 아님)

### 구현

- 주어진 문제를 알고리즘을 통해 코드로 바꾸는 것

### 이분 탐색

- 순차적인 배열 탐색으로는 시간 초과가 나는 문제에 사용됨
- 시간복잡도: O(logN)로 그냥 순회하는 O(N)보다 빠르다.
- 이분 탐색 + 다익스트라 등 다른 유형과 연계되는 문제가 출제될 수 있다.

### 트리

- DFS를 활용한 전/중/후위 순회, 루트노드, 서브트리, 높이, 너비, 그래프에서 트리의 수 찾기 등 다양하게 출제할 수 있다.

### 그래프

- 최단 경로를 구하는 문제가 많이 출제된다.
- 최단 경로를 구할 때는 다익스트라, 벨만-포드, BFS, 플로이드-워셜 알고리즘들을 사용하자.
- 그래프 순회에는 DFS / BFS를 사용하자.
- 추가로 학습하면 좋은 알고리즘으로는 위상정렬이 있다.

### 투 포인터, 슬라이팅 윈도우

- 배열에서 인덱스 2개를 사용해야 할때 평범하게 for 문 2번으로 풀이하면 시간 초과가 발생하는 경우 생각해볼 수 있다.
- 인덱스 2개를 while 문 한번으로 사용하면서 문제를 해결할 수 있다.
- 구간 합도 공부하자.

### 유니온 파인드

- 노드를 집합에 속하게 하는 Union 연산과 노드의 루트 노드를 찾는 Find 연산으로 이루어진다.
- 노드들을 루트 노드를 기준으로 하는 어떠한 집합으로 묶는다고 생각하면 이해하기 편하다.

### 최소 신장 트리

- 크루스칼, 프림 알고리즘을 공부하자.

### 비트마스킹

- AND, OR, XOR, NOT, Shift