@TemporalType(TemporalType.TIMPSTAMP) -> 최근에는 사용 x 요즘은 그냥 LocalDateTime 사용하면 됨
@Lob 
@Transient : DB와 매핑하지 않겠다


EnumType.ORDINAL : enum 순서를 db에 저장
-> 굉장이 위험함. 사용을 피해야함. enum이 추가되었을 때 순서가 조금이라도 달라지면 중복이 일어날 수 있음. 데이터의 무결성 깨짐
EnumType.STRING : enum에 문자 그대로 db에 저장 

Lob은 필드타입이 문자면 CLOB 그 외 BLOB

# 연관 관계 

```java
@Entity
public class Member{

	@Id @GeneratedValue
	@Column(name = "Member_Id")
	private Long id;

	@ManyToOne
	@joinColumn(name="Team_Id")
	private Team team;
}
```

양방향 
```java
public class Team{
	@Id @GeneratedValue
	@Column(name = "Team_id")
	private Long id;

	@OneToMany(mappedBy = "team") 
	private List<Member> members = new ArrayList<>(); // 관례_초기화 
}
```

연관관계의 주인과 mappedBy 

객체의 양방향 관계는 사실은 서로 다른 단방향 관계 2개다. 

- 연관 관계의 주인만 외래키를 관리한다(등록, 수정)
- 주인이 아닌쪽은 읽기만 가능하다. 
- 주인은 mappedBy 속성 사용 불가 
- 주인이 아니면 mappedBy 속성으로 주인 지정 

즉 위 예시에서는 Team 이 주인. members는 조회만
members에는 값을 넣어도 등록이나 수정이 되지 않는다. 

누구를 주인으로?
- 외래키가 있는 곳을 주인으로 정해라 
  ex) Member.team이 연관관계 주인 
- 연관관계가 다(N)인 쪽이 주인 

양방향 매핑의 경우 그냥 양쪽 모두 값을 입력하자. 
- 플러시 클리어를 안 해주면 1차 캐시에(즉 메모리) 값이 모두 물려 있어서 값이 조회가 안 될 수 있음 
- JPA에서 완전히 클리어 해버리면 1차 캐시가 비어있어서 db에서 다시 조회해야함.


롬복에서는 toString() 쓰지 말자 

- 컨트롤러에서는 절대 entity를 반환하지 말자. 
- 설계할 때 단방향 매핑 해야함

연관 관계 매핑시 고려사항 3가지 
- 다중성
- 단방향, 양방향
- 연관관계 주인





# 상속 관계 

@Inheritance(strategy = InheritanceType.JOINED)
위 속성 추가해주면 분활 테이블로 생성됨 (부모 테이블의 PK를 자식테이블이 PK로 가짐)
장점
- 데이터 정규화 
- 외래 키 참조 무결성 제약조건 활용 가능 
- 저장공간의 효율화 
단점
- 조회시 조인을 많이 사용
- 데이터 저장시 삽입 SQL 2번 호출 
(그러나 이정도는 딱히 단점도 아님)

-> 조인 정략이 정석이라고 생각해야함 
부모테이블이 단독으로 사용할 일 없으면 abstract. 

@DiscriminatorColumn
Dtype 컬럼이 부모테이블에 생기고 자식 테이블(엔티티)명이 저장된다.
JPA 기본 전략은 싱글테이블 SINGLE_TABLE 이고, 싱글테이블의 경우 DTYPE이 자동 생성된다. 

TABLE_PER_CLASS
- 각 분리된 테이블이 생성된다. 
- 아이템(부모클래스)로 하위 테이블을 찾을 때 비효율적인 쿼리 발생 (union으로 전부 조인해서 찾아옴)
- 사용하면 안되는 전략

- DTYPE은 운영상 어떤 전략이든 항상 존재하는 것이 좋다. 

