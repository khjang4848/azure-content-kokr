<properties 
	pageTitle="스트림 분석 이벤트 처리를 사용하여 실시간 이벤트 처리 | Microsoft Azure" 
	description="실시간 이벤트 처리 및 분석을 구현하기 위해 Azure 서비스가 어떻게 상호 운용되는지 알아보세요." 
    keywords="실시간 처리, 이벤트 처리, 참조 아키텍처"
	services="stream-analytics,event-hubs,storage,sql-database" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor=""/>

<tags 
	ms.service="stream-analytics" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>

# 참조 아키텍처: Microsoft Azure 스트림 분석으로 실시간 이벤트 처리

Microsoft Azure 스트림 분석과 실시간 이벤트 처리의 참조 아키텍처는 Microsoft Azure를 지원하는 실시간 PaaS(Platform as a Service) 스트림 처리 솔루션 배포에 대한 전체적인 청사진을 제공하기 위한 것입니다.

## 요약

일반적으로 분석 솔루션은 ETL(추출, 변환, 부하) 및 분석 전에 데이터를 저장하는 데이터 웨어하우징과 같은 기능에 기반을 두었습니다. 더 빠르게 수신되는 데이터를 비롯한 변화하는 요구 사항에 따라 이 기존 모델은 한계에 다다르고 있습니다. 이동하는 스트림 내의 데이터를 저장 전에 분석하는 기능은 하나의 솔루션이며, 새로운 기능은 아니지만 접근 방식이 모든 업종에 널리 채택되지는 않았습니다.

Microsoft Azure는 여러 솔루션 시나리오 및 요구 사항을 지원할 수 있는 광범위한 분석 기술 카탈로그를 제공합니다. 솔루션의 범위로 볼 때 종단간 솔루션에 배포할 Azure 서비스를 선택하는 것은 어려울 수 있습니다. 이 문서는 이벤트 스트리밍 솔루션을 지원하는 다양한 Azure 서비스의 기능 및 상호 운용에 대해 설명하기 위한 것입니다. 또한 고객이 이러한 접근 방식으로 혜택을 볼 수 있는 시나리오를 몇 가지 설명 합니다.

## 목차

- 요약
- 실시간 분석 소개
- Azure에서 실시간 데이터의 가치 제안
- 실시간 분석의 일반적인 시나리오
- 아키텍처 및 구성 요소
	- 데이터 원본
	- 데이터 통합 계층
	- 실시간 분석 계층
	- 데이터 저장소 계층
	- 프레젠테이션/소비 계층
- 결론

**작성자:** Charles Feddersen - Microsoft Corporation, Data Insights Center of Excellence, 솔루션 설계자

**게시:** 2015년 1월

**수정 버전:** 1.0

**다운로드:** [Microsoft Azure 스트림 분석과 실시간 이벤트 처리](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)


## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

 

<!---HONumber=AcomDC_0921_2016-->