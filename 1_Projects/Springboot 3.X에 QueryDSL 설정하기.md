#springboot #querydsl #buildgradle #gradle

Springboot 2.X에서 QueryDSL을 적용하는 방법과 조금 차이가 있다. 3.X일 경우 아래와 같이 적용하기로 한다.

```sh
dependencies {
	// querydsl
	implementation "com.querydsl:querydsl-jpa:5.0.0:jakarta"
	annotationProcessor "com.querydsl:querydsl-apt:5.0.0:jakarta"
	annotationProcessor "jakarta.annotation:jakarta.annotation-api"
	annotationProcessor "jakarta.persistence:jakarta.persistence-api"
}

def querydslDir = "$buildDir/generated/querydsl"

sourceSets {
	main.java.srcDirs += [ querydslDir ]
}
  
tasks.withType(JavaCompile) {
	options.generatedSourceOutputDirectory = file(querydslDir)
}

clean.doLast {
	file(querydslDir).deleteDir()
}
```


> [!warning] 자세한 설명은 아래부터 참고하자
 
#### dependencies

Springboot 3.X인 경우 맨 뒤에 jakarta를 붙이고, 2.X인 경우에는 jpa를 사용한다.

```sh
# 3.X
implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
# 2.X
implementation 'com.querydsl:querydsl-jpa:5.0.0:jpa'
```

아래와 같은 코드를 넣으면 컴파일 단계에서 쿼리에 필요한 파일들을 만들 수 있다.

```sh
annotationProcessor "com.querydsl:querydsl-apt:5.0.0:jakarta"
annotationProcessor "jakarta.annotation:jakarta.annotation-api"
annotationProcessor "jakarta.persistence:jakarta.persistence-api"
```

컴파일 단계에서 만들어지는 파일들의 경로를 사용자가 설정한다. 취향에 따라 "src/main/generated"와 같이 소스 폴더 예하에 둘 수도 있다.

```sh
# 일반적으로 build 폴더 밑에 파일을 두도록 설정하지만,
def querydslDir = "$buildDir/generated/querydsl"  
# 취향에 따라 소스 폴더 밑에 둘 수도 있다.
def querydslDir = "src/main/generated"  
```

실제 컴파일 단계에서 만든 파일을 위에서 설정한 폴더에 넣는 작업이다.

```sh
tasks.withType(JavaCompile) {  
	options.generatedSourceOutputDirectory = file(querydslDir)  
}
```