<properties
	pageTitle="Python 및 SDK 설치 - Azure"
	description="Azure에서 사용할 Python 및 SDK를 설치하는 방법에 대해 알아봅니다."
	services=""
	documentationCenter="python"
	authors="lmazuel"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="lmazuel"/>

# Python 및 SDK 설치

Python은 Windows에서 쉽게 설정할 수 있으며 Mac, Linux 및 [Bash for Windows](https://msdn.microsoft.com/commandline/wsl/about)에서는 사전 설치되어 제공됩니다. 이 가이드에서는 설치 및 Azure와 함께 사용할 수 있도록 컴퓨터를 설정하는 작업을 단계별로 안내합니다.

## Python Azure SDK에 포함된 내용

Python용 Azure SDK에는 Azure용 Python 응용 프로그램을 개발, 배포 및 관리할 수 있는 구성 요소가 포함되어 있습니다. 구체적으로 말해서 Python용 Azure SDK에는 다음이 포함되어 있습니다.

* **관리 라이브러리**. 이러한 클래스 라이브러리는 저장소, 계정, 가상 컴퓨터와 같은 Azure 리소스를 관리하는 인터페이스를 제공합니다.

* **런타임 라이브러리**. 이러한 클래스 라이브러리는 저장소 및 서비스 버스와 같은 Azure 기능에 액세스하는 인터페이스를 제공합니다.

## 사용할 Python 및 버전

사용할 수 있는 몇 가지의 Python 인터프리터 옵션이 있으며 다음을 예로 들 수 있습니다.

* CPython - 가장 일반적으로 사용되는 표준 Python 인터프리터
* PyPy - 빠르고 규정을 준수하는 CPython에 대한 대체 구현
* IronPython - .Net/CLR에서 실행되는 Python 인터프리터
* Jython - Java 가상 컴퓨터에서 실행되는 Python 인터프리터

**CPython** v2.7 또는 v3.3 이상 및 PyPy 5.4.0은 Python Azure SDK에 대해 테스트 및 지원됩니다.

## Python을 구하는 위치

CPython을 구하는 몇 가지 방법이 있습니다.

* [www.python.org][]에서 직접 구하기
* [www.continuum.io][], [www.enthought.com][] 또는 [www.activestate.com][]과 같은 신뢰할 수 있는 배포자로부터 구하기
* 원본에서 빌드

구체적인 요구 사항이 없다면 처음 두 가지 옵션을 사용하는 것이 좋습니다.

## Windows, Linux 및 MacOS에 SDK 설치(클라이언트 라이브러리만 해당)

Python이 이미 설치되어 있는 경우 pip를 사용하여 기존 Python 2.7 또는 Python 3.3 + 환경에 모든 클라이언트 라이브러리의 번들을 설치할 수 있습니다. 이 경우 PyPI([Python 패키지 인덱스][])에서 패키지가 다운로드됩니다.

관리자 권한이 필요할 수 있습니다.

- Linux 및 MacOS, 다음 `sudo` 명령을 사용합니다. `sudo pip install azure-mgmt-compute`.
- Windows: 관리자로 PowerShell/명령 프롬프트를 엽니다.

각 Azure 서비스에 대한 각 라이브러리를 개별적으로 설치할 수 있습니다.

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

미리 보기 패키지는 `--pre` 플래그를 사용하여 설치할 수 있습니다.

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

`azure` 메타패키지를 사용하여 한 줄로 Azure 라이브러리의 집합도 설치할 수 있습니다. 이 메타패키지에서 일부 패키지는 아직 안정적으로 게시되지 않으므로 `azure` 메타패키지는 아직 미리 보기 상태입니다. 하지만 코어 패키지는 코드 품질/완전성 관점에서 현재 "안정적"으로 간주할 수 있습니다.
- 가능한 빠른 시간 내에 다른 언어와 동기화되어 공식적으로 레이블 표시될 예정입니다. 그때까지는 더 이상의 주요 변경 내용은 계획에 없습니다.

미리 보기 릴리스 상태이므로 `--pre` 플래그를 사용해야 합니다.

```console
   $ pip install --pre azure
```
   
또는 직접

```console
   $ pip install azure==2.0.0rc6
```

## 추가 패키지 가져오기

[Python 패키지 인덱스][]\(PyPI)에서 다양한 Python 라이브러리를 선택할 수 있습니다. Distro를 설치하도록 선택했다면 웹 개발 및 기술 컴퓨팅을 포함한 다양한 시나리오에 맞는 거의 모든 흥미로운 기능을 이미 갖추었습니다.


## Python Tools for Visual Studio

[Visual Studio용 Python Tools][]\(PTVS)는 VS를 완전한 Python IDE로 전환하는 Microsoft의 무료/OSS 플러그 인입니다.

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

PTVS는 선택 사항이지만 Python 및 웹 프로젝트/솔루션 지원, 디버깅, 프로파일링, 대화형 창, 템플릿 편집 및 Intellisense를 제공합니다.

또한 PTVS를 사용하면 [클라우드 서비스][] 및 [웹 사이트][] 배포 지원과 함께 Microsoft Azure에 쉽게 배포할 수 있습니다.

PTVS는 기존 Visual Studio 2013 또는 2015 설치와 함께 작동합니다. 설명서, 다운로드 및 토론에 대한 자세한 내용은 [Python Tools for Visual Studio]를 참조하세요.

## Linux 및 MacOS용 Python Azure 시나리오

Linux 또는 MacOS의 경우 지원되는 주요 Azure 시나리오는 다음과 같습니다.

1. Python용 클라이언트 라이브러리를 사용하여 Azure 서비스 사용

2. Linux VM에서 앱 실행

3. Git를 사용하여 개발 및 Azure 웹 사이트에 게시

첫 번째 시나리오에서는 Azure REST API용 Python 래퍼를 통해 [Blob 저장소][], [큐 저장소][], [테이블 저장소][] 등의 Azure PaaS 기능을 활용하는 풍부한 기능의 웹앱을 작성할 수 있습니다. 이 기능은 Windows, Mac 및 Linux에서 동일하게 작동합니다. 또한 로컬 개발 컴퓨터 또는 Azure에서 실행되는 Linux VM에서 이러한 클라이언트 라이브러리를 사용할 수 있습니다.

VM 시나리오의 경우, 원하는 Linux VM(Ubuntu, CentOS, SUSE)을 시작한 후 원하는 대로 실행하고 관리하면 됩니다. 예를 들어 Windows/Mac/Linux 컴퓨터에서 [IPython][] REPL/Notebook을 실행하고 Azure에서 IPython Engine을 실행하는 Linux 또는 Windows 다중 프로세싱 VM으로 브라우저를 가리키면 됩니다. 자세한 내용은 [Azure의 IPython Notebook][] 자습서를 참조하세요.

Linux VM을 설정하는 방법에 대한 자세한 내용은 [Linux를 실행하는 가상 컴퓨터 만들기][]를 참조하십시오.

Git 배포를 사용하여 Python 웹 응용 프로그램을 개발하고 모든 운영 체제에서 Azure 웹 사이트에 게시할 수 있습니다. Azure에 리포지토리를 푸시할 때 가상 환경이 자동으로 만들어지고 PIP가 필요한 패키지를 설치합니다.

Azure Websites 개발 및 게시에 대한 자세한 내용은 [Django를 사용하여 Websites 만들기][], [Bottle을 사용하여 Websites 만들기][] 및 [Flask를 사용하여 Websites 만들기][]에 대한 자습서를 참조하십시오. WSGI 규격 프레임워크 사용에 대한 일반적인 정보는 [Azure Websites를 사용하여 Python 구성][]을 참조하세요.


## 추가 소프트웨어 및 리소스

* [Python ReadTheDocs용 Azure SDK](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Python Github용 Azure SDK](https://github.com/Azure/azure-sdk-for-python)
* [공식 Python용 Azure 샘플](https://azure.microsoft.com/documentation/samples/?platform=python)
* [지속성 분석 Python 배포][]
* [Enthought Python 배포][]
* [ActiveState Python 배포][]
* [SciPy - Scientific Python 라이브러리 제품군][]
* [NumPy - Python의 숫자 라이브러리][]
* [Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS][]
* [IPython - Python용 고급 REPL/Notebook][]
* [Azure의 IPython Notebook][]
* [GitHub의 Python Tools for Visual Studio][]
* [Python 개발자 센터](/develop/python/)

[지속성 분석 Python 배포]: http://continuum.io
[Enthought Python 배포]: http://www.enthought.com
[ActiveState Python 배포]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - Scientific Python 라이브러리 제품군]: http://www.scipy.org
[NumPy - Python의 숫자 라이브러리]: http://www.numpy.org
[Django 프로젝트 - 완성도 높은 웹 프레임워크/CMS]: http://www.djangoproject.com
[IPython - Python용 고급 REPL/Notebook]: http://ipython.org
[IPython]: http://ipython.org
[Azure의 IPython Notebook]: virtual-machines-linux-jupyter-notebook.md
[클라우드 서비스]: cloud-services-python-ptvs.md
[웹 사이트]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Visual Studio용 Python Tools]: http://aka.ms/ptvs
[GitHub의 Python Tools for Visual Studio]: https://github.com/microsoft/ptvs
[Python 패키지 인덱스]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Linux를 실행하는 가상 컴퓨터 만들기]: virtual-machines-linux-quick-create-cli.md
[Django를 사용하여 Websites 만들기]: web-sites-python-create-deploy-django-app.md
[Bottle을 사용하여 Websites 만들기]: web-sites-python-create-deploy-bottle-app.md
[Flask를 사용하여 Websites 만들기]: web-sites-python-create-deploy-flask-app.md
[Azure Websites를 사용하여 Python 구성]: web-sites-python-configure.md
[테이블 저장소]: storage-python-how-to-use-table-storage.md
[큐 저장소]: storage-python-how-to-use-queue-storage.md
[Blob 저장소]: storage-python-how-to-use-blob-storage.md

<!---HONumber=AcomDC_0914_2016-->