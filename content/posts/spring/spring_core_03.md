---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 03. IoC 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법"
date: 2020-07-02T02:21:47+09:00
categories: ["스프링"]
---
# 2. IoC 컨테이너 2부 : ApplicationContext와 다양한 빈 설정 방법

<img  alt="_2020-07-01__3 02 37 2" src="https://user-images.githubusercontent.com/50758600/86273994-fc31b680-bc0b-11ea-8d2f-78829aad6f82.png">


## 스프링 IoC 컨테이너의 역할

- 빈 인스턴스 생성
- 의존관계설정
- 빈 제공

## ApplicationContext

크게 두가지로 나누어진다

- ClassPathXmlApplicationContext (XML)
- AnnotationConfigApplicationContext (Java)

자세하게 살펴보자.

1. 기존의 빈 등록 방식

    class 정보

    ```java
    public class BookRepository {
    }
    ```

    ```java
    public class BookService {

        BookRepository bookRepository;

        public void setBookRepository(BookRepository bookRepository) {
            this.bookRepository = bookRepository;
        }
    }
    ```

    application.xml에 다음과 같이 작성함

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="bookService"
              class="com.test.spring.core.applicationcontext.applicationcontext.BookService"
              scope="singleton"
              autowire="default">
               <property name="bookRepository" ref="bookRepository"/>
        </bean>

        <bean id="bookRepository"
              class="com.test.spring.core.applicationcontext.applicationcontext.BookRepository"/>

    </beans>
    ```

    DemoApplication.java

    ```java
    public class  DemoApplication {

        public static void main(String[] args) {
    			ApplicationContext applicationContext = new ClassPathXmlApplicationContext("application.xml");    }
    }
    ```

2. Component-Scan 사용(Spring 2.5 부터 가능한 방법)

    class 정보

    ```java
    @Component
    public class BookRepository {
    }
    ```

    ```java
    @Service
    public class BookService {
    		
    		@Autowired
        BookRepository bookRepository;

        public void setBookRepository(BookRepository bookRepository) {
            this.bookRepository = bookRepository;
        }
    }
    ```

    application.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

        <context:component-scan base-package="com.test.spring.core.applicationcontext.applicationcontext"/>

    </beans>
    ```

    DemoApplication.java

    ```java
    public class  DemoApplication {

        public static void main(String[] args) {
    			ApplicationContext applicationContext = new ClassPathXmlApplicationContext("application.xml");    }
    }
    ```

    특정 패키지 이하의 모든 클래스 중에 @Component 애노테이션을 사용한 클래스를 빈으로 자동으로 등록 해 줌.

3. Java 설정

    빈 설정을 xml이 아닌 자바로 설정할 수 없을까 해서 등장한 방법.

    class 정보

    ```java
    public class BookRepository {
    }
    ```

    ```java
    public class BookService {
    		
        BookRepository bookRepository;

        public void setBookRepository(BookRepository bookRepository) {
            this.bookRepository = bookRepository;
        }
    }
    ```

    ApplicationConfig.java

    ```java
    @Configuration
    public class ApplicationConfig {

    		@Bean
    		public BookRepository bookRepository() {
    			return new BookRepository();
    		}
    		
    		// method parameter로 주입받아서도 사용 가능
    		@Bean
    		public BookService bookService() {
    			BookService bookService = new BookService();
    			bookService.setBookRepository(bookRepository());
    			
    			return bookService; 
    		}
    }
    ```

    DemoApplication.java

    ```java
    public class  DemoApplication {

        public static void main(String[] args) {
    				ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationContext.class);
        }

    }
    ```

4. @ComponentSacn 등장

    ApplicaitonConfig에 일일이 등록해줘야하는 단점에 등장

    ApplicationConfig.java

    ```java
    @Configuration
    @ComponentScan(basePackageClasses = DemoApplication.class)
    ppublic class ApplicationConfig {

    }
    ```

    @ComponentScan 이용시 @ComponentScan(basePackage = "com.도메인...") 보다 @ComponentScan(basePackageClasses = DemoApplication.class)을 사용하는 것이 타입 세잎하다. basePackageClasses는 해당 클래스가 있는 곳부터 scan하는 방식이다.

    DemoApplication.java

    ```java
    @SpringBootAplication
    public class  DemoApplication {

        public static void main(String[] args) {
        }

    }
    ```