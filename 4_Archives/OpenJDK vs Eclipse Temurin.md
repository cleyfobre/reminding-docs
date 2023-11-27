#openjdk #eclipsetemurin #jdk #opensource

#### OpenJDK
자바 플랫폼의 공식 레퍼런스이며, 여러 JDK의 기초가 되는 오픈소스이다. OpenJDK 커뮤니티와 Oracle, Red Hat과 같은 조직에서 관리된다. 

#### Eclipse Temurin
Eclipse 재단의 Adoptium 프로젝트 팀에서 제공하는 OpenJDK의 빌드이다.

#### 둘의 차이
더 범용적인 JDK를 사용하느냐, Eclipse 관리 하에 있는 JDK를 사용하느냐의 차이다. 다만 Eclipse는 엄격한 테스트 및 품질 보증 프로세스에 중점을 둔다고 하며 기업에서 안정적으로 개발을 할 수 있도록 지원한다고 한다. 

#### JRE(Java Runtime Environment)의 부재
OpenJDK 11 이후 더이상 Eclipse Temurin에서 JRE가 제공되지 않는다고 한다. 그래서 커스텀으로 런타임 환경을 세팅해서 사용하기도 한다. 그 예는 아래와 같다. 

```Dockerfile
# Example of custom Java runtime using jlink in a multi-stage container build
FROM eclipse-temurin:11 as jre-build

# Create a custom Java runtime
RUN $JAVA_HOME/bin/jlink \
         --add-modules java.base \
         --strip-debug \
         --no-man-pages \
         --no-header-files \
         --compress=2 \
         --output /javaruntime

# Define your base image
FROM debian:buster-slim
ENV JAVA_HOME=/opt/java/openjdk
ENV PATH "${JAVA_HOME}/bin:${PATH}"
COPY --from=jre-build /javaruntime $JAVA_HOME

# Continue with your application deployment
RUN mkdir /opt/app
COPY japp.jar /opt/app
CMD ["java", "-jar", "/opt/app/japp.jar"]
```

솔직히 굳이 이렇게까지 써가며 Temurin을 써야 하나 싶다. JDK가 설치되면 알아서 PATH를 찾아 나의 코드를 실행해주는게 더 좋은거 아닌가. OpenJDK 레퍼런스를 써도 불편한 적이 없었는데 말이다. 그래도 몰라서 안쓰는 경우일 수도 있으니 정리 해본다.