
#### 1. Spring Data JPA (스프링 데이터 JPA)

- **스프링 데이터 JPA**는 스프링 프레임워크와 **JPA**를 함께 사용하여 데이터 액세스를 간소화하는 데 도움을 주는 프레임워크이다.
	- JPA는 자바 표준 ORM(Object-Relational Mapping) 기술로, 객체와 관계형 데이터베이스 간의 매핑을 처리한다.
	- 스프링 데이터 JPA는 이러한 JPA를 기반으로 하여, 데이터베이스 Access(접근)를 보다 쉽고 간편하게 만들어준다.

- 스프링 데이터 JPA를 사용하면 **CRUD(Create, Read, Update, Delete)** 작업과 쿼리 메소드의 작성이 간편해지며, 반복적이고 일반적인 데이터 액세스 작업을 자동화할 수 있다. 
	- 또한 스프링 데이터 JPA는 리포지토리 인터페이스를 정의하여, 애플리케이션에서 데이터베이스와의 상호 작용을 추상화하고 단순화할 수 있도록 지원한다.


#### 2. 스프링 데이터 JPA 회원 리포지토리

```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
import org.springframework.data.jpa.repository.JpaRepository;  
  
import java.util.Optional;  
  
// 인터페이스가 인터페이스를 받을 때는, extends  
public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {  
    Optional<Member> findByName(String name);  
}
```

- 먼저, 이번에는 `src/main/java/hello/hellospring/repository` 하위에, 인터페이스 자바 파일을 만들어, 위의 코드를 작성해주자.
	1. `public interface` 
		- 이것은 자바에서 인터페이스를 정의하는 키워드이다.
		- 인터페이스는 클래스와 달리 구현을 가지지 않으며, 메소드와 상수만을 정의한다.
	    
	2. `SpringDataJpaMemberRepository`
		- 이것은 인터페이스의 이름으로,  개발자가 선택한 것이며, 보통 해당 인터페이스가 하는 일을 나타내는 이름을 선택합니다.
	    
	3. `extends JpaRepository<Member, Long>`
		- 이 부분은 Spring Data JPA의 핵심이다.
		- JpaRepository는 스프링 데이터 JPA가 제공하는 인터페이스로, 데이터 액세스 작업을 위한 기본적인 CRUD 메소드를 제공한다.
		- 이 때, `Member`는 Entity 클래스를 나타내며, `Long`은 해당 Entity의 기본 키의 데이터 형식을 의미한다.
	    
	4. `, MemberRepository`
		- 이 부분은 추가적인 사용자 정의 메소드를 포함하는 인터페이스를 지정한다.
		- JpaRepository에서 제공되지 않는 특별한 동작이 필요한 경우 해당 인터페이스를 추가할 수 있다.

- 이렇게 하면, 스프링 데이터 JPA가 JpaRepository를 받고 있는 인터페이스에 대한 구현체를 자동으로 만들어준다.
	- 이렇게 하면, 스프링 빈을 자동으로 등록해준다.
	- **정리하면, JpaRepository를 `extends`하는 인터페이스가 있다면, 스프링 데이터 JPA가 그에 관련된 구현체를 스스로 만들어서 스프링 빈을 등록해준다.**


#### 3. 스프링 데이터 JPA 회원 리포지토리를 사용하도록, 스프링 설정 변경

- 다음으로, `SpringConfig` 파일에서 스프링 데이터 JPA가 만들어준 스프링 빈을 가져다 쓸 수 있도록 해야 한다.
	- 그 코드는 아래와 같다.

```java
package hello.hellospring;  
  
import hello.hellospring.repository.*;  
import hello.hellospring.service.MemberService;  
import jakarta.persistence.EntityManager;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import javax.sql.DataSource;  
  
// 스프링이 실행될 때, Configuration을 먼저 읽고, config 내부의 @Bean annotation으로 설정된 요소를 스프링이 스프링 컨테이너에 스프링 빈으로 등록한다.  
@Configuration  
public class SpringConfig {  
  
    private final MemberRepository memberRepository;  

	// 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입해준다.
    @Autowired  
    public SpringConfig(MemberRepository memberRepository){  
	    // 여기에서, 스프링 데이터 jpa가 구현체를 만든 것이 이리로 들어옴
        this.memberRepository = memberRepository;  
    }  
  
    @Bean  
    public MemberService memberService(){  
	    // 의존관계 세팅: MemberService > memberRepository
        return new MemberService(memberRepository);  
    }  
  
}
```

- 테스트를 통과했다.
![[스프링 데이터 JPA 테스트.png]]


#### 4. 스프링 데이터 JPA 제공 클래스

- 어떻게 이게 가능한걸까?
	- 모든 이유는 **JpaRepository**에 있다.

- JpaRepository에는 다음과 같은 기본 제공 메소드가 존재한다.
	- 또한, JpaRepository의 부모 클래스가 제공하는 기본 메소드도 살펴볼 수 있다.

- 위 설명 중 `"스프링 데이터 JPA를 사용하면 **CRUD(Create, Read, Update, Delete)** 작업과 쿼리 메소드의 작성이 간편해지며, 반복적이고 일반적인 데이터 액세스 작업을 자동화할 수 있다."`라는 문장이 기억나는가?
	- 아래 코드와 같은 메소드가 기본적으로 제공되고 있는 것을 확인했으니, 이제는 위의 문장을 좀 더 쉽게 이해할 수 있을 것이다.

