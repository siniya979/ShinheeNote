# DI(Dependency Injection) 의존성 주입

- 의존성 주입 
	- 한 객체가 다른 객체를 사용할 때 의존성이 있다고 함
- 런타임시 의존 관계를 맺는 대상을 외부에서 결정하고 주입해 주는 것
- 스프링 프레임워크는 DI 기능을 지원해준다. 

DI 장점
- 의존성 주입을 인터페이스 기반으로 설계하면, 코드가 유연해진다. 
	- 느슨한 결합도
- 변경에 유연해짐
	- 결합도가 낮은 개체끼리는 부품을 쉽게 갈아끼울 수 있다
	- 테스트가 용의해진다. 



# 서비스 실행 

IntelliJ Run 탭에서 실행할 수도 있지만, Services 탭에서 실행시킬 수도 있다.

- Run에서 빌드 작업 혹은 테스트를 할 때 생기는 로그와 서비스 실행 로그와 분리해서 볼 수 있다. 
- 멀티 모듈 프로젝트로 구성해서 인텔리제이 윈도우 안에서 여러 개의 부트 프로젝트를 관리할 때 한 화면에서 한 번에 관리할 수 있다. 


JPA BUDDY  플러그인 활용버 




# @ToString

@ToString은 lazy loaded 필드 혹은 연관 관게를 가지고 있으므로, 퍼포먼스나 메모리 저하를 야기할 수 있습니다. 

@ToString includes lazy loaded fields and/or associations. this can cause performance and memory consumption issues.

![](https://i.imgur.com/E04Z2lV.png)

연관 관계를 맺고 있을 때 순환 참조 대참사를 야기할 수 있으므로 해당 필드에 대해서는 @ToString을 Exclude 해주면 된다. 
즉 A와 B가 서로 양방향 연관 관계라면 ToString 을 한 쪽에서 끊어줘야 한다. 그렇지 않으면 계속 참조를 위해 순환이 된다. 






# 생성자의 팩토리 메소드 패턴 

```java
    protected ArticleComment() {}

    private ArticleComment(Article article, UserAccount userAccount, String content) {
        this.article = article;
        this.userAccount = userAccount;
        this.content = content;
    }

    public static ArticleComment of(Article article, UserAccount userAccount, String content) {
        return new ArticleComment(article, userAccount, content);
    }
```

> 위 코드에서 생성자를 private 로 선언하여 외부 접근을 막고, of 라는 이름의 메서드를 통해 해당 생성자에 접근하여 객체 생성할 수 있도록 만들었다. 이를 팩토리 메서드 패턴이라고 한다. 
>
>팩토리 메서드 패턴을 이용하여 객체를 생성하면 아래와 같은 장점이 있다.
>1. 이름 부여 : 생성자에 상황에 맞는 이름을 부여할 수 있다. 따라서 어떤 종류의, 혹은 필요성의 객체를 생성하는지 쉽게 파악할 수 있다. 
>2. 유연성 : 객체 생성 로직을 메서드 내에서 캡슐화 할 수 있다. 또한 객체 생성 전 유효성 검사를 수행하거나 캐싱된 객체를 반환할 수 있게 된다. 
>3. 인스턴스 생성 제어 : 객체 생성 과정을 완전히 커스터마이징 가능하다. 
>4. 재사용성 

> [재사용성]
>  여러 곳에서 동일한 방식의 객체를 생성하는 로직이 있고, 생성자를 사용해 중복 코드가 여러 곳에 작성되어 있다고 가정해보자. 만약 해당 객체 생성 로직을 수정해야 한다면 개발자는 모든 동일 생성자를 찾아내서 수정해야 한다. 그러나 팩토리 메서드를 사용하면 동일한 방식으로 객체를 생성하는 메서드 하나만 수정하면 된다.
>  
>  또한, 팩토리 메서드는 호출될 때마다 새로운 객체를 생성하는 대신, 동일한 객체의 경우 이전에 생성된 객체를 반환할 수 있다. 개발자가 따로 캐싱을 해주지 않아도 동일한 요청에 대해 저장하고 있던 객체를 반환해주기 때문에 재사용이 가능하며 메모리 효율을 조금이나마 높일 수 있다. 


# @RepositoryRestResource

기본 CRUD 작업에 대한 엔드포인트 자동생성. 컨트롤러 없이 API 요청이 가능 


# Entitiy 와 DTO

## ArticleCommentDto
``` JAVA
public record ArticleCommentDto(
        Long id,
        Long articleId,
        UserAccountDto userAccountDto,
        String content,
        LocalDateTime createdAt,
        String createdBy,
        LocalDateTime modifiedAt,
        String modifiedBy
) {
    public static ArticleCommentDto of(Long id,
                                       Long articleId,
                                       UserAccountDto userAccountDto,
                                       String content,
                                       LocalDateTime createdAt,
                                       String createdBy,
                                       LocalDateTime modifiedAt,
                                       String modifiedBy){
        return new ArticleCommentDto(id, articleId, userAccountDto, content, createdAt, createdBy, modifiedAt, modifiedBy);
    }

    public static ArticleCommentDto from(ArticleComment entity){
        return new ArticleCommentDto(
                entity.getId(),
                entity.getArticle().getId(),
                UserAccountDto.from(entity.getUserAccount()),
                entity.getContent(),
                entity.getCreatedAt(),
                entity.getCreatedBy(),
                entity.getModifiedAt(),
                entity.getModifiedBy()
        );
    }

    public ArticleComment toEntity(Article entity){
        return ArticleComment.of(
                entity,
                userAccountDto.toEntity(),
                content
        );
    }
}
```

-  DTO 는 RECORD 로 선언했다. Record 의 필드는 모두 private final 로 선언된다. 
- Entity가 아니라 DTO 에서 엔티티 -> DTO 로 변경하느