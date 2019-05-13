Django 소개
-----------

---

이 첫 번째 단원에서는 "Django는 무엇입니까?"라는 질문에 대하여 답합니다. 왜 Django 웹 프레임워크의 특별한 점을 개략적으로 설명합니다. 이 Django 웹 프레임워크 단원에서 자세히 다루지 않을 고급 기능과 주요 기능에 대해 간략하게 소개합니다. 또한 Django 어플리케이션의 주요 구성 요소 중 일부를 설명함으로써 설치와 준비 작업에 대한 아이디어를 제공합니다.

> 선수지식: 기본적인 컴퓨터 활용 능력, 즉 [서버사이드 웹사이트 프로그래밍](../First_steps/webProgrammingFramework.md), 특히 [웹사이트에서 클라이언트-서버 상호 작용](../First_steps/clientServerOverview.md)의 메커니즘에 대한 전반적인 이해가 필요합니다.
>
> 목표: Django가 무엇인지, Django가 제공하는 기능과 Django 어플리케이션의 주요 구성 요소를 이해합니다.

---

### Django 란?

Django는 안전하고 관리하기 용이한 웹사이트를 신속하게 개발할 수 있는 고급 Python 웹 프레임워크입니다. 숙련된 개발자가 만든 Django는 웹 개발의 번거로움을 덜어 주므로 바닥에서부터 만들 필요없이 앱을 작성하는 데 집중할 수 있습니다. Django는 무료이며, 오픈 소스입니다. 지금도 발전하고 있으며, 활발한 커뮤니티, 훌륭한 문서, 무료 및 유료 지원을 위한 다양한 옵션을 제공합니다.

Django는 다음과 같은 소프트웨어를 작성할 수 있도록 지원합니다.

**완벽한 (Complete)**

