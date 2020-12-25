---
title: \[Mongo DB\] Mongo DB Replica Set
categories : [Mongo,MongoDB]
tags: [Mongo,MongoDB,DB]
toc: true
toc_sticky: true
toc_label: "목차"
---
# Why Replica Set   
-----
- replica 는 사전적 의미로 복제라는 뜻을 지닌다. 즉 DB를 여러개 복제하여 사용한다 라는 뜻을 갖고 있다.
- 굳이 왜 같은 데이터를 갖고 있는 DB 를 복제하면서 까지 써야할까??

>1

![whyrs](/assets/img/back_end/2020_12_25/whyrs.png)
- DB 가 1개가 있다고 가정을 해보자
- 만약 서비스 도중 DB가 죽게 된다면?? 서비스는 중단될것이다.
  - 어떻게든 DB 를 살려야한다.
  - DB를 살리는 동안 서비스는 중단이 된다.
- DB 를 Scale up 을 해도 1개인 DB 가 죽으면 위랑 똑같은 상황이 된다.
- 그래서 Scale out 를 통하여 여러개의 DB 를 둬서 서비스가 중단되지 않게 해야한다.


>2

- MongoDB 4.x 버전 부터 transaction을 지원한다!!
- 개발, 운영하는데 있어서 DB 의 Transaction 은 정말 필수중에 필수중에 필수다.
- **Transaction 기능을 사용하려면 반드시 replica set 을 구성해야한다.**
  - 스포일러를 하자면 replica set 은 write, read 노드가 따로 존재한다.
  - 이때 write 를 진행한 primary 에서 replication 을 해야 read 를 진행하는 secondary 에서 추가, 수정된 데이터를 읽을 수 있다. (replication 을 하는 시점은 transaction 이 끝나는 시점 or session이 끝나는 시점)
- 즉 mongoDB 로 운영까지 하려면 replica set은 옵션이 아닌 필수다.


# Replica Set 이란?
-------


![rs1](/assets/img/back_end/2020_12_25/rs1.png)

- **Primary**는 쓰기 담당 **Secondary**는 읽기 담당을 하고 있다.
  - 이때 **Primary는 단 1개** 밖에 존재할 수 없다.

- Client(Server) 에서 데이터 조회를 요청을 하면 Secondary 에서 수행을 하고 쓰기,삭제,수정을 요청하면 Primary 에서 수행을 한다.
- 쓰기, 삭제, 수정이 수행이 되면 Primary 는 Replication 을 통해 Secondary 에도 정보를 변경한다. (동일성)


![rs2](/assets/img/back_end/2020_12_25/rs2.png)
- 만약 Primary DB 가 다운이 되면 쓰기 기능이 불가능해지면 어떡하지? 라는 고민을 해결하기 위해 나타난게 **Arbiter**
- **Arbiter는 투표만** 가능하다.
  - 즉 평상시에는 데이터 쓰기, 읽기도 안하다가 Primary 가 죽어 다음 Primary 를 선출할때 사용된다.
  - **그래서 arbiter 는 데이터를 들고 있지 않는다.**
- 기본적으로 모든 set 은 투표권을 갖고 있다.
  - 하지만 Primary - Secondary - Secondary 일때 Primary 가 죽으면 투표권이 1대 1이 되므로 Primary 가 선출되지 않는다.
  - 이를 방지하기 위해서 Arbiter 를 생성한다.
  - 설정할때 Arbitor 에게 priority 를 줄수 있는데 이는 arbitor 가 원하는 set으로 선출할 가능성을 높이는거지 반드시 해당 set 이 선출되는 것은 아니다.
- **중요!!** replica set 당 1개의 arbiter 만 배포하자

![elect](/assets/img/back_end/2020_12_25/elect.png)
- 투표 과정을 통하여 primary 가 죽어도 새로운 primary 를 선출하여 서비스에 지장이 없도록 도와준다.
- 투표과정도 흥미롭다 다음 포스팅에서 replica set 투표를 좀 자세하게 다뤄보도록 하자!



# MongoDB Replica set & docker
--------
> docker compose

```docker
version: '3.7'
  mongo1:
    hostname: mongo1
    container_name: mongo1
    image: mongo:4.2
    ports:
      - 27017:27017
    restart: always
    entrypoint: [ "/usr/bin/mongod", "--bind_ip_all", "--replSet", "rs0", "--journal", "--dbpath", "/data/db", "--enableMajorityReadConcern", "false" ]
    volumes:
      - /db/mongo/mongo1:/data/db
      - /db/mongo/mongo1/configdb:/data/configdb
  mongo2:
    hostname: mongo2
    container_name: mongo2
    image: mongo:4.2
    ports:
      - 27018:27017
    restart: always
    entrypoint: [ "/usr/bin/mongod", "--bind_ip_all", "--replSet", "rs0", "--journal", "--dbpath", "/data/db", "--enableMajorityReadConcern", "false" ]
    volumes:
      - /db/mongo/mongo2:/data/db
      - /db/mongo/mongo2/configdb:/data/configdb
  mongo3:
    hostname: mongo3
    container_name: mongo3
    image: mongo:4.2
    ports:
      - 27019:27017
    restart: always
    entrypoint: [ "/usr/bin/mongod", "--bind_ip_all", "--replSet", "rs0", "--journal", "--dbpath", "/data/db", "--enableMajorityReadConcern", "false" ]
    volumes:
        - /db/mongo/mongo3:/data/db
        - /db/mongo/mongo3/configdb:/data/configdb
```

- docker compose 로 구성한후 한번에 띄워준다.


> step 1

- mongo1 컨테이너에서 mongo에 접속한다.

```
docker exec -it mongo1 mongo
```

```javascript
rs.initiate(
  {
    _id : 'rs0',
    members: [
      { _id : 0, host : "mongo1:27017" },
      { _id : 1, host : "mongo2:27018" },
      { _id : 2, host : "mongo3:27019", arbiterOnly: true }
    ]
  }
)

rs.conf();
```
- 이걸 입력하면 끄으읕.. 정말 간단하게 설정 할 수 있다.







# 출처
-------
[https://docs.mongodb.com/manual/replication/](https://docs.mongodb.com/manual/replication/)

[https://docs.mongodb.com/manual/core/replica-set-arbiter/](https://docs.mongodb.com/manual/core/replica-set-arbiter/)

[https://unagi44.wordpress.com/2015/09/10/mongodb-replica-set%EC%9D%98-%ED%95%84%EC%9A%94%EC%84%B1-3/](https://unagi44.wordpress.com/2015/09/10/mongodb-replica-set%EC%9D%98-%ED%95%84%EC%9A%94%EC%84%B1-3/)

[https://unagi44.wordpress.com/2015/09/10/mongodb-replica-set-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0/](https://unagi44.wordpress.com/2015/09/10/mongodb-replica-set-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0/)

[https://medium.com/@simone.pezzano/quick-docker-and-mongodb-replica-set-on-your-computer-5c2470012a41](https://medium.com/@simone.pezzano/quick-docker-and-mongodb-replica-set-on-your-computer-5c2470012a41)

[https://gist.github.com/harveyconnor/518e088bad23a273cae6ba7fc4643549](https://gist.github.com/harveyconnor/518e088bad23a273cae6ba7fc4643549)

<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
