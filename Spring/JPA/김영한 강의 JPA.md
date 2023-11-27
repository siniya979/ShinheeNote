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

JPA 기본 전략은 싱글테이블 s
@Inheritance(strategy = InheritanceType.JOINED)
위 속성 추가해주면 분활 테이블로 생성됨 (부모 테이블의 PK를 자식테이블이 PK로 가짐)

@DiscriminatorColumn
Dtype 컬럼이 부모테이블에 생기고 자식 테이블(엔티티)명이 저장된다.
