<properties
   pageTitle="Microsoft Azure 로그 통합 소개 | Microsoft Azure"
   description="Azure 로그 통합, 주요 기능 및 작동 원리에 대해 알아봅니다."
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

# Microsoft Azure 로그 통합(미리 보기) 소개

Azure 로그 통합, 주요 기능 및 작동 원리에 대해 알아봅니다.

## 개요

Azure에 호스트된 PaaS(Platform as a Service)와 IaaS(Infrastructure as a Service)는 보안 로그에 많은 양의 데이터를 생성합니다. 이러한 로그에는 정책 위반, 내부 및 외부 위협, 규정 준수 문제, 네트워크, 호스트 및 사용자 작업에서 잘못된 부분에 대한 인텔리전스 및 강력한 통찰력을 제공할 수 있는 중요 정보가 포함되어 있습니다.

Azure 로그 통합은 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있게 해줍니다. Azure 로그 통합은 Windows*(WAD)* 가상 컴퓨터에서 Azure 진단을 수집할 뿐 아니라 WAF(웹 응용 프로그램 방화벽) 같은 파트너 솔루션에서도 진단을 수집합니다. 이 통합은 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공합니다.

![Azure 로그 통합][1]

## 어떤 로그와 통합할 수 있나요?

Azure에서는 모든 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 이러한 로그는 다음과 같은 두 가지 유형으로 분류됩니다.

- **컨트롤/관리 로그** - Azure Resource Manager CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. Azure 감사 로그가 이 로그 유형에 속합니다.
- **데이터 평면 로그** - Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 가상 컴퓨터의 Windows 이벤트 시스템, 보안 및 응용 프로그램 로그가 이 로그 유형에 속합니다.

현재 Azure 로그 통합은 Azure 감사 로그, 가상 컴퓨터 로그 및 Azure 보안 센터 경고의 통합을 지원합니다.

Azure 로그 통합에 대한 질문이 있으면 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)으로 전자 메일을 보내주세요.

## 다음 단계

이 문서에서는 Azure 로그 통합을 소개했습니다. Azure 로그 통합 및 지원되는 로그 유형에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Azure Log Integration for Azure logs(미리 보기)](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 로그 통합에 대한 세부 정보, 시스템 요구 사항 및 설치 지침을 다운로드할 수 있습니다.
- [Azure 로그 통합 시작](security-azure-log-integration-get-started.md) - 이 자습서에서는 Azure 로그 통합을 설치하고 Azure 저장소, Azure 감사 로그 및 보안 센터 경고의 로그를 통합하는 방법을 안내합니다.
- [파트너 구성 단계](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 이 블로그 게시물에서는 Splunk, HP ArcSight, IBM QRadar 등의 파트너 솔루션과 함께 작동하도록 Azure 로그 통합을 구성하는 방법을 보여줍니다.
- [Azure 로그 통합 FAQ(질문과 대답)](security-azure-log-integration-faq.md) - 이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다.
- [보안 센터 경고를 Azure 로그 통합과 통합](../security-center/security-center-integrating-alerts-with-log-integration.md) - 이 문서에서는 Azure 진단 및 Azure 감사 로그에 수집된 가상 컴퓨터 보안 이벤트와 함께 보안 센터 경고를 로그 분석 또는 SIEM 솔루션과 동기화하는 방법을 보여줍니다.
- [Azure 진단 및 Azure 감사 로그를 위한 새 기능](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 이 블로그 게시물에서는 Azure 리소스 운영에 대한 정보 수집에 도움이 되는 Azure 감사 로그 및 기타 기능에 대해 소개합니다.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

<!---HONumber=AcomDC_0921_2016-->