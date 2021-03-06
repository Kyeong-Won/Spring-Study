# Spring Study 2주차

## 웹 MVC 개발

- 홈 컨트롤러 클래스 파일을 만들어 index.html보다 앞서 참조 될 home을 설정한다.
- `GetMapping` 은 url, return은 참조할 html이라고 볼 수 있다.
( 현재까지는 html파일의 경로를 반환했으므로..)
- `postMapping` 을 이용해 **form형태** 내용을 전달 받을 수 있다.
    - 받을 형태의 객체를 인자로 지정해주고 받은 인자를 처리해서 리턴할 수 있다.
- **thymeleaf** 문법을 통해 모든 객체를 웹 화면에 뿌려줄 수 있다. (ex. th:each)
    - 모델에 있는 속성을 꺼내 표현할 수 있다.

### DB 접근 기술

- h2 데이터베이스 활용
    - 개발이나 테스트 용도의 가벼운 DB

- 순수 JDBC : 고대의 개발자들이 해왔던 방법들
    - JdbcMemberRepository ⇒ DB와 연동되는 Repository 역할
    - datasource를 주입 받음
        1. 커넥션 가져오기
        2. sql 명령 준비
        3. 결과값 받아오기
        4. 결과값 꺼내서 사용하기 (member에 반환)

## 객체 지향의 다형성을 활용한 스프링

- 개방-폐쇄 원칙(OCP)
    - 확장에는 열려있고, 수정, 변경에는 닫혀있다.
- 스프링의 `Dependecy Injection`을 활용하여 **Config파일** 수정만으로 **구현 클래스**를 변경할 수 있다.
- 스프링 부트가 application.properties에 datasource정보를 참조해 빈에 등록하고 사용할 구현체에 datasource를 주입해준다.

![README%20816492d4fb50412a8d82458406e9ecab/_2020-12-21__4.10.18.png](IMG/_2020-12-21__4.10.18.png)

## 스프링 통합 테스트 (MemberServiceIntegrationTest)

> 스프링 컨테이너 + DB 연결 통합 테스트

테스트 실행 시 스프링의 수행이 함께 필요한 경우 사용

- 스프링이 따로 필요없는 테스트 라면 기능 단위로 나눠서 테스트 하는 것도 중요하다. (단위 테스트) ⇒ 빠른 실행
- `@SpringBootTest` : 스프링 컨테이너와 테스트를 함께 실행한다. (통합 테스트) ⇒ 느린 실행
- `@Transcation` 을 통해 테스트 시작 전 트랜잭션이 시작되고 완료 후에 항상 롤백한다.
따라서, **다음 테스트에 영향을 주지 않는다.**

### JDBC Template

JDBC API에서 본 반복 코드를 대부분 제거해주나 SQL문은 직접 작성해야만 한다.

`jdbcTemplate.query("쿼리문");`

## JPA (객체 ORM기술)

인터페이스이며 구현체로 Hibernate등을 사용한다.

### JPA 리포지토리

- `@Entity` JPA에서 관리하는 객체임을 표시
- `@Id` PK
- `@GeneratedValue(strategy = Genartion.IDENTITY)` 자동 생성 되는 필드
- `@Column` 으로 DB 필드명 지정 가능

- 스프링부트가 property파일과 DB정보를 참조해 `EntityManger`를 주입해서 사용
- INSERT 문 사용하기 ⇒ (EntityManger 인스턴스 == em)
    - `em.persist(member);`
    - DB에 넣는 작업을 모두 수행
- SELECT 문 사용 ⇒ `em.find(Member.class, id);`

- `@Transactional` 필수

## 스프링 데이터 JPA

- 스프링 부트 + JPA 사용을 통한 개발 생산성 증가 (핵심 데이터 로직만 작성 가능)
- JPA 사용을 통한 구현 클래스 없이 `인터페이스만` 사용하기
- 스프링 데이터 JPA의 기본적인 CRUD 기능이 만들어져 있다! (따로 구현 없이 바로 사용 가능)
- 간단한 기능은 여러 규칙을 이용해서 바로 사용할 수 있다.
- 하지만 그 비즈니스만의 특별한 기능은 따로 구현해야만 한다.

```java
public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {
    @Override
    Optional<Member> findByName(String name);

}
```

# AOP

모든 메소드의 호출 시간을 측정하고 싶다면? (공통 관심 사항)

핵심 로직 ⇒ 핵심 관심 사항만으로 깔끔하게 유지를 원함

- 핵심 관심 사항과 공통 관심 사항을 분리하자!

    ![README%20816492d4fb50412a8d82458406e9ecab/_2021-01-04__1.10.39.png](IMG/_2021-01-04__1.10.39.png)

- `@Aspect` AOP 클래스 위 어노테이션
- SpringConfig에 `@Bean` 을 이용해 등록 (or `@Component` 등록)
- `@Around(execution(적용 패키지명))` : 적용 범위 지정
