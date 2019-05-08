Django 튜토리얼 7부: 세션 프레임워크
------------------------------------

이 문서는 저작자 동의없이 KAIST 대학정보화사업팀을 위하여 [Django Tutorial Part 7: Sessions framework](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Sessions)를 번역 편집하여 작성한 것입니다.

이 단계에서는 LocalLibrary 웹사이트를 확장하여 홈 페이지에 세션 기반 방문 카운터를 추가합니다. 이는 비교적 간단한 예제이지만 세션 프레임워크를 사용하여 사이트에서 익명 사용자의 지속적인 동작을 규정하는 방법을 보여줍니다.

> 선수지식: [Django 튜토리얼 6부: 일반 목록과 상세 보기](genericViews.md)까지 모든 튜토리얼들을 숙지하여야 합니다.
>
> 목표: 세션 사용 방법을 이해합니다.

---

### 개요

이전 단계에서 만든 [LocalLibrary](tutorialLocalLibraryWebsite.md) 웹사이트를 통해 사용자는 카탈로그에서 도서와 저자를 탐색할 수 있습니다. 데이터베이스로부터 동적으로 콘텐츠를 생성하지만 모든 사용자는 기본적으로 사이트를 사용할 때 동일한 페이지와 유형의 정보를 접근할 수 있습니다.

"실제" 라이브러리에서는 사이트와 설정 등에 대한 과거 사용 경험을 바탕으로 개별 사용자에게 사용자에 따른 사용 환경을 제공 할 수 있기를 원합니다. 예를 들어, 다음에 사용자가 방문 또는 설정 저장 및 실행 했을 때 이전에 알려주었던 경고 메시지를 보여주지 않을 수 있습니다 (예 : 각 페이지에 표시할 검색 결과 수).

세션 프레임워크를 통해 이러한 종류의 동작을 구현할 수 있으므로 사이트 방문자별로 임의의 데이터를 저장하고 검색할 수 있습니다.

---

### 세션이란?

웹 브라우저와 서버 간의 모든 통신은 상태가 없는 HTTP 프로토콜을 통해 이루어집니다. 프로토콜에 상태가 없다라는 것은 클라이언트와 서버 간의 메시지들이 서로 독립적이라는 것을 의미합니다. 이전 메시지에 따른 "시퀀스" 또는 동작에 대한 개념이 없는 것입니다. 따라서 클라이언트와의 지속적인 관계를 유지하는 사이트를 원한다면 직접 구현해야 합니다.

세션은 사이트와 특정 브라우저 사이의 "상태"를 추적하는 데 Django(대부분의 인터넷)가 사용하는 메커니즘입니다. 세션을 사용하면 브라우저는 임의의 데이터를 저장할 수 있으며 브라우저가 연결될 때마다 이 데이터를 사이트에서 사용할 수 있습니다. 세션에 연결된 개별 데이터 항목을 참조하는 "키"를 데이터의 저장 및 검색에 모두 사용합니다.

Django는 브라우저와 사이트와 관련된 세션을 식별하는 세션 id를 저장하기 위하여 쿠키를 사용합니다. 기본적으로 실제 세션 데이터를 사이트 데이터베이스에 저장합니다 (쿠키에 데이터를 저장하는 것은 악의적인 사용자에 취약하므로 데이터베이스에 저장하는 것이 보다 안전합니다). Django가 세션 데이터를 다른 장소 (캐시, 파일, "보안"쿠키)에 저장하도록 구성할 수 있지만 기본 위치 즉 데이터베이스가 비교적 안전하고 좋은 선택입니다.

---

### 세션 활성화

세션은 [골조 웹사이트를 만들 때](skeletonWebsite.md) (Django 튜토리얼 2부) 자동으로 활성화되었습니다 .

프로젝트 파일 (<b>locallibrary/locallibrary/settings.py</b>)의 <code>INSTALLED_APPS</code> 및 <code>MIDDLEWARE</code> 섹션에 아래과 같이 설정합니다.

