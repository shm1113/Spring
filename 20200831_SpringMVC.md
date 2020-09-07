### 프로젝트 : Spring07_HelloMVC,SpringMVC01_Hello , SpringMVC02



# 스프링

**POJO기반의 경량 컨테이너이다 순수 자바객체만 사용해서 만든다는 특징이 있다.**



## 주요특징

### OCP : 필요한건 open 건드리면 안되는 부분은 close해준 형태

### DI/IoC 

### 	Dependency Injection : 값을 주입해주는 것 

### 	Inversion of Control : 결합도를 낮추기 위해서 객체를 생성하는 곳과 생성하는 곳을  나눠주었다

### AOP :관점지향프로그램

##### CC : 주 관심사항(핵심)

##### ccc- 공통 관심사항

-   Joinpoint
-   Pointcut
-   Advice
-   Advisor(Aspect)
-   Weaving



#### :book:  maven(pom.xml) : 빌드 / 배포 관리 툴 , 필요한 jar파일들을 자동으로 다운받아서 dependency에 추가해준다





<br><br>

# MVC시작하기

## 1. dependency 추가

>   pom.xml에 spring-web, spring-webmvc dependency를 추가해준다
>
>   search.maven.org
>
>   mvnrepository.com 사이트

```xml
	<dependencies>
	<!-- TODO : 01. dependency 추가 -->
		<!-- org.springframework.spring-web/ spring- webmvc -->
		<!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>5.2.8.RELEASE</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>5.2.8.RELEASE</version>
		</dependency>
	</dependencies>
```

<br><br>

## 2. ContextLoader Listener  (어떤 xml을 쓸건지를 호출한다)

>   COntextLoader Listener는 web.xml에 설정파일들이 모두 load 되도록 등록할 때 사용되고, 서블릿 이전에 서블릿을 초기화 시켜주며 contextConfigLocation으로 설정파일들의 위치를 지정시켜준다 
>
>   Spring에서는 DispatcherServlet은 그자체가 Servlet이기 때문에 1개 이상의 DispatcherServlet을 설정하는 것이 가능하다

<br>

## 3.DispatcherServer(hello-servlet)

>   **클라이언트의 요청을 전달받아서 컨트롤러에게 클라이언트의 요청을 전달하고 컨트롤러가 리턴한 결과값을 View에 전달하여 알맞은 응답을 생성한다.**

<br>

## 4. handler mapping("/hello.do")통해 controller의 메서드를 찾아온다 

>   클라이언트의 요청을 바탕으로 어떤 Handler(Controller메소드)를 실행할지 결정한다

<br>

## 5. biz호출 (@ Service)

>   #### @Service 
>
>   -   #####  비지니스 로직이 들어간 클래스를 의미한다 

<br>

## 6.Dao(@Repository)호출

>   #### @Repository 
>
>   -   ##### 일반적으로 DAO에 사용되며 DB Exception을 DataAcessException으로 변환한다 

<br>

## 7. Dao에서 return

<br>

## 8. Biz에서 return

<br>

## 9. return 받은 값을 model 객체에 담아서 전달(ModelAndView)



<br><br>



### Model과 ModelAndView 차이

-   **Model**을 사용한 Controller는 **Model객체에 put을 해주고** **리턴해줄때 문자열로 view 이름을 리턴** 하지만

-   **ModelAndView**를 사용한 Controller는 **ModelAndView의 생성자에 view이름과 Model객체를 넣어준 뒤 리턴**해준다.

<br>

### Model

-   파라미터 방식으로 메소드( return 타입 String) 에  Model 인자를 넣어준다 

-   #### `model.addAttribute("key","value")` : 데이터 입력

-   #### `return "경로"`   : VIEW지정

```java
	@RequestMapping("/hello.do")
	public String getHello(Model model) {
		
		// TODO : 09. return 받은 값을 model 객체에 담아서 전달 (ModelAndView)
		// Dispatch Servlet으로 응답해준다 
		model.addAttribute("message",biz.getHello());
		
		return "/WEB-INF/views/hello.jsp";
	}
```



### ModelAndView

-   ModelAndView 객체를 생성한다 

-   Controller 처리 결과 후 응답할 view와 view에 전달할 값을 저장한다 

-   #### `addObject("key",value)`  :  **데이터 입력**

    -   무슨타입가능??

-   #### `setViewName("view")`   :  **화면 지정**

    -   jsp파일경로??

-   #### `return 'ModelAndView객체'`

-   ```java
    	@RequestMapping("/bye.do")
    	public ModelAndView getBye(@RequestParam("name") String nickname) {
    		ModelAndView mav=new ModelAndView();
    		
    		mav.setViewName("/WEB-INF/views/bye.jsp");
    		mav.addObject("message","Bye,"+ nickname);
    		return mav;
    	}
    ```

    

