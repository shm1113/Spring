# Filter , Interceptor , AOP

>   세가지 기능이 모드 비슷한 기능을 하는데, 이들이 필요한 이유는 프로그램을 만들거나 작업을 할 때 공통되는 부분이 굉장히 많다
>
>   예를 들어 , 인증 처리라던지, XSS로부터 보안처리,로깅 처리, 페이지 인코딩도 포함된다 
>
>   **위에 세가지는 프로그램 내에서 자주 사용되는 공통 기능들을 빼서 관리한다는 공통점이 있다.**
>
>   ### ![image-20200907185450231](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200907185450231.png)
>
>   1.  서버를 실행시키면 Servlet이 올라오는 동안에 init이 실행되고, 그 후 doFilter가 실행된다
>   2.  컨트롤러에 들어가기 전 preHandler가 실행된다.
>   3.  컨트롤러에서 나와 @Around,postHandler, doFilter 순으로 진행된다.
>   4.  서블릿 종료시 destroy가 실행된다 
>
>   
>
>   -   순서는 Filter -> Interceptor -> AOP -> interceptor -> Filter 순으로 이루어진다.
>   -   Filter와 Interceptor은 Servlet 단위에서 이루어진다



<br><br>



# Spring Filter

>   Dispatcher Servlet 영역에 들어가기 전 Front Controller 앞 범위에서 수행된다.
>
>   또한, Controller 이후 자원 처리가 끝난 후 응답 처리에 대해서도 변경,조작을 수행할 수 있다.
>
>   #### 사용되는 곳
>
>   -   ##### 인코딩 처리 , XSS 방어 , 로그인 여부확인
>
>   #### &#127752;Filter 실행메소드
>
>   -   init() - 필터 인스턴스 초기화
>   -   doFilter() - 실제 처리 로직
>   -   destroy() - 필터 인스턴스 종료

<br><br>



# Spring Interceptor

>   Filter는 스프링 컨텍스트 이전에 실행되어 스프링과 무관하다 
>
>   하지만 인터셉터의 경우에는 스프링의 **`DispatcherServlet이`** Controller를 호출하기 전 ,후에 끼어들기 때문에 스프링 컨텍스트(Context영역)내부에서
>
>   Controller에 관한 **`요청`** 과 **`응답`**에 관여한다.
>
>   그리고 스프링의 모든 @Bean에 접근이 가능하다. 
>
>   #### 사용되는 곳
>
>   -   ##### 로그인 체크, 권한체크, 프로그램 실행시간 계산,로그확인,업로드, 파일처리등
>
>   #### &#127752;Interceptor 실행메소드
>
>   -   preHandler() - Controller 실행 전
>   -   postHandler() - Controller 실행 후 view Rendering 실행 전
>   -   afterCompletion() - view Rendering 이후

<br><br>



# Spring AOP

>   Controller 처리 이후 주로 비지니스 로직에서 실행된다.
>
>   주로 **'로깅','트랜잭션','에러 처리' 등** 비지니스 단의 메서드에서 구체적인 조정이 필요할 때 사용한다
>
>   Filter와 Interceptor와 달리 메소드 전후 지점에서 자유롭게 설정이 가능하다.
>
>   AOP는 주소, 파라미터, 어노테이션 등 다양한 방법으로 대상을 지정할 수 있다.
>
>   #### @Advice와 HandlerInterceptor의 가장 큰 차이는 파라메터의 차이다.
>
>   @**`Advice`**의 경우에는 JoinPoint와 ProceedingJoinPoint 등을 활용하여 호출 가능하다. 
>
>   반면 **`HandelrInterceptor`**의 경우는 HttpServletRequest, HttpServletResponse를 파라메터로 사용한다 
>
>   #### &#127752;AOP의 포인트컷
>
>   -   ##### @Before: 대상 메서드의 수행 전
>
>   -   ##### @After: 대상 메서드의 수행 후
>
>   -   ##### @After-returning: 대상 메서드의 정상적인 수행 후
>
>   -   ##### @After-throwing: 예외발생 후
>
>   -   ##### @Around: 대상 메서드의 수행 전/후
>
>   
>
>   



<br><br>



## command객체

>   폼 요청을 처리하는 컨트롤러는 각 파라미터의 값을 구하기 위해 HttpServletRequest객체를 이용할 수 있다. 그렇지만 처리해야 할 파라미터가 늘어나면 늘어날 수록 작성해야 할 코드가 많아지고 이를 보완하기 스프링은 커맨드 객체를 지원한다
>
>   http요청 파라미터의 이름을 이용한 setter메소드를 작성한 클래스를 만들고, 해당 클래스의 객체(커맨드 객체)를 메소드의 파라미터 값으로 넣어주면, 스프링은 요청 파라미터의 값을 커맨드 객체에 담아준다 
>
>   ex) name파라미터를 받기위해서는 setName()메소드가 필요하다
>
>   ![image-20200907195756014](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200907195756014.png)

<br><br>



## Filter추가하는법

