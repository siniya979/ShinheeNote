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

``` Java
public interface UserRepository {
	Optional<UserEntity> findById(long id);
	Optional<UserEntity> findByIdAndStatus(long id, UserStatus userStatus);
	Optional<UserEntity> findByEmailAndStatus(String email, UserStatus userStatus);
	UserEntity save(UserEntity userEntity);
}
```

UserEntity에 대응하는 User 클래스를 만든다. 

```java
public interface UserRepository {
	Optional<User> findById(long id);
	Optional<User> findByIdAndStatus(long id, UserStatus userStatus);
	Optional<User> findByEmailAndStatus(String email, UserStatus userStatus);
	UserEntity save(User user);
}
```

jpa 엔티티에 jpa 엔티티를 도메인 엔티티로 바꿔주는 메소드를 만든다. toModel()
```java
public User toMode(){
	return User.builder()
		.id()
		.email(email)
		.address(address)
		.certificationCode(certificationCode)
		.status(status)
		.lastLoginAt(lastLoginAt)
		.build();
}
```

```java 
@Repository 
@RequiredArgsConstructor
public class UserRepositoryImpl implements UserRepository {

	private final UserJpaRepository userJpaRepository;

	@Override
	public Optional<User> findById(long id){
		return userJpaRepository.findById(id).map(UserEntity::toModel);
	}

	@Override
	public Optional<User> findByIdAndStatus(long id, UserStatus userStatus){
		return userJpaRepository.findByIdAndStatus(id, userStatus)
								.map(UserEntity::toModel);
	}

	@Override
	public Optional<User> findByEmailAndStatus(String email, UserStatus userStatus){
		return userJpaRepository.findByEmailAndStatus(email, userStatus)
								.map(UserEntity::toModel);
	}

	@Override
	public User save(User user){
		return userJpaRepository.save(userEntity.fromModel(user)).toModel();
	}
}
```


save 의 경우 User 도메인에서 영속성 객체로 바꿔주는 방법을 생각할 수도 있지만, 도메인은 인프라 레이어의 정보를 모르는 것이 좋다. 그래서 영속성 객체에서 모델을 받아 변환하는 코드를 삽입해준다. 

``` java 
public UserEntity fromModel(User user){
	UserEntitiy userEntity = new UserEntity();
	userEntity.id = user.getId();
	userEntity.email =user.getEmail();
	...(중략)...
	userEntity.lastLoginAt = user.getLastLoginAt();
	return userEntity;
}
```
