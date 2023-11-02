---
sticker: emoji//1f4c5
---

# Why Java Date is deprecated?
날짜와 시간 데이터를 담을 때, 자연스레 어떤 의심 없이 LocalDateTime을 사용해왔다. Date의 존재는 알았지만 LocalDateTime이 익숙했기에 둘의 차이를 알아볼 생각조차 하지 않았다. 어쩌다보니 뭐가 다르지?라는 생각이 갑자기 들어 정리해본다.

Date 클래스가 deprecated 된 것이 아닌, **Date constructor 가 deprecated** 되었다. 그러나 거의 전부 deprecated 되었기 때문에 Date 클래스가 deprecated 되었다고 작성했다.
## What’s wrong with the old Java Date API?

Date는 JDK1.0부터 제공된 오래된 클래스다. 오래되었다고 나쁜건 아니지만 Date는 몇가지 문제가 있어 현재는 권장하지 않는다. 다만, 상용화된 레거시 코드에 아직도 많이 남아있기 때문에 **'사용'은 가능**하다.

### 직관적이지 않다.

2023년 10월 13일에 대한 데이터를 Date 객체에 담으려면 어떻게 해야할까? 'java.time'은 아래와 같이 코드를 작성한다.

```java
LocalDate date = new LocalDate(2023, 10, 13); // 2023_10_13
```

직관적인 코드로 당연하게 느껴진다. 그러나 Date는 다르다

```java
Date date = new Date(123, 9, 13); // 2023_10_13
```

2023년을 의미하는 데이터를 담기 위해 123을, 10월은 10이 아닌 9를 저장한다. 전혀 직관적이지 않다.

Date의 생성자, Date(int year, int month, int day)를 사용할 때,'year'는 담고자 하는 연도에서 1900을 빼야한다. 'month'는 0부터 시작하기 때문에 10이 아닌 10에서 1을 뺀 9를 담아주어야 한다.

Date에 대해 모른다면 개발자는 원치 않는 오류를 만들어 낼 수 있다.

### TimeZone 을 고려하지 않는다.

타임존 정보가 없다는 것은 여러 지역에서 현재 시간을 저장할 때, 어느 지역에서 값을 저장했는지에 대한 정보가 없다. 다국적 어플리케이션의 경우 시간대 변환 및 특정 지역에 대한 시간 관리를 위해 타임존 정보가 필요하다.

예를 들어, 아메리카, 유럽, 아시아에서 동시에 now() 메서드를 실행시키고 저장했을 때 서로 다른 값이 저장된다. 따라서 타임존이 다른 유저가 날짜와 시간이 사용되는 로직에서 같은 요청을 보내도 다른 결과값을 얻을 수 있다.

반면 java.time의 'ZonedDateTime', 'OffsetDateTime' 와 같은 타임존 정보를 포함하는 클래스를 사용하여 타임존이 다른 경우에도 시간 관리를 할 수 있다.

### 가변 클래스, 불변성을 보장하지 않는다.

Date 클래스가 더이상 사용되지 않는 가장 큰 이유 중 하나라고 생각되는 것이 불변성을 보장하지 않는다는 점이다.

Date 는 가변클래스로 객체가 생성된 이후 멤버 변수를 변경할 수 있다. 즉 set() 메서드 사용이 가능하다는 것이다. 단순하게 생각해봐도 값이 임의로 변경이 된다는 것은 위험하게 들린다. 그러나 정확히 어떤 문제가 있을 수 있는 것일까?

이건 Date의 문제보다 가변 클래스의 문제점을 찾아보면 된다. 솔직히 교과서적인 내용이기 때문에 문제가 될 수 있는 상황을 생각해보자.

보안 프로토콜에서 유효한 인증서를 만료시키거나 갱신할 때 데이터가 가변적이라면 신뢰할 수 없다. 따라서 보안 관련 문제를 방지하는데 도움이 된다. 보안 뿐만 아니라 예약, 주문등 많은 상용 서비스에서도 시간이 임의로 변경되면 서비스의 신뢰성이 떨어진다.

따라서 가변 클래스라는 것은 어플리케이션이 운영되는데 심각한 문제를 초래할 수 있다.

### 날짜 및 시간 연산 기능 부족

Date 클래스는 총 29개의 메서드를 제공한다. 이중 set과 get 그리고 toString 등의 메서드를 제외하면 실제 연산과 관련된 메서드는 before, after 두 개 뿐이다.

반면 jave.time 중 LocalDateTime 의 경우 총 77개의 메서드를 제공하고 훨씬 다양한 연산 메서드를 제공하고 있다.

> **공식문서**  
> Date  
> [https://docs.oracle.com/javase/8/docs/api/java/util/Date.html](https://docs.oracle.com/javase/8/docs/api/java/util/Date.html)  
> LocalDateTime [https://docs.oracle.com/javase/8/docs/api/java/time/LocalDateTime.html](https://docs.oracle.com/javase/8/docs/api/java/time/LocalDateTime.html)

또 LocalDateTime과 다르게 Data는 윤년, 윤초를 완벽하게 처리하지 못한다. 아래의 코드로 확인해보자

```java
import java.util.Date;

public class LeapYearExample {
    public static void main(String[] args) {
        Date date = new Date(120, 1, 29); // 2020/2/29

        int year = date.getYear(); 
        System.out.println("Year: " + year); // 결과 : 120

        if (isLeapYear(year)) {
            System.out.println("It's a leap year.");
        } else {
            System.out.println("It's not a leap year.");
        }
    }

    public static boolean isLeapYear(int year) {
        if (year % 4 == 0 && (year % 100 != 0 || year % 400 == 0)) {
            return true;
        }
        return false;
    }
}
```

알고리즘 공부를 하면 한 번쯤은 풀어보는 윤년 계산 조건문이다. 2020년은 29일까지 존재하므로 윤년이 아니다. 그러나 Date 클래스는 앞서 말했듯 1900년부터 게산하므로 2020년이 아닌 120년을 반환하여 2020년을 윤년으로 간주하고 "It's a leap year."을 출력한다. ( 믈론 +1900을 해주면 해결된다. )

### 결론

이제 Date가 아니라 **java.time**의 **LocalDateTime**을 사용하자.
