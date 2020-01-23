---
title: Spring DI,IoC 실습 (xml,annotation)
categories : [Spring]
tags: [Spring, Web, BackEnd]
toc: true
toc_sticky: true
toc_label: "목차"
---


Bean,DI with xml
--

- 저번 포스팅때 Spring DI를 xml로 실습을 했었지만 조금더 자세하게 포스팅을 해보겠다.

>Cpu.java

```java
package com.study.testfordi;

public interface Cpu {
	public void showBrand();
}
```

- 우선은 이렇게 interface를 하나 만들어 놓은다. 여기서 showBrand()는 추후 이 interface를 상속 하는 class는 저 메소드가 반드시 있어야한다.

>Model1.java

```java
package com.study.testfordi;

public class Model1 implements Cpu{
	String brand;
	
	public void setBrand(String brand) {
		this.brand = brand;
	}
	
	public void showBrand() {
		System.out.println(brand);
	}
}
```
- 이건 model1 클래스다. Cpu를 상속했기때문에 showBrand는 반드시 재정의 해줘야한다.
- **또한 추후 bean 파일에서 property 영역에 값을 넣어주려면 setter는 반드시 있어야한다.** 이건 조금있다가 설명하겠다.

>Model2.jaba

```java
package com.study.testfordi;

public class Model2 implements Cpu{
		String brand;

		public void setBrand(String brand) {
			this.brand = brand;
		}
		
		public void showBrand() {
			System.out.println(brand);
		}
}
```

- model1과 같으므로 패스!

>MainBoard.java

```java
package com.study.testfordi;

public class MainBoard {
	public Cpu cpu;
	
	
	public MainBoard(Cpu cpu) {
		this.cpu = cpu;
	}
	
	public void modelname() {
		cpu.showBrand();
	}
}
```

- 이제 MainBoard 클래스이다. 
- 메인보드에 Cpu를 장착해줘야 하니 Cpu를 선언해준다.
- 여기서 model1,2와 달리 **생성자** 로 선언한 이유는 bean에서 constructor-arg를 사용하기 때문이다. 이것도 자세히 설명하겠다.

>Driver.jaba

```java
package com.study.testfordi;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Driver {

	public static void main(String[] args) {


		ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
		
		
		
		MainBoard m = (MainBoard) ac.getBean("mainboard");
		m.modelname();
		
	}

}
```
- ApplicationContext와 ClassPathXmlApplicationContext를 이용하여 bean 파일에 접근을 할수 있다.
- 저렇게 .getBean("id")를 통하여 원하는 bean을 꺼내오고 있다.
- 위의 예시로 mainboard bean은 Object타입 이므로 형 변환이 반드시 필요하다. 그러므로 (MainBoard)로 형 변환을 해준다.
- m.modelname()으로 현재 메인보드에 장착된 cpu 모델을 확인한다.

>ApplicationConfigure.xml

```java
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

    <bean id="intel" class="com.study.testfordi.Model1">
        <property name="brand" value="intel"></property>
    </bean>

    <bean id="amd" class="com.study.testfordi.Model2">
        <property name="brand" value="amd"></property>
    </bean>

    <bean id="mainboard" class="com.study.testfordi.MainBoard">
        <constructor-arg ref="intel"></constructor-arg>
    </bean>
</beans>
```

- 이게 이제 중요하다.beans 하고 쓰여진 저것들은 xml 파일에 반드시 입력해줘야되는 필수적인 부분들이다.

- bean id는 말그대로 id값 class는 사용자가 bean에 등록하고 싶은 클래스가 위치한 곳을 명시해주면 된다.
  - 여기서 id는 꼭 class 이름을 따를 필요가 없다.

<span id="here"></span>

- **property는 어떤 변수등을 삽입을 할때 사용한다**
  - property를 사용하려면 반드시 **setter**가 있어야한다.
  - property의 name은 변수이름 value는 해당 변수에 넣고 싶은 값을 넣으면 된다.
- **constructor-arg도 property와 같은 기능을한다.**
  - 하지만 다른점은 property는 setter가 있어야한다면 constructor-arg는 **생성자**가 있어야한다.


