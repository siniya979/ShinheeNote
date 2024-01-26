Spring의 IoC(제어의 역전) 컨테이너는 스프링 프레임워크의 핵심이다. IoC 컨테이너는 객체를 생성하고 객체의 의존성을 구성 및 조립하며 객체의 전체 생명 주기를 관리한다. 컨테이너는 애플리케이션을 구성하는 컴포넌트들을 관리하기 위해 DI(의존성 주입)을 사용한다. 자바 POJO 클래스, 자바 어노테이션, 자바 코드 또는 구성 파일(XML)로 부터 객체의 대한 정보를 가져온다. 이러한 객체들을 Bean 이라 한다. 자바 객체들과 객체들의 생명 주기는 개발자에 의해 관리되지 않기 때문에 IoC(제어의 역전)이라고 이름이 붙여졌다. 

IoC 컨테이너는 두 가지 유형이 존재한다. 
1. BeanFactory
2. ApplcationContext 

이는 스프링에서 IoC 컨테이너를 사용할 때 BeanFactory 또는 ApplicationContext 를 사용해야 하는 지 여부를 의미한다. BeanFactory 는 IoC 컨테이너의 가장 기본적인 버전이다. 그리고 ApplicationContext 는 BeanFactory 의 기능을 확장한다. 다음은 Spring IoC 의 주요 기능들의 일부다. 

* 객체 생성
* 객체 관리
* 애플리케이션 구성 관리
* 종속성 관리

<hr>

> Shinny 정리 
> 모든 객체가 Bean 이 되는 것이 아니다. @Controller, @Service, @Repository 등이 붙은 클래스들은 개발자가 직접 해당 객체를 인스턴스화 하여 사용하지 않는다. 싱글톤 패턴을 이용하여 스프링의 IoC 컨테이너가 이들을 Bean 으로 등록하여 자동으로 객체를 생성하고 이들의 생명주기를 관리하는 것이다. 
> 
> 즉 예를 들어 RequestDto 는 빈으로 사용하지 않는다. 개발자가 자유롭게 new (생성자)를 사용하여 객체를 생성하고 삭제하고 생명주기를 관리한다. 반면 Controller 는 애플리케이션을 빌드할 때 스프링이 자동으로 객체를 생성하고 관리한다. 

<hr> 

아래의 코드로 스프링의 IoC 가 무엇인지 이해해보자

```JAVA
public interface Sim
{
	void calling();
	void data();
}
```

위 Sim 을 상속 받아 Sim 의 메서드를 오버라이드하는 Airtel 그리고 Jio 라는 두 개의 클래스가 있다. 

```JAVA
public class Airtel implements Sim {

	@Override public void calling()
	{
		System.out.println("Airtel Calling");
	}

	@Override public void data()
	{
		System.out.println("Airtel Data");
	}
}
```

```JAVA
public class Jio implements Sim{
	@Override
	public void calling() {
		System.out.println("Jio Calling");
	}

	@Override
	public void data() {
		System.out.println("Jio Data");
	}
}
```

이제 메서드를 기본(Main) 메서드에서 호출해보자. 런타임 다형성을 개념을 구현함으로서 다음과 같은 작업을 수행할 수 있다.

```JAVA
public class Mobile {

	// Main driver method
	public static void main(String[] args)
	{
		Sim sim = new Jio();

		// Sim sim = new Airtel();

		sim.calling();
		sim.data();
	}
}
```

만약 나중에 또 다른 새로운 'Vodafone'와 같은 'Sim'이 나타나고 코드에서 자식 클래스의 이름을 다시 바꿔야 할 때는 어떻게 될까? 

```JAVA
Sim sim = new Vodafone();
```

코드 소스를 변경해야 할 것이다. 그러나 우리는 이 소스 코드를 손대지 않고 그대로 유지하고 싶기에 소스 코드는 고정되어야 한다. 어떻게 해결할 수 있을까? 여기서 Spring IoC 컨테이너가 등장한다. 이 예시에서 ApplicationContext 를 이용해 IoC 컨테이너를 구현할 것이다. 첫번째로, beans.xml 이라는 XML 파일을 생성한다.

**Example**: beans.xml File
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	https://www.springframework.org/schema/beans/spring-beans.xsd">

<bean id="sim" class="Jio"></bean>

</beans>
```

< **설명** > 
**beans.xml 파일에서** **beans 를** 생성했다 . 따라서 ID 내부에서는 고유 ID를 전달해야 하고 클래스 내부에서는 Bean을 생성하려는 클래스 이름을 전달해야 한다. 나중에 main 메소드 내에서 다음 프로그램에서 설명할 내용을 조정할 수 있다.

<hr>
> Shinny
> `<bean>` 엘리먼트의 `id`는 해당 빈의 고유한 식별자이며, `class`에는 빈으로 사용할 클래스의 이름인 "Jio"를 지정한다. 설정이 끝난 후, 소스 코드에서는 해당 빈을 가져와서 사용할 수 있다. 이를 통해 런타임에 어떤 클래스의 인스턴스를 사용할지 설정 파일에서 변경할 수 있게 된다. 
> 
<hr>

> **Bean 정의** : Spring에서 애플리케이션의 핵심 구조를 형성하고 Spring IoC 컨테이너에 의해 관리되는 객체를 빈이라고 합니다. Bean은 Spring IoC 컨테이너에 의해 인스턴스화되고, 어셈블링(조립)되고, 관리되는 객체입니다.

```JAVA
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Mobile {
	public static void main(String[] args) {
		// Using ApplicationContext tom implement Spring IoC
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans.xml");
		
		// Get the bean
		Sim sim = applicationContext.getBean("sim", Sim.class);
		
		// Calling the methods
		sim.calling();
		sim.data();
	}
}
```

> 결과 (Output)** <br>
> Jio Calling
> Jio Data

그리고 