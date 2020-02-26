---
title: Spring MVC, Spring Boot
categories : [Spring, SpringBoot]
tags: [Spring, Web, BackEnd,DesignPattern]
toc: true
toc_sticky: true
toc_label: "목차"
---


MVC(Model-View-Controller)란?
--

**Model**
- 클라이언트가 원하는 정보를 제공하는 역할을 한다.
- VO, DTO, JAVA BEAN 등을 Model이라고 한다.


**View**
- 클라이언트에게 화면을 출력한다.
- html,css 등을 View라고 한다.

**Controller**
- 클라이언트로부터 받은 요청들을 처리한다.
- Servlet과 같은걸 Controller라고 한다.



### Model1

![model1](/assets/img/back_end/2020_01_26/model1.png)

>장점

- 구조가 굉장히 단순하다.

>단점

- View와 Controller가 혼재되어 코드가 굉장히 복잡해진다.
- 코드가 복잡하여 유지보수가 어렵다.
- JSP에서 프론트엔드와 백엔드의 구분이 모호하다.
- 이러한 단점을 보완하기 위해 Model2가 나왔다.

<hr>


### Model2

![model2](/assets/img/back_end/2020_01_26/model2.png)

>장점

- View와 Controller가 분리가 되어 코드가 복잡하지가 않다.
- 코드가 분리되므로 프론트, 백엔드 구분이 확실하다.
- 유지보수가 용이하다.

>단점

- 구조가 복잡하여 높은 기술 스택이 요구된다.


### Model1 vs Model2

- 모델2가 모델1의 후속 모델이긴 하지만 그렇다고 무조건 모델2가 좋은건 아니다.

- **Model1**
  - 규모가 작은 프로젝트
  - 업데이트가 적어 유지보수가 굳이 필요없는 프로젝트
  - 이러한 프로젝트일 경우 굳이 모델2를 사용하여 복잡한 구조를 사용할 필요가없다.

- **Model2**
  - 규모가 큰 프로젝트
  - 업데이트를 자주해줘서 유지보수가 필요한 프로젝트


- 상황에 맞는 모델을 사용하여 개발을 하자.





<br><br>


Spring MVC
--

![springmvc](/assets/img/back_end/2020_01_26/springmvc.png)

- Spring의 MVC는 대략적으로 위와 같이 흘러간다.
- 기본적으로 Model2와 비슷한 구조다.

<br><br>


Spring Boot
--

- Spring boot와 Spring Framework는 서로 다른게 **아니다**.
- Spring을 사용하면서 해줘야하는 설정들을 Spring boot에서는 매우 쉽게 가능하다.
- 서버를 내장하고 있다.
  - Tomcat을 추가로 설정할 필요가 없다.
- 프로젝트 설정할때 XML 코드가 필요 없다.
- Spring MVC를 사용할때 설정해줘야할 (web.xml, rootcontext.xml) 등등의 파일들을 SpringBoot 에선 (application.yml or application.properties) 이 파일 하나에 전부다 관리할 수 있다.
- Spring Boot 는 /src/main/resources/ 디렉터리를 기본 **classpath**로 가지고 있다.



<br><br>


**개인적인 의견으로 Spring MVC로 진행하다 Spring Boot로 넘어오면 너무 편하다. 하지만 그렇다고 Spring Boot를 처음부터 하면 왜 편한지 이해가 안갈수도 있다. 그러므로 Spring MVC로 충분히 Spring 설정등을 익히고 Spring Boot로 넘어가서 공부하는것을 추천한다.**



출처
--

[https://www.edwith.org/boostcourse-web/lecture/16762/](https://www.edwith.org/boostcourse-web/lecture/16762/)

[https://epthffh.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81Spring-MVC-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%ACModel-View-Controller-Framework](https://epthffh.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81Spring-MVC-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%ACModel-View-Controller-Framework)

[https://medium.com/@jang.wangsu/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-mvc-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-1d74fac6e256](https://medium.com/@jang.wangsu/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-mvc-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-1d74fac6e256)

[https://hsp1116.tistory.com/9](https://hsp1116.tistory.com/9)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
