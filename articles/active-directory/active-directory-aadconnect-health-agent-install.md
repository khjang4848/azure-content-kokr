<properties
	pageTitle="Azure AD Connect Health Agent 설치 | Microsoft Azure"
	description="AD FS 및 동기화를 위한 에이전트 설치에 관해 설명하는 Azure AD Connect Health 페이지입니다."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="femila"
	editor="karavar"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/05/2016"
	ms.author="vakarand"/>


# Azure AD Connect Health Agent 설치

이 문서는 Azure AD Connect Health Agent를 설치하고 구성하는 방법을 설명합니다. [여기](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent)에서 에이전트를 다운로드할 수 있습니다.

## 	요구 사항
다음 표는 Azure AD Connect Health를 사용하기 위한 요구 사항 목록입니다.

| 요구 사항 | 설명|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health는 Azure AD Premium 기능이기 때문에 Azure AD Premium이 필요합니다. </br></br>자세한 내용은 [Azure AD Premium 시작하기](active-directory-get-started-premium.md)를 참조하세요.</br>30일 무료 평가판을 시작하려면 [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조하세요.|
|사용자는 Azure AD Connect Health를 시작할 수 있는 Azure AD의 전역 관리자여야 합니다.|기본적으로 전역 관리자만 시작할 Health Agent를 설치 및 구성하고 포털에 액세스하여 Azure AD Connect Health 내에서 작업을 수행할 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 관리](active-directory-administer.md)를 참조하세요. <br><br> 역할 기반 액세스 제어를 사용하여 조직에서 다른 사용자에게 Azure AD Connect Health에 액세스할 수 있습니다. 자세한 내용은 [Azure AD Connect Health용 역할 기반 액세스 제어](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)를 참조하세요. </br></br>**중요:** 에이전트를 설치할 때 사용하는 계정은 필히 직장 또는 학교 계정이어야 하며 Microsoft 계정은 사용할 수 없습니다. 자세한 내용은 [조직으로 Azure 등록](sign-up-organization.md)을 참조하십시오.
|Azure AD Connect Health Agent는 각 대상 서버에 설치됩니다.| Azure AD Connect Health는 포털에 표시되는 데이터를 제공하기 위해 대상 서버에 에이전트를 설치할 것을 요구합니다. </br></br>예를 들어, AD FS 온-프레미스 인프라에서 데이터를 얻기 위해 에이전트는 AD FS 서버, AD FS 서버 및 웹 응용 프로그램 프록시 서버에 설치되어야 합니다. 마찬가지로 온-프레미스 AD DS 인프라에 대한 데이터를 가져오려면 에이전트는 도메인 컨트롤러에 설치되어야 합니다. </br></br>**중요:** 에이전트를 설치할 때 사용하는 계정은 직장 또는 학교 계정이어야 하며 Microsoft 계정은 사용할 수 없습니다. 자세한 내용은 [조직으로 Azure 등록](sign-up-organization.md)을 참조하십시오.|
|Azure 서비스 끝점에 대한 아웃바운드 연결|에이전트는 설치 및 런타임 중에 아래 나열된 Azure AD Connect Health 서비스 끝점에 연결되어야 합니다. 아웃바운드 연결을 차단하는 경우 다음 항목이 허용 목록에 추가되어 있는지 확인합니다.</br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|에이전트를 실행하는 서버의 방화벽 포트| 에이전트가 Azure AD Health 서비스 끝점과 통신하기 위해 다음 방화벽 포트를 열어놓아야 합니다.</br></br><li>TCP/UDP port 443</li><li>TCP/UDP port 5671</li>
|IE 보안 강화를 사용하는 경우 다음 웹 사이트 허용|에이전트가 설치될 서버에서 IE 보안 강화를 사용하도록 설정되어 있는 경우 다음 웹 사이트를 허용해야 합니다.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Azure Active Directory에 의해 신뢰할 수 있는 조직의 페더레이션 서버입니다. 예: https://sts.contoso.com</li>




## AD FS용 Azure AD Connect Health Agent 설치
에이전트 설치를 시작하려면 다운로드한 .exe 파일을 두 번 클릭합니다. 첫 번째 화면에서 설치를 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install1.png)

설치가 완료되면 지금 구성을 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install2.png)

명령 프롬프트가 시작된 다음 PowerShell에서 Register-AzureADConnectHealthADFSAgent를 실행합니다. Azure에 로그인하라는 메시지가 표시됩니다. 로그인을 진행합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install3.png)


로그인한 후 PowerShell이 계속됩니다. PowerShell이 완료되면 닫을 수 있으며 구성이 완료됩니다.

