---
sticker: lucide//trash-2
---
<h1> Garbage Collection (GC) </h1>
> 가비지 컬렉션은 자바 메모리 관리 방법 중 하나로 JVM의 <span style="color: rgb(237, 125, 49);">Heap 영역</span>에서 동적으로 할당했던 메모리 중 더이상 필요 없는 메모리를 모아 주기적으로 제거하는 프로세스이다. 

C / C++ 과 달리 JAVA 에서는 GC 가 메모리 관리를 대행해주기 때문에 개발자가 메모리 누수 관리를 하지 않고 개발에만 집중할 수 있다는 장점이 있다. 

그러나,  <span style="color: rgb(237, 125, 49);">메모리가 언제 해제되는지 정확히 알 수 없어</span> 제어하기 힘들며, GC가 동작하는 동안에는 **다른 동작이 멈추기** 때문에 **오버헤드가** 발생되는 문제점이 있다. = **Stop-The-World**



https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC

