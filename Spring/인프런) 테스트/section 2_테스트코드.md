# UserRepository Test 

```JAVA
@ExtendWith(SpringExtension.clss) // DataJpaTest에 내장되어 있으므로 생략 가능
@TestPropertySource("classpath:test-application.properties") //기본 설정 되어 있음
@DataJpaTest(showsql=true)
public class UserRepositoryTest{

	@Autowried
	private UserRepository userRepository;
	
	@Test
	void UserRepository_가_제대로_연결되었다(){
		// given
		UserEntity userEntity = new UserEntity();
		userEntity.setEmail("test@naverm.com");
		userEntity.setAddress("Seoul");
		userEntity.setNickname("test");
		userEntity.setStatus(UserStauts.ACTIVE);
		userEntity.setCertificationCode("aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa");
		
		// when
		UserEntity result = userRepository.save(userEntity);
		
		// then
		assertThat(result.getId()).isNotNull();
	}

	@Test
	void findByIdAndStatus_로_유저_데이터_찾아올_수_있다() {
		// given 
		UserEntity userEntity = new UserEntity();
		userEntity.setId(1L);
		userEntity.setEmail("test@naverm.com");
		userEntity.setAddress("Seoul");
		userEntity.setNickname("test");
		userEntity.setStatus(UserStauts.ACTIVE);
		userEntity.setCertificationCode("aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa");

		// when 
		userRepository.save(userEntity);
		Optional<UserEntity> result = userRepository.findByIdAndStatus(1,
		UserStatus.ACTIVE);

		// then
		asserThat(result.isPresent()).isTrue();
	}
	
	@Test
	void findByIdAndStatus_는_데이터가_없으면_Optional_empty() {
		// given 
		UserEntity userEntity = new UserEntity();
		userEntity.setId(1L);
		userEntity.setEmail("test@naverm.com");
		userEntity.setAddress("Seoul");
		userEntity.setNickname("test");
		userEntity.setStatus(UserStauts.ACTIVE);
		userEntity.setCertificationCode("aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa");

		// when 
		userRepository.save(userEntity);
		Optional<UserEntity> result = userRepository.findByIdAndStatus(1,
		UserStatus.ACTIVE);

		// then
		asserThat(result.isEmpty()).isTrue();
	}

	@Test
	void findByEmailAndStatus_로_유저_데이터_찾아올_수_있다() {
		// given 
		UserEntity userEntity = new UserEntity();
		userEntity.setId(1L);
		userEntity.setEmail("test@naverm.com");
		userEntity.setAddress("Seoul");
		userEntity.setNickname("test");
		userEntity.setStatus(UserStauts.ACTIVE);
		userEntity.setCertificationCode("aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa");

		// when 
		userRepository.save(userEntity);
		Optional<UserEntity> result = userRepository.findByEmailAndStatus(
		"test@naverm.com",UserStatus.ACTIVE);

		// then
		asserThat(result.isPresent()).isTrue();
	}
	
	@Test
	void findByEmailAndStatus_는_데이터가_없으면_Optional_empty() {
		// given 
		UserEntity userEntity = new UserEntity();
		userEntity.setId(1L);
		userEntity.setEmail("test@naverm.com");
		userEntity.setAddress("Seoul");
		userEntity.setNickname("test");
		userEntity.setStatus(UserStauts.ACTIVE);
		userEntity.setCertificationCode("aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa");

		// when 
		userRepository.save(userEntity);
		Optional<UserEntity> result = userRepository.findByEmailAndStatus(
		"test@naverm.com",UserStatus.ACTIVE);

		// then
		asserThat(result.isEmpty()).isTrue();
	}
}
```

단일 테스트를 하면 성공하는데, 전체 테스트를 돌리면 실패한다.
-> 대표적인 비결정적 테스트. 테스트 메서드가 별렬로 처리되는데 동시성 제어가 안되어 발생. 따라서 아래와 같이 수정해줘야함. 

```java
@ExtendWith(SpringExtension.clss) // DataJpaTest에 내장되어 있으므로 생략 가능
@TestPropertySource("classpath:test-application.properties") //기본 설정 되어 있음
@Sql("/sql/user-repository-test-data.sql`")
@DataJpaTest(showsql=true)
public class UserRepositoryTest{

	@Autowried
	private UserRepository userRepository;

	@Test
	void findByIdAndStatus_로_유저_데이터_찾아올_수_있다() {
		// given 
		// when 
		Optional<UserEntity> result = userRepository.findByIdAndStatus(1,
		UserStatus.ACTIVE);

		// then
		asserThat(result.isPresent()).isTrue();
	}
	
