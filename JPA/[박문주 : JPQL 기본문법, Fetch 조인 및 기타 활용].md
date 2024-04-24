## [JPQL]
#### [[JPQL이란?]]
- JPA가 제공하는 객체 지향 쿼리.
  1. 추상화된 SQL : DBMS에 종속적이지 않다.
  2. 테이블이 아닌 **객체**를 대상으로 한 쿼리
- 단점 : 검색 쿼리와 동적 쿼리
  - 문자열이라 컴파일 체크가 안돼서 버그 발생이 쉽다(특히 공백처리)
  - 핵심 비즈니스 로직에 비해 분기처리 코드가 더 많음
![](https://velog.velcdn.com/images/melodie104/post/e012a85f-67ac-4e3a-87b5-71432e687eb2/image.png) ![](https://velog.velcdn.com/images/melodie104/post/0cdb7a86-d83a-458b-b0c7-440ef39e5e33/image.png)

#### [[Criteria & QueryDSL]]
- JPA 공식 JPQL 빌더. 문자열이 아닌 자바 코드로 JPQL 작성 가능(컴파일 체크)
- 단점 : 지나치게 복잡해서 실용성이 떨어짐
- 실무에서는 QueryDSL을 많이 사용함(동적 쿼리를 쉽게 작성할 수 있는 JQPL 빌더)
![](https://velog.velcdn.com/images/melodie104/post/e44e12a1-590b-4538-8258-9272c67e7e1f/image.png)
#### [[QueryDSL로 작성하면?]] : 이후 추가 예정

#### [[Native SQL]]
- 특정 데이터베이스만의 문법이나 SQL힌트를 사용하기 위해 직접 SQL을 작성하는 기능 
- 실무에서는 JPA와 기타 SQL Mapper(MyBatis, JDBCTemplate) 등을 함께 조합해서 사용하는 경우가 있다.
  - 이 경우, SQL을 실행하기 전에 영속성 컨텍스트 수동 flush 필수.  
  - 참고! flush()가 호출될 때 
  : 1. tx.commit(), 2. 쿼리 직접 실행 시, 3. flush() 직접 호출.

### [JPQL 기본 문법과 기능]
#### [[기본 문법 관련]]
- 엔티티와 속성은 대소문자 구분O (Member, age)
- JPQL 키워드는 대소문자 구분X (SELECT, select, From, WHERE 등)
- JPQL은 **객체를 대상**으로 하는 쿼리. 테이블 명이 아닌 **엔티티 이름**을 사용한다 + **별칭** 필수(Member m)
- SQL 기본문법과 대부분 유사
  - COUNT(), SUM(), AVG(), MAX(), MIN() 등 사용 가능
  - GROUP BY, HAVING / ORDER BY 가능
- TypeQuery vs. Query
  - TypeQuery : 반환 타입이 명확할 때  
  ```java
  TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);
  ```
  - Query : 반환 타입을 특정지을 수 없을 때  
  ```java
  Query query = em.createQuery("SELECT m.username, m.age from Member m");
  ```
#### [[조회 관련]]
- getResultList() vs. getSingleResult()
  - query.getResultList() : 결과가 없으면 null이 아닌 빈 리스트 반환. **NPE** 발생x.
  - query.getSingleResult() : 
    - 결과가 없음 : NoResultException 발생
    - 결과가 둘 이상 : NoUniqueResultException
    - 즉, getResultResult()는 Exception 반환 가능.   
      -> SpringDataJPA는 내부 try-catch로 예외처리 후 Optional을 반환.
- query.setParameter() : 위치 기준은 추천x. 이름 기준으로 작성.
- **프로젝션** : SELECT 절에 조회 대상을 지정하는 것
  - 대상 : 엔티티, 임베디드 타입, 스칼라 타입(기본 데이터 타입)
  - DISTINCT 키워드 사용 가능
  ``` java
  SELECT m FROM Member m : 엔티티 프로젝션
  SELECT m.team FROM Member m : 엔티티 프로젝션
  ** 주의 : 묵시적 join이 발생한다.
  다음과 같이 join은 명시적으로 표현해야 유지보수에 좋다.
  -> SELECT t FROM Member m join m.team t
  SELECT m.address FROM Member m : 임베디드 타입 프로젝션
  SELECT m.username, m.age FROM Member m : 스칼라 타입 프로젝션
  ```
  
#### [[페이징 API]]
- Oracle vs. MySQL
: Oracle은 ROWNUM으로 페이징 (인라인 뷰 서브쿼리를 두번 사용. depth: 3)  
: MySQL은 LIMIT과 OFFSET 사용
- JPA는 페이징을 추상화해서 제공(DB종류에 관계x)
  - setFirstResult(int startPosition) : 조회 시작 위치 (0부터)
  - setMaxResult(int maxResult) : 조회할 데이터 수
  
#### [[JPQL의 조인]]
- Inner join
: SELECT m FROM Member m [INNER] JOIN m.team t;
- Outer join
: SELECT m FROM Member m LEFT [OUTER] JOIN m.team t;
- Theta join 
: SELECT count(m) from Member m, Team t where m.username = t.name;  
  - 연관관계가 없는 두 엔티티 사이의 조인. 내부 조인만 지원.  
  - 각 테이블의 레코드 수를 곱한 만큼의 레코드를 출력 (Cartesian Product 발생 가능)
  - JOIN 키워드는 없이, where 절의 조건을 join조건으로 삼는다.
- JOIN ON절 (외부 조인에 쓰임)
  - 조인 대상 필터링   
  ex. 회원과 팀을 조인하며, 팀 이름이 A인 팀만 조인  
  : SELECT m, t FROM Member m LEFT JOIN m.team t on t.name = 'A' 
  - 연관 관계 없는 엔티티 외부 조인   
  ex. 회원의 이름과 팀의 이름이 같은 대상 외부 조인  
  : SELECT m, t FROM Meber m LEFT JOIN Team t on m.username = t.name
#### [[서브쿼리]]
- EXIST, NOT EXIST, ALL, ANY, IN, NOT IN 등 비교연산자 모두 사용 가능
- JPQL을 사용한 서브 쿼리 예제(생략. 강의 자료 참고)
- JPA 서브 쿼리 한계 : 공식적으로는 SELECT, WHERE, HAVING 절에서만 가능
  - 하이버네이트6에서는 FROM절 서브쿼리도 지원

#### [[JPQL 타입 표현 및 기본 함수, CASE 문]]
- 생략. 강의 자료 참고
- ENUM 타입의 경우
  - 하드 코딩시에는 where m.type = `패키지명 포함 fullName`
  - setParameter("userType", MemberType.USER) 간단하게 가능.
- 엔티티 타입 (상속관계에서 사용)
: SELECT i FROM Item i WHERE type(i) = Book;
#### [[사용자 정의 함수 호출]]
- 대부분의 경우, JPA와 해당 데이터베이스의 기본 제공 방언만으로도 충분하지만,  
개발자가 직접 정의한 데이터베이스 함수 등이 필요할 수도 있다.
- hibernate 버전에 따라 방식이 상이하다. 아래는 hibernate6 기준.
1. `FunctionContributor`의 구현체를 만들고
2. `src/main/resources/META-INF/services/org.hibernate.boot.model.FunctionContributor` 파일을 생성
3. 파일 안에 `패키지명.구현체이름`을 등록(작성)한다.
4. Dialect 변경은 따로 x.
[참고] https://www.inflearn.com/questions/1096265/hibernate-6-custom-함수-등록-방법-공유

## [fetch 조인] - 매.우.중.요!!
- SQL 조인이 x, JPQL의 성능 최적화 기능
- **연관된 엔티티나 컬렉션을 한번의 SQL로 함께 조회하는 것**
- FetchType.LAZY여도 fetch 조인이 우선권을 가짐(즉시 로딩 효과)
### 엔티티 fetch join vs. 컬렉션 fetch join
 #### 1. 엔티티 Fetch Join (@ManyToOne)
 ![](https://velog.velcdn.com/images/melodie104/post/a0763524-d180-4c59-9d1d-d5fb147c5485/image.png)![](https://velog.velcdn.com/images/melodie104/post/fbd3c54b-b914-4ba6-ac1e-06224782aaba/image.png)![](https://velog.velcdn.com/images/melodie104/post/1523feb8-a674-417f-8f6a-a7ca7fe15ecf/image.png)![](https://velog.velcdn.com/images/melodie104/post/d6df6850-79b3-427f-b40e-3365c3dda3b3/image.png)![](https://velog.velcdn.com/images/melodie104/post/77f2adb9-39ac-44be-8f5b-ef19e82f75c9/image.png)
 
 #### 2. 컬렉션 Fetch Join (@OneToMany)
 ```java
JPQL) SELECT t FROM Team t JOIN FETCH t.members
WHERE t.name = '팀A'

SQL) SELECT T.*, M.*
FROM TEAM T
INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
WHERE T.NAME = '팀A'
 ```
 - 1:N 조인은 중복된 Data 결과 값을 받게 된다.  
(ex. Member1과 Member2가 모두 Team1에 속할 때,   
위의 JPQL 수행 결과 Team1은 2번 조회된다->중복)
- JPQL의 DISTINCT
   1. SQL에 DISTINCT 추가
   2. 애플리케이션에서 같은 식별자를 가진 엔티티 중복 제거 (1만으로는 엔티티 중복 해결 x)
 - 하이버네이트6에서는 DISTINCT 명시하지 않아도, 중복 제거가 자동으로 일어남.


### fetch join의 특징과 한계  
- fetch join은 대상에 별칭을 줄 수 없다.   
: 객체 그래프 탐색을 위해서 연관된 엔티티를 모두 끌어온다는 fetch join의 기본 컨셉과 어긋남.   
즉, 별칭을 주고 on 또는 where절로 제한을 가해 필터링 하고 싶은 경우라면  
객체 그래프와 DB사이의 데이터의 일관성, 정합성이 깨질 수 있으므로 fetch join이 맞지 않다.    
(단, 하이버네이트에서는 가능. 하지만 가급적 사용 X)  
https://www.inflearn.com/questions/15876/fetch-join-시-별칭관련-질문입니다  
https://www.inflearn.com/questions/59632/fetch-join-관련-질문-드립니다

- 둘 이상의 컬렉션을 fetch join 할 수 없다.  
(예 : Team이 members와 Orders를 가질 때, 이 둘을 한번에 fetch join으로 가져올 수 없다.)
- **컬렉션**을 fetch join시 페이징 API 사용 불가   
(단일값 연관 필드는 fetch join + 페이징 가능)
  - 해결? @BatchSize 활용하기 (아래)
### @BatchSize
![](https://velog.velcdn.com/images/melodie104/post/587765ae-05dd-4a38-aa64-d934c2109a20/image.png) ![](https://velog.velcdn.com/images/melodie104/post/d6f095ef-4fbb-49d7-aaf9-cb6dfad4f881/image.png)persistence.xml에서 글로벌하게 batch 설정을 할 수 있다.

[ 연관된 컬렉션을 페이징 옵션으로 조회하기 ]
![](https://velog.velcdn.com/images/melodie104/post/095506e2-35d9-4543-b5d2-b5fe642f250e/image.png) ![](https://velog.velcdn.com/images/melodie104/post/60b6a019-b464-4fad-b166-5549db5c5742/image.png)


### 일반 조인 vs. fetch 조인
- JPQL은 결과 반환시 연관관계 고려x. 
- 명시된 대로 조인은 하되, select절에서 지정한 엔티티만 조회한다.  
-> N+1 문제 발생 가능
- 반면, fetch 조인은 즉시 로딩의 처럼 작동해서 연관관계에 있는 엔티티를 함께 조회한다.  
   **실무에서 발생하는 대부분의 N+1 문제는 fetch조인으로 해결 가능**
### fetch join 정리
- **실무** : 글로벌 로딩 전략은 모두 LAZY로 설정 + 최적화 필요한 곳에 fetch join
- 여러 테이블을 조인한 뒤 엔티티가 아닌 다른 형태로 반환해야 한다면?  
  -> 일반 조인을 쓰고, 필요한 데이터들만 조회해 DTO로 반환하는 것이 효과적.
  
	1. fetch join으로 entity를 가져와서 그대로 쓴다.
	2. fetch join으로 entity 가져오고 application에서 DTO로 변환
	3. JPQL 짤 때부터 DTO로 switching

## [경로 표현식]
- .(점)을 찍어 객체 그래프를 탐색하는 것
 ``` sql
SELECT m.username  ---> 1. 상태 필드
FROM Member m
JOIN m.team t  ---> 2. 단일 값 연관 필드
JOIN m.order o ---> 3. 컬렉션 값 연관 필드
WHERE t.name = '팀A'
 ```
- 상태 필드
  - 단순 값 저장. 경로 탐색의 끝. 추가 탐색 불가.
- 단일 값 연관 필드 (@XToOne의 타겟 필드)
  - **묵시적 inner join** 발생. 추가 탐색 가능.(m.team.name)  
  객체 입장에서는 .점 하나이지만, 실제는 조인이 발생하는 쿼리.  
  **묵시적 join을 피하자(한눈에 파악하기 쉽지 않아서 쿼리 튜닝 어려워짐). join은 명시적으로!!**  
  ex) select m.team from Member m 대신  
  -> select t from Member m join m.team t

- 컬렉션 값 연관 필드(@XToMany의 타겟 필드)
  - 묵시적 inner join 발생, 추가 탐색 불가 
  - 명시적 조인 + 별칭을 통해 추가 탐색 가능
  ex) SELECT t.members.username FROM Team t (X)  
  -> SELECT m.username FROM Team t JOIN t.member m (O)

## [다형성 쿼리]
- type, TREAT
- type : 조회 대상을 특정 자식으로 한정하기
 ```java
SELECT i FROM Item i WHERE type(i) IN (BOOK, MOVIE);
//쿼리에서는 where i.DTYPE in ('BOOK', 'MOVIE');
 ```
- TREAT : 타입 캐스팅과 유사. 부모타입을 특정 자식 타입으로 다루기
 ```java
SELECT i FROM ITEM i WHERE TREAT(i as BOOK).author = 'kim' 
// where i.DTYPE = 'BOOK' and i.author = 'kim'
 ```
## [엔티티 직접 사용]
- JPQL에서 엔티티를 직접 사용하면 해당 엔티티의 PK를 사용하는 것과 같다.
```java
String jpql = "select m from Member m where m = :member"; 
//m.id = :memberId와 같다.
```
```java
String jpql = "select m from Member m where m.team = :team"; 
// where m.team.id = :teamId와 같다.
```
## [Named 쿼리]
- 미리 정의해서 이름을 부여해두고 쓰는 JPQL  
- 정적쿼리만 가능(동적쿼리x)  
- 애플리케이션 로딩 시점에 파싱 후 SQL로 캐싱(재사용)한다.  
-> 즉, 애플리케이션 로딩 시점에 쿼리를 검증할 수 있다는 것이 장점.  
- 엔티티에 @NamedQuery(name = , query = )  
```java
@Entity
@NamedQuery(name = "Member.findByUsername",
		   query = "select m from Member m 
           where m.username = :username")
public class Member {
...
}
```
---------------
```java
List<Member> resultList = 
em.createNamedQuery("Member.findByUsername", member.class)
	.setParameter("username", "회원1")
    .getResultList();
```
- XML 또는 어노테이션으로 정의. (XML의 우선순위 더 높음)
- XML 정의 장점 = 운영환경에 따라 다른 XML매핑 파일만 따로 배포 가능하다.
- cf. SpringDataJPA의 @Query  
  - 엔티티가 아닌 리포지토리의 메서드에 작성  
  - @Query안에 JPQL을 적고, @Param으로 파라미터 바인딩
  - @NamedQuery는 자칫 엔티티에 쿼리가 쌓여 단일 책임 원칙을 위배하게 될 수 있음.
```java
@Query("select i from Item i where i.itemName 
					like :itemName and i.price <= :price")
List<Item> findItems(@Param("itemName") String itemName,
							@Param("price") Integer price);
```
## [벌크 연산]
- JPA는 실시간, 단건에 더 최적화되어 있지만, 다량을 한 번에 update, delete 해야 할 때가 존재
- JPA의 dirty checking 기능 사용시, 너무 많은 SQL이 실행   
(변경이 100건이면 100번의 UPDATE 쿼리가 실행됨)
- 벌크 연산 : 쿼리 한번으로 여러 행을 변경하기    
- 주의 사항 : 벌크 연산 수행시 영속성 컨텍스트를 고려해야 한다.   
(벌크 연산은 영속성 컨텍스트를 무시하고 직접 쿼리 날림)  
  - 벌크 연산을 먼저 실행
  - 벌크 연산 수행 후 영속성 컨텍스트 초기화(clear)
![](https://velog.velcdn.com/images/melodie104/post/4647a24f-6fba-4fc5-a758-abad9751b823/image.png)



-------------------------
[참고]
JPQL과 조인 : 
- https://devraphy.tistory.com/587
- https://taegyunwoo.github.io/jpa/JPA_ObjectQuery_JPQL_Join

NamedQuery와 SpringDataJPA의 @Query : 
- https://velog.io/@pp8817/JPQL-Named-query

기타: https://russell-seo.tistory.com/25
