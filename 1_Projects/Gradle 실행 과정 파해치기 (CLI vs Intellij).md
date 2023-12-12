#gradle #intellij #gradlew #build 

gradle clean이나 build를 할 때, 안될 때가 있다. intellij의 gradle 탭에서는 잘 되는데 terminal에서 하면 안되는 경우다. intellij에서 안될 때에는 보통 두 가지 케이스로 해결이 된다. 

1. Settings의 Gradle 탭에서 Gradle JVM을 로컬에 설치된 적절한 JDK와 연결해주거나
2. File -> Project Settings 에서 적절한 JDK를 연결해주면 된다.

그러나 이러한 설정이 CLI에서의 동작을 보장하지 않는다. StackOverFlow에 이와 관련된 답변이 있다.

---

IntelliJ IDEA uses the _Gradle Wrapper_ or the _Gradle distributed location_ that you specify in your IntelliJ IDEA settings, but when you are using the terminal window, it uses your native operating system command line, so if you don't have Gradle configured in your `PATH` environment variable, it will just not work because it doesn't know what `gradle` is.

Add `gradle` to your path in:

- Windows
	- In File Explorer right-click on the This PC (or Computer) icon, then click Properties -> Advanced System Settings -> Environmental Variables.
	- Under System Variables select Path, then click Edit.
	- Add an entry for C:\\Gradle\\gradle-3.5\\bin. Click OK to save.
- Linux / Mac
```sh
export PATH=$PATH:/opt/gradle/gradle-3.5/bin
```
---
