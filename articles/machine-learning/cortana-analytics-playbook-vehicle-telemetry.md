<properties 
	pageTitle="차량 원격 분석 솔루션 플레이북 | Microsoft Azure" 
	description="Cortana Intelligence의 기능을 사용하여 차량 상태 및 주행 습관에 대한 예측 가능한 통찰력 및 실시간 정보를 얻습니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="bradsev" />


# 차량 원격 분석 솔루션 플레이북

이 **메뉴**는 이 플레이북 장에 연결됩니다.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## 개요
슈퍼 컴퓨터가 랩 밖으로 나와 이제 차고에 주차되어 있습니다. 이러한 최첨단 자동차는 수많은 센서를 포함하고 있어서 초당 수백만 개의 이벤트를 추적하고 모니터링할 수 있습니다. 2020년까지 이러한 자동차 대부분이 인터넷에 연결될 것으로 기대하고 있습니다. 이러한 엄청난 데이터를 활용하여 최고의 안전과 안정성 및 승차 환경을 제공한다고 상상해 보세요! Microsoft는 Cortana Intelligence를 통해 이 꿈을 현실화했습니다.

Microsoft의 Cortana Intelligence는 데이터를 지능형 작업으로 변환하는 데 사용되는 완전히 관리되는 빅 데이터 및 고급 분석 제품군입니다. Cortana Intelligence 차량 원격 분석 솔루션 템플릿을 소개하고자 합니다. 이 솔루션은 자동차 대리점, 자동차 제조업체 및 보험 회사가 Cortana Intelligence의 기능을 사용하여 차량 상태 및 주행 습관에 대한 예측 가능한 통찰력을 실시간으로 얻을 수 있는 방법을 보여 줍니다.

이 솔루션은 실시간 일괄 처리를 위해 Cortana Intelligence 플랫폼의 전체 잠재력을 보여 주는 [람다 아키텍처 패턴](https://en.wikipedia.org/wiki/Lambda_architecture)으로 구현되었습니다. 솔루션:

- Vehicle Telematics 시뮬레이터 제공
- 이벤트 허브를 이용하여 수백만 개의 차량 원격 분석 이벤트를 Azure에 수집
- Stream Analytics를 사용하여 차량 상태에 대한 실시간 정보 파악
-  더 다양한 배치 분석을 위해 데이터를 장기적인 저장소에 유지합니다.
- 기계 학습을 이용하여 실시간으로 변칙 검색을 수행하고 일괄 처리하여 예측 가능한 통찰력을 얻습니다.
- HDInsight를 이용하여 대규모로 데이터를 변환하고 Data Factory를 이용하여 일괄 처리 파이프라인의 오케스트레이션, 예약, 리소스 관리 및 모니터링을 처리
- Power BI를 사용하여 실시간 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공

## 아키텍처

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *그림 1 - 차량 원격 분석 솔루션 아키텍처*

이 솔루션은 다음 **Cortana Intelligence 구성 요소**를 포함하고 종단 간 통합을 보여 줍니다.


- **이벤트 허브** - 수백만 개의 차량 원격 분석 이벤트를 Azure에 수집합니다.
- **스트림 분석** - 차량 상태에 대한 실시간 통찰력을 얻고 다양한 일괄 처리 분석을 위해 해당 데이터를 장기간용 저장소에 보관합니다.
- **기계 학습** - 실시간으로 변칙 검색을 수행하고 일괄 처리하여 예측 가능한 통찰력을 얻습니다.
- **HDInsight** - 대규모로 데이터를 변환하는 데 이용됩니다.
- **Data Factory** - 일괄 처리 파이프라인의 오케스트레이션, 예약, 리소스 관리 및 모니터링을 처리합니다.
- **Power BI** - 실시간 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공합니다.

이 솔루션은 서로 다른 두 개의 **데이터 원본**에 액세스합니다.

- **시뮬레이트된 차량 신호 및 진단**: 차량 텔레매틱스 시뮬레이터는 지정된 시간에 차량의 상태 및 주행 패턴에 해당하는 진단 정보 및 신호를 내보냅니다.
- **차량 카탈로그**: 모델 매핑에 대한 VIN이 포함된 참조 데이터 집합입니다.

<!---HONumber=AcomDC_0914_2016-->