- **value는 String으로 혹은 Int로된 형태의 변수를 넣을때 ref는 객체형태로 넣을때 사용된다.**
  - ref="intel"은 즉 mainboard 생성자에 intel 객체를 넣어라 라는 뜻이된다.
  - 그럼 메인보드에 intel cpu가 들어가게 된다.
  - 추후에 cpu를 바꿀때 코드에서 바꿔주는것이 아닌 여기서 intel을 amd로 바꿔주면 된다.

<br><br>


Bean,DI with Java config(Annotation)
--

- 우선 xml로 했던 DI 예제와 Annotation으로 하는 예제 코드는 살짝 변동을 주겠다.


>ApplicationConfigure.java

```java
package com.study.testfordi;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicationConfigure {
	@Bean
	public MainBoard mainboard() {
		return new MainBoard();
	}
	
	@Bean
	public Intel intel() {
		return new Intel();
	}
}
```

- @Configuration : 스프링 클래스를 선언하는 Annotation
  - 즉 해당 Annotation이 붙어있으면 Bean을 생성해주는 클래스다~ 라고 생각하면 될것 같다.
- @Bean : Bean을 생성해주는 annotation

>Change

![an1](/assets/img/back_end/2020-01-22/an1.png)
- 기존 xml로 사용했더라면 annotation을 사용하면 위와 같이 변환 할수 있다.

![an2](/assets/img/back_end/2020-01-22/an2.png)

- property나 constructor-arg를 사용했을 경우다.
- property를 사용하려면 위에서도 말했지만 setter함수가 있어야하므로 저런식으로 초기화를 해준다.
- constructor 같은경우 생성자로 초기화를 해줘야한다.
- 화살표에 번호가 있으므로 순서대로 잘 따라가보자
- property, constructor, ref등이 이해가 안되면 <a href="#here">여기</a>로 이동해서 다시한번 보자
- 이 클래스에 선언하는 Class들은 당연히 실제로 존재해야하는 클래스여야 한다.


>Cpu.java

```java
package com.study.testfordi;
import org.springframework.stereotype.Component;

@Component
public interface Cpu {
	public void showBrand();
}
```
- 그냥 인터페이스 클래스다

>Intel.java

```java
package com.study.testfordi;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class Intel implements Cpu{
	public void showBrand() {
		System.out.println("intel");
	}
}
```

- xml과 다른점이라면 편의를 위해서 이름도 바꾸고 brand 변수도 없앴다.
- @Component : @ComponentScan의 대상이 되는 annotation으로 주로 유틸, 지원 클래스등에 붙는다.
- 즉 Component annotation으로 컨테이너에 등록 대상이 되는 클래스다.
- 사실 요번에는 ComponentScan을 사용하지 않으므로 안써도 무방하다.


>Mainboard.java

```java
package com.study.testfordi;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;


@Component
public class MainBoard {
	@Autowired
	private Intel cpu;
	
	public void modelname() {
		cpu.showBrand();
	}
}
```

- @Autowired : 주입 대상이 되는 bean 컨테이너를 찾아 자동으로 주입해주는 annotation
- 한마디로 Intel로 객체가 생성이 됬다면 그 생성된 객체를 MainBoard 객체에 **자동**으로 주입해준다.



>Driver.java

```java
package com.study.testfordi;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Driver {

	public static void main(String[] args) {
		ApplicationContext ac = new AnnotationConfigApplicationContext(ApplicationConfigure.class);
		
		MainBoard m = (MainBoard) ac.getBean("mainboard");
		m.modelname();
		
	}
}
```

- ClassPathXmlApplicationContext가 아닌 AnnotationConfigApplicationContext를 사용한다.
- ()안에는 @Configuration이 붙은 클래스를 넣어주면된다.


<hr>

<br><br>



- 사실 이외에도 수많은 annotation과 위에서 사용된 annotation들도 응용하는 방법이 엄청 많다. 요번에는 이정도로만 정리하고 추후에 필요한 annotation 또는 위에 사용된 annotation에 대해서만 자세하게 포스팅을 하겠다.

<br><br>


xml, annotation DI 차이
--

![annotation](/assets/img/back_end/2020-01-22/annotation.png)

- **xml**로 bean을 만든다면 xml파일이 container에 bean을 생성후 xml파일이 DI까지 해준다.
- **java**클래스 즉 **annotation**을 사용하면 java 파일이 직접 container에 bean을 생성후 java파일이 DI까지 해준다. 즉 xml파일은 필요가 없다.



<br><br>




**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
