레이어드 아키텍처 
> 유사한 기능들을 같은 계층으로 묶어 관리하는 방식의 카텍처 구조 > 


![|400](https://i.imgur.com/3rS5nuL.png)

레이어드 아키텍처의 장점은 기능 개발을 할 때 가시적인 무언가를 만들기에 가장 쉬운 방법이라는 점이다. 그러나 레이어드 아키텍처는 DB 주도 설계를 유도한다. 모든 것이 영속성 계층을 토대로 만들어진다. 

주문 시스템을 만들어야 할 때, JPA Entity나 테이블을 어떻게 구성해야 할 지를 고민 할 것이다. 그러나 실은 주문 시스템에 필요한 Use case를 파악하는게 먼저다. 그 다음 이를 처리하기 위한 도메인과 도메인들의 관계를 생각하는 것이 우선되어야 한다. 

두 번째로, 동시 작업 문제이다. 어떤 기능을 개발할 때 영속성 객체와 Repository가 나와야 Servie 개발이 가능하고 Servie가 나와야 Controller 개발이 가능하다. 그래서 특정 기능 개발은 한 명만 수행이 가능하다. 사실 이 문제는 절차 지향적인 코드를 짰을 때도 발생한다. 

세 번째로 도민이 죽는다는 문제가 있다. 객체에 대한 진지한 고민을 하지 않고 getter와 setter를 가지고 수동적으로 돌아갈 확률이 높다. 또한 함수 위주로 돌아가게 된다. 

개선된 아키텍처

![](https://i.imgur.com/HHJwCTA.png)
![](https://i.imgur.com/5KDktnQ.png)
또한 도메인 레이어에서는 Lombok을 제외한 어노테이션이 달려있지 않게 만든다. (OOP)
![](https://i.imgur.com/JrzIOZo.png)

Repository를 해결해야 한다. 의존성으로 발생하는 문제는 의존성 역전을 활용해서 해결. 

![](https://i.imgur.com/GwrEXnE.png)

![](https://i.imgur.com/bUVoQX6.png)

컨트롤러를 테스트하기 위해 3개의 의존성을 전부 가져오는 것은 비효율적

![](https://i.imgur.com/JBurU9L.png)

이렇게 하면 fake 객체 사용 가능 
![](https://i.imgur.com/mREADPb.png)


최종 
![](https://i.imgur.com/wK40l9B.png)


![](https://i.imgur.com/MDce4Wc.png)
모든 외부 연동 - infrastructure 


테스트 범위 
![](https://i.imgur.com/rRm2g2X.png)
API와 Repository의 impl은 테스트 범위 해당 x 


![](https://i.imgur.com/HwLh1yX.png)
이곳은 테스트 하는게 전체 애플리케이션 테스트의 최소 조건이다. 

![](https://i.imgur.com/X8yEnpo.png)
의존성 역전은 의존성을 약화시킨다는 의미이며 호출자와 구현체를 독립적으로 구성하겠다는 의미이다. 또한 이를 통해 테스트 가능성을 높이고자 하는 것이다. 


![](https://i.imgur.com/0nfl0ig.png)

MSA 중요성이 높아지는 상황에서 필요에 따라 도메인을 분리해서 Y축 확장이 가능해진다는 장점도 있다. 

