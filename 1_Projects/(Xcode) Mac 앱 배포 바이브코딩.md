#swift #xcode

### 프로젝트 GrowTodo
- 소개: 투두리스트를 완료함으로써 새싹을 키우는 앱이다. 하루에 3개까지 투두리스트를 적을 수 있고, 완료하면 Done 처리를 한다. Done 수가 많아질수록 나의 새싹은 자란다.
- 린스타트업: '안녕하세요!'만 보여주는 단순 Mac용 앱!

### 앱스토어에서 Xcode 설치
Apple Developer 에서 계정 생성은 안했다. 테스트용인데 매년 12만원도 아깝고, 앱 스토어에 배포하지는 않을 것이기 때문에 일단 Apple Developer 계정 없이 진행한다.

### Xcode 실행 및 개발
- 새 프로젝트를 생성
- 'App'을 선택한다.
```yaml
App (선택해야 할 것):
  - 일반적인 Mac 애플리케이션
  - 독립적으로 실행되는 앱
  - 웹에서 다운로드 배포에 가장 적합
  - 여러분이 원하는 일반적인 Mac 앱
  
Document App:
  - 특정 문서 형식을 다루는 앱 (ex. 텍스트 에디터, 이미지 뷰어)
  - 파일 열기/저장이 주요 기능인 앱
  - 복잡한 문서 관리 기능이 필요할 때
  
Safari Extension App:
  - Safari 브라우저 확장 프로그램
  - 웹 브라우징을 도와주는 도구
  - 일반 Mac 앱이 아님
```

- 프로젝트 이름은 growtodo 로 정한다.
- Team 은 None 이다.
- Testing System은 현재 그대로 유지 (현재: Swift Testing with XCTest UI Tests)
  - 초보자에게 가장 적합한 기본 설정
  - 단위 테스트와 UI 테스트 모두 지원
  - Apple이 공식 지원하는 테스팅 프레임워크
  - 나중에 필요하면 테스트 코드 작성 가능
- Storage도 None (나중에 AWS 등의 클라우드를 이용할 예정)

### 나의 코드
```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            Image(systemName: "globe")
                .imageScale(.large)
                .foregroundStyle(.tint)
            Text("안녕하세요!")
                .font(.title2)
                .foregroundColor(.secondary)
        }
        .frame(minWidth: 300, minHeight: 200)
                .background(Color(.windowBackgroundColor))
        .padding()
    }
}

#Preview {
    ContentView()
}
```

### 빌드 및 배포
- Xcode에서 할 수 있으나, Apple Developer 코드 서명이 필요하기에 CLI로 빌드를 진행한다.
- 참고로 Xcode에서는 다음과 같이 진행한다.
  - 상단 탭에서 'Product' -> 안전하게 Clean Build Folder... -> Archive
  - <img width="1400" height="853" alt="image" src="https://github.com/user-attachments/assets/0f7f709a-0609-4da7-9411-d1005e26a851" />
  - 위 이미지가 나오면 Direct Distibution을 선택한다. App Store 없이 직접 배포할꺼고, Web에서 다운로드 링크 제공할 것이기 때문이다. 그리고 Apple Developer 계정없이도 가능하다.
  - 혹시라도 team 을 세팅했었다면 다음 이미지를 참고하여 team 설정을 제거한다. (Automatically manage signing)
  - <img width="3038" height="1138" alt="image" src="https://github.com/user-attachments/assets/3f880123-ad1c-490c-8743-7fd0dddca543" />
  - 하지만 이렇게 해도 아래와 같은 에러가 나오면서 안될 것이다. 그러니 CLI로 진행한다.
  - 'No Team Found in Archive. Use the Signing & Capabilities editor to assign a team to the targets and build a new archive.'
- CLI 배포는 다음과 같이 진행한다.
- Xcode가 아닌 CLI를 사용하면 xcodebuild 명령어가 안될 것이니, 다음을 실행한다.
```shell
# 터미널은 연다.

# 현재 설정 확인
xcode-select -p

# xcode.app의 경로 확인하기.
ls -la /Applications/ | grep -i xcode

# 정식 설치라면 이 경로를 사용하면 된다. /Applications/Xcode.app/Contents/Developer

# Xcode로 변경 (비밀번호 입력 필요)
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer

# 설정 확인
xcode-select -p

```
- 프로젝트 상단으로 이동한다. ls 명령어를 쓰면 다음과 같이 나올 것이다.
  - growtodo		growtodo.xcodeproj	growtodoTests		growtodoUITests
- 현재 directory 에서 다음 명령어를 실행한다.
```shell
# 빌드
xcodebuild -project growtodo.xcodeproj -scheme growtodo -configuration Release -derivedDataPath ./build clean build

# 빌드 완료 후 생성된 앱 확인
ls ./build/Build/Products/Release/

# growtodo.app 파일이 생성됨

# Release 폴더로 이동
cd ./build/Build/Products/Release/

# 앱을 zip으로 압축
zip -r growtodo.zip growtodo.app

# 압축 파일 확인
ls -la growtodo.zip
```