```python
INSTALLED_APPS = [
    ...
    'django.contrib.sessions',
    ....

MIDDLEWARE = [
    ...
    'django.contrib.sessions.middleware.SessionMiddleware',
    ....
```

---

### 세션 사용

요청의 매개 변수 (HttpRequest에 전달되는 뷰의 첫 번째 인수)로부터 뷰에 저장된 세션 속성을 액세스할 수 있습니다. 이 세션 속성은 현재 사용자에 대한 연결(보다 정확하게는 이 사이트의 브라우저 쿠키에 있는 세션 ID로 식별되는 현재 브라우저에 대한 연결)을 지정합니다.

세션 속성은 (Python) 사전 타입 객체로 수정하고, 읽고, 쓸 수 있습니다. 모든 데이터를 지우고, 키가 있는지 테스트하고, 데이터를 루핑하는 등 사전에서 할 수 있는 일반적인 작업을 수행 할 수 있습니다. 그러나 대부분의 경우 표준 "사전" API를 사용하여 값을 가져오고 설정합니다.

아래의 코드는 현재 세션 (브라우저)과 연관된 "my_car" 키를 사용하여 일부 데이터를 가져오고, 설정하고, 삭제할 수 있는 방법을 보여 줍니다.

> <b>Note</b>: Django의 장점 중 하나는 세션을 사용자 뷰의 현재 요청에 연결하는 메커니즘에 대해 생각할 필요가 없다는 것입니다. 아래 뷰를 사용하면 my_car에 대한 정보가 현재 요청을 보낸 브라우저에만 연결된다는 것을 알 수 있습니다.

```python
# Get a session value by its key (e.g. 'my_car'), raising a KeyError if the key is not present
my_car = request.session['my_car']

# Get a session value, setting a default if it is not present ('mini')
my_car = request.session.get('my_car', 'mini')

# Set a session value
request.session['my_car'] = 'mini'

# Delete a session value
del request.session['my_car']
```

