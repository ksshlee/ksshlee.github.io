---
title: Spring MyBatis
categories : [Spring]
tags: [Spring, Web, BackEnd, MyBatis]
toc: true
toc_sticky: true
toc_label: "목차"
---


MyBatis란?
--

- 자바에서 관계형 DB 프로그래밍을 쉽게 도와주는 프레임워크
- SQL 쿼리문들을 Java 파일이 아닌 xml 파일로 구성하여 관리한다.
- 중복작업을 최소화 시킨다.
- SQL 구문을 바꿀때 xml에서 수정해서 유지보수에 매우 용이하다.

### JDBC VS MYBATIS

*JDBC*
- 한 파일에 Java언어와 SQL언어가 섞여있다.
- 한 파일에 같은 SQL구문을 사용하는 일이 발생할 수 있음.
- 조회 결과를 Resultset등을 사용하여 .next()를 활용해야된다.

*MYBATIS*
- SQL 구문을 xml파일에서 따로 관리한다.
- JDBC를 사용하면 해야되는 Connection try,catch,finally Statement 등을 mybatis가 직접 관리해준다.
- 조회 결과를 사용자가 정의한 VO 혹은 MAP등으로 매핑할수 있다.


### 구조

- MyBatis도 JDBC 드라이버를 사용한다.
- sqlSessionFactory, sqlSession, dataSource를 bean으로 설정하고 DI(의존성 주입)를 하기 때문에 사용자가 쉽게 사용이 가능하다.

<span id="point1"></span>

![mybatis](/assets/img/back_end/2020-01-27/mybatis.png)

- JDBC 였다면 JDBC Interfaces에 있는 단계를 개발자가 해줘야 된다.
- MyBatis가 JDBC Interfaces에 있는 단계를 자동으로 해줘서 개발자가 훨씬 편리해졌다.


### 흐름

<span id="point2"></span>

![mybatis](/assets/img/back_end/2020-01-27/mybatis2.png)

> 응용프로그램 최초 실행

- Application은 추후에 SqlSessionFactory에 접근을 해야된다.
  - 그러므로 Factory Builder한테 Factory좀 만들어달라고 한다.
- Builder는 Config File을 조회한다.
  - Config File을 토대로 Build를 한다.
  - Config File에는 alias로 이름을 간편하게 설정해줄수 있는등 여러가지 설정을 할 수 있다.
  - Example) com.ksshlee.dto.BoardVO를 일일이 치지않고 alias로 boardVO로 정의하면 boardVO만 입력하면 된다.
- Builder는 Config File을 기반으로 Session Factory를 생성한다.
  - 생성된 Factory에는 dataSource, config 파일의 위치, mapper 위치 정보들이 담겨져 있다.

> 사용자로부터 요청이 들어왔을 때

- Application은 SessionFactory한테서 정보를 가져온다.
  - Connection을 하기위해 dataSource를 가져온다.
  - 그외에 모든 정보들을 가져온다.
- Factory는 SqlSession을 생성한다.
  - 결국엔 Application이 Factory에서 정보를 갖고오는것이 아닌 SqlSession에서 정보를 갖고온다.
  - 요청 -> Factory가 Session 만들어줌 -> application은 Session 가져오기
- Application은 Mapper Interface를 호출한다.
- Mapper Interface는 SqlSession에서 정보를 얻어 SQL과 연결을 맺는다.
- 연결을 맺은후 Mapping File에서 실행할 SQL 구문을 찾아 실행한다.
  - Application에서 이미 Mapper Interface한테 야 ~~좀 실행해라고 명령을 내린후다.


<br><br>


MyBatis 연동
--

- 간단하게 MyBatis를 이용해서 값을 꺼내보자


### Directory 구조

![dir](/assets/img/back_end/2020-01-27/dir.png)

- 시작전에 완성후의 directory 구조다 어디에다가 만들지!? 가 궁금할땐 여기와서 확인을 해보자!
- 파란색은 새로 만든 파일 빨강색은 기존에 있던 파일을 수정한것.





### maven or gradle에 라이브러리 추가

>pom.xml

```xml
        <!-- mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.1</version>
		</dependency>

		<!-- mybatis-spring -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.0</version>
		</dependency>

        <!-- MySQL -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>8.0.18</version>
		</dependency>


        <!-- Spring JDBC -->
        <dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
```

- mybatis, 사용할 DB(oracle,mysql,등등), **JDBC**를 추가해준다.
- **JDBC를 추가하지 않으면 <a href="#point1">여기</a>에서 JDBC interface나 driver등을 사용을 할 수 없으므로 오류가 발생한다.**


### dataSource, sqlSession Factory

