<properties
	pageTitle="Microsoft Azure Microsoft Azure Virtual Machines, Cloud Services 및 Web Apps에서 자동 크기 조정 개요 | Microsoft Azure"
	description="Microsoft Azure의 자동 크기 조정의 개요입니다. Virtual Machines, Cloud Services 및 Web Apps에 적용됩니다."
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
	ms.date="09/06/2016"
	ms.author="robb"/>

# Microsoft Azure Microsoft Azure Virtual Machines, Cloud Services 및 Web Apps에서 자동 크기 조정 개요

이 문서에서는 Microsoft Azure 자동 크기 조정에 대해 설명하고 그 이점과 사용 방법을 소개합니다.

Azure Insights 자동 크기 조정은 [가상 컴퓨터 크기 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/) 및 [App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)에만 적용됩니다.

>[AZURE.NOTE] Azure에는 두 개의 자동 크기 조정 메서드가 있습니다. 이전 버전의 자동 크기 조정은 Virtual Machines(가용성 집합)에 적용됩니다. 이 기능은 제한적으로 지원하므로 빠르고 안정적인 자동 크기 조정 지원을 위해 VM 크기 조정 설정으로 마이그레이션하는 것이 좋습니다. 이전 기술을 사용하는 방법에 대한 링크는 이 문서에 포함됩니다.


## 자동 크기 조정이란?

