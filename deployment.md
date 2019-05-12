Django 튜토리얼 11부: 프로덕션(Production)으로 Django 배치(Deploying)
---------------------------------------------------------------------

이제는 멋진 LocalLibrary 웹사이트를 만들었고 테스트했으므로 인터넷을 통해 도서관 직원과 회원이 접근할 수 있도록 공용 웹 서버를 설치하려고 합니다. 이 단원에서는 웹사이트를 배포할 호스트를 찾는 방법과 프로덕션 사이트 준비를 위해 해야 할 일에 대해 간략하게 설명합니다.

> 선수지식: [Django 튜토리얼 10부: Django 웹 응용프로그램 테스팅](testing.md)까지 모든 주제를 숙지하여야 합니다.
>
> 목표: Django 앱을 프로덕션 환경에 배치하는 방법과 플랫폼에 대하여 공부합니다.

---

### 개요

일단 사이트 개발이 끝나면 (또는 공개 테스트를 시작하기에 "충분히" 완료된) 개발자의 개발용 컴퓨터보다 공개적이고 접근이 가능한 어딘가에서 호스팅해야 합니다.

지금까지 개발 환경에서는 Django 개발 웹 서버를 사용하여 로컬 브라우저와 네트워크를 공유하는 사이트에서 디버깅과 기타 개인 정보이 노출되는 (불안전한) 상태의 개발 설정에서 웹사이트가 실행되었습니다. 웹사이트를 외부에 호스팅하려면 먼저 다음 사항들이 선행되어야 합니다.

-	프로젝트 설정을 약간 변경합니다.
-	Django 앱을 호스팅할 환경을 선택합니다.
-	정적 파일을 호스팅할 환경을 선택합니다.
-	웹사이트의 서비스를 위한 프로덕션 수준의 인프라를 설정합니다.

11부에서는 호스팅 사이트 선택 옵션, Django 앱 프로덕션 준비 사항에 대한 간단한 개요와 Heroku 클라우드 호스팅 서비스를 사용하여 LocalLibrary 웹사이트를 설치하는 방법의 실례를 위한 안내를 합니다.

---

### 프로덕션 환경이란?

프로덕션 환경은 웹사이트의 외부 사용을 위해 실행할 서버 컴퓨터가 제공하는 환경입니다. 환경에는 다음 사항을 포함합니다.

-	웹사이트를 실행하는 컴퓨터 하드웨어.
-	운영체제 (예 : Linux, Windows).
-	웹사이트를 작성한 프로그래밍 언어 런타임과 프레임워크 라이브러리.
-	페이지와 기타 콘텐츠를 제공하는 웹 서버 (예 : Nginx, Apache).
-	Django 웹사이트와 웹 서버간에 "동적" 요청을 전달하는 어플리케이션 서버.
-	웹사이트가 사용하는 데이터베이스.

> **Note**: 프로덕션 구성 방법에 따라 역방향 프록시,로드 밸런서 등이 필요할 수 있습니다.

서버 컴퓨터를 구내에(on premises) 설치하면 링크를 통해 빠르게 인터넷에 연결될 수 있지만 "클라우드"에 호스팅된 컴퓨터를 사용하는 것이 훨씬 더 일반적입니다. 이것이 실제로 의미하는 바는 호스팅 회사의 데이터 센터에 있는 일부 원격 컴퓨터 (또는 "가상"컴퓨터)에서 코드가 실행되는 것입니다. 원격 서버는 일반적으로 보장된 수준의 컴퓨팅 리소스 (예: CPU, RAM, 저장 메모리 등) 및 인터넷 연결을 정해진 가격으로 제공합니다.

원격에서 접근할 수 있는 이런 종류의 컴퓨팅/네트워킹 하드웨어를 <i>IaaS (Infrastructure as a Service)</i>라고 합니다. 많은 IaaS 공급 업체에서는 특정한 운영 체제를 사전 설치하는 옵션을 제공하므로 이후 프로덕션 환경의 다른 구성 요소를 설치해야 합니다. 완벽한 Django와 웹 서버 설정을 포함하는 기능을 갖춘 환경을 다른 벤더들로부터 선택할 수도 있습니다.

> **Note**: 사전 구축된 환경은 설정 노력을 줄여 주기 때문에 웹사이트를 매우 쉽게 구성할 수 있지만 익숙하지 않은 서버(또는 다른 구성 요소)로 인하여 설정 옵션이 제한될 수 있으며 이전 버전의 OS를 사용할 수도 있습니다. 구성 요소를 직접 설치하여 원하는 항목을 얻는 것이 더 바람직할 때가 많으며, 시스템의 일부를 업그레이드해야 하는 경우도 있습니다. 따라서 어디에서 어떻게 시작할 지를 알고 있어야 합니다.

다른 호스팅 제공 업체에서는 Django를 <i>Platform as a Service (PaaS)</i> 서비스의 일부로 지원합니다. 이러한 호스팅의 경우 호스트 플랫폼이 사용자를 위해 처리하므로 대부분의 프로덕션 환경(웹 서버, 어플리케이션 서버, 로드 밸런서 등)에 대해 걱정할 필요(어플리케이션 확장을 위한 대부분의 수행해야 하는 작업과 함께)가 없습니다 . 따라서 웹 어플리케이션에만 집중하고 다른 모든 서버 인프라에는 신경쓸 필요가 없으므로 배치가 보다 쉽습니다.

일부 개발자는 PaaS에 비해 높은 IaaS의 유연성 향상을 선택할 것이고 다른 개발자는 PaaS의 유지 관리 오버 헤드 감소와 보다 용이한 확장성을 선택할 것입니다. 시작하기 전에 PaaS에서 웹사이트를 설정하는 것이 훨씬 쉬워 졌으므로 이 단원에서 수행할 쉬어진 작업으로 설명합니다.

