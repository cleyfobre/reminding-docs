#swift #xcode

### 프로젝트 GrowTodo
- 소개: 투두리스트를 완료함으로써 새싹을 키우는 앱이다. 하루에 3개까지 투두리스트를 적을 수 있고, 완료하면 Done 처리를 한다. Done 수가 많아질수록 나의 새싹은 자란다.
- 린스타트업: '안녕하세요!'만 보여주는 단순 Mac용 앱!

### 앱스토어에서 Xcode 설치
Apple Developer 에서 계정 생성은 안했다. 테스트용인데 매년 12만원도 아깝고, 앱 스토어에 배포하지는 않을 것이기 때문에 일단 Apple Developer 계정 없이 진행한다.

### Xcode 실행
- 새 프로젝트를 생성
- 'App'을 선택한다.
```
  App (선택해야 할 것):
  일반적인 Mac 애플리케이션
  독립적으로 실행되는 앱
  웹에서 다운로드 배포에 가장 적합
  여러분이 원하는 일반적인 Mac 앱
  
  Document App:
  특정 문서 형식을 다루는 앱 (예: 텍스트 에디터, 이미지 뷰어)
  파일 열기/저장이 주요 기능인 앱
  복잡한 문서 관리 기능이 필요할 때
  
  Safari Extension App:
  Safari 브라우저 확장 프로그램
  웹 브라우징을 도와주는 도구
  일반 Mac 앱이 아님
```

- 프로젝트 이름은 growtodo 로 정한다.
- Testing System은 현재 그대로 유지 (현재: Swift Testing with XCTest UI Tests)
  - 초보자에게 가장 적합한 기본 설정
  - 단위 테스트와 UI 테스트 모두 지원
  - Apple이 공식 지원하는 테스팅 프레임워크
  - 나중에 필요하면 테스트 코드 작성 가능
- Storage도 None (나중에 AWS 등의 클라우드를 이용할 예정)
- 나의 코드
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