자동 크기 조정을 사용하면 응용 프로그램의 부하를 처리하기 위해 적절한 양의 리소스가 실행되도록 할 수 있습니다. 그러므로 늘어난 부하를 처리하기 위해 리소스를 추가하고 유휴 상태의 리소스를 제거하여 비용을 절약할 수 있습니다. 실행할 최소/최대 인스턴스 수를 지정하고 규칙 집합을 기준으로 VM을 자동으로 추가 또는 제거합니다. 최소값을 설정하면 응용 프로그램이 항상 부하가 적은 상태에서 실행될 수 있습니다. 최대값을 설정하면 가능한 총 시간 비용이 제한됩니다. 만든 규칙을 사용하여 이러한 두 극한값 사이를 자동으로 조정합니다.

 ![자동 크기 조정이 설명되었습니다. VM 추가 및 제거](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

규칙 조건이 충족되면 하나 이상의 자동 크기 조정 동작이 트리거됩니다. VM을 추가 및 제거하거나 다른 작업을 수행할 수 있습니다. 다음 개념 다이어그램에서는 이 프로세스를 보여 줍니다.

 ![개념적 자동 크기 조정 흐름 다이어그램](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)
 

## 자동 크기 조정 프로세스 설명
다음 설명은 이전 다이어그램의 부분에 적용됩니다.

### 리소스 메트릭 
리소스는 메트릭을 내보내며 나중에 규칙에 의해 처리됩니다. 메트릭은 다른 메서드를 통해 제공됩니다. Web Apps 및 Cloud Services의 원격 분석은 Azure 인프라에서 직접 제공되는 반면 VM 크기 조정 설정에서는 Azure 진단 에이전트의 원격 분석 데이터를 사용합니다. 일부 자주 사용되는 통계는 CPU 사용량, 메모리 사용량, 스레드 수, 큐 길이 및 디스크 사용량을 포함합니다. 사용할 수 있는 원격 분석 데이터 목록은 [자동 크기 조정 공통 메트릭](insights-autoscale-common-metrics.md)을 참조하세요.

### Time
일정 기반 규칙은 UTC 기준으로 합니다. 규칙을 설정할 때 표준 시간대를 올바르게 설정해야 합니다.

### 규칙
다이어그램에서는 자동 크기 조정 규칙을 하나만 표시하지만 여러 개 있을 수도 있습니다. 상황에 필요한 대로 겹치는 복잡한 규칙을 만들 수 있습니다. 규칙 형식은 다음을 포함합니다.
 
 - **메트릭 기반** - 예를 들어 CPU 사용률이 50%보다 높으면 이 작업을 수행합니다.
 - **시간 기반** - 예를 들어 지정된 표준 시간대에 토요일 오전 8시마다 웹후크를 트리거합니다.

메트릭 기반의 규칙은 응용 프로그램 부하를 측정하고 해당 부하를 기반으로 VM을 추가 또는 제거합니다. 일정 기반의 규칙을 사용하면 부하 시간에서 패턴이 있는 경우 부하가 늘어나거나 줄어들기 전에 크기를 조정할 수 있습니다.

 
### 작업 및 자동화

규칙은 하나 이상의 형식을 가진 작업을 트리거할 수 있습니다.

- **크기 조정** - VM을 감축 또는 확장합니다.
- **메일** - 구독 관리자, 공동 관리자 및 지정한 추가 메일 주소로 메일을 보냅니다.
- **Webhook 통해 자동화** - Azure의 내부 또는 외부에서 여러 복잡한 작업을 트리거할 수 있는 웹후크를 호출합니다. Azure에서 Azure Automation runbook, Azure Function 또는 Azure Logic App을 시작할 수 있습니다. Azure 외부의 타사 예제는 Slack 및 Twilio와 같은 서비스를 포함합니다.


## 자동 크기 조정 설정
자동 크기 조정에서는 다음 용어와 구조를 사용합니다.

- **자동 크기 조정 설정**은 자동 크기 조정 엔진에서 크기를 조정할지 여부를 결정하는 데 사용됩니다. 여기에는 하나 이상의 프로필, 대상 리소스에 대한 정보 및 알림 설정이 포함됩니다.
    - **자동 크기 조정 프로필**은 용량 설정, 트리거를 관리하는 규칙 집합 및 프로필에 대한 크기 조정 작업, 되풀이의 조합입니다. 여러 프로필을 설정하여 겹치는 다양한 요구 사항을 해결할 수 있습니다.
        - **용량 설정**은 인스턴스 수의 최소값, 최대값 및 기본값을 나타냅니다. [그림 1을 사용하는 데 적절한 위치]
        - **규칙**에는 트리거(메트릭 트리거 또는 시간 트리거)와 크기 조정 작업이 포함되며, 규칙을 충족할 때 크기를 자동으로 조정할지 여부를 나타냅니다.
        - **되풀이**는 자동 크기 조정에서 이 프로필을 적용할 시기를 지정합니다. 예를 들어 서로 다른 날짜 또는 요일에 대해 서로 다른 자동 크기 조정 프로필을 적용할 수 있습니다.
- **알림 설정**은 자동 크기 조정 설정의 한 프로필에 대한 조건을 만족하여 자동 크기 조정 이벤트가 발생할 때 발생하는 알림을 정의합니다. 자동 크기 조정은 하나 이상의 메일 주소에 알리거나 하나 이상의 웹후크를 호출할 수 있습니다.
 
![Azure 자동 크기 조정 설정, 프로필 및 규칙 구조](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

구성 가능한 필드 및 설명의 전체 목록은 [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931928.aspx)에서 사용할 수 있습니다.

코드 예제는 다음을 참조하세요.

* [Resource Manager 템플릿을 사용하여 VM 크기 집합에 대한 고급 자동 크기 조정 구성](insights-advanced-autoscale-virtual-machine-scale-sets.md)
* [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931953.aspx)



## 수평 및 수직적 크기 조정
  
자동 크기 조정은 리소스의 크기를 수평적으로만 조정하며 이는 VM 인스턴스 수의 증가("규모 확장") 또는 감소("규모 감축")입니다. 수평적 크기 조정은 부하를 처리하기 위해 잠재적으로 수천 개의 VM을 실행할 수 있으므로 클라우드 상황에서 더 유연합니다. 수직적 크기 조정은 다릅니다. 수직적 크기 조정은 동일한 수의 VM을 유지하지만 VM을 강력하게("강화") 만들거나 약하게("축소") 만듭니다. 전원은 메모리, CPU 속도, 디스크 공간 등에서 측정됩니다. 수직적 크기 조정에 더 많은 제한이 있습니다. 큰 하드웨어의 가용성에 따라 다르며 지역에 따라 달라지고 신속하게 상한값에 도달할 수 있습니다. 또한 수직적 크기 조정은 VM 중지 및 시작이 필요합니다. 자세한 내용은 [Azure Automation을 사용하여 Azure 가상 컴퓨터를 수직으로 확장](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md)을 참조하세요.


## 액세스 방법 
자동 크기 조정은 다음을 통해 설정할 수 있습니다.

- [Azure 포털](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [CLI(플랫폼 간 명령줄 인터페이스)](insights-cli-samples.md#autoscale)
- [Insights REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## 자동 크기 조정이 지원되는 서비스


| 부여 | 스키마 및 문서 |
|--------------------------------------|-----------------------------------------------------|
| 웹앱 | [웹앱 크기 조정](insights-how-to-scale.md) |
| 클라우드 서비스 | [클라우드 서비스 자동 크기 조정](../cloud-services/cloud-services-how-to-scale.md) |
| Virtual Machines: 기본 | [클래식 가상 컴퓨터 가용성 집합 크기 조정](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtual Machines: Windows 크기 조정 설정| [Windows에서 VM 규모 설정 크기 조정](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Virtual Machines: Linux 크기 조정 설정 | [Linux에서 VM 규모 설정 크기 조정](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Virtual Machines: Windows 예제 | [Resource Manager 템플릿을 사용하여 VM 크기 집합에 대한 고급 자동 크기 조정 구성](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## 다음 단계

자동 크기 조정에 대한 자세한 내용은 앞에 나열된 자동 크기 조정 연습을 사용하거나 다음 리소스를 참조하세요.

- [Azure Insights 자동 크기 조정 공용 메트릭](insights-autoscale-common-metrics.md)
- [Azure Insights 자동 크기 조정에 대한 모범 사례](insights-autoscale-best-practices.md)
- [크기 자동 조정 작업을 사용하여 전자 메일 및 웹후크 경고 알림 보내기](insights-autoscale-to-webhook-email.md)
- [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
- [가상 컴퓨터 규모 집합 자동 크기 조정 문제 해결](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

<!---HONumber=AcomDC_0914_2016-->