> **Note**: Python/Django 친화적인 호스팅 제공 업체를 선택했다면 업체는 웹 서버, 어플리케이션 서버, 리버스 프록시 등의 다른 구성을 사용하여 Django 웹사이트를 설정하는 방법에 대한 지침을 제공해야 합니다 (PaaS를 선택한 경우에는 필요 없습니다). 예를 들어, [Digital Ocean Django community docs](https://www.digitalocean.com/community/tutorials?q=django)에는 다양한 구성을 위한 단계별 가이드를 제공합니다.

---

### 호스팅 공급자 선택

Django를 적극적으로 지원하거나 잘 운영하는 것으로 알려진 100여 호스팅 제공 업체가 있습니다 ([Djangofriendly hosts](http://djangofriendly.com/hosts/)에서 상당히 광범위한 목록을 찾을 수 있습니다). 이러한 업체들은 다양한 유형의 환경(IaaS, PaaS)을 제공하며, 또한 다양한 수준의 컴퓨팅과 네트워크 리소스를 서로 다른 가격으로 제공합니다.

호스트를 선택할 때 고려해야 할 사항은 다음과 같습니다.

-	사이트가 얼마나 바쁠 지, 그리고 수요를 충족시키기 위하여 필요한 데이터와 이 에 필요한 컴퓨팅 리소스 비용.
-	수평 확장(기계 추가), 수직 확장(강력한 시스템으로 업그레이드) 및 이를 수행하는 데 드는 비용.
-	공급 업체가 데이터 센터를 보유하고 있고 접근이 가장 빠른 곳.
-	호스트의 가동과 중지 시간 성능 이력.
-	사이트 관리를 위해 제공되는 도구(SFTP와 FTP 등)는 사용하기 쉽고 안전한가?
-	서버 모니터링을 위하여 내장된 프레임워크.
-	공개된 제한 사항. 일부 호스트는 의도적으로 특정 서비스(예: 이메일)를 차단합니다. 다른 경우는 어떤 가격에서 특정 시간 동안에만 "실시간" 서비스를 제공하거나 소량의 저장 공간만을 제공합니다.
-	추가 혜택. 일부 업체는 무료 도메인 이름을 제공하기도 하고, SSL 인증서 지원을 위하여 따로 지불해야 할 수도 있습니다.
-	의존하고 있는 "무료" 계층이 시간이 지남에 따라 만료되는 지 여부와 더 비싼 서비스 계층으로 마이그레이션하는 비용 여부에 따라 처음부터 해당 서비스 계층을 사용하는 것이 더 바람직할 수 있습니다.

좋은 소식은 "평가", "개발자" 또는 "애호가"를 위한 "무료"로 컴퓨팅 환경을 제공하는 사이트가 다수 있습니다. 이들은 항상 상당히 제약이 있고, 제한적 환경이며, 일정 기간 후에 종료될 수 있음을 알고 있어야 합니다. 그러나 실제 환경에서 소통량이 적은 사이트를 테스트할 때 유용하며, 사이트가 화장되어 더 많은 리소스가 필요하여 유료로 전환할 때 쉽게 마이그레이션을 지원할 수도 있습니다. 이 범주에서 볼 때 널리 사용되는 선택으로 [Heroku](https://www.heroku.com/), [Python Anywhere](https://www.pythonanywhere.com/), [Amazon Web Services](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-free-tier.html), [Microsoft Azure](https://azure.microsoft.com/en-us/pricing/details/app-service/) 등이 있습니다.

많은 공급 업체는 보다 유용한 수준의 컴퓨팅 성능과 더 적은 제한 사항을 제공하는 "기본" 계층을 갖추고 있습니다. 호스팅 제공 업체로 [Digital Ocean](https://www.digitalocean.com/)과 [Python Anywhere](https://www.pythonanywhere.com/)는 비교적 저렴하게 (월 5달러에서 10달러 사이) 기본 컴퓨팅 계층을 제공하여 인기를 끌고 있습니다.

> **Note**: 선택 기준이 가격만은 아닙니다. 웹사이트가 성공적으로 개발되어 운영 중이면 확장성이 가장 중요한 고려 사항이 될 수 있습니다.

---

### 웹사이트 공개(publish) 준비

<i>django-admin</i>와 <i>manage.py</i>를 사용하여 [Django 골조 웹사이트](skeletonWebsite.md)의 개발이 쉽도록 작성되었습니다. (<b>settings.py</b>에 지정된) Django 프로젝트 설정 중 많은 부분은 보안 또는 성능 상의 이유로 프로덕션 환경에 따라 변경되어야 합니다.

> **Tip**: 프로덕션을 위해 별도의 <b>settings.py</b> 파일을 가져가고, 이를 위하여 별도 파일이나 환경 변수에서 민감한 설정을 임포트하는 것이 일반적입니다. 소스 코드를 공용 저장소에 공개한 경우에도 이 파일에 ㄷ한 보안이 필요하기 때문입니다.

확인해야 하는 중요한 설정은 다음과 같습니다.

-	`DEBUG`. 프로덕션 환경에서는 이 값을 `False`로 설정해야 합니다 (<code>DEBUG = False</code>). 민감하고 감춰야 하는 디버그 추적과 변수 정보를 디스플레이하지 않도록 합니다.
-	<code>SECRET_KEY</code>. 이것은 CSRF 보호 등에 사용되는 큰 난수값입니다. 프로덕션 환경에서 사용되는 키는 소스 코드 버전 관리 시스템에 저장하지 말아야 하며 프로덕션 서버 외부에서 접근할 수도 없어야 합니다. Django 문서에서는 환경 변수에서 로드하거나 서브 전용 파일에서 읽는 것이 바람직하다고 합니다.

```python
# Read SECRET_KEY from an environment variable
import os
SECRET_KEY = os.environ['SECRET_KEY']

# OR

# Read secret key from a file
with open('/etc/secret_key.txt') as f:
    SECRET_KEY = f.read().strip()
```

*LocalLibrary* 어플리케이션을 변경하여 환경 변수에서 <code>SECRET_KEY</code>와 <code>DEBUG</code> 변수를 정의하였다면 이를 읽고 그렇지 않다면 설정 파일의 기본값을 사용하십시오.

<b>/locallibrary/settings.py</b>를 열고, 원래 <code>SECRET_KEY</code> 설정(두번째 문장)을 비활성화하고 새로이 두 줄을 추가하십시오. 개발 중에는 키를 위한 환경 변수를 지정하지 않으므로 기본값을 사용합니다 (프로덕션 환경에서는 개발 키를 사용하지 않으므로 개발 중에는 어떤 키를 사용하거나 키의 "누출"은 문제가 되지 않습니다).

```python
# SECURITY WARNING: keep the secret key used in production secret!
# SECRET_KEY = 'cg#p$g+j9tax!#a3cup@1$8obt2_+&k3q+pmu)5%asj6yjpkag'
import os
SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY', 'cg#p$g+j9tax!#a3cup@1$8obt2_+&k3q+pmu)5%asj6yjpkag')
```

다음 기존 DEBUG 설정을 주석 처리하고 아래 줄을 추가하십시오.

```python
# SECURITY WARNING: don't run with debug turned on in production!
# DEBUG = True
DEBUG = os.environ.get('DJANGO_DEBUG', '') != 'False'
```

`DEBUG`의 값은 기본적으로 `True`입니다. 그러나 `DJANGO_DEBUG` 환경 변수 값을 `False`로 설정하면 `False`가 됩니다. 환경 변수는 Python 데이터 타입(`Boolean`)이 아니고 문자열입니다. 따라서 문자열로 비교해야 합니다. `DEBUG` 변수를 `False`로 설정하는 방법은 실제 `False` 문자열로 이를 설정하는 것입니다.

다음 명령을 실행하여 환경 변수를 False로 설정할 수 있습니다.

```bash
export DJANGO_DEBUG=False
```

[Deployment checklist](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)(Django 문서)는 변경하여야 하는 설정의 전체 체크리스트를 나열합니다. 아래 terminal 명령을 사용하여 체크리스트를 볼 수 있습니다.

```bash
python3 manage.py check --deploy
```

---

### Heroku에 LocalLibrary 설치

이 섹션에서는 [Heroku PaaS cloud](http://heroku.com/)에 <i>LocalLibrary</i>를 설치하는 실용적인 시현를 보입니다.

#### 왜 Heroku?

Heroku는 가장 오래되고 인기있는 클라우드 기반 PaaS 서비스 중 하나입니다. 원래는 Ruby 앱만 지원했지만 지금은 Django를 포함한 많은 프로그래밍 환경에서 앱 호스팅을 지원할 수 있습니다.

아래 여러 이유로 Heroku를 선택합니다.

-	Heroku는 (몇 가지 제한이 있지만) 실제 무료인 무료 티어(tier)를 제공합니다.
-	PaaS로서, Heroku는 개발자를 위해 많은 웹 인프라를 관리합니다. 서버, 로드 밸런서, 역방향 프록시 또는 Heroku가 제공하는 기타 웹 인프라에 대해 걱정할 필요가 없으므로 쉽게 시작할 수 있습니다.
-	제한 사항들이 있지만 특정 어플리케이션에 영향을 주지 않습니다. 예를 들면,

	-	Heroku는 지속성있는 저장소를 지원하지 않으므로 사용자는 업로드한 파일을 Heroku 자체에 안전하게 저장할 수 없습니다.
	-	무료 티어는 30분 이내 요청이 없으면 비활성 웹 앱을 수면(sleep) 상태로 바꿉니다. 이후 요청이 있으면 사이트를 깨워 응답하는 데까지 몇 초 걸릴 수 있습니다.
	-	무료 티어는 월 단위로 사이트가 실행되는 시간(사이트가 "잠든 시간"은 제외)을 제한합니다. 사용률이 낮거나 데모 사이트인 경우에는 좋지만 100% 가동 시간이 필요할 경우에는 적합하지 않습니다.
	-	다른 제약 사항에 대해 [Limits](https://devcenter.heroku.com/articles/limits)(Heroku 문서)를 참고하십시오.

-	대부분을 작동하고, 작동시킬 수 있다면, 어플리케이션 확장이 매우 쉽습니다.

Heroku는 이 데모를 시연하기에 적합하지만 실제 웹 사이트를 운영하기에는 적합하지 않을 수 있습니다. 무료 티어를 사용하지 않는다면 Heroku는 비용 측면에서 덜 유연하고 잠재적으로 훨씬 비싸지만 설정하고 확장하기에는 용이합니다.

#### Heroku 작동 방법

Heroku는 하나 이상의 "[Dynos](https://devcenter.heroku.com/articles/dynos)"에서 Django 웹사이트를 실행합니다. 이 Dynos 웹사이트는 어플리케이션 실행에 필요한 환경을 제공하는 격리된 가상화 Unix 컨테이너입니다. dynos는 완전히 격리되어 있으며 임시 파일 시스템(dyno가 다시 시작될 때마다 정리되고 비워지는 짧은 수명의 파일 시스템)을 가지고 있습니다. dynos가 기본적으로 유일하게 공유하는 것은 어플리케이션 [configuration variables](https://devcenter.heroku.com/articles/config-vars)입니다. Heroku는 내부적으로 로드 밸런서를 사용하여 웹 트래픽을 모든 "웹" dyno에 분배합니다. 그들 사이에 아무 것도 공유되지 않으므로 Heroku는 더 많은 dynos를 추가하여 (물론 추가 연결을 허용하려면 데이터베이스를 확장해야 할 수도 있지만) 어플리케이션을 수평적으로 확장할 수 있습니다.

임시 파일 시스템이므로 어플리케이션에서 직접 요구하는 서비스(예: 데이터베이스, 대기열, 캐싱 시스템, 저장소, 전자 메일 서비스 등)를 설치할 수 없습니다. 대신 Heroku 웹 어플리케이션은 Heroku 또는 타사가 제공하는 독립적인 "add-on" 백업 서비스를 사용할 수 있습니다. 웹 어플리케이션에 연결되면 어플리케이션 구성 변수에 설정된 정보를 사용하여 dynos는 서비스를 접근합니다.

어플리케이션을 실행하려면 Heroku가 적절한 환경과 종속성을 설정할 수 있어야 하고 실행 방법을 이해할 수 있어야 합니다. Django 앱의 경우 이 정보를 텍스트 파일 형태로 제공합니다.

-	<b>runtime.txt</b>: 사용할 프로그래밍 언어와 그 버전
-	<b>requirements.txt</b>: Django를 포함한 Python 구성 요소 종속성.
-	<b>Procfile</b>: 웹 어플리케이션을 시작하기 위해 실행될 프로세스의 목록입니다. Django의 경우 대개 (<code>.wsgi</code> 스크립트를 사용하는) Gunicorn 웹 어플리케이션 서버가 됩니다.
-	<b>wsgi.py</b>: Heroku 환경에서 Django 어플리케이션을 호출하기 위한 [WSGI](http://wsgi.readthedocs.io/en/latest/what.html) 설정 파일.

개발자는 특별한 클라이언트 앱이나 terminal을 사용하여 Heroku와 상호 작용합니다. 이는 유닉스 bash 스크립트와 매우 유사합니다. 이를 통해 git 저장소에 저장된 코드를 업로드하고 실행 중인 프로세스를 검사하고 로그를 보고 구성 변수를 설정하는 등의 작업을 수행할 수 있습니다.

Heroku에서 동작하는 어플리케이션을 생성하려면 Django 웹 어플리케이션을 git 저장소에 저장하고, 위의 파일을 추가하고, 데이터베이스 애드온과 통합하고, 정적 파일을 적절히 처리하도록 변경해야 합니다.

일단 Heroku 계정을 생성한 다음 Heroku 클라이언트를 설치하여 웹사이트 설치에 사용할 수 있습니다.

> **Note**: 작성 시점에 Heroku를 사용하는 방법인 아래 지침을 반영하고 있습니다. Heroku가 프로세스를 크게 변경하면 설치 문서([Getting Started on Heroku with Django](https://devcenter.heroku.com/articles/getting-started-with-python#introduction))를 확인하는 것이 바람직 합니다.

여기까지 시작하기 위해 필요한 전체 개요입니다. 보다 포괄적인 가이드를 ([How Heroku works](https://devcenter.heroku.com/articles/how-heroku-works)에서 찾아볼 수 있습니다).

#### Github에 어플리케이션 리포지토리 생성

Heroku는 **git** 소스 코드 버전 제어 시스템과 밀접하게 통합되어 있으며 서비스 중인 시스템의 모든 변경 사항을 업로드하고 동기화합니다. Heroku 클라우드에서 소스 저장소를 가리키는 <i>heroku</i>라는 새로운 heroku "원격" 저장소를 추가하여 이 작업을 수행합니다. 개발하는 동안 git을 사용하여 변경 사항을 "마스터" 저장소에 저장합니다. 사이트를 배치하려면 변경 사항을 Heroku 저장소와 동기화하여야 합니다.

> **Note**: 바람직한 소프트웨어 개발 방법을 따르고 있다면 이미 git 또는 다른 SCM 시스템을 사용하고 있을 것입니다. 이미 git 저장소를 사용하고 있다면 이 단계를 건너 뛸 수 있습니다.

git으로 작업하는 여러 방법이 있지만, 가장 쉬운 방법 중 하나는 먼저 [Github](https://github.com/)에 계정을 개설하여 저장소를 만들고 로컬로 동기화하는 것입니다.

1.	[https://github.com/](https://github.com)을 방문하여 계정을 만드십시오.
2.	로그인한 다음 상단 툴바에서 **\+** 링크를 클릭하고 <b>New repository</b>를 선택하십시오.
3.	이 양식의 모든 입력란을 채웁니다. 필수 사항은 아니지만 적극 권장합니다.

	-	새 저장소 이름 (예: <i>django_local_library</i>)과 설명 (예: 'Django로 작성된 LocalLibrary 웹 사이트')을 입력하십시오.
	-	<i>Add .gitignore</i> 선택 목록에서 <b>Python</b>을 선택하십시오.
	-	<i>Add license</i> 선택 목록에서 원하는 라이센스를 선택하십시오.
	-	<b>Initialize this repository with a README</b>를 선택하십시오.

4.	<b>Create repository</b>를 누르십시오.

5.	새 Repo 페이지에서 녹색 <b>"Clone or download"</b> 버튼을 클릭하십시오.

6.	나타나는 대화 상자 안의 텍스트 필드에서 URL 값을 복사하십시오 (<b>https://github.com/<i><your_git_user_id></i>/django_local_library.git</b>와 같아야합니다).

이제 리포지토리( "repo")가 생성되어 로컬 컴퓨터에 복제하려고 합니다.

1) 로컬 컴퓨터용 <i>git</i>을 설치하십시오 (다른 플랫폼용 버전은 [여기](https://git-scm.com/downloads)에서 찾을 수 있습니다).

2) 명령 프롬프트 또는 터미널을 열고 위에서 복사한 URL을 사용하여 저장소를 복제하십시오. 그러면 현재 디렉토리 밑에 저장소가 생성됩니다.

```bash
  git clone https://github.com/<your_git_user_id>/django_local_library.git
```

3) 새 저장소로 이동합니다.

```bash
cd django_local_library
```

마지막 단계는 어플리케이션 코드를 복사한 다음 git을 사용하여 파일을 저장소에 추가하는 것입니다.

1) Django 어플리케이션(locallibrary 폴더를 **제외한** <b>manage.py</b>와 같거나 아래 레벨의 모든 파일)을 이 폴더에 복사합니다.

2) <b>.gitignore</b> 파일을 열고 아래 줄을 복사하여 파일 끝에 저장하십시오 (이 파일은 기본적으로 git에 업로드하지 말아야 하는 파일을 지정하는 데 사용됩니다).

```bash
# Text backup files
*.bak

# Database
*.sqlite3
```

3) 명령 프롬프트 또는 터미널을 열고 `add` 명령을 사용하여 git에 모든 파일을 추가하십시오.

```bash
git add -A
```

4) status 명령을 사용하여 추가하려는 모든 파일이 정확한 지 확인하십시오 (바이너리, 임시 파일 등이 아닌 원본 파일). 아래와 비슷하게 보일 것입니다.

```bash
> git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   .gitignore
        new file:   catalog/__init__.py
        ...
        new file:   catalog/migrations/0001_initial.py
        ...
        new file:   templates/registration/password_reset_form.html
```

5) 만족하면 파일을 로컬 저장소에 커밋하십시오.

```bash
git commit -m "First version of application moved into github"
```

6) 다음 명령을 사용하여 로컬 저장소를 Github 웹사이트와 동기화하십시오.

```bash
git push origin master
```

완료되면 Github에서 repo를 만든 페이지로 돌아가서 페이지를 새로 고침하여 전체 어플리케이션이 업로드되었는 지 확인할 수 있습니다. 이 add/commit/push 주기를 사용하여 파일이 변경될 때마다 로컬 저장소를 계속 업데이트할 수 있습니다.

> **Tip**: "바닐라" 프로젝트의 백업으로 만드는 것이 바람직합니다. 다음 섹션에서 예정하는 변경 사항 중 일부 플랫폼에서 배포(또는 개발)에 유용할 수 있지만 그렇지 않을 수도 있습니다.
>
> 가장 좋은 방법은 git을 사용하여 수정한 내용들을 관리하는 것입니다. git을 사용하면 특정 이전 버전으로 돌아갈 수 있을 뿐만아니라 프로덕션 변경 사항과 별도 "분기"를 유지 관리하고 변경 사항을 선택하여 프로덕션과 개발 분기간에 이동할 수 있습니다. [Learning Git](https://help.github.com/articles/good-resources-for-learning-git-and-github/)는 배울 가치가 있지만 주제의 범위를 벗어납니다.
>
> 가장 쉬운 방법은 파일을 다른 위치로 복사하는 것입니다. 그러나 git에 대한 지식과 가장 비슷한 방법을 사용하십시오.

#### Heroku에서 앱 갱신

이 섹션에서는 Heroku에서 LocalLibrary 어플리케이션이 동작되도록 필요한 변경 사항에 대해 설명합니다. Heroku의 [Heroku Getting Started with Django](https://devcenter.heroku.com/articles/getting-started-with-python#introduction) 지침은 Heroku 클라이언트를 사용하여 로컬 개발 환경을 실행한다고 가정하지만 여기서 변경된 사항들은 기존 Django 개발 서버에서 이미 배운 방법과 호환됩니다.

##### Procfile

어플리케이션의 프로세스 유형과 진입점을 선언하려면 GitHub 저장소의 루트에 (확장자 없는) `Procfile` 파일을 만듭니다. 다음 텍스트를 복사하십시오.

```
web: gunicorn locallibrary.wsgi --log-file -
```

"<code>web:</code>"은 Heroku에게 이는 웹 dyno이고 HTTP 트래픽을 보낼 수 있음을 알려줍니다. 이 dyno에서 시작하는 프로세스는 Heroku가 권장하고 널리 사용되는 웹 어플리케이션 서버인 gunicorn입니다. (어플리케이션 골조인 <b>/locallibrary/wsgi.py</b>와 같이 생성된) 모듈 <code>locallibrary.wsgi</code>의 설정 정보를 사용하여 Gunicorn을 시작합니다.

##### Gunicorn

[Gunicorn](http://gunicorn.org/)은 Heroku에서 Django와 함께 사용을 권장하는 HTTP 서버입니다 (위의 Procfile에서 참조). 단일 dyno에서 여러 Python 컨커런트(concurrent) 프로세스를 실행할 수 있는 WSGI 어플리케이션용 순수 Python HTTP 서버입니다 ([Deploying Python applications with Gunicorn](https://devcenter.heroku.com/articles/python-gunicorn)에서 자세한 내용을 참조하십시오).

Gunicorn은 개발 중에는 LocalLibrary 어플리케이션을 제공할 필요가 없지만 원격 서버에 Heroku를 설치하기 위한 [requirements](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment#requirements)이므로 설치하여야 합니다.

pip 명령를 사용하여 Gunicorn을 로컬에 설치\([setting up the development environment](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/development_environment))하십시오.

```bash
pip3 install gunicorn
```

##### 데이터베이스 설정

Heroku에서는 어플리케이션이 다시 시작될 때(일반적으로 하루에 한 번 또는 어플리케이션이나 구성 변수가 변경 될 때)마다 <i>임시</i> 파일 시스템이 삭제되기 때문에 파일 기반인 기본 SQLite 데이터베이스를 사용할 수 없습니다.

이런 상황을 처리하는 Heroku 메커니즘은 [database add-on](https://elements.heroku.com/addons#data-stores)을 사용하고 add-on에 의해 설정된 환경 [configuration variable](https://devcenter.heroku.com/articles/config-vars)의 정보를 사용하도록 웹 어플리케이션을 설정하는 것입니다. 여러 데이터베이스를 사용하는 옵션이 있지만 여기서는 Heroku postgres 데이터베이스를 [hooby tier](https://devcenter.heroku.com/articles/heroku-postgres-plans#plan-tiers)에서 사용할 것입니다. 이는 무료이며 Django를 지원하며 무료 hobby dyno 플랜 티어을 사용하면 자동으로 새로운 Heroku 어플리케이션에 추가됩니다.

데이터베이스 연결 정보는 <code>DATABASE_URL</code>이라는 설정 변수를 사용하여 웹 dyno에 전달됩니다. 이 정보를 Django에 하드 코딩하는 대신, 개발자는 [dj-database-url](https://warehouse.python.org/project/dj-database-url/) 패키지를 사용하여 DATABASE_URL 환경 변수를 분석하고 자동으로 Django 설정 형식으로 변환할 것을 권장합니다. <i>dj-database-url</i> 패키지를 설치하는 것외에도 Django는 Postgres 데이터베이스와 상호 작용하기 위해 [psyop2](http://initd.org/psycopg/)를 설치해야 합니다.

<b><u>dj-database-url (환경 변수에 Django 데이터베이스 설정)</u></b>

Heroku가 원격 서버에 설정하는 [requirements](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment#requirements)의 일부가 되도록 <i>dj-database-url</i>을 로컬에 설치하십시오.

```bash
$ pip3 install dj-database-url
```

<b><u>settings.py</u></b>

<b>/locallibrary/settings.py</b>를 열고 파일의 끝에 다음 설정을 복사하십시오.

```python
# Heroku: Update database configuration from $DATABASE_URL.
import dj_database_url
db_from_env = dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(db_from_env)
```

> **Note**: - 개발용 컴퓨터에서는 DATABASE_URL 환경 변수를 설정하지 않았으므로 개발 중에 SQLite를 계속 사용합니다.
>
> -	conn_max_age = 500 값은 연결을 영구적으로 만들어 주므로 모든 요청 주기마다 연결을 다시 작성하는 것보다 훨씬 효율적입니다. 그러나 이는 선택 사항이며 필요하다면 제거할 수 있습니다.

<b><u>psycopg2 (Python Postgres 데이터베이스 지원)</u></b>

Django에서 Postgres 데이터베이스를 사용하기 위해 psycopg2가 필요합니다. Heroku가 이 요구 사항을 원격 서버에 설정하는 [requirements.txt](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment#requirements)에 추가해야 합니다 (아래 요구 사항 섹션 참조).

Django는 로컬 환경에서 <code>DATABASE_URL</code> 환경 변수가 설정되어 있지 않으므로 기본적으로 로컬 SQLite 데이터베이스를 사용합니다. Postgres로 완전히 전환하고 개발과 프러덕션에서 Heroku 무료 티어 데이터베이스를 사용할 수 있습니다. 예를 들어 Linux 기반 시스템에 psycopg2와 그 종속성을 로컬에 설치하려면 아래 bash 또는 terminal 명령을 사용하십시오.

```bash
sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib
pip3 install psycopg2-binary
```

다른 플랫폼에 대한 설치 지침은 [psycopg2 website](http://initd.org/psycopg/docs/install.html)에서 찾을 수 있습니다.

그러나 이것을 수행할 필요는 없습니다. 요구 사항인 <code>requirements.txt</code> (아래 참조)에서 요구 사항을 Heroku에 제공한다면 개발용 로컬 컴퓨터에 PostgreSQL을 활성화할 필요는 없습니다.

##### 프로덕션에서 정적 파일 서비스

개발하는 동안 Django와 Django 개발 웹 서버를 사용하여 정적 파일(CSS, JavaScript 등)을 서비스했습니다. 프로덕션 환경에서는 대신 일반적으로 CDN (Content Delivery Network) 또는 웹 서버에서 정적 파일을 서비스합니다.

> **Note**: Django나 웹 어플리케이션을 통해 정적 파일을 제공하는 것은 비효율적입니다. 요청이 웹 서버 또는 완전히 별도의 CDN에서 직접 처리되기 보다는 불필요한 추가 코드 (Django)를 거쳐야하기 때문입니다. 개발 중 로컬 사용에는 문제가 되지 않지만 프로덕션에서 동일한 방식을 사용하는 경우 성능에 큰 영향을 미칩니다.

Django 웹 어플리케이션과 별도로 정적 파일을 쉽게 호스트할 수 있도록 하기 위해 Django는 배치를 위하여 이러한 파일을 수집하는 <i>collectstatic</i> 도구를 제공합니다 (<i>collectstatic</i>이 실행될 때 파일을 수집해야 하는 위치를 정의하는 설정 변수가 있습니다). Django 템플리트는 설정 변수(<code>STATIC_URL</code>)를 기준으로 정적 파일의 호스팅 위치를 참조하므로 정적 파일을 다른 호스트나 서버로 이동하면 이 위치를 변경해야 합니다.

관련 설정 변수는 다음과 같습니다.

-	<code>STATIC_URL</code>: 정적 파일이 저장된 기본 URL 위치입니다 (예: CDN). 이것은 기본 템플리트에서 액세스되는 정적 템플리트 변수로 사용됩니다 ([Django 튜토리얼 5부: 홈 페이지 만들기](homePage.md) 참조).
-	<code>STATIC_ROOT</code>: Django의 "collectstatic" 도구가 템플리트에서 참조하는 정적 파일을 수집할 디렉토리의 절대 경로입니다. 일단 수집되면 파일을 호스팅할 위치에 그룹으로 업로드할 수 있습니다.
-	<code>STATICFILES_DIRS</code> : Django의 collectstatic 도구가 정적 파일을 검색해야 하는 추가 디렉토리를 나열합니다.

<b><u>settings.py</u></b>

<b>/locallibrary/settings.py</b>를 열고 파일 끝에 설정을 복사하십시오. <code>BASE_DIR</code>은 이미 파일에 정의되어 있어야 합니다 (<code>STATIC_URL</code>은 파일을 만들 때 이미 정의되었을 수 있습니다. 문제가 없지만 이전 중복 참조를 삭제할 수 있습니다).

```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/2.1/howto/static-files/

# The absolute path to the directory where collectstatic will collect static files for deployment.
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

# The URL to use when referring to static files (where they will be served from)
STATIC_URL = '/static/'
```

실제로는 [WhiteNoise](https://warehouse.python.org/project/whitenoise/)라는 라이브러리를 사용하여 파일을 서비스할 것입니다. 이 라이브러리는 다음 섹션에서 설치하고 구성합니다.

자세한 내용은 [Django and Static Assets](https://devcenter.heroku.com/articles/django-assets) (Heroku docs)을 참조하십시오.

##### Whitenoise

프로덕션에서는 다양한 방법으로 정적 파일을 서비스합니다 (이전 섹션에서 Django 관련 설정을 보았습니다). Heroku는 프로덕션에서 Gunicorn에서 직접 정적 자산을 제공하는 [WhiteNoise](https://warehouse.python.org/project/whitenoise/) 프로젝트를 사용할 것을 권장합니다.

> **Note**: Heroku는 자동으로 collectstatic을 호출하고 어플리케이션을 업로드한 후 WhiteNoise에서 사용할 정적 파일을 준비합니다. WhiteNoise가 어떻게 작동하는지 그리고 이러한 파일의 서비스를 비교적 효율적으로 어떻게 구현되었는 지를 설명하는 [WhiteNoise](https://warehouse.python.org/project/whitenoise/) 문서를 확인하십시오.

프로젝트와 함께 사용할 WhiteNoise를 설정하는 단계는 [여기](http://whitenoise.evans.io/en/stable/django.html)에 있습니다 (아래에서 재현합니다).

<b><u>WhiteNoise</u></b>

다음 명령을 사용하여 로컬에 whitenoise를 설치하십시오.

```bash
$ pip3 install whitenoise
```

<b><u>setting.py</u></b>

Django 어플리케이션에 <i>WhiteNoise</i>를 설치하려면 <b>/locallibrary/settings.py</b>를 열고 `MIDDLEWARE` 설정을 찾아 `SecurityMiddleware` 바로 아래에 `WhiteNoiseMiddleware`를 추가하십시오

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

선택적으로, 정적 파일이 서비스될 때 (보다 효율적으로) 정적 파일의 크기를 줄일 수 있습니다. <b>/locallibrary/settings.py</b> 끝에 다음을 추가하십시오.

```python
# Simplified static file serving.
# https://warehouse.python.org/project/whitenoise/
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

##### 요구 사항

웹 어플리케이션의 파이썬 요구 사항을 저장소의 루트에 <b>requirements.txt</b> 파일에 저장해야 합니다. 다음 Heroku는 환경을 다시 작성할 때 이를 자동으로 설치합니다. 명령에서 pip를 사용하여 이 파일을 만들 수 있습니다 (repo 루트에서 다음을 실행합니다).

```bash
pip3 freeze > requirements.txt
```

위의 모든 종속성을 설치한 후 <b>requirements.txt</b> 파일의 버전 번호가 다를 수 있지만 나열된 항목들은 최소한 있어야 합니다. 이 어플리케이션을 위하여 명시적으로 추가하지 않았다면 아래에 나열되지 않은 다른 종속성들을 삭제하십시오.

```
dj-database-url==0.5.0
Django==2.1.5
gunicorn==19.9.0
psycopg2-binary==2.7.7
whitenoise==4.1.2
```

> 위와 같이 <b>psycopg2</b>이 있는지 확인하십시오. 이 프로그램을 로컬에 설치하지 않은 경우에도 <b>requirements.txt</b>에는 추가해야 합니다.

##### 런타임

<b>runtime.txt</b> 파일이 정의된 경우 Heroku에게 사용할 프로그래밍 언어를 알려줍니다. repo의 루트에 파일을 만들고 다음 텍스트를 추가하십시오.

```
python-3.7.0
```

> **Note**: Heroku는 소수의 [Python runtime](https://devcenter.heroku.com/articles/python-support#supported-python-runtimes)만 지원합니다 (작성 당시에는 위의 것이 포함되었습니다). Heroku는 파일에 지정된 값과 상관없이 지원되는 런타임을 사용합니다.

##### Github에 변경 저장과 재테스트

다음으로 모든 변경 사항을 Github에 저장합니다. 터미널에서 (해당 저장소에 있다면) 다음 명령을 입력하십시오.

```bash
git add -A
git commit -m "Added files and changes required for deployment to heroku"
git push origin master
```

더 진행하기 전에 사이트를 로컬에서 다시 테스트하고 위의 변경 사항이 영향을 받지 않았는지 확인하십시오. 평소대로 개발 웹 서버를 실행한 다음 브라우저에서 예상대로 사이트가 계속 작동하는 지 확인하십시오.

```bash
python3 manage.py runserver
```

이제 Heroku에서 LocalLibrary를 배포할 준비가 되었습니다.

#### Heroku 계정 생성

Heroku를 사용하려면 먼저 계정을 만들어야합니다.

-	[www.heroku.com](https://www.heroku.com/)에서 <b>SIGN UP FOR FREE</b> 버튼을 클릭하십시오.
-	세부 정보를 입력한 다음 CREATE FREE ACCOUNT를 누릅니다. 계정에 가입 이메일 확인을 요청합니다.
-	가입 이메일에서 계정 활성화 링크를 클릭하십시오. 웹 브라우저에서 귀하의 계정으로 다시 연결됩니다.
-	암호를 입력하고 SET PASSWORD AND LOGIN을 클릭하십시오.
-	다음 로그인하면 [Heroku dashboard](https://dashboard.heroku.com/apps)로 이동합니다.

#### 클라이언트 설치

[instructions on Heroku](https://devcenter.heroku.com/articles/getting-started-with-python#set-up)에 따라 Heroku 클라이언트를 다운로드하고 설치하십시오.

클라이언트가 설치되면 명령을 실행할 수 있습니다. 예를 들어 클라이언트에 대한 도움말을 얻으려면 아래과 같습니다.

```bash
heroku help
```

#### 웹사이트 생성과 업로드

앱을 만들기 위해 저장소의 루트 디렉토리에서 "create" 명령을 실행합니다. 이로부터 로컬 git 환경에서 <i>heroku</i>라는 git remote("원격 저장소에 대한 포인터")를 생성합니다.

```bash
heroku create
```

> **Note**: "create" 다음에 값을 지정하여 원하는 git 리모트의 이름을 지정할 수 있습니다. 지정하지 않으면 임의의 이름을 얻을 것입니다. 이름은 기본 URL에서 사용됩니다.

다음 아래와 같이 Heroku 저장소에 앱을 푸시할 수 있습니다. 그러면 앱이 업로드되고, dyno에 패키지되며, collectstatic가 실행되고 사이트가 시작됩니다.

```bash
git push heroku master
```

운이 좋다면 어플리케이션이 사이트에서 "실행 중"이지만 어플리케이션에서 사용할 데이터베이스 테이블을 설정하지 않았으므로 제대로 작동하지 않습니다. 이를 위해서 heroku 실행 명령을 사용하고 마이그레이션 작업을 수행하기 위해 "one off dyno"를 시작해야합니다. 터미널에 다음 명령을 입력하십시오.

```bash
heroku run python manage.py migrate
```

또한 책과 저자를 추가할 수 있어야 하므로 일회용 dyno를 사용하여 관리 수퍼 유저도 생성합니다.

```bash
heroku run python manage.py createsuperuser
```

이 작업이 완료되면 사이트를 볼 수 있습니다. 책이 아직 없어도 작동할 것입니다. 브라우저로 새 웹사이트를 열려면 다음 명령을 사용하십시오.

```bash
heroku open
```

관리 사이트에서 책를 만들고 사이트가 예상대로 작동하는 지 확인하십시오.

#### addon 관리

<code>heroku addons</code> 명령을 사용하여 앱의 부가 기능을 확인할 수 있습니다. 그러면 모든 add-on과 그 가격 티어와 상태가 나열됩니다.

```bash
> heroku addons

Add-on                                     Plan       Price  State
─────────────────────────────────────────  ─────────  ─────  ───────
heroku-postgresql (postgresql-flat-26536)  hobby-dev  free   created
 └─ as DATABASE
```

여기서 add-on 기능 하나로 postgres SQL 데이터베이스가 있음을 알 수 있습니다. 이것은 무료이며 앱을 만들 때 자동으로 만들어졌습니다. 다음 명령을 사용하여 웹 페이지를 열어 데이터베이스 add-on(또는 기타 add-on)을 보다 자세하게 살펴볼 수 있습니다.

```bash
heroku addons:open heroku-postgresql
```

(열기와 유사한 구문으로) 다른 명령을 사용하여 add-on을 작성, 삭제, 업그레이드 및 다운 그레이드 할 수 있습니다. 자세한 내용은 [Managing Add-ons](https://devcenter.heroku.com/articles/managing-add-ons) (Heroku docs)를 참조하십시오.

#### 구성 변수 설정

`heroku config` 명령을 사용하여 사이트의 설정 변수를 확인할 수 있습니다. 아래에서 데이터베이스를 설정하는 데 사용되는 <code>DATABASE_URL</code> 변수가 하나만 있음을 알 수 있습니다.

```bash
> heroku config

=== locallibrary Config Vars
DATABASE_URL: postgres://uzfnbcyxidzgrl:j2jkUFDF6OGGqxkgg7Hk3ilbZI@ec2-54-243-201-144.compute-1.amazonaws.com:5432/dbftm4qgh3kda3
```

[웹사이트 공개 준비](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/Deployment#Getting_your_website_ready_to_publish) 섹션을 기억해 보면 환경 변수 <code>DJANGO_SECRET_KEY</code>와 <code>DJANGO_DEBUG</code>를 설정해야 합니다.

> **Note**: 비밀 키는 정말 비밀스러워야 합니다. 새 키를 생성하는 한 방법은 [Django Secret Key Generator](https://www.miniwebtool.com/django-secret-key-generator/)를 사용하는 것입니다.

<code>config:set</code> 명령을 사용하여 (아래 처럼) <code>DJANGO_SECRET_KEY</code>를 설정합니다. 자신의 비밀 키를 사용하는 것을 잊지 마십시오.

```
> heroku config:set DJANGO_SECRET_KEY='eu09(ilk6@4sfdofb=b_2ht@vad*$ehh9-)3u_83+y%(+phh&='

Setting DJANGO_SECRET_KEY and restarting locallibrary... done, v7
DJANGO_SECRET_KEY: eu09(ilk6@4sfdofb=b_2ht@vad*$ehh9-)3u_83+y%(+phh
```

유사하게 <code>DJANGO_DEBUG</code>를 설정합니다.

```bash
> heroku config:set DJANGO_DEBUG=

Setting DJANGO_DEBUG and restarting locallibrary... done, v8
```

<code>DEBUG=False</code> (보안 조치)인 경우 [ALLOWED_HOSTS](https://docs.djangoproject.com/en/2.1/ref/settings/#allowed-hosts) 설정이 <i>필요</i>하므로 사이트에 지금 방문하면 "Bad request" 오류가 발생합니다. /<b>locallibrary/settings.py</b>를 열고 <code>ALLOWED_HOSTS</code> 설정을 기본 앱 url (예: 'locallibrary1234.herokuapp.com')과 로컬 개발 서버에서 일반적으로 사용하는 URL을 포함하도록 변경하십시오.

```python
ALLOWED_HOSTS = ['<your app URL without the https:// prefix>.herokuapp.com','127.0.0.1']
# For example:
# ALLOWED_HOSTS = ['fathomless-scrubland-30645.herokuapp.com', '127.0.0.1']
```

다음 설정을 저장하고 Github 저장소와 Heroku에 커밋하십시오.

```bash
git add -A
git commit -m 'Update ALLOWED_HOSTS with site and development server URL'
git push origin master
git push heroku master
```

> Heroku에 사이트 업데이트를 완료하면 존재하지 않는 URL (예 : /catalog/doesnotexist/)을 입력하십시오. 이전에는 자세한 디버그 페이지가 디스플레이되었지만 이제는 간단한 "Not Found" 페이지만 보여집니다.

#### 디버깅

Heroku 클라이언트는 디버깅을 위한 몇 가지 도구를 제공합니다.

```bash
# Show current logs
heroku logs

# Show current logs and keep updating with any new results
heroku logs --tail

# Add additional logging for collectstatic (this tool is run automatically during a build)
heroku config:set DEBUG_COLLECTSTATIC=1

# Display dyno status
heroku ps
```

이들이 제공할 수 있는 것보다 더 많은 정보가 필요하다면 Django Logging을 살펴 봐야 할 것입니다.

---

### 요약

Django를 프로덕션 환경으로 설정하는 방법에 대한 튜토리얼과 Django에 대한 일련의 튜토리얼이 끝났습니다. 도움이 되었으면 합니다. [여기 Github](https://github.com/mdn/django-locallibrary-tutorial)에서 소스 코드의 버전 전체를 확인할 수 있습니다.

다음 단계는 마지막 몇몇 문서를 읽고 평가 작업을 완료하는 것입니다 (작업 중).

---

### 참고 문서

-	[Deploying Django](https://docs.djangoproject.com/en/2.1/howto/deployment/) (Django docs)
	-	[Deployment checklist](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) (Django docs)
	-	[Deploying static files](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) (Django docs)
	-	[How to deploy with WSGI](https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/) (Django docs)
	-	[How to use Django with Apache and mod_wsgi](https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/modwsgi/) (Django docs)
	-	[How to use Django with Gunicorn](https://docs.djangoproject.com/en/2.1/howto/deployment/wsgi/gunicorn/) (Django docs)
-	Heroku
	-	[Configuring Django apps for Heroku](https://devcenter.heroku.com/articles/django-app-configuration) (Heroku docs)
	-	[Getting Started on Heroku with Django ](https://devcenter.heroku.com/articles/getting-started-with-python#introduction) (Heroku docs)
	-	[Django and Static Assets](https://devcenter.heroku.com/articles/django-assets) (Heroku docs)
	-	[Concurrency and Database Connections in Django](https://devcenter.heroku.com/articles/python-concurrency-and-database-connections) (Heroku docs)
	-	[How Heroku works](https://devcenter.heroku.com/articles/how-heroku-works) (Heroku docs)
	-	[Dynos and the Dyno Manager](https://devcenter.heroku.com/articles/dynos) (Heroku docs)
	-	[Configuration and Config Vars](https://devcenter.heroku.com/articles/config-vars) (Heroku docs)
	-	[Limits](https://devcenter.heroku.com/articles/limits) (Heroku docs)
	-	[Deploying Python applications with Gunicorn](https://devcenter.heroku.com/articles/python-gunicorn) (Heroku docs)
	-	[Deploying Python and Django apps on Heroku](https://devcenter.heroku.com/articles/deploying-python) (Heroku docs)
	-	[Other Heroku Django docs](https://devcenter.heroku.com/search?q=django)
-	Digital Ocean
	-	[How To Serve Django Applications with uWSGI and Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-ubuntu-16-04)
	-	[Other Digital Ocean Django community docs](https://www.digitalocean.com/community/tutorials?q=django)

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
-	[Django 웹 어플리케이션 보안](webApplicationSecurity.md)
