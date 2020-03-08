---
title: \[Java\] static, final
categories : [Java]
tags: [Java]
toc: true
toc_sticky: true
toc_label: "목차"
---


Static 멤버
--

### non static vs static

- non static
  - **인스턴스 멤버**라고 부른다.
  - 객체가 생길때 생성이 된다.
  - 객체가 사라지면 사라진다.

- static
  - **클래스 멤버**라고 부른다.
  - 객체 내부가 아닌 별도의 공간에 생성된다.
  - 객체를 생성하지 않고도 사용할 수 있다.
  - 객체가 사라져도 static 멤버는 사라지지 않는다.
  - 프로그램이 종료될때 사라진다.
  - **동일한 클래스의 객체들에 공유된다.**

### static 메모리 영역

![static](/assets/img/programmingskill/2020-03-08/static.png)

- 모든 클래스들은 static영역에 있다.
- new() 로 객체를 생성하면 Heap영역에 객체를 만들게 된다.
- Garbage Collector는 Heap 영역만 관리한다. (Static 영역은 관리하지 않는다)
- 그래서 Static 영역은 프로그램이 종료 될때까지 메모리에 할당된 채로 존재한다.
- 즉 무분별한 static 사용은 메모리 퍼포먼스에 악영향을 주게 된다.


### static 사용 예시

> static 변수

```java
public class Family{
    String address = "경기도" ;

    public static void main(String[] args){
        Family me = new Family();
        Family sister = new Family();
    }
}
```
![static](/assets/img/programmingskill/2020-03-08/static2.png)


- 위와 같이 생성한다면 같은 "경기도"라는 값을 갖고 있어도 서로 다른 메모리 주소를 갖는다.
- 즉 **메모리를 별도로 할당한다.**

```java
public class Family{
    static String address = "경기도" ;

    public static void main(String[] args){
        Family me = new Family();
        Family sister = new Family();
    }
}
```

![static](/assets/img/programmingskill/2020-03-08/static3.png)


- 하지만 이렇게 "static" 선언을 하게 되면 **메모리를 별도로 할당하지 않아도 된다.**
- 즉 Family 객체를 생성하게 되면 address는 저 static address가 할당된 메모리를 가르키게 된다.



> static 메소드

![static](/assets/img/programmingskill/2020-03-08/static4.png)

- 메소드가 static이 아니라면
- User 클래스에서 Code 클래스의 메소드를 사용하고 싶다면 Code 생성자로 객체를 생성한 후 사용해야한다.


![static](/assets/img/programmingskill/2020-03-08/static5.png)

- 메소드가 static으로 선언되었다면
- User 클래스에서 따로 객체를 생성하지 않고 사용할 수 있다.
- 단 반드시 해당 메소드는 **public**이어야 한다.



### static 메소드 제약

> 1 static 변수

```java
public class StaticTest {
    private int num = 1;
    private static int num2 = 2;


    public static void printName(){
        int num3 = 3;
       //System.out.println(num); 사용 불가능
       System.out.println(num2);
       System.out.println(num3);
    }
}
```

- static 메소드는 static 변수만 사용이 가능하다.
- 하지만 해당 메소드 안에 생성된 변수는 static이 아니여도 된다.


> 2 this 사용 불가

```java
public class StaticTest {
    private static int num = 1;

    public static void printName(){
       //System.out.println(this.num); this 자체를 사용 불가능
       System.out.println(num);
    }
}
```

- static 메소드에선 this가 사용이 불가능하다.

<br><br>


final
--

- final 키워드는 해당 변수, 클래스, 메소드를 한번만 할당한다.
- 처음 할당한 후 **재할당**하면 **오류**가 발생한다.
- READ ONLY인 키워드
- 클래스, 메소드, 변수가 변하지 못하도록 하고 싶으면 final로 선언하면 된다.
- final로 생성한다면 **상속이 되지 않는다**.

### final 사용

> 1 클래스

```java
public final class FinalTest{}

public class FinalTest2 extends FinalTest{} // error
```

- final로 클래스를 선언하면 해당 클래스에 대한 상속을 제한 할 수 있다.

> 2 메소드

```java
public final void finalMethod(){}

public static final void staticFinalMethod(){}//static final method
```

- 메소드 역시 상위 클래스에서 final로 선언한다면 오버라이딩이 **불가능**하다.
- 아무리 해당 메소드가 static이여도 final로 선언한다면 오버라이딩 불가능

> 3 변수

```java
final String name = "이상혁";
```


<br><br>


출처
--
[https://wikidocs.net/228](https://wikidocs.net/228)

[https://mangkyu.tistory.com/47](https://mangkyu.tistory.com/47)

[https://blog.lulab.net/programming-java/java-final-when-should-i-use-it/](https://blog.lulab.net/programming-java/java-final-when-should-i-use-it/)

<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