>   -   ### web.xml에서 추가하는 법
>
>       **< filter-name >** :필터 별칭 설정
>
>       **< filter - class >** : 패키지 이름을 포함한 필터 클래스 작성
>
>       **< init-param >** : 필터가 사용할 정적 데이터 설정
>
>       **< filter-mapping >** : 필터를 사용할 URL매핑
>
>       **< filter-name >** : 필터 별칭 지정
>
>       **< url-pattern >** : 필터가 적용되어야 하는 URL 지정 /*와 같이 지정하면 모든 요청에 대해 필터 적용 
>
>   ```xml
>   <filter>		<!-- 객체만들고 -->
>   		<filter-name>logFilter</filter-name>
>   		<filter-class>com.mvc.upgrade.common.filter.LogFilter</filter-class>
>   </filter>
>   
>   <filter-mapping> <!-- 연결 -->
>   		<filter-name>logFilter</filter-name>
>   		<filter>/*</filter>
>   </filter-mapping>
>   	
>   ```
>
>   -   ### 어노테이션에 배치 정보 설정
>
>   필터 클래스에 위와 같이 @WebFilter 어노테이션을 추가한다.
>
>   urlPatterns에 지정해야 하는 값은 위 web.xml로 설정하는 것에서 < filter-mapping >부와 동일하다 
>
>   이렇게 필터를 구현하고 적용하면 POST요청에서 매개변수를 꺼내는 부분에서 setCharacterEncoding() 호출부를 모두 제거해도 한글이 깨지지 않는다.
>
>   ```java
>   @WebFilter(
>   	urlPatterns="/*",	//<filter-mapping>와 동일
>   	initParams={
>   		@WebInitParam(name="encoding",value="UTF-8")
>   	}
>   )<br><br>
>   ```

<br><br>

## Filter클래스 만드는법

-   #### 순서

1.  클래스를 생성한다
2.  javax.servlet.Filter인터페이스를 implements한다 
3.  init() , doFilter() , destroy() 를 구현한다

-   #### init()

>   필터 객체가 생성되고 준비 작업을 위해 딱 한번 호출된다. **이 메소드의 매개변수는 FilterConfig의 인스턴스**이다. 이 인스턴스를 통해 **필터 초기화 매개변수의 값을 꺼낼수 있다**. 위 모드에서는 doFilter)에서 사용하기 위해 인서턴스 변수 config에 저장한다

-   #### doFilter()

>   필터와 매핑된 URL에 요청이 들어올때마다 doFilter()가 호출된다. 이 메소드에 필터가 할 작업을 작성한다 **`filterChain`**은 다음 필터를 가리키고 **`FIlterChain.doFilter()`**는 다음 필터를 호출한다 다음 필터가 없다면 내부적으로 서블릿의 service()를 호출한다

서블릿이 실행되기전 처리할 작업  = filterChain.doFilter()이전에 /  서블릿이 실행된 후에 처리할 작업은 filterChain.doFilter() 이후에 작성한다.

-   #### destroy()

>   서블릿 컨테이너는 웹 어플리케이션을 종료하기 전에 필터들에 대해 destroy()를 호출해서 마무리 작업을 한다. 특별히 마무리할 작업이 없으면 빈 메소드로 둔다 . 

```java
package com.mvc.upgrade.common.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;

public class Logfilter implements Filter {

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		// TODO Auto-generated method stub

	}

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)	//
			throws IOException, ServletException {
		HttpServletRequest req=(HttpServletRequest) request;
		
		String remoteAddr=req.getRemoteAddr();
		String uri=req.getRequestURI();
		String url=req.getRequestURL().toString();
		String queryString=req.getQueryString();
		
		String referer=req.getHeader("referer");
		String agent= req.getHeader("User-Agent");
		
		StringBuffer sb=new StringBuffer();
		sb.append("* remoteAddr : " +remoteAddr + "\n")
		.append("* remoteAddr : " + remoteAddr+"\n")
		.append("* uri : " + uri + "\n")
		.append("* url : " + url + "\n")
		.append("* queryString : " + queryString + "\n")
		.append("* referer : " + referer + "\n")
		.append("* agent : " + agent);
		
		System.out.println("LogFilter");
		System.out.println(sb);
		
		chain.doFilter(req, response);
	}

	@Override
	public void destroy() {

	}

}

```





<br><br>



## Filter의 구동 과정

1.  서블릿 컨테이너는 웹 어플리케이션을 시작할 때 DD파일(web.xml)에 등록된 인스턴스를 생성하고 init()을 호출한다 
2.  클라이언트 요청이 들어오면 해당하는 필터의 doFilter()를 호출한다.
3.  doFilter()에서 작업을 실행하고 다음 필터의 doFilter()를 호출한다 
4.  마지막 필터까지 ③을 반복한다
5.  마지막 필터는 서블릿의 service()를 호출한다
6.  서블릿의 service()가 끝나면 service()를 호출했던 이전 필터로 돌아간다.
7.  반복해서 제일 처음 호출 됐던 필터까지 돌아간다
8.  마지막으로 클라이언트에게 응답 결과를 보낸다

<br><br>

## 필터 적용 사례

| 사전 작업(서블릿 실행전) | 사후 작업(서블릿 실행 후) |
| ------------------------ | ------------------------- |
| 문자 집합 설정           | 응답 데이터 압축          |
| 압축 해제                | 응답 데이터 암호화        |
| 암호화된 데이터의 복호화 | 응답 데이터 형식 변환     |
| 로그 작성                |                           |
| 사용자 검증              |                           |
| 사용자 권한 확인         |                           |








