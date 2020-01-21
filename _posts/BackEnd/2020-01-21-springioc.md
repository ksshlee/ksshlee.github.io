---
title: Spring 이론 (IoC,DI)
categories : [Spring]
tags: [Spring, Web, BackEnd]
toc: true
toc_sticky: true
toc_label: "목차"
---


DI(Dependency Injection)란?
--

### 정의

- DI는 의존성 주입이라는 뜻이다.
- 빈(Bean) 설정파일에서 의존 관계의 정보만 추가해주면 된다. (조립은 Spring Framework가 해준다.)
- 개발자들은 의존성에 신경을 덜 쓸수 있다.
- 높은 재사용성, 조합, 가독성있게 코드를 작성할 수 있다.

>### Example

![DI](/assets/img/back_end/2020-01-21/di.gif)
(아이콘 출처 : flaticon, mynamepong, phatplus)

- cpu, gpu, ram 을 메인보드한테 의존하는 객체라하자
- DI를 통하여 의존성 주입을 하면 부품 객체를 메인보드 객체에 부착할 수 있다.
- 자기가 원하는 부품들을 선택해서 넣을수 있다.

>Code Example

```java
//Not DI
package com.computer;

public class MainBoard{
    private CPU cpu;

    public MainBoard(){
        cpu = New Inteli7();
    }
}
/*문제점
- MainBoard 객체는 Inteli7 객체의 생성을 제어하기 때문에 두 객체간에는 결합이 생긴다. MainBoard객체가 변동되면 Inteli7객체도 변동이왼다.
- 즉 1개의 객체가 바뀌면 다른객체도 바뀐다.
*/



//DI

//Container
<beans>
    <bean id="cpu" class="com.computer.intel">
    <bean id="mainboard" class="com.computer.mainboard">
        <property name="cpu" ref="cpu">
    </bean>
</beans>


//application
package com.computer;

public class MainBoard{
    private Cpu cpu;

    public void setCpu(Cpu cpu){
        this.cpu = cpu;
    }

}

```
- DI는 결합도를 낮추기위해 사용된다.
- **DI를 사용하지 않으면** new Mainboard()를 하면 부품을 바꾸지 못한다.
- **DI를 사용하면** new Mainboard()를 하더라도 Mainboard.setCpu()를 통해 부품을 변경할 수 있다.




### 장점

- 종속성이 감소한다.
- 재사용성이 증가한다.
- 코드가 가독성이 편해진다.





### DI의 유형

**1. Constructor Injection(생성자를 이용한 의존성 삽입)**

```java
public class MainBoard{
    private Cpu cpu;

    public void MainBoard(Cpu cpu){
        this.cpu = cpu;
    }
}
```

**2. Setter Injection (setter를 이용한 의존성 삽입)**

```java
public class MainBoard{
    private Cpu cpu;

    public void setCpu(Cpu cpu){
        this.cpu = cpu;
    }
}
```


**3. Fiel Injection**

```java
public class MainBoard{
    private Cpu cpu;
}
```
- 멤버 변수로 전달

**개발자가 할일은 어떤것을 주입할 건지 (xml,annotation)등으로 작성하는것**


<br><br>







<br><br>

Spring DI 사용
--

**IoC 컨테이너에는 2개의 유형이 있다.**
1. BeanFactory
   - DI의 기본 기능을 탑재

2. ApplicationContext
   - 주로 사용되는 유형
   - BeanFactory를 상속하지만 더 많은 기능들 탑재

<br>


![di2](/assets/img/back_end/2020-01-21/di2.png)

[출처](https://gmlwjd9405.github.io/2018/11/09/dependency-injection.html) 여기에 개념이 정말 잘 정리가 되어 있어서 많이 배웠다.

<br>

>CPU 클래스

```java
package com.computerpart;

public class Cpu{
    public void model();
}
```

>CPU를 상속받은 클래스

```java
//1

package com.computerpart;

public class Intel implements Cpu{
    String brand;
    
    public void setBrand(String brand){
        this.brand = brand;
    }

    public void model(){
        System.out.println(brand + "i7");
    }
}

//2

package com.computerpart;

public class Amd implements Cpu{
    String brand;
    
    public void setBrand(String brand){
        this.brand = brand;
    }

    public void model(){
        System.out.println(brand + "ryzen");
    }
}
```

>MainBoard 클래스

```java
package com.computerpart;

public class MainBoard{
    public Cpu cpu;

    public MainBoard(Cpu cpu){
        this.cpu = cpu;
    }

    public void modelname(){
        cpu.model();
    }
}
```

>Driver

```java
package com.computerpart;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Driver{
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("com/computerpart/beans/computerpart.xml");

        Mainboard m = context.getBean("mainBoard");
        m.modelname();
        context.close();
}
```

>Beans

```java
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.2.xsd">

    <bean id="intel" class="com.computerpart.Intel">
        <property name="brand" value="intel"></property>
    </bean>

    <bean id="cat" class="com.computerpart.Amd">
        <property name="brand" value="amd"></property>
    </bean>

    <bean id="mainboard" class="com.computerpart.MainBoard">
        <constructor-arg ref="intel"></constructor-arg>
    </bean>
</beans>
```

- bean->
  - 이름이 intel인 빈 객체 생성/ Intel객체 생성
    - property->
      - brand라는 property에 intel 값 대입 (set 함수가 존재해야됨)
      - setter 함수를 사용하여 초기값 설정

- constructor->
  - mainboard인 빈객체 생성 / MainBoard객체 생성
    - intel이라는 객체 삽입
    - 생성자를 사용하여 초기값 설정
    - **객체를 삽입할때는 ref, String을 삽입할때는 value**


<br><br>


출처
--
[https://12bme.tistory.com/158?category=682904](https://12bme.tistory.com/158?category=682904)

[https://jins-dev.tistory.com/entry/Spring-DIDependency-Injection-의-정의와-사용](https://jins-dev.tistory.com/entry/Spring-DIDependency-Injection-의-정의와-사용)

[아이콘출처](https://www.freepik.com/free-icon/desktop-computer_700301.htm)


[http://www.nextree.co.kr/p11247/](http://www.nextree.co.kr/p11247/)

[https://gmlwjd9405.github.io/2018/11/09/dependency-injection.html](https://gmlwjd9405.github.io/2018/11/09/dependency-injection.html)

[https://sehun-kim.github.io/sehun/springbean-lifecycle/](https://sehun-kim.github.io/sehun/springbean-lifecycle/)


<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
