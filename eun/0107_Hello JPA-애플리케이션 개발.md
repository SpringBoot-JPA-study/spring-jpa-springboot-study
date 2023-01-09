# < Hello JPA - 애플리케이션 개발 >

**목차**

**[1] JPA 구동방식 및 코딩 개념적 순서.**

**[2] 정석 try-catch문을 이용한 코딩**

**[3] 수정,삭제 및 단건 조회**

**[4] 주의사항**

**[5] “조회” 기능**


---

# [1] JPA 구동방식 및 개념적 순서

**→ insert 트랜잭션을 기준으로 설명한다.**

## 나오는 개념들

- META-INF/persistence.xml 설정파일
- Persistence 클래스
- EntityManagerFactory 클래스
- EntitiManager 클래스
- EntityTransaction 클래스

## 0차. persistence.xml 설정

- 라이브러리 끌고오기
- jdbc설정
- ORM구현체 설정
- DB로케이션
- DB dialect

## 1차. EntityManagerFactory 생성하기

### 1-0) EntityManagerFactory란?

- app 하나에 하나씩 물려가는 클래스
- EntityManager를 생성하는 기능
- 생성시점은, 요청이 들어올 때마다, 요청(혹은 트랜잭션)에 해당요청에 대응하는 EntityManager를 생성함

### 1-1) → **Persistence클래스의 메서드 사용해서 생성**

```java
EntityManagerFactory emf 
= Persistence.createEntityManagerFactory("hello");

emf.close(); //앱 끝나면, emf 종료시키기
```

### 1-2) **→ 설정xml파일 persistence.xml을 읽음**

but 그 안에 어떤 persistence-unit 설정을 읽어올 것인지 알려주는 name을  Persistence클래스에게 알려주어야함. 강의에서는 “hello”가 네임

## 2차. EntityManager 생성하기

```java
EntityManagerFactory emf 
= Persistence.createEntityManagerFactory("hello");
EntityManager em = emf.createEntityManager();

em.close(); //요청처리 끝나면 em종료시키
emf.close(); //앱 끝나면, emf 종료시키기
```

## 3차. DB테이블과 연동시킬 자바클래스 생성

3-0) 해당 테이블

```sql
create table Member(
	id bigint not null,  /* 컬럼1 id */
	name varchar(255),   /* 컬럼2 name */
	primary key(id)      /* 기본키 PK 지정 */
)
```

3-1) 연동 자바클래스

```sql
import javax.persistence.Entity;   
import javax.persistence.Id;

@Entity     // -->  JPA가 무엇을 관리할 클래스인지 인식하도록 함
public class Member{
	@Id     // --> 최소한 PK를 JPA에게 알려주어야함
	private Long id;
	private String name;

	/* Getter Setter 코드는 기록에서는 생략 */
}

```

기타) DB의 테이블명이나 컬럼명이 엔터티와 다를 때

→ @Table과 @Column 사용해서 매핑해주면 됨.

(만약 table명이 USER이고, name변수 컬럼명이 username인 경우)

```sql
import javax.persistence.Entity;   
import javax.persistence.Id;

@Entity     // -->  JPA가 무엇을 관리할 클래스인지 인식하도록 함
@Table(name="USER")
public class Member{
	@Id     // --> 최소한 PK를 JPA에게 알려주어야함
	private Long id;
	@Column(name="username")
	private String name;

	/* Getter Setter 코드는 기록에서는 생략 */
}

```

## 4차. EnttiyTranscation 생성 및 트랜잭션 열기

JPA에서는 트랜잭션 단위로 움직인다.

따라서 →  트랜잭션을 열어져 있는 상태에서 작업을 하고, 트랜잭션을 닫아준다.

em 즉 EntityManager의 메서드로 생성

EntityTransaction tx = em.getTransaction();

tx.begin() 메서드로 트랜잭션 열기

