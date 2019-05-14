Django 개발 환경 설정
---------------------

---

이제 Django를 개략적으로 알았습니다. Windows, Linux (Ubuntu) 및 macOS에서 Django 개발 환경을 설정하고 테스트하는 방법을 설명합니다. 어떤 일반적인 운영 체제에서든 이 단계를 통하여 Django 앱을 개발할 수 있어야 합니다.

> 선수지식: 터미널에서 CLI 사용 방법을 알아야 합니다. 또한 개발 컴퓨터의 운영 체제에 소프트웨어 패키지를 설치하는 방법을 알고 있어야 합니다.
>
> 목표: 컴퓨터에서 실행되는 Django (2.0) 개발 환경 설치.

---

### Django 개발환경 개요

웹 어플리케이션 개발을 시작할 수 있도록 Django는 개발 환경을 컴퓨터에 쉽게 설치할 수 있습니다. 이 섹션에서는 개발 환경에서 얻을 수 있는 것을 설명하고 설치와 설정 옵션에 대한 개요를 설명합니다. 이 단원의 끝 부분에서 Ubuntu, macOS 및 Windows에 Django 개발 환경을 설치하는 방법과 이를 테스트하는 방법에 대해 설명합니다.

#### Django 개발환경이란?

Django 앱을 프로덕션 환경으로 배치하기 전에 개발하고 테스트를 위하여 사용할 수 있도록 로컬 컴퓨터에 Django를 설치하는 것이 곧 개발 환경입니다.

Django 자체가 제공하는 주요 도구는 웹 브라우저에서 로컬(즉, 외부 웹서버가 아닌 로컬 컴퓨터) Django 웹 어플리케이션 테스트를 위하여 사용할 수 있는 간단한 <i>개발 웹서버</i>와 함께 Django 프로젝트를 만들고 작업하기 위한 Python 스크립트 세트입니다.

