<properties
	pageTitle="Jupyter/IPython Notebook 만들기 | Microsoft Azure"
	description="Azure의 리소스 관리자 배포 모델을 사용하여 만든 Linux 가상 컴퓨터에 Jupyter/IPython Notebook을 배포하는 방법을 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter="python"
	authors="crwilcox"
	manager="wpickett"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="python"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="crwilcox"/>

# Azure의 Jupyter Notebook

[IPython 프로젝트](http://ipython.org) 이전의 [Jupyter 프로젝트](http://jupyter.org)는 라이브 계산 문서의 생성을 사용하여 코드 실행을 결합하는 강력한 대화형 셸을 사용하여 과학적 계산용 도구의 컬렉션을 제공합니다. 이러한 노트북 파일에는 임의 텍스트, 수식, 입력 코드, 결과, 그래픽, 비디오를 비롯하여 최신 웹 브라우저에서 표시할 수 있는 기타 모든 미디어가 포함될 수 있습니다. Python을 처음 접하며 재미있는 대화형 환경에서 이를 배우려는 경우든, 병렬/기술 컴퓨팅에 대해 상당한 조예가 있는 경우든 Jupyter Notebook을 사용하면 많은 이점을 누릴 수 있습니다.

![스크린샷](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) SciPy 및 Matplotlib 패키지를 사용하여 소리 녹음의 구조를 분석합니다.


## Jupyter 두 가지 방법: Azure 노트북 또는 사용자 지정 배포
Azure는 [Jupyter를 사용하여 즉시 시작](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)하는데 사용할 수 있는 서비스를 제공합니다. Azure 노트북 서비스를 사용하면 Python과 많은 라이브러리를 완벽하게 활용할 수 있는, 확장 가능한 컴퓨팅 리소스에 대한 Jupyter의 웹 기반 인터페이스에 대한 액세스를 쉽게 얻을 수 있습니다. 설치는 서비스에 의해 처리되므로 사용자는 사용자에 의한 관리 및 구성에 대한 필요 없이 이러한 리소스에 액세스할 수 있습니다.

노트북 서비스가 시나리오에 대해 작동하지 않는 경우 Linux 가상 컴퓨터(VM)를 사용하여 Microsoft Azure의 Jupyter Notebook을 배포하는 방법을 보여 주는 이 문서를 계속해서 읽으세요.

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Azure에서 VM 만들기 및 구성

첫 번째 단계는 Azure에서 실행되는 VM(가상 컴퓨터)을 만드는 것입니다. 이 VM은 클라우드에서 작동하는 완벽한 운영 체제이며 Jupyter Notebook을 실행하는데 사용됩니다. Azure는 Linux 및 Windows 가상 컴퓨터를 모두 실행할 수 있으므로, 두 가지 유형의 가상 컴퓨터에서 Jupyter를 설치하는 과정에 대해 살펴보겠습니다.

### Linux VM을 만들고 Jupyter에 대한 포트 열기

[여기][portal-vm-linux]에 나와 있는 지침에 따라 *Ubuntu* 배포의 가상 컴퓨터를 만듭니다. 이 자습서에서는 Ubuntu Server 14.04 LTS를 사용합니다. 사용자 이름은 *azureuser*라고 가정합니다.

가상 컴퓨터가 배포한 후 네트워크 보안 그룹의 보안 규칙을 열어야 합니다. Azure 포털에서 **네트워크 보안 그룹**으로 이동하고 VM에 해당하는 보안 그룹에 대한 탭을 엽니다. 다음 설정을 사용하여 인바운드 보안 규칙을 추가해야 합니다. 프로토콜에 대한 **TCP**, 소스(공용) 포트에 대한***** 및 대상(개인) 포트에 대한 **9999**

![스크린샷](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

네트워크 보안 그룹에 있는 동안 **네트워크 인터페이스**를 클릭하고 다음 단계에서 VM에 연결하는데 필요하므로 **공용 IP 주소**를 적어 둡니다.

## VM에 필수 소프트웨어 설치

VM에서 Jupyter Notebook을 실행하려면 먼저 Jupyter 및 종속성을 설치해야 합니다. vm을 만들 때 선택한 ssh 및 사용자 이름/암호 쌍을 사용하여 linux vm에 연결합니다. 이 자습서에서는 PuTTY를 사용하고 Windows에서 연결합니다.

### Ubuntu에 Jupyter 설치
[지속성 분석](https://www.continuum.io/downloads)에서 제공된 링크 중 하나를 사용하여 인기 있는 데이터 과학 python 배포인 Anaconda를 설치합니다. 이 문서를 작성할 당시 아래 링크는 가장 최신 버전입니다.

#### Linux용 Anaconda 설치
<table>
  <th>Python 3.4</th>
  <th>Python 2.7</th>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64비트</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64비트</href>
	</td>
  </tr>
  <tr>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32비트</href>
	</td>
    <td>
		<a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32비트</href>
	</td>  
  </tr>
</table>


#### Ubuntu에 Anaconda3 2.3.0 64비트 설치
예로 Ubuntu에 Anaconda를 설치하는 방법입니다.

	# install anaconda
	cd ~
	mkdir -p anaconda
	cd anaconda/
	curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
	sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

	# clean up home directory
	cd ..
	rm -rf anaconda/

	# Update Jupyter to the latest install and generate its config file
	sudo /anaconda3/bin/conda install jupyter -y
	/anaconda3/bin/jupyter-notebook --generate-config


![스크린샷](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### Jupyter 구성 및 SSL 사용
설치한 후 Jupyter에 대한 구성 파일을 설치해야 합니다. Jupyter 구성 문제가 발생하는 경우 [노트북 서버 실행에 대한 Jupyter 설명서](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html)를 보면 쉽게 이해할 수 있습니다.

그런 다음 프로필 디렉터리로 변경하여(`cd`) SSL 인증서를 만들고 프로필 구성 파일을 편집합니다.

Linux에서는 다음 명령을 사용합니다.

    cd ~/.jupyter

다음 명령을 사용하여 SSL 인증서를 만듭니다(Linux 및 Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

자체 서명된 SSL 인증서를 만들고 있으므로 노트북에 연결할 때 브라우저에서 보안 경고를 표시합니다. 장기 프로덕션용으로는 조직과 연결된 적절히 서명된 인증서를 사용하는 것이 좋습니다. 이 데모에서는 인증서 관리를 다루지 않으므로 지금은 자체 서명된 인증서만을 설명하겠습니다.

인증서를 사용할 뿐만 아니라, 권한이 없는 사용자로부터 노트북을 보호하기 위해 암호도 제공해야 합니다. 보안상의 이유로 Jupyter는 구성 파일에서 암호화된 암호를 사용하므로, 먼저 암호를 암호화해야 합니다. IPython에서 암호화할 수 있는 유틸리티를 제공하므로 명령 프롬프트에서 다음 명령을 실행합니다.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

암호 및 암호 확인을 위한 프롬프트가 표시된 후 암호가 표시됩니다. 다음 단계를 위해 암호를 적어 둡니다.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

다음으로, 프로필 구성 파일을 편집합니다. 사용자가 있는 디렉터리의 `jupyter_notebook_config.py` 파일입니다. 이 파일이 존재하지 않을 수 있습니다. 이 경우 만들면 됩니다. 이 파일에는 여러 필드가 포함되어 있으며 기본적으로 모든 필드는 주석 처리되어 있습니다. 원하는 텍스트 편집기로 이 파일을 열 수 있으며, 다음과 같은 내용이 있는지 확인해야 합니다. **구성에 있는 c.NotebookApp.password를 이전 단계의 sha1로 대체해야 합니다**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Jupyter Notebook 실행

이제 Jupyter Notebook을 시작할 준비가 되었습니다. 이렇게 하려면 노트북을 저장할 디렉터리로 이동한 후 다음 명령을 사용하여 Jupyter Notebook 서버를 시작합니다.

    /anaconda3/bin/jupyter-notebook

이제 주소 `https://[PUBLIC-IP-ADDRESS]:9999`의 Jupyter Notebook에 액세스할 수 있어야 합니다.

노트북에 처음 액세스하는 경우 로그인 페이지에서 암호를 묻습니다. 로그인한 후에는 모든 노트북 작업에 대한 제어 센터인 "Jupyter Notebook 대시보드"가 표시됩니다. 이 페이지에서 새 노트북을 만들고 기존 노트북을 열 수 있습니다.

![스크린샷](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### Jupyter Notebook 사용

**New** 단추를 클릭하는 경우 다음과 같은 여는 페이지가 표시됩니다.

![스크린샷](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

`In []:` 프롬프트로 표시되는 이 영역은 입력 영역이며, 여기서 유효한 Python 코드를 입력할 수 있고 `Shift-Enter`를 누르거나 "Play" 아이콘(도구 모음의 오른쪽을 가리키는 삼각형)을 클릭하면 코드가 실행됩니다.

## 강력한 패러다임: 다양한 미디어를 지원하는 라이브 컴퓨팅 문서

노트북은 Python과 워드 프로세서가 결합된 방식이므로 노트북 자체는 Python 및 워드 프로세서를 사용해 본 적이 있는 사람들에게는 매우 자연스럽게 느껴집니다. Python 코드 블록을 실행할 수 있지만 도구 모음의 드롭다운 메뉴를 사용하여 "Code"에서 "Markdown"으로 셀 스타일을 변경해서 메모 및 기타 텍스트를 유지할 수도 있습니다.

Jupyter는 컴퓨팅 및 다양한 미디어(텍스트, 그래픽, 비디오 및 최신 웹 브라우저에서 표시할 수 있는 모든 것)의 결합을 허용하므로 워드 프로세서보다 훨씬 많은 기능을 제공합니다. 텍스트, 코드, 동영상 및 더 많은 것을 혼합할 수 있습니다.

![스크린샷](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

또한 Python에서 지원하는 여러 가지 탁월한 과학 및 기술 컴퓨팅용 라이브러리 덕분에 다음 스크린샷과 같이 단일 환경에서 단순한 계산뿐만 아니라 복잡한 네트워크 분석도 간편하게 수행할 수 있습니다.

최신 웹 기능과 라이브 컴퓨팅을 결합한 이 패러다임은 많은 가능성을 제공하며, 클라우드에 이상적으로 적합하므로 Notebook을 다음과 같이 활용할 수 있습니다.

* 컴퓨팅 스크래치패드로 사용하여 문제에 대한 예비 작업을 기록합니다.

* '라이브' 컴퓨팅 형식이나 하드카피 형식(HTML, PDF)으로 결과를 동료와 공유하는 데 사용할 수 있습니다.

* 학생들이 실제 코드로 즉시 실험해 보고 수정하고 대화형으로 다시 실행할 수 있도록 계산이 포함된 라이브 교육 교재를 배포하고 표시하는 데 사용할 수 있습니다.

* 다른 사람이 즉시 재현하고 유효성을 검사하고 확장할 수 있는 방식으로, 연구 결과를 나타내는 "실행 가능한 백서"를 제공하는 데 사용할 수 있습니다.

* 협업 컴퓨팅을 위한 플랫폼으로 사용할 수 있습니다. 즉, 여러 사용자가 동일한 노트북 서버에 로그인하여 라이브 컴퓨팅 세션을 공유할 수 있습니다.


IPython 소스 코드 [리포지토리][]로 이동하면 노트북 예제가 있는 전체 디렉터리를 볼 수 있습니다. 여기서 예제를 다운로드하여 Azure Jupyter VM에서 실험해 볼 수 있습니다. 사이트에서 `.ipynb` 파일을 다운로드하고 노트북 Azure VM의 대시보드에 업로드(또는 VM으로 직접 다운로드)하기만 하면 됩니다.

## 결론

Jupyter Notebook은 Azure에서 Python 에코시스템의 기능에 대화형으로 액세스할 수 있는 강력한 인터페이스를 제공합니다. 이 인터페이스는 간단한 Python 탐색 및 학습, 데이터 분석 및 시각화, 시뮬레이션, 병렬 컴퓨팅 등 다양한 사용 사례를 처리합니다. 결과적으로 생성된 Notebook 문서에는 작업을 수행한 후 다른 Jupyter 사용자와 공유할 수 있는 전체 컴퓨팅 레코드가 포함됩니다. Jupyter Notebook은 로컬 응용 프로그램으로 사용할 수 있지만 Azure의 클라우드 배포에 이상적으로 적합합니다.

또한 Jupyter의 핵심 기능은 [Python Tools for Visual Studio][] \(PTVS)(영문)를 통해 Visual Studio 내에서도 사용할 수 있습니다. PTVS는 Microsoft의 무료 오픈 소스 플러그 인으로, IntelliSense, 디버깅, 프로파일링, 병렬 컴퓨팅 통합 등 고급 편집기 기능을 포함하는 고급 Python 개발 환경으로 Visual Studio를 전환해 줍니다.

## 다음 단계

자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

[portal-vm-linux]: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[리포지토리]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

<!---HONumber=AcomDC_0601_2016--->