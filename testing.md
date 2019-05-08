Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅
---------------------------------------------------

이 문서는 저작자 동의없이 KAIST 대학정보화사업팀을 위하여 [Django Tutorial Part 10: Testing a Django web application](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Testing)를 번역 편집하여 작성한 것입니다.

웹사이트가 성장함에 따라 테스트를 수동으로 하는 것이 어려워집니다. 테스트할 부분이 많을뿐만 아니라 구성 요소간의 상호 작용이 더욱 복잡해지면서 한 영역의 작은 변경이 다른 영역에 영향을 미칠 수 있으므로 변경 사항이 추가될 때마다 모든 것이 계속 작동하고 오류가 발생하지 않도록 변경해야 합니다 . 이러한 문제를 완화하는 한 방법은 자동화된 테스트를 작성하는 것입니다. 자동화된 테스트는 변경 작업을 수행할 때마다 쉽고 믿을 수 있게 실행할 수 있습니다. 이 장에서는 Django의 테스트 프레임워크를 사용하여 웹사이트의 단위 테스트를 자동화하는 방법을 설명합니다.

> 선수지식: [Django 튜토리얼 9부: 양식 작업](forms.md)까지 모든 주제를 숙지하여야 합니다.
>
> 목표: 사용자로부터 정보를 얻고 데이터베이스를 업데이트할 수 있도록 양식을 작성하는 방법을 이해합니다. 제네릭 클래스 기반 양식 편집 뷰를 사용하여 단일 모델에 대한 작업 양식 작성을 크게 단순화시키는 방법을 이해합니다.

---

### 개요

[Local Library](tutorialLocalLibraryWebsite.md)은 현재 모든 도서와 저자의 목록을 표시하는 페이지, `Book`과 `Author` 항목의 상세 보기, `BookInstances`를 갱신하는 페이지, `Author` 항목(그리고 [앞 장](forms.md)의 도전과제를 수행하였다면 `Books` 레코드)을 작성, 변경과 삭제하는 페이지로 이루어져 있습니다. 이 사이트가 비교적 작지만 각 페이지로 이동하며 모든 것이 예상대로 작동하는 지를 피상적으로라도 수동으로 확인하는 데 몇 분이 걸릴 수 있습니다. 사이트가 성장함에 따라 사이트를 변경하고 모든 것이 "적절하게" 작동하는 지를 수동으로 확인하는 데 필요한 시간은 점점 길어집니다. 현재 상태를 유지하려고 해도 개발자는 대부분의 시간을 테스트에 보내고 코드 개선에 보낼 시간은 거의 없을 것입니다.

자동화된 테스트는 이 문제를 도와줄 수 있습니다. 명백한 장점은 수작업 테스트보다 훨씬 빠르게 실행할 수 있으며 훨씬 더 세부적인 수준으로 테스트할 수 있으며 매번 똑같은 기능을 정확하게 테스트할 수 있다는 것입니다 (테스트 인력은 신뢰할 만하지 않습니다). 신속하기 때문에 자동화된 테스트를 보다 규칙적으로 실행할 수 있으며, 테스트가 실패하면 코드가 예상대로 수행하지 않는 부분을 가리킵니다.

