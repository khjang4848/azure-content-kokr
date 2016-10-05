<properties
	pageTitle="가상 컴퓨터를 IPython Notebook 서버로 설정 | Microsoft Azure"
	description="고급 분석용 IPython 서버와 함께 데이터 과학 환경에서 사용할 Azure 가상 컴퓨터를 설정합니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="xibingao;bradsev" />

# 고급 분석을 위해 Azure 가상 컴퓨터를 IPython Notebook으로 설정

이 토픽에서는 데이터 과학 환경의 일부로 사용할 수 있는 고급 분석용 Azure 가상 컴퓨터를 프로비전 및 구성하는 방법을 보여 줍니다. Windows 가상 컴퓨터는 IPython Notebook, Azure 저장소 탐색기 및 AzCopy와 같은 지원 도구뿐만 아니라 고급 분석 프로젝트에 유용한 기타 유틸리티로 구성됩니다. 예를 들어 Azure 저장소 탐색기와 AzCopy는 로컬 컴퓨터에서 Azure Blob 저장소로 데이터를 업로드하거나 Blob 저장소에서 로컬 컴퓨터로 데이터를 다운로드하는 데 편리한 방법을 제공합니다.

## <a name="create-vm"></a>1단계: 범용 Azure 가상 컴퓨터 만들기

