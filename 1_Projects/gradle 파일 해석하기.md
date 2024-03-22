#gradle #groovy

밑에 gradle 파일 예제에 주석을 달아놨다. 친절한 설명과 함께. 보고 참고 하면서 주석이 달리지 않은 부분은 혼자 해석해보길.

```groovy

// 이 프로젝트를 빌드하는 데에 필요한 플러그인이나 디펜던시 등을 어디서 찾을지 써놓음.
buildscript {
    repositories {
        mavenLocal()
        mavenCentral()
        gradlePluginPortal()
        maven { url "https://repo.spring.io/plugins-release" }
    }
    dependencies {
    }
}

// gradle task들의 묶음 (build, test 등등)
plugins {
    id "java"  
	id "maven-publish"  
	id "idea"
	...
}

// 이 프로젝트의 기본 정보 입력
group = "kr.co.test.gateway"
version = "0.0.1-SNAPSHOT"
description = ""

// 컴파일 때 사용하는 jdk 버전
sourceCompatibility = 11

// 프로그램에서 실행할 수 있는 가장 낮은 jdk 버전
targetCompatibility = 11

// 시스템 프로퍼티를 특정 값으로 assertion하고 싶을 때 아래와 같이 한다.
assert System.properties["java.specification.version"] == "1.8" || "11" || "12" || "13" || "14"
// 만약 위와 같은 자바의 프로퍼티를 모두 확인하고 싶으면 이렇게 아래와 같은 명령어를 치면 된다.
// java -XshowSettings:properties -version

// 다른 그래들 파일을 여기에 실행시킴.
apply from: "gradle/docker.gradle"
apply from: "gradle/sonar.gradle"

// 그래들 빌드할 때 이렇게 한다 ./gradlew -Pdev clean bootJar
// 이때 -P가 프로퍼티 넣는 문법이다. 아래 if문은 이것에 따른 분기처리다.
// 참고로 gae는 Google App Engine을 말한다.
if (project.hasProperty("prod") || project.hasProperty("gae")) {
    apply from: "gradle/profile_prod.gradle"
} else if (project.hasProperty("stage")) {
    apply from: "gradle/profile_stage.gradle"
} else if (project.hasProperty("dev")) {
    apply from: "gradle/profile_dev.gradle"
} else {
    apply from: "gradle/profile_local.gradle"
}

if (project.hasProperty("war")) {
    apply from: "gradle/war.gradle"
}

if (project.hasProperty("gae")) {
    apply plugin: 'maven'
    apply plugin: 'org.springframework.boot.experimental.thin-launcher'
    apply plugin: 'io.spring.dependency-management'

    dependencyManagement {
        imports {
            mavenBom "io.github.jhipster:jhipster-dependencies:${jhipster_dependencies_version}"
        }
    }    appengineStage.dependsOn thinResolve
}

if (project.hasProperty("zipkin")) {
    apply from: "gradle/zipkin.gradle"
}

// Intellij IDEA에서 node_modules 디렉터리가 프로젝트 일부로 처리되지 않도록 함.
idea {
    module {
        excludeDirs += files("node_modules")
    }
}

// Eclipse에서 아래 경로의 파일들을 인식할 수 있도록 함.
eclipse {
    sourceSets {
        main {
            java {
                srcDirs += ["build/generated/sources/annotationProcessor/java/main"]
            }
        }
    }
}

// gradle 실행될 때 아무 task를 명시하지 않으면 아래와 같이 bootRun이 실행 됨.
defaultTasks "bootRun"

// 메인 클래스를 명시해줌.
springBoot {
    mainClassName = "kr.co.test.gateway.GatewayApp"
}

test {
    useJUnitPlatform()
    exclude "**/*IT*", "**/*IntTest*"
    testLogging {
        events 'FAILED', 'SKIPPED'
    }
    // uncomment if the tests reports are not generated  
    // see https://github.com/jhipster/generator-jhipster/pull/2771 and https://github.com/jhipster/generator-jhipster/pull/4484    
    // ignoreFailures true
      
    reports.html.enabled = false
}

task integrationTest(type: Test) {
    useJUnitPlatform()
    description = "Execute integration tests."
    group = "verification"
    include "**/*IT*", "**/*IntTest*"

    testLogging {
        events 'FAILED', 'SKIPPED'
    }

    if (project.hasProperty('testcontainers')) {
        environment 'spring.profiles.active', 'testcontainers'
    }
    
    // uncomment if the tests reports are not generated  
    // see https://github.com/jhipster/generator-jhipster/pull/2771 and https://github.com/jhipster/generator-jhipster/pull/4484    
    // ignoreFailures true
    
    reports.html.enabled = false
}

check.dependsOn integrationTest
task testReport(type: TestReport) {
    destinationDir = file("$buildDir/reports/tests")
    reportOn test
}
  
task integrationTestReport(type: TestReport) {  
    destinationDir = file("$buildDir/reports/tests")  
    reportOn integrationTest  
}  
  
if (!project.hasProperty("runList")) {  
    project.ext.runList = "main"  
}  
  
project.ext.diffChangelogFile = "src/main/resources/config/liquibase/changelog/" + new Date().format("yyyyMMddHHmmss") + "_changelog.xml"  
  
liquibase {  
    activities {  
        main {  
            driver "com.mysql.cj.jdbc.Driver"  
            url "jdbc:mysql://localhost:3306/db_gateway"  
            username "root"  
            password ""  
            changeLogFile "src/main/resources/config/liquibase/master.xml"  
            defaultSchemaName "gateway"  
            logLevel "debug"  
            classpath "src/main/resources/"  
        }  
        diffLog {  
            driver "com.mysql.cj.jdbc.Driver"  
            url "jdbc:mysql://localhost:3306/db_gateway"  
            username "root"  
            password ""  
            changeLogFile project.ext.diffChangelogFile  
            referenceUrl "hibernate:spring:kr.co.test.gateway.domain?dialect=org.hibernate.dialect.MySQL8Dialect&hibernate.physical_naming_strategy=org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy&hibernate.implicit_naming_strategy=org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy"  
            defaultSchemaName "gateway"  
            logLevel "debug"  
            classpath "$buildDir/classes/java/main"  
        }  
    }  
    runList = project.ext.runList  
}  
  
gitProperties {  
    failOnNoGitDirectory = false  
    keys = ["git.branch", "git.commit.id.abbrev", "git.commit.id.describe"]  
}  
  
checkstyle {  
    toolVersion '${checkstyle_version}'  
    configFile file("checkstyle.xml")  
    checkstyleTest.enabled = false  
}  
nohttp {  
    source.include "build.gradle", "README.md"  
}  
  
configurations {  
    providedRuntime  
    implementation.exclude module: "spring-boot-starter-tomcat"  
    all {  
        resolutionStrategy {
	        regressions:  
				force 'org.liquibase:liquibase-core:3.9.0'  
        }  
    }
}  

// 디펜던시는 아래 리포지토리들로부터 다운받는다. 
// mavenLocal은 로컬에 위치한 maven 리포지토리를 의미한다.
// mavenCentral은 maven의 기본 리포지토리다. 보통 여기서 받는다.
// jcenter는 mavenCentral의 대체재다. 더 많은 아티팩트가 있는 걸로 알려져있다.
repositories {  
    mavenLocal()  
    mavenCentral()  
    jcenter()
}  

dependencies {  
    // import JHipster dependencies BOM  
    if (!project.hasProperty("gae")) {  
        implementation platform("io.github.jhipster:jhipster-dependencies:${jhipster_dependencies_version}")  
    }  
  
	implementation(platform("org.apache.logging.log4j:log4j-bom:2.15.0"))  
    implementation group: "io.github.jhipster", name: "jhipster-framework"  
    implementation "javax.annotation:javax.annotation-api"  
    implementation "org.springframework.boot:spring-boot-starter-cache"  
    implementation "io.dropwizard.metrics:metrics-core"  
    implementation "io.micrometer:micrometer-registry-prometheus"  
    implementation "net.logstash.logback:logstash-logback-encoder"  
    implementation "org.liquibase:liquibase-core" 
    ...
}  
  
if (project.hasProperty("gae")) {  
    task createPom {  
        def basePath = 'build/resources/main/META-INF/maven'  
        doLast {  
            pom {  
                withXml(dependencyManagement.pomConfigurer)  
            }.writeTo("${basePath}/${project.group}/${project.name}/pom.xml")  
        }  
    }    bootJar.dependsOn = [createPom]  
}  
  
task cleanResources(type: Delete) {  
    delete "build/resources"  
}

wrapper {  
    gradleVersion = "6.5"  
}  
  
  
if (project.hasProperty("nodeInstall")) {  
    node {  
        version = "${node_version}"  
        npmVersion = "${npm_version}"  
        yarnVersion = "${yarn_version}"  
        download = true  
    }  
}  
compileJava.dependsOn processResources  
processResources.dependsOn bootBuildInfo
```

### Gradlew
gradlew은 스크립트다. 그리고 파일들의 모음집이다. 그래서 gradle 프로젝트를 pull받았다면, gradle을 따로 설치할 필요없이 gradlew을 사용하면 된다. 

gradlew은 해당된 프로젝트를 빌드할 때 무슨 버전의 gradle로 빌드할 것인지 명시하는 게 목적이다. 왜냐하면 이미 로컬에 gradle이 설치되어 있을 수도 있기 때문에 그것과 상관없이 프로젝트를 고정된 버전으로 빌드하기 위함이다.