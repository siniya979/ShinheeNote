# 이론 및 자세 

TDD란 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술이다. 

TDD의 아이러니 중 하나는 테스트 기술이 아니라는 점이다. TDD는 분석 기술이며, 설계 기술이다. 
> 분석이나 설계가 필요하지 않다는 말이 아니다. 요구사항 분석을 제대로 하고 이를 설계해야 TDD가 가능하다. 


TDD = Test First Development + **Refactoring** 
> TDD란, 테스트를 먼저 만드는 것이 아니라 **테스트를 기반으로 설계를 끊임없이 개선**해나가는 것.

![](https://i.imgur.com/wwCR7qb.png)

구현에 집중
- 실패하는 테스트 -> 테스트 성공
- 로직을 구현하는 것에 집중
- 테스트를 통과하기 위해 어떠한 행위도 허용 

--> 이너 피스 

설계에 집중
- 리팩토링
	- 메서드 클래스 설계 
	- 클린 코드 구현 


##  생활 체조 

객체지향 생활 체조 원칙은 [소트웍스 앤솔러지](http://wikibook.co.kr/thoughtworks-anthology/) 책에서 다루고 있는 내용으로 객체지향 프로그래밍을 잘 하기 위한 9가지 원칙을 제시하고 있다.  이 책에서 주장하는 9가지 원칙은 다음과 같다.

- 규칙 1: 한 메서드에 오직 한 단계의 들여쓰기(indent)만 한다.
- 규칙 2: else 예약어를 쓰지 않는다.
- 규칙 3: 모든 원시값과 문자열을 포장한다.
- 규칙 4: 한 줄에 점을 하나만 찍는다.
- 규칙 5: 줄여쓰지 않는다(축약 금지).
- 규칙 6: 모든 엔티티를 작게 유지한다.
- 규칙 7: 3개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
- 규칙 8: 일급 콜렉션을 쓴다.
- 규칙 9: 게터/세터/프로퍼티를 쓰지 않는다.



## 엘레강트 오브젝트 _ 이름 짓기 

- 객체들이 무엇을 하는지(what he does) -> 잘못됨 
- 객체들이 무엇인지 (what he is)



# TEST 코드 작성 

JUnit의 `ParameterizedTest`를 활용해 중복 코드를 제거

> 다양한 매개변수를 사용하여 하나의 테스트를 여러 번 실행할 수 있게 해주는 기능
> 이를 통해 다양한 입력 값에 대해 같은 테스트 로직을 반복적으로 실행하면서, 각기 다른 조건에서의 테스트 결과를 확인할 수 있다. 

`@ParameterizedTest` 선언 
`@ValueSource` : 매개변수 제공 방법 정의를 위한 어노테이션 

```JAVA
class StringTest {

    @ParameterizedTest
    @ValueSource(strings = {"Hello", "JUnit", "ParameterizedTest"})
    void isNotEmpty(String word) {
        assertTrue(!word.isEmpty());
    }
}
```


@ValueSource(ints = {1, 2, 3, 4, 5}) 형태로 int , long, double 등 여러타입 가능 

`@CsvSource`
> 문자열 배열을 매개변수로 받는다. 각 문자열은 CSV 형식으로 되어 있으며, 각 항목은 테스트 메소드의 매개변수에 순서대로 매핑된다. 

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.assertEquals;

class SumTest {

    @ParameterizedTest
    @CsvSource({
        "1, 1, 2",
        "2, 3, 5",
        "3, 5, 8",
        "10, 20, 30"
    })
    void addsTwoNumbers(int first, int second, int expectedResult) {
        assertEquals(expectedResult, first + second);
    }
}
```
