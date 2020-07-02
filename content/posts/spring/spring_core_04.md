---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 04. @Autowired"
date: 2020-07-03T02:49:29+09:00
categories: ["스프링"]
---

# @Autowired
필요한 의존 객체의 "타입"에 해당하는 빈을 찾아 주입한다.


## 사용할 수 있는 위치

### 클래스 주입

 BookRepository.java

```java
public class BookRepository {
}
```

BookService.java

```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public  BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
}
```

DemoApplication.java

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

위 경우에 BookRepository가 빈 등록이 안되어있어 오류

이후 다음과 같이 BookRepository를 수정해보자.

 BookRepository.java

```java
@Repository
public class BookRepository {
}
```

이후 실행시 정상적으로 작동하는 것을 확인할 수 있다.

### Setter 주입

setter로 주입하는 과정을 확인해보자.

 BookRepository.java

```java
public class BookRepository {
}
```

BookService.java

```java
@Service
public class BookService {

    BookRepository bookRepository;

    @Autowired
    public void setBookRepository(BookRepository bookRepository) {
			this.bookRepository = bookRepository;
		}
}
```

실행하면 오류가 뜨는 것을 확인 할 수 있다.

여기서 "BookService 자체의 인스턴스는 만들 수 있는거 아닌가?" 라는 의문을 가질 수 있다.

실제로 BookService라는 인스턴스 자체는 만들 수 있는데, 다만 @Autowired가 있어서 의존성을 주입하려고 시도하는 과정이 실패하는 것이다.

결론적으로 BookService 인스턴스는 만들 수 있지미나 @Autowirder가 있어서 실패하는 것이다.

이 경우에 @Autowired가 필수적인게 아니라고하면 @Autowired(required = false) 로 하면 정상적으로 실행된다.

(required : 기본값은  true(따라서 못 찾으면 애플리케이션 구동 실패))

### 필드 주입

BookService.java

```java
@Service
public class BookService {
		
		@Autowired(required = false)
    BookRepository bookRepository;

}
```

required가 false이기 때문에, 정상적으로 실행이 가능하다.

<br><br>
## 주입이 여러개인 경우

추가적으로 다음과 같은 클래스를 생성해보자.

MyBookRepository.java

```java
@Repository
public class MyBookRepository extends BookRepository{
}
```

MinseongBookRepository.java

```java
@Repository
public class MinseongBookRepository extends BookRepository{
}
```

이렇게 BookRepository를 상속받는 새로운 repository 2개를 만들어보자.

이후 BookService를 다음과 같이 만들어준다.

BookService.java

```java
@Service
public class BookService {
		
		@Autowired
    BookRepository bookRepository;

}
```

어떠한 결과가 나올까?

```
Parameter 0 of constructor in com.spring.core.autowired.demo.BookService required a single bean, but 2 were found:
	- minseongBookRepository: defined in file [/Users/choeminseong/IdeaProjects/study/spring/core/autowired/target/classes/com/spring/core/autowired/demo/MinseongBookRepository.class]
	- myBookRepository: defined in file [/Users/choeminseong/IdeaProjects/study/spring/core/autowired/target/classes/com/spring/core/autowired/demo/MyBookRepository.class]

Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```

우리는 위와 같은 결과를 확인할 수 있다.

bookRepository에 주입할 대안이 2개이기 때문에 어떤것을 주입해야할지 모른다는 문구와 함께 3가지 대안이 나온다.

### 1. Consider marking one of the beans as @Primary

@Primary 어노테이션을 사용하여 사용하고 싶은 빈이 무엇인지 마킹해라

MinseongRepository.java

    ```java
    @Repository
    @Primary
    public class MinseongBookRepository extends BookRepository{
    }
    ```

위의 코드와 같이 원하는 빈에 @Primary 어노테이션을 활용하면 정상적으로 코드가 수행된다.

### 2. updating the consumer to accept multiple beans

모든 빈을 받아들이자

    ```java
    @Service
    public class BookService {
        @Autowired
        List<BookRepository> bookRepositories;
    }
    ```

위의 코드처럼 여러개의 빈을 주입 받도록 하면 정상적으로 코드가 수행된다.

### 3. using @Qualifier to identify the bean that should be consumed

@Qualifier 어노테이션을 사용하자

    ```java
    @Service
    public class BookService {
        @Autowired
        @Qualifier("minseongBookRepository")
        BookRepository bookRepository;
    }
    ```

위의 코드와 같이 BookService에 명시적으로 어떠한 빈을 주입받는지 설정해주면 정상적으로 작동된다.

### 4. **변수명으로 식별해주는 방법**

위의 3가지 방법 외에도 변수명으로 식별해주는 방법이 존재한다.

BookService.java

    ```java
    @Service
    public class BookService {
        @Autowired
        BookRepository minseongBookRepository;
    }
    ```

이렇게해도 정상적인 실행은 되나 추천되는 방법은 아니다.

## 동작 원리

- BeanPostProcessor
    - 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스
- AutowiredAnnotationBeanPostProcessor enxtend BeanPostProcessor
    - 스프링이 제공하는 @Autowired와 @Value 그리고 JSR-330의 @Inject를 지원하는 어노테이션 처리기
