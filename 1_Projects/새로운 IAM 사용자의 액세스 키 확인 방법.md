#iam #accesskey
 
**AdministratorAccess** 권한을 가진 IAM 사용자로 로그인한 후, **액세스 키(Access Key ID/Secret Key)**는 다음과 같이 확인할 수 있습니다.  

---

### **📌 액세스 키 확인 단계**  
#### **1. AWS 콘솔에서 직접 확인**  
1. **IAM 콘솔** 접속:  
   - [AWS IAM 콘솔](https://console.aws.amazon.com/iam/) → **"사용자"** 선택.  
2. 대상 사용자 클릭:  
   - 생성한 IAM 사용자 이름 (예: `github-actions-user`) 선택.  
3. **"보안 자격 증명"** 탭 이동:  
   - **"액세스 키"** 섹션에서 기존 키 확인 또는 새 키 생성.  

#### **2. 새 액세스 키 생성 (기존 키 없을 경우)**  
1. **"액세스 키 만들기"** 버튼 클릭.  
2. **"애플리케이션 코드"** 또는 **"CLI"** 용도 선택 → **"생성"**.  
3. **액세스 키 ID**와 **비밀 액세스 키** 표시:  
   - **⚠️ 이 단계에서만 Secret Key 확인 가능** → CSV 다운로드 또는 안전한 곳에 복사.  

---

### **🔍 주의사항**  
- **Secret Key는 최초 생성 시에만 확인 가능**합니다. 이후에는 재생성해야 합니다.  
- **키 분실 시**: 기존 키 삭제 후 새로 발급.  
- **보안**:  
  - 키는 **GitHub Secrets**이나 **비밀 관리 도구**에 저장.  
  - 절대 코드/공개 채널에 노출 금지.  

---

### **💡 AWS CLI로 확인 (옵션)**  
```bash
aws iam create-access-key --user-name <IAM_USER_NAME>
```
- 출력 예:  
  ```json
  {
    "AccessKey": {
      "UserName": "github-actions-user",
      "AccessKeyId": "AKIAXXXXXXXXXXXXXXXX",
      "Status": "Active",
      "SecretAccessKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYXXXXXXX",
      "CreateDate": "2024-06-12T00:00:00Z"
    }
  }
  ```

---

### **📌 액세스 키 관리 팁**  
1. **키 로테이션**: 90일마다 재발급.  
2. **불필요한 키 비활성화**:  
   ```bash
   aws iam update-access-key --access-key-id AKIAXXXXXX --status Inactive --user-name <IAM_USER_NAME>
   ```
3. **최소 권한 부여**: `AdministratorAccess` 대신 필요한 권한만 할당.  

> 🔐 **중요**: 액세스 키는 **AWS 리소스의 문**과 같습니다. **절대 외부에 유출되지 않도록** 주의하세요!  


### **🔒 보안 강화를 위한 추천 설정**  
- **액세스 키 사용 제한**:  
  - IAM 정책에서 `"Condition": { "IpAddress": { "aws:SourceIp": ["GitHub Actions IP 범위"] } }` 추가.  
  - [GitHub Actions IP 목록](https://api.github.com/meta) 참고.  
- **정기적 키 로테이션**: 90일마다 액세스 키 재발급.  