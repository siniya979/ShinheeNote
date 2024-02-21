# DI(Dependency Injection) 의존성 주입

- 의존성 주입 
	- 한 객체가 다른 객체를 사용할 때 의존성이 있다고 함
- 런타임시 의존 관계를 맺는 대상을 외부에서 결정하고 주입해 주는 것
- 스프링 프레임워크는 DI 기능을 지원해준다. 

DI 장점
- 의존성 주입을 인터페이스 기반으로 설계하면, 코드가 유연해진다. 
	- 느슨한 결합도
- 변경에 유연해짐
	- 결합도가 낮은 개체끼리는 부품을 쉽게 갈아끼울 수 있다
	- 테스트가 용의해진다. 



# 서비스 실행 

IntelliJ Run 탭에서 실행할 수도 있지만, Services 탭에서 실행시킬 수도 있다.

- Run에서 빌드 작업 혹은 테스트를 할 때 생기는 로그와 서비스 실행 로그와 분리해서 볼 수 있다. 
- 멀티 모듈 프로젝트로 구성해서 인텔리제이 윈도우 안에서 여러 개의 부트 프로젝트를 관리할 때 한 화면에서 한 번에 관리할 수 있다. 


JPA BUDDY  플러그인 활용버 




# @ToString

@ToString은 lazy loaded 필드 혹은 연관 관게를 가지고 있으므로, 퍼포먼스나 메모리 저하를 야기할 수 있습니다. 

@ToString includes lazy loaded fields and/or associations. this can cause performance and memory consumption issues.

![](https://i.imgur.com/E04Z2lV.png)

연관 관계를 맺고 있을 때 순환 참조 대참사를 야기할 수 있으므로 해당 필드에 대해서는 @ToString을 Exclude 해주면 된다. 
즉 A와 B가 서로 양방향 연관 관계라면 ToString 을 한 쪽에서 끊어줘야 한다. 그렇지 않으면 계속 참조를 위해 순환이 된다. 



