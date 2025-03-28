#github #githubactions #ecs #deploy #배포 #무중단 #롤링 #rolling #카나리 #canary #블루그린 #bluegreen 

현재 여러분의 GitHub Actions 워크플로우 설정(`amazon-ecs-deploy-task-definition@v1` 사용)으로는 **기본적인 롤링 배포(Rolling Deployment)** 전략이 적용됩니다. 다른 배포 전략(카나리, 블루/그린)과의 차이점을 포함해 상세히 설명드리겠습니다.

---

### 🔍 **현재 설정의 배포 전략: 롤링 배포 (Rolling Deployment)**
#### ✅ **동작 방식**
1. **점진적 교체**:
   - 새 태스크 정의 버전(`dogdef:N+1`)을 등록한 후, **기존 실행 중인 태스크를 하나씩 종료**하면서 동시에 새 버전을 시작합니다.
   - 예: 2개 태스크가 실행 중이라면, `v1` 1개 종료 → `v2` 1개 시작 → 나머지 `v1` 종료 → `v2` 추가 시작.

2. **무중단 서비스**:
   - `wait-for-service-stability: true` 설정으로 **새 태스크가 Healthy 상태가 될 때까지 기다리기** 때문에 서비스 중단이 발생하지 않습니다.

3. **자동 복구**:
   - 새 버전의 태스크가 Health Check에 실패하면 자동으로 롤백됩니다.

#### ⚙️ **관련 AWS 설정**
- ECS 서비스의 **배포 유형(Deployment Configuration)**에서 기본값인 `ECS`가 선택된 상태입니다.
- 추가 파라미터(`minimumHealthyPercent`, `maximumPercent`)로 교체 속도 제어 가능:
  ```json
  "deploymentConfiguration": {
    "deploymentCircuitBreaker": {
      "enable": true,
      "rollback": true
    },
    "maximumPercent": 200,  // 최대 2배까지 태스크 증가 허용
    "minimumHealthyPercent": 50  // 최소 50%의 건강한 태스크 유지
  }
  ```

---

### 🆚 **다른 배포 전략과 비교**
| 전략          | 현재 적용 여부 | 설명                                                                 | 필요한 추가 설정 |
|---------------|----------------|----------------------------------------------------------------------|------------------|
| **롤링**       | ✅ Yes          | 점진적 교체 (기본값)                                                 | 별도 설정 불필요 |
| **블루/그린**  | ❌ No           | 완전히 새로운 환경(v2)을 구성 후 트래픽 전환                        | CodeDeploy 연동 필요 |
| **카나리**     | ❌ No           | 일부 트래픽만 새 버전으로 라우팅 (A/B 테스트)                       | ALB + CodeDeploy |
| **재생성**     | ❌ No           | 모든 태스크를 한 번에 종료 후 새 버전 배포 (Downtime 발생)          | `"maximumPercent": 100` 설정 |

---

### 🛠️ **다른 전략을 사용하려면?**
#### 1. **블루/그린 배포 (권장 for Production)**
   - **필요 조건**: AWS CodeDeploy + ALB 설정
   - **워크플로우 변경**:
     ```yaml
     - name: Deploy via CodeDeploy
       uses: aws-actions/amazon-ecs-deploy-task-definition@v1
       with:
         task-definition: task-definition.json
         service: ${{ secrets.ECS_SERVICE }}
         cluster: ${{ secrets.ECS_CLUSTER }}
         codedeploy-appspec: appspec.yaml  # CodeDeploy 설정 파일
     ```

#### 2. **카나리 배포**
   - **필요 조건**: ALB 리스너 규칙 수정 (가중치 기반 라우팅)
   - **예시 ALB 설정**:
     ```json
     "loadBalancers": [
       {
         "targetGroupArn": "arn:aws:elasticloadbalancing:...",
         "containerName": "my-app",
         "containerPort": 8080
       }
     ]
     ```

---

### 💡 **최적화 팁**
1. **롤링 배포 속도 조절**:
   ```yaml
   - name: Deploy to ECS Fargate
     uses: aws-actions/amazon-ecs-deploy-task-definition@v1
     with:
       task-definition: task-definition.json
       service: ${{ secrets.ECS_SERVICE }}
       cluster: ${{ secrets.ECS_CLUSTER }}
       wait-for-service-stability: true
       deployment-configuration: "minimumHealthyPercent=75,maximumPercent=150"
   ```

2. **배포 실패 시 자동 롤백**:
   ```json
   // task-definition.json
   {
     "deploymentConfiguration": {
       "deploymentCircuitBreaker": {
         "enable": true,
         "rollback": true
       }
     }
   }
   ```

---

### 📌 **결론**
- **현재 설정은 롤링 배포**가 기본이며, 무중단 배포에 적합합니다.  
- **블루/그린이나 카나리가 필요하면 CodeDeploy 연동이 필수**입니다.  
- `deployment-configuration` 파라미터로 롤링 속도를 세밀하게 제어할 수 있습니다.  

배포 전략 선택은 트래픽 규모와 안정성 요구사항에 따라 결정하시면 됩니다! 😊