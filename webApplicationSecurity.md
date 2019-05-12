Django 웹 애플리케이션 보안
---------------------------

사용자 데이터를 보호하는 것은 모든 웹 사이트 디자인에서 필수적인 부분입니다. 이전 [Web security](https://developer.mozilla.org/en-US/docs/Web/Security) 문서에서 보다 일반적인 보안 위협에 대해 설명했습니다. 이 단원에서는 Django의 내장된 보호 기능이 이러한 위협을 처리하는 방법에 대한 실용적인 시연을 보입니다.

> 선수지식: 서버 사이드 프로그래밍 "[Website security](https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Website_security)"를 읽으십시오. 적어도 [Django 튜토리얼 9부: 양식 작업](forms.md)까지는 숙지하십시오.
>
> 목표: Django 웹 어플리케이션의 보안을 유지하기 위해 해야 할 일 (또는 하지 말아야 할 일)을 이해합니다.

---

### 개요

[Website security](https://developer.mozilla.org/en-US/docs/Web/Security)에서는 서버 사이드 설계를 위한 웹사이트 보안의 의미와 보호해야 할 일반적인 위협에 대한 개요를 다룹니다. 이 단원의 주요 메시지 중 하나는 웹 어플리케이션이 브라우저의 데이터를 신뢰하면 거의 모든 공격이 성공한다는 것입니다.

> **Important**: 웹사이트 보안에 대해 배울 수 있는 가장 중요한 교훈은 브라우저가 전송한 데이터를 절대 신뢰하지 않는 것입니다. URL 매개 변수 중 GET 요청 데이터, POST 데이터, HTTP 헤더와 쿠키, 사용자 업로드 파일 등이 이에 해당됩니다. 모든 입력 데이터를 항상 확인하고 안전하게 만들어야 합니다. 항상 최악의 상황을 가정합니다.

일반적인 위협의 많은 부분을 프레임워크가 처리할 수 있다는 것은 Django 사용자들에게 좋은 소식입니다. [Security in Django](https://docs.djangoproject.com/en/2.0/topics/security/)(Django 문서)에서는 Django의 보안 기능과 Django 기반 웹사이트 보안 방법에 대해 설명합니다.

---

### 일반적인 위협과 보호

이 단원에서는 Django 문서를 중복 설명하는 대신 Django [LocalLibrary](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website) 튜토리얼 맥락에서 몇 가지 보안 기능을 시연합니다.

#### Cross site scripting (XSS)

XSS는 공격자가 웹사이트를 <i>통해</i> 클라이언트 사이드 스크립트를 다른 사용자의 브라우저에 주입하는 공격 클래스를 일컫는 것입니다. 대개 악의적인 스크립트를 데이터베이스에 저장하여 다른 사용자가 검색과 디스플레이하도록 하거나 사용자 브라우저에서 공격자의 JavaScript를 실행하는 링크를 클릭하도록 유도합니다.

Django의 템플리트 시스템은 HTML에서 "위험한" [특정 문자를 이스케이프](https://docs.djangoproject.com/en/2.0/ref/templates/language/#automatic-html-escaping) 처리하여 대다수의 XSS 공격으로부터 사용자를 보호합니다. [Django 튜토리얼 9부: 양식 작업](forms.md)에서 Create-author 양식을 사용하여 LocalLibrary 웹사이트에 JavaScript를 삽입하여 이를 시연할 수 있습니다.

1.	개발 서버를 사용하여 웹사이트를 시작하십시오 (<code>python3 manage.py runserver</code>).
2.	로컬 브라우저에서 사이트를 열고 수퍼유저 계정으로 로그인하십시오.
3.	author-creation page (URL: http://127.0.0.1:8000/catalog/author/create/)로 이동하십시오.
4.	새로운 사용자의 이름과 날짜 정보를 입력한 다음 Last Name 필드에 다음 텍스트를 추가하십시오. <code>\<script>alert('Test alert');\</script></code>

![](Pics/author_create_form_alert_xss.png)

> **Note**: 이것이 실행되면 브라우저에 경고 상자를 디스플레이하지만 해는 끼치지 않는 스크립트입니다. 레코드를 입력할 때 경고 메시지가 디스플레이되면 사이트는 XSS 위협에 취약한 것입니다.

1.	<b>Submit</b>을 눌러 레코드를 저장하십시오.
2.	저자를 저장하면 아래와 같이 표시됩니다. XSS 보호 때문에 <code>alert()</code>가 실행되어서는 안됩니다. 대신 스크립트는 일반 텍스트로 디스플레이됩니다.

![](Pics/author_detail_alert_xss.png)

페이지 HTML 소스 코드를 보면 스크립트 태그의 위험한 문자는 아래와 같이 무해한 이스케이프 코드로 바뀌었음(예: <code>></code> 지금은 <code>\&gt;</code>)을 알 수 있습니다.

```html
<h1>Author: Boon&lt;script&gt;alert(&#39;Test alert&#39;);&lt;/script&gt;, David (Boonie) </h1>
```

Django 템플리트을 사용하면 대다수의 XSS 공격으로부터 보호 받을 수 있습니다. 그러나 이 보호 기능을 해제할 수 있으며 보호 기능은 일반적으로 사용자 입력이 필요하지 않는 모든 태그에 자동으로 적용되지는 않습니다 (예를 들어 양식 필드의 help_text는 대개 사용자가 입력을 제공하지 않기 때문에 Django 그 값을 이스케이프 처리하지 않습니다).

XSS 공격은 (페이지에 포함되기 전에 데이터가 충분히 안전하지 않는) 쿠키, 웹 서비스 또는 업로드된 파일과 같이 신뢰할 수 없는 다른 데이터 소스에서 비롯된 것일 수도 있습니다. 이러한 출처의 데이터를 표시하려면 자체 안전 처리 코드를 추가해야 할 수도 있습니다.

#### Cross site request forgery (CSRF) 보호

CSRF 공격으로 악의있는 사용자가 다른 사용자의 지식이나 동의없이 해당 사용자의 자격 증명을 사용하여 작업을 실행할 수 있습니다. 예를 들어 LocalLibrary에 대한 추가 저자를 만들고자 하는 해커가 있는 경우를 생각해 보십시오.

> **Note**: 분명히 해커는 돈을 위해 이를 하지 않습니다. 더 야심찬 해커는 다른 사이트에서 동일한 해법을 사용하여 훨씬 위험한 일(예: 자신의 계좌로 돈을 이체하는 등)을 할 수 있습니다.

이렇게 하기 위해 아래 HTML 파일을 만들 수 있습니다. 이 파일에는 파일을 로드하자마자 제출되는 (이전 섹션에서 사용한) author-creation 양식이 들어 있습니다. 다음 파일을 모든 사서에게 보내고 파일을 열어 보라고 제안합니다 (정당한 무해한 정보가 담겨 있습니다). 로그인한 사서가 파일을 열면 양식이 자격 증명과 함께 제출되어 새로운 저자가 생성됩니다.

```html
<html>
<body onload='document.EvilForm.submit()'>

<form action="http://127.0.0.1:8000/catalog/author/create/" method="post" name='EvilForm'>
  <table>
    <tr><th><label for="id_first_name">First name:</label></th><td><input id="id_first_name" maxlength="100" name="first_name" type="text" value="Mad" required /></td></tr>
    <tr><th><label for="id_last_name">Last name:</label></th><td><input id="id_last_name" maxlength="100" name="last_name" type="text" value="Man" required /></td></tr>
    <tr><th><label for="id_date_of_birth">Date of birth:</label></th><td><input id="id_date_of_birth" name="date_of_birth" type="text" /></td></tr>
    <tr><th><label for="id_date_of_death">Died:</label></th><td><input id="id_date_of_death" name="date_of_death" type="text" value="12/10/2016" /></td></tr>
  </table>
  <input type="submit" value="Submit" />
</form>

</body>
</html>
```

개발 웹 서버를 실행하고 수퍼유저 계정으로 로그인하십시오. 위의 텍스트를 파일로 복사한 다음 브라우저에서 엽니다. Django는 이런 종류의 것을 막아주기 때문에 CSRF 오류가 발생합니다.

보호 기능을 사용하는 방법은 <code>{% csrf_token %}</code> 템플리트 태그를 양식 정의에 포함시키는 것입니다. 다음 이 토큰은 현재 브라우저에서 사용자의 고유한 값과 함께 HTML에 렌더링됩니다.

```html
<input type='hidden' name='csrfmiddlewaretoken' value='0QRWHnYVg776y2l66mcvZqp8alrv4lb8S8lZ4ZJUWGZFA5VHrVfL2mpH29YZ39PW' />
```

Django는 사용자 또는 브라우저에 특정된 키를 생성하여 필드를 포함하지 않거나 사용자 또는 브라우저에 부정확한 필드 값을 포함하는 양식을 거부합니다.

이 유형의 공격을 위해 해커는 특정 대상 사용자에 대한 CSRF 키를 찾아야 합니다. 또한 악의적인 파일을 모든 사서에게 보내고 CSRF 키가 브라우저마다 다르기 때문에 그 중 하나가 열리길 바라는 "scattergun" 접근 방식을 사용할 수 없습니다.

Django는 CSRF 보호 기능을 기본적으로 사용하도록 설정되어 있습니다. 양식에 항상 <code>{% csrf_token %}</code> 템플리트 태그를 사용해야 하며 데이터베이스에 데이터를 변경하거나 추가할 수 있는 요청에 `POST`를 사용해야 합니다.

#### 기타 보호 기능

Django는 다른 형태의 보호 기능을 제공합니다 (대부분은 시연하기 어렵거나 특별히 유용하지 않습니다).

##### SQL injection 방지

SQL 주입(injection) 취약점은 악의적인 사용자가 데이터베이스에 임의의 SQL 코드를 실행할 수 있게 하여 사용자 권한에 관계없이 데이터를 접근, 수정 또는 삭제할 수 있게 합니다. 거의 모든 경우 Django의 쿼리세트와 모델을 사용하여 데이터베이스를 접근하므로, 기본 데이터베이스 드라이버는 결과 SQL을 정확히 이스케이프합니다. 원시 쿼리 또는 사용자 정의 SQL을 작성해야 하는 경우 SQL 주입 방지에 대해 명시적으로 고려해야 합니다.

##### Clickjacking 방지

이 공격에서 악의적인 사용자는 보이는 최상위 사이트에 대한 클릭을 가로 채어 그 아래의 숨겨진 페이지로 라우팅합니다. 예를 들어 이 기술은 합법적인 은행 사이트를 디스플레이하는 데 사용되지만 공격자가 제어하는 보이지 않는 <code>\<iframe></code>에서 로그인 자격 증명을 캡처합니다. Django는 브라우저에서 지원한다면 사이트가 프레임 내부에서 렌더링되는 것을 방지 할 수 있는 [<code>X-Frame-Options middleware</code>](https://docs.djangoproject.com/en/2.0/ref/middleware/#django.middleware.clickjacking.XFrameOptionsMiddleware) 형태로 [Clickjacking](https://docs.djangoproject.com/en/2.0/ref/clickjacking/#clickjacking-prevention) 방지 기능을 제공합니다.

##### SSL/HTTPS 집행

웹 서버에서 SSL/HTTPS를 활성화하면 일반 텍스트로 전송되는 인증 자격 증명을 비롯하여 사이트와 브라우저간 모든 트래픽을 암호화할 수 있습니다 (HTTPS 사용을 권장합니다). HTTPS가 활성화되면 Django는 사용할 수 있는 여러 다른 보호 기능을 제공합니다.

- <code>SECURE_PROXY_SSL_HEADER</code>는 비HTTP 프록시에서 들어오는 것일지라도  콘텐츠가 안전한지 여부를 확인하는 데 사용할 수 있습니다.
- <code>SECURE_SSL_REDIRECT</code>는 모든 HTTP 요청을 HTTPS로 리디렉션하는 데 사용됩니다.
- [HTTP Strict Transport Security](https://docs.djangoproject.com/en/2.0/ref/middleware/#http-strict-transport-security)(HSTS)을 사용하십시오. 이는 특정 사이트에 대한 향후 모든 연결은 항상 HTTPS를 사용해야 한다는 것을 브라우저에 알리는 HTTP 헤더입니다. 이 설정은 HTTP 요청을 HTTPS로 리디렉션하는 것과 결합되어 성공적인 연결이 발생한 후에 항상 HTTPS가 사용되도록 합니다. HSTS는 <code>SECURE_HSTS_SECONDS</code>와 <code>SECURE_HSTS_INCLUDE_SUBDOMAINS</code> 또는 웹 서버에서 구성할 수 있습니다.
- <code>SESSION_COOKIE_SECURE</code>와 <code>CSRF_COOKIE_SECURE</code>를 True로 설정하여 '안전한' 쿠키를 사용하십시오. 이렇게 하면 쿠키가 HTTPS를 통해서만 전송됩니다.

#### 호스트 헤더 검증

[<code>ALLOWED_HOSTS</code>](https://docs.djangoproject.com/en/2.0/ref/settings/#std:setting-ALLOWED_HOSTS)를 사용하여 신뢰할 수 있는 호스트의 요청만 수락하십시오.

위의 메커니즘을 사용하려면 많은 다른 보호 장치와 주의 사항이 있습니다. 이는 Django가 제공하는 기능에 대한 개요를 설명하지만 Django 보안 문서를 읽어야 합니다.

---

### 요약

Django는 XSS와 CSRF 공격을 비롯한 여러 가지 공통적인 위협을 효과적으로 보호합니다. 이 단원에서는 <i>LocalLibrary</i> 웹사이트에서 Django가 이러한 특정 위협을 처리하는 방법에 대해 설명했습니다. 또한 다른 보호 기능에 대하여도 간략한 개요를 설명했습니다.

이는 웹 보안에 대한 매우 간단한 시작입니다. 더 깊은 이해를 얻으려면 [Security in Django](https://docs.djangoproject.com/en/2.0/topics/security/) 읽기를 적극 추천합니다.

Django에 대한 이 튜토리얼의 다음 단계와 마지막 단계는 [assessment task](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/django_assessment_blog)을 완료하는 것입니다.

---

### 참고 문서

- [Security in Django](https://docs.djangoproject.com/en/2.0/topics/security/) (Django docs)
- [Server side website security](https://developer.mozilla.org/en-US/docs/Web/Security) (MDN)
- [Web security](https://developer.mozilla.org/en-US/docs/Web/Security) (MDN)
- [Securing your site](https://developer.mozilla.org/en-US/docs/Web/Security/Securing_your_site) (MDN)

---

### 이 튜토리얼은 다음과 같이 구성되어 있습니다.

-	[Django 소개](introduction.md)
-	[Django 개발 환경 설정](developmentEnvironment.md)
-	[Django 튜토리얼: The Local Library website](tutorialLocalLibraryWebsite.md)
-	[Django 튜토리얼 2부: 웹사이트 골조 만들기](skeletonWebsite.md)
-	[Django 튜토리얼 3부: 모델](models.md)
-	[Django 튜토리얼 4부: Django 관리 사이트](adminSite.md)
-	[Django 튜토리얼 5부: 홈 페이지 만들기](homePage.md)
-	[Django 튜토리얼 6부: 일반 목록과 상세 보기](genericViews.md)
-	[Django 튜토리얼 7부: 세션 프레임워크](sessions.md)
-	[Django 튜토리얼 8부: 사용자 인증과 권한관리](authentication.md)
-	[Django 튜토리얼 9부: 양식](forms.md)
-	[Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅](testing.md)
-	[Django 튜토리얼 11부: 프로덕션으로 Django 배치](deployment.md)
-	[Django 웹  보안](webApplicationSecurity.md)
