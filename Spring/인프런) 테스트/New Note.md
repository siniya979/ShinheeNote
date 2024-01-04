# 외부 연동을 다루는 방법 

- 기존 JpaRepository를 상속 받는 Repository의 이름을 엔티티명+JpaRepository로 변경
- 의존성 역전을 위한  **엔티티명+Repository** 이름의 interface 를 생성한다. 
- 위 인터페이스의 구현체 클래스를 생성한다. implements 엔티티명+Repository
