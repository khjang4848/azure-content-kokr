<properties 
	pageTitle="Azure Multi-Factor Authentication 보고서"
	description="Azure Multi-Factor Authentication 기능 - 보고서를 사용하는 방법을 설명합니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication에서 보고서

Azure Multi-Factor Authentication은 사용자 및 사용자의 조직에서 사용할 수 있는 다양한 보고서를 제공합니다. Multi-Factor Authentication 관리 포털을 통해 이러한 보고서에 액세스할 수 있으며 Azure MFA 공급자나 Azure MFA, Azure AD Premium 또는 Enterprise Mobility Suite 라이선스가 필요합니다. 다음은 사용 가능한 보고서의 목록입니다.

Azure 관리 포털을 통해 보고서에 액세스할 수 있습니다.

Name| 설명
:------------- | :------------- |
사용 | 사용 현황 보고서는 전반적인 사용 현황, 사용자 요약 및 사용자 세부 내용에 대한 정보를 표시합니다.
서버 상태|이 보고서는 계정에 연결된 Multi-Factor Authentication 서버의 상태를 표시합니다.
차단된 사용자 기록|이러한 보고서는 사용자 차단 또는 차단 해제 요청 기록을 보여 줍니다.
무시된 사용자 기록|사용자의 전화번호에 대한 Multi-Factor Authentication 무시 요청 기록을 보여 줍니다.
사기 행위 경고|지정한 날짜 범위 동안 제출된 사기 행위 경고 기록을 보여 줍니다.
Queued|처리 및 해당 상태에 대해 대기 중인 보고서가 나열되어 있습니다. 보고서가 완료되면 보고서를 다운로드하거나 볼 링크가 제공됩니다.

## 보고서를 보려면

1.	http://azure.microsoft.com에 로그온합니다.
2.	왼쪽에서 Active Directory를 선택합니다.
3.	다음 옵션 중 하나를 선택합니다.
	- **옵션 1**: 다단계 인증 공급자 탭을 클릭합니다. MFA 공급자를 선택하고 맨 아래에 있는 관리 단추를 클릭합니다.
	- **옵션 2**: 디렉터리를 선택하고 구성 탭을 클릭합니다. 다단계 인증 섹션에서 서비스 설정 관리를 선택합니다. MFA 서비스 설정 페이지의 맨 아래에서 포털로 이동 링크를 클릭합니다.
4.	Azure Multi-Factor Authentication 관리 포털의 왼쪽 탐색에서 보고서 보기 섹션을 찾을 수 있습니다. 여기에서 위에 설명된 보고서를 선택할 수 있습니다.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>


**추가 리소스**

* [사용자](multi-factor-authentication-end-user.md)
* [MSDN에서 Azure Multi-Factor Authentication](https://msdn.microsoft.com/library/azure/dn249471.aspx)

<!---HONumber=AcomDC_0921_2016-->