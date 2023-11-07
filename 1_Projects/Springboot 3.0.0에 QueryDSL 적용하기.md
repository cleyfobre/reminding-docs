#springboot #querydsl

Springboot 2.X에서 QueryDSL을 적용하는 방법과 조금 차이가 있다. 3.X일 경우 아래와 같이 적용하기로 한다.
```sh
dependencies {
	// querydsl
	implementation 'com.querydsl:querydsl-jpa:5.0.0:jakarta'
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

