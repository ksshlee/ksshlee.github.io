---
title: 인터셉터(Interceptor) & 필터(Filter)
categories : [Spring, Web, SpringBoot]
tags: [Spring, Web, SpringBoot]
toc: true
toc_sticky: true
toc_label: "목차"
---


Filter, Interceptor
--

Spring 혹은 Spring Boot를 활용하여 로그인 기능을 구현하면 사용자 인증과 권한 인증을 확인할 필요가 있다. 추가적으로 어떤 api를 사용하는데 권한을 확인할 때도 있을 것이다.

```java
@RestController
public ExampleController{
    @PostMapping("/login")
    void login(){
        checkLogin();
        checkAuthenticate();
    }

    @GetMapping("/secretInfo")
    void login(){
        checkLogin();
        checkAuthenticate();
    }
}
```

지금은 api가 2개 밖에 없지만 수십개, 수백개의 api가 존재한다면 저렇게 일일이 확인하기엔 너무 비효율적이다.<br>

**그래서** 이런 비효율적인것을 줄이기 위해 3가지의 기술이 존재한다.
1. Filter
2. Interceptor
3. AOP

**AOP**는 추후에 따로 정리하도록 하겠다.


![if](/assets/img/back_end/2020-02-27-1/if.png)

- HandlerMapping에서는 URI 매핑을 확인하고 해당 Handler(Controller)에게 처리 요청을 한다.
- Controller에서 처리중 예외가 발생하면 HandlerExceptionResolver에게 처리를 요청한다.
- [여기](https://ksshlee.github.io/spring/springboot/springmvc/)에서 Spring MVC Flow를 간단하게 확인할 수 있다. 해당 그림은 Filter, Interceptor에 초첨을 둔 그림이다.


>### Filter vs Interceptor

- Filter
  - DipatcherServlet 앞에서 먼저 동작한다.
  - 모든 request, response는 Filter를 거치게 된다.
  - Log, 인코딩, 보안과 같은 web 전역적으로 처리해야 하는 로직들은 Filter로 관리한다.

- Interceptor
  - DipatcherServlet 뒤에서 동작한다.
  - 특정 URI에서만 동작하며 controller 앞단에서 처리를한다.
  - 로그인, 사용자 권한 확인 등과 같은 특정 URI에서만 필요한 로직들을 Interceptor로 관리한다.


<br><br>


Filter
--

>Interface

```java
public interface Filter {
  void init(FilterConfig config);
  void doFilter(ServletRequest request, ServletResponse response, FilterChain chain);
  void destroy();
}
```

- init
  - 처음 시작되면 initialize 하는 메소드다.

- doFilter
  - init 이후 동작하는 메소드다.
  - request, response의 실질적인 로직을 담당하는 메소드다.

- destroy
  - Filter가 종료될때 실행되는 메소드다.


>Filter 등록 (Bean)

- Filter를 등록하는 방법은 2가지가 있다. 첫번째로 Bean 으로 등록하는 방법이다.


**FilterExample.java**

```java
public class FilterExample implements Filter{

    @Override
	public void init(FilterConfig filterConfig) throws ServletException {
		log.info("init FilterExample");
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse res = (HttpServletResponse) response;
		chain.doFilter(req, res);
        System.out.println(req,res);
	}
	
	@Override
	public void destroy() {
		log.info("destroy FilterExample");
	}
}
```


**WebConfigure.java**

```java
@Configuration
public class WebConfigure implements WebMvcConfigurer {
	
	@Bean
	public FilterRegistrationBean getFilterRegistrationBean() {
		FilterRegistrationBean registrationBean = new FilterRegistrationBean(new FilterExample());
		registrationBean.setUrlPatterns(Arrays.asList("/test"));// test 요청으로 오는 Url만 필터가능
		return registrationBean;
	}
}
```

- url 설정은 가능하지만 **특정 Url 필터 제외** 기능은 없다.


>Filter 등록 (Annotation)

- 이 방법은 매우 간단하다.


**FilterExample.java**

```java
@WebFilter(urlPatterns= "/test")
public class FilterExample implements Filter{

    @Override
	public void init(FilterConfig filterConfig) throws ServletException {
		log.info("init FilterExample");
	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
		HttpServletRequest req = (HttpServletRequest) request;
		HttpServletResponse res = (HttpServletResponse) response;
		chain.doFilter(req, res);
        System.out.println(req,res);
	}
	
	@Override
	public void destroy() {
		log.info("destroy FilterExample");
	}
}
```

**TestApplication.java**

```java
@ServletComponentScan // <- 추가
@SpringBootApplication
public class TestApplication {
	public static void main(String[] args) {
		SpringApplication.run(TestApplication.class, args);
	}
}
```



<br><br>

Interceptor
--

>Interface

```java
public interface HandlerInterceptor {
  boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler);
  void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView mav);
  void afterCompletion(HttpServletRequest request, HttpServeletResponse response, Object handler, Exception ex);
}
```

- preHandle
  - controller 처리 전 메소드
  - return false시 controller로 요청을 하지 않는다.

- postHandle
  - controller 처리 이후 메소드

- afterCompletion
  - view 처리 이후 메소드


>Interceptor 등록

**TestInterceptor.java**

```java
public class TestInterceptor extends HandlerInterceptorAdapter {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		log.info("before Handler");
		return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("after Handler");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object object, Exception arg3) throws Exception {
        log.info("after view" );
    }
}
```

**WebConfig.java**

```java
@Configuration
public class WebConfig extends WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new TestInterceptor())
                .addPathPatterns("/board/**")// /board/** 의 url에 대하여 인터셉터를 호출하게 된다.
                .excludePathPatterns("/users/login"); // 제외할 url 패턴
    }
}
```

<br><br>

출처
--
[https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-13-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Filter-%EC%84%A4%EC%A0%95/](https://linked2ev.github.io/gitlog/2019/09/15/springboot-mvc-13-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-MVC-Filter-%EC%84%A4%EC%A0%95/)

[https://elfinlas.github.io/2017/12/28/SpringBootInterceptor/](https://elfinlas.github.io/2017/12/28/SpringBootInterceptor/)




<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
