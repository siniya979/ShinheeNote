Spring의 IoC(제어의 역전) 컨테이너는 스프링 프레임워크의 핵심이다. IoC 컨테이너는 객체를 생성하고 객체의 의존성을 구성 및 조립하며 객체의 전체 생명 주기를 관리한다. 컨테이너는 애플리케이션을 구성하는 컴포넌트들을 관리하기 위해 DI(의존성 주입)을 사용한다. 자바 POJO 클래스, 자바 어노테이션, 자바 코드 또는 구성 파일(XML)로 부터 객체의 대한 정보를 가져온다. 이러한 객체들을 Bean 이라 한다. 자바 객체들과 객체들의 생명 주기는 개발자에 의해 관리되지 않기 때문에 IoC(제어의 역전)이라고 이름이 붙여졌다. 

IoC 컨테이너는 두 가지 유형이 존재한다. 
1. BeanFactory
2. ApplcationContext 

이는 스프링에서 IoC 컨테이너를 사용할 때 BeanFactory 또는 ApplicationContext 를 사용해야 하는 지 여부를 의미한다. BeanFactory 는 IoC 컨테이너의 가장 기본적인 버전이다. 그리고 ApplicationContext 는 BeanFactory 의 기능을 확장한다. 다음은 Spring IoC 의 주요 기능들의 일부다. 

* 객체 생성
* 객체 관리
* 애플리케이션 구성 관리
* 의존성 관리

<hr>

> Shinny 정리 
> 