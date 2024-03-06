# 리뷰

2단계 - 문자열 덧셈 계산기 과제를 제출했다. 1단계 학습 테스트와 다르게 테스트 코드가 존재하고 이에 대한 구현을 하는 과제였다.  어제 강사님의 TDD의 장점을 보며 공감이 되었기 때문에 일단 처음에는 하드코딩으로 테스트 코드를 성공시키는데 집중했다. 하나의 메서드에 모든 로직을 때려 박아 테스트 코드를 통과 시킨 이후에 하나하나 로직을 분리하여 메서드의 책임을 분산시켰다. 전반적인 분리를 끝내고 나니 불필요한 로직이 눈에 보여 삭제했고 코드를 간결화했다. 

만약 처음부터 분리된 로직을 설계하고 코딩하려 했으면 어려웠겠다는 생각이 들었다. 아직은 처음이라 설레는 마음으로 임하고 있다. 이번 코드리뷰가 기대된다. 

3단계 - TDD의 어려움 

# 공부

```java
Matcher m = Pattern.compile("//(.)\n(.*)").matcher(text);
if (m.find()) {
    String customDelimiter = m.group(1);
    String[] tokens= m.group(2).split(customDelimiter);
    // 덧셈 구현
}
```

## Pattern 클래스
> 정규 표현식이 컴파일된 클래스. 정규 표현식에 대상 문자열을 검증하거나, 활용하기 위해 사용되는 클래스

- Static Pattern compile(String regex) : 주어진 정규식을 갖는 패턴 생성

## Matcher 클래스 
> Pattern클래스를 받아 대상 문자열과 패턴이 일치하는 부분을 찾거나 전체 일치 여부 등을 판별하기 위해 사용

- boolean find() : 패턴이 일치하는 다음 문자열을 찾는다  
다음 문자열이 있다면 true
- boolean matches() : 패턴에 전체 문자열이 일치한 경우 true를 반환
- String group() : 매치와 일치하는 문자열을 반환
- String group(int group) : 매칭되는 문자열 중 group번째 그룹의 문자열 반환 , 0은 그룹의 전체 패턴을 의미 group(0) = group()
