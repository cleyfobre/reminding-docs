#gradle #bootrun #intellij #cicd #devops 


Intellij에서 **Run**버튼으로 실행하는 Application run configurations와 gradle bootRun의 Task는 애플리케이션을 실행하는 것이다. 주로 스프링부트 애플리케이션에서 주로 사용되는 기능이다. 이 두 기능은 비슷하나 몇가지 차이점이 있다.

#### Application Run Configuration in IntelliJ
- Intellij는 ==Run Configurations==를 제공한다. 에디터 안에서 애플리케이션을 설정하고 실행하는 기능이다.
- 이 기능은 Intellij의 특화된 기능이지, 프로젝트 자체의 빌드시스템(Gradle or Maven)의 일부분이 아니다.
- 커스텀하게 설정 파라미터를 넘길 수도 있다. (environment variables, program arguments, working directory, and more)
- UX적으로 편하게 실행과 디버깅이 가능하다. 

#### "gradle bootRun" Task
- gradle bootRun은 Springboot gradle plugin에서 제공되는 gradle task의 일부분이다. 프로젝트의 빌드 시스템의 일부분이며 build.gradle과 같은 스크립트에서 정의된다.
- Intellij 안의 Gradle 탭이나 커맨드라인에서 사용 가능하다.
- 스크립트를 통한 자동화, 지속적통합, CLI 사용 등이 특징이다. 보통 배포할 때 명령어를 스크립트로 작성해놓고 사용하기 때문에 CI/CD 파이프라인 구축에 많이 사용된다.