	@Test
	void findByIdAndStatus_는_데이터가_없으면_Optional_empty() {
		// given 
		// when 
		Optional<UserEntity> result = userRepository.findByIdAndStatus(1,
		UserStatus.ACTIVE);

		// then
		asserThat(result.isEmpty()).isTrue();
	}

	@Test
	void findByEmailAndStatus_로_유저_데이터_찾아올_수_있다() {
		// given 
		// when 
		Optional<UserEntity> result = userRepository.findByEmailAndStatus(
		"test@naverm.com",UserStatus.ACTIVE);

		// then
		asserThat(result.isPresent()).isTrue();
	}
	
	@Test
	void findByEmailAndStatus_는_데이터가_없으면_Optional_empty() {
		// given 
		// when 
		Optional<UserEntity> result = userRepository.findByEmailAndStatus(
		"test@naverm.com",UserStatus.ACTIVE);

		// then
		asserThat(result.isEmpty()).isTrue();
	}
}
```


`user-repository-test-data.sql`
```SQL
insert into `users` (`id`, `email`,`nickname`,`address`,`certification_code`,`status`,`last_login_at`)
values (1,`test@naverm.com`,`test`,`Seoul`,`aaaaaa-aaaa-aaaa-aaaa-
		aaaaaaaaaaaa`,`ACTIVE`,`0`)
```
# UserSerivce Test

get 과 find 컨벤션 
get 은 애초에 데이터가 없으면 에러를 던진다는 의미가 내포되어 있다. 
- findByIdOrElseThrow  / getById
Optional을 반환하는 ~ById 는 findById가 더 적절하다. 

```java

@SpringBootTest
@sqlGroup({
	@Sql("/sql/user-service-test-data.sql", executionPhase = 
	ExecutionPhase.BEFORE_TEST_METHOD),
	@Sql(value = "/sql/delete-all-data.sql", executionPhase = 
	ExecutionPhase.AFTER_TEST_METHOD)
})
public class UserSerivceTest{

	@Autowried
	private UserService userSerivce;
	
	@MockBean
	private JavaMailSender mailSender;

	@Test
	void getByEmail은_ACTIVE_상태인_유저를_찾아올_수_있다(){
		// given
		String email = "test@naver.com";
		// when
		UserEntity result = userService.getByEmail(email);
		// then
		assertThat(result.getNickname()).isEqualTo("test");
	}

	@Test
	void getByEmail은_PENDING_상태인_유저는_찾아올_수_없다(){
		// given
		String email = "fail@naver.com";
		
		// when
		// then
		assertThatThorwnBy(()->{
			UserEntity result = userService.getByEmail(email);
		}).isInstanceOf(ResourceNotFoundException.class);
		
	}

	@Test
	void getById는_ACTIVE_상태인_유저를_찾아올_수_있다(){
		// given
		// when
		UserEntity result = userService.getById(1);
		// then
		assertThat(result.getNickname()).isEqualTo("test");
	}

	@Test
	void getById는_PENDING_상태인_유저는_찾아올_수_없다(){
		// given		
		// when
		// then
		assertThatThorwnBy(()->{
			UserEntity result = userService.getById(2);
		}).isInstanceOf(ResourceNotFoundException.class);
		
	}

	@Test
	void userCreateDto_를_이용하여_유저를_생성할_수_있다(){
		// given
		UserCreateDto userCreateDto = UserCreateDto.builder()
			.email("test@kakao.com")
			.address("Inchoen")
			.nickname("test-K")
			.build();
		// SimpleMailMessage를 사용하는 send 메서드가 호출돼도 작동하지 마라.
		BDDMockito.doNothing().when(mailSender).send(any(SimpleMailMessage.class))
		// when
		UserEntity result = userService.create(userCreateDto);
		// then
		assertThat(result.getStatus()).isEqualTo(UserStatus.PENDING);
		assertThat(result.getId()).isNotNull();
		//assertThat(result.getCertificationCode()).isEqualTo(???)현재 테스트 불가
	}

	@Test
	void userupdateDto_를_이용하여_유저를_수정할_수_있다(){
		// given
		UserCreateDto userCreateDto = UserCreateDto.builder()
			.address("Busan")
			.nickname("update-K")
			.build();
	
		// when
		userService.update(1, userCreateDto);
		// then
		UserEntity userEntity = userService.getById(1);
		assertThat(result.getId()).isNotNull();
		assertThat(result.getAddress()).isEqualTo("Busan");
		assertThat(result.getNickname()).isEqualTo("update-K");
		//assertThat(result.getCertificationCode()).isEqualTo(???)현재 테스트 불가
	}