@MappedSuperClass
- 상속관련 맵핑이랑 관련 없음 
- 공통 매핑 정보가 필요할 때 사용 
- 실제 엔티티 X 테이블과 매핑 X 따라서 생성 X 
- 조회, 검색 안됨 
- 직접 생성해서 사용할 일이 없으므로 추상 클래스 권장
-
# 프록시 
em.getReference(); 데이터베이스 조회를 미루는 가짜(프록시)
-> 가짜 엔티티를 준다. (껍데기는 똑같은데 안에는 비어있음)
![](https://i.imgur.com/cIlYNje.png)

- 실제 클래스를 상속 받아서 만들어짐
- 실제 클래스와 겉 모양이 같다.
- 사용하는 입장에서는 진짜 객체인지 프록시 객체인지 구분하지 않고 사용하면 된다(이론상)
- 프록시 객체는 실제 객체의 참조를 보관
- 프로시 객체를 호출하면 프록시 객체는 실제 객체의 메소드 호출 
- **프폭시 객체는 원본 엔티티를 상속받음, 따라서 타입 체크시 주의해야함(== 대신, instance of 사용)**

- JPA는 한 트랜잭션에서 같은 영속성 컨텍스트에서 가져온 같은 데이터는 == 동일성을 보장한다. 
![](https://i.imgur.com/LPOP7Tr.png)
따라서 위 코드의 결과도 true , 프록시가 아닌 멤버 객체 보장 

- 프록시로 조회가 되면 em.find 도 프록시로 조회가 됨 

but 실무에서 저렇게 복잡할 일 거의 없음 


- 영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일때, 프록시를 초기화하면 문제 발생(하이버네이트는 org.hibernate.LazyInitioalizationException 예외를 터뜨린다. )

getPersistenceUnitUtil.isLoaded(Object entity) 프록시 인스턴스 초기화 여부 확인
entity.getClass().getName() 출력 : 프록시 클래스 확인 방법
org.hibernate.Hiberante.initialize(entity) : 프록시 강제 초기화
- JPA 표준은 강제 초기화 없음 


# 즉시 로딩과 지연 로딩

fetch = FetchType.LAZY   ( 지연로딩 )
- 실제 조회 메서드 전에는 프록시 객체로 가져옴 
![](https://i.imgur.com/nFZCI42.png)
실제 team을 사용하는 시점에 초기화

fetch = FetchType.EAGER ( 즉시로딩 )

**주의** 
- 가급적 지연 로딩만 사용(특히 실무에서)
- 즉시 로딩 적용하면 예상하지 못한 SQL이 발생
- 즉시 로딩은 JPQL에서 N+1 문제를 일으킨다.
- @ManyToOne, @OneToOne은 기본이 즉시 로딩 -> LAZY로 설정 
- @OneToMany, @ManyToMany는 기본이 지연로딩

N+1 
최초 쿼리가 1 

select m from member m; 을 조회하는데 
속해 있는 멤버들이 속한 팀이 5개면 
멤버 조회 쿼리 1번, 각 팀 아이디로 팀 정보 조회하는 쿼리가 각 5개 발생한다. 
따라서 최조 쿼리 1 + 연관관계 정보 조회 쿼리 N개 = N+1 문제

해결법
- 모든 연관관계 지연로딩으로 깐다 .
- 1. fetchJoin 한다.  "select m from Member m join fetch m.team"

# 영속성전이(CASCADE)와 고아 객체 
- 특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들고 싶은 경우 
- 영속성 전이는 연관 관계를 매핑하는 것과 아무 관련이 없음
- 엔티티를 영속화 할 때 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐 
- 예: 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장 

cascade = CascadeType.ALL : 모두 적용 
cascade = CascadeType.PERSIST : 영속 
cascade = CascadeType.REMOVE : 삭제
- all, persist 두 개 정도만 사용 

언제 사용하는가?? 
- 부모가 정말 하나의 부모와 자식을 관리할 때 의미 있음
- ex) 게시판 - 첨부파일 ( 그 첨부파일을 다른 곳에서 관리하면 쓰면 안됨 )
- 소유자가 하나일 때!! 

고아 객체
- 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제 
- orphanRemoval = true
- ![](https://i.imgur.com/ngNykcl.png)

주의 
- 참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능
- **참조하는 곳이 하나일 때 사용해야함
- **특정 엔티티가 개인 소유할 때 사용** 
- @OneToOne @OneToMany 만 가능

영속성전이+고아객체 
- 스스로 생명주기를 관리하는 엔티티는 em.persist()로 영속화, em.remove()로 제거 
- 두 옵션을 모두 활성화하면 부모 엔티티를 통해서 자식의 생명주기를 관리할 수 있다.
- 도메인 주도 설계(DDD)의 Aggregate Root 개념을 구현할 때 유용 



# 값 타입

1. 엔티티 타입 
   - @Entity
   - 데이터가 변해도 식별자로 지속해서 추적 가능

2. 값 타입 
   - int, Integer, String 처럼 단순히 값으로 사용하는 자바 기본 타입 객체 
   - 식별자가 없고 값만 있으므로 변경시 추적 불가
   1. 기본값 타입 
   2. 임베디드 타입 
   3. 컬렉션 값 타입

## 기본값 타입 
- 생명주기를 엔티티에 의존
- 값 타입은 공유X
- 기본 타입은 항상 값을 복사한다. 
- Integer 같은 래퍼 클래스나 String 같은 특수한 클래스는 공유 가능한 객체이지만 변경 X 

## 임베디드(복합값) 타입 
- 새로운 값 타입을 직접 정의할 수 있음
- JPA는 임베디드 타입이라 함
- 주로 기본 값 타입을 모아서 만들어서 복합 값 타입이라고도 함
- int, String 과 같은 값 타입 

@Embeddable : 값 타입을 정의하는 곳에 표시 
@Embedded : 값 타입을 사용하는 곳에 표시 
( 기본 생성자 필수), 위 둘 중 하나만 사용해도 되지만 둘 다 사용 권장 

**장점** 
- 재사용
- 높은 응집도 
- Period.isWork()처럼 해당 값 타입만 사용하는 의미있는 메소드를 만들 수 있음
- 임베디드 타입을 포함한 모든 값 타입은, 값 타입을 소유한 엔티티에 생명주기를 의존함.

임베디드 
- 임베디드 타입은 엔티티의 값일 뿐이다.
- 임베디드 타입을 사용하기 전과 후에 매핑하는 테이블은 같다.
- 객체와 테이블을 아주 세밀하게 매핑하는 것이 가능하다.
- 잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많음

**임베디드 타입과 연관관계** 
![](https://i.imgur.com/5rQXqTs.png)
**@AttributeOverride : 속성 재정의** 
- 한 엔티티에서 같은 값 타입을 사용하면?
- 컬럼 명이 중복되서 에러 
- @AttributeOverrides, @AttributeOverride를 사용해서 컬럼명 속성 재정의 할 수 있음 

## 값타입과 불변 객체 

**값타인 공유 참조** 
임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 위험함 
부작용(side effect) 발생 
![](https://i.imgur.com/80puI7O.png)
위 코드의 경우 멤버 두개 모두 값이 바뀐다. 

**값 타입 복사**
- 값 타입의 실제 인스턴스인 값을 공유하는 것은 위험
- 대신 값(인스턴스)를 복사해서 사용 
  ![](https://i.imgur.com/WmG7Pto.png)

**객체 타입의 한계** 
- 항상 값을 복사해서 사용하면 공유 참조로 인해 발생하는 부작용을 피할 수 있다.
- 문제는 임베디드 타입처럼 직접 정의한 값 타입은 자바의 기본 타입이 아니라 객체타입이다.
- 객체 타입은 참조 값을 직접 대입하는 것을 막을 방법이 없다. 
- 객체의 공유 참조는 피할 수 없다. 
-> 즉 누군가 실수 했을 때 막을 방도가 없음 

불변 객체 
- 객체 타입을 수정할 수 없게 만들면 부작용을 원천 차단
- 값 타입은 불변 객체로 설계해야함
- 불변 객체 : 생성 시점 이후 절대 값을 변경할 수 없는 객체 
- 생성자로만 값을 설정하고 수정자(Setter)를 만들지 않으면 된다.
- Integer, String은 자바가 제공하는 대표적인 불변 객체 

불변이라는 작은 제약으로 부작용이라는 큰 재앙을 막을 수 있다. 

## 값 타입의 비교 

값 타입 : 인스턴스가 달라도 그안에 값이 같으면 같은 것으로 봐야 함 

동일성 비교 : 인스턴스의 참조값을 비교, == 사용
동등성 비교 : 인스턴스의 값을 비교, equals() 사용

값 타입은 a.equals(b)를 사용해서 동등성 비교를 해야함 
값 타입의 equals() 메소드를 적절하게 재정의(주로 모든  필드 사용)

## 값 타입 컬렉션
- 값 타입을 하나 이상 저장할 때 사용
- ElementCollection, @CollectionTable 사용
- 데이터베이스는 컬렉션을 같은 테이블에 저장할 수 없다
- 컬렉션을 저장하기 위한 별도의 테이블이 필요함 

값 타입 컬렉션 사용
- 컬렉션은 지연 로딩 
- 값 타입 컬렉션은 영속성 전이 + 고아객체 저게 기능을 필수로 가진다. 
![](https://i.imgur.com/8T8L8tz.png)

새로 갈아껴야 함. 수정시 setter 안됨 새로 객체 생성해서 넣어줘야함 
FavoritFood 의 경우 업데이트가 아니라 통째로 갈아 끼워야함

제약 사항
- 값 타입은 엔티티와 다르게 식별자 개념이 없다.
- 값은 변경하면 추적이 어렵다
- 값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장한다.   
![](https://i.imgur.com/B559mKl.png)
삭제 쿼리 1번, 인서트 쿼리가 2번 

- 값 타입 컬렉션을 매핑하는 테이블은 모든 컬럼을 묶어서 기본키를 구성해야함
  null 입력 x , 중복 저장 x 

값 타입 컬렉션 대안
- 실무에서는 상황에 따라 값 타입 컬렉션 대신에 일대다 관계를 고려 
- 일대다 관계를 위한 엔티티를 만들고, 여기에서 값 타입을 사용
- 영속성 전이 + 고아 객체 제거를 사용해서 값 타입을 컬렉션 처럼 사용
: 값 타입보다 훨씬 더 활용도가 높다. 쿼리 최적화도 유리하다. 

언제 쓰는가? 
- 진짜 단순할 때!! 값이 바뀌어도 문제 없고 중요하지 않은 경우 
- 저런 경우 말고는 다 엔티티 사용함 

엔티티 타입 vs 값 타입 특징 
엔티티 
식별자 O 
생명 주기 관리 
공유 O 

값타입 
식별자X 
생명 주기를 엔티티에 의존
공유하지 않는 것이 안전(복사하기)
불변 객체로 만드는 것이 안전(세터 사용 금지)

- 값타입은 정말 값 타입이 판단 될 때만 사용 (실무에서 잘 사용 x )
- 엔티티를 값 타입으로 만들면 안됨
- 식별자가 필요하고 지속해서 값을 추적, 변경해야 한다면 그것은 값 타입이 아닌 엔티티


# 객체지향 쿼리 언어1 - 기본 문법

query.getResultList()
query.getSingleResult() - 결과 없어도 Exception, 둘 이상이어도 Exception
-> 논란이 많음 결과가 없어도 Exception이 터져서 

(Spring Data JPA 는 다름, 옵셔널 제공, 지금은 순수 Jpa )

## 페이징 
- setFirstResult(int startPositon) : 조회 시작 위치(0부터 시작)
- setMaxResults(int maxResult) :  조회할 데이터 수  



## 경로 표현식  용어 정리
- 상태 필드 : 단순히 값을 저장하기 위한 필드
- 연관 필드 : 연관 관계를 위한 필드
  - 단일 값 연관 필드 ( @ManyToOne, @OneToOne, 대상이 엔티티 )
  - 컬렉션 값 연관 필드 ( @OneToMany, @ManyToMany, 대상이 컬렉션 )

경로 표현식 특징 
- 상태필드 : 경로 탐색의 끝, 탐색x
- 단일 값 연관 경로 : **묵시적 내부 조인**(inner join) 발생, 탐색O
- 컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, 탐색 X 
  - From 절에서 **명시적 조인**을 통해 별칭을 얻으면 별칭을 통해 탐색 가능 

< **명시적 조인을 사용해야함. 실무에서 묵시적 조인은 문제가 많을 수 있음** > 

```SQL
SELECT T.MEMBER FROM TEAM T JOIN T.MEMBERS M        -- 명시적 조인
SELECT T.MEMBERS FROM TEAM T;                       -- 묵시적 조인
```

- 영한님은 묵시적 조인 안 쓴다고 함 - 항상 명시적 조인 사용하기 

## FetchJoin ( 매우 중요 )
- SQL 조인 종류 x 
- JPQL 에서 성능 최적화를 위해 제공하는 기능
- 연관된 엔티티나 컬렉션을 SQL 한 번에 함께 조회하는 기능 
- join fetch  명령어 사용 

엔티티 페치 조인 
- 회원을 조회하면서 연관된 팀도 함께 조회
- SQL을 보면 회원 뿐만 아니라 팀(T.*)도 함게 SELECT


컬렉션 Fetch 조인 
- 일대다 관계, 컬렉션 Fetch 조인 
```SQL
[JPQL]
SELECT t FROM Team t JOIN FETCH t.members WHERE t.name = '팀A'     
[SQL]
SELECT T.*, M.* FROM TEAM T INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
WHERE T.NAME ='팀A';
```

- 문제점: 일대다 관계는 조인하면 데이터가 뻥튀기 될 수 있음 
  ![](https://i.imgur.com/AzqVr8Q.png)
페치 조인과 DISTINCT
- SQL의 DISTINCT 는 중복된 결과를 제거하는 명령
- JPQL의 DISTICNT 2가지 기능 제공
  1. SQL에 DISTICNT 추가 
  2. 애플리케이션에서 엔티티 중복 제거 

```sql 
SELECT DISTINCT t FROM Team t JOIN FETCH t.members WHERE t.name = '팀A'     
```
- SQL에 DISTINCT 를 추가해도 데이터가 다르므로 SQL 결과에서 중복 제거 실패 
  ![](https://i.imgur.com/CDSb8TN.png)
- DISTINCT 가 추가로 애플리케이션에서 중복 제거 시도 
- 같은 식별자를 가진 Team 엔티티 제거 

fetch 조인과 일반 조인의 차이
- 일반 조인 실행시 연관 엔티티를 함께 조인하지 않음 

```SQL
[JPQL]
SELECT t FROM Team t JOIN t.members WHERE t.name = '팀A'     
[SQL]
SELECT T.* FROM TEAM T INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
WHERE T.NAME ='팀A';
```
- 멤버를 가져오지 않음 
- 페치 조인을 사용할 때만 연관된 엔티티도 함께 조회(즉시 로딩)
- 페지 조인은 객체 그래프를 SQl 한 번에 조회하는 개념

fetch 조인의 특징과 한계
**- fetch 조인 대상에는 별칭을 줄 수 없다. 
	- 하이버네이트는 가능하나 가급적 사용X** 
- 둘 이상의 컬렉션은 페치 조인 할 수 없다. 
- 컬렉션을 페치 조인하면 페이징 API를 사용할 수 없다.
	- 일대일, 다대일 같은 단일 값 연관 필드들은 페치 조인해도 페이징 가능
	- 하이버네이트는 경고 로그를 남기고 메모리에서 페이징(**매우 위험**)

hibernate.default_bath_fetch_size value = 100 

