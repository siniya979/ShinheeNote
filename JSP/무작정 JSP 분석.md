```HTML
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
```

JSP 페이지의 콘텐츠 타입과 페이지 인코딩 설정. 

```HTML
<%@ taglib uri="/WEB-INF/.../test.tld" prefix="test" %>
```

사용자 지정 태그 라이브러리 임포트. 보통 웹 애플리케이션의 WEB-INF 디렉토리 내부에 위치한 태그 라이브러리 파일을 참조. prefix는 말그대로 호출 위한 프리픽스 

```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

JSTL 코어 태그 라이브러리 호출

> `<%@ %>` : JSP 지시자, JSP 속성 설정하거나 라이브러리 임포트 할 때 사용


```html
< form name = "", method = "", action = "" > 
</form>
```

name : 폼의 이름 설정, 폼 식별에 사용 
method : 폼 데이터를 서버로 전송할 때 사용할 HTTP 메소드 ( GET, POST 등 )
action : 폼 데이터를 전송할 대상. 주로 서버 측 프로그램의 url 이나 다른 리소스 url. 클라이언트가 폼 제출시 이 url 로 이동하거나 해당 url 로 post 또는 get 요청 

