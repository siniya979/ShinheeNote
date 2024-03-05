
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