> `JpaRepository.class`
```java
//  
// Source code recreated from a .class file by IntelliJ IDEA  
// (powered by FernFlower decompiler)  
//  
  
package org.springframework.data.jpa.repository;  
  
import java.util.List;  
import org.springframework.data.domain.Example;  
import org.springframework.data.domain.Sort;  
import org.springframework.data.repository.ListCrudRepository;  
import org.springframework.data.repository.ListPagingAndSortingRepository;  
import org.springframework.data.repository.NoRepositoryBean;  
import org.springframework.data.repository.query.QueryByExampleExecutor;  
  
@NoRepositoryBean  
public interface JpaRepository<T, ID> extends ListCrudRepository<T, ID>, ListPagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {  
    void flush();  
  
    <S extends T> S saveAndFlush(S entity);  
  
    <S extends T> List<S> saveAllAndFlush(Iterable<S> entities);  
  
    /** @deprecated */  
    @Deprecated  
    default void deleteInBatch(Iterable<T> entities) {  
        this.deleteAllInBatch(entities);  
    }  
  
    void deleteAllInBatch(Iterable<T> entities);  
  
    void deleteAllByIdInBatch(Iterable<ID> ids);  
  
    void deleteAllInBatch();  
  
    /** @deprecated */  
    @Deprecated  
    T getOne(ID id);  
  
    /** @deprecated */  
    @Deprecated  
    T getById(ID id);  
  
    T getReferenceById(ID id);  
  
    <S extends T> List<S> findAll(Example<S> example);  
  
    <S extends T> List<S> findAll(Example<S> example, Sort sort);  
}
```

> `JpaRepository` 부모에 있는 `ListCrudRepository.class`
```java
//  
// Source code recreated from a .class file by IntelliJ IDEA  
// (powered by FernFlower decompiler)  
//  
  
package org.springframework.data.repository;  
  
import java.util.List;  
  
@NoRepositoryBean  
public interface ListCrudRepository<T, ID> extends CrudRepository<T, ID> {  
    <S extends T> List<S> saveAll(Iterable<S> entities);  
  
    List<T> findAll();  
  
    List<T> findAllById(Iterable<ID> ids);  
}
```

>  `ListCrudRepository.class` 부모에 있는 `CrudRepository.class`
```java
//  
// Source code recreated from a .class file by IntelliJ IDEA  
// (powered by FernFlower decompiler)  
//  
  
package org.springframework.data.repository;  
  
import java.util.Optional;  
  
@NoRepositoryBean  
public interface CrudRepository<T, ID> extends Repository<T, ID> {  
    <S extends T> S save(S entity);  
  
    <S extends T> Iterable<S> saveAll(Iterable<S> entities);  
  
    Optional<T> findById(ID id);  
  
    boolean existsById(ID id);  
  
    Iterable<T> findAll();  
  
    Iterable<T> findAllById(Iterable<ID> ids);  
  
    long count();  
  
    void deleteById(ID id);  
  
    void delete(T entity);  
  
    void deleteAllById(Iterable<? extends ID> ids);  
  
    void deleteAll(Iterable<? extends T> entities);  
  
    void deleteAll();  
}
```

- 대체적인 제공 클래스 내용은 아래 이미지와 같다.

![[스프링 데이터 JPA 제공 클래스.png]]


#### 5. 스프링 데이터 JPA 메소드 커스텀 

- 그럼, JpaRepository가 제공하는 기본 메소드가 아니거나, 내용 일부를 수정해야 하는 등의 비공통화처리의 경우는 어떻게 해야할까?
	- 답은 메소드 이름을 활용하는 것이다.
 
-  Spring Data JPA에서는 메소드 이름을 통해 쿼리를 생성하는 규칙이 있다. 이 규칙은 메소드 이름을 분석하여 해당 메소드에 맞는 JPQL(Java Persistence Query Language) 쿼리를 자동으로 생성할 수 있도록 한다.
	- 이를 통해 개발자는 별도의 JPQL 쿼리를 작성하지 않고도 간단한 쿼리 메소드를 정의하여 데이터베이스를 조회할 수 있다.

- Spring Data JPA의 쿼리 메소드 생성 규칙은 다음과 같다.
	1. 메소드 이름은 `find...By`, `read...By`, `query...By`, `count...By`, `get...By`로 시작해야 한다. 
		- 이는 해당 메소드가 조회를 위한 메소드임을 나타낸다.
    
	2. `By` 다음에는 엔티티의 필드 이름이 옵니다. 이를 통해 해당 필드를 기준으로 조회할 수 있다.
    
	3. 필드 이름 뒤에는 검색 조건을 표현하는 키워드가 온다.
		- 예를 들어, `Equals`, `IgnoreCase`, `Like`, `Between`, `GreaterThan`, `LessThan` 등이 있습니다.
    
	4. 필드 이름 뒤에 `And`, `Or`, `Between`, `LessThan`, `GreaterThan` 등을 사용하여 조건을 결합할 수 있다.
    
	5. 반환 타입은 해당 엔티티 타입이거나, 해당 필드에 대한 Projection이거나, 컬렉션 타입이다.

- 간단한 예를 들어보면, 아래와 같다.
	- 이름으로 멤버를 찾는 경우: `Optional<Member> findByName(String name);`
	- 나이가 특정 값보다 큰 멤버를 찾는 경우: `List<Member> findByAgeGreaterThan(int age);`
	- 이름이 특정 문자열로 시작하는 멤버를 찾는 경우: `List<Member> findByNameStartingWith3(String prefix);`

- 이런 식으로 메소드 이름을 작성하면, Spring Data JPA는 해당 메소드에 맞는 JPQL 쿼리를 자동으로 생성하여 실행한다.