API는 또한 관련 세션 쿠키를 관리하는 데 주로 사용되는 여러 가지 방법을 제공합니다. 예를 들어 클라이언트 브라우저에서 쿠키 지원 여부의 테스트, 쿠키 만료 날짜의 설정과 확인, 데이터 저장소에서 만료된 세션을 삭제하는 등의 메서드가 있습니다. [How to use sessions](https://docs.djangoproject.com/en/2.1/topics/http/sessions/)(Django 문서)에서 전체 API를 참고하십시오.

---

### 세션 데이터 저장

기본적으로 Django는 세션 데이터베이스에 저장하고 세션이 <i>수정</i>(할당)되거나 <i>삭제</i>될 때 세션 쿠키를 클라이언트에 보냅니다. 이전 섹션에서 설명한 것처럼 세션 키를 사용하여 일부 데이터를 업데이트하는 경우 이 점에 대해 걱정할 필요가 없습니다. 예를 들면,

```python
# This is detected as an update to the session, so session data is saved.
request.session['my_car'] = 'mini'
```

세션 데이터의 일부를 업데이트할 경우 Django는 세션이 변경되고 데이터를 저장했음을 인식하지 못합니다 (예 : "<code>my_car</code>" 데이터에서 "<code>wheel</code>" 데이터를 변경하려는 경우, 아래 그림과 같이). 이 경우 명시적으로 세션이 수정되었음을 표시해야 합니다.

```python
# Session object not directly modified, only data within the session. Session changes not saved!
request.session['my_car']['wheels'] = 'alloy'

# Set session as modified to force data updates/cookie to be saved.
request.session.modified = True
```

> <b>Note</b>: <code>SESSION_SAVE_EVERY_REQUEST = True</code>를 프로젝트 설정(<b>locallibrary/locallibrary/settings.py</b>)에 추가하여 사이트가 각 요청마다 데이터베이스를 업데이트하거나 쿠키를 보내도록 수정할 수 있습니다.

---

### 간단한 예 - 방문 카운터

라이브러리를 업데이트하여 현재 사용자에게 <i>LocalLibrary</i> 홈페이지를 몇 번 방문했는지 알려주는 간단한 예입니다.

<b>/locallibrary/catalog/views.py</b>를 열고 "<code># Number of visits to this view ... </code>" 주석 코드 블럭을 아래와 같이 변경하십시오.

```python
def index(request):
    ...

    num_authors = Author.objects.count()  # The 'all()' is implied by default.

    # Number of visits to this view, as counted in the session variable.
    num_visits = request.session.get('num_visits', 0)
    request.session['num_visits'] = num_visits + 1

    context = {
        'num_books': num_books,
        'num_instances': num_instances,
        'num_instances_available': num_instances_available,
        'num_authors': num_authors,
        'num_visits': num_visits,
    }

    # Render the HTML template index.html with the data in the context variable.
    return render(request, 'index.html', context=context)
```

여기서는 먼저 <code>'num_visits'</code> 세션 키 값을 얻습니다. 이전에 설정되지 않았다면 값을 0으로 설정합니다. 요청을 받을 때마다 값을 증가시키고 (사용자의 다음 페이지 방문을 위하여) 세션에 다시 저장합니다. 다음 <code>num_visits</code> 변수를 컨텍스트 변수의 템플리트로 전달합니다.

> <b>Note</b>: 브라우저의 쿠키 지원 여부도 테스트할 수 있지만 (예: [How to use sessions](https://docs.djangoproject.com/en/2.1/topics/http/sessions/) 참조), 이와 무관하게 UI를 디자인할 수 있습니다.

컨텍스트 변수를 디스플레이하기 위하여 "Dynamic content" 섹션의 맨 아래에 있는 주 HTML 템플리트 (<b>/locallibrary/catalog/templates/index.html</b>)에 아래 블록 끝에 표시된 줄을 추가하십시오.

```html
<h2>Dynamic content</h2>

<p>The library has the following record counts:</p>
<ul>
  <li><strong>Books:</strong> {{ num_books }}</li>
  <li><strong>Copies:</strong> {{ num_instances }}</li>
  <li><strong>Copies available:</strong> {{ num_instances_available }}</li>
  <li><strong>Authors:</strong> {{ num_authors }}</li>
</ul>

<p>You have visited this page {{ num_visits }}{% if num_visits == 1 %} time{% else %} times{% endif %}.</p>
```

수정을 저장하고 테스트 서버를 다시 시작하십시오. 페이지를 새로 고칠 때마다 숫자가 업데이트되어야 합니다.

---

### 요약

세션을 사용하여 익명 사용자와 상호 작용을 향상시키는 것이 얼마나 간단한 지 알 수 있었습니다.

다음 단계에서는 인증 및 권한 부여 (사용 권한) 프레임워크에 대해 설명하고 사용자 계정을 지원하는 방법을 보일 것입니다.

---

### 참고 문서

-	[How to use sessions](https://docs.djangoproject.com/en/2.1/topics/http/sessions/) (Django docs)

---

### 이 단원에서 아래 단계를 다룹니다.

-	[Django 소개](introduction.md)
-	[Django 개발 환경 설정](developmentEnvironment.md)
-	[Django 튜토리얼: The Local Library website](tutorialLocalLibraryWebsite.md)
-	[Django 튜토리얼 2부: 웹사이트 골조 만들기](skeletonWebsite.md)
-	[Django 튜토리얼 3부: 모델](models.md)
-	[Django 튜토리얼 4부: Django 관리 사이트](adminSite.md)
-	[Django 튜토리얼 5부: 홈 페이지 만들기](homePage.md)
-	[Django 튜토리얼 6부: 일반 목록과 상세 보기](genericViews.md)
-	[Django 튜토리얼 7부: 세션 프레임워크](sessions.md)
-	[Django 튜토리얼 8부: 사용자 인증 및 사용 권한](authentication.md)
-	[Django 튜토리얼 9부: 양식 작업](forms.md)
-	[Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅](testing.md)
-	[Django 튜토리얼 11부: 운영으로 Django 전개](deployment.md)
-	[Django 웹 응용프로그램 보안](webApplicationSecurity.md)