>root-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- dataSource -->
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName"
			value="com.mysql.cj.jdbc.Driver"></property>
		<property name="url"
			value="jdbc:mysql://localhost/Board?serverTimezone=UTC"></property>
		<property name="username" value="test"></property>
		<property name="password" value="test"></property>
	</bean>


	<!-- SqlSessionFactoryBean -->
	<bean id="sqlSessionFactory"
		class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource"></property>
		<property name="configLocation"
			value="classpath:/mybatis-config.xml"></property>
		<property name="mapperLocations"
			value="classpath:mappers/**/*Mapper.xml"></property>

	</bean>


	<!-- SqlSessionTemplate -->
	<bean id="sqlSession"
		class="org.mybatis.spring.SqlSessionTemplate"
		destroy-method="clearCache">
		<constructor-arg name="sqlSessionFactory"
			ref="sqlSessionFactory"></constructor-arg>
	</bean>

</beans>

```

- 이 작업은 <a href="#point2">여기</a>서 응용 프로그램이 최초 시작한 후 실행할 것들을 설정해주는것이다. 즉 Factory를 Bean으로 만들어주는 작업이다.
- bean id,  name 특히 **class**는 건들면 안된다. id는 건들이면 추후 DI를 할때 귀찮아도 다 바꿔주면 작동이 될수 있지만 class는 절대로 건들면 안된다.
- dataSource는 **Connection을 위한 DB 정보**들을 담고 있는 bean이다.
  - jdbc DRIVER, DB url, username, password를 담고있다.
- sqlSessionFactory는 dataSource, configLocation (config 파일 위치), mapperLocations(mapper 위치)를 담고있다.
  - 해당 디렉토리 mybatis-config.xml 파일이 config 파일이야~~
  - 해당 디렉토리밑에 *Mapper.xml 파일은 mapper야~~
- sqlSession은 생성자로 sqlSessionFactory를 객체형태로 받고 있다.
  - 해당 xml이 이해가 안된다면 <a href="https://ksshlee.github.io/spring/springioc/#di%EC%9D%98-%EC%9C%A0%ED%98%95">여기</a>로 가서 한번 읽고 오자


### VO(DTO)생성

>BoardVO.java

```java
package com.ksshlee.dto;

public class BoardVO {
	private int pid;
	private String title;
	private String content;
	private String pwd;
	private String author;
	
	public int getPid() {
		return pid;
	}
	public void setPid(int pid) {
		this.pid = pid;
	}
	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public String getContent() {
		return content;
	}
	public void setContent(String content) {
		this.content = content;
	}
	public String getPwd() {
		return pwd;
	}
	public void setPwd(String pwd) {
		this.pwd = pwd;
	}
	public String getAuthor() {
		return author;
	}
	public void setAuthor(String author) {
		this.author = author;
	}
}
```
- 이건 그냥 테스트를 하는 용으로 만들었다. Board라는 Table에 pid, title, 등등의 칼럼들이 있는데 sql문으로 요청을 하고 응답을 받을 때 VO 로 받는 실험을 해보자.

### config 파일 생성

>mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration 
    PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

	<typeAliases>
		<typeAlias type="com.ksshlee.dto.BoardVO" alias="boardVO" />
	</typeAliases>
</configuration>
```

- <a href="#point2">여기</a>에서 말한 config 파일이다. Factory Builder는 해당파일을 참고하여 Factory를 생성한다. 위의 예시말고도 다양하게 사용법이 있지만 이 포스팅에서는 간단하게 alias만 사용해보겠다.
- **!DOCTYPE configuration ... 이내용들 꼭 같이 넣어주기!**
- com.ksshlee.dto.BoardVO 를 boardVO로 닉변을 했다. 이걸 왜하는지 곧 알려줄 예정!



### mapper 설정

>boardMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 
<mapper namespace="com.ksshlee.mappers.boardMapper">
 
 
 
 <!-- 전체 글 조회 -->
 <select id="allBoard" resultType="boardVO">
 	SELECT * FROM Board
 </select>
 
  
</mapper>
```

- mapper namespace는 "나는 mapper인데 나의 이름은 com.ksshlee.mappers.boardMapper야" 라고 말하는거라고 생각하면 될것 같다.
- SELECT 문이므로 select 태그를 사용 태그에다가 SQL 쿼리문을 넣어주면 된다.(delete면 delete 각각에 맞는 태그를 사용하면 된다.)
  - 해당 SQL쿼리문의 id = allboard 반환 타입은 boardVO다
  - config 파일에서 alias를 안해줬더라면 com.ksshlee...등을 항상 쳐줘야 된다. 그 불편함을 줄이기 위해 alias를 해준것!


### DAO, DAOImpl

>BoardDAO

```java
package com.ksshlee.dao;

import java.util.List;

import com.ksshlee.dto.BoardVO;

public interface BoardDAO {
	public List<BoardVO> allBoard() throws Exception;
}
```

>BoardDAOImpl

```java
package com.ksshlee.dao;

