<properties
	pageTitle="SQL Server 가상 컴퓨터를 IPython Notebook 서버로 설정 | Microsoft Azure"
	description="SQL Server 및 IPython 서버를 사용하여 데이터 과학 가상 컴퓨터를 설정합니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev" 
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="xibingao;bradsev" />

# 고급 분석을 위해 Azure SQL Server 가상 컴퓨터를 IPython Notebook으로 설정

이 토픽에서는 클라우드 기반 데이터 과학 환경의 일부로 사용할 SQL Server 가상 컴퓨터를 프로비전 및 구성하는 방법을 보여 줍니다. Windows 가상 컴퓨터는 IPython Notebook, Azure 저장소 탐색기 및 AzCopy와 같은 지원 도구뿐만 아니라 데이터 과학 프로젝트에 유용한 기타 유틸리티로 구성됩니다. 예를 들어 Azure 저장소 탐색기와 AzCopy는 로컬 컴퓨터에서 Azure Blob 저장소로 데이터를 업로드하거나 Blob 저장소에서 로컬 컴퓨터로 데이터를 다운로드하는 데 편리한 방법을 제공합니다.

Azure 가상 컴퓨터 갤러리에는 Microsoft SQL Server가 포함된 몇 개의 이미지가 있습니다. 데이터 요구 사항에 적합한 SQL Server VM 이미지를 선택하세요. 권장 이미지는 다음과 같습니다.

- 중소 규모 데이터의 경우 SQL Server 2012 SP2 Enterprise
- 대규모 데이터의 경우 SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads

 > [AZURE.NOTE] SQL Server 2012 SP2 Enterprise 이미지에는 **데이터 디스크가 포함되어 있지 않습니다**. 데이터를 저장할 하나 이상의 가상 하드 디스크를 추가 및/또는 연결해야 합니다. Azure 가상 컴퓨터를 만들면 C 드라이브에 매핑되는 운영 체제용 디스크와 D 드라이브에 매핑되는 임시 디스크가 있습니다. D 드라이브는 데이터 저장에 사용하지 마세요. 이름이 의미하는 것과 같이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.


##<a name="Provision"></a>Azure 클래식 포털에 연결 및 SQL Server 가상 컴퓨터 프로비전

