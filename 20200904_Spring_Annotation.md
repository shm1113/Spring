## @Component 

-   해당 클래스를 bean으로 등록할거라는 의미이다 
-   해당 클래스의 첫글자를 소문자로 변경한 이름으로 bean이 생성된다
-   **`@Component("이름")`**
    -   원하는 이름으로 Bean을 생성해줄 수도 있다.

## @Autowired 

-   bytype을 먼저찾고 byname으로 찾아준다 필요한 bean을 자동으로 가져와준다
-   **값이 두개인 경우는 사용할 수 없다**
-   bean을 만들고 ref속성으로 가져와서 참조타입 변수에 값을 넣어주던 것을 해당 참조변수 위에 @Autowired어노테이션을 입력만하면 해당 클래스타입에 해당하는 객체를 자동으로 생성해서 값을 넣어준다

## @Qualifier() 

-   중복되는 객체가 존재할 떄 특정 객체를 연결해줄 수 있는 어노테이션

-   ```java
    @Autowired
    	@Qualifier("kim")
    	private Student student;
    
    ```

    

### :book:annotation은 ​ 바로 밑에 것만 적용된다





## < context:component-scan >

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">


<context:component-scan base-package="com.test01.anno"></context:component-scan>

</beans>

```

>   **<context:component-scan**  : 해당 basepackage를 쭉 훑어서 어노테이션을 읽어서 bean으로 만들어줄거다 
>
>   모든 어노테이션을 
>
>   -   @Component라는 걸 달고있는애들을 전부 객체로 만들어준다 
>       -   **형태** 
>           -   < bean id="nickName" class="com.test01.anno.NickName"/ >
>   -   첫글자를 소문자로 바꾼 id값으로 bean을 만들어준다 
>   -   applicationContext라는 컨테이너안에서 만들어진다
>
>   base-package : java 소스가 들어있는 폴더를 선택해서 해당 폴더를 전부 스캔한다
>
>   bean의 등록 여부와 관계없다. 스프링이 알아서 bean 스캔을 통해 Annotation을 해석하고 활성화한다.
>
>   @Autowired와 @Qualifier 뿐만 아니라 @Service, @Component, @Controller, @Repository 등 모든 클래스를 스캔하고
>
>   bean을 작성한다.
>
>   따라서 이 태그를 사용하면 < context:annotation-config /> 태그는 사용할 필요가 없다.
>
>   다만 이 경우 base-package를 통해 스프링이 스캔할 패키지 위치를 지정해두는 것이 일반적이다.







## < context:annotation-config />

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

	<context:annotation-config />
</beans>

```

>   이미 등록된 bean에 대해서만 Annotation을 활성화한다. 
>
>   어딘가에 bean을 등록해놓으면 @Autowired와 @Qualifier Annotation을 해석해서 가져다 쓰겠다는 의미이다. 
>
>   @Autowired와 @Qualifier 두가지만 해결한다 

