<properties
   pageTitle="Azure 로그 통합 시작 | Microsoft Azure"
   description="Azure 로그 통합 서비스를 설치하고 Azure 저장소, Azure 감사 로그 및 Azure 보안 센터 경고의 로그를 통합하는 방법에 대해 배웁니다."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Azure 로그 통합(미리 보기) 시작

Azure 로그 통합은 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있게 해줍니다. 이 통합은 응용 프로그램과 관련된 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공합니다.

이 자습서에서는 Azure 로그 통합을 설치하고 Azure 저장소, Azure 감사 로그 및 Azure 보안 센터 경고의 로그를 통합하는 방법을 안내합니다. 이 자습서를 완료하기 위한 예상 시간은 1시간입니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 로그 통합 서비스를 설치할 온-프레미스 또는 클라우드의 컴퓨터. 이 컴퓨터는 .Net 4.5.1이 설치된 64비트 Windows 운영 체제를 실행 중이어야 합니다. 이 컴퓨터를 **Azlog 통합자**라고 부릅니다.
- 동작합니다. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- Azure 가상 컴퓨터(VM)에 사용하도록 설정된 Azure 진단. 클라우드 서비스에 진단을 사용하려면 [Azure 클라우드 서비스에서 Azure 진단 사용](../cloud-services/cloud-services-dotnet-diagnostics.md)을 참조하세요. Windows를 실행하는 Azure VM에 진단을 사용하려면 [PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)을 참조하세요.
- Azlog 통합자를 Azure 저장소에 연결하고 Azure 구독에 인증하고 권한 부여.
- Azure VM 로그의 경우 Azlog 통합자에 SIEM 에이전트(예: Splunk Universal Forwarder, HP ArcSight Windows Event Collector 에이전트, IBM QRadar WinCollect)가 설치되어 있어야 합니다.

## 배포 고려 사항

이벤트 볼륨이 높으면 Azlog 통합자의 여러 인스턴스를 실행할 수 있습니다. Windows*(WAD)*에 대한 Azure 진단 저장소 계정의 부하 분산 및 인스턴스에 제공할 구독의 수는 용량을 기반으로 해야 합니다.

8-프로세서(코어) 컴퓨터에서는 Azlog 통합자의 인스턴스 하나가 하루에 약 2400만 이벤트를 처리할 수 있습니다(~1M/시간).

4-프로세서(코어) 컴퓨터에서는 Azlog 통합자의 인스턴스 하나가 하루에 약 150만 이벤트를 처리할 수 있습니다(~62.5K/시간).

## Azure 로그 통합 설치