Django는 "건전지가 포함된(Batteries included)" 철학으로 개발자가 "즉시" 개발을 시작할 수 있는 거의 모든 기능을 제공합니다. 필요한 모든 기능이 "제품"의 구성 요소이므로 모든 기능이 서로 원활하게 작동하고, 일관된 디자인 원칙을 따르며, 광범위한 [최신 문서](https://docs.djangoproject.com/en/stable/)를 제공하고 있습니다.

**유연한 (versatile)**

Django는 콘텐츠 관리 시스템과 위키부터 소셜 네트워크, 뉴스 사이트에 이르기까지 거의 모든 종류의 웹사이트를 이미 구축했으며 구축할 수도 있습니다. 모든 클라이언트사이드 프레임워크에서 작동할 수 있으며, HTML, RSS 피드, JSON, XML 등 거의 모든 형식의 컨텐츠를 전달할 수 있습니다.

내부적으로 원하는 기능을 위하여 선택할 수 있지만 (예: 대중적인 데이터베이스, 템플리트 엔진 등), 필요하다면 다른 구성 요소를 사용할 수 있도록 확장할 수도 있습니다.

**안전한 (secure)**

Django는 개발자가 웹사이트를 보호하기 위해 자동으로 "일을 정확하게 수행하도록" 설계된 프레임워크를 제공함으로써 많은 일반적인 보안 실수를 피할 수 있도록 도와줍니다. 예를 들어 Django는 세션 정보를 취약한 곳(예: 쿠키에 키가 포함되어 있고 실제 데이터는 데이터베이스에 저장됨)에 저장하거나 비밀번호 해시 대신 비밀번호를 직접 저장하는 것과 같은 일반적인 실수를 피하고, 사용자 계정과 비밀번호를 안전하게 관리할 수 있는 방법을 제공합니다.

암호 해시는 암호를 입력으로 [암호 해시 함수](https://en.wikipedia.org/wiki/Cryptographic_hash_function)를 이용하여 만든 고정 길이 값입니다. Django는 입력된 암호의 해시 함수 실행 결과와 저장된 해시 값을 비교하여 일치함을 확인합니다. 그러나 함수의 "단방향" 특성으로 인해 저장된 해시 값이 손상되더라도 공격자가 원래 암호를 알아내는 것은 어렵습니다.

Django는 SQL 삽입, 크로스사이트 스크립팅, 크로스사이트 요청 위조, 클릭 재킹 (이러한 공격에 대한 자세한 내용은 [웹사이트 보안](../AboutServersideWebProgrammingFirstStep/webSiteSecurity.md) 참조) 등 많은 취약점에 대한 보안 기능을 기본적으로 갖고 있습니다.

**확장성 (Scalable)**

Django는 컴포넌트기반 "[비공유 (shared-nothing)](https://en.wikipedia.org/wiki/Shared_nothing_architecture) 아키텍처를 사용합니다 (아키텍처의 각 부분이 다른 부분에 서로 독립적이므로 필요에 따라 교체하거나 변경할 수 있습니다). 다른 부분을 명확히 구분하면 캐싱 서버, 데이터베이스 서버 또는 어플리케이션 서버와 같은 수준의 하드웨어를 추가하여 트래픽 증가에 맞게 확장할 수 있습니다. 트랙픽이 많은 사이트 중 일부(예를 들어 Instagram 및 Disqus)에서는 Django 호가장에 성공하여 서비스하고 있습니다.

**유지보수 (Maintainable)**

유지 보수가 쉽고 재사용이 가능한 코드를 장려하는 디자인 원리와 패턴을 사용하여 Django 코드를 작성합니다. 특히, "Do not Repeat Yourself (DRY)" 원칙을 적용하여 불필요한 중복을 제거하여 코드의 양(LoC)을 줄였습니다. Django는 관련 기능을 재사용 가능한 "어플리케이션"으로 그룹화하여 하위 수준에서 모듈로 관련된 코드를 ([Model View Controller (MVC)](https://developer.mozilla.org/en-US/Apps/Fundamentals/Modern_web_app_architecture/MVC_architecture) 패턴의 라인을 따라) 그룹핑합니다.

**이식 가능한 (Portable)**

Django는 Python으로 작성되어 많은 플랫폼에서 실행할 수 있습니다. 즉, 특정 서버 플랫폼에 종속되지 않아 Linux, Windows, Mac OS X 등의 다양한 버전에서 어플리케이션을 실행할 수 있습니다. 또한 많은 웹 호스팅 공급업체에서 Django를 원활히 지원하며, 이들은 특정 인프라와 Django 사이트 호스팅을 위한 문서를 제공합니다.

---

### Django 프로젝트 시작은?

신문 웹사이트를 만들고 유지 관리하는 웹 팀이 2003년에서 2005년 동안 Django를 처음 개발했습니다. 여러 사이트를 개발한 다음 팀은 여러 공통 코드와 디자인 패턴을 추출하여 재사용하기 시작했습니다. 이 공통 코드는 제네릭한 웹 개발 프레임워크로 발전하였고, 2005년 7월에 "Django" 프로젝트로 오픈소스화하였습니다.

Django는 2008년 9월에 발표된 첫 번째 버전 (1.0)을 시작으로 2017년 버전 2.0을 발표하였고 2019년 3월 현재 버전 2.1이 최근 릴리스되었으며, 베타 버전 2.2에 이르기까지 계속해서 성장하고 진화하고 있습니다. 각 릴리스에는 새로운 타입의 데이터베이스 지원, 템플리트 엔진과 캐싱에서 부터, "일반" 뷰 기능, (수 많은 프로그래밍 작업에서 개발자가 작성해야 하는 코드의 양을 줄이는) 클래스 등에 이르기까지 새로운 기능과 버그 수정이 추가되었습니다.

> **note**: Django 웹사이트의 [릴리스 노트](https://docs.djangoproject.com/en/stable/releases/)를 체크아웃하여 최신 버전의 변경 사항과 Django 개선 작업을 확인하십시오.

Django는 수천 명의 사용자와 기여자가 참여하여 번창하고 있는 협력적인 오픈 소스 프로젝트입니다. Django는 태생부터 반영된 몇몇 기능을 가지고 있지만 모든 타입의 웹사이트를 개발할 수 있는 다양한 프레임워크로 발전해 왔습니다.

---

### 대중적?

\([Hot Framework](http://hotframeworks.com/)와 같은 사이트에서는 GitHub 프로젝트 수와 각 플랫폼에 대한 StackOverflow 질문 수와 같은 자료를 바탕으로 인지도를 평가하려고 시도하지만) 서버사이드 프레임워크 사용의 대중성을 쉽게 확실히 측정할 수는 없습니다. 가장 좋은 질문은 Django의 "충분한 인지도"로 개발자의 주의를 끌지 못하는 플랫폼의 문제를 피할 수 있도록 충분히 대중적는 가를 살펴 보는 것입니다. 계속 진화하고 있습니까? 필요한 경우 도움을 받을 수 있습니까? Django를 배우면 좋은 직장을 얻을 수 있는 기회가 있습니까? 등입니다.

Django를 이용한 개발 경력을 언급하는 많은 프로필 사이트의 숫자, 코드베이스에 기여한 개발자들의 숫자와 유무료 지원을 제공하는 개발자들의 숫자를 보면 Django는 대중적인 프레임워크입니다.

Django를 사용하는 유명 사이트로는 Disqus, Instagram, Knight Foundation, MacArthur Foundation, Mozilla, National Geographic, Open Knowledge Foundation, Pinterest, Open Stack (출처: Django 홈 페이지) 등이 있습니다.

---

### 독적선 (opinionated)?

웹 프레임워크는 종종 스스로를 "독선적인" 또는 "협업적인" 것으로 나눕니다.

독선적 프레임워크는 특정 작업을 처리하는 "올바른 방법"에 대한 견해를 갖고 있는 프레임워크입니다. 그들은 종종 (특정 유형의 문제를 해결하며) 특정 영역에서의 신속한 개발을 지원합니다. 무엇이든 할 수 있는 적합한 방법은 대개 잘 이해되고 잘 문서화되어 있기 때문입니다. 그러나 목표 도메인이 아닌 문제를 취급할 때 유연성이 떨어질 수 있으며 사용할 수있는 구성 요소와 접근 방식에 대한 선택의 폭이 줄어듭니다.

대조적으로, 협업적 프레임워크에서는 목표를 달성하기 위해 구성 요소를 함께 묶는 효율적인 방법에 대한 제한이 훨씬 적어 심지어 개발자들도 어떤 구성 요소를 사용해야 할 지 모릅니다. 따라서 개발자가 직접 특정 구성 요소를 찾는 비용은 들지만 가장 적합한 도구를 사용하여 특정 작업을 쉽게 수행할 수 있습니다.

Django는 "어느 정도 독단적"이므로 "두 견해의 장점"을 제공합니다. 대부분의 웹 개발 작업과 사용을 선호하는 한 두 가지 방법을 채택할 수 있도록 구성 요소 집합을 제공합니다. 그러나 Django의 비 공유(shared-nothing) 아키텍처에서는 일반적으로 여러 옵션 중 하나를 선택하거나 사용할 수 있거나 원하는 경우 완전히 새로운 옵션 지원도 추가할 수 있습니다.

---

### Django 코드

전통적인 데이터 중심 웹사이트에서는 웹 어플리케이션이 웹 브라우저 (또는 다른 클라이언트)의 HTTP 요청을 기다립니다. 요청을 수신하면 어플리케이션은 URL 및 <code>POST</code> 데이터 또는 <code>GET</code> 데이터의 정보를 기반으로 이를 처리합니다. 필요에 따라 데이터베이스에서 정보를 읽거나 쓰거나 요청을 처리하기 위하여 필요한 다른 작업을 수행할 수 있습니다. 다음 어플리케이션은 검색된 데이터를 HTML 템플리트 양식에 채워 웹 브라우저에 응답으로 반환합니다. 브라우저는 동적으로 HTML 페이지를 만들어 이를 사용자에게 보여줍니다.

전통적으로 각 단계를 처리하는 코드를 개별 파일로 Django 웹 어플리케이션을 그룹화합니다.

![](Pics/basicDjango.png)

-	**URLs**: 단일 URL의 모든 요청을 하나의 함수로 처리할 수 있지만, 각 리소스를 처리하기 위해 별도의 뷰(view) 함수을 작성하면 유지 관리가 보다 용이합니다. URL 기반의 HTTP 요청을 적절한 뷰로 전달하기 위하여 URL 맵퍼를 사용합니다. 또한 URL 매퍼는 URL에 나타나는 특정 패턴의 문자열 또는 숫자를 비교하여 일치하면 이를 데이터로 뷰 함수에 전달할 수도 있습니다.

-	**Views**: 뷰(view)는 HTTP 요청을 수신하여 HTTP 응답으로 반환하는 요청 처리 함수입니다. 뷰는 요청을 만족시키기 위하여 모델을 통해 데이터에 접근하고 응답을 위한 양식 처리를 템플리트에 위임합니다.

-	**Models**: 모델은 어플리케이션의 데이터 구조를 정의하고 데이터베이스의 레코드를 관리(추가, 수정 및 삭제)하고 검색하는 메커니즘을 제공하는 Python 객체입니다.

-	**Templates**: 템플리트는 텍스트 파일로 실제 내용을 보여 주는 양식을 정의하는 파일의 구조 또는 (HTML 페이지와 같은) 레이아웃입니다. 뷰는 모델의 데이터를 HTML 템플리트에 채워 동적으로 HTML 페이지를 작성합니다. 템플리트을 사용하여 모든 형태의 파일 구조를 정의 할 수 있습니다. 또한 그것이 꼭 HTML일 필요는 없습니다.

> **Note**: Django는 이를 "Model View Template(MVT)" 아키텍처라고 부릅니다. 이미 잘 알려진 [Model View Controller](https://developer.mozilla.org/en-US/docs/Web/Apps/Fundamentals/Modern_web_app_architecture/MVC_architecture) 아키텍처와 많이 유사합니다.

아래 섹션을 읽으면 Django 앱의 핵심 구성 요소들에 대한 아이디어를 줄 것입니다 (개발 환경 설정에서 자세히 설명할 것입니다).

#### 요청을 뷰에 정확하게 전달 (urls.py)

URL 매퍼는 일반적으로 <b>urls.py</b>라는 파일에 저장됩니다. 아래 예제에서 매퍼 (<code>urlpatterns</code>)는 <i>경로</i>(특정 URL <i>패턴</i>)과 해당 뷰 함수간의 매핑 목록을 정의합니다. 지정된 패턴과 일치하는 URL의 HTTP 요청이 수신되면 지정된 뷰 함수를 호출하여 요청을 전달합니다.

```Python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('book/<int:id>/', views.book_detail, name='book_detail'),
    path('catalog/', include('catalog.urls')),
    re_path(r'^([0-9]+)/$', views.best),
]
```

<code>urlpatterns</code> 객체는 <code>path()</code> 또는 <code>re_path()</code> 함수의 목록입니다 (대괄호를 사용하여 Python 리스트로 정의하고, 쉼표로 요소를 구분하며, <code>[item1, item2, item3, ]</code>와 같이 [선택적으로 끝에 쉼표](https://docs.python.org/3/faq/design.html#why-does-python-allow-commas-at-the-end-of-lists-and-tuples)를 사용할 수 있습니다.

두 메소드의 첫 번째 인수는 일치 패턴입니다. <code>path()</code> 메서드는 지정된 이름 인수로 뷰 함수에 전달할 수 있도록 URL의 일부를 정의하며 이를 위하여 소괄호를 사용합니다. <code>re_path()</code> 함수는 정규식이라고 하는 유연한 패턴 매칭 방식을 사용합니다. 나중에 이것에 대해 설명하겠습니다.

두 번째 인수는 패턴이 일치할 때 호출될 함수입니다. <code>views.book_detail</code>이라는 표기법은 이 함수가 <code>views</code>(즉, <code>views.py</code>라는 파일)라는 모듈에 있는 <code>book_detail()</code>함수를 나타냅니다.

#### 요청 처리 (views.py)

뷰는 웹 어플리케이션의 핵심이며, 웹 클라이언트에서 HTTP 요청을 수신하고 HTTP 응답을 반환합니다. 그 사이에 데이터베이스 접근, 템플리트 렌더링 등을 위하여 프레임워크의 다른 자원들을 동원합니다.

아래 예제는 이전 섹션에서 설명한 URL 매퍼가 호출하는 뷰 함수 <code>index()</code>를 보여줍니다. 모든 뷰 함수는 <code>HttpRequest</code> 객체를 매개변수(<code>request</code>)로 받아 <code>HttpResponse</code> 객체를 반환합니다. 아래 예의 경우는 요청에 아무 작업을 않고 응답으로 단순히 주어진 문자열을 반환합니다. 더 복잡한 요청에 대한 처리는 이후 섹션에서 설명할 예정입니다.

```python
# filename: views.py (Django view functions)

from django.http import HttpResponse

def index(request):
    # Get an HttpRequest - the request parameter
    # perform operations using information from the request.
    # Return HttpResponse
    return HttpResponse('Hello from Django!')
```

> **Note**: Python 약간
>
> -	[파이썬 모듈](https://docs.python.org/3/tutorial/modules.html)은 별도의 파일에 저장되어 있는 함수들의 "라이브러리"로 다른 모듈에서 호출할 수 있습니다. 여기에서는 <code>django.http</code> 모듈에서 정의한 <code>HttpResponse</code> 객체를 import하여(<code>from django.http import HttpResponse</code>) 뷰에서 사용합니다. 모듈에서 일부 또는 모든 객체를 import하는 다른 방법도 있습니다.
>
> -	위에서 처럼 <code>def</code> 키워드를 사용하여 함수를 선언하며 함수의 이름 뒤 괄호 안에 이름으로 매개변수를 나열합니다. 그리고 콜론으로 끝냅니다. 다음 줄에서 시작된 <b>들여쓰기</b>에 유의하십시오. 들여쓰기는 코드문이 특정 블록 안에 있음을 나타내기 때문에 중요합니다 (이는 필수이며 들여쓰기는 Python 구문의 핵심입니다. 이 때문에 Python 코드가 읽기 쉬운 것입니다).
>

뷰는 일반적으로 <b>views.py</b>라는 파일에 저장됩니다.

#### 데이터 모델 정의 (models.py)

Django 웹 어플리케이션은 모델에 정의된 Python 객체를 통해 데이터를 관리하고 검색합니다. 모델은 필드 타입과 최대 크기, 기본 값, 선택 목록 옵션, 문서의 도움말 텍스트, 양식의 레이블 텍스트 등을 저장하는 데이터 구조를 정의합니다. 사용하는 데이터베이스의 스키마와 독립적으로 모델을 정의할 수 있습니다. 프로젝트 설정의 일부로 선택할 수 있습니다. 어떤 데이터베이스를 사용하기로 결정했다 하더라도 데이터베이스에 직접 연결할 필요는 없습니다. 개발자는 모델 구조와 데이터를 처리하는 코드를 작성하고, Django는 데이터베이스와 통신하는 모든 귀찮은 작업을 처리합니다.

아래 코드 스니펫은 <code>Team</code> 객체에 대한 매우 간단한 Django 모델을 보여줍니다. django 클래스 <code>models.Model</code>로부터 상속받아 <code>Team</code> 클래스를 정의합니다. Team 클래스는 <code>team_name</code>과 <code>team_level</code>을 문자 필드로 정의하고, 각 필드에 저장할 문자열의 최대 길이를 지정합니다. <code>team_level</code>는 여러 값 중 하나이므로 선택 필드로 정의하고 표시할 선택 항목과 저장할 데이터간의 매핑을 디폴트 값과 함께 제공합니다.

```python
# filename: models.py

from django.db import models

class Team(models.Model):
    team_name = models.CharField(max_length=40)

    TEAM_LEVELS = (
        ('U09', 'Under 09s'),
        ('U10', 'Under 10s'),
        ('U11', 'Under 11s'),
        ...  #list other team levels
    )
    team_level = models.CharField(max_length=3, choices=TEAM_LEVELS, default='U11')
```

> **Note**: Python에 관하여
>
> -	Python은 "객체지향 프로그래밍"을 지원합니다. 이는 객체로 코드를 구성하는 프로그래밍 스타일입니다. 객체는 객체에 관련 데이터와 해당 데이터를 조작하는 함수를 포함합니다. 객체는 다른 객체로 부터 상속, 확장 및 파생할 수 있으므로 관련 객체들은 함수로 정의된 공통적인 동작(메소드))을 공유할 수 있습니다. Python에서는 키워드 "<b>class</b>"를 사용하여 객체에 대한 "설계도(blueprint)"를 정의합니다. 동일 객체 타입의 여러 인스턴스를 클래스 모델을 기반으로 생성할 수 있습니다.
>
> -	예를 들어 여기에서는 <code>Team</code> 클래스는 <code>Model</code> 클래스에서 파생된되었습니다. Team 클래스는 모델이고, Model의 모든 메소드를 포함하지만, Team 클래스 자신만의 기능으로 메소드를 정의할 수도 있습니다. 모델에서는 데이터베이스가 데이터를 저장하는 데 필요한 필드를 정의하고 이름을 지정합니다. Django는 필드 이름을 포함하는 정의를 사용하여 사용할 데이터베이스를 생성합니다.
>

#### 데이터 검색 (views.py)

Django 모델은 데이터베이스 검색을 위한 간단한 쿼리 API를 제공합니다. 조건(예: 일치, 대소 문자를 구별하지 않음, 크다 등)을 사용하여 한 번에 여러 필드를 비교할 수 있으며 복잡한 명령문을 지원할 수 있습니다 (예: "Fr"로 시작하거나 "al"로 끝나는 이름을 갖는 팀을 U11 팀 중에서 검색할 수 있습니다).

아래 코드 스니펫은 모든 U09 팀을 출력하는 뷰 함수(리소스 처리기)를 보여줍니다. <code>list*teams = Team.objects.filter(team*level\_*exact="U09")</code> 문은 모델 쿼리 API를 사용하여 <code>team\_level</code> 필드의 텍스트가 정확히 'U09'와 일치하는 모든 레코드를 필터링하는 방법을 보입니다 (<code>filter()</code> 함수에 필드 이름과 두개 밑줄(<b>\_\_</b>)을 사용하여 구별되는 일치 타입 <b>team\_level*\_exact</b>을 기준으로 합니다).

```python
## filename: views.py

from django.shortcuts import render
from .models import Team

def index(request):
    list_teams = Team.objects.filter(team_level__exact="U09")
    context = {'youngest_teams': list_teams}
    return render(request, '/best/index.html', context)
```

이 함수는 <code>render()</code> 함수를 사용하여 브라우저로 반환되는 <code>HttpResponse</code>를 생성합니다. 이 함수는 <i>지름 길</i>입니다. 지정된 HTML 템플리트와 템플리트를 채울 일부 데이터("<code>context</code>"라는 변수에 있음)와 결합하여 HTML 파일을 작성합니다. 다음 섹션에서는 HTML을 만들기 위해 템플리트에 데이터를 채우는 방법을 설명합니다.

#### 데이터 랜더링 (HTML 템플리트)

템플리트 시스템을 사용하면 페이지 생성시 채울 데이터에 대한 자리 표시자를 사용하여 출력 문서의 구조를 정의할 수 있습니다. HTML 작성에 종종 템플리트를 사용하지만 다른 종류의 문서를 작성할 수도 있습니다. Django는 자체 템플리트 시스템과 [Jinja2](http://jinja.pocoo.org/)라는 보편적인 Python 라이브러리를 모두 지원합니다 (필요하면 다른 시스템을 지원하도록 만들 수도 있습니다).

아래 코드 스니펫은 이전 섹션의 <code>render()</code> 함수가 호출한 HTML 템플리트를 어떻게 적용할 수 있는지 보여줍니다. 이 템플리트를 렌더링할 때 <code>youngest_teams</code>라는 리스트 변수(위의 <code>render()</code> 함수 내부의 <code>context</code> 변수에 포함되어 있음)를 접근할 것이라 가정하고 작성되었습니다. <code>youngest_teams</code> 변수를 먼저 확인하고 HTML 양식 안에서 <code>for</code> 루프로 식(expression)을 반복합니다. 매 반복에서 템플리트는 <code>\<li\></code> 요소에 있는 각 팀의 <code>team_name</code> 값을 출력합니다.

```HTML
## filename: best/templates/best/index.html

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Home page</title>
</head>
<body>
  {% if youngest_teams %}
    <ul>
      {% for team in youngest_teams %}
        <li>{{ team.team_name }}</li>
      {% endfor %}
    </ul>
  {% else %}
    <p>No teams are available.</p>
  {% endif %}
</body>
</html>
```

---

### 기타 기능

이전 섹션에서 거의 모든 웹 어플리케이션에서 사용할 URL 매핑, 뷰, 모델, 템플리트 등 주요 기능을 설명하였습니다. Django가 제공하는 몇 가지 다른 기능들은 다음과 같습니다.

-	**양식**: 서버에서 처리할 사용자 입력 데이터를 위하여 HTML 양식을 사용합니다. Django는 단순하게 양식 생성, 유효성 검사와 이를 처리합니다.
-	**사용자 인증 및 권한관리**: Django에는 보안을 염두에 두고 구축된 강력한 사용자 인증 및 권한관리 시스템을 보유하고 있습니다.
-	**캐싱**: 콘텐츠를 동적으로 생성하는 것은 정적 콘텐츠를 제공하는 것보다 훨씬 계산 집약적이며 느립니다. Django는 렌더링한 페이지의 전부 또는 일부를 저장할 수 있도록 유연한 캐싱 기능을 제공하여 필요한 경우를 제외하고는 다시 렌더링하지 않도록 합니다.
-	**관리 사이트**: Django 관리 사이트는 기본 골조를 사용하여 앱을 개발할 때 기본적으로 포함됩니다. 사이트 관리자가 쉽게 사이트의 모든 데이터 모델을 작성, 편집 및 출력할 수 있는 관리 페이지를 제공합니다.
-	**데이터 순서화 (serializing)**: Django는 XML 또는 JSON으로 데이터를 순서화하여 제공하기 용이힙니다. 이 기능은 웹 서비스(다른 어플리케이션이나 사이트에서 사용하는 데이터를 순수하게 제공만하고 아무 것도 표시하지 않는 웹 사이트)를 만들 때나 클라이언트사이드 코드가 모든 데이터 렌더링을 처리하는 웹 사이트를 개발할 때 유용할 수 있습니다.

---

### 요약

수고하셨습니다. Django 여행의 준비를 완료하셨습니다! 이제 Django의 주요 장점과 역사에 대해 조금 이해하였고, Django 어플리케이션의 주요 부분들을 대략 설명하였습니다. 또한 리스트, 함수 및 클래스 구문을 비롯하여 Python 프로그래밍 언어에 대한 몇 가지 사항을 다루었습니다.

위에서 실제 Django 코드를 이미 보았듯이 클라이언트사이드 코드와 달리 실행하기 위해서는 개발 환경을 설정해야 합니다. 이것이 바로 다음 단계입니다.

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
-	[Django 튜토리얼 10부: Django 웹 어플리케이션 테스팅](testing.md)
-	[Django 튜토리얼 11부: 프로덕션으로 Django 전개](deployment.md)
-	[Django 웹 어플리케이션 보안](webApplicationSecurity.md)
