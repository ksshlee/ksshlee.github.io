---
title: Spring Security
categories : [Spring, BackEnd]
tags: [Spring, SpringSecurity]
toc: true
toc_sticky: true
toc_label: "목차"
---


Spring Security?
--

- 스프링 기반 어플리케이션의 보안(인증, 권한, 세션관리)을 담당하는 **프레임워크**다.
- Spring Security는 **filter**를 기반으로 동작한다.
- Spring MVC 와 **분리**되어 관리된다.

### 용어

- Principal (접근 주체)
  - 보호된 대상에 접근하는 유저
  - 즉 User라고 생각하면 된다.
- Authenticate (인증)
  - 접근하는 대상이 누구인지 확인
  - 로그인과 같은 행위
- Authorize (인가)
  - 접근한 유저의 권한을 검사
  - admin인지 일반 user인지 검사




<br><br>


Spring Security Architecture
--
<span id="here"></span>

>Authenticate architecture(인증 아키텍쳐)

![architecture](/assets/img/back_end/2020-02-17/springsecurity.png)

- 1) AuthenticationFilter
  - request를 받으면 첫번째 filter에서 입력값이 null인지 체크를 해준다.
- 2) Authentication Token
  - Filter에서 통과되면 인증용 객체(토큰)를 생성한다.
- 3) AuthenticationManager
  - Manager는 실제로 인증을 시행하는 Provider에게 위에서 생성한 객체를 전달한다.
- 4) AuthenticationProvider
  - provider는 전달받은 객체를 UserDetailsService에 넘겨준다.
- 5) UserDetailsService
  - 전달받은 객체를 db에서 조회하고 결과를 UserDetails 로 반환해준다.
  - 이게 7번까지
- 6) SecurityContextHolder
  - 쭉쭉 넘어가서 spring security 인메모리 세션저장소인 SecurityContextHolder 에 저장해준다.
  - Authenticaiton 객체를 보관한다.
  - 이 SecurityContextHolder에 Authenticaiton을 넣고 꺼낸다.




>Filters

![filter](/assets/img/back_end/2020-02-17/filter.png)

- 하나의 FilterChainProxy로 등록되어 있지만 실제로 여러개의 filter로 구성되어있다.
- 이 필터들은 SessionManagementFilter, LogoutFilter 등등 11개의 필터들로 구성되어져있다.

<br><br>



Spring Security 핵심 Class
--

>### SecurityConfig.class

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    private UserService userService;


    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
    @Override
    public void configure(WebSecurity web) {
	  //example
      web.ignoring().antMatchers("/css/**", "/js/**", "/img/**", "/lib/**");
	}

    @Override
    protected void configure(HttpSecurity http) throws Exception {
	 //example
     http.authorizeRequests()
      .antMatchers("/admin/**").hasRole("ADMIN")//ADMIN 권한 갖은 사람에게 허용
                .antMatchers("/board/create").hasRole("MEMBER")//MEMBER 권한 갖은 사람에게 허용
                .antMatchers("/**").permitAll()
            .and() // 로그인 설정
                                .formLogin()
                .loginPage("/user/login")
                .defaultSuccessUrl("/")//로그인 성공 Url
                .permitAll()//모든 사람들에게 권한 허용
            .and() // 로그아웃 설정
                               .logout()
                .logoutRequestMatcher(new AntPathRequestMatcher("/user/logout"))
                .logoutSuccessUrl("/")//로그아웃 성공 url
                .invalidateHttpSession(true)//세션 제거
	}

    @Override
    protected void configure(AuthenticationManagerBuilder auth) {
      //example
      auth.userDetailsService(userService).passwordEncoder(passwordEncoder());
    }
}
```
- @EnableWebSecurity, @Configuration
  - 해당 어노테이션으로 SpringConfig 파일임을 명시해준다.
- 대부분 WebSecurityConfigurerAdapter 클래스를 상속받는다.


>passwordEncoder()

- Spring Security에서 제공해준다.
- 비밀번호를 암호화해주는 객체
- 회원가입과 같은 클래스에서도 사용할 수 있게 Bean으로 등록해준다.

>configure(WebSecurity web)

- WebSecurity는 FilterChainProxy를 생성하는 필터다.
- 해당 메소드는 인증을 무조건 통과하는 파일들을 설정해두는 메소드다.

>**configure(HttpSecurity http)**

- 가장 중요한 메소드다
- 페이지 또는 url별로 접근 권한을 설정해줄수 있다.
- 이부분은 매우 중요하므로 정교하게 설정을 해야된다.


>configure(AuthenticationManagerBuilder auth)

- 모든 인증은 AuthenticationManager를 통해 이루어진다.
- <a href="#here">여기</a>에서 4번에 해당된다.



> ### UserService.class

```java
@Service
public class UserService implements UserDetailsService {

