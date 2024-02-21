# Index.html

```html
<!DOCTYPE html>
<html lang="ko" >
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Title</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-rbsA2VBKQhggwzxH7pPCaAqO46MgnOM80zW1RWuH61DGLwZJEdK2Kadq2F9CUG65" crossorigin="anonymous">
    <link rel="stylesheet" href="/css/search-bar.css">
</head>
```

> `content = "width=device-width, initial-scale=1" `
> 너비를 디바이스 너비 만큼(동적 조정), 초기회면 배율 1 


```html
<body>
<header id="header">
    헤더 삽입부
    <hr>
</header>
```
임시 

```html
<main class="container">

    <div class="row">
        <div class="card card-margin search-form">
            <div class="card-body p-0">
                <form id="card search-form">
                    <div class="row">
                        <div class="col-12">
                            <div class="row no-gutters">
                                <div class="col-lg-3 col-md-3 col-sm-12 p-0">
                                    <label for="search-type" hidden>검색 유형</label>
                                    <select class="form-control" id="search-type">
                                        <option>제목</option>
                                        <option>본문</option>
                                        <option>id</option>
                                        <option>닉네임</option>
                                        <option>해시태그</option>
                                    </select>
                                </div>
                                <div class="col-lg-8 col-md-6 col-sm-12 p-0">
                                    <label for="search-value" hidden>검색어</label>
                                    <input type="text" placeholder="검색어..." class="form-control" id="search-value" name="search-value">
                                </div>
                                <div class="col-lg-1 col-md-3 col-sm-12 p-0">
                                    <button type="submit" class="btn btn-base">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-search">
                                            <circle cx="11" cy="11" r="8"></circle>
                                            <line x1="21" y1="21" x2="16.65" y2="16.65"></line>
                                        </svg>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>
```

``` html
    <table class="table" id="article-table">
        <thead>
        <tr>
            <th class="title col-6">제목</th>
            <th class="hashtag col-2">해시태그</th>
            <th class="user-id col">작성자</th>
            <th class="created-at col">작성일</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td class="title"><a>첫글</a></td>
            <td class="hashtag">#java</td>
            <td class="user-id">Uno</td>
            <td class="created-at"><time>2022-01-01</time></td>
        </tr>
        <tr>
            <td>두번째글</td>
            <td>#spring</td>
            <td>Uno</td>
            <td><time>2022-01-02</time></td>
        </tr>
        <tr>
            <td>세번째글</td>
            <td>#java</td>
            <td>Uno</td>
            <td><time>2022-01-03</time></td>
        </tr>
        </tbody>
    </table>

    <nav id="pagination" aria-label="Page navigation">
        <ul class="pagination justify-content-center">
            <li class="page-item"><a class="page-link" href="#">Previous</a></li>
            <li class="page-item"><a class="page-link" href="#">1</a></li>
            <li class="page-item"><a class="page-link" href="#">Next</a></li>
        </ul>
    </nav>
</main>

<footer id="footer">
    <hr>
    푸터 삽입부
</footer>
<!--body 태그 밑에 두는 이뉴는 마크업 문서가 순서대로 읽히며 인터프리팅 되기 때문이다. 마지막에 두어야 순서 문제에서 벗어날 수 있다.
만약 js 를 상단에 배치하면 우선 js 코드를 읽고 실행한 다음 맡에 있는 엘리먼트들을 읽게 되는데 시간차로 인해 제대로 렌더링이 되지 않을 수도 있다.-->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-kenU1KFdBIe4zVF0s0G1M5b4hcpxyD9F7jL+jjXkk+Q2h455rYXK/7HAuoJl+0I4" crossorigin="anonymous"></script>
</body>
</html>
```

# index.th.xml

``` html
<?xml version="1.0"?>
<thlogic>
    <attr sel = "#header" th:replace = "header :: header" />
    <attr sel = "#footer" th:replace = "footer :: footer" />
```

> `thlogic` : 타임리프 가장 상위 레벨에 위치, html 의 `<body>`와 유사한 역할, 시작과 끝 정의 
> `attr` : attribute 즉 html 속성을 조작하거나 설정. 
> `sel` : 선택자(selector) , 특정 html 요소 선택, 해당 요소에 대한 타임리프 속성 적용하는데 사용. css 선택자와 유사한 형태.  
> `th:replace` : 해당 요소를 다른 템플릿으로 대체(replace) 하는데 사용한다. 

즉, html 에서 `header` 라는 id 를 가진 요소를 선택하여 header 템플릿으로 대체한다. footer 로 마찬가지


``` html
    <attr sel="#article-table">
        <attr sel="tbody" th:remove="all-but-first">
            <attr sel="tr[0]" th:each="article : ${articles}">
                <attr sel="td.title/a" th:text="${article.title}" th:href="@{'/articles/' + ${article.id}}" />
                <attr sel="td.hashtag" th:text="${article.hashtag}" />
                <attr sel="td.user-id" th:text="${article.nickname}" />
                <attr sel="td.created-at/time" th:datetime="${article.createdAt}" th:text="${#temporals.format(article.createdAt, 'yyyy-MM-dd')}" />
            </attr>
        </attr>
    </attr>
    ```

> ` th:remove="all-but-first"` : tbody 에서 첫 번째 요소를 제외한 모든 요소(행)를 제거. 
> 	즉, index.html 에서 구조를 위해 임의로 하드 코딩 해둔 부분을 삭제한다는 뜻. 
> `tr[0]` : tr 의 첫 요소부터 시작 
> `th:each` : 타임리프의 반복문. articles 라는 컬렉션의 각 요소를 반복하며 article 이라는 변수에 할당. 
> `sel="td.title/a"` : `<td class="title"><a>첫글</a></td>`  a 마크 다운 선택 
> `th:text="${article.title}"` : th 의 text 요소에 article 의 title 데이터를 할당. 
> `th:href="@{'/articles/' + ${article.id}}"` : href _ 링크 독적 생성 
> 	 `@{...}` 는 url 표현식이다. 
> 	`/articles/`: 기본 URL 경로
> 	`${article.id}`: 현재 반복되고 있는 `article` 객체의 `id` 속성값
> 
``` html
    <attr sel="#pagination">
        <attr sel="li[0]/a"
              th:text="'previous'"
              th:href="@{/articles(page=${articles.number - 1})}"
              th:class="'page-link' + (${articles.number} <= 0 ? ' disabled' : '')"
        />
        <attr sel="li[1]" th:class="page-item" th:each="pageNumber : ${paginationBarNumbers}">
            <attr sel="a"
                  th:text="${pageNumber + 1}"
                  th:href="@{/articles(page=${pageNumber})}"
                  th:class="'page-link' + (${pageNumber} == ${articles.number} ? ' disabled' : '')"
            />
        </attr>
        <attr sel="li[2]/a"
              th:text="'next'"
              th:href="@{/articles(page=${articles.number + 1})}"
              th:class="'page-link' + (${articles.number} >= ${articles.totalPages - 1} ? ' disabled' : '')"
        />
    </attr>
</thlogic>
```

> 페이지 네이션을 위한 부분 
> 
> `<attr sel="li[0]/a" ...>` : 첫 번째 `<li>` 요소를 선택
> `th:href="@{/articles(page=${articles.number - 1})}"`
> 	이전 페이지 링크의 주소를 설정한다. `${articles.number - 1}`는 현재 페이지 번호에서 1을 뺀 값
> `th:class="'page-link' + (${articles.number} <= 0 ? ' disabled' `: 
> 	 이전 페이지 링크의 클래스를 설정, 현재 페이지 번호가 0보다 작거나 같은 경우 `disabled` (비활성화)