또한 자동회된 테스트는 코드의 실제 "사용자"로서의 역할을 할 수 있으므로 웹사이트의 작동 방식을 정의하고 문서화하는 작업을 엄격하게 수행해야 합니다. 이들은 종종 코드 예제와 기초 문서가 될 수 있습니다. 그리하여 일부 소프트웨어 개발 프로세스는 테스트 정의와 구현으로 시작하고 다음 필요한 작동(예: [테스트 기반](https://en.wikipedia.org/wiki/Test-driven_development)과 [동작 기반](https://en.wikipedia.org/wiki/Behavior-driven_development) 개발)에 맞추어 코드를 작성합니다.

이 장에서는 *LocalLibrary* 웹사이트에 여러 가지 테스트를 추가하여 Django를 위한 자동화된 테스트를 작성하는 방법을 보입니다.

#### 테스트 유형

테스트와 테스트 방법에는 다양한 유형, 수준 및 분류가 있습니다. 가장 중요한 자동화 테스트는 다음과 같습니다.

##### 단위 테스트 (Unit tests)

개별 구성 요소에 대한 기능 작동을 클래스 및 기능 수준에 따라 확인합니다.

##### Regression tests

버그 이력을 재현하는 테스트이며 각 테스트는 처음에 버그가 수정되었는 지 확인하기 위해 실행된 다음 나중에 다시 코드를 변경하면 동일 버그가 발생되지 않도록 다시 실행합니다.

##### 통합 테스트 (Integration tests)

함께 수행될 때 구성 요소의 그룹핑을 검증합니다. 통합 테스트는 구성 요소 간의 요구한 상호 작용을 인식하지만 각 구성 요소의 내부 작동이 반드시 필요한 것은 아닙니다. 그러므로 전체 웹사이트를 통해 구성 요소의 간단한 그룹핑을 커버할 수 있습니다.

> **Note**: 다른 일반적인 유형의 테스트는 블랙 박스, 화이트 박스, 수동, 자동화, 카나리아, 연기, 적합성, 수용, 기능, 시스템, 성능, 부하 및 스트레스 테스트를 포함합니다. 더 많은 정보를 찾아 보십시오.

#### 테스트를 위한 Django 기능

웹사이트의 테스트는 복잡한 작업입니다. HTTP 수준의 요청 처리에서 부터, 모델에 대한 쿼리, 양식에 대한 유효성 검사 및 처리와 템플릿 렌더링까지 여러 논리 계층으로 이루어지기 때문입니다.

Django는 Python 표준 [unittest](https://docs.python.org/3/library/unittest.html#module-unittest) 라이브러리를 기반으로 작은 클래스 계층 구조로 테스트 프레임 워크를 제공합니다. 이름에도 불구하고 테스트 프레임워크는 단위 테스트와 통합 테스트에 모두 적합합니다. Django 프레임워크에 테스트 웹과 Django 특정 동작을 테스트하는 데 도움을 주는 API 메소드와 도구를 추가합니다. 이를 통해 요청을 시뮬레이션하고 테스트 데이터를 삽입하며 응용 프로그램의 출력을 검사할 수 있습니다. 또한 Django는 [다른 테스트 프레임워크를 사용하기](https://docs.djangoproject.com/en/2.1/topics/testing/advanced/#other-testing-frameworks) 위한 API([LiveServerTestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#liveservertestcase))와 도구를 제공합니다. 예를 들어 잘 알려진 [Selenium](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment) 프레임워크와 통합하여 라이브 브라우저와 상호 작용하는 사용자를 시뮬레이트할 수 있습니다.

테스트를 작성하려면 Django (또는 *unittest*) 테스트 기본 클래스 ([SimpleTestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#simpletestcase), [TransactionTestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#transactiontestcase), [TestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#testcase), [LiveServerTestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#liveservertestcase))로 부터 파생시켜 클래스를 생성하고, 특정 기능이 예상대로 작동하는지 확인하는 별도의 메소드를 작성하십시오 (테스트에서는 표현식(exrpession)의 결과가 `True` 또는 `False` 값을 갖거나 두 값이 같은 경우 등을 테스트하기 위하여 "assert" 메소드를 사용합니다). 테스트 수행을 시작할 때 프레임워크는 파생 클래스에서 선택한 테스트 메소드를 실행합니다. 테스트 메소드는 아래 그림과 같이 클래스에서 정의된 일반적인 설정과 사후 동작을 함께 또는 개별적으로 각각 독립적인 환경에서 실행합니다.

```python
class YourTestClass(TestCase):
    def setUp(self):
        # Setup run before every test method.
        pass

    def tearDown(self):
        # Clean up run after every test method.
        pass

    def test_something_that_will_pass(self):
        self.assertFalse(False)

    def test_something_that_will_fail(self):
        self.assertTrue(False)
```

대부분의 테스트에 가장 적합한 기본 클래스는 [django.test.TestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#testcase)입니다. 이 테스트 클래스는 테스트가 실행되기 전 깨끗한 데이터베이스를 만들고 자체 트랜잭션에서 모든 테스트 함수를 실행합니다. 또한 클래스는 뷰 수준에서 코드와 상호 작용하는 사용자를 시뮬레이트할 수 있는 테스트 [Client](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#django.test.Client)를 갖고 있습니다. 다음 절에서는 이 [TestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#testcase) 기본 클래스를 사용하여 생성된 단위 테스트에 대해 집중적으로 다룰 것입니다.

> **Note**: [django.test.TestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#testcase) 클래스는 매우 편리하지만 일부 테스트가 필요할 때보다 느려질 수 있습니다 (모든 테스트가 자체 데이터베이스를 설정하거나 뷰 상호 작용을 시뮬레이션해야 하는 것은 아닙니다). 이 클래스로 할 수 있는 것에 익숙해지면 일부 시험을 보다 간단한 테스트 클래스로 대체할 수 있습니다.

#### 무엇을 테스트?

Python이나 Django의 일부로 제공되는 라이브러리나 기능이 아닌 개발한 코드 자체 모든 양상을 테스트해야 합니다.

예를 들어 아래 정의된 `Author` 모델을 고려한다면 Django가 정의한 것이므로 `first_name`과 `last_name`은 `CharField`로 데이터베이스에 적절히 저장되었음(실제로 개발 중에는 필연적으로 이 기능을 테스트할 지라도)을 명시적으로 테스트할 필요는 없습니다. 또한 `date_of_birth`는 Django에서 구현한 것이기 때문에 date 필드의 검증을 테스트할 필요가 없습니다.

그러나 레이블(*First name*, *Last name*, *Date of birth*, *Died*)에 사용되는 텍스트와 텍스트에 할당된 필드의 크기(*100 chars*)는 확인해야 합니다. 이들은 디자인의 일부분이므로 향후 깨지거나 바뀔 수 있기 때문입니다.

```python
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return '%s, %s' % (self.last_name, self.first_name)
```

마찬가지로 사용자 정의 메소드 <code>get\_absolute_url()</code>과 `__str__()`</code>은 코드 또는 비즈니스 로직이므로 요구 사항에 따라 작동하는 지 확인해야 합니다. <code>get\_absolute_url()</code>의 경우 Django `reverse()` 메서드가 적절히 구현되었다는 것을 믿을 수 있습니다. 따라서 테스트할 대상은 연결된 뷰를 실제로 정의한 것입니다.

> **Note**: 독창적인 독자는 출생과 사망의 날짜를 합리적인 값으로 제한하고 출생 후 사망 여부를 확인하기를 원할 수도 있습니다. Django에서 이러한 제약 조건을 양식 클래스에 추가할 수 있습니다 (필드 수준 유효성 검사기로 정의할 수 있을 지라도 모델 수준이 아닌 양식 수준에서만 사용되는 것처럼 보입니다).

이를 염두에 두고 테스트를 정의하고 실행하는 방법을 살펴 보겠습니다.

---

### 테스트 구조 개요

"무엇을 테스트할 것인가"의 세부 사항에 들어가기 전에 먼저 테스트를 정의하는 위치와 방법을 간단히 살펴 보겠습니다.

Django는 unittest 모듈의 [built-in test discovery](https://docs.python.org/3/library/unittest.html#unittest-test-discovery)을 사용하여 <b>test*.py</b> 패턴으로 명명 된 파일을 현재 작업 디렉토리 내에서 테스트를 검색합니다. 파일의 이름을 적당히 지정하려면 개발자가 원하는 구조를 사용할 수 있습니다. 테스트 코드에 대한 모듈을 만들고 모델, 뷰, 양식과 테스트해야 하는 다른 유형의 코드에 대해 별도의 파일을 만드는 것이 좋습니다.

```
catalog/
  /tests/
    __init__.py
    test_models.py
    test_forms.py
    test_views.py
```

*LocalLibrary* 프로젝트에 위와 같이 파일 구조를 만듭니다. **\_\_init\_\_.py**는 빈 파일이어야 합니다 (이것은 디렉토리가 패키지라는 것을 파이썬에게 알려줍니다). 골조 테스트 파일 <b>/catalog/tests.py</b>를 복사하고 이름을 바꾸어 세 개의 테스트 파일을 만들 수 있습니다.

> **Note**: 골조 테스트 파일 <b>/catalog/tests.py</b>은 [웹사이트 골조 만들기](skeletonWebsite.md)에서 자동으로 만들어졌습니다. 그 안에 모든 테스트를 넣는 것은 "합법적"이지만, 적절히 테스트하기에는 매우 빠르게 커져 관리하기 어려운 테스트 파일이 됩니다.
>
> 필요하지 않으므로 골조 파일을 삭제하십시오.

<b>/catalog/tests/test_models.py</b>를 엽니다. 아래과 같이 파일에 <code>django.test.TestCase</code>를 임포트합니다.

```python
from django.test import TestCase

# Create your tests here.
```

종종 특정 기능을 테스트하기 위한 개별 메소드와 함께 테스트할 각 모델/뷰/ 양식에 대한 테스트 클래스를 추가합니다. 다른 경우에는 특정 유스 케이스를 테스트하기 위한 별도의 클래스를 사용하고 그 유스 케이스의 양상을 테스트하는 개별 테스트 함수를 사용할 수도 있습니다 (예: 각각의 가능한 실패 사례를 테스트할 함수와 모델 필드가 적절히 유효한지 테스트하는 클래스). 다시 말하지만 구조는 개발자에게 달려 있습니다. 그러나 일관성이 있다면 가장 좋습니다.

아래 테스트 클래스를 파일의 끝에 추가하십시오. 이 클래스은 TestCase에서 파생하여 테스트 케이스 클래스를 구성하는 방법을 보입니다.

```python
class YourTestClass(TestCase):
    @classmethod
    def setUpTestData(cls):
        print("setUpTestData: Run once to set up non-modified data for all class methods.")
        pass

    def setUp(self):
        print("setUp: Run once for every test method to setup clean data.")
        pass

    def test_false_is_false(self):
        print("Method: test_false_is_false.")
        self.assertFalse(False)

    def test_false_is_true(self):
        print("Method: test_false_is_true.")
        self.assertTrue(False)

    def test_one_plus_one_equals_two(self):
        print("Method: test_one_plus_one_equals_two.")
        self.assertEqual(1 + 1, 2)
```

새 클래스는 사전 테스트 구성(예 : 테스트에 필요한 모델이나 다른 객체를 만드는 경우)에 사용할 수 있는 두 메서드를 정의합니다.

-	`setUpTestData()`는 클래스 수준 설정을 위해 테스트를 시작할 때 한 번 호출됩니다. 이 방법을 사용하여 테스트 메소드에서 수정하거나 변경하지 않을 객체를 만들 수 있습니다.
-	`setUp()`은 모든 테스트 함수 앞에 호출되어 테스트가 수정할 수 있는 객체를 설정합니다. 모든 테스트 함수는 이러한 객체의 "최신" 버전을 가져옵니다.

테스트 클래스에는 아직 사용하지 않은 `tearDown()` 메소드도 있습니다. 이 메소드는 데이터베이스 자체 테스트에는 특히 유용하지 않지만 `TestCase` 기본 클래스에서 데이터베이스의 사후 처리를 합니다.

아래에는 `Assert` 함수를 사용하여 조건이 true, false 또는 equal인지 테스트하는 여러 테스트 메서드가 있습니다 (`AssertTrue`, `AssertFalse`, `AssertEqual`). 조건 실행의 결과가 예상과 다르다면 테스트는 실패이고 콘솔에 오류를 보고합니다.

`AssertTrue`, `AssertFalse`, `AssertEqual`은 **unittest**에서 제공하는 표준 주장(assertion)입니다. 프레임워크에는 다른 표준 주장이 있고, 뷰가 리디렉션되는지 (`assertRedirects`), 특정 템플릿이 사용되었는지 (`assertTemplateUsed`) 등을 테스트하기 위한 [Django-specific assertions](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#assertions)도 있습니다.

일반적으로 위와 같이 테스트에 **print()** 함수를 포함하면 **안됩니다**. 여기서는 (다음 절에서) 설정 기능을 콘솔에서 호출하여 순서를 볼 수 있도록하기 위해 여기에서만 사용합니다.

---

### 테스트 수행 방법

아래의 명령은 모든 테스트를 실행하는 가장 쉬운 방법입니다.

```bash
python3 manage.py test
```

현재 디렉토리 아래에 <b>test\*.py</b> 패턴으로 명명된 모든 파일을 발견하고 적절한 기본 클래스를 사용하여 정의된 모든 테스트(여기에는 많은 테스트 파일이 있지만 <b>/catalog/tests/test_models.py</b>가 현재 포함하고 있는 테스트만)를 수행합니다. 기본적으로 테스트는 먼저 테스트 요약을 출력하고 테스트 실패에 대해서만 개별적으로 보고합니다.

> <code>ValueError: Missing staticfiles manifest entry ...</code>와 유사한 오류를 얻었다면, 이는 테스트가 기본적으로 <i>collectstatic</i>을 실행하지 않아 앱이 이를 요구하는 storage 클래스를 사용하기 때문일 수 있습니다 (자세한 내용은 [manifest_strict](https://docs.djangoproject.com/en/2.1/ref/contrib/staticfiles/#django.contrib.staticfiles.storage.ManifestStaticFilesStorage.manifest_strict)를 참조하십시오).여러 가지 방법으로 이 문제를 극복 할 수 있습니다. 가장 쉬운 방법은 테스트를 실행하기 전에 간단히 <i>collectstatic</i>을 실행하는 것입니다.

```bash
python3 manage.py collectstatic
```

<i>LocalLibrary</i>의 루트 디렉토리에서 테스트를 실행하십시오. 아래와 같은 결과가 나타납니다.

```bash
> python3 manage.py test

Creating test database for alias 'default'...
setUpTestData: Run once to set up non-modified data for all class methods.
setUp: Run once for every test method to setup clean data.
Method: test_false_is_false.
setUp: Run once for every test method to setup clean data.
Method: test_false_is_true.
setUp: Run once for every test method to setup clean data.
Method: test_one_plus_one_equals_two.
.
======================================================================
FAIL: test_false_is_true (catalog.tests.tests_models.YourTestClass)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\Github\django_tmp\library_w_t_2\locallibrary\catalog\tests\tests_models.py", line 22, in test_false_is_true
    self.assertTrue(False)
AssertionError: False is not true

----------------------------------------------------------------------
Ran 3 tests in 0.075s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

여기에서 테스트 실패가 하나 있었음을 알 수 있으며, 실패한 함수와 실패한 이유를 정확히 볼 수 있습니다 (`False`가 `True`가 아니기 때문에 예상된 실패입니다).

> **Tip**: 가장 중요한 점은 위의 테스트 결과를 통해 객체와 메소드에 서술적이고 유의미한 이름을 사용하면 훨씬 더 가치가 있다는 것입니다.

위의 **굵게**(?) 표시된 텍스트는 일반적으로 테스트 출력에 나타나지 않습니다 (테스트의 `print()` 함수에 의해 생성됩니다). 이것은 `setUpTestData()` 메소드를 클래스에 대해 한 번 호출하고 각 메소드 앞에서 `setUp()`을 호출하는 방법을 보여줍니다.

다음 절에서는 특정 테스트를 실행하는 방법과 테스트에 표시되는 정보의 양을 제어하는 방법을 보여줍니다.

#### 더 많은 테스트 정보 보기

테스트 수행에 대한 자세한 정보를 얻으려면 <i>verbosity</i>를 변경합니다. 예를 들어 테스트 실패뿐만 아니라 성공(그리고 테스트 데이터베이스 설정 방법에 대한 정보)을 나열하려면 아래과 같이 verbosity를 "2"로 설정합니다.

```bash
python3 manage.py test --verbosity 2
```

허용되는 verbosity 수준은 0, 1, 2과 3이며 기본 값은 "1"입니다.

#### 특정 테스트 수행

패키지, 모듈, `TestCase` 하위 클래스 또는 메소드에 대한 전체 점 경로를 지정하여 테스트의 부분 집합을 실행할 수 있습니다.

```bash
# Run the specified module
python3 manage.py test catalog.tests

# Run the specified module
python3 manage.py test catalog.tests.test_models

# Run the specified class
python3 manage.py test catalog.tests.test_models.YourTestClass

# Run the specified method
python3 manage.py test catalog.tests.test_models.YourTestClass.test_one_plus_one_equals_two
```

---

### LocalLibrary 테스트

이제 테스트를 수행하는 방법과 테스트해야 할 것이 무엇인지 알았습니다. 몇 가지 실용적인 예제를 살펴 보겠습니다.

> <b>Note</b>: 가능한 모든 테스트를 작성하지 않지만 테스트 작동 방법과 더 많은 작업 수행에 필요한 아이디어를 제공할 것입니다.

#### 모델

위에서 논의했듯이 설계 또는 작성한 코드로 정의된 것을 테스트해야 하지만 Django 또는 Python 개발팀에서 이미 테스트 한 라이브러리나 코드는 테스트하지 않습니다.

예를 들어, 아래 `Author` 모델을 고려할 때 모든 필드의 레이블을 테스트해야 합니다. 대부분의 필드를 명시적으로 지정하지 않았지만 설계단계에서 값을 지정해야 하기 때문입니다. 값을 테스트하지 않는다면 필드 레이블이 원하는 값을 가지고 있는지 확인할 수 없습니다. 마찬가지로 Django가 지정된 길이의 필드를 만들 것이라고 믿지만, 계획대로 구현 되었는가 확인을 위해 길이에 대한 테스트를 지정하는 것이 좋습니다.

```python
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return f'{self.last_name}, {self.first_name}'
```

<b>/catalog/tests/test\_models.py</b> 파일을 열고 기존 코드를 `Author` 모델의 다음 테스트 코드로 바꿉니다.

여기서는 `TestCase`를 처음 임포트하여 테스트 클래스 (`AuthorModelTest`)를 파생하고, 서술적 이름을 사용하면 테스트 출력에서 실패한 테스트를 쉽게 찾을 수 있습니다. 다음 `setUpTestData()`를 호출하여 테스트에서 사용하지만 수정하지 않을 저자 객체를 만듭니다.

```python
from django.test import TestCase

from catalog.models import Author

class AuthorModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Set up non-modified objects used by all test methods
        Author.objects.create(first_name='Big', last_name='Bob')

    def test_first_name_label(self):
        author = Author.objects.get(id=1)
        field_label = author._meta.get_field('first_name').verbose_name
        self.assertEquals(field_label, 'first name')

    def test_date_of_death_label(self):
        author=Author.objects.get(id=1)
        field_label = author._meta.get_field('date_of_death').verbose_name
        self.assertEquals(field_label, 'died')

    def test_first_name_max_length(self):
        author = Author.objects.get(id=1)
        max_length = author._meta.get_field('first_name').max_length
        self.assertEquals(max_length, 100)

    def test_object_name_is_last_name_comma_first_name(self):
        author = Author.objects.get(id=1)
        expected_object_name = f'{author.last_name}, {author.first_name}'
        self.assertEquals(expected_object_name, str(author))

    def test_get_absolute_url(self):
        author = Author.objects.get(id=1)
        # This will also fail if the urlconf is not defined.
        self.assertEquals(author.get_absolute_url(), '/catalog/author/1')
```

필드 테스트는 필드 레이블 (verbose_name)의 값과 문자 필드의 크기가 예상한 것과 같은지 확인합니다. 이 메소드는 모두 설명을 포함하는 이름을 가지며 동일한 패턴을 따릅니다.

```python
# Get an author object to test
author = Author.objects.get(id=1)

# Get the metadata for the required field and use it to query the required field data
field_label = author._meta.get_field('first_name').verbose_name

# Compare the value to the expected result
self.assertEquals(field_label, 'first name')
```

아래와 같은 흥미로운 점이 있습니다.

-	<code>author.first_name.verbose_name</code>를 사용하여 <code>verbose_name</code>을 직접 가져올 수 없습니다. <code>author.first_name</code>은 문자열이지만 <code>author.first_name</code>의 속성을 접근하는 데 사용할 수있는 <code>first_name</code> 객체의 핸들은 아닙니다. 대신 필드의 인스턴스를 가져와 저자의 <code>\_meta</code> 속성을 이용하고, 추가 정보를 위한 쿼리에 이용합니다.
-	<code>assertTrue (field_label == 'first name')</code>보다는 <code>assertEquals (field_label, 'first name')</code>를 사용하기로 했습니다. 그 이유는 테스트가 실패하면 후자의 출력은 실제로 발생한 레이블을 알려주므로 문제의 디버깅이 약간 쉬워집니다.

> **Note**: <code>last_name</code>와 <code>date_of_birth</code> 레이블에 대한 테스트와 <code>last_name</code> 필드의 길이에 대한 테스트는 생략합니다. 위에서 제시된 명명 규칙과 접근법에 따라 이제 자신의 버전을 추가하십시오.

또한 커스텀 메소드도 테스트하여야 합니다. 이들은 본질적으로 "Last Name", "First Name" 형식을 사용하여 기대했던 대로 객체 이름을 구성하였는지 그리고 `Author` 항목에서 얻은 URL이 기대한 것과 같은 지를 확인합니다.

```python
def test_object_name_is_last_name_comma_first_name(self):
    author = Author.objects.get(id=1)
    expected_object_name = f'{author.last_name}, {author.first_name}'
    self.assertEquals(expected_object_name, str(author))

def test_get_absolute_url(self):
    author = Author.objects.get(id=1)
    # This will also fail if the urlconf is not defined.
    self.assertEquals(author.get_absolute_url(), '/catalog/author/1')
```

테스트를 실행합니다. 모델 튜토리얼에서 설명한 것처럼 Author 모델을 만든 경우 아래에 표시된 것처럼 <code>date_of_death</code> 레이블에서 오류가 발생할 가능성이 큽니다. Django에서 레이블을 정의할 때 첫 문자를 대문자로 사용하지 않는 규칙을 따르도록 기대했기 때문에 테스트가 실패했습니다 (Django가 이를 수행합니다).

```bash
======================================================================
FAIL: test_date_of_death_label (catalog.tests.test_models.AuthorModelTest)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "D:\...\locallibrary\catalog\tests\test_models.py", line 32, in test_date_of_death_label
    self.assertEquals(field_label,'died')
AssertionError: 'Died' != 'died'
- Died
? ^
+ died
? ^
```

이것은 매우 사소한 버그이지만 스타일 테스트가 규칙을 얼마나 철저히 검사할 수 있는 지를 보여줍니다.

> **Note**: date_of_death 필드의 레이블 (/catalog/models.py)을 "died"로 변경하고 테스트를 다시 실행하십시오.

다른 모델을 테스트하기 위한 패턴도 유사하므로 더 이상 설명하지 않을 것입니다. 다른 모델에 대한 테스트를 쉽게 만들 수 있습니다.

#### 양식

양식을 테스트하는 철학은 모델을 테스트하는 철학과 동일합니다. 코딩한 내용이나 디자인에서 지정한 내용은 테스트해야 하지만 기본 프레임 워크 및 서드 파티 라이브러리의 동작은 테스트하지 않습니다.

일반적으로 양식에 원하는 필드가 있는지 테스트하고 적절한 레이블과 도움말을 디스플레이하는지 확인해야 합니다. Django에서 (사용자 정의 필드와 유효성 검사를 생성하지 않았다면) 필드 유형의 유효성을 올바르게 검사하는지, 즉 이메일 필드에서는 이메일만을 허용하는지 테스트할 필요가 없습니다. 그러나 필드에 대해 예상되는 추가 유효성 검사와 코드에서 오류를 생성할 메시지는 테스트해야 합니다.

책을 갱신하는 양식에는 갱신 날짜에 대한 필드가 하나 있으며 이 필드에는 확인해야 할 레이블과 도움말 텍스트가 있습니다.

```python
class RenewBookForm(forms.Form):
    """Form for a librarian to renew books."""
    renewal_date = forms.DateField(help_text="Enter a date between now and 4 weeks (default 3).")

    def clean_renewal_date(self):
        data = self.cleaned_data['renewal_date']

        # Check if a date is not in the past.
        if data < datetime.date.today():
            raise ValidationError(_('Invalid date - renewal in past'))

        # Check if date is in the allowed range (+4 weeks from today).
        if data > datetime.date.today() + datetime.timedelta(weeks=4):
            raise ValidationError(_('Invalid date - renewal more than 4 weeks ahead'))

        # Remember to always return the cleaned data.
        return data
```

<b>/catalog/tests/test_forms.py</b> 파일을 열고 `RenewBookForm`의 기존 코드를 아래 양식 테스트 코드로 바꿉니다. 양식 및 시간 관련 기능 테스트를 지원하는 파이썬과 장고 라이브러리를 임포팅하는 것으로 시작합니다. 다음 `TestCase`에서 파생된 테스트 클래스에 대한 설명이 포함된 이름을 사용하여 모델과 동일한 방식으로 양식 테스트 클래스를 선언합니다.

```python
import datetime

from django.test import TestCase
from django.utils import timezone

from catalog.forms import RenewBookForm

class RenewBookFormTest(TestCase):
    def test_renew_form_date_field_label(self):
        form = RenewBookForm()
        self.assertTrue(form.fields['renewal_date'].label == None or form.fields['renewal_date'].label == 'renewal date')

    def test_renew_form_date_field_help_text(self):
        form = RenewBookForm()
        self.assertEqual(form.fields['renewal_date'].help_text, 'Enter a date between now and 4 weeks (default 3).')

    def test_renew_form_date_in_past(self):
        date = datetime.date.today() - datetime.timedelta(days=1)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertFalse(form.is_valid())

    def test_renew_form_date_too_far_in_future(self):
        date = datetime.date.today() + datetime.timedelta(weeks=4) + datetime.timedelta(days=1)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertFalse(form.is_valid())

    def test_renew_form_date_today(self):
        date = datetime.date.today()
        form = RenewBookForm(data={'renewal_date': date})
        self.assertTrue(form.is_valid())

    def test_renew_form_date_max(self):
        date = timezone.now() + datetime.timedelta(weeks=4)
        form = RenewBookForm(data={'renewal_date': date})
        self.assertTrue(form.is_valid())
```

첫 두 함수는 필드의 `label`과 `help_text`를 테스트합니다. 필드 사전을 사용하여 필드를 접근해야 합니다 (예: <code>form.fields ['renewal_date']</code>). Django가 레이블을 정확히 렌더링할 지라도 값이 *명시적으로* 설정되어 있지 않으면 `None`을 반환하기 때문에 레이블 값이 `None`인지 테스트해야 합니다.

나머지 함수는 양식이 허용 범위에 있는 갱신 날짜는 유효하며 범위를 벗어난 값에 대해서는 유효하지 않음을 테스트합니다. <code>datetime.timedelta()</code>를 사용하여 현재 날짜 (<code>datetime.date.today()</code>)에서 테스트 날짜 값을 구성하는 방법(이 경우 일 또는 주를 지정)에 유의하십시오. 다음 양식을 작성하여 데이터를 전달하고 유효한지 테스트합니다.

> **Note**: 여기서 우리는 실제로 데이터베이스나 테스트 클라이언트를 사용하지 않습니다. [SimpleTestCase](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#django.test.SimpleTestCase)를 사용하려면 이 테스트를 수정하십시오.
>
> 또한 양식이 유효하지 않은 경우 오류가 정확히 발생했는지 확인해야 하지만, 일반적으로 뷰 처리의 일부로 수행되므로 다음 섹션에서 다루도록 합니다.

양식에 관한 모든 것입니다. 다른 양식이 있지만, 제네릭 클래스 기반의 편집 뷰가 자동으로 생성하고 그곳에서 양식을 테스트해야 합니다. 테스트를 실행하고 코드가 여전히 통과되는지 확인하십시오.

#### 뷰

뷰 동작을 확인하기 위해 Django 테스트 [Client](https://docs.djangoproject.com/en/2.1/topics/testing/tools/#django.test.Client)를 사용합니다. 이 클래스는 URL에서 `GET`과 `POST` 요청을 시뮬레이트하고 응답을 관찰하는 데 사용할 수 있는 더미 웹 브라우저와 같은 역할을 합니다. 낮은 수준의 HTTP (결과 헤더 및 상태 코드)부터 렌더링하는 데 사용하는 템플리트와 템플리트에 전달되는 컨텍스트 데이터 에 이르기까지 거의 모든 응답을 볼 수 있습니다. (있다면) 리디렉션 체인을 확인하고 각 단계에서 URL과 상태 코드를 확인할 수도 있습니다. 이를 통해 각 뷰가 예상한대로 작동하는지 확인할 수 있습니다.

저자 목록을 제공하는 가장 간단한 뷰부터 시작하겠습니다. URL <b>/catalog/authors/</b>(URL 설정에서 'authors'라는 URL)에서 디스플레이합니다.

```python
class AuthorListView(generic.ListView):
    model = Author
    paginate_by = 10
```

위는 제네릭 목록 뷰이므로 Django가 거의 모든 것을 하였습니다. 아마 장고를 믿는다면, URL로부터 뷰에 정확히 접근할 수 있고 그 이름을 사용하여 접근할 수 있다는 것을 유일하게 테스트할 수 있을 것입니다. 그러나 테스트 주도 개발 프로세스를 사용하는 경우 뷰에 한 페이지에 10 저자를 디스플레이하는지 확인하는 테스트를 작성하는 것으로부터 시작합니다.

<b>/catalog/tests/test_views.py</b> 파일을 열고 `AuthorListView`의 기존 텍스트를 아래 테스트 코드로 바꿉니다. 이전과 마찬가지로 모델과 몇몇 유용한 클래스를 임포트합니다. `setUpTestData()` 메소드에서는 충분한 수의 `Author` 객체를 설정하여 페이지 매김을 테스트할 수 있습니다.

```python
from django.test import TestCase
from django.urls import reverse

from catalog.models import Author

class AuthorListViewTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        # Create 13 authors for pagination tests
        number_of_authors = 13

        for author_id in range(number_of_authors):
            Author.objects.create(
                first_name=f'Christian {author_id}',
                last_name=f'Surname {author_id}',
            )

    def test_view_url_exists_at_desired_location(self):
        response = self.client.get('/catalog/authors/')
        self.assertEqual(response.status_code, 200)

    def test_view_url_accessible_by_name(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)

    def test_view_uses_correct_template(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'catalog/author_list.html')

    def test_pagination_is_ten(self):
        response = self.client.get(reverse('authors'))
        self.assertEqual(response.status_code, 200)
        self.assertTrue('is_paginated' in response.context)
        self.assertTrue(response.context['is_paginated'] == True)
        self.assertTrue(len(response.context['author_list']) == 10)

    def test_lists_all_authors(self):
        # Get second page and confirm it has (exactly) remaining 3 items
        response = self.client.get(reverse('authors')+'?page=2')
        self.assertEqual(response.status_code, 200)
        self.assertTrue('is_paginated' in response.context)
        self.assertTrue(response.context['is_paginated'] == True)
        self.assertTrue(len(response.context['author_list']) == 3)
```

모든 테스트는 (TestCase 파생 클래스에 속한) 클라이언트를 사용하여 GET 요청을 시뮬레이트하고 응답을 받습니다. 첫 번째 버전은 지정된 URL (참고 사항, 도메인이 없는 지정된 경로)을 확인하는 반면 두 번째 버전은 URL 설정에 있는 이름으로부터 URL을 생성합니다.

```python
response = self.client.get('/catalog/authors/')
response = self.client.get(reverse('authors'))
```

응답을 받으면 상태 코드, 사용된 템플리트, 응답의 페이지 매김 여부, 반환된 항목 수 및 총 항목 수를 응답에 쿼리합니다.

위에서 살펴본 가장 흥미로운 변수는 <code>response.context</code>입니다. 이는 뷰가 템플리트로 전달하는 컨텍스트 변수입니다. 이는 템플리트가 필요한 모든 데이터를 확보하고 있는지 확인할 수 있기 때문에 테스트에 매우 유용합니다. 즉, 의도한 템플리트와 템플리트에서 얻은 데이터를 확인할 수 있습니다. 이는 렌더링 문제가 템플릿 때문이라는 것을 확인하는 데 많은 도움을 줍니다.

##### 로그인한 사용자로 제한된 뷰

어떤 경우에는 로그인한 사용자로 제한된 뷰를 테스트하려고 할 수 있습니다. 예를 들어, `LoanedBooksByUserListView`는 이전 뷰와 매우 유사하지만 로그인한 사용자만 사용할 수 있으며 현재 사용자가 대여한 `BookInstance` 레코드를 'on loan'이며 "가장 오래된 것부터"라는 순서로 디스플레이합니다.

```python
from django.contrib.auth.mixins import LoginRequiredMixin

class LoanedBooksByUserListView(LoginRequiredMixin, generic.ListView):
    """Generic class-based view listing books on loan to current user."""
    model = BookInstance
    template_name ='catalog/bookinstance_list_borrowed_user.html'
    paginate_by = 10

    def get_queryset(self):
        return BookInstance.objects.filter(borrower=self.request.user).filter(status__exact='o').order_by('due_back')

```

<b>/catalog/tests/test_views.py</b>에 다음 테스트 코드를 추가하십시오. 먼저 SetUp()을 사용하여 테스트에서 사용할 사용자 로그인 계정과 `BookInstance` 객체(관련 서적과 기타 레코드를 함께)를 생성합니다. 책의 절반은 각 테스트 사용자가 빌리겠지만 처음에는 모든 책의 상태를 "maintenance"로 설정합니다. 나중에이 객체 중 일부를 수정하여야 하므로 `setUpTestData()` 대신 `SetUp()`을 사용합니다.

> **Note**: 아래의 `setUp()` 코드는 지정된 언어의 책을 생성하지만 코드는 언어 모델을 포함하지 않았습니다. 이를 도전 과제로 남겨 놓았습니다. 언어 객체를 만들거나 임포트하는 코드 부분을 주석으로 처리합니다. 다음에 나오는 `RenewBookInstancesViewTest` 절에서도 이 작업을 수행해야 합니다.

```python
import datetime

from django.utils import timezone
from django.contrib.auth.models import User # Required to assign User as a borrower

from catalog.models import BookInstance, Book, Genre, Language

class LoanedBookInstancesByUserListViewTest(TestCase):
    def setUp(self):
        # Create two users
        test_user1 = User.objects.create_user(username='testuser1', password='1X<ISRUkw+tuK')
        test_user2 = User.objects.create_user(username='testuser2', password='2HJ1vRV0Z&3iD')

        test_user1.save()
        test_user2.save()

        # Create a book
        test_author = Author.objects.create(first_name='John', last_name='Smith')
        test_genre = Genre.objects.create(name='Fantasy')
        test_language = Language.objects.create(name='English')
        test_book = Book.objects.create(
            title='Book Title',
            summary='My book summary',
            isbn='ABCDEFG',
            author=test_author,
            language=test_language,
        )

        # Create genre as a post-step
        genre_objects_for_book = Genre.objects.all()
        test_book.genre.set(genre_objects_for_book) # Direct assignment of many-to-many types not allowed.
        test_book.save()

        # Create 30 BookInstance objects
        number_of_book_copies = 30
        for book_copy in range(number_of_book_copies):
            return_date = timezone.now() + datetime.timedelta(days=book_copy%5)
            the_borrower = test_user1 if book_copy % 2 else test_user2
            status = 'm'
            BookInstance.objects.create(
                book=test_book,
                imprint='Unlikely Imprint, 2016',
                due_back=return_date,
                borrower=the_borrower,
                status=status,
            )

    def test_redirect_if_not_logged_in(self):
        response = self.client.get(reverse('my-borrowed'))
        self.assertRedirects(response, '/accounts/login/?next=/catalog/mybooks/')

    def test_logged_in_uses_correct_template(self):
        login = self.client.login(username='testuser1', password='1X<ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Check we used correct template
        self.assertTemplateUsed(response, 'catalog/bookinstance_list_borrowed_user.html')
```

사용자가 로그인하지 않은 상태에서 보기가 로그인 페이지로 리디렉션되는지 확인하기 위해 `test_redirect_if_not_logged_in()`에 설명된대로 `assertRedirects`를 사용합니다. 로그인한 사용자의 페이지가 표시되는지 확인하려면 먼저 테스트 사용자로 로그인한 다음 페이지를 다시 접근하여 `status_code`가 200 (성공)인지 확인하십시오.

나머지 테스트는 뷰가 현재 대출자에게 대여한 책만 반환한다고 확인합니다. 아래 코드를 복사하여 위의 테스트 클래스 끝에 붙여 넣으십시오.

```python
def test_only_borrowed_books_in_list(self):
        login = self.client.login(username='testuser1', password='1X<ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Check that initially we don't have any books in list (none on loan)
        self.assertTrue('bookinstance_list' in response.context)
        self.assertEqual(len(response.context['bookinstance_list']), 0)

        # Now change all books to be on loan
        books = BookInstance.objects.all()[:10]

        for book in books:
            book.status = 'o'
            book.save()

        # Check that now we have borrowed books in the list
        response = self.client.get(reverse('my-borrowed'))
        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        self.assertTrue('bookinstance_list' in response.context)

        # Confirm all books belong to testuser1 and are on loan
        for bookitem in response.context['bookinstance_list']:
            self.assertEqual(response.context['user'], bookitem.borrower)
            self.assertEqual('o', bookitem.status)

    def test_pages_ordered_by_due_date(self):
        # Change all books to be on loan
        for book in BookInstance.objects.all():
            book.status='o'
            book.save()

        login = self.client.login(username='testuser1', password='1X<ISRUkw+tuK')
        response = self.client.get(reverse('my-borrowed'))

        # Check our user is logged in
        self.assertEqual(str(response.context['user']), 'testuser1')
        # Check that we got a response "success"
        self.assertEqual(response.status_code, 200)

        # Confirm that of the items, only 10 are displayed due to pagination.
        self.assertEqual(len(response.context['bookinstance_list']), 10)

        last_date = 0
        for book in response.context['bookinstance_list']:
            if last_date == 0:
                last_date = book.due_back
            else:
                self.assertTrue(last_date <= book.due_back)
                last_date = book.due_back
```

원한다면 페이지 매김 테스트를 추가 할 수도 있습니다.

##### 양식과 뷰 동시 테스트

양식을 사용한 뷰 테스트에서 초기 디스플레이, 데이터 유효성 검사 실패 후 디스플레이 및 유효성 검사 성공 후 디스플에이하는 코드 경로를 더 테스트해야 하기 때문에 위의 경우보다 조금 복잡합니다. 다행한 것은 디스플레이 전용 뷰와 거의 동일한 방식으로 테스트 용 클라이언트를 사용한다는 것입니다.

시연을 위해, 도서 갱신에 사용된 뷰에 대한 몇 가지 테스트를 작성해 봅니다 (<code>renew_book_librarian()</code>).

```python
from catalog.forms import RenewBookForm

@permission_required('catalog.can_mark_returned')
def renew_book_librarian(request, pk):
    """View function for renewing a specific BookInstance by librarian."""
    book_instance = get_object_or_404(BookInstance, pk=pk)

    # If this is a POST request then process the Form data
    if request.method == 'POST':

        # Create a form instance and populate it with data from the request (binding):
        book_renewal_form = RenewBookForm(request.POST)

        # Check if the form is valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required (here we just write it to the model due_back field)
            book_instance.due_back = form.cleaned_data['renewal_date']
            book_instance.save()

            # redirect to a new URL:
            return HttpResponseRedirect(reverse('all-borrowed'))

    # If this is a GET (or any other method) create the default form
    else:
        proposed_renewal_date = datetime.date.today() + datetime.timedelta(weeks=3)
        book_renewal_form = RenewBookForm(initial={'renewal_date': proposed_renewal_date})

    context = {
        'book_renewal_form': book_renewal_form,
        'book_instance': book_instance,
    }

    return render(request, 'catalog/book_renew_librarian.html', context)
```
<code>can_mark_returned</code> 권한을 가진 사용자만 뷰를 사용할 수 있는지 테스트하고 존재하지 않는 `BookInstance`를 갱신하려고 하면 HTTP 404 오류 페이지로 리디렉션됩니다. 양식의 초기 값이 3 주 후의 날짜로 되어 있는지 확인하고 유효성 검사가 성공하면 "대여한 모든 도서" 뷰로 리디렉션됩니다. validation-fail 테스트를 검사하는 과정에서 양식이 적절한 오류 메시지를 보내고 있는지 확인합니다.

<b>/catalog/tests/test_views.py</b> 끝에 테스트 클래스(아래 참조)의 첫 번째 부분을 추가하십시오 . 이렇게하면 사용자 두명와 책 인스턴스 둘이 작성되지만 오직 한 사용자에게 뷰를 접근하는 데 필요한 권한을 제공합니다.

```python
import uuid

from django.contrib.auth.models import Permission # Required to grant the permission needed to set a book as returned.

class RenewBookInstancesViewTest(TestCase):
    def setUp(self):
        # Create a user
        test_user1 = User.objects.create_user(username='testuser1', password='1X<ISRUkw+tuK')
        test_user2 = User.objects.create_user(username='testuser2', password='2HJ1vRV0Z&3iD')

        test_user1.save()
        test_user2.save()

        permission = Permission.objects.get(name='Set book as returned')
        test_user2.user_permissions.add(permission)
        test_user2.save()

        # Create a book
        test_author = Author.objects.create(first_name='John', last_name='Smith')
        test_genre = Genre.objects.create(name='Fantasy')
        test_language = Language.objects.create(name='English')
        test_book = Book.objects.create(
            title='Book Title',
            summary='My book summary',
            isbn='ABCDEFG',
            author=test_author,
            language=test_language,
        )

        # Create genre as a post-step
        genre_objects_for_book = Genre.objects.all()
        test_book.genre.set(genre_objects_for_book) # Direct assignment of many-to-many types not allowed.
        test_book.save()

        # Create a BookInstance object for test_user1
        return_date = datetime.date.today() + datetime.timedelta(days=5)
        self.test_bookinstance1 = BookInstance.objects.create(
            book=test_book,
            imprint='Unlikely Imprint, 2016',
            due_back=return_date,
            borrower=test_user1,
            status='o',
        )

        # Create a BookInstance object for test_user2
        return_date = datetime.date.today() + datetime.timedelta(days=5)
        self.test_bookinstance2 = BookInstance.objects.create(
            book=test_book,
            imprint='Unlikely Imprint, 2016',
            due_back=return_date,
            borrower=test_user2,
            status='o',
        )
```

위의 코드에서 테스트 중에 사용 권한을 부여하는 코드 블록은 다음과 같습니다.

```python
        permission = Permission.objects.get(name='Set book as returned')
        test_user2.user_permissions.add(permission)
        test_user2.save()
```

테스트 클래스의 끝에 다음 테스트를 추가하십시오. 적절한 권한을 가진 사용자(testuser2)만 뷰를 접근할 수 있는지 확인합니다. 사용자가 로그인하지 않았을 때, 사용자가 로그인했지만 권한이 없는 경우, 권한이 있지만 대출자가 아닌 경우 (성공해야 함), 존재하지 않는 `BookInstance`에 접근을 시도할 때  어떤 일이 발생하는지 등 모든 경우를 확인합니다. 해당 템플리트를 사용하는 지도 확인합니다.

```python
def test_redirect_if_not_logged_in(self):
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        # Manually check redirect (Can't use assertRedirect, because the redirect URL is unpredictable)
        self.assertEqual(response.status_code, 302)
        self.assertTrue(response.url.startswith('/accounts/login/'))

    def test_redirect_if_logged_in_but_not_correct_permission(self):
        login = self.client.login(username='testuser1', password='1X<ISRUkw+tuK')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 403)

    def test_logged_in_with_permission_borrowed_book(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance2.pk}))

        # Check that it lets us login - this is our book and we have the right permissions.
        self.assertEqual(response.status_code, 200)

    def test_logged_in_with_permission_another_users_borrowed_book(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))

        # Check that it lets us login. We're a librarian, so we can view any users book
        self.assertEqual(response.status_code, 200)

    def test_HTTP404_for_invalid_book_if_logged_in(self):
        # unlikely UID to match our bookinstance!
        test_uid = uuid.uuid4()
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk':test_uid}))
        self.assertEqual(response.status_code, 404)

    def test_uses_correct_template(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 200)

        # Check we used correct template
        self.assertTemplateUsed(response, 'catalog/book_renew_librarian.html')
```

아래처럼 다음 테스트 메소드을 추가하십시오. 이것은 양식의 초기 날짜가 3 주 후인지 확인합니다. 양식 필드의 초기값을 접근하는 방법에 유의하십시오.

```python
def test_form_renewal_date_initially_has_date_three_weeks_in_future(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        response = self.client.get(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}))
        self.assertEqual(response.status_code, 200)

        date_3_weeks_in_future = datetime.date.today() + datetime.timedelta(weeks=3)
        self.assertEqual(response.context['form'].initial['renewal_date'], date_3_weeks_in_future)
```

> **!** 아래처럼 다음 테스트 메소드을 추가하십시오. 이는 양식의 초기 날짜가 3 주 후인지 확인합니다. 양식 필드의 초기값을 접근하는 방법에 유의하십시오.

다음 테스트 (클래스에 이를 추가)는 갱신을 성공하면 빌린 모든 책 목록으로  뷰 리다이렉트을 확인합니다. 여기서 차이점은 클라이언트를 사용하여 데이터를 `POST`하는 방법을 처음 보여줍니다. post <i>데이터</i>는 post 함수의 두 번째 인수이며, key/value 사전으로 지정됩니다.

```python
def test_redirects_to_all_borrowed_book_list_on_success(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        valid_date_in_future = datetime.date.today() + datetime.timedelta(weeks=2)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk':self.test_bookinstance1.pk,}), {'renewal_date':valid_date_in_future})
        self.assertRedirects(response, reverse('all-borrowed'))
```

> **!** <i>모든 대여 도서</i> 뷰를 <i>도전 과제</i>에 추가하였으며, 대신 코드가 홈 페이지 '/'로 리디렉션될 수 있습니다. 그렇다면 테스트 코드의 마지막 두 줄을 아래 코드와 같이 수정하십시오. 요청에서 <code>follow=True</code>는 요청이 최종 목적 URL을 반환하도록 합니다 (따라서 `/`가 아니라 `/catalog/`임을 확인합니다).
```python
response = self.client.post(reverse('renew-book-librarian', kwargs={'pk':self.test_bookinstance1.pk,}), {'renewal_date':valid_date_in_future}, follow=True )
 self.assertRedirects(response, '/catalog/')
```

아래에서 볼 수 있듯이 마지막 두 함수를 클래스에 복사합니다. 이들은 유효하지 않은 갱신 날짜가 있는 `POST` 요청을 다시 테스트합니다. `assertFormError()`를 사용하여 오류 메시지가 예상한 것과 같은지 확인합니다.

```python
def test_form_invalid_renewal_date_past(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        date_in_past = datetime.date.today() - datetime.timedelta(weeks=1)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}), {'renewal_date': date_in_past})
        self.assertEqual(response.status_code, 200)
        self.assertFormError(response, 'form', 'renewal_date', 'Invalid date - renewal in past')

    def test_form_invalid_renewal_date_future(self):
        login = self.client.login(username='testuser2', password='2HJ1vRV0Z&3iD')
        invalid_date_in_future = datetime.date.today() + datetime.timedelta(weeks=5)
        response = self.client.post(reverse('renew-book-librarian', kwargs={'pk': self.test_bookinstance1.pk}), {'renewal_date': invalid_date_in_future})
        self.assertEqual(response.status_code, 200)
        self.assertFormError(response, 'form', 'renewal_date', 'Invalid date - renewal more than 4 weeks ahead')
```

같은 종류의 기술을 사용하여 다른 뷰를 테스트할 수 있습니다.

#### 템플리트

Django는 테스트 API를 제공하여 뷰에 의해 해당 템플리트가 호출되는지 확인하고 정보를 정확히 전송하고 있는지 확인할 수있게 합니다. 그러나 Django에서 HTML 출력이 예상대로 렌더링되는 지 테스트하기 위한 특정 API 지원은 없습니다.

---

### 추천 테스트 도구들

Django의 테스트 프레임워크는 효과적인 유닛과 통합 테스트를 작성하는 데 도움을 줄 수 있습니다. 근본적인 **unittest** 프레임워크가 수행할 수있는 작업의 단면만 살짝 보았고, 장고의 추가 사항은 말할 것도 없습니다. (예를 들어 [unittest.mock](https://docs.python.org/3.5/library/unittest.mock-examples.html)을 사용하여 서드 파티 라이브러리를  패치하는 방법을 체크 아웃하여 코드를 보다 철저하게 테스트할 수 있습니다).

사용 가능한 많은 테스트 도구들이 있지만 다음 두 가지를 추천합니다.

- [Coverage](http://coverage.readthedocs.io/en/latest/): 이 Python 도구는 테스트에서 실제로 실행된 코드의 양을 보고합니다. 특히 시작하기에 유용하며 테스트해야 할 내용을 정확하게 확인하려고 합니다.
- [Selenium](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Your_own_automation_environment)는 실제 브라우저에서 테스트를 자동화하는 프레임워크입니다. 이 도구를 사용하면 사이트와 상호 작용하는 실제 사용자를 시뮬레이트할 수 있으며, 사이트 테스트(통합 테스트의 다음 단계)를 위한 훌륭한 프레임워크를 제공합니다.

---

### 도전 과제

테스트할 수 있는 많은 모델과 뷰가 있습니다. 간단한 작업으로 `AuthorCreate` 뷰를 위한 테스트 케이스를 만들어 보십시오.

```python
class AuthorCreate(PermissionRequiredMixin, CreateView):
    model = Author
    fields = '__all__'
    initial = {'date_of_death':'12/10/2016'}
    permission_required = 'catalog.can_mark_returned'
```

지정한 것이나 설계의 일부분을 검사해야 한다는 것을 기억하십시오. 여기에는 전근 권한이 있는 사용자, 초기 날짜, 사용된 템플리트 및 성공시 뷰를 리디렉션할 위치를 포함합니다.

---

### 요약

테스트 코드를 작성하는 것은 재미도 매력도 없으므로 결과적으로 웹사이트를 만들 때 종종 남겨놀 수 있습니다. 그러나 변경 후에도 코드를 공개하는 것이 안전하고 유지 관리에 비용 효율적이라는 것을 확신하는 것은 중요합니다.

이 부에서는 모델, 폼 및 뷰에 대한 테스트를 작성하고 실행하는 방법을 보여줍니다. 가장 중요한 것은 테스트해야 할 사항에 대한 간략한 요약을 제공 했습니다. 이는 언제 시작 하느냐에 따라 가장 힘든 일이 될 수 있습니다. 알아야 할 것이 많지만, 이미 배운 것만을 사용해서 라도 웹사이트에 대한 효과적인 단위 테스트를 만들 수 있어야 합니다.

다음인 마지막 부에서는 훌륭한 (그리고 완전히 테스트 된) Django 웹사이트 배포 방법을 보여줍니다.

---

### 참고 문서

-	[Writing and running tests](https://docs.djangoproject.com/en/2.1/topics/testing/overview/) (Django docs)
-	[Writing your first Django app, part 5 > Introducing automated testing](https://docs.djangoproject.com/en/2.1/intro/tutorial05/) (Django docs)
-	[Testing tools reference](https://docs.djangoproject.com/en/2.1/topics/testing/tools/) (Django docs)
-	[Advanced testing topics](https://docs.djangoproject.com/en/2.1/topics/testing/advanced/) (Django docs)
-	[A Guide to Testing in Django](http://toastdriven.com/blog/2011/apr/10/guide-to-testing-in-django/) (Toast Driven Blog, 2011)
-	[Workshop: Test-Driven Web Development with Django](http://test-driven-django-development.readthedocs.io/en/latest/index.html) (San Diego Python, 2014)
-	[Testing in Django (Part 1) - Best Practices and Examples](https://realpython.com/blog/python/testing-in-django-part-1-best-practices-and-examples/) (RealPython, 2013)

---

### 이 튜토리얼은 다음 부들로 구성되어 있습니다.

-	[Django 소개](introduction.md)
-	[Django 개발 환경 설정](developmentEnvironment.md)
-	[Django 튜토리얼: The Local Library website](tutorialLocalLibraryWebsite.md)
-	[Django 튜토리얼 2부: 웹사이트 골조 만들기](skeletonWebsite.md)
-	[Django 튜토리얼 3부: 모델](models.md)
-	[Django 튜토리얼 4부: Django 관리 사이트](adminSite.md)
-	[Django 튜토리얼 5부: 홈 페이지 만들기](homePage.md)
-	[Django 튜토리얼 6부: 일반 목록과 상세 보기](genericViews.md)
-	[Django 튜토리얼 7부: 세션 프레임워크](sessions.md)
-	[Django 튜토리얼 8부: 사용자 인증 및 권한](authentication.md)
-	[Django 튜토리얼 9부: 양식 작업](forms.md)
-	[Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅](testing.md)
-	[Django 튜토리얼 11부: 운영으로 Django 전개](deployment.md)
-	[Django 웹 응용프로그램 보안](webApplicationSecurity.md)