    private UserDAO userDAO;


    public Long createUser(MemberDto memberDto) {
        // 비밀번호 암호화
               BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
        memberDto.setPassword(passwordEncoder.encode(memberDto.getPassword()));

        return userDAO.save(memberDto);
        }



    @Override
    public UserDetails loadUserByUsername(String id) throws UsernameNotFoundException {
       
       UserDTO user = userDAO.findbyId(id);

       if (user == null){
           throw new UsernameNotFoundException(id);
       }

       List<GrantedAuthority> authorities = new ArrayList<>();

        if (("admin@admin.com").equals(id)) {
            authorities.add(new SimpleGrantedAuthority(admin 권한 부여);
        } else {
            authorities.add(new SimpleGrantedAuthority(member 권한 부여);
        }

        return new User(user.getId(), user.getPassword(), authorities);
    }

}
```

>createUser

- 회원가입하는 메소드다
- BCryptPasswordEncoder를 통하여 암호화를 한후 DB에 저장한다.



>loadUserByUsername(String id)

- UserDetailsService에 존재하는 메소드다.
- new User(아이디,비밀번호,권한) 을 return 해주면된다.
- 이 User 클래스 역시 org.springframework.security.core.userdetails.User 정의되어있다.
- 위의 class를 implements하여 커스터마이징을 할수 있다.


> ### Authentciation.class

```java
public interface Authentication extends Principal, Serializable { 
    Collection<GrantedAuthority> getAuthorities(); // Authentication 저장소에 의해 인증된 사용자의 권한 목록 
    String getName();//사용자 이름
    Object getCredentials(); // 주로 비밀번호 
    Object getDetails(); // 사용자 상세정보 
    Object getPrincipal(); // 주로 ID 
    boolean isAuthenticated(); //인증 여부 
    void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException; 
}
```

<br><br>



로그인한 사용자 정보 가져오기
--

> SecurityContextHolder 이용

```java
Object principal1 = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
UserDetails userdetails = (UserDetails) principal1;
String un = userdetails.getUsername();
String pw = userdetails.getPassword();
```

- 상황에 따라 password는 제공이 안될수도 있다.


> Controller 에서 이용

```java
//1

@GetMapping("/principle")
  public String principal(Principal principal) {

    return principal.getName();
}


//2
@GetMapping("/authentication")
public String authentication(Authentication authentication) { 
    UserDetails userDetails = (UserDetails) authentication.getPrincipal(); 
    return userDetails.getUsername(); 
    }
```


> UserDetails 확장


>Class

```java
@Data 
public class UserDetailsCustom implements UserDetails { 
    // UserDetails 상속 받아 클래스 생성
    }
```

- UserDeatils를 상속 받아서 새로운 클래스 작성


>Service

```java
@Service
public class UserService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        //임시로 작성한 로직이다.
        UserDetailsCustom user = userService.find(username);

        return user
```

- UserDetails를 custom한 클래스를 return

> Controller

```java
@Controller
public class MainController{
    @GetMapping("/userInfo")
    public String userInfo(@AuthenticationPrincipal  UserDetailsCustom user){
        String username = user.getUsername();
    }
}
```

- @AuthenticationPrincipal로 커스텀한 클래스 선언

<br><br>





출처
--
[https://sjh836.tistory.com/165](https://sjh836.tistory.com/165)

[https://docs.spring.io/spring-security/site/docs/current/guides/html5/helloworld-boot.html](https://docs.spring.io/spring-security/site/docs/current/guides/html5/helloworld-boot.html)

[https://itstory.tk/entry/Spring-Security-%ED%98%84%EC%9E%AC-%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%9C-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EB%B3%B4-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0](https://itstory.tk/entry/Spring-Security-%ED%98%84%EC%9E%AC-%EB%A1%9C%EA%B7%B8%EC%9D%B8%ED%95%9C-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%A0%95%EB%B3%B4-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)

[https://www.slideshare.net/madvirus/ss-36809454](https://www.slideshare.net/madvirus/ss-36809454)

<br><br>



**혹시 제가 잘못 알고 있거나 오타, 궁금한점 있으시면 댓글 남겨주시면 감사겠습니다!**
