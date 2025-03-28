#github #githubactions #ecs 

### DeepSeek에게 질문했던 내용

.github/workflows/{파일명}.yml 에 아래와 같은 step이 있어.

```yaml
- name: Deploy to ECS Fargate
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: task-definition.json
    service: ${{ secrets.ECS_SERVICE }} 
    cluster: ${{ secrets.ECS_CLUSTER }} 
    wait-for-service-stability: true
```

이 step이 실질적으로 어떤 명령어를 수행하는지 궁금해

---

 **`aws-actions/amazon-ecs-deploy-task-definition@v1` 액션**은 내부적으로 다음과 같은 AWS CLI 명령어들을 조합하여 실행합니다.  
(실제로는 더 복잡한 로직이 포함되어 있지만, 핵심 동작을 단순화해 설명드리겠습니다.)

---

### 🔨 **내부적으로 수행되는 주요 명령어**
1. **태스크 정의 등록 (필수)**  
   ```bash
   aws ecs register-task-definition \
     --cli-input-json file://task-definition.json
   ```
   - `task-definition.json` 파일을 읽어 **새 버전의 태스크 정의를 등록**합니다.  
   - *이 단계에서 `family`가 같아도 무조건 새 버전이 생성됩니다.*

2. **서비스 업데이트 (필수)**  
   ```bash
   aws ecs update-service \
     --cluster ${{ secrets.ECS_CLUSTER }} \
     --service ${{ secrets.ECS_SERVICE }} \
     --task-definition <새로_등록된_태스크_정의_ARN> \
     --force-new-deployment
   ```
   - 새로 등록된 태스크 정의의 ARN을 자동으로 찾아 서비스에 적용합니다.

3. **안정화 대기 (옵션)**  
   ```bash
   aws ecs wait services-stable \
     --cluster ${{ secrets.ECS_CLUSTER }} \
     --services ${{ secrets.ECS_SERVICE }}
   ```
   - `wait-for-service-stability: true`일 때만 실행됩니다.  
   - **새 태스크가 Running 상태가 되고, 로드 밸런서 health check가 통과할 때까지 대기**합니다.

---

### ⚠️ **주의사항: 매번 새 버전이 생성되는 이유**
- **`register-task-definition`은 항상 새 버전을 생성**하므로, `task-definition.json` 내용이 **완전히 동일해도** 버전 번호가 증가합니다.  
- *예시:*  
  - 1차 배포 → `dogdef:1`  
  - 2차 배포 (변경 없음) → `dogdef:2`  
  - 3차 배포 (변경 없음) → `dogdef:3`

---

### 💡 **불필요한 버전 생성을 막는 방법**
#### 1. **이미지 변경 시에만 배포 (권장)**  
   ```yaml
   - name: Check if image changed
     id: check-image
     run: |
       CURRENT_IMAGE=$(aws ecs describe-task-definition \
         --task-definition ${{ secrets.ECS_SERVICE }} \
         --query 'taskDefinition.containerDefinitions[0].image' \
         --output text)
       NEW_IMAGE="${{ secrets.ECR_REPOSITORY }}:${{ github.sha }}"
       if [ "$CURRENT_IMAGE" == "$NEW_IMAGE" ]; then
         echo "skip_deploy=true" >> $GITHUB_OUTPUT
       fi

   - name: Deploy to ECS Fargate
     if: steps.check-image.outputs.skip_deploy != 'true'
     uses: aws-actions/amazon-ecs-deploy-task-definition@v1
     with:
       task-definition: task-definition.json
       service: ${{ secrets.ECS_SERVICE }}
       cluster: ${{ secrets.ECS_CLUSTER }}
   ```

#### 2. **수동으로 오래된 버전 삭제**  
   ```bash
   # 최신 5개를 제외한 모든 버전 삭제
   aws ecs list-task-definitions \
     --family-prefix your-family \
     --query 'taskDefinitionArns[5:]' \
     --output text | xargs -n1 aws ecs deregister-task-definition
   ```

---

### 📌 **결론**
- **`amazon-ecs-deploy-task-definition` 액션은 내부적으로 `register-task-definition` + `update-service`를 실행**합니다.  
- **이미지 태그가 바뀌지 않아도 무조건 새 버전이 생성되므로**, 위의 방법으로 최적화하는 것이 좋습니다.  
- 단, **로드 밸런서 설정, 환경 변수 등 다른 변경 사항이 있을 때는 반드시 새 버전이 필요**하므로 주의하세요!  

도움이 되셨다면 👍 해주세요! 😊