	@Test
	void user를_로그인_시키면_마지막_로그인_시간이_변경된다(){
		// given
		// when
		userService.login(1);
		// then
		UserEntity userEntity = userService.getById(1);
		// 현재는 테스트할 방도가 없음 
		//assertThat(result.getLastLoginAt()).isEqualTo(....);
		assertThat(userEntity.getlastLoginAt()).isGreaterThan(0L);
	}

	@Test
	void PENDING_상태의_사용자는_인증_코드로_ACTIVE_할_수_있다(){
		// given
		// when
		userService.verifyEmail(2, "aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaab");
		// then
		UserEntity userEntity = userService.getById(2);
		assertThat(userEntity.getStatus()).isEqualTo(UserStatus.ACTIVE);
	}
	
	@Test
	void PENDING_상태의_사용자는_잘못된_인증코드를_받으면_에러를_던진다(){
		// given
		// when
		// then
		assertThatThrownBy(()->{
			userService.verifyEmail(2, "aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaac");
		}).isInstatnceOf(CertificationCodeNotMatchedException.class);
	

	}
}	
```

`user-service-test-data.sql`
```SQL
insert into `users` (`id`, `email`,`nickname`,`address`,`certification_code`,`status`,`last_login_at`)
values (1,`test@naverm.com`,`test`,`Seoul`,`aaaaaa-aaaa-aaaa-aaaa-
		aaaaaaaaaaaa`,`ACTIVE`,`0`)

insert into `users` (`id`, `email`,`nickname`,`address`,`certification_code`,`status`,`last_login_at`)
values (2,`fail@naverm.com`,`fail`,`Seoul`,`aaaaaa-aaaa-aaaa-aaaa-
		aaaaaaaaaaab`,`PENDING`,`0`)

```

`delete-all-data.sql`
```SQL
delete from `users` where 1;
delete from `posts` where 1;
```
# UserController Test


**`@AutoConfigureTestDatabase`**
In-memory embedded databases generally work well for tests, since they are fast and do not require any installation. If, however, you prefer to run tests against a real database you can use the `@AutoConfigureTestDatabase` annotation

인메모리 임베디드 데이터베이스는 빠르고 설치가 필요하지 않기 때문에 일반적으로 테스트에 적합합니다. 그러나 실제 데이터베이스를 실행시키지 않고 테스트를 하고 싶다면`@AutoConfigureTestDatabase` 를 사용할 수 있습니다. 

**`@AutoConfigureMockMvc`**

만약 당신이 구성 요소 설정을 자동으로 하고 싶다면 `@AutoConfigureMockMvc` 속성을 사용할 수 있습니다. 실제 웹 환경을 모방하여 컨트롤러 테스트를 수행할 수 있게 해주는 테스트 유틸리티입니다

```java
@SpringBootTest
@AutoConfigureMockMvc
@AutoConfigureTestDatabase
public class HealthCheckTest{

	@Autowired
	private MockMvc mockMvc;

	@Test
	void 헬스_체크_응답이_200이다() throws Exception {
		mockMvc.perfrom(get("/health_check_html"))
			.andExpect(status().isOk());
	}

}
```

```java
@SpringBootTest
@AutoConfigureMockMvc
@AutoConfigureTestDatabase
@sqlGroup({
	@Sql("/sql/user-controller-test-data.sql", executionPhase = 
	ExecutionPhase.BEFORE_TEST_METHOD),
	@Sql(value = "/sql/delete-all-data.sql", executionPhase = 
	ExecutionPhase.AFTER_TEST_METHOD)
})
public class UserControllerTest{

	@Autowired
	private MockMvc mockMvc;
	@Autowired
	private UserRepository userRepository;
	private final ObjectMapper objectMapper = new ObjectMapper();

	@Test
	void 사용자는_특정_유저의_정보를_개인정보는_소거된_채_전달_받을_수_있다() throws Expetion{
		// given
		// when 
		// then
		mockMvc.perfrom(get("/api/users/1"))
			.andExpect(status().isOk())
			.andExpect(jsonPath("$.id").value(1))
			.andExpect(jsonPath("$.email").value("test@naver.com"))
			.andExpect(jsonPath("$.nickname").value("test"))
			.andExpect(jsonPath("$.address").doesNotExist())
			.andExpect(jsonPath("$.status").value("ACTIVE"));
	}