## Eclipse TODO

>   **안보이는 Task 보이는법 :** Preferences - TODO -Task Tags - Enable searching for task tags 체크 
>
>   #### :book:작성 규칙을 동일하게 맞춰야지 정렬되어 출력된다(1,2,3,4,5 / 01,02,03,04,05 )





<br>

## @Autowired

>   적합한 이름의 스프링 빈을 자동으로 주입한다 
>
>   명백하게 1개의 스프링 빈만 존재할 경우 사용한다

## @Controller

>   클라이언트의 요청을 처리한 뒤, 그 결과를 DispatcherServlet에 알려준다 
>
>   비즈니스 로직을 호출하여 처리 결과 ModelAndView 인스턴스를 반환한다.
>
>   -   해당 클래스를 웹 요청을 처리하는 컨트롤러로 사용한다
>
>   -   클래스 타입에 적용 가능하다

## @RequestMapping

>   RequestMapping은 요청에 대해 어떤 Controller, 어떤 메소드가 처리할지를 맵핑하기 위한 어노테이션이다.
>
>   #### :book:메서드 내에서 viewName을 별도로 설정하지 않으면 @RequestMapping의 path로 설정한 URL이 그대로 viewName으로 설정된다 :star:
>
>   #### 속성
>
>   -   #### `path(string,string[])`
>
>       -   요청 uri를 명시한다 배열을 통한 복수개의 요청 URI를 지정할 수 있다.
>
>   -   #### ` value()`
>
>       -   URL 값으로 매핑 조건을 부여(default)
>
>       -   타입 : String[]
>
>       -   ```java
>           @RequestMapping(value="/login")
>           @RequestMapping("/login")
>           ```
>
>   -   #### `method`
>
>       -   Http Request 메소드 값을 매핑 조건으로 부여
>
>       -   타입 : RequestMethod[]
>
>       -   사용 가능한 메소드는 GET,POST,HEAD,OPTIONS,PUT,DELETE,TRACE
>
>       -   ```java
>           @RequestMapping(value="/login",method=RequestMethod.GET)
>           @RequestMapping(value="/login",method=RequestMethod.POST)
>           ```
>
>   -   #### `params()` :HTTP Request 파라미터
>
>       -   같은 URL을 사용하더라도 파라미터에 따라 별도의 작업을 수행한다
>
>       -   사용자가 원하는 매개변수에 값을 매핑하기위해 사용한다
>
>       -   **URL에 포함된 파라미터(GET)만 비교하는건 아니고 POST방식으로 전송한 파라미터도 비교 대상에 포함된다 단, <u>method=RequestMethod.GET/POST를 지정하지 않았을 경우</u>**
>
>       -   **`@RequestParam`**은 생략이 가능하다 <u>**단, 사용자가 입력한 key값과 매개변수의 이름을 비교하여 값을 넣어주기 때문이다**</u>
>
>           ```java
>           @PostMapping("/member")
>           public String member(@RequestParam String name, @RequestParam Int age)
>           
>           @PostMapping("/member")
>           public String member(String name,Int age)
>                
>           @PostMapping("/member/{name}/{age}")
>                public String member(@PathVariable("name") String name, @PathVariable("age") String age)
>           ```
>
>   







## SpringMVC 란

## 특징

## 장점





# 쌉중요

**-****DispatcherServlet** :  (Servlet이 뭐였지??)

클라이언트의 요청을 전달 받는다. 컨트롤러에게 클라이언트의 요청을 전달하고 컨트롤러가 리턴한 결과값을 View에 전달하여알맞은 응답을 생성한다.
 **-****HandlerMapping** (어떤 dispatcher를 사용할지): 

클라이언트의 요청 URL을 어떤 컨트롤러가 처리할지를 결정한다.

RequestURL과 Controller 클래스의 맵핑을 관리한다.
 **-Controller** : 

클라이언트의 요청을 처리한 뒤, 그 결과를 DispatcherServlet에 알려준다.

