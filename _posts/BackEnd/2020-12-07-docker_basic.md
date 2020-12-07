---
title: \[도커\] 기본 개념
categories : [도커,docker]
tags: [도커,docker]
toc: true
toc_sticky: true
toc_label: "목차"
---

도커
-
![docker](/assets/img/back_end/2020_12_07/docker.png)

- 도커는 컨테이너 기반의 오픈소스 **가상화** 플랫폼이다.
- 맥, 윈도우, 리눅스에서 사용이 가능하다. 즉 os에 크게 구애를 받지 않는다.
- 실행환경 혹은 어플리케이션을 컨테이너로 배포함으로써 관리를 단순하게 해준다.
  - 즉 Mongo, MySql, Spring, Express 등등을 컨테이너로 추상화가 가능하다.


### 컨테이너?

![container](/assets/img/back_end/2020_12_07/container.png)

- 컨테이너는 격리된 공간에서 어플리케이션이 동작하도록 하는 기술이다.
- 컨테이너를 격리시켜 **독립적으로** 동작시키기 위한 기술이다.
- 가상화 기술의 하나다.

![docker-vs-vm](/assets/img/back_end/2020_12_07/docker-vm.png)

- 기존의 가상환경은 hypervisor 위에 Guest OS 위에 애플리케이션을 동작시킨다.
  - 즉 운영체제를 하나 더 실행시키므로 사용법이 간단하지만 **무겁고** **느리다**.
- 도커의 컨테이너 기술은 도커 엔진위에 바로 애플리케이션을 실행시킨다.
  - 즉 단순히 **프로세스를 격리** 하는 방식이다.
  - 가볍고 빠르게 동작한다.
  - CPU, Memory 등의 자원은 프로세스가 필요한 만큼만 추가로 사용해서 기존 가상 환경과는 달리 퍼포먼스 이슈가 **거의 없다**.
  - 컨테이너를 사용함으로써 서로 다른 컨테이너끼리 영향을 미치지 않고 독립적으로 실행이 된다.
    - 단 도커 네트워크를 통하여 서로 통신은 가능하다. (추후 포스팅!)
- 컨테이너에 **이미지**를 담아서 실행을 한다. 



### 이미지?

![docker_images](/assets/img/back_end/2020_12_07/images.png)

- 간단하게 생각하면 컨테이너에서 실행이 될 애플리케이션들 이다.
- mysql 이 하나의 이미지가 될 수 있고, spring boot, ubuntu 도 이미지가 될 수 있다.
- ubuntu에 mysql, spring boot 를 설치하여 커스텀 이미지를 만들수도 있다.
- 이렇게 만든 이미지를 컨테이너에 넣고 구동시키면 하나의 프로세스가 독립적으로 실행된다.


![docker_images](/assets/img/back_end/2020_12_07/docker-image.png)

- 도커는 이미지를 생성할때 굉장히 단순한 방법을 사용한다.
- 이미지를 수정하면 기존 이미지에서 새로운 파일을 추가하는 방법으로 이미지를 저장한다.
- 이미지를 만드는 방법은 다음 포스팅에서 기본 명령어와 같이 포스팅을 하겠다.

### why docker?
여기서부턴 지극히 개인적인 경험을 바탕으로 작성을 해보겠다.

- config 파일 혹은 yml 파일 어디에다가 저장했었지..?🤔 와 같은 고민은 🖐
- 추후 물리적 서버를 바꿔야하는데 설정 파일들이 이리저리 흩어져 있고 수십개의 어플리케이션들이 돌아가고 있다면..?
  - 도커를 사용한다면 docker hub에 이미지를 넣는다. -> 이전할 서버에서 pull 해서 컨테이너에 태운다 -> 고민끝!
- 개발환경 통일
  - 실제로 개발을 하다보면 개발자들끼리 서로 다른 설정, 서로 다른 환경 때문에 여기에선 분명 되는데 왜 안되지? 와 같은 상황이 가끔 발생한다.
  - 하지만 도커를 사용한다면 해결!
- 여러개의 어플리케이션 관리 편리
  - redis, mongo, mysql, spring boot 등등 수십개의 어플리케이션들이 구동하고 있다고 가정을 해보자
  - 수십개의 어플리케이션들을 일일이 서로 다른 명령어들로 설정하고 실행하고 (물론 systemctl 혹은 brew 등이 있지만) 하는 것은 굉장히 복잡한 작업이다.
  - 하지만 도커를 사용한다면 컨테이너로 관리하기 때문에 관리가 매우매우 편리하다.


<br><br>




출처
-

[https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)

[https://www.docker.com/resources/what-container](https://www.docker.com/resources/what-container)]

[https://cultivo-hy.github.io/docker/image/usage/2019/03/14/Docker%EC%A0%95%EB%A6%AC/](https://cultivo-hy.github.io/docker/image/usage/2019/03/14/Docker%EC%A0%95%EB%A6%AC/)

<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
