<properties
	pageTitle="스트림 분석 작업 시각화 및 문제 해결 | Microsoft Azure"
	description="진단 다이어그램 기능을 사용하여 셀프 서비스 문제 해결을 위해 스트림 분석 작업 파이프라인을 시각화하는 방법을 알아봅니다."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"
/>


# 스트림 분석 작업 시각화 및 문제 해결

다른 클라우드 기반 기술과 마찬가지로 스트림 분석에서도 작업이 예상된 출력(또는 해당 문제에 대한 출력)을 생성하지 않는 이유를 알아내기 위해 문제 해결이 필요합니다. 이러한 점을 고려할 때 스트림 분석은 스트리밍 작업을 시각화하는 기능을 제공합니다. 이 기능은 모델링 도구만큼 유용하며 업무에 필요한 문서 작성이라는 부수적인 효과도 제공합니다.

시각화 패널에는 실행되는 쿼리뿐만 아니라 입력 내용과 구성된 모든 출력 내용이 표시됩니다. 연결 또는 구성 문제가 좀 더 명확히 드러나며, 구성을 시각적으로 나타내는 것도 도움이 될 수 있습니다.

## 진단 다이어그램 도구 사용

이 시각화 도우미에 액세스하려면 스트림 분석 작업의 "설정" 블레이드에서 "진단 다이어그램" 단추를 클릭하면 됩니다.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

모든 입력 및 출력은 아래와 같이 해당 구성 요소의 현재 상태를 나타내기 위해 색으로 표시됩니다.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

사용자가 작업 내의 데이터 흐름 패턴을 이해하기 위해 중간 쿼리 단계를 확인하려는 경우 시각화 도구에 쿼리가 구성 요소 단계 및 흐름 시퀀스로 구분되어 표시됩니다. 각 쿼리 단계를 클릭하면 그림과 같이 쿼리 편집 창에 해당 섹션이 표시됩니다.

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0921_2016-->