```java
/* 빈칸줄 */

EntityManagerFactory emf 
= Persistence.createEntityManagerFactory("hello");
EntityManager em = emf.createEntityManager();

// 트랜잭션
EntityTransaction tx = em.getTransaction();
tx.begin();

em.close(); //요청처리 끝나면 em종료시키
emf.close(); //앱 끝나면, emf 종료시키기
```

## 5차. 자바객체 끌어와서 작업하기

- EntityManager의 .persist() 클래스에 해당 자바객체를 넣어서 DB작업함.
- 그런데, 트랜잭션의 commit을 때려주어야. 실제로 JPA가 DB에 SQL을 날려주게 됨.

끝 단순.

```java
/* 빈칸줄 */

EntityManagerFactory emf 
= Persistence.createEntityManagerFactory("hello");
EntityManager em = emf.createEntityManager();

// 트랜잭션
EntityTransaction tx = em.getTransaction();
tx.begin();

// 데이터 넣기
Member mebmer = new Member();
member.setId(1L);
member.setName("HelloJPA");

// DB작업
em.persist(member);
tx.commit();

em.close(); //요청처리 끝나면 em종료시키
emf.close(); //앱 끝나면, emf 종료시키기
```

여기까지는 JPA작동과정 순서를 설명하기위한 내용이고,

실제 정석 코딩은 try-catch문을 사용한다. 왜냐하면 윗부분에서 뭔가 안되었을 때 아래쪽으로 진행하지 않으면 안된다.

예를 들어 rollback을 시켜야하거나, tx나 em등을 종료시켜야할 때

---

# [2] 정석 try-catch문을 이용한 코딩

```java
/* 빈칸줄 */

EntityManagerFactory emf 
= Persistence.createEntityManagerFactory("hello");
EntityManager em = emf.createEntityManager();

// 트랜잭션
EntityTransaction tx = em.getTransaction();
tx.begin();

try{
	// 데이터 넣기
	Member mebmer = new Member();
	member.setId(1L);
	member.setName("HelloJPA");

	// DB작업
	em.persist(member);
	tx.commit();

}catch(Exception e){
	tx.rollback();
}finally{
	em.close();
}

em.close(); //요청처리 끝나면 em종료시키
emf.close(); //앱 끝나면, emf 종료시키기
```

물론 더 실제로는, try-catch문도 스프링이 자동으로 해주게 된다.

---

# [3] 수정,삭제 및 단건 조회

## 1. 해당 DB정보 엔티티로 뽑아내기

자바컬렉션에서 안의 아이템 찾듯이, PK로 찾아서, 엔티티로 뽑아낼 수 있다.

### 예) PK가 “1L”

```java
Member findMember = em.find(Member.class, “1L”);
```

## 2. 삭제

### **EntityManager의 .remove() 메서드 사용**

그 안에다가 넣으면 된다.

```java
/* 위에서 찾아낸 엔티티 findMember를 삭제하기 위해서는 */
em.remover(findMember);
```

## 3. 수정

→ 걍 객체에 있는 세터 사용하면 된다.

```java
findMember.setName("수정내용");
```

EntityManager에 findMember를 다시 넣어줄 필요없다.

왜냐하면 이미 findMember는 매핑 연동 되어서 엔티티매니저가 이미 관리중이다. 바뀐 부분이 있으면 알아서 수정해준다.

# [4] 주의사항

- 엔티티 매니저 팩토리는 애플리케이션 전체에서 하나를 공유해서 사용함
- 엔티티 매니저는 쓰레드간 공유하지 않는다. 한번 쓰고 버린다.
- JPA의 모든 데이터변경은 트랜잭션 안에서 실행된다.

---

# [5] “조회” 기능 

CRUD 중에서, CUD 방법은 알아보았다.

단건 조회는 PK 넣어서 했었고

조건에 맞는 조회(READ 또는 SELECT)는 어케함?

→ 다음에 다시 정리 JPQL
