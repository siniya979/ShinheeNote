1. 오류(Error) VS 예외(Exception)

오류 : 시스템에 비정상적인 상황이 생겼을 때 발생. 
>  심각한 수준의 오류, 개발자가 미리 예측하여 처리할 수 없기 때문에, 어플리케이션에서 오류에 대한 처리를 덜 신경써도 된다. 

오류가 시스템 레벨에서 발생한다면, 예외는 개발자가 구현한 로직에서 발생한다. 

예외가 필요한 3가지 이유 
1. 예외로 발생할 상황을 미리 예측하여 처리할 수 있다.
2. 예외를 개발자가 처리할 수 있게 구분하고 그에 따른 처리 방법을 명확히 알고 적용할 수 있다.
3. 예외를 통해 사용자에게 오류 발생을 알려야 한다. 

2. Java Exception 에러 출력 
- e.getMessage() : 에러와 원인을 간단하게 출력
- e.toStirng() : 에러의 Exception 내용과 원인을 출력
- e.printStackTrace() : 에러의 발생근원지를 찾아서 단계별로 에러 출력 

> log4j에서는 e.printStackTrace()를 log 안에 담을수 없기 떄문에 log.error("error : ", e); 로 사용

3. Checked Exception VS Unchekced Exception
![](https://i.imgur.com/LoORtVj.png)
> Checked Exception과 Unchecked Exception의 가장 명확한 구분 기준은 ‘꼭 처리를 해야 하느냐’ 이다. 

- Checked Exception이 발생할 가능성이 있는 메소드라면 반드시 로직을 try/catch로 감싸거나 throw로 던져서 처리해야한다. 
- Unchecked Exception은 명시적인 예외처리를 하지 않아도 된다. 이 예외는 피할 수 있지만 개발자가 부주의해서 발생하는 경우가 대부분이고, 미리 예측하지 못했던 상황에서 발생하는 예외가 아니기 때문에 굳이 로직으로 처리를 할 필요가 없다. 

예외 처리 방법 

예를 들어 NoSuchElementFoundException 가 발생한다면 접속 환경에 따라 다른 에러 처리를 받게 된다. 웹 페이지로 접속했다면 whitelabel Error Page를 반환 받는다. 

![](https://i.imgur.com/r5YuAI3.png)

Spring은 만들어질 때(1.0)부터 에러 처리를 위한 BasicErrorController를 구현해두었고, 스프링 부트는 예외가 발생하면 기본적으로 /error로 에러 요청을 다시 전달하도록 WAS 설정을 해두었다.

> 참고로 이는 스프링 부트의 WebMvcAutoConfiguration를 통해 자동 설정이 되는 WAS의 설정이다.




