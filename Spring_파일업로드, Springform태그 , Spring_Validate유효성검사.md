#### :book: 패키지구성을 암묵적으로 만든다  -(기관성격) .  -(커다란프로젝트) . -(세부프로젝트)

### 









# 파일업로드

## pom.xml 추가

```xml
	<!-- 추가 : commons-io, commons-fileupload  -->
		<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
		<!-- 아파치 소프트웨어 재단에서 제공하는 자바 기반의 io관련 오픈소스(io를 제공하는 유틸리티 컴포넌트) -->
		<dependency>
			<groupId>commons-io</groupId>
			<artifactId>commons-io</artifactId>
			<version>2.8.0</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
		<!-- 파일 업로드를 할 때 필요한 라이브러리 -->
		<dependency>
			<groupId>commons-fileupload</groupId>
			<artifactId>commons-fileupload</artifactId>
			<version>1.4</version>
		</dependency>
```



## applicationContext로 변경 및 bean 추가 및 web.xml 수정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- Root Context: defines shared resources visible to all other web components -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- 화면 단에서 multipart/form-data방식으로 서버에 전송되는 데이터를 스프링 MVC multipartResolver로 처리 -->
		<property name="maxUploadSize" value="10000000"></property>
			<!-- 들어갈 수 있는 최대 파일 크기 -->
		<property name="defaultEncoding" value="UTF-8"></property>
			<!-- 파일 인코딩을 UTF-8로  -->
	</bean>
</beans>

```

```xml
		<!-- The definition of the Root Spring Container shared by all Servlets and Filters -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/appServlet/applicationContext.xml</param-value>
	</context-param>
```



## filter추가 (url-pattern은 그대로)

```xml
	`<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```



## index.jsp생성

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
		<c:redirect url="form"></c:redirect>  <!-- form이라는 url로 redirect 시켜줌-->
</body>
</html>
```



## upload.jsp생성

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<h1>Upload Page</h1>
	
	<form:form method="post" enctype="multipart/form-data" modelAttribute="uploadFile" action="upload">
	file<br/>
	<input type="file" name="mpfile"/><br/>
		<p style="color:red; font-weight: bold;">
	<form:errors path="mpfile"/></p><br/>
		<!-- 지정한 프로퍼티와 관련된 한 개 이상의 에러 메세지를 출력한다  -->
	
	description<br/>
	<textarea rows="10" cols="40" name="desc"></textarea><br/>
	<input type="submit" value="send"/>
	</form:form>
		<!-- 
		-- enctype : 전송되는 데이터 형식을 설정한다.
			1. application/www-form-urlencoded : 문자들이 encoding 됨( default)
			2. multipart/form-data : 파일이나 이미지를 서버로 전송할 경우 이 방식을 사용한다.
			3. text/plain : 문자들을  encoding 하지 않음, 기본적인 문자열 자체로 보내겠다
		-- modelAttribute : 폼에 있는 요소들의 값을 채우기 위해서 사용될 객체를 request 로부터 찾을때 사용할 이름을 지정,기본값은 'command'입니다.
							 이걸 넘기는 이유는 스프링 form 태그를 사용한 부분이랑 매핑시키기 위해서이다.  
		-- form:form은 spring form tag이다
		-- form:errors -> Errors,BindingResult를 사용할 때 command객체의 특정 field에서 발생하는 예외에 대한 메시지 출력 가능 	

		-->
</body>
</html>

```



## homecontroller수정

```java
package com.mvc.updown;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.mvc.updown.validate.FileValidator;

@Controller
public class HomeController {


	
	@RequestMapping("/form")
	public String uploadForm() {
		return "upload";
	}
	
}

```



## FileValidator클래스 생성(implements Validator(org.springframework...))

:star:**Validator(인터페이스)** : 도메인 객체를 검증할 수 있음 객체를 검증하는데 실패하면 Errors객체에 에러를 등록함으로써 동작

| method                                                 |                                                              |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| support(class)                                         | 매개변수로 전달된 클래스를 검증할 수 있는지 여부를 변환      |
| validate(Object,org.springframework.validation.Errors) | 매개변수로 전달된 객체를 검증하고 실패하면 Errors 객체에 에러를 등록 |