[Azure 로그 통합](https://www.microsoft.com/download/details.aspx?id=53324)을 다운로드합니다.

Azure 로그 통합 서비스는 서비스가 설치된 컴퓨터에서 원격 분석 데이터를 수집합니다. 수집된 원격 분석 데이터는 다음과 같습니다.

- Azure 로그 통합 실행 중에 발생하는 예외
- 처리된 쿼리 및 이벤트 수에 대한 메트릭
- 어떤 Azlog.exe 명령줄 옵션이 사용되었는지에 대한 통계

> [AZURE.NOTE] 이 옵션을 선택 취소하여 원격 분석 데이터의 컬렉션을 해제할 수 있습니다.

## Azure 진단 저장소 계정의 Azure VM 로그 통합

1. Azure 로그 통합을 계속하기 전에 WAD 저장소 계정에서 로그를 수집하도록 위에 설명된 필수 조건을 확인하세요. WAD 저장소 계정에서 로그를 수집하지 않는 경우에는 다음 단계를 수행하지 마세요.

2. 명령 프롬프트를 열고 **c:\\Program Files\\Microsoft Azure Log Integration**으로 **cd**합니다.

3. 명령 실행

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      StorageAccountName을 VM에서 진단 이벤트를 수신하도록 구성된 Azure 저장소 계정의 이름으로 바꿉니다.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      이벤트 XML에 구독 id를 표시하려면 이름에 구독 ID를 추가합니다.

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. 30-60분 정도 기다렸다가(1시간 정도 소요될 수 있음) 저장소 계정에서 가져온 이벤트를 확인합니다. 이벤트를 보려면 Azlog 통합자에서 **이벤트 뷰어 > Windows 로그 > 전달된 이벤트**를 엽니다.

5. 컴퓨터에 설치된 표준 SIEM 커넥터가 **전달된 이벤트** 폴더에서 이벤트를 수집하여 SIEM 인스턴스로 전달하도록 구성되었는지 확인합니다. SIEM 구성을 검토하여 로그 통합을 구성 및 확인합니다.

## 전달된 이벤트 폴더에 데이터가 나타나지 않으면 어떻게 하나요?

1시간이 지난 후에도 **전달된 이벤트** 폴더에 데이터가 나타나지 않으면 다음 조치를 수행합니다.

1. 컴퓨터가 Azure에 액세스할 수 있는지 확인합니다. 연결을 테스트하려면 브라우저에서 [Azure 포털](http://portal.azure.com)을 열어봅니다.

2. 사용자 계정 **azlog**가 **users\\azlog** 폴더에 대한 쓰기 권한을 갖고 있는지 확인합니다.

3. **azlog source list** 명령을 실행하면 **azlog source add** 명령에서 추가된 저장소 계정이 표시되는지 확인합니다.
4. Azure 로그 통합에서 오류를 보고한 경우에는 **이벤트 뷰어 > Windows 로그 > 응용 프로그램**으로 이동합니다.

그래도 이벤트가 표시되지 않으면 다음 조치를 수행합니다.

1. [Microsoft Azure Storage Explorer](http://storageexplorer.com/)를 다운로드합니다.

2. **azlog source add** 명령에서 추가된 저장소 계정에 연결합니다.

3. Microsoft Azure Storage Explorer에서 **WADWindowsEventLogsTable** 테이블을 찾아서 데이터가 있는지 확인합니다. 데이터가 없으면 VM에서 진단이 올바르게 구성되지 않은 것입니다.

## Azure 감사 로그 및 보안 센터 경고 통합

1. 명령 프롬프트를 열고 **c:\\Program Files\\Microsoft Azure Log Integration**으로 **cd**합니다.

2. 명령 실행

        azlog createazureid

      이 명령은 사용자에게 Azure 로그인을 요구합니다. 그런 다음 로그인한 사용자가 관리자, 공동 관리자 또는 소유자인 Azure 구독을 호스트하는 Azure AD 테넌트에 [Azure Active Directory 서비스 주체](../active-directory/active-directory-application-objects.md)를 만듭니다. 로그인한 사용자가 Azure AD 테넌트의 게스트 사용자이면 명령이 실패합니다. Azure에 대한 인증은 Azure Active Directory(AD)를 통해 수행됩니다. Azlog 통합에 대한 서비스 주체를 만들면 Azure 구독을 읽을 수 있는 Azure AD ID가 생성됩니다.

3. 명령 실행

        azlog authorize <SubscriptionID>

      이 명령은 구독에 대한 읽기 권한자 액세스 권한을 2단계에서 만든 서비스 주체에 할당합니다. SubscriptionID를 지정하지 않으면 사용자가 액세스 권한을 갖고 있는 모든 구독에 서비스 주체 읽기 관리자 역할을 할당하려고 시도합니다.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] **createazureid** 명령을 실행한 직후 **authorize** 명령을 실행하면 경고가 표시될 수 있습니다. Azure AD 계정이 생성되는 시점과 계정이 사용 가능하게 되는 시점 사이에 약간의 대기 시간에 있습니다. **createazureid** 명령을 실행한 후 **authorize** 명령을 실행하기 위해 약 10초 대기한 경우 이러한 경고가 표시되지 않습니다.

4. 다음 폴더에서 감사 로그 JSON 파일이 있는지 확인하세요.

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. 다음 폴더에서 보안 센터 경고가 있는지 확인하세요.

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. 표준 SIEM 파일 전달자 커넥터가 데이터를 SIEM 인스턴스로 파이프하기 위한 적절한 폴더를 가리킵니다. 사용 중인 SIEM 제품에 따라 일부 필드 매핑이 필요할 수 있습니다.

Azure 로그 통합에 대한 질문이 있으면 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)으로 전자 메일을 보내주세요.

## 다음 단계

이 자습서에서는 Azure 로그 통합을 설치하고 Azure 저장소의 로그를 통합하는 방법을 알아보았습니다. 자세한 알아보려면 다음을 참조하세요.

- [Microsoft Azure Log Integration for Azure logs(미리 보기)](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
- [Azure 로그 통합 소개](security-azure-log-integration-overview.md) - 이 문서에서는 Azure 로그 통합, 주요 기능 및 작동 원리를 소개합니다.
- [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.
- [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
- [보안 센터 경고를 Azure 로그 통합과 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) - 이 문서에서는 Azure 진단 및 Azure 감사 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 보안 센터 경고를 로그 분석 또는 SIEM 솔루션과 동기화하는 방법을 보여줍니다.
- [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.

<!---HONumber=AcomDC_0921_2016-->