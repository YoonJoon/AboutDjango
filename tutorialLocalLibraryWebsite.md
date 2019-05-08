Django 튜토리얼: The Local Library website
------------------------------------------

---

이 문서는 저작자 동의없이 KAIST 대학정보화사업팀을 위하여 [Django Tutorial: The Local Library website](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Tutorial_local_library_website)를 번역 편집하여 작성한 것입니다.

실용적인 튜토리얼 시리즈의 첫 번째 단계인 이 단계에서 배울 것을 설명하고 이후 단계에서 다루는 'local library' 예제 웹 사이트의 개요를 설명합니다.

> 선수지식: [Django 소개](introduction.md)를 읽었어야 합니다. 다음 단계로 [Django 개발 환경을 설정](developmentEnvironment.md)해야 합니다.
>
> 목표: 튜토리얼에서 사용하는 예제 응용프로그램을 소개하고, 독자가 다루는 항목을 이해할 수 있도록 합니다.

---

### 개요

MDN "Local Library" Django 튜토리얼에 오신 것을 환영합니다. 이 튜토리얼에서는 동네 도서관에서 카탈로그 관리를 위하여 사용할 수 있는 웹 사이트를 개발합니다.

일련의 튜토리얼 각 단계에서는 다음을 수행합니다.

-	Django의 도구를 사용하여 골조 웹 사이트와 응용프로그램을 만듭니다.
-	개발 서버의 실행을 시작하고 중지합니다.
-	응용프로그램의 데이터를 나타내는 모델을 만듭니다.
-	Django 관리 사이트를 사용하여 사이트의 데이터를 삽입합니다.
-	다양한 요청에 대한 응답을 위하여 요청된 데이터를 검색하는 view와 HTML로 데이터를 렌더링하여 브라우저에 보여주는 템플리트를 작성합니다.
-	다양한 URL 패턴과 이를 view에 연결하는 매퍼를 만듭니다.
-	사용자 권한 및 세션을 추가하여 사이트 동작 및 접근을 제어합니다.
-	양식 작업.
-	앱의 테스트 코드를 작성합니다.
-	Django의 보안을 효과적으로 사용합니다.
-	응용프로그램을 프로덕션 환경으로 배포합니다.

---

### The Local Library website

이미 일부 내용은 다루었고 다른 것들은 간단히 언급했습니다. 튜토리얼을 마치면 간단한 Django 앱을 직접 개발할 수 있을 것입니다.

<i>LocalLibrary</i>는 튜토리얼을 통해 작성하고 발전시킬 웹 사이트의 이름입니다. 예상하듯이, 웹 사이트의 목적은 소규모 지역 도서관에 대한 온라인 카탈로그를 제공하는 것입니다. 그럼으로써 사용자는 소장된 서적을 검색하고 계정을 관리할 수 있습니다.

원하는 만큼 자세하게 또는 간단하게 보여줄 수 있고, 거의 모든 Django 기능을 보여줄 수 있는 예제를 신중하게 선택하였습니다. 더욱 중요한 것은 Django 웹 프레임워크의 중요한 기능에 대한 <i>안내 역할</i>을 할 수 있는 것입니다.

-	튜토리얼의 처음 몇 단계에서는 라이브러리 회원이 소장하고 있는 책을 찾기 위해 사용할 수있는 간단한 찾아보기 전용 사이트를 정의합니다. 이를 통해 거의 모든 웹 사이트의 공통 작업인 데이터베이스의 내용을 읽고 표시 할 수 있습니다.
-	진행하면서 라이브러리 예제를 자연스럽게 확장하여 고급 Django 기능을 보여줍니다. 예를 들어 사용자가 책을 예약 할 수 있도록 사이트를 확장하고, 이를 사용하여 양식 사용과 사용자 인증 지원 방법을 보여줍니다.

확장 가능한 예이지만 Django를 신속하게 시작하고 실행할 수있는 최소한의 정보를 보여주기 위한 것이므로 <b>Local</b>Library라고 부릅니다. 결과적으로 책, 본수, 저자 및 기타 책에 대한 주요 정보를 저장합니다. 그러나 도서관이 저장할 수 있는 다른 품목에 대한 정보를 저장하거나 여러 도서관 사이트 또는 "대형 도서관"의 기능을 지원하는 데 필요한 인프라는 제공하지 않습니다.

---

### 막막합니다, 소스 코드를 어디서 구할 수 있나요?

튜토리얼를 진행하면서 각 지점에서 복사하여 붙여 넣을 수있는 적절한 코드 스니펫을 제공하며, (지침이 있는 경우) 스스로 확장시킬 수 있는 다른 코드도 있습니다. 막히면 [Github](https://github.com/mdn/django-locallibrary-tutorial)에서 개발된 버전의 웹 사이트를 참고할 수도 있습니다.

---

### 요약

이제 LocalLibrary 웹 사이트와 배울 내용에 대해 조금 더 알게 되었으므로 예제를 구성하는 [골조를 만드는 프로젝트](skeletonWebsite.md)로 갈 차례입니다.

---

### 이 단원에서 아래 단계를 다룹니다.

-	[Django 소개](introduction.md)
-	[Django 개발 환경 설정](developmentEnvironment.md)
-	[Django 튜토리얼: The Local Library website](tutorialLocalLibraryWebsite.md)
-	[Django 튜토리얼 2부: 웹사이트 골조 만들기](skeletonWebsite.md)
-	[Django 튜토리얼 3부: 모델](models.md)
-	[Django 튜토리얼 4부: Django 관리 사이트](adminSite.md)
-	[Django 튜토리얼 5부: 홈 페이지 안들기](homePage.md)
-	[Django 튜토리얼 6부: 일반 목록과 상세 보기](genericViews.md)
-	[Django 튜토리얼 7부: 세션 프레임워크](sessions.md)
-	[Django 튜토리얼 8부: 사용자 인증 및 사용 권한](authentication.md)
-	[Django 튜토리얼 9부: 양식 작업](forms.md)
-	[Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅](testing.md)
-	[Django 튜토리얼 11부: 운영으로 Django 전개](deployment.md)
-	[Django 웹 응용프로그램 보안](webApplicationSecurity.md)