비즈니스 로직을 호출하여 처리 결과 ModelAndView 인스턴스를 반환한다.
 **-****ModelAndView** : (★★

컨트롤러가 처리한 결과 정보 및 뷰 선택에 필요한 정보를 담는다.
 **-****ViewResolver** : 

컨트롤러의 처리결과를 생성할 뷰를 결정한다.





request.setattribute == Model (Model and view) : 컨트롤러에서 dispatcher로 전달되는 데이터를 처리

## 흐름도

1.  요청이들어오면  

    ```xml
    <listener>
         <listener-class> org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    ```

    listener에서 어떤을 호출해줘야하는지 처리해준다

    ?????????????????????????????????????







## @

/*
 * 	RequestMapping
 * - url을 class 또는 method와 mapping 시켜주는 역할
 * 
 * @RequestParam 
 * - key = value 형태로 넘어오는 queryString(Parameter)을 mapping된 method의 파라미터와 연결
 * 
 * @ModelAttribute
 * -form tag를 통해 넘어온 model을 mapping된 method의 파라미터와 연결
 * 	
 * @SessionAttribute
 * - session 객체에 model 정보를 유지하고 싶을 경우 사용 
 * 
 * */



## 작동원리

listener객체가 들어온 요청이 어떤 xml을 실행시켜야하는지 판단하고  application이냐 hello이냐 라는 것을 찾아주는데 그 이유는 둘중에 하나만 올라와 있을 수 있기 떄문이다 

applicationcontext와 hello는 차이가 있다

controller에서 bye.do 요청이들어오면 handler동작을 통해서 disaptcher를 호출하고 dispatcher는 컨트롤러 - biz - db



#### 값받기 2가지

-   ```java
    @RequestMapping("/hello.do")
    	public String getHello(Model model) {
    		
    		// TODO : 09. return 받은 값을 model 객체에 담아서 전달 (ModelAndView)
    		// Dispatch Servlet으로 응답해준다 
    		model.addAttribute("message",biz.getHello());
    		
    		return "/WEB-INF/views/hello.jsp";
    	}
    ```

-   ```java
    	
    	@RequestMapping("/bye.do")
    	public ModelAndView getBye(@RequestParam("name") String nickname) {
    		ModelAndView mav=new ModelAndView(); // 값을 받아
    		
    		mav.setViewName("/WEB-INF/views/bye.jsp");
    		mav.addObject("message","Bye,"+ nickname);
    		return mav;
    	}
    ```

    





### < Context-param > : context전체에서 사용할 수 있는 파라미터(프로젝트 전체 )

### < servlet >  < servlet -name > < servlet-class > < init-param >  (서블릿이 생성될 때 서블릿 안에서만 사용할 수 있는 Param이된다)





#### :book: Spring mvc프로젝트 생성시 package는 3단계로 하는이유?



![image-20200831123935752](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200831123935752.png)



:book: tomcat이 가지고있던 war파일을 자동으로 가져왔다?





## root-context.xml

>   ​	<!--  모든 다른 웹 컴포넌트와  자원을 공유한다   , 프로젝트 전체에서 공유되는 자원이다  , ex) -->



## servlet-context.xml

>   = dispatcher servlet





#### :book: junit할떄 쓰는거라 신경안써도 된다 src/test/java , src/test/resources

### :book: handeler 로부터 요청받은 dispatcher servlet이 model 과 locale을 controller에 넘겨준다???



## ViewResolver

컨트롤러의 처리결과로 가져올 뷰를 결정한다 

![image-20200831131620069](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200831131620069.png)





## 인코딩 3가지방법

세가지 전부 다 해준다 

-   jsp
-   controller
-   xml  :star: 무조건 해준다
    -   filter 
        -   클라이언트랑 서버가 있다 
        -   클라이언트가 서버에 요청하고 서버가 응답한다
        -   필터는 요청을 서버에서 받기직전에 위치해서 작업한다
        -   ![image-20200831134851528](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200831134851528.png)



## Filter

>   **`Listene`**r의 경우에는 웹 어플리케이션에 발생한 주요 이벤트를 감지하고 **그 이벤트 이전에 특별한 작업을 처리 할 수 있는 것**인데 그에 반해 **`Filter`**는 **사용자에게 온 요청을 Servlet에게 전달하기 전에 특별한 처리를 할 수 있도록 해준다**



### :book:src/main/webapp   = (servlet) webcontent 







## get방식과 post를 다르게 만들때





## 요청변수명과 받는 변수명이 다를때

>   @RequestParam을 사용한다 다른애들은 requestParam이 생략된것이다



## modelandview

model and view가 string을 리턴하면 resolver로 가서 결과 뷰를 보여주는데 

만약에 String을 주지 않으면 @RequestMapping()으로 넘어온 mapping주소를 찾아간다 





# 오류 : namespaces가 안나타날때

maven전부다 삭제하고 다시 dependency추가하기







# 결론



## 핸들러매핑과 리퀘스트 매핑 차이





## Spring MVC 흐름 (request ->?->response)



## ??? =>> 객체들 , 어노테이션



## url mapping 하는방법



#### :book:public String만 있는건 아니지만 주로 쓸거다



Spring이뭔가요

Spring MVC흐름, 객체, ... 설명시킨다