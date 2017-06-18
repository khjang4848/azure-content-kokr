<properties 
	pageTitle="엔터프라이즈 통합 개요 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="엔터프라이즈 통합의 기능을 사용하여 논리 앱을 사용하는 비즈니스 프로세스 및 통합 시나리오 사용" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2016" 
	ms.author="deonhe"/>

# 엔터프라이즈 통합 팩 개요

## 엔터프라이즈 통합 팩이란?
엔터프라이즈 통합 팩은 B2B(기업 간) 통신을 원활하게 사용하기 위한 Microsoft의 클라우드 기반 솔루션입니다. 해당 팩은 [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md) 및 [EDIFACT](./app-service-logic-enterprise-integration-edifact.md)를 비롯한 업계 표준 프로토콜을 사용하여 비즈니스 파트너 사이에 메시지를 교환합니다. 암호화 및 디지털 서명을 모두 사용하여 메시지를 선택적으로 보호할 수 있습니다.

해당 팩은 다른 프로토콜 및 형식을 사용하는 조직이 다양한 형식을 모두 조직의 시스템에서 해석하고 작업을 수행할 수 있는 형식으로 변환하여 메시지를 전자적으로 교환할 수 있도록 합니다.

BizTalk Server 또는 Microsoft Azure BizTalk 서비스를 잘 알고 있다면 개념이 대부분 유사하기 때문에 엔터프라이즈 통합 기능을 쉽게 사용할 수 있습니다. 주요 차이점은 엔터프라이즈 통합이 통합 계정을 사용하여 B2B 통신에 사용한 아티팩트의 저장 및 관리를 간소화한다는 점입니다.

엔터프라이즈 통합 팩은 기반 아키텍처 측면에서 B2B 앱을 설계, 배포 및 유지 관리하는 데 사용할 수 있는 모든 아티팩트를 저장하는 **통합 계정**에 기반합니다. 통합 계정이 기본적으로 스키마, 파트너, 인증서, 맵 및 규약 등의 아티팩트를 저장하는 클라우드 기반 컨테이너입니다. 이러한 아티팩트는 논리 앱에서 B2B 워크플로를 빌드하는 데 사용될 수 있습니다. 논리 앱에서 아티팩트를 사용하기 전에 통합 계정을 논리 앱에 연결해야 합니다. 연결한 후에 논리 앱은 통합 계정의 아티팩트에 대한 액세스 권한을 가집니다.

## 엔터프라이즈 통합을 사용하는 이유
- 엔터프라이즈 통합을 사용하면 한 곳에 모든 아티팩트를 저장할 수 있고 이것이 통합 계정 입니다.
- 논리 앱 엔진 및 모든 해당 커넥터를 활용하여 B2B 워크플로를 구축하고 사용자 지정 응용 프로그램 뿐만 아니라 타사 SaaS 응용 프로그램, 온-프레미스 앱과 통합할 수 있습니다.
- Azure Functions를 활용할 수도 있습니다.

## 엔터프라이즈 통합을 시작하는 방법
**Azure 포털**의 논리 앱 설계자를 통해 엔터프라이즈 통합 팩을 사용하여 B2B 앱을 구축하고 관리할 수 있습니다.

[PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "논리 앱 PowerShell 항목")을 사용하여 논리 앱을 관리할 수도 있습니다.

Azure 포털에서 앱을 만들기 전에 수행해야 하는 단계의 개요는 다음과 같습니다. ![개요 이미지](./media/app-service-logic-enterprise-integration-overview/overview-0.png)

## 몇 가지 일반적인 시나리오는 무엇인가요?

엔터프라이즈 통합은 이러한 업계 표준을 지원합니다.

- EDI - 전자 데이터 교환
- EAI - 엔터프라이즈 응용 프로그램 통합

## 시작하기 위해 필요한 항목은 다음과 같습니다.
- 통합 계정을 사용하는 Azure 구독
- 맵과 스키마를 만드는 Visual Studio 2015
- [Visual Studio용 Microsoft Azure Logic Apps 엔터프라이즈 통합 도구 2015 2.0](https://aka.ms/vsmapsandschemas)

## 시도
[지금 시도](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)하여 논리 앱의 B2B 기능을 사용하는 완벽하게 작동하는 샘플 AS2 송신 및 수신 논리 앱을 배포합니다.

## 다음에 대해 자세히 알아봅니다.
- [규약](./app-service-logic-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")
- [B2B(기업 간) 시나리오](./app-service-logic-enterprise-integration-b2b.md "B2B 기능으로 논리 앱을 만드는 방법 알아보기")
- [인증서](./app-service-logic-enterprise-integration-certificates.md "엔터프라이즈 통합 인증서에 대해 알아보기")
- [플랫 파일 인코딩/디코딩](./app-service-logic-enterprise-integration-flatfile.md "플랫 파일 내용을 인코딩 및 디코딩하는 방법 알아보기")
- [통합 계정](./app-service-logic-enterprise-integration-accounts.md "통합 계정에 대해 알아보기")
- [맵](./app-service-logic-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")
- [파트너](./app-service-logic-enterprise-integration-partners.md "엔터프라이즈 통합 파트너에 대해 알아보기")
- [스키마](./app-service-logic-enterprise-integration-schemas.md "엔터프라이즈 통합 스키마에 대해 알아보기")
- [XML 메시지 유효성 검사](./app-service-logic-enterprise-integration-xml.md "논리 앱을 사용하여 XML 메시지의 유효성을 검사하는 방법 알아보기")
- [XML 변환](./app-service-logic-enterprise-integration-transform.md "엔터프라이즈 통합 맵에 대해 알아보기")
- [엔터프라이즈 통합 커넥터](../connectors/apis-list.md "엔터프라이즈 통합 팩 커넥터에 대해 알아보기")

<!---HONumber=AcomDC_0921_2016-->