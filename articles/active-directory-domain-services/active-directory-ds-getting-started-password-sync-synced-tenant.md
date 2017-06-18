<properties
	pageTitle="Azure AD 도메인 서비스: 암호 동기화 활성화 | Microsoft Azure"
	description="Azure Active Directory 도메인 서비스 시작"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="maheshu"/>

# Azure AD 도메인 서비스에 대한 암호 동기화 활성화
앞의 태스크에서 Azure AD 테넌트에 대한 Azure AD 도메인 서비스를 활성화했습니다. 다음 태스크는 Azure AD 도메인 서비스에 대한 암호의 동기화를 활성화하는 것입니다. 자격 증명 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다.

관련된 단계는 조직에 클라우드 전용 Azure AD 테넌트가 있는지, 아니면 Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화되도록 설정되었는지에 따라 다릅니다.

<br>

> [AZURE.SELECTOR]
- [클라우드 전용 Azure AD 테넌트](active-directory-ds-getting-started-password-sync.md)
- [동기화된 Azure AD 테넌트](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## Task 5: 동기화된 Azure AD 테넌트에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화
동기화된 Azure AD 테넌트는 Azure AD Connect를 사용하여 조직의 온-프레미스 디렉터리와 동기화하도록 설정됩니다. azure AD Connect는 기본적으로 NTLM 및 Kerberos 자격 증명 해시를 Azure AD에 동기화하지 않습니다. Azure AD 도메인 서비스를 사용하려면 Azure AD Connect를 구성하여 NTLM 및 Kerberos 인증에 필요한 자격 증명 해시를 동기화해야 합니다. 다음 단계를 통해 Azure AD 테넌트에 필요한 자격 증명 해시를 동기화할 수 있습니다.


### Azure AD Connect 설치 또는 업데이트
도메인에 가입한 컴퓨터에 Azure AD Connect의 최신 권장 릴리스를 설치합니다. Azure AD Connect 설치의 기존 인스턴스가 있는 경우 최신 버전의 Azure AD Connect를 사용하도록 업데이트해야 합니다. 이미 해결되었을 수도 있는 알려진 문제/버그를 방지하려면 최신 버전의 Azure AD Connect를 사용해야 합니다.

**[Azure AD Connect 다운로드](http://www.microsoft.com/download/details.aspx?id=47594)**

권장 버전: **1.1.281.0** - 2016년 9월 7일에 게시되었습니다.

  > [AZURE.WARNING] Azure AD 테넌트에 동기화할 레거시 암호 자격 증명(NTLM 및 Kerberos 인증에 필요)을 사용하도록 설정하려면 Azure AD Connect의 최신 권장 릴리스를 설치해야 합니다. 이 기능은 Azure AD Connect의 이전 릴리스 또는 레거시 DirSync 도구에서 사용할 수 없습니다.

Azure AD Connect에 대한 설치 지침은 [Azure AD Connect 시작](../active-directory/active-directory-aadconnect.md) 문서에 나와 있습니다.


### NTLM 및 Kerberos 자격 증명 해시를 Azure AD에 동기화하도록 설정합니다.
전체 암호 동기화를 강제로 적용하려면 각 AD 포리스트에서 다음 PowerShell 스크립트를 실행하고 모든 온-프레미스 사용자의 자격 증명 해시를 사용하도록 설정합니다. 이 스크립트를 사용하면 Azure AD 테넌트로 동기화할 NTLM/Kerberos 인증에 필요한 자격 증명 해시를 활성화합니다.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

디렉터리의 크기(사용자, 그룹 등의 수)에 따라 자격 증명 해시를 Azure AD에 동기화하는 데는 시간이 걸립니다. 자격 증명 해시를 Azure AD에 동기화한 직후 Azure AD 도메인 서비스 관리되는 도메인에서 암호를 사용할 수 있습니다.


<br>

## 관련 콘텐츠

- [클라우드 전용 Azure AD 디렉터리에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync.md)

- [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)

- [Windows 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)

- [Red Hat Enterprise Linux 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0921_2016-->