```java
package com.mvc.updown.validate;

import org.springframework.stereotype.Service;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

import com.mvc.updown.dto.UploadFile;

@Service //이따가 homecontroller에서 filevalidator를 사용해야하는데 호출하기 힘드니깐
public class FileValidator implements Validator {		//객체를 검증할 때 사용하는 인터페이스 

	@Override
	public boolean supports(Class<?> clazz) {	//해당 클래스에 대한 값 검증을 지원하는 지의 여부를 리턴
		return false;
	}
	@Override
	public void validate(Object target, Errors errors) {	//target 객체에 대한 검증을 실행. 검증 결과에 문제가 있을 경우 errors 객체에 에러 정보를 저장한다 
		UploadFile file=(UploadFile) target;	//검사전 타입변환
			System.out.println(file);
		if(file.getMpfile().getSize()==0) {		//검증 후 결과를 errors에 저장한다 .
			errors.rejectValue("mpfile", "fileNPE","Please select a file");
				//주어진 오류 설명을 사용하여 현재 개체의 지정된 필드에 대한 필드 오류를 등록한다(있는 경우 현재 중첩 경로를 고려)
		}
	}
}

```



## UploadFile (DTO)생성

```java
package com.mvc.updown.dto;

import org.springframework.web.multipart.MultipartFile;

public class UploadFile {

​	private String name;
​	private String desc;
​	private MultipartFile mpfile;
​	
​	public String getName() {
​		return name;
​	}
​	public void setName(String name) {
​		this.name = name;
​	}
​	public String getDesc() {
​		return desc;
​	}
​	public void setDesc(String desc) {
​		this.desc = desc;
​	}
​	public MultipartFile getMpfile() {
​		return mpfile;
​	}
​	public void setMpfile(MultipartFile mpfile) {
​		this.mpfile = mpfile;
​	}
​	@Override
​	public String toString() {
​		return "UploadFile [name=" + name + ", desc=" + desc + ", mpfile=" + mpfile + "]";
​	}


​	
}
```




## FileValidator클래스 생성 

implements Validator추가

```java
package com.mvc.updown.validate;

import org.springframework.stereotype.Service;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;

import com.mvc.updown.dto.UploadFile;

@Service //이따가 homecontroller  에서 filevalidator를 사용해야하는데 호출하기 힘드니깐
public class FileValidator implements Validator {

​	@Override
​	public boolean supports(Class<?> clazz) {
​		return false;
​	}

​	@Override
​	public void validate(Object target, Errors errors) {
​		UploadFile file=(UploadFile) target;
​		
​		if(file.getMpFile().getSize()==0) {
​			errors.rejectValue("mpfile", "fileNPE","Please select a file");
​		}
​	}
}
```



## homeController에  FileValidator 추가

```java
package com.mvc.updown;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import com.mvc.updown.validate.FileValidator;

@Controller
public class HomeController {

	@Autowired
	private FileValidator fileValidator;
	
	@RequestMapping("/form")
	public String uploadForm() {
		return "upload";
	}
	
}

```



## update.jsp작성

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<h1>Upload Page</h1>
	
	<form:form method="post" enctype="multipart/form-data" modelAttribute="uploadFile" action="upload">
	file<br/>
	<input type="file" name="mpfile"/><br/>
	<p style="color:red; font-weight: bold;"><form:errors path="mpfile"/></p><br/>
	
	description<br/>
	<textarea rows="10" cols="40" name="desc"></textarea><br/>
	<input type="submit" value="send"/>
	</form:form>
</body>
</html>
```



## homecontroller 추가작성

```java
package com.mvc.updown;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.util.WebUtils;

import com.mvc.updown.dto.UploadFile;
import com.mvc.updown.validate.FileValidator;

@Controller
public class HomeController {

	@Autowired
	private FileValidator fileValidator;
	
	@RequestMapping("/form")
	public String uploadForm() {
		return "upload";
	}
	
	@RequestMapping("/upload")
	public String fileUpload(HttpServletRequest request,Model model, UploadFile uploadFile, BindingResult result) {
		
		fileValidator.validate(uploadFile, result);
		
		if(result.hasErrors()) {
			return "upload";
		}
		
		MultipartFile file=uploadFile.getMpFile();
		String name=file.getOriginalFilename();
		
		UploadFile fileObj=new UploadFile();
		fileObj.setName(name);
		fileObj.setDesc(uploadFile.getDesc());
		
		InputStream inputStream=null;
		OutputStream outputStream =null;
		
		try {
			inputStream=file.getInputStream();
			String path=WebUtils.getRealPath(request.getSession(),getServletContext(),"/resources/storage");			
			System.out.println("업로드 될 실제 경로: " + path);
			
			File storage=new File(path);
			if(!storage.exists()) {
				storage.mkdir();
			}
			
			File newFile=new File(path+"/"+name);
			if(!newFile.exists()) {
				newFile.createNewFile();
			}
			
			outputStream = new FileOutputStream(newFile);
			
			int read=0;
			byte[] b= new byte[(int)file.getSize()];
			
			while((read=inputStream.read(b))!=-1) {
				outputStream.write(b,0,read);
			}
			
		} catch (IOException e) {
			e.printStackTrace();
		}finally {
			try {
				inputStream.close();
				outputStream.close();
			}catch(IOException e) {
				e.printStackTrace();
			}
		}
		
		model.addAttribute("fileObj",fileObj);
		
		
		return "download";
	}
	
}