Azure 가상 컴퓨터가 이미 있는 경우 여기에서 IPython Notebook 서버를 설정하려면 이 단계를 건너뛰고 [2단계: 기존 가상 컴퓨터에 IPython Notebook에 대한 끝점 추가](#add-endpoint)로 이동할 수 있습니다.

Azure에서 가상 컴퓨터를 만드는 프로세스를 시작하기 전에 해당 프로젝트에 대한 데이터를 처리하는 데 필요한 컴퓨터의 크기를 결정해야 합니다. 컴퓨터가 작을수록 메모리가 작고 CPU 코어 수가 적지만 비용도 적게 듭니다. 컴퓨터 유형 및 가격 목록은 <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">가상 컴퓨터 가격</a> 페이지를 참조하세요.

1. <a href="https://manage.windowsazure.com" target="_blank">Azure 클래식 포털</a>에 로그인하여 왼쪽 아래에서 **새로 만들기**를 클릭합니다. 팝업 창이 나타납니다. **계산** -> **가상 컴퓨터** -> **갤러리에서**를 선택합니다.

	![작업 영역 만들기][24]

2. 다음 이미지 중 하나를 선택합니다.

	* Windows Server 2012 R2 Datacenter
	* Windows Server Essentials Experience(Windows Server 2012 R2)

	그런 다음 오른쪽 아래에서 오른쪽 화살표를 클릭하여 다음 구성 페이지로 이동합니다.

	![작업 영역 만들기][25]

3. 만들려는 가상 컴퓨터의 이름을 입력하고, 컴퓨터에서 처리할 데이터의 크기 및 컴퓨터의 성능(메모리 크기 및 계산 코어 수)에 따라 컴퓨터의 크기(기본값: A3)를 선택한 후 컴퓨터의 사용자 이름 및 암호를 입력합니다. 그런 다음 오른쪽 화살표를 클릭하여 다음 구성 페이지로 이동합니다.

	![작업 영역 만들기][26]

4. 이 가상 컴퓨터에 사용할 **저장소 계정**이 포함된 **지역/선호도 그룹/가상 네트워크**를 선택한 후 해당 저장소 계정을 선택합니다. 끝점의 이름(여기에서는 "IPython")을 입력하여 **끝점** 필드의 맨 아래에 끝점을 추가합니다. 원하는 문자열을 끝점의 **이름**으로 선택할 수 있으며 **사용 가능한** 0에서 65536 사이의 모든 정수를 **공용 포트**로 선택할 수 있습니다. **개인 포트**는 **9999**여야 합니다. 사용자는 인터넷 서비스에 이미 할당된 공용 포트를 사용해서는 **안 됩니다**. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">인터넷 서비스용 포트</a>에서는 이미 할당되어 사용해서는 안 되는 포트 목록을 제공합니다.

	![작업 영역 만들기][27]

5. 확인 표시를 클릭하여 가상 컴퓨터 프로비저닝 프로세스를 시작합니다.

	![작업 영역 만들기][28]


가상 컴퓨터 프로비저닝 프로세스를 완료하는 데 15~25분이 걸릴 수 있습니다. 가상 컴퓨터를 만든 후에는 이 컴퓨터의 상태가 **실행 중**으로 표시됩니다.

![작업 영역 만들기][29]

## <a name="add-endpoint"></a>2단계: 기존 가상 컴퓨터에 IPython Notebook에 대한 끝점 추가

1단계의 지침에 따라 가상 컴퓨터를 만든 경우 IPython Notebook에 대한 끝점이 이미 추가되었으므로 이 단계를 건너뛸 수 있습니다.

가상 컴퓨터가 이미 있는 경우 아래의 3단계에서 설치할 IPython Notebook에 대한 끝점을 추가하려면 먼저 Azure 클래식 포털에 로그인하여 가상 컴퓨터를 선택하고 IPython Notebook 서버에 대한 끝점을 추가합니다. 다음 그림에는 IPython Notebook에 대한 끝점이 Windows 가상 컴퓨터에 추가된 후의 포털에 대한 스크린샷이 나와 있습니다.

![작업 영역 만들기][17]

## <a name="run-commands"></a>3단계: IPython Notebook 및 기타 지원 도구 설치

가상 컴퓨터를 만들 후 RDP(원격 데스크톱 프로토콜)를 사용하여 Windows 가상 컴퓨터에 로그온합니다. 지침은 [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines/virtual-machines-windows-classic-connect-logon.md)을 참조하세요. **관리자** 권한으로 **명령 프롬프트**(**Powershell 명령 창이 아님**)를 열고 다음 명령을 실행합니다.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

설치가 완료되면 *C:\\Users\\<user name>\\Documents\\IPython Notebooks* 디렉터리에서 IPython Notebook 서버가 자동으로 시작됩니다.

메시지가 표시되면 IPython Notebook의 암호화 컴퓨터 관리자의 암호를 입력합니다. 그러면 IPython Notebook을 컴퓨터에서 서비스로 실행할 수 있습니다.

## <a name="access"></a>4단계: 웹 브라우저에서 IPython Notebook 액세스
IPython Notebook 서버에 액세스하려면 웹 브라우저를 열고 URL 텍스트 상자에 *https://&#60;virtual machine DNS name>:&#60;public port number>*를 입력합니다. 여기서 *&#60;public port number>*는 IPython Notebook 끝점을 추가할 때 지정한 포트 번호여야 합니다.

*&#60;virtual machine DNS name>*은 Azure 클래식 포털에서 확인할 수 있습니다. 클래식 포털에 로그인한 후 **가상 컴퓨터**를 클릭하고, 사용자가 만든 컴퓨터를 선택한 후 **대시보드**를 선택합니다. 그러면 DNS 이름이 다음과 같이 표시됩니다.

![작업 영역 만들기][19]

다음 그림과 같이 _이 웹 사이트의 보안 인증서에 문제가 있습니다_(Internet Explorer) 또는 _Your connection is not private_(Chrome)이라는 경고가 나타납니다. **이 웹 사이트를 계속 탐색합니다(권장하지 않음)**(Internet Explorer)를 클릭하거나 **Advanced**, **Proceed to &#60;*DNS Name*> (unsafe)**(Chrome)를 차례로 클릭하여 계속합니다. 그런 다음 이전에 지정한 암호를 입력하여 IPython Notebook에 액세스합니다.

**Internet Explorer:** ![작업 영역 만들기][20]

**Chrome:** ![작업 영역 만들기][21]

IPython Notebook에 로그온하면 *DataScienceSamples* 디렉터리가 브라우저에 표시됩니다. 이 디렉터리에는 사용자가 데이터 과학 작업을 수행하도록 도와주기 위해 Microsoft에서 공유하는 샘플 IPython Notebook이 포함되어 있습니다. 이러한 샘플 IPython Notebook은 IPython Notebook 서버 설정 프로세스 중에 [**Github 리포지토리**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)에서 가상 컴퓨터로 체크 아웃됩니다. Microsoft는 이 리포지토리를 유지 관리하고 자주 업데이트합니다. 사용자는 Github 리포지토리를 방문하여 최근에 업데이트된 샘플 IPython Notebook을 얻을 수 있습니다. ![작업 영역 만들기][18]

## <a name="upload"></a>5단계: 로컬 컴퓨터에서 IPython Notebook 서버로 기존 IPython Notebook 업로드

IPython Notebook은 사용자가 로컬 컴퓨터에 있는 기존 IPython Notebook을 가상 컴퓨터의 IPython Notebook 서버로 쉽게 업로드할 수 있는 방법을 제공합니다. 사용자는 웹 브라우저에서 IPython Notebook에 로그온한 후 IPython Notebook을 업로드할 **디렉터리**를 클릭합니다. 그런 다음 **파일 탐색기**를 사용하여 로컬 컴퓨터에서 업로드할 IPython Notebook .ipynb 파일을 선택한 후 웹 브라우저에서 IPython Notebook 디렉터리로 끌어 놓습니다. **업로드** 단추를 클릭하여 .ipynb 파일을 IPython Notebook 서버에 업로드합니다. 이제 다른 사용자가 자신의 웹 브라우저에서 이 파일을 사용할 수 있습니다.

![작업 영역 만들기][22]

![작업 영역 만들기][23]


##<a name="shutdown"></a>사용하지 않을 때 가상 컴퓨터 종료 및 할당 해제

Azure 가상 컴퓨터는 **종량제**로 비용이 청구됩니다. 가상 컴퓨터를 사용하지 않을 때 비용이 청구되지 않도록 하려면 **중지(할당 해제)** 상태에 있어야 합니다.

> [AZURE.NOTE] Windows 전원 옵션을 사용하여 VM 내부에서 가상 컴퓨터를 종료하면 VM이 중지되지만 여전히 할당된 상태로 유지됩니다. 비용이 계속 청구되지 않도록 하려면 항상 [Azure 클래식 포털](http://manage.windowsazure.com/)에서 가상 컴퓨터를 중지하세요. "PostShutdownAction"이 "StoppedDeallocated"와 동일한 **ShutdownRoleOperation**을 호출하여 Powershell을 통해 VM을 중지할 수도 있습니다.

가상 컴퓨터를 종료하고 할당을 해제하려면 다음을 수행합니다.

1. 사용자 계정을 사용하여 [Azure 클래식 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **가상 컴퓨터**를 선택합니다.

3. 가상 컴퓨터 목록에서 가상 컴퓨터의 이름을 클릭하여 **대시보드** 페이지로 이동합니다.

4. 페이지 아래쪽에서 **종료**를 클릭합니다.

![VM 종료][15]

가상 컴퓨터가 할당 해제되지만 삭제되지는 않습니다. Azure 클래식 포털에서 언제든지 가상 컴퓨터를 다시 시작할 수 있습니다.

## Azure VM을 사용할 준비가 되었습니다. 다음 단계는 무엇입니까?

이제 데이터 과학 연습에서 가상 컴퓨터를 사용할 준비가 완료되었습니다. 또한 데이터 탐색 및 처리, Azure 기계 학습 및 팀 데이터 과학 프로세스와 함께 수행할 다른 작업 등에 가상 컴퓨터를 IPython Notebook 서버로 사용할 준비가 완료되었습니다.

팀 데이터 과학 프로세스의 다음 단계는 [학습 경로](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)에서 매핑되며, 데이터를 HDInsight로 이동한 후 Azure 기계 학습에서 데이터를 통해 학습할 준비를 수행하면서 데이터를 처리 및 샘플링하는 단계를 포함할 수 있습니다.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png

<!---HONumber=AcomDC_0921_2016-->