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