1.  사용자 계정을 사용하여 [Azure 클래식 포털](http://manage.windowsazure.com/)에 로그인합니다. Azure 계정이 없는 경우 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 방문하십시오.

2.  Azure 클래식 포털 웹 페이지의 왼쪽 아래에서 **+새로 만들기**, **계산**, **가상 컴퓨터**, **갤러리에서**를 차례로 클릭합니다.

3.  **가상 컴퓨터 만들기** 페이지에서 데이터 요구 사항에 따라 SQL Server가 포함된 가상 컴퓨터 이미지를 선택한 후 페이지의 오른쪽 아래에서 다음 화살표를 클릭합니다. Azure에서 지원되는 SQL Server 이미지에 대한 최신 정보는 [Azure 가상 컴퓨터의 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294720) 문서 집합의 [Azure 가상 컴퓨터에서 SQL Server 시작](http://go.microsoft.com/fwlink/p/?LinkId=294719) 토픽을 참조하십시오.

	![SQL Server VM 선택][1]

4.  첫 번째 **가상 컴퓨터 구성** 페이지에서 다음 정보를 입력합니다.

    -   **가상 컴퓨터 이름**을 입력합니다.
    -   **새 사용자 이름** 상자에 VM 로컬 관리자 계정의 고유한 사용자 이름을 입력합니다.
    -   **새 암호** 상자에 강력한 암호를 입력합니다. 자세한 내용은 [강력한 암호](http://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.
    -   **암호 확인** 상자에 암호를 다시 입력합니다.
    -   드롭다운 목록에서 적절한 **크기**를 선택합니다.

     > [AZURE.NOTE] 프로비전하는 동안 가상 컴퓨터의 크기가 지정됩니다. A2는 프로덕션 작업에 권장 되는 가장 작은 크기입니다. SQL Server Enterprise Edition을 사용할 경우 가상 컴퓨터의 최소 권장 크기는 A3입니다. SQL Server Enterprise Edition을 사용할 경우 A3 이상을 선택합니다. 트랜잭션 작업 이미지에 최적화된 SQL Server 2012 또는 2014 Enterprise를 사용할 때는 A4를 선택합니다. 데이터 웨어하우스 작업 이미지에 최적화된 SQL Server 2012 또는 2014 Enterprise를 사용할 때는 A7을 선택합니다. 선택한 크기는 구성할 수 있는 데이터 디스크 수를 제한합니다. 사용 가능한 가상 컴퓨터 크기 및 가상 컴퓨터에 연결할 수 있는 데이터 디스크 수에 대한 최신 정보는 [Azure의 가상 컴퓨터 크기](http://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하십시오. 가격 정보는 [가상 컴퓨터 가격](https://azure.microsoft.com/pricing/details/virtual-machines/)을 참조하세요.

    오른쪽 아래에 있는 다음 화살표를 클릭하여 계속합니다.

    ![VM 구성][2]

5.  두 번째 **가상 컴퓨터 구성** 페이지에서 네트워킹, 저장소 및 가용성에 대한 리소스를 구성합니다.

    -   **클라우드 서비스** 상자에서 **새 클라우드 서비스 만들기**를 선택합니다.
    -   **클라우드 서비스 DNS 이름** 상자에 **TESTNAME.cloudapp.net** 형식으로 이름이 완성되도록 선택한 DNS 이름의 첫 번째 부분을 입력합니다.
    -   **지역/선호도 그룹/가상 네트워크** 상자에서 이 가상 이미지를 호스트할 영역을 선택합니다.
    -   **저장소 계정**에서 기존 저장소 계정 또는 자동으로 생성된 저장소 계정을 선택합니다.
    -   **가용성 집합** 상자에서 **(없음)**을 선택합니다.
    -   가격 정보를 읽고 동의합니다.

6.	**끝점** 섹션에서 **이름** 아래에 있는 빈 드롭다운을 클릭하고 **MSSQL**을 선택한 다음 데이터베이스 엔진 인스턴스의 포트 번호(기본 인스턴스의 경우 **1433**)를 입력합니다.

7.  SQL Server VM을 IPython Notebook 서버로 사용할 수도 있습니다. 이는 이후 단계에서 구성합니다. 새 끝점을 추가하여 IPython Notebook 서버에 사용할 포트를 지정합니다. **이름** 열에 이름을 입력하고 공용 포트에 대해 원하는 포트 번호를 선택하고 개인 포트에 대해 9999를 선택합니다.

	오른쪽 아래에 있는 다음 화살표를 클릭하여 계속합니다.

	![MSSQL 및 IPython 포트 선택][3]

8.  기본 **VM 에이전트 설치** 옵션을 선택된 채로 두고 마법사의 오른쪽 아래에 있는 확인 표시를 클릭하여 VM 프로비저닝 프로세스를 완료합니다.

	`![VM 최종 옵션][4]

9.  Azure에서 가상 컴퓨터를 준비하는 동안 기다립니다. 다음을 통해 진행할 가상 컴퓨터 상태를 예상합니다.

    -   시작 중(프로비전 중)
    -   중지됨
    -   시작 중(프로비전 중)
    -   실행 중(프로비전 중)
    -   실행 중


##<a name="RemoteDesktop"></a>원격 데스크톱을 사용하여 가상 컴퓨터 열기 및 설치 완료

1.  프로비전이 완료되면 가상 컴퓨터의 이름을 클릭하여 대시보드 페이지로 이동합니다. 페이지 맨 아래에 있는 **Connect**를 클릭합니다.

2.  Windows 원격 데스크톱 프로그램(`%windir%\system32\mstsc.exe`)을 사용하여 rpd 파일을 열도록 선택합니다.

3.  **Windows 보안** 대화 상자에서, 이전 단계에서 지정한 로컬 관리자 계정의 암호를 입력합니다. 가상 컴퓨터의 자격 증명을 확인하도록 요청될 수도 있습니다.

4.  이 가상 컴퓨터에 처음 로그온하는 경우 데스크톱 설정, Windows 업데이트 및 Windows 초기 구성 작업(sysprep) 완료를 포함하여 여러 프로세스를 완료해야 할 수 있습니다. Windows sysprep가 완료되면 SQL Server 설치 프로세스에서 구성 작업을 완료합니다. 이러한 작업으로 인해 완료되는 동안 잠시 지연이 발생할 수 있습니다. SQL Server 설치가 완료될 때까지 `SELECT @@SERVERNAME`에서 올바른 이름을 반환하지 못할 수 있으며, SQL Server Management Studio가 시작 페이지에 표시되지 않을 수 있습니다.

Windows 원격 데스크톱을 사용하여 가상 컴퓨터에 연결된 후 가상 컴퓨터는 다른 컴퓨터와 상당히 유사하게 작동합니다. SQL Server Management Studio(가상 컴퓨터에서 실행 중인)가 설치되어 있는 기본 SQL Server 인스턴스에 일반적인 방식으로 연결합니다.


##<a name="InstallIPython"></a>IPython Notebook 및 기타 지원 도구 설치

IPython Notebook 서버 역할을 하도록 새 SQL Server VM을 구성하고 AzCopy, Azure 저장소 탐색기, 유용한 데이터 과학 Python 패키지 등의 추가 지원 도구를 설치할 수 있도록 특별한 사용자 지정 스크립트가 제공됩니다. 설치하려면 다음을 수행합니다.

- **Windows 시작** 아이콘을 마우스 오른쪽 단추로 클릭하고 **명령 프롬프트(관리자)**를 클릭합니다.
- 다음 명령을 복사하여 명령 프롬프트에 붙여 넣습니다.

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- 메시지가 표시되면 IPython Notebook 서버에 대한 원하는 암호를 입력합니다.
- 사용자 지정 스크립트는 다음과 같은 여러 설치 후 절차를 자동화합니다.
	+ IPython Notebook 서버 설치 및 설정
	+ 이전에 만든 끝점에 대해 Windows 방화벽에서 TCP 포트 열기
	+ SQL Server 원격 연결
	+ IPython Notebook 서버 원격 연결
	+ 샘플 IPython Notebook 및 SQL 스크립트 가져오기
	+ 유용한 데이터 과학 Python 패키지 다운로드 및 설치
	+ AzCopy 및 Azure 저장소 탐색기 와 같은 Azure 도구 다운로드 및 설치 <br>
- 모든 로컬 또는 원격 브라우저에서(여기서 port는 가상 컴퓨터를 프로비전하는 동안 선택한 IPython 공용 포트) `https://<virtual_machine_DNS_name>:<port>` 형식의 URL을 사용하여 IPython Notebook에 액세스하고 실행할 수 있습니다.
- IPython Notebook 서버는 백그라운드 서비스로 실행되며, 가상 컴퓨터를 다시 시작하면 자동으로 다시 시작됩니다.

##<a name="Optional"></a>필요에 따라 데이터 디스크 연결

VM 이미지에 데이터 디스크, 즉 C 드라이브(OS 디스크) 및 D 드라이브(임시 디스크) 이외의 디스크가 포함되지 않은 경우 데이터를 저장할 데이터 디스크를 하나 이상 추가해야 합니다. SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads용 VM 이미지는 SQL Server 데이터 및 로그 파일용 추가 디스크로 미리 구성된 상태로 제공됩니다.

 > [AZURE.NOTE] D 드라이브는 데이터 저장에 사용하지 마세요. 이름이 의미하는 것과 같이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.

추가 데이터 디스크를 연결하려면 다음 과정을 안내하는 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-windows-classic-attach-disk.md)에 설명된 단계를 따릅니다.

1. 이전 단계에서 프로비전한 가상 컴퓨터에 빈 디스크 연결
2. 가상 컴퓨터에서 새 디스크 초기화


##<a name="SSMS"></a>SQL Server Management Studio에 연결하여 혼합 모드 인증 설정

SQL Server 데이터베이스 엔진은 도메인 환경에서만 Windows 인증을 사용할 수 있습니다. 다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 혼합 모드 인증을 위해 SQL Server를 구성하십시오. 혼합 모드 인증은 SQL Server 인증과 Windows 인증을 모두 허용합니다. SQL 인증 모드는 데이터 가져오기 모듈을 사용하여 [Azure 기계 학습 스튜디오](https://studio.azureml.net)의 SQL Server VM 데이터베이스에서 직접 데이터를 수집하는 데 필요합니다.

1.  원격 데스크톱을 사용하여 가상 컴퓨터에 연결되어 있는 동안 Windows **검색** 창에 **SQL Server Management Studio**(SMSS)를 입력합니다. SSMS(SQL Server Management Studio)를 클릭하여 시작합니다. 나중에 사용하기 위해 바탕 화면에 SSMS의 바로 가기를 추가할 수 있습니다.

    ![SSMS 시작][5]

    처음으로 Management Studio를 열 때 사용자 Management Studio 환경이 만들어져야 합니다. 어느 정도 시간이 걸릴 수 있습니다.

2.  Management Studio를 열면 **서버에 연결** 대화 상자가 표시됩니다. **서버 이름** 상자에, 개체 탐색기를 사용하여 데이터베이스 엔진에 연결할 가상 컴퓨터의 이름을 입력합니다. 가상 컴퓨터 이름 대신 **(로컬)** 또는 단일 기간을 **서버 이름**으로 사용할 수도 있습니다. **Windows 인증**을 선택하고, **사용자 이름** 상자의 ***VM\_이름**\\로컬\_관리자*를 그대로 둡니다. **Connect**를 클릭합니다.

    ![서버에 연결][6]

	<br>

	 > [AZURE.TIP] Windows 레지스트리 키 변경 또는 SQL Server Management Studio를 사용하여 SQL Server 인증 모드를 변경할 수 있습니다. Windows 레지스트리 키 변경을 사용하여 인증 모드를 변경하려면 **새 쿼리**를 시작하고 다음 스크립트를 실행합니다.

		USE master
    	go

    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	SQL Server Management Studio를 사용하여 인증 모드를 변경하려면 다음을 수행합니다.

3.  **SQL Server Management Studio 개체 탐색기**에서 SQL Server 인스턴스의 이름(가상 컴퓨터 이름)을 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.

    ![서버 속성][7]

4.  **보안** 페이지의 **서버 인증**에서 **SQL Server 및 Windows 인증 모드**를 선택한 후 **확인**을 클릭합니다.

    ![인증 모드 선택][8]

5.  **SQL Server Management Studio** 대화 상자에서 **확인**을 클릭하여 SQL Server를 다시 시작해야 하는 요구 사항을 확인합니다.

6.  **개체 탐색기**에서 서버를 마우스 오른쪽 단추로 클릭한 후 **다시 시작**을 클릭합니다. SQL Server 에이전트가 실행 중인 경우 에이전트도 다시 시작해야 합니다.

    ![다시 시작][9]

7.  **SQL Server Management Studio** 대화 상자에서 **예**를 클릭하여 SQL Server를 다시 시작한다는 데 동의합니다.

##<a name="Logins"></a>SQL Server 인증 로그인 만들기

다른 컴퓨터에서 데이터베이스 엔진에 연결하려면 SQL Server 인증 로그인을 하나 이상 만들어야 합니다.

SQL Server Management Studio를 사용하거나 프로그래밍 방식으로 새 SQL Server 로그인을 만들 수 있습니다. SQL 인증을 사용하는 새 sysadmin 사용자를 프로그래밍 방식으로 만들려면 **새 쿼리**를 시작하고 다음 스크립트를 실행합니다. <새 사용자 이름> 및 <새 암호>를 원하는 *사용자 이름* 및 *암호*로 바꿉니다.


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


필요에 따라 암호 정책을 조정합니다(샘플 코드에서는 정책 확인 및 암호 만료를 해제함). SQL Server 로그인에 대한 자세한 내용은 [로그인 만들기](http://msdn.microsoft.com/library/aa337562.aspx)를 참조하십시오.

SQL Server Management Studio를 사용하여 새 SQL Server 로그인을 만들려면 다음을 수행합니다.

1.  **SQL Server Management Studio 개체 탐색기**에서 새 로그인을 만들 서버 인스턴스의 폴더를 확장합니다.

2.  **보안** 폴더를 마우스 오른쪽 단추로 클릭하고, **새로 만들기**를 가리키고 **로그인...**을 선택합니다.

    ![새 로그인][10]

3.  **로그인 - 신규** 대화 상자의 **일반** 페이지에 있는 **로그인 이름** 상자에 새 사용자의 이름을 입력합니다.

4.  **SQL Server 인증**을 선택합니다.

5.  **암호** 상자에 새 사용자의 암호를 입력합니다. **암호 확인** 상자에 암호를 다시 입력합니다.

6.  복잡성 및 강제성에 대한 암호 정책 옵션을 적용하려면 **암호 정책 강제 적용**(권장)을 선택합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

7.  만료에 대한 암호 정책 옵션을 적용하려면 **암호 만료 강제 적용**(권장)을 선택합니다. 이 확인란을 사용하려면 먼저 암호 정책 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

8.  처음으로 로그인을 사용한 후 사용자가 새 암호를 만들도록 강제하려면 **다음 로그인할 때 반드시 암호 변경**을 선택합니다. 다른 사용자가 이 로그인을 사용할 경우에 이 옵션을 선택하는 것이 좋습니다. 사용자만 이 로그인을 사용하는 경우에는 이 옵션을 선택하지 마십시오. 이 확인란을 사용하려면 먼저 암호 만료 강제 적용을 선택해야 합니다. SQL Server 인증을 선택할 경우 이 항목은 기본 옵션입니다.

9.  **기본 데이터베이스** 목록에서 로그인의 기본 데이터베이스를 선택합니다. **master**가 이 옵션의 기본값입니다. 사용자 데이터베이스를 아직 만들지 않은 경우 **master**로 설정된 상태로 둡니다.

10. **기본 언어** 목록에서 **기본값**을 선택 값으로 둡니다.

    ![로그인 속성][11]

11. 처음 로그인을 만드는 경우 이 로그인을 SQL Server 관리자로 지정할 수 있습니다. 그렇게 하는 경우 **서버 역할** 페이지에서 **sysadmin**을 선택합니다.

    > [AZURE.IMPORTANT] sysadmin 고정 서버 역할의 구성원은 데이터베이스 엔진을 완전히 제어할 수 있습니다. 보안상의 이유로 이 역할의 구성원은 신중하게 제한해야 합니다.

    ![sysadmin][12]

12. 확인을 클릭합니다.

##<a name="DNS"></a>가상 컴퓨터의 DNS 이름 확인

다른 컴퓨터에서 SQL Server 데이터베이스 엔진에 연결하려면 가상 컴퓨터의 DNS(Domain Name System) 이름을 알아야 합니다.

이 이름은 인터넷에서 가상 컴퓨터를 식별하는 이름입니다. IP 주소를 사용할 수 있지만 Azure가 중복 또는 유지 관리를 위해 리소스를 이동할 경우 IP 주소가 변경될 수 있습니다. DNS 이름은 새 IP 주소로 리디렉션할 수 있으므로 안정적입니다.

1.  Azure 클래식 포털(또는 이전 단계)에서 **가상 컴퓨터**를 선택합니다.

2.  **가상 컴퓨터 인스턴스** 페이지의 **DNS 이름** 열에서 **http://**로 시작하는 가상 컴퓨터의 DNS 이름을 찾아서 복사합니다. 사용자 인터페이스에 전체 이름이 표시되지 않을 수도 있지만 이름을 마우스 오른쪽 단추로 클릭하고 복사를 선택합니다.

##<a name="cde"></a>다른 컴퓨터에서 데이터베이스 엔진에 연결

1.  인터넷에 연결된 컴퓨터에서 SQL Server Management Studio를 엽니다.

2.  **서버에 연결** 또는 **데이터베이스 엔진에 연결** 대화 상자의 **서버 이름** 상자에 가상 컴퓨터의 DNS 이름(이전 작업에서 확인된 이름) 및 공개 끝점 포트 번호를 *DNS이름,포트번호* 형식(예: **tutorialtestVM.cloudapp.net,57500**)으로 입력합니다.

3.  **인증** 상자에 **SQL Server 인증**을 선택합니다.

4.  **로그인** 상자에, 이전 작업에서 만든 로그인 이름을 입력합니다.

5.  **암호** 상자에, 이전 작업에서 만든 로그인의 암호를 입력합니다.

6.  **연결**을 클릭합니다.

##<a name="amlconnect"></a>Azure 기계 학습에서 데이터베이스 엔진에 연결

팀 데이터 과학 프로세스의 이후 단계에서는 [Azure 기계 학습 스튜디오](https://studio.azureml.net)를 사용하여 기계 학습 모델을 빌드 및 배포합니다. 학습 또는 점수 매기기를 위해 SQL Server VM 데이터베이스에서 직접 Azure 기계 학습으로 데이터를 수집하려면 새 [Azure 기계 학습 스튜디오](https://studio.azureml.net) 실험에서 **데이터 가져오기** 모듈을 사용합니다. 이 토픽은 팀 데이터 과학 프로세스 가이드 링크를 통해 보다 자세히 다룹니다. 지침은 [Azure 기계 학습 스튜디오란?](machine-learning-what-is-ml-studio.md)을 참조하세요.

2.	[데이터 가져오기 모듈](https://msdn.microsoft.com/library/azure/dn905997.aspx)의 **속성** 창에 있는 **데이터 원본** 드롭다운 목록에서 **Azure SQL 데이터베이스**를 선택합니다.

3.	**데이터베이스 서버 이름** 텍스트 상자에 `tcp:<DNS name of your virtual machine>,1433`을 입력합니다.

4.	**서버 사용자 계정 이름** 텍스트 상자에 SQL 사용자 이름을 입력합니다.

5.	**서버 사용자 계정 암호** 텍스트 상자에 SQL 사용자의 암호를 입력합니다.

	![Azure ML 데이터 가져오기][13]

##<a name="shutdown"></a>사용하지 않을 때 가상 컴퓨터 종료 및 할당 해제

Azure 가상 컴퓨터는 **종량제**로 비용이 청구됩니다. 가상 컴퓨터를 사용하지 않을 때 비용이 청구되지 않도록 하려면 **중지(할당 해제)** 상태에 있어야 합니다.

> [AZURE.NOTE] Windows 전원 옵션을 사용하여 내부에서 가상 컴퓨터를 종료하면 VM이 중지되지만 여전히 할당된 상태로 유지됩니다. 비용이 청구되지 않도록 하려면 항상 [Azure 클래식 포털](http://manage.windowsazure.com/)에서 가상 컴퓨터를 중지하세요. "PostShutdownAction"이 "StoppedDeallocated"와 동일한 ShutdownRoleOperation을 호출하여 Powershell을 통해 VM을 중지할 수도 있습니다.

가상 컴퓨터를 종료하고 할당을 해제하려면 다음을 수행합니다.

1. 사용자 계정을 사용하여 [Azure 클래식 포털](http://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **가상 컴퓨터**를 선택합니다.

3. 가상 컴퓨터 목록에서 가상 컴퓨터의 이름을 클릭하여 **대시보드** 페이지로 이동합니다.

4. 페이지 아래쪽에서 **종료**를 클릭합니다.

![VM 종료][15]

가상 컴퓨터가 할당 해제되지만 삭제되지는 않습니다. Azure 클래식 포털에서 언제든지 가상 컴퓨터를 다시 시작할 수 있습니다.

## Azure SQL Server VM을 사용할 준비가 되었습니다. 다음 단계는 무엇입니까?

이제 데이터 과학 연습에서 가상 컴퓨터를 사용할 준비가 완료되었습니다. 또한 데이터 탐색 및 처리, Azure 기계 학습 및 TDSP(팀 데이터 과학 프로세스)와 함께 수행할 다른 작업 등에 가상 컴퓨터를 IPython Notebook 서버로 사용할 준비가 완료되었습니다.

데이터 과학 프로세스의 다음 단계는 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)에서 매핑되며, 데이터를 HDInsight로 이동한 후 Azure 기계 학습에서 데이터를 통해 학습할 준비를 수행하면서 데이터를 처리 및 샘플링하는 단계를 포함할 수 있습니다.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

<!---HONumber=AcomDC_0921_2016-->