import java.util.List;

import javax.inject.Inject;

import org.apache.ibatis.session.SqlSession;
import org.springframework.stereotype.Repository;

import com.ksshlee.dto.BoardVO;

@Repository
public class BoardDAOImpl implements BoardDAO {
	@Inject
	private SqlSession sqlSession;

	private static final String Namespace = "com.ksshlee.mappers.boardMapper";

	// 전체 게시글 출력
	@Override
	public List<BoardVO> allBoard() throws Exception {
		return sqlSession.selectList(Namespace + ".allBoard");
	}

}
```

- DAO 즉 sql에 접근하는 파일들이다.
- BoardDAO라는 **Interface**를 만들어준다.
- BoardDAO를 상속받는 BoardDAOImpl라는 **Class**를 만들어준다.

```java
@Inject
	private SqlSession sqlSession;
```
- <a href="#point2">여기서</a> 4번에 해당하는 코드다. sqlSession을 주입해준다.

- Namespace는 아까 mapper에서 설정한 "이름"을 넣어주면 된다.
- sqlSession.selectList(mapper의 이름 + 원하는 쿼리문의 id) 이 코드는 <a href="#point2">여기서</a> 7번에 해당된다고 볼수 있다.


### Service ServiceImpl 생성

>BoardService.java

```java
package com.ksshlee.service;

import java.util.List;

import com.ksshlee.dto.BoardVO;

public interface BoardService {
	public List<BoardVO> allBoard() throws Exception;
}
```

- BoardService interface 생성

>BoardServiceImpl.java

```java
package com.ksshlee.service;

import java.util.List;

import javax.inject.Inject;

import org.springframework.stereotype.Service;

import com.ksshlee.dao.BoardDAO;
import com.ksshlee.dto.BoardVO;

@Service
public class BoardServiceImpl implements BoardService{

	@Inject
	private BoardDAO dao;
	
	//게시글 전체 출력 
		@Override
		public List<BoardVO> allBoard() throws Exception{
			return dao.allBoard();
		}
	
}
```

- Annotation으로 @Service로 Service 파일이라고 암시해준다.
- BoardDAO 를 주입해준다.



### Controller 생성

>IndexController

```java
package com.ksshlee.controller;

import java.util.List;

import javax.inject.Inject;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.ksshlee.dto.BoardVO;
import com.ksshlee.service.BoardService;

@Controller
public class IndexController {

	@Inject
	private BoardService service;

	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String index(Model model) throws Exception {

		List<BoardVO> boardList = service.allBoard();		
		model.addAttribute("boardList", boardList );

		return "home";

	}

}
```

- sql쿼리문의 응답을 BoardVO 객체 형태의 List로 받는다.
- 해당 List를 model에다가 boardList로 선언해준다.



### JSP

>home.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<head>
	<title>TEST</title>
</head>
<body>
<table border=1>
	<thead>
		<tr>
			<th>번호</th>
			<th>게시글 제목</th>
			<th>작성자</th>
		</tr>
	</thead>
	<tbody>
		<c:forEach items="${boardList}" var="board">
			<tr>
				<td>${board.pid}</td>
				<td><a href="./read?pid=${board.pid}">${board.title}</a></td>
				<td>${board.author}</td>
			</tr>
		</c:forEach>
	</tbody>
</table>
</body>
</html>
```

- 여기서는 jstl, el 표기법이 들어갔다. 추후에 포스팅을 할테니 여기선 그냥 가볍게 넘어가자
<br>

![result](/assets/img/back_end/2020-01-27/result.png)

- 그러면 이렇게 성공적으로 뜨는것을 확인할 수 있다.
- 미리 Table에 값을 넣어놔서 저렇게 뜬다. Table에 아무것도 안넣었다면 아무것도 안뜨겠죠?!?!

<br><br>


출처
--
[http://mybatis.org/spring/ko/index.html](http://mybatis.org/spring/ko/index.html)


[https://khj93.tistory.com/entry/MyBatis-MyBatis%EB%9E%80-%EA%B0%9C%EB%85%90-%EB%B0%8F-%ED%95%B5%EC%8B%AC-%EC%A0%95%EB%A6%AC](https://khj93.tistory.com/entry/MyBatis-MyBatis%EB%9E%80-%EA%B0%9C%EB%85%90-%EB%B0%8F-%ED%95%B5%EC%8B%AC-%EC%A0%95%EB%A6%AC)


[http://blog.daum.net/_blog/BlogTypeView.do?blogid=0ZeCX&articleno=74&categoryId=8&regdt=20180710120743](http://blog.daum.net/_blog/BlogTypeView.do?blogid=0ZeCX&articleno=74&categoryId=8&regdt=20180710120743)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
