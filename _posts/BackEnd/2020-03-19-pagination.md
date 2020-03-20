---
title: ⭐️Pagination (Offset based ,Cursor based Pagination)⭐️
categories : [Spring, Spring Boot, BackEnd]
tags: [Pagination, Skill]
toc: true
toc_sticky: true
toc_label: "목차"
---

페이지네이션(Pagination) 이란?
--

![pagination](/assets/img/back_end/2020-03-19/pagination.gif)

- 리소스를 분할하여 전달하는 기법
- 어떤 기준으로 정렬한 데이터를 지정된 갯수의 데이터만 전달
- 네트워크의 낭비를 막아준다.
- 빠른 응답의 효과가 있다.

**Pagination에는 2가지 방식이 존재한다.**

1. 오프셋 기반 페이지네이션 (Offset based Pagination)
   - DB의 `OFFSET`쿼리와 `LIMIT`쿼리를  사용
   - **페이지**단위로 구분
2. 커서 기반 페이지네이션 (Cursor based Pagination)
   - `Cursor`의 개념을 사용
   - 사용자에게 응답해준 마지막의 데이터가 Cursor가 된다.
   - Cursor를 기준으로 Cursor 다음 n개의 데이터를 응답

<br><br>




오프셋 페이지네이션 (Offset Pagination)
--

> 첫번째 페이지

```
/*MySQL*/

SELECT ID, CONTENT, CREATION_DATE
FROM POST
ORDER BY CREATION_DATE DESC
LIMIT 5

/*MONGO*/

db.posts.find().limit(5)
```

- LIMIT은 제한이다 즉 최대 5개의 값만 출력이된다.


> 첫번째 페이지 이후

```
/*MySQL*/

SELECT ID, CONTENT, CREATION_DATE
FROM POST
ORDER BY CREATION_DATE DESC
LIMIT 5
OFFSET ((page-1) x limit)

/*MONGO*/

db.posts.find().skip(((page-1) x limit)).limit(5)
```

- OFFSET, skip 쿼리문을 사용하였다.
- 해당 쿼리들은 건너뛰어라 라는 의미가 있다.
- OFFSET과 skip은 몇번째 페이지에 따라서 값이 증가 한다.
  -  ex) 4페이지 = ((4-1)x5)15, 6페이지 = ((6-1)x5)25 이런식으로
  -  즉 4페이지면 15 건너뛰고 16 5개를 보여줘라

  

**하지만 오프셋 페이지네이션에는 치명적인 단점이 있다.**  

> 데이터 중복 issue

![pagination](/assets/img/back_end/2020-03-19/pagination2.png)

- 위의 쿼리를 예시로 들겠다.
- 1 페이지를 요청하면 날짜를 내림차순 (최신순)으로 정렬을 한고 1~5번째 데이터를 응답해준다.

![pagination](/assets/img/back_end/2020-03-19/pagination3.png)

- 2 페이지를 요청을 한다.
- 하지만 그 사이 5개의 새로운 게시물이 생겼다.
- DB에선 쿼리가 `OFFSET 5`또는 `skip(5)`로 되어있을테니 5개를 건너뛰어서 6번째 즉 id가 1부터 5개를 응답해줄 것이다.


> 성능 issue

- 극단적으로 10억번째 페이지에 있는 값을 찾고 싶다면 OFFSET 또는 skip에 매우 큰 숫자가 들어가게 된다.
- 그렇게 되면 퍼포먼스가 매우 떨어질 것이다.


**`정리`**
- 데이터의 생성이 거의 없다.
- 중복된 데이터를 노출해도 상관없다.
- 데이터의 양이 많지 않아 퍼포먼스적 이슈를 고려할 필요가 없다.

**위와 같은 상황이라면 오프셋 페이지네이션을 사용해도 무방하다.**

<br><br>


커서 페이지네이션 (Cursor Pagination)
--

> 첫번째 페이지

```
/*MySQL*/

SELECT ID, CONTENT, CREATION_DATE
FROM POST
ORDER BY CREATION_DATE DESC
LIMIT 5

/*MONGO*/

db.posts.find().limit(5)
```
- 1번째 페이지는 오프셋과 같습니다.


> 첫번째 페이지 이후

```
/*MySQL*/

SELECT ID, CONTENT, CREATION_DATE
FROM POST
WHERE CREATION_DATE < (CreationDate Cursor)
    OR (CREATION_DATE = (CreationDate Cursor) AND id>(Id Cursor))
ORDER BY CREATION_DATE DESC, ID ASC
LIMIT 5

/*MONGO*/

db.posts
.find(
'CREATION_DATE' : {'$lt' : (CreationDate Cursor)}
{ $or : [ { $and : [{'CREATION_DATE' : (CreationDate Cursor)},{'id' : {'$gt' : (Id Cursor)}}]}]}
).limit(5)
```

