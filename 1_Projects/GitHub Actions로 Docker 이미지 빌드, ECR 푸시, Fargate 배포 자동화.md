#github #githubactions #action

**✅ 가능합니다!** GitHub Actions를 사용하면 **CI/CD 파이프라인을 완전 자동화**할 수 있습니다.  
아래는 **전체 흐름**과 **필요한 단계**를 설명드립니다.  

---

## **📌 전체 아키텍처**  
1. **코드 푸시** → GitHub Actions 트리거  
2. **Docker 이미지 빌드** → Amazon ECR에 푸시  
3. **ECS Fargate 서비스 업데이트** (새 이미지로 배포)  

---

## **🚀 3단계로 구현하기**  
### **1. 사전 준비**  
#### **(1) AWS 리소스 설정**  
- **ECR 리포지토리 생성** (예: `my-app`)  
- **ECS Fargate 클러스터 & 서비스 생성**  
- **IAM 역할 생성** (GitHub Actions가 AWS에 접근할 권한 부여)  

#### **(2) GitHub Secrets 등록**  
리포지토리 설정 → `Settings > Secrets and variables > Actions`에서 다음 정보 추가:  
- `AWS_ACCESS_KEY_ID`: AWS IAM 사용자 Access Key  
- `AWS_SECRET_ACCESS_KEY`: AWS IAM 사용자 Secret Key  
- `AWS_REGION`: 리전 (예: `ap-northeast-2`)  
- `ECR_REPOSITORY`: ECR URI (예: `123456789012.dkr.ecr.ap-northeast-2.amazonaws.com/my-app`)  
- `ECS_CLUSTER`: ECS 클러스터 이름 (예: `my-cluster`)  
- `ECS_SERVICE`: ECS 서비스 이름 (예: `my-service`)  

---

### **2. GitHub Actions 워크플로우 파일 (`.github/workflows/deploy.yml`)**  
```yaml
name: Build and Deploy to ECS

on:
  push:
    branches: [ "main" ]  # main 브랜치 푸시 시 실행

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build, tag, and push image to Amazon ECR
        env:
          ECR_REGISTRY: ${{ secrets.ECR_REPOSITORY }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY:$IMAGE_TAG .
          docker push $ECR_REGISTRY:$IMAGE_TAG

      - name: Deploy to ECS Fargate
        uses: aws-actions/amazon-ecs-deploy-task-definition@v1
        with:
          task-definition: task-definition.json  # ECS 태스크 정의 파일 경로
          service: ${{ secrets.ECS_SERVICE }}
          cluster: ${{ secrets.ECS_CLUSTER }}
          wait-for-service-stability: true
```

---

### **3. ECS 태스크 정의 파일 (`task-definition.json`)**  
```json
{
  "family": "my-app",
  "networkMode": "awsvpc",
  "executionRoleArn": "arn:aws:iam::123456789012:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "name": "my-app",
      "image": "123456789012.dkr.ecr.ap-northeast-2.amazonaws.com/my-app:latest",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp"
        }
      ],
      "essential": true
    }
  ],
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512"
}
```

---

## **🔍 주요 단계 설명**  
1. **AWS 인증 구성**: GitHub Actions가 AWS 리소스에 접근할 수 있도록 IAM 키로 인증.  
2. **ECR 로그인**: Docker 클라이언트가 ECR에 이미지를 푸시할 수 있도록 로그인.  
3. **이미지 빌드 & 푸시**: Dockerfile 기반으로 이미지 빌드 후 ECR에 푸시.  
4. **ECS 배포**: 새 이미지로 ECS 서비스 업데이트 (롤링 배포).  

---

## **💡 추가 팁**  
- **태스크 정의 동적 업데이트**:  
  `amazon-ecs-deploy-task-definition` 액션은 **기존 태스크 정의를 자동으로 업데이트**합니다.  
- **배포 검증**:  
  `wait-for-service-stability: true`로 설정하면 배포 성공 여부를 확인합니다.  
- **Rollback 설정**:  
  ECS 서비스에서 **배포 실패 시 자동 롤백** 옵션을 활성화하세요.  

> **📜 공식 문서**:  
> - [GitHub Actions + ECR](https://docs.github.com/en/actions/deployment/deploying-to-your-cloud-provider/deploying-to-amazon-elastic-container-service)  
> - [ECS 배포 액션](https://github.com/aws-actions/amazon-ecs-deploy-task-definition)  

이제 **코드 푸시 → 자동 배포**가 가능해집니다! 🚀