```

## download.jsp생성

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>Download Page</h1>

	file: ${fileObj.name }
	<br /> desc:${fileObj.desc }

	<form action="download" method="post">
		<input type="hidden" name="name" value="${fileObj.name }" /> 
		<input type="submit" value="download" />
	</form>
</body>
</html>
```

## homecontroller 추가 작성

```java
package com.mvc.updown;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.util.FileCopyUtils;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.util.WebUtils;

import com.mvc.updown.dto.UploadFile;
import com.mvc.updown.validate.FileValidator;

@Controller
public class HomeController {

	@Autowired
	private FileValidator fileValidator;

	@RequestMapping("/form")
	public String uploadForm() {
		return "upload";
	}

	@RequestMapping("/upload")
	public String fileUpload(HttpServletRequest request,Model model, UploadFile uploadFile, BindingResult result) {
		
		fileValidator.validate(uploadFile, result);
		
		if(result.hasErrors()) {
			return "upload";
		}
		
		MultipartFile file=uploadFile.getMpfile();
		String name=file.getOriginalFilename();
		
		UploadFile fileObj=new UploadFile();
		fileObj.setName(name);
		fileObj.setDesc(uploadFile.getDesc());
		
		InputStream inputStream=null;
		OutputStream outputStream =null;
		
		
	    try {
			inputStream = file.getInputStream();
			String path = WebUtils.getRealPath(request.getSession().getServletContext(),"/resources/storage");
			System.out.println("업로드 될 실제 경로: " + path);
			
			File storage=new File(path);
			if(!storage.exists()) {
				storage.mkdir();
			}
			
			File newFile=new File(path+"/"+name);
			if(!newFile.exists()) {
				newFile.createNewFile();
			}
			
			outputStream = new FileOutputStream(newFile);
			
			int read=0;
			byte[] b= new byte[(int)file.getSize()];
			
			while((read=inputStream.read(b))!=-1) {
				outputStream.write(b,0,read);
			}
			
		} catch (IOException e) {
			e.printStackTrace();
		}finally {
			try {
				inputStream.close();
				outputStream.close();
			}catch(IOException e) {
				e.printStackTrace();
			}
		}
		
		model.addAttribute("fileObj",fileObj);
		
		
		return "download";
	}
	
	@RequestMapping("/download")
	@ResponseBody
	public byte[] fileDownload(HttpServletRequest request, HttpServletResponse response, String name) {
		byte[] down= null;
		
		try {
			String path=WebUtils.getRealPath(request.getSession().getServletContext(),"/resources/storage");
			File file=new File(path+"/"+name);
			
			down=FileCopyUtils.copyToByteArray(file);
			String filename=new String(file.getName().getBytes(),"8859_1");
			
			response.setHeader("Content-Disposition", "attachment; filename=\""+filename+"\"");
			response.setContentLength(down.length);
		}catch(FileNotFoundException e) {
			e.printStackTrace();
		}catch(IOException e) {
			e.printStackTrace();
		}
		return down;
	}
	
}

```



<br><br>

# form form (Spring form태그)

>   spring버전 2.0부터 jsp와 웹MVC를 사용할 때, 폼 요소를 좀 더 편리하게 다룰 수 있도록 데이터 바인딩과 관련된 광범위한 태그를 제공 
>
>   -   HTML에서 form태그를 생성하고 바인딩하기 위해서 form 내부태그에 바인딩 경로'path'를 지정한다
>
>   ##### 입력값이 없을시
>
>   -   method는 'post'로 , action값은 '현재 요청 URL' 값이 설정된다 
>
>   ##### id값
>
>   -   생성된 form 에는 id속성이 주어지는데, 이때 id 속성값은 입력폼의 값을 저장하는 **커맨트 객체**의 이름이 할당된다.
>   -   따라서 커맨드 객체의 이름이 기본값이 command가 아닐 경우에는 commandName속성에 해당 커맨드 객체의 이름을 명시해야 한다.

### form form(Spring form)태그 사용하기

```jsp
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>	

<form:form id="command" action="현재 요청 URI" method="post">
     id의 속성값 - 커맨드 객체의 이름, 기본값은 "command"
     action 속성값 - 기본값은 현재 요청 URI
     method 속성값 - 기본값은 "post"
     commandName 속성값 - 커맨드 객체 이름 설정
     path 속성값 - 바인딩 되는 커맨드 객체의 프로퍼티 지정
</form::form>
```



