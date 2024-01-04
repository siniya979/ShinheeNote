# 외부 연동을 다루는 방법 

- 기존 JpaRepository를 상속 받는 Repository의 이름을 엔티티명+JpaRepository로 변경
- 의존성 역전을 위한  **엔티티명+Repository** 이름의 interface 를 생성한다. 
- 위 인터페이스의 구현체 클래스를 생성한다. implements 엔티티명+RepositoryImpl

UserRepository 의 위치가 infrastructure 에 있는게 부적절하다고 느껴진다. UserRepository를 infrastructure 에 두면 상위 모듈인 service 에서 infrastructure 인 패키지에 의존하는 그림이 되기 때문이다. 따라서 service에 외부 연동을 담당하는 port 패키지를 만들어 해당 패키지에 service에서 사용하는 인터페이스들을 넣어준다. 

```java 
@Repository
@RequriedArgsConstructor
public class PostRepositoryImpl implements PostRepository { 
	private final PostJpaRepository postJpaRepository;
}
```

```java 
public interface PostRepository { 

}
```


기존 메일 서비스도 따로 자체적인 서비스로 분리함
```java
@Service 
@RequiredArgsConstructor
public class CertificationService {

	private final JavaMailSender javaMailSender;
}
```

# 도메인과 영속성 객체 구분하기

```java
public interface UserRepository {
	Optional<UserEntity> findById(long id);
	Optional<UserEntity> findByIdAndStatus(long id, UserStatus userStatus);
	Optional<UserEntity> findByEmailAndStatus(String email, UserStatus userStatus);
	UserEntity save(UserEntity userEntity);
}
```

UserEntity에 대응하는 User 클래스를 만든다. 

