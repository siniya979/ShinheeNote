
contains 
isNotEmpty
startsWith
isTrue / isFalse
isEqualTo / isNotEqualTo

isGreaterThanOrEqualTo
isLowerCase() / isUpperCase()

``` java
 assertThat(someFile)
   .exists()  // 주어진 파일 또는 디렉토리 존재 여부 확인 
   .isFile()
   .canRead() 
   .canWrite();
```



containsKey()


### 컬렉션과 배열에 대한 체이닝 단언
```java
import static org.assertj.core.api.Assertions.assertThat;

List<String> list = Arrays.asList("apple", "banana", "cherry");

assertThat(list)
    .hasSize(3)
    .contains("banana")
    .doesNotContain("date")
    .containsExactly("apple", "banana", "cherry"); // 순서까지 검증
```