<br><br>



## 스프링의 유효성검사 두가지

-   웹 브라우저 : 자바스크립트로 웹서버에 전송하기 전 검사한다.
-   **웹 서버 : 전달 받은 요청 파라미터를 검사한다**

### 스프링의 유효성 검사

>   org.springframework.validation.Validator 인터페이스는 스프링이 제공하는 객체 검증, 에러 메세지 지원 등의 기능을 사용할 수 있다.
>
>   특히 컨트롤러에서 커맨드 객체의 값을 검증할 때 Validator가 유용하다 
>
>   1.  유효성 검사를 수행할 클래스를 만들고
>   2.   Vlidator 인터페이스를 구현한다. 
>   3.  그리고 2개의 메서드를 오바라이딩한다.
>
>   **-Validator 인터페이스**
>
>   ```
>   supports()-객체의 타입 검증
>   validate()-자바빈의 속성 값 검증
>   
>   package com.mvc.updown.validate;
>   
>   import org.springframework.stereotype.Service;
>   import org.springframework.validation.Errors;
>   import org.springframework.validation.Validator;
>   
>   import com.mvc.updown.dto.UploadFile;
>   
>   @Service //이따가 homecontrolleㄱ에서 filevalidator를 사용해야하는데 호출하기 힘드니깐
>   public class FileValidator implements Validator {		//객체를 검증할 때 사용하는 인터페이스 
>   
>   	@Override
>   	public boolean supports(Class<?> clazz) {	//해당 클래스에 대한 값 검증을 지원하는 지의 여부를 리턴
>   		return false;
>   	}
>   
>   	@Override
>   	public void validate(Object target, Errors errors) {	//target 객체에 대한 검증을 실행. 검증 결과에 문제가 있을 경우 errors 객체에 에러 정보를 저장한다 
>   		UploadFile file=(UploadFile) target;
>   		
>   		if(file.getMpfile().getSize()==0) {
>   			errors.rejectValue("mpfile", "fileNPE","Please select a file");
>   		}
>   			//파일에 값이저장 됐는지 파일을 넘겨줬는지? 
>   	}
>   
>   }
>   
>   ```
>
>   validate()메서드의 target은 검증할 객체, errors는 검증 객체가 올바르지 않을 경우 에러 정보를 저장한다
>
>   BindingResult 또는 Errors 파라미터는 반드시 커맨드 객체 바로 뒤에 위치해야한다.
>
>   BindingResult는 Errors 인터페이스를 상속 받은 타입으로 둘 중 하나를 사용하면 된다
>
>   ### BindingResult와 Errors 인터페이스의 주요 메서드
>
>   ##### Errors 
>
>   >    org.springframework.validation.Errors 인터페이스는 유효성 검증 결과를 저장할 때 사용
>
>   ##### BindingResult 
>
>   >    org.springframework.validation.BindingResult인터페이스 Errors의 인터페이스의 하위 인터페이스로서 폼 값을 커맨드 객체에 바인딩한 결과를 저장하고 에러 코드로부터 에러 메시지를 가져옴
>
>   ```
>   ● reject(String errorCode) : 전체 객체에 대한 글로벌 에러 코드를 추가
>   
>   ● reject(String errorCode, String defaultMessage) : 전체 객체에 대한 글로벌 에러 코드를 추가. 에러 코드에 대한 메시지가 존재하지 않을 경우 defaultMessage를 사용
>   
>   ● reject(String errorCode, Object[] errorArgs, String defaultMessage) : 전체 객체에 대한 글로벌 에러 코드를 추가. 메시지 인자로 errorArgs를 전달. 에러 코드에 대한 메시지가 존재하지 않을 경우 defaultMessage를 사용
>   
>   ● rejectValue(String field, String errorCode) : 필드에 대한 에러 코드를 추가
>   
>   ● rejectValue(String field, String errorCode, String defaultMessage) : 필드에 대한 에러 코드를 추가. 에러 코드에 대한 메시지가 존재하지 않을 경우 defaultMessage를 사용
>   
>   ● rejectValue(String field, String errorCode, Object[] errorArgs, String defaultMessage) : 필드에 대한 에러 코드를 추가. 메시지 인자로 errorArgs를 전달. 에러 코드에 대한 메시지가 존재하지 않을 경우 defaultMessage를 사용
>   
>   출처: https://snoopy81.tistory.com/321 [영혼과영혼의양식]
>   ```
>
>   