- 즉 유저에게 마지막으로 응답했던 데이터중에서 마지막 데이터가 Cursor가 된다.
  - 마지막 data의 CreationDate와 Id가 각각의 cursor가 된다.
- 그럼 왜 OR 절이 있나?
  - 만약 정확히 같은 시간에 여러개의 게시글이 생겼을 때 1개의 게시글을 제외하고 나머지는 무시될수 있기 때문에 OR 절을 활용하여 표현한다.


![pagination](/assets/img/back_end/2020-03-19/pagination4.png)

- 첫번째 페이지는 오프셋과 같다

![pagination](/assets/img/back_end/2020-03-19/pagination5.png)

- 아무리 새로운 데이터가 생성이 되도 Cursor기반이므로 중복되지 않는다.
- 위의 쿼리대로 생성시간이 6보다 작거나 (생성시간이 6이랑 같고 id가 5보다 큰) 값만 검색하게 된다.
- 만약 OR절이 없었다면 id가 11,12인 값은 무시하고 넘어 갔을 것이다. 이런거 때문에 **OR절이 반드시 필요하다**
- 커서 기반은 `페이지`개념이 아니고 쉽게 생각하면 인스타, 페이스북 같이 스크롤이라고 생각하면 된다.
  - `어 그럼 한번에 데이터를 한번에 다 조회해서 보여주면 되는거 아닌가요?`
    - 예를들어 매번 150개의 데이터를 준다고 가정해보자
    - 사용자는 보통 10번째 12번째 게시물만 보고 앱 사용을 종료한다
    - 그렇게 되면 네트워크의 낭비가 너무 심하다
    - 또한 그 데이터가 1000억개가 되면 속도도 굉장히 느릴것이다. 

### 커서 페이지네이션 응용

 `해당 기술은 김민상 개발자님께서 포스팅해주신 글에서 학습했습니다🙏`

- 위의 쿼리 처럼 사용하면 OR절도 항상 사용해야되고 상당히 복잡하다.
> 첫번째 페이지

```
SELECT ID, CONTENT ,CREATION_DATE,
		CONCAT(LPAD(CREATION_DATE, 3, '0'), LPAD(ID, 3, '0')) as `CURSOR`
	FROM `POST`
	ORDER BY CREATION_DATE DESC, ID DESC
	LIMIT 5;
```

- `CONCAT` : 문자열을 합치는 쿼리문
  - ex) CONCAT('SANG','HYUK') => SANGHYUK
- `LPAD` : 지정된 길이로 해당 문자열로 채움 (왼쪽)
  - ex) LPAD('HI',5,'0') => 000HI

> Query Result Example

![pagination](/assets/img/back_end/2020-03-19/pagination6.png)

> 첫번째 페이지 이후

```
SELECT ID, CONTENT ,CREATION_DATE,
		CONCAT(LPAD(CREATION_DATE, 3, '0'), LPAD(ID, 3, '0')) as CURSOR
	FROM POST
    WHERE CURSOR < CONCAT(LPAD(CREATION_DATE_CURSOR, 3, '0'), LPAD(ID_CURSOR, 3, '0'))
	ORDER BY CREATION_DATE DESC, ID DESC
	LIMIT 5;
```

- CONCAT 과 LPAD 혹은 RPAD를 사용하면 훨씬 쉽게 쿼리를 작성할 수 있다.

<br><br>





출처
--

[https://www.codementor.io/@arpitbhayani/fast-and-efficient-pagination-in-mongodb-9095flbqr](https://www.codementor.io/@arpitbhayani/fast-and-efficient-pagination-in-mongodb-9095flbqr)

[https://dev.to/jackmarchant/offset-and-cursor-pagination-explained-b89](https://dev.to/jackmarchant/offset-and-cursor-pagination-explained-b89)

[https://velog.io/@leejh3224/%ED%8E%98%EC%9D%B4%EC%A7%80%EB%84%A4%EC%9D%B4%EC%85%98-%EC%BB%A4%EC%84%9C%EA%B8%B0%EB%B0%98-%ED%8E%98%EC%9D%B4%EC%A7%80%EA%B8%B0%EB%B0%98](https://velog.io/@leejh3224/%ED%8E%98%EC%9D%B4%EC%A7%80%EB%84%A4%EC%9D%B4%EC%85%98-%EC%BB%A4%EC%84%9C%EA%B8%B0%EB%B0%98-%ED%8E%98%EC%9D%B4%EC%A7%80%EA%B8%B0%EB%B0%98)

[커서 페이지네이션 응용 기술](https://velog.io/@minsangk/%EC%BB%A4%EC%84%9C-%EA%B8%B0%EB%B0%98-%ED%8E%98%EC%9D%B4%EC%A7%80%EB%84%A4%EC%9D%B4%EC%85%98-Cursor-based-Pagination-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)




<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
