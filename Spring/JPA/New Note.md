Test에 @Transactional 붙이면, 테스트 끝나고 롤백 
@Rollback(false) 하면 롤백 안 함 

MEMBER 와 ORDER 에 대해 많이 착각 하는 것이 양방향 연관 관계 
MEMBER 가 ORDER 를 컬렉션으로 가지고 있어야 한다는 생각은 사람의 생각이다. 
MEMBER 랑 ORDER 는 동급으로, 주문을 할때 회원 정보가 필요하다로 생각해야한다.
ORDER에서 조건 필터링에서 멤버로 필터링 하면 될 뿐이다. 

![](https://i.imgur.com/ipeAqwV.png)

![](https://i.imgur.com/8WFV934.png)

