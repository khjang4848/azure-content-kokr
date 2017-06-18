<properties
	pageTitle="Microsoft Azure에서 모니터링 및 진단 사용 | Microsoft Azure "
	description="Azure에서 리소스에 대한 진단을 설정하는 방법에 대해 알아봅니다."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2015"
	ms.author="robb"/>

# 모니터링 및 진단 사용

[Azure 포털](https://portal.azure.com)에서 리소스에 대한 다양하고 빈번한 모니터링 및 진단 데이터를 구성할 수 있습니다. [REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) 또는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/)를 사용하여 프로그래밍 방식으로 진단을 구성할 수도 있습니다.

Azure의 진단, 모니터링 및 메트릭 데이터는 선택한 저장소 계정에 저장됩니다. 따라서 저장소 탐색기부터 Power BI 및 타사 도구에 이르기까지 원하는 도구를 사용하여 데이터를 읽을 수 있습니다.

## 리소스를 만드는 경우

[Azure 포털](https://portal.azure.com)에서 리소스를 처음 만드는 경우 대부분의 서비스를 통해 진단을 사용하도록 설정할 수 있습니다.

1. **새로 만들기**로 이동한 다음 관심 있는 리소스를 선택합니다.

2. **선택적 구성**을 선택합니다. ![진단 블레이드](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. **진단**을 선택하고 **On**을 클릭합니다. 진단을 저장할 저장소 계정을 선택해야 합니다. 저장소 계정에 진단을 보내는 경우 저장소 및 트랜잭션에 대해 표준 데이터 요금이 부과됩니다.

4. **확인**을 클릭하여 리소스를 만듭니다.

## 기존 리소스에 대한 설정 변경

리소스를 이미 만들었으며 진단 설정을 변경하려는 경우(예: 데이터 컬렉션 수준 변경), Azure 포털에서 바로 변경할 수 있습니다.

1. 리소스로 이동한 다음 **설정** 명령을 클릭합니다.

2. **진단**을 선택합니다.

3. **진단** 블레이드에는 해당 리소스에 대한 가능한 진단 및 모니터링 컬렉션 데이터가 모두 포함되어 있습니다. 일부 리소스의 경우 데이터에 대한 **보존** 정책을 선택하여 저장소 계정에서 정리할 수도 있습니다. ![저장소 진단](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 설정을 선택한 후 **저장** 명령을 클릭합니다. 처음 사용하도록 설정하는 경우 모니터링 데이터가 표시되는 데 시간이 걸릴 수도 있습니다.

### 가상 컴퓨터에 대한 데이터 컬렉션 범주
가상 컴퓨터의 경우 모든 메트릭과 로그가 1분 간격으로 기록되므로 컴퓨터에 대한 최신 정보를 항상 확인할 수 있습니다.

- **기본 메트릭**: 프로세서, 메모리 등 가상 컴퓨터에 대한 상태 메트릭입니다.
- **네트워크 및 웹 메트릭**: 네트워크 연결 및 웹 서비스에 대한 메트릭입니다.
- **.NET 메트릭**: 가상 컴퓨터에서 실행되는 .NET 및 ASP.NET 응용 프로그램에 대한 메트릭입니다.
- **SQL 메트릭**: Microsoft SQL Service를 실행하는 경우 해당 성능 메트릭입니다.
- **Windows 이벤트 응용 프로그램 로그**: 응용 프로그램 채널로 전송되는 Windows 이벤트입니다.
- **Windows 이벤트 시스템 로그**: 시스템 채널로 전송되는 Windows 이벤트입니다. [Microsoft 맬웨어 방지 프로그램](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)의 모든 이벤트도 포함됩니다.
- **Windows 이벤트 보안 로그**: 보안 채널로 전송되는 Windows 이벤트입니다.
- **진단 인프라 로그**: 진단 수집 인프라에 대한 로깅입니다.
- **IIS 로그**: IIS 서버에 대한 로그입니다.

현재 특정 Linux 배포는 지원되지 않으며, 가상 컴퓨터에 게스트 에이전트를 설치해야 합니다.

## 다음 단계

* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](insights-receive-alert-notifications.md)합니다.
* [서비스 메트릭을 모니터링](insights-how-to-customize-monitoring.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* [인스턴스 개수를 자동으로 조정](insights-how-to-scale.md)하여 수요를 기준으로 서비스 크기가 조정되도록 합니다.
* 클라우드에서 코드의 성능을 정확히 파악하려는 경우[응용 프로그램 성능을 모니터링](../application-insights/app-insights-azure-web-apps.md)합니다.
* [이벤트 및 감사 로그를 보고](insights-debugging-with-events.md) 서비스에서 발생한 모든 사항을 알아봅니다.
* [서비스 상태를 추적](insights-service-health.md)하여 Azure에서 성능 저하 또는 서비스 중단이 발생한 시기를 확인합니다.

<!---HONumber=AcomDC_0914_2016-->