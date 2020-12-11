---
title: \[도커\] 기본 명령어
categories : [도커,docker]
tags: [도커,docker]
toc: true
toc_sticky: true
toc_label: "목차"
---

# 도커
-----
![docker](/assets/img/back_end/2020_12_07/docker.png)


## 💿 도커 이미지 명령어


이미지 pull
```
docker pull [image]:[tag]
```
- 원격 저장소에서 이미지를 pull한다.
  - [여기](https://hub.docker.com/?ref=login)가 docker hub
  - 기본적으로 github와 비슷하다.
  - 대부분의 이미지들(ubuntu, spring, mysql)은 존재하고 유저들이 커스텀한 이미지들도 존재한다.
- tag는 생략이 가능하다.
  - default 는 latest
- Official 이미지 들은 이름만 붙히면 되지만 커스텀 이미지 들은 repository 이름 까지 같이 명시해야한다.
    ```
    docker pull ksshlee/mysql:5.7
    ```


이미지 검색
```
docker search [option] [keyWord]
```
- 옵션
  - --automated=true : automated Build 만 표시
  - --starts=N : N개 이상의 star 만 표시
- 옵션은 생략 가능하다.


이미지 목록
```
docker images
```
- 로컬에 저장된 이미지 목록들을 확인



이미지 삭제
```
docker rmi [option] [image id]
```
- 옵션
  - -f : 컨테이너도 강제 삭제
- image id를 가진 이미지 삭제

## 📦 도커 컨테이너 명령어

컨테이너 목록
```
docker ps [option]
```
- 옵션
  - -a : 모든 컨테이너 목록 (종료된 컨테이너도 출력)
  - -s : 컨테이너의 디스크 사용량
- 현재 구동중인 컨테이너 목록 출력


컨테이너 명령어 사용
```
docker exec -it [id || name] [command]
```
- command 에 원하는 명령어를 치면 컨테이너에서 해당 명령어가 실행이 된다.


컨테이너 시작
```
docker start [id || name]
```

컨테이너 정지
```
docker stop [id || name]
```

컨테이너 재시작
```
docker restart [id || name]
```

컨테이너 삭제
```
docker rm [id || name]
```

모든 컨테이너 삭제
```
docker rm `docker ps -a -q`
```


## 🐳 도커 Run

```
docker run [option] [image]:[tag] [command] [args]
```
옵션
- -d : detached 모드 실행으로 백그라운드에서 실행을 하게 해주는 옵션이다.
- -it : -i, -t 명령어를 합친 것으로 주로 같이 쓰인다. 컨테이너를 종료하지 않은 상태로, 터미널에 입력을 계속하게 하기 위한 명령어다.
  - cli 를 자주 사용하는 컨테이너에서 주로 사용된다.
- --name : 컨테이너의 이름을 정해주는 옵션이다. 컨테이너의 id는 기억하기 어려워 해당 옵션으로 이름을 붙혀 사용한다.
- -e : 컨테이너의 환경변수를 설정하기 위해 사용하는 옵션
- -p : 포트 바인드를 하기 위해서 사용되는 옵션
  ```
  docker run -p 80:8080 test
  ```
  - 간단하게 80 포트로 접속을 하면 컨테이너 내부에서 8080포트로 포워딩을 해라
- -v : 호스트와 컨테이너 간의 볼륨을 설정하기 위한 옵션.
  - 호소트 컴퓨터의 경로를 컨테이너의 특정 경로로 마운트를 해준다.
  - 주로 로그, db 컨테이너에서 사용되는 옵션이다.
- --rm : 일회성 컨테이너에서 사용되는 옵션
  - 컨테이너가 종료될때 컨테이너, 관련 리소스를 모두 제거한다.


<br><br>




# 출처
-----

[https://velog.io/@wlsdud2194/-Docker-%EB%8F%84%EC%BB%A4-%EA%B8%B0%EB%B3%B8-%EB%AA%85%EB%A0%B9%EC%96%B4-%EB%AA%A8%EC%9D%8C](https://velog.io/@wlsdud2194/-Docker-%EB%8F%84%EC%BB%A4-%EA%B8%B0%EB%B3%B8-%EB%AA%85%EB%A0%B9%EC%96%B4-%EB%AA%A8%EC%9D%8C)

[https://www.daleseo.com/docker-run/](https://www.daleseo.com/docker-run/)
<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