이제 서비스가 자동으로 시작되고 에이전트는 데이터를 모니터링하고 수집합니다. 이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [여기](active-directory-aadconnect-health-agent-install.md#requirements)에서 요구 사항을 완료해야 합니다. 아래 스크린샷은 이러한 오류의 예제입니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install4.png)

에이전트가 설치되었는지 확인하려면 서비스를 열고 다음 사항을 확인합니다. 구성을 완료한 경우 다음 서비스가 실행되고 있어야 합니다. 그렇지 않은 경우 구성이 완료될 때까지 서비스가 시작되지 않습니다.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-requirements/install5.png)


### Windows Server 2008 R2 서버에서 에이전트 설치

Windows Server 2008 R2 서버에 대해 다음을 수행합니다.

1. 서버가 서비스 팩 1 이상에서 실행되고 있는지 확인합니다.
1. 에이전트 설치에 대해 IE ESC를 끕니다.
1. AD Health Agent를 설치하기 전에 각 서버에서 Windows PowerShell 4.0을 설치합니다. Windows PowerShell 4.0을 설치하려면
 - 오프라인 설치 관리자를 다운로드하려면 다음 링크를 사용하여 [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779)를 설치합니다.
 - Windows 기능에서 PowerShell ISE를 설치합니다.
 - [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치합니다.
 - 서버에서 Internet Explorer 버전 10 이상을 설치합니다. Health Service에서 Azure 관리자 자격 증명을 사용하여 인증하는 데 필요합니다.
1. Windows Server 2008 R2에서 Windows PowerShell 4.0을 설치하는 방법에 대한 자세한 내용은 [여기](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)에서 wiki 문서를 참조하세요.

### AD FS 감사 사용

사용 현황 분석 기능을 통해 데이터를 수집하고 분석하려면 Azure AD Connect Health Agent에 AD FS 감사 로그의 정보가 필요합니다. 이러한 로그는 기본적으로 사용하도록 설정되어 있지 않습니다. 이 사항은 AD FS 페더레이션 서버에만 적용됩니다. AD FS 프록시 서버 또는 웹 응용 프로그램 프록시 서버에서 감사를 사용하도록 설정할 필요는 없습니다. AD FS 감사를 사용하도록 설정하고 AD FS 감사 로그를 찾으려면 다음 절차에 따르세요.

#### AD FS 2.0에 대해 감사를 사용하도록 설정하려면

1. **시작**을 클릭하고 **프로그램**, **관리 도구** 순으로 가리킨 다음 **로컬 보안 정책**을 클릭합니다.
2. **보안 설정\\로컬 정책\\사용자 권한 관리** 폴더로 이동하여 보안 감사 생성을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 2.0 서비스 계정이 나열되어 있는지 확인합니다. 없는 경우 **사용자 또는 그룹 추가**를 클릭하여 목록에 추가하고 **확인**을 클릭합니다.
4. 상승된 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 감사를 사용합니다.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. 로컬 보안 정책을 닫고 관리 스냅인을 엽니다. 관리 스냅인을 열려면 **시작**을 클릭하고 **프로그램**, **관리 도구** 순으로 가리킨 다음 AD FS 2.0 관리를 클릭합니다.
6. 작업 창에서 페더레이션 서비스 속성 편집을 클릭합니다.
7. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 클릭합니다.
8. **성공 감사** 및 **실패 감사** 확인란을 선택합니다.
9. **확인**을 클릭합니다.

#### Windows Server 2012 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자**를 열거나 바탕 화면 작업 표시줄에서 서버 관리자를 열어 **로컬 보안 정책**을 연 다음 **도구/로컬 보안 정책**을 클릭합니다.
2. **보안 설정\\로컬 정책\\사용자 권한 할당** 폴더로 이동하여 **보안 감사 생성**을 두 번 클릭합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 나열되어 있는지 확인합니다. 없는 경우 **사용자 또는 그룹 추가**를 클릭하여 목록에 추가하고 **확인**을 클릭합니다.
4. 상승된 권한으로 명령 프롬프트를 열고 다음 명령을 실행하여 감사를 사용합니다.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. **로컬 보안 정책**을 닫고 **AD FS 관리** 스냅인을 엽니다(서버 관리자에서 도구를 클릭한 다음 AD FS 관리 선택).
6. 작업 창에서 **페더레이션 서비스 속성 편집**을 클릭합니다.
7. 페더레이션 서비스 속성 대화 상자에서 **이벤트** 탭을 클릭합니다.
8. **성공 감사 및 실패 감사** 확인란을 선택하고 **확인**을 클릭합니다.






#### AD FS 감사 로그를 찾으려면


1. **이벤트 뷰어**를 엽니다.
2. Windows 로그로 이동하여 **보안**을 선택합니다.
3. 오른쪽에서 **현재 로그 필터링**을 클릭합니다.
4. 이벤트 소스에서 **AD FS 감사**를 선택합니다.

![AD FS 감사 로그](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] AD FS 감사를 사용하지 않도록 설정하는 그룹 정책이 있는 경우 Azure AD Connect Health Agent가 정보를 수집할 수 없습니다. 감사를 사용하지 않도록 설정할 수 있는 그룹 정책이 없는지 확인합니다.

[//]: # "에이전트 프록시 구성 섹션의 시작"

## 동기화용 Azure AD Connect Health Agent 설치
동기화용 Azure AD Connect Health Agent는 Azure AD Connect의 최신 빌드에 자동으로 설치됩니다. 동기화용 Azure AD Connect를 사용하려면 Azure AD Connect의 최신 버전을 다운로드하여 설치해야 합니다. 최신 버전은 [여기](http://www.microsoft.com/download/details.aspx?id=47594)에서 다운로드할 수 있습니다.

에이전트가 설치되었는지 확인하려면 서비스를 열고 다음 사항을 확인합니다. 구성을 완료한 경우 다음 서비스가 실행되고 있어야 합니다. 그렇지 않은 경우 구성이 완료될 때까지 서비스가 시작되지 않습니다.

- Azure AD Connect Health Sync Insights Service
- Azure AD Connect Health Sync Monitoring Service

![동기화에 대한 Azure AD Connect Health 확인](./media/active-directory-aadconnect-health-sync/services.png)

> [AZURE.NOTE] Azure AD Connect Health를 사용하려면 Azure AD Premium이 필요합니다. Azure AD Premium이 없는 경우 Azure 포털에서 구성을 완료할 수 없습니다. 자세한 내용은 [여기](active-directory-aadconnect-health-agent-install.md#requirements)에서 요구 사항을 참조하세요.


## 동기화에 대한 Azure AD Connect Health 수동 등록
Azure AD Connect를 성공적으로 설치한 후 동기화에 대한 Azure AD Connect Health 등록이 실패할 경우 다음 PowerShell 명령을 사용하여 수동으로 에이전트를 등록할 수 있습니다.

>[AZURE.IMPORTANT] 이 PowerShell 명령은 Azure AD Connect를 설치한 후 에이전트 등록에 실패하는 경우에만 필요합니다.

아래 PowerShell 명령은 Azure AD Connect를 성공적으로 설치 및 구성한 후에도 Health 에이전트 등록이 실패하는 경우에만 필요합니다. 이러한 경우 Azure AD Connect Health 서비스는 에이전트가 등록될 때까지 시작되지 않습니다.

다음 PowerShell 명령을 사용하여 동기화에 대한 Azure AD Connect Health 에이전트를 수동으로 등록할 수 있습니다.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

이 명령은 다음 매개 변수를 사용합니다.

- AttributeFiltering: Azure AD Connect가 기본 특성 집합을 동기화하지 않으며 필터링된 특성 집합을 사용하도록 사용자 지정된 경우에는 $true(기본값)이고, 그렇지 않으면 $false입니다.
- StagingMode: Azure AD Connect 서버가 준비 모드에 있지 않으면 $false(기본값)이고, 서버가 준비 모드에 있도록 구성되면 $true입니다.

인증하라는 메시지가 표시되면 Azure AD Connect 구성에 사용한 것과 동일한 전역 관리자 계정(예: admin@domain.onmicrosoft.com)을 사용해야 합니다.

## AD DS용 Azure AD Connect Health 에이전트 설치
에이전트 설치를 시작하려면 다운로드한 .exe 파일을 두 번 클릭합니다. 첫 번째 화면에서 설치를 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

설치가 완료되면 지금 구성을 클릭합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

명령 프롬프트가 시작된 다음 PowerShell에서 Register-AzureADConnectHealthADDSAgent를 실행합니다. Azure에 로그인하라는 메시지가 표시됩니다. 로그인을 진행합니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

로그인한 후 PowerShell이 계속됩니다. PowerShell이 완료되면 닫을 수 있으며 구성이 완료됩니다.

이제 서비스가 자동으로 시작되고 에이전트는 데이터를 모니터링하고 수집합니다. 아래 스크린샷은 이러한 출력의 예제입니다. 이전 섹션에 설명된 필수 조건이 모두 충족되지 않으면 PowerShell 창에 경고가 표시됩니다. 에이전트를 설치하기 전에 [여기](active-directory-aadconnect-health-agent-install.md#requirements)에서 요구 사항을 완료해야 합니다.

![AD DS용 Azure AD Connect Health 확인](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

에이전트가 설치되었는지 확인하려면 서비스를 열고 다음 사항을 확인합니다.

- Azure AD Connect Health AD DS Insights 서비스
- Azure AD Connect Health AD DS 모니터링 서비스

이러한 두 가지 서비스는 구성이 완료될 때까지 시작되지 않습니다.

![Azure AD Connect Health 확인](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Server Core에 AD DS용 Azure AD Connect Health 에이전트 설치. 
.exe 파일을 설치한 후 다음 PowerShell 명령을 사용하여 등록 프로세스를 완료할 수 있습니다.

`Register-AzureADConnectHealthADDSAgent -Credentials $cred

## HTTP 프록시를 사용하도록 Azure AD Connect Health Agent 구성
HTTP 프록시와 작동하도록 Azure AD Connect Health Agent를 구성할 수 있습니다.

>[AZURE.NOTE]
- 에이전트가 Microsoft Windows HTTP Services 대신 System.Net을 사용하여 웹 요청을 수행했으므로 “Netsh WinHttp set ProxyServerAddress” 사용이 작동하지 않습니다.
- 구성된 Http 프록시 주소는 통화 암호화된 Https 메시지에 사용됩니다.
- 인증된 프록시(HTTPBasic 사용)는 지원되지 않습니다.

### Health Agent 프록시 구성 변경
HTTP 프록시를 사용하도록 Azure AD Connect Health Agent를 구성하는 옵션은 다음과 같습니다.

>[AZURE.NOTE] 모든 Azure AD Connect Health Agent 서비스를 다시 시작하여 프록시 설정을 업데이트해야 합니다. 다음 명령을 실행합니다.<br> Restart-Service AdHealth*

#### 기존 프록시 설정 가져오기

##### Internet Explorer에서 가져오기
Health Agent를 실행하는 각 서버에서 다음 PowerShell 명령을 실행하여 Internet Explorer HTTP 프록시 설정을 가져와서 Azure AD Connect Health Agent에 사용할 수 있습니다.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### WinHTTP에서 가져오기
Health Agent를 실행하는 각 서버에서 다음 PowerShell 명령을 실행하여 WinHTTP 프록시 설정을 가져올 수 있습니다.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### 수동으로 프록시 주소 지정
Health Agent를 실행하는 각 서버에서 다음 PowerShell 명령을 실행하여 수동으로 프록시 서버를 지정할 수 있습니다.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

예: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- "address"는 DNS 확인 가능 서버 이름 또는 IPv4 주소일 수 있습니다.
- "port"는 생략할 수 있습니다. 생략하면 443이 기본 포트로 선택됩니다.

#### 기존 프록시 구성 지우기
다음 명령을 실행하여 기존 프록시 구성을 지울 수 있습니다.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### 현재 프록시 설정 읽기
다음 명령을 사용하여 현재 구성된 프록시 설정을 읽을 수 있습니다.

	Get-AzureAdConnectHealthProxySettings


## Azure AD Connect Health 서비스에 대한 테스트 연결
Azure AD Connect Health 에이전트에서 Azure AD Connect Health 서비스와의 연결이 끊어지게 하는 문제가 발생할 수 있습니다. 여기에는 네트워크 문제, 권한 문제 또는 기타 다양한 이유가 포함됩니다.

에이전트에서 3시간 이상 Azure AD Connect Health 서비스로 데이터를 보낼 수 없는 경우 "상태 서비스 데이터가 최신 상태가 아닙니다."라는 경고가 표시됩니다. 해당 문제가 발생한 에이전트가 있는 컴퓨터에서 다음 PowerShell 명령을 실행하여 Azure AD Connect Health 에이전트에서 Azure AD Connect Health 서비스로 데이터를 업로드할 수 있는지 여부를 테스트할 수 있습니다.

    Test-AzureADConnectHealthConnectivity -Role Adfs

역할 매개 변수가 현재 다음 값을 사용합니다.

- Adfs
- 동기화
- ADDS

명령에서 -ShowResults 플래그를 사용하면 자세한 내용을 볼 수 있습니다. 다음 예제를 사용합니다.

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]연결 도구를 사용하려면 먼저 에이전트 등록을 완료해야 합니다. 에이전트 등록을 완료할 수 없는 경우 Azure AD Connect Health에 대한 [요구 사항](active-directory-aadconnect-health-agent-install.md#requirements)을 모두 충족했는지 확인합니다. 이 연결 테스트는 에이전트를 등록하는 동안 기본적으로 수행됩니다.



## 관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0928_2016-->