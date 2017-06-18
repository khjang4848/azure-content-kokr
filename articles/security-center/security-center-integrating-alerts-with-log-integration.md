<properties
   pageTitle="Azure 로그 통합에 Azure 보안 센터 알림 통합(미리 보기) | Microsoft Azure"
   description="이 문서는 Azure 로그 통합에 보안 센터 알림을 통합하는 데 도움을 줍니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# Azure 로그 통합에 보안 센터 알림 통합(미리 보기)

많은 보안 작업 및 사고 대응 팀은 보안 경고를 조사하고 심사하기 위한 시작점으로 SIEM(보안 정보 및 이벤트 관리) 솔루션을 활용합니다. Azure 로그 통합을 사용하여 고객은 Azure 진단 및 Azure 감사 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 Azure 보안 센터 경고를 거의 실시간으로 로그 분석 또는 SIEM 솔루션과 동기화할 수 있습니다.

Azure 로그 통합은 HP ArcSight, Splunk, IBM QRadar 등과 함께 작동합니다.

## 어떤 로그와 통합할 수 있나요?

Azure에서는 모든 서비스에 대해 광범위한 로깅을 생성합니다. 이러한 로그는 다음과 같이 분류됩니다.

- **컨트롤/관리 로그** - Azure Resource Manager CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다.
- **데이터 평면 로그** - Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 예는 Windows 이벤트 로그이며 가상 컴퓨터의 보안 및 응용 프로그램 로그입니다.

현재 Azure 로그 통합에서는 다음에 대한 통합을 지원합니다.

- Azure VM 로그
- Azure 감사 로그
- Azure 보안 센터 경고

## Azure 로그 통합 설치

[Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324)을 다운로드합니다.

Azure 로그 통합 서비스는 서비스가 설치된 컴퓨터에서 원격 분석 데이터를 수집합니다. 수집된 원격 분석 데이터는 다음과 같습니다.

- Azure 로그 통합 실행 중에 발생하는 예외
- 처리된 쿼리 및 이벤트 수에 대한 메트릭
- 어떤 Azlog.exe 명령줄 옵션이 사용되었는지에 대한 통계

> [AZURE.NOTE] 이 옵션을 선택 취소하여 원격 분석 데이터의 컬렉션을 해제할 수 있습니다.

## Azure 감사 로그 및 보안 센터 경고 통합

1. 명령 프롬프트를 열고 **c:\\Program Files\\Microsoft Azure Log Integration**으로 **cd**합니다.

2. **azlog createazureid** 명령을 실행하여 Azure 구독을 호스트하는 Azure AD(Active Directory) 테넌트에서 [Azure Active Directory 서비스 주체](../active-directory/active-directory-application-objects.md)를 만듭니다.

    Azure 로그인을 묻는 메시지가 표시됩니다.

    > [AZURE.NOTE] 구독의 소유자 또는 서비스 관리자여야 합니다.

    Azure에 대한 인증은 Azure AD를 통해 수행됩니다. Azure 로그 통합에 대한 서비스 주체를 만들면 Azure 구독에서 읽기에 대한 액세스를 제공하는 Azure AD ID가 만들어집니다.

3. **azlog authorize <SubscriptionID>** 명령을 실행하여 2단계에서 만든 서비스 주체에 구독에 대한 읽기 관리자 액세스를 할당합니다. **SubscriptionID**를 지정하지 않으면 서비스 주체에 액세스 권한이 있는 모든 구독에 대한 읽기 관리자 역할이 할당됩니다.

    > [AZURE.NOTE] **createazureid** 명령 직후 **authorize** 명령을 실행하면 Azure AD 계정이 생성되는 시간과 계정이 사용 가능해지는 시간 간에 약간의 대기 시간이 있으므로 경고가 표시될 수 있습니다. **createazureid** 명령을 실행한 후 **authorize** 명령을 실행하기 위해 약 10초 대기한 경우 이러한 경고가 표시되지 않습니다.

4. 다음 폴더에서 감사 로그 JSON 파일이 있는지 확인하세요.

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. 다음 폴더에서 보안 센터 경고가 있는지 확인하세요.

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. 표준 SIEM 파일 전달자 커넥터가 데이터를 SIEM 인스턴스로 파이프하기 위한 적절한 폴더를 가리킵니다. SIEM 구성에서 [SIEM 구성](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm)을 참조하세요.

Azure 로그 통합에 대한 질문이 있으면 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)으로 전자 메일을 보내주세요.

## 다음 단계

Azure 감사 로그 및 속성 정의에 대해 자세히 알아보려면 다음을 참조하세요.

- [리소스 관리자로 작업 감사](../resource-group-audit.md)
- [구독에서 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx) - 감사 로그 이벤트를 검색합니다.

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
- [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!---HONumber=AcomDC_0921_2016-->