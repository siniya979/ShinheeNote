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
- 특정 엔티티를 영속 상탤