	@Test
	void 사용자는_존재하지_않는_유저의_아이디로_API_호출시_404_응답을_받는다(){
		// given
		// when 
		// then
		mockMvc.perfrom(get("/api/users/1234"))
			.andExpect(status().isNotFound())
			.andExpect(content().string("Users에서 ID 1234를 찾을 수 없습니다."));
	}
	
	@Test
	void 사용자는_인증코드로_계정을_활성화_시킬_수_있다() throws Expetion{
		// given
		// when 
		// then
		mockMvc.perfrom(
			get("/api/users/2/verfiy").
			.queryParam("certificationCode", "aaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaab"))
			.andExpect(status().isFound());
		UserEntity.userEntity = userRepository.findById(2L).get();
		assertThat(userEntity.getStatus()).isEqualTo(UserStatus.ACTIVE);
	}

	@Test
	void 사용자는_내_정보를_불러올_때_개인정보인_주소도_갖고_올_수_있다() throws Expetion{
		// given
		// when 
		// then
		mockMvc.perfrom(
			get("/api/users/me")
				.header("EMAIL", "test@naver.com"))
			.andExpect(status().isOk())
			.andExpect(jsonPath("$.id").value(1))
			.andExpect(jsonPath("$.email").value("test@naver.com"))
			.andExpect(jsonPath("$.nickname").value("test"))
			.andExpect(jsonPath("$.address").value("Seoul"))
			.andExpect(jsonPath("$.status").value("ACTIVE"));
	}

	@Test
	void 사용자는_내_정보를_수정할_수_있다() throws Expetion{
		// given
		UserUpdateDto userUpdateDto = UserUpdateDto.builder()
			.nickname("updateN")
			.address("Pangyo")
			.build();
		// when 
		// then
		mockMvc.perfrom(
			put("/api/users/me")
				.header("EMAIL", "test@naver.com"))
				.contentType(MediaType.APPPLICATION.JSON)
				.content(objectMapper.writeValueAsString(userUpdateDto)))
			.andExpect(status().isOk())
			.andExpect(jsonPath("$.id").value(1))
			.andExpect(jsonPath("$.email").value("test@naver.com"))
			.andExpect(jsonPath("$.nickname").value("test"))
			.andExpect(jsonPath("$.address").value("Seoul"))
			.andExpect(jsonPath("$.status").value("ACTIVE"));
	}
}
```

`user-controller-test-data.sql`
```SQL
insert into `users` (`id`, `email`,`nickname`,`address`,`certification_code`,`status`,`last_login_at`)
values (1,`test@naverm.com`,`test`,`Seoul`,`aaaaaa-aaaa-aaaa-aaaa-
		aaaaaaaaaaaa`,`ACTIVE`,`0`)

insert into `users` (`id`, `email`,`nickname`,`address`,`certification_code`,`status`,`last_login_at`)
values (2,`fail@naverm.com`,`fail`,`Seoul`,`aaaaaa-aaaa-aaaa-aaaa-
		aaaaaaaaaaab`,`PENDING`,`0`)

```

# UserCreateController Test


```java
@SpringBootTest
@AutoConfigureMockMvc
@AutoConfigureTestDatabase
@Sql(value = "/sql/delete-all-data.sql", executionPhase = 
	ExecutionPhase.AFTER_TEST_METHOD)
public class HealthCheckTest{

	@Autowired
	private MockMvc mockMvc;
	private final ObjectMapper objectMapper = new ObjectMapper();

	@Test
	void 사용자는_회원_가입을_할_수_있고_회원가입된_사용자는_PENDING_상태다() throws Expetion{
			// given
		UseCreateDto userCreateDto = UseCreateDto.builder()
			.email("test@kakao.com")
			.nickname("test-K")
			.address("Pangyo")
			.build();
		BDDMockito.doNothing().when(mailSender).send(any(SimpleMailMessage.class));
		// when 
		// then
		mockMvc.perfrom(
			post("/api/users")
				.header("EMAIL", "test@naver.com"))
				.contentType(MediaType.APPPLICATION.JSON)
				.content(objectMapper.writeValueAsString(userCreateDto)))
			.andExpect(status().isCreated())
			.andExpect(jsonPath("$.id").isNumber())
			.andExpect(jsonPath("$.email").value("test@kakao.com"))
			.andExpect(jsonPath("$.nickname").value("test-K"))
			.andExpect(jsonPath("$.status").value("PENDING"));
	}

}
```
