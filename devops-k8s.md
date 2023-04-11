## k8s

### 쿠버네티스, v1.20부터 도커 지원 중단
- 도커는 이제 쓸모가 없어진 것일까?
  - 먼저 쿠버네티스 런타임에서 도커를 제거하더라도 도커에서 만든 컨테이너 이미지를 등록하고 실행하는 것은 가능합니다. 도커가 생성하는 이미지는 도커에만 특정된 이미지가 아닌 OCI(Open Container Initiative)와 호환되는 이미지이기 때문이죠. 
- 도커를 대신할 컨테이너 런타임은 무엇이 있을까? 
  - containerd: 도커 자체적으로도 containerd를 사용하지만, 이 런타임은 따로 도커심과 같은 중단 매체가 필요 없습니다.
  - CRI-O: 이를 사용하면 훨씬 가볍게 컨테이너 실행이 가능합니다. 
  - 다만, 도커가 제공하는 컨테이너 생성 및 이미지 빌드 기능은 제공하지 않아요. 그래서 도커 대신에 이미지를 빌드할 수 있는 툴이 필요한데, 대표적으로는 kaniko, buildash, Podman, Skpeo 등이 있습니다.

### 미니큐브
- 실제 k8s 클러스터를 구축 테스트를 하는 것은 많은 부담이 된다. 어렵고, AWS EKS를 쓰자니 비용도 많이 들고, 가상머신이든 물리머신이든 여러대가 필요하다. 그래서 로컬에서 간단히 테스트할 수 있는 오픈소스가 있다. 그것이 바로 미니큐브다.

### 데모 프로젝트: test-k8s
- Write 4 files
  - ConfigMap: 몽고db 엔드포인트
  - Secret: 몽고db useranme, password
  - Deployment & Service: 몽고db 앱 with internal service
  - Deployment & Service: webapp with external service

### test-k8s steps
- ConfigMap: from
42:56 https://www.youtube.com/watch?v=s_o8dwzRlu4