코드 편집을 위한 [텍스트 편집기](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/Available_text_editors) 또는 IDE, 코드의 버전을 관리하기 위한 [Git](https://git-scm.com/)과 같은 버전 관리 도구 등 개발 환경의 일부를 구성하는 주변 도구들은 여기서 다루지 않을 것입니다. 또한 개발 컴퓨터에 이미 텍스트 편집기가 설치되어 있다고 가정하고 시작합니다.

#### Django 설치 옵션

설치와 설정할 수 있는 방법과 플랫폼 관점에서 Django는 매우 유연합니다.

-	다양한 운영 체제에 설치할 수 있습니다.
-	소스, Python 패키지 색인 (PyPi) 및 대부분 호스트 컴퓨터의 패키지 관리자 어플리케이션에서 설치할 수 있습니다.
-	여러 데이터베이스 중 하나를 사용하지만 그 설치와 설정을 데이터베이스에 따라 할 수 있습니다.
-	메인 시스템 Python 환경 또는 별도의 Python 가상 환경에서 실행할 수 있습니다.

이 옵션들 각각은 약간 다른 설정과 설치를 필요로합니다. 다음에서 몇몇 옵션을 설명합니다. 이후 샘플 운영체제에서 Django를 설치하는 방법을 보이며 이후에서 이렇게 설치되었다고 가정합니다.

> **Note**: 가능한 다른 설치 옵션은 공식 Django 문서에서 다룹니다. 아래에서 [적절한 해당 문서](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/development_environment#)를 링크할 것입니다.

##### 지원 운영체제

Python 3 프로그래밍 언어를 실행할 수있는 거의 모든 컴퓨터(Windows, macOS, Linux / Unix, Solaris 등)에서 Django 웹 어플리케이션을 실행할 수 있습니다. 개발하는 동안 Django를 실행하기 위하여 사용할 컴퓨터는 적절한 성능을 가져야 합니다.

이 단원에서는 Windows, macOS 및 Linux 또는 Unix에 대한 지침을 제공합니다.

##### 사용 Python 버젼

가장 최신 버전을 사용하는 것을 권장합니다. 이 글을 번역하는 시점에서 최신 버전은 Python 3.7.2입니다.

필요하다면, Python 3.5 이상을 사용할 수 있습니다 (그러나 이후 릴리스에서 Python 3.5 지원이 중단될 수 있습니다).

> **Note**: Django 2.1에 Python 2.7을 사용할 수 없습니다 (Django 1.11.x 시리즈가 Python 2.7을 마지막으로 지원합니다).

##### Django 다운로드 사이트

세 사이트에서 Django를 다운로드받을 수 있습니다.

-	<i>pip</i> 도구를 사용하여 Python 패키지 저장소 (PyPi)로 부터 Django의 안정된 최신 버전을 얻는 것이 가장 좋은 방법입니다.
-	컴퓨터 패키지 관리자의 버전을 사용하여 운영체제에 번들로 제공되는 Django 배포판은 친숙한 설치 메커니즘을 제공합니다. 그러나 패키지된 버전은 최신 버전이 아닐 수 있으며, 시스템의 Python 환경에만 설치될 수 있습니다 (원하는 버전이 아닐 수도 있음).
-	Django의 가장 최신 버전을 다운로드받아 설치할 수 있습니다. 초보자에게 이 방법을 권장하지 않지만 Django 자체에 기여할 준비가 되어 있다면 바람직합니다.

이 단원에서는 PyPi으 부터 Django를 설치하여 안정된 최신 버전을 얻는 방법을 보입니다.

##### 데이터베이스

Django는 4 개의 메인 데이터베이스 (PostgreSQL, MySQL, Oracle 및 SQLite)를 지원하며 대중적인 여러 SQL과 NoSQL 데이터베이스에 대해 다양한 수준의 지원을 제공하는 커뮤니티 라이브러리가 있습니다. 프로덕션과 개발을 위해 동일한 데이터베이스를 선택하는 것을 권장합니다. (Django는 ORM (Object-Relational Mapper)을 사용하여 여러 데이터베이스 차이점을 추상화하지만 아직 잠재적인 문제가 있어 피할 수 있다면 피하는 것이 바람직합니다.)

이 단원(그리고 이 튜토리얼 대부분)에서는 데이터를 파일에 저장하는 SQLite 데이터베이스를 사용합니다. SQLite는 데이터베이스를 가볍게 사용하기 위한 DBMS로, 높은 수준의 동시성을 지원할 수 없습니다. 그러나 주로 읽기 전용 어플리케이션에서는 탁월한 선택입니다.

> **Note**: Django는 표준 도구(<i>django-admin</i>)를 사용하여 웹사이트 프로젝트를 시작할 때 기본적으로 SQLite를 사용하도록 구성됩니다. 추가 구성이나 설정이 필요하지 않으므로 처음 시작하기에 적합합니다.

##### 시스템 또는 파이썬 가상 환경에 설치?

Python3를 설치하면 모든 Python3 코드가 공유하는 단일 글로벌 환경을 갖습니다. 원하는 특정 환경을 지원하는 Python 패키지를 설치할 수는 있지만 한 번에 하나의 특정 패키지 버전만을 설치할 수 있습니다.

> **Note**: 글로벌 환경에서 설치한 Python 어플리케이션은 잠재적으로 서로 충돌(즉, 동일한 패키지의 서로 다른 버전에 있는 경우)할 수 있습니다.

Django를 기본 즉 전역 환경으로 설치하면 컴퓨터에서 한 버전의 Django만 대상으로 할 수 있습니다. 이전 버전에 의존하는 웹 사이트를 유지하면서 새로운 웹사이트(Django의 최신 버전 사용)를 개발하는 경우 문제가 발생할 수 있습니다.

결과적으로 숙련된 Python/Django 개발자는 일반적으로 독립적인 <i>Python 가상 환경</i>에서 Python 어플리케이션을 실행합니다. 이는 하나의 컴퓨터에서 여러 다른 버전의 Django 환경을 가능하게 합니다. Django 개발팀은 Python 가상 환경을 사용할 것을 권장합니다!

이 튜토리얼에서는 Django를 가상 환경에 설치했다고 가정합니다. 그 방법을 아래에서 보입니다.

---

### Python 3 설치

Django를 사용하려면 운영체제에 Python을 설치해야 합니다. <i>Python 3</i>를 사용하려 한다면 Django와 다른 Python 어플리케이션에서 사용하는 Python 패키지/라이브러리 관리(설치, 업데이트 및 제거)에 사용하는 [Python Package Index](https://pypi.python.org/pypi) 도구 <i>pip3</i>이 필요합니다.

이 섹션에서는 Ubuntu Linux 18.04, macOS 및 Windows 10에 대해 Python의 버전을 확인하고 필요에 따라 새 버전을 설치하는 방법을 간략하게 설명합니다.

> **note**: 플랫폼에 따라 운영체제 자체 패키지 관리자 또는 다른 메커니즘을 통해 Python/pip를 설치할 수도 있습니다. 대부분의 플랫폼에서 [https://www.python.org/downloads/](https://www.python.org/downloads/)에서 필요한 설치 파일을 다운로드받고, 적절한 플랫폼별 방법을 사용하여 설치할 수 있습니다.

#### Ubuntu 18.04

Ubuntu Linux 18.04 LTS는 기본적으로 Python 3.6.6을 포함하고 있습니다. bash 터미널에서 다음 명령을 실행하여 이를 확인할 수 있습니다.

```bash
python3 -V
 Python 3.6.6
```

그러나 Python 3 (Django 포함) 패키지를 설치하는 데 필요한 Python Package Index 도구는 기본적으로 <b>사용할 수 없습니다</b>. 다음을 사용하여 bash 터미널에 pip3을 설치할 수 있습니다.

```bash
sudo apt install python3-pip
```

#### macOS

macOS "El Capitan"과 다른 최신 버전에는 Python 3이 포함되어 있지 않습니다. bash 터미널에서 다음 명령을 실행하여 이를 확인할 수 있습니다.

```bash
python3 -V
 -bash: python3: command not found
```

[python.org](https://www.python.org/)에서 (<i>pip3</i> 도구와 함께) Python 3를 쉽게 설치할 수 있습니다.

1.	필수 설치 프로그램을 다운로드하십시오.
	1.	[https://www.python.org/downloads/](https://www.python.org/downloads/)로 이동하십시오.
	2.	<b>Download Python 3.7.2</b> 버튼을 선택하십시오 (정확한 마이너 버전 번호는 다를 수 있음).
2.	<i>Finder</i>를 사용하여 파일을 찾은 다음 패키지 파일을 두 번 클릭합니다. 설치 후 프롬프트가 표시됩니다.

이제 아래와 같이 <i>Python 3</i> 버전을 검사하여 성공적으로 설치되었는 가를 확인할 수 있습니다.

```bash
python3 -V
 Python 3.7.2
```

마찬가지로 사용 가능한 패키지를 리스팅함으로써 <i>pip3</i> 설치를 확인할 수 있습니다.

```bash
pip3 list
```

#### Windows 10

Windows는 기본적으로 Python을 포함하지 않지만 [python.org](https://www.python.org/)에서 <i>pip3</i> 도구와 함께 쉽게 설치할 수 있습니다.

1.	필수 설치 프로그램을 다운로드하십시오.
	1.	[https://www.python.org/downloads/](https://www.python.org/downloads/)로 이동하십시오.
	2.	<b>Download Python 3.7.2</b> 버튼을 선택하십시오 (정확한 마이너 버전 번호는 다를 수 있음).
2.	다운로드한 파일을 두 번 클릭하고 설치 프롬프트에 따라 Python을 설치하십시오.
3.	"Add Python to PATH"상자 선택을 확인하십시오.

다음 명령 프롬프트에서 아래 텍스트를 입력하여 Python 3 설치를 확인할 수 있습니다.

```bash
py -3 -V
 Python 3.7.2
```

Windows 설치 프로그램은 기본적으로 <i>pip3</i> (Python 패키지 관리자)와 통합되어 있습니다. 다음과 같이 설치된 패키지를 나열 할 수 있습니다.

```bash
pip3 list
```

> **note**: 설치 관리자는 위 명령을 실행하는 데 필요한 모든 것을 설정해야 합니다. 그러나 Python을 찾을 수 없다는 메시지가 나타나면 시스템 경로에 추가하여야 합니다. 설치 프로그램을 다시 실행하고 "Modify"을 선택한 다음 두 번째 페이지의 "Add Python to environment variables"라고 표시된 상자를 선택하여 이 작업을 수행할 수 있습니다.

---

### Python 가상 환경에서 Django 사용하기

가상 환경을 만드는 데 사용할 라이브러리는 [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/index.html) (Linux 및 macOS)와 [virtualenvwrapper-win](https://pypi.python.org/pypi/virtualenvwrapper-win) (Windows)이며, 둘 모두 [virtualenv](https://developer.mozilla.org/en-US/docs/Python/Virtualenv) 도구를 사용합니다. 래퍼 도구는 모든 플랫폼에서 인터페이스를 관리하기 위한 일관된 인터페이스를 생성합니다.

#### 가상 환경 소프트웨어 설치

##### Ubuntu 가상 환경 설정

Python과 pip을 설치한 후, <i>virtualenvwrapper</i>\(<i>virtualenv</i> 포함)를 설치할 수 있습니다. 공식 설치 안내서는 [여기](http://virtualenvwrapper.readthedocs.io/en/latest/install.html) 또는 아래 지침을 참조하십시오.

<i>pip3</i>을 사용하여 도구를 설치하십시오.

```bash
sudo pip3 install virtualenvwrapper
```

다음 쉘 시작 파일(사용자 홈 디렉토리의 숨겨진 파일 <b>.bashrc</b>)의 끝에 아래 코드를 추가하십시오. 가상 환경이 있어야 하는 위치, 개발 프로젝트 디렉토리의 위치와 이 패키지가 함께 설치된 스크립트의 위치를 설정합니다.

```bash
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export VIRTUALENVWRAPPER_VIRTUALENV_ARGS=' -p /usr/bin/python3 '
export PROJECT_HOME=$HOME/Devel
source /usr/local/bin/virtualenvwrapper.sh
```

> **note**: <code>VIRTUALENVWRAPPER_PYTHON</code> 및 <code>VIRTUALENVWRAPPER_VIRTUALENV_ARGS</code> 변수는 Python3이 설치된 통상적인 경로를 가리키며 <code>source /usr/local/bin/virtualenvwrapper.sh</code>는 <code>virtualenvwrapper.sh</code> 스크립트의 통상적인 경로를 가리킵니다. 테스트할 때 <i>virtualenv</i>가 작동하지 않으면 Python과 스크립트가 지정한 경로에 있는 지 확인한 다음 시작 파일을 적절히 변경하십시오.
>
> <code>which virtualenvwrapper.sh</code>와 <code>which python3</code> 명령을 사용하여 시스템에 저장된 정확한 경로를 찾을 수 있습니다.

다음 터미널에서 아래 명령을 실행하여 시작 파일을 다시 로드하십시오.

```bash
source ~/.bashrc
```

터미널에서 다음과 같이 많은 스크립트가 실행되는 것을 볼 수 있습니다.

```bash
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/premkproject
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/postmkproject
...
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/preactivate
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/postactivate
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/get_env_details
```

이제 <i>mkvirtualenv</i> 명령을 사용하여 새로운 가상 환경을 만들 수 있습니다.

##### macOS 가상 환경 설정

macOS에서 <i>virtualenvwrapper</i>를 설정하는 것은 Ubuntu에서와 거의 같습니다 (다시, [공식 설치 안내서](http://virtualenvwrapper.readthedocs.io/en/latest/install.html) 또는 아래 지침을 따라 합니다).

아래와 같이 <i>pip3</i>를 사용하여 <i>virtualenvwrapper</i> (및 <i>virtualenv</i>를 묶어) 같이 설치합니다.

```bash
sudo pip3 install virtualenvwrapper
```

다음 쉘 시작 파일 끝에 다음 행을 추가합니다.

```bash
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export PROJECT_HOME=$HOME/Devel
source /usr/local/bin/virtualenvwrapper.sh
```

> **note**: <code>VIRTUALENVWRAPPER_PYTHON</code> 변수는 Python3의 통상적인 설치 경로를 가리키며 <code>source /usr/local/bin/virtualenvwrapper.sh</code>는 <code>virtualenvwrapper.sh</code> 스크립트의 통상적인 경로를 가리 킵니다. 테스트할 때 <i>virtualenv</i>가 작동하지 않으면 Python과 스크립트가 지정한 경로에 있는 지 확인한 다음 시작 파일을 적절히 변경하십시오.
>
> 예를 들어, macOS에 대한 하나의 설치 테스트는 시작 파일에 다음과 같은 스크립트가 필요합니다.
>
> ```bash
> export WORKON_HOME=$HOME/.virtualenvs
> export VIRTUALENVWRAPPER_PYTHON=/Library/Frameworks/Python.framework/Versions/3.7/bin/python3
> export PROJECT_HOME=$HOME/Devel
> source /Library/Frameworks/Python.framework/Versions/3.7/bin/virtualenvwrapper.sh
> ```
>
> <code>which virtualenvwrapper.sh</code>와 <code>which python3</code> 명령을 사용하여 시스템에 저장된 정확한 경로를 찾을 수 있습니다.

Ubuntu에서와 거의 같지만, 시작 파일은 다르게 홈 디렉토리에 숨겨진 파일 <b>.bash_profile</b>입니다.

> **note**: Finder에서 편집 할 <b>.bash_profile</b>을 찾을 수 없으면 nano를 사용하여 터미널에서이 파일을 열 수 있습니다.
>
> 명령은 다음과 같습니다.
>
> ```bash
> cd ~  # Navigate to my home directory
> ls -la #List the content of the directory. You should see .bash_profile
> nano .bash_profile # Open the file in the nano text editor, within the terminal
> # Scroll to the end of the file, and copy in the lines above
> # Use Ctrl+X to exit nano, Choose Y to save the file.
> ```

다음 터미널에서 다음과 같은 명령으로 시작 파일을 다시 수행합니다.

```bash
source ~/.bash_profile
```

결과로 많은 스크립트가 실행되는 것을 볼 수 있습니다 (Ubuntu 설치와 동일한 스크립트). 이제 <i>mkvirtualenv</i> 명령을 사용하여 새로운 가상 환경을 만들 수 있습니다.

##### Windows 가상 환경 설정

[virtualenvwrapper-win](https://pypi.python.org/pypi/virtualenvwrapper-win)을 설치하는 것은 도구가 가상 환경 정보를 저장하는 경로를 지정할 필요가 없으므로 <i>virtualenvwrapper</i>를 설정하는 것보다 간단합니다 (기본값이 있음). 명령 프롬프트에서 다음 명령을 실행하기만 하면 됩니다.

```bash
pip3 install virtualenvwrapper-win
```

이제 <code>mkvirtualenv</code> 명령을 사용하여 새로운 가상 환경을 만들 수 있습니다.

#### 가상 환경 생성

<i>virtualenvwrapper</i> 또는 <i>virtualenvwrapper-win</i>을 설치하면 모든 플랫폼 상의 가상 환경에서 작업은 매우 유사합니다.

이제 <code>mkvirtualenv</code> 명령을 사용하여 새로운 가상 환경을 만들 수 있습니다. 이 명령을 실행하면 설정된 환경이 표시됩니다 (플랫폼에 따라 약간씩 다름). 명령이 완료되면 새로운 가상 환경이 활성화됩니다. 프롬프트은 괄호안에 있는 환경 이름으로 시작합니다 (Ubuntu에서는 보여 줍니다만, Windows / macOS에서는 마지막 줄이 비슷합니다).

```bash
$ mkvirtualenv my_django_environment

Running virtualenv with interpreter /usr/bin/python3
...
virtualenvwrapper.user_scripts creating /home/ubuntu/.virtualenvs/t_env7/bin/get_env_details
(my_django_environment) ubuntu@ubuntu:~$
```

이제 Django를 설치하여 개발을 시작할 수 있는 가상 환경에 있습니다.

> **note**: 이제부터는 이 단계(실제로 튜토리얼)에서 위에서 설정한 Python 가상 환경에서 명령이 실행된다고 가정합니다.

#### <a name="Using_a_virtual_environment"></a>가상 환경 사용

가상 환경을 사용하기 위하며 주로 사용되는 유용한 명령입니다 (도구 설명서에 더 많은 내용이 있지만 주로 많이 사용하는 명령입니다).

-	<code>deactivate</code> - 현재 파이썬 가상 환경을 종료합니다
-	<code>workon</code> - 사용 가능한 가상 환경 나열
-	<code>workon name_of_environment</code> - 지정된 Python 가상 환경 활성화
-	<code>rmvirtualenv name_of_environment</code> - 지정된 환경을 제거합니다.

---

### Django 설치

가상 환경을 생성하고, <code>workon</code>을 실행하고 가상 환경을 입력하였으면, pip3를 사용하여 Django를 설치할 수 있습니다.

```bash
pip3 install django
```

다음 명령을 실행하여 설치된 Django를 테스트할 수 있습니다 (Python이 Django 모듈을 찾을 수 있는지 테스트하는 것입니다).

```bash
# Linux/macOS
python3 -m django --version
 2.1.5

# Windows
py -3 -m django --version
 2.1.5
```

> **note**: 위의 Windows 명령이 django 모듈을 보여주지 않으면 다음을 시도합니다.
>
> ```bash
> py -m django --version
> ```
>
> Windows애서는 명령 앞에 <code>py -3</code>을 붙임으로써 <i>Python 3</i> 스크립트를 시작합니다. 그러나 설치에 따라 다를 수 있습니다. 명령에 문제가 발생하면 <code>-3</code> 수정자를 생략하여 보십시오. Linux와 macOS에서 명령은 <code>python3</code>입니다.
>
> **Important**: 이후 Python 3를 호출하기 위해 Linux 명령(<code>python3</code>)을 사용합니다. <i>Windows</i>에서 작업하는 경우 이 명령을 <code>py -3</code>으로 바꾸어서 시작합니다.

---

### 설치 테스팅

위의 테스트는 작동하지만 아직 새로운 것은 아닙니다. 보다 흥미로운 테스트는 골격(skeleton) 프로젝트를 생성하여 작동시키는 것입니다. 이렇게 하려면 먼저 명령 프롬프트 또는 터미널에서 Django 앱을 저장할 위치로 이동합니다. 테스트 사이트를 위한 폴더를 만들고 그 폴더로 이동하십시오.

```bash
mkdir django_test
cd django_test
```

아래과 같이 <b>django-admin</b> 도구를 사용하여 "<i>mytestsite</i>"라는 새 골조 사이트를 만들 수 있습니다. 사이트를 만든 후에는 <b>manage.py</b>라는 프로젝트 관리를 위하여 주로 사용되는 스크립트를 저장한 폴더를 찾아 탐색할 수 있습니다.

```bash
django-admin startproject mytestsite
cd mytestsite
```

아래과 같이 <b>manage.py</b>와 <code>runserver</code> 명령을 사용하여이 폴더 내에서 <i>개발 웹서버</i>를 실행합니다.

```bash
$ python3 manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).

You have 15 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

December 16, 2018 - 07:06:30
Django version 2.1.5, using settings 'mytestsite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

> <b>note</b>: 위의 명령은 Linux 또는 macOS에서의 명령입니다. 위의 출력 중 경고 "15 unapplied migration(s)"를 무시하십시오!

서버가 실행되면 로컬 웹 브라우저에 URL http://127.0.0.1:8000/ 로 이동하면 사이트를 볼 수 있습니다. 다음과 같은 사이트가 보여야 합니다.

![](Pics/DjangoSkeletonWebsiteHomepage_2_1.png)

---

### 요약

이제 Django 개발 환경을 컴퓨터에서 설치하였습니다.

테스트 섹션에서 <code>django-admin startproject</code>를 사용하여 새로운 Django 웹사이트를 만드는 방법을 간략히 보았고 개발 웹 서버를 동작하여(<code>python3 manage.py runserver</code>), 브라우저에서 실행하는 방법을 간략하게 살펴 보았습니다. 다음 단계에서 이 프로세스를 확장하여 간단하면서도 완벽한 웹 어플리케이션을 작성합니다.

---

### 더 보기

-	[Quick Install Guide](https://docs.djangoproject.com/en/2.1/intro/install/) (Django docs)
-	[How to install Django — Complete guide](https://docs.djangoproject.com/en/2.1/topics/install/) (Django docs) - Django를 제거하는 방법에 대한 정보가 있습니다.
-	[How to install Django on Windows](https://docs.djangoproject.com/en/2.1/howto/windows/) (Django docs)

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
