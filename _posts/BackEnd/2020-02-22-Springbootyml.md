---
title: Spring Boot Yml(Properties)파일 응용하기
categories : [Spring,SpringBoot]
tags: [Spring,SpringBoot]
toc: true
toc_sticky: true
toc_label: "목차"
---


2개 이상의 Yml 적용하기
--

- Spring boot 프로젝트를 진행하면 property 혹은 yml은 필수로 사용한다.
- 1개의 yml에 모든 정보를 담기에는 **oauth, mongodb,mysql** 등등과 같은 민감한 정보를 담는 내용들도 있다. 
- **.gitignore** 파일로 git에 올리지 않는 방법이 있는데 이때 사용하는것이 파일을 분리 하는 것이다.
- 즉 mongo.yml, oauth.yml, mysql.yml 파일을 각각 만들어 이 3개의 파일은 .gitignore를 통하여 git에 올리지 않는 것이다.

>Bad Example

```java
String APPLICATION = "spring.config.location=classpath:/application.yml";
String OAUTH = "spring.config.location=classpath:/oauth.yml";
String MONGODB = "spring.config.location=classpath:/mongo.yml";
String MYSQL = "spring.config.location=classpath:/mysql.yml";

public static void main(String[] args) {
    new SpringApplicationBuilder(RecruitJogbo.class)
                    .properties(APPLICATION, OAUTH, MONGODB, MYSQL)
                    .run(args);
}
```

- 이런식으로 Appplication Builder를 통하여 properties를 추가를 해주면 **에러가 발생할것 이다.**
- 이유는 SpringApplicationBuilder클래스를 살펴보면 .properties는 **마지막 값** 즉 MYSQL 만 읽을수 있다.
- 모든 yml 파일을 1개의 변수에 ","를 구분으로 작성해줘야 한다.

>Good Example

```java
String PROPERTIES = "spring.config.location=classpath:/application.yml"
+",classpath:/oauth.yml"
+",classpath:/mongo.yml"
+",classpath:/mysql.yml";


public static void main(String[] args) {
    new SpringApplicationBuilder(RecruitJogbo.class)
                    .properties(PROPERTIES)
                    .run(args);
}
```

- 이런식으로 여러개의 yml 파일들을 적용시킬 수 있다.




<br><br>


profile에 따른 yml 사용
--

- 애플리케이션을 배포할때 테스트환경, 개발환경, 실제환경 모두 다를것이다.
- 테스트환경의 yml, 개발환경의 yml 등등 각각의 yml 파일을 만들어 해당 환경에 yml을 적용하면 매우 번거롭다.
- Spring 프레임워크에선 profile 기능을 갖고 있어 1개의 yml파일로 각각의 환경을 구분지을 수 있다.


```java
spring:
  profiles:
    active: dev

---

spring:
  profiles:dev

server:
  port: 8080

---

spring:
  profiles:test

server:
  port: 8081
```

- "---"를 기준으로 서로 구분을 한다.
- active 가 dev로 되어있으므로 해당 서버는 8080포트에서 작동된다.
- 만약 test 환경을 적용해보고 싶다면 active: test로 변경하면 된다.




<br><br>


출처
--

[https://velog.io/@hellozin/Spring-Boot%EC%97%90%EC%84%9C-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%98-Property-Yml%EC%9D%84-%EC%A0%81%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-xdjz1kkmbr](https://velog.io/@hellozin/Spring-Boot%EC%97%90%EC%84%9C-%EC%97%AC%EB%9F%AC%EA%B0%9C%EC%9D%98-Property-Yml%EC%9D%84-%EC%A0%81%EC%9A%A9%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-xdjz1kkmbr)

[https://effectivesquid.tistory.com/entry/Spring-boot-profile-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0](https://effectivesquid.tistory.com/entry/Spring-boot-profile-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
