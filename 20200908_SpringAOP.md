#### :book: 같은 dependency가 두개 있을 때 서로 버전이 다르면 충돌이 난다

#### :book: 용량이 많은 syso보다 logger를 쓴다 

#### :book: bean의 name속성과 id속성은 크게 다르지 않다









## Logger

>   프로그램 개발이나 운영 시 발생하는 문제점을 추적 하거나 운영 상태를 모니터링 하는 정보를 기록하는 것 
>
>   스프링에서는 기본적으로 commons.logging 라이브러리(Apache의 JCL,Jakarta Commons Logging)을 사용한다
>
>   예전에는 스프링에서 로그를 남길 때 Log4J를 사용했었는데, 성능 및 기능상의 이유로 대체 logger들이 많아졌고 현재는 대부분 SLF4J인터페이스를 구현한 Logback을 사용한다.
>
>   ### slf4j란
>
>   **Simple Logging facade For java의 약자로서 자바 로깅 시스템을 쉽게 사용할 수 있도록 해주는 라이브러리이다**
>
>   #### 특징
>
>   -   기존에 사용하는 로깅 시스템을 교체하고 싶을 때, 소스 코드를 수정하지 않고도 maven이나 gradle의 의존성 설정만 바꾸면 손쉽게 적용할 수 있다
>
>   ### log4j란
>
>   **log for java의 약자로서 로깅을 위한 라이브러리이다 . 로깅라이브러리를 별도로 사용하지 않으면 system.out.println()을 사용해야하는데 이는 리소스낭비이다.**
>
>   -   system.out.println()에 비해 속도가 훨씬 좋다
>   -   멀팃레드 환경에서 사용해도 안전하다
>   -   계층적인 로그 설정과 처리가 가능하다
>   -   출력을 콘솔, 파일 , 원격,email.DB등 다양하게 가능하다



## Log4j의 구조

| 요소     | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| Logger   | 출력할 메세지를 Appender에 전달한다                          |
| Appender | 전달된 로그를 어디에 출력할 지 결장한다(콘솔 출력,파일 기록,DB 저장 등) |
| Layout   | 로그를 어떤 형식으로 출력할 지 결정한다                      |

## Log4j

| 로그 레벨 | 설명                                                         |
| --------- | ------------------------------------------------------------ |
| FATAL     | 아주 심각한 에러가 발생한 상태를 나타낸다                    |
| ERROR     | 어떠한 요청을 처리하는 중 문제가 발생한 상태를 나타낸다      |
| WARN      | 프로그램의 실행에는 문제가 없지만 ,향후 시스템의 에러의 원인이 될 수 있는 경고성 메시지를 나타낸다 |
| INFO      | 어떠한 상태변경과 같은 정보성 메시지를 나타낸다              |
| DEBUG     | 개발시 디버그 용도로 사용하는 메시지를 나타낸다              |
| TRACE     | 디버그 레벨이 너무 광범위한것을 해결하기 위해서 좀 더  상세한 이벤트를 나타낸다. |





## LogAop클래스만들기

```java
package com.mvc.upgrade.common.aop;

import org.aspectj.lang.JoinPoint;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogAop {

		public void beforeLog(JoinPoint join) {	//org.aspect.lang
			Logger logger=LoggerFactory.getLogger(join.getTarget()+"");  //org.slf4j  // class : class타입의 변수다(예약어class를사용할수없기떄문에)
			logger.info("---------------AOP Start-----------------------");
			
			Object[] args=join.getArgs();
			if(args!=null) {
				logger.info("method :" +join.getSignature().getName());
				for(int i=0;i<args.length;i++) {
					logger.info(i + "번째  :" + args[i]);
				}
			}
		}
		/*
		 * getTarget() : 누구를 호출하고 있는지 , 대상객체
		 * getArgs() :   대상 파라미터들
		 * getSignature() :   대상 메서드 정보
		 * */
	
		
		public void afterLog(JoinPoint join) {
		}
		
		public void afterThrowingLog(JoinPoint join) {
			
			
		}
}

```



## aop-context.xml 만들기

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.1.xsd">

	
		<bean name="logAop" class="com.mvc.upgrade.common.aop.LogAop"/>
		
		<!-- pointcut : execution(public * com.mvc.upgrade.model.dao.*Dao*(..))-->
		
		<aop:config>
			<aop:pointcut expression="execution(public * com.mvc.upgrade.model.dao.*Dao*.*(..))" id="daoPoint"/>
			<aop:aspect ref="logAop" id="logAop">
				<aop:before method="beforeLog" pointcut-ref="daoPoint"/>
				<aop:after method="afterLog" pointcut-ref="daoPoint"/>
				<aop:after-throwing method="afterThrowingLog" pointcut-ref="daoPoint"/>
			</aop:aspect>
		</aop:config>





</beans>

```



## web.xml에 aop.context.xml을 추가해주었다

![image-20200908125117683](C:\Users\shm11\AppData\Roaming\Typora\typora-user-images\image-20200908125117683.png)





## context-param

>   해당 context전체에 적용되는 파라미터이다



## init-param

>   해당 DispatcherServlet안에서만 사용되는 context
>
>   *.do를 통해서 dispatcher가 동작할 때만 < init-param > 안에 있는 것들이 실행된다



## 필터와 AOP의 차이점??

필터는 클라이언트와 서버사이에서 동작처리

AOP는 자바클래스안에서 동작하면서 공통관심사항과 주관심사항을 분리해서 원하는 시점에 붙여 실행할수 있는것