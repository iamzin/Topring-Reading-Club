# 오브젝트와 의존관계

## 해당 챕터를 읽으면서 느낀 점
- 스프링이 지향하는 바에 대해서 이해하는 시간
- 내가 느끼고 알고 있던 스프링을 조금 더 구체적이고 명확하게 알게 되는 시간
- 스프링을 더 알고 싶다고 느낌

## 다른 사람에게 전달하고 싶은 정보
- 저의 소스코드 의존성 관리는 Gradle을 이용
- Spring은 5.3.22 version을 이용
  - 책에서 제공한 의존 관계
    - com.springsource.net.sf.cglib-2.2.0
    - com.springsource.org.apache.commons.logging-1.1.1
    - org.springframework.asm-3.0.7.RELEASE
    - org.springframework.beans-3.0.7.RELEASE
    - org.springframework.context-3.0.7.RELEASE
    - org.springframework.core-3.0.7.RELEASE
    - org.springframework.expression-3.0.7.RELEASE
  - 실제로 내가 작성한 의존 관계([build.gradle](https://github.com/dojinyou/Tobys-Spring-Reading/blob/main/ObjectAndDependency/build.gradle))
    - org.springframework:spring-beans:5.3.22
    - org.springframework:spring-context:5.3.22
    - org.springframework:spring-core:5.3.22
    - org.springframework:spring-expression:5.3.22
  -  변경 사항  
      - 3.0.7 -> 5.3.22로 버전 업
      - 삭제
        - com.springsource.net.sf.cglib-2.2.0
          - CGLib는 **클래스 기반으로 바이트코드를 조작하여 프록시를 생성**하는 방식이다.  ↔ JDK Dynamic Proxy는 인터페이스 기반  
          - Spring Core에 포함
        - com.springsource.org.apache.commons.logging-1.1.1
          - Srping Core에 포함 (Spring-jcl(jakarta common logging)) 
      - org.springframework.asm-3.0.7.RELEASE
        - ObjectWeb의 [ASM](http://asm.ow2.org/) 라이브러리는 클래스 바이트코드 조작과 분석을 위한 라이브러리이다. 스프링은 빈 스캐너에서나 메소드의 변수 이름을 읽는 등의 용도로 이를 사용한다. ASM의 장점은 클래스를 로딩하지 않고도 클래스의 메타 정보를 읽을 수 있다는 점이라고 한다. ([https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=bestdriver94&logNo=205113048](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=bestdriver94&logNo=205113048))
        - Spring 3.2.1 version에서 Spring-core에 포함됨.

- 예제 코드 실행을 위해서 Docker를 이용하여 DB(Mysql 8.0)를 사용
  - [관련 레포지토리 폴더](https://github.com/dojinyou/Tobys-Spring-Reading/tree/main/docker)
  - [docker-compose 작성 참고자료(by jason)](https://github.com/next-step/ddd-legacy/blob/master/docker/docker-compose.yml)
  - 관련 의존성 추가
    ```
    ./build.gradle

    implementation 'mysql:mysql-connector-java:8.0.30'
    ```
  - 관련 추가 변경 사항
    1. hosturl에 timezon 명시 : Mysql 8.0부터는 Server의 Timezon을 명시적으로 작성해주어야함.
        ```java
          // database(scheme) 이름은 topring으로 사용하였음.
          private static final String HOST_URI = "jdbc:mysql://localhost:33306/topring?useUnicode=true&serverTimezone=Asia/Seoul&useSSL=false";
          private static final String DB_USER_NAME = "root";
          private static final String DB_USER_PASSWORD = "mysql";
        ```
    2. Driver Class가 기존 예제와 다름  
        `com.mysql.jdbc.Driver` -> `com.mysql.cj.jdbc.Driver`

## 다른 사람들과 나누고 싶은 이야깃거리
- 자원 반납에 대한 관리의 책임  
  초기 statement를 실행하는 코드에서 `Connenction, PreparedStatement, ResultSet`의 자원을 반납(`resource.close()`)하는 코드가 있고 이에 대한 책임도 명시 됨.
  - 해당 문제는 java의 문법인 [try-resource](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html) 문법을 사용하여 개선  
  이 방법은 어떤 지?
  다른 방법이 있다면 어떤 게 있을 지?  
  또는 Try-resource의 단점이 있다면 어떤 점이 있는 지?
- 생성자 주입과 메소드 주입  
  책에서는 메소드 주입을 의존성 주입의 주요 방법으로 설명하고 있음.  
  스프링팀은 Spring 4.0부터 생성자 주입을 권장  
  왜 그때는 메소드 주입이 더 좋다고 생각했을 까?

- 자바의 싱글톤 패턴 구현과 스프링의 싱글톤 패턴 구현은 어떻게 다른 가?
  - 안티패턴이라고 불리는 싱글톤은 어떨 때는 사용해도 괜찮을 까?