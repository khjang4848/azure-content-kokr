<properties 
	pageTitle="차량 원격 분석 솔루션 플레이 북: 솔루션에 대한 심층 분석 | Microsoft Azure" 
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


# 차량 원격 분석 솔루션 플레이 북: 솔루션에 대한 심층 분석

이 **메뉴**는 다음 플레이 북의 섹션에 연결됩니다.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

이 섹션은 사용자 지정을 위한 지침 및 조언과 함께 솔루션 아키텍처에 나와 있는 각 단계를 드릴다운합니다.

## 데이터 원본

이 솔루션은 서로 다른 두 개의 데이터 원본을 사용합니다.

- **시뮬레이션된 차량 신호 및 진단 데이터 집합** 및
- **차량 카탈로그**

차량 텔레매틱스 시뮬레이터가 이 솔루션의 일부로 포함되어 있습니다. 이 기능은 지정된 시기에 차량 상태 및 운전 패턴에 해당하는 신호를 내보냅니다. 요구 사항에 따라 사용자 지정할 **차량 텔레매틱스 시뮬레이터 Visual Studio 솔루션**을 다운로드하려면 [차량 텔레매틱스 시뮬레이터](http://go.microsoft.com/fwlink/?LinkId=717075)를 클릭하세요. 차량 카탈로그는 모델 매핑에 대한 VIN이 있는 참조 데이터 집합이 들어 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*그림 2 – 차량 텔레매틱스 시뮬레이터*

다음 스키마를 포함하는 JSON 형식 데이터 집합입니다.

열 | 설명 | 값 
 ------- | ----------- | --------- 
VIN | 임의로 생성된 차량 식별 번호 | 10,000개의 임의로 생성된 차량 식별 번호의 마스터 목록에서 가져옵니다.
외부 온도 | 차량이 주행 중인 외부 온도 | 0-100까지의 임의로 생성된 번호
엔진 온도 | 차량의 엔진 온도 | 0-500까지의 임의로 생성된 번호
속도 | 차량이 주행 중인 엔진 속도 | 0-100까지의 임의로 생성된 번호
연료 | 차량의 연료 수준 | 0-100까지의 임의로 생성된 번호(연료 수준 비율을 나타냄)
엔진 오일 | 차량의 엔진 오일 수준 | 0-100까지의 임의로 생성된 번호(엔진 오일 수준 비율을 나타냄)
타이어 압력 | 차량의 타이어 압력 | 0-50까지의 임의로 생성된 번호(타이어 압력 수준 비율을 나타냄)
주행 기록계 | 차량의 주행 기록계 판독값 | 0-200000까지의 임의로 생성된 번호
Accelerator\_pedal\_position | 차량의 가속 페달 위치 | 0-100까지의 임의로 생성된 번호(가속기 수준 비율을 나타냄)
Parking\_brake\_status | 차량이 주차되었는지 여부를 나타냅니다. | True 또는 False
Headlamp\_status | 헤드 램프가 켜져 있는지 여부를 나타냅니다. | True 또는 False
Brake\_pedal\_status | 브레이크 페달을 눌렀는지 여부를 나타냅니다. | True 또는 False
Transmission\_gear\_position | 차량의 트랜스미션 기어 위치 | 상태: 1단, 2단, 3단, 4단, 5단, 6단, 7단, 8단
Ignition\_status | 차량이 운행 중인지 정지 상태인지를 나타냅니다. | True 또는 False
Windshield\_wiper\_status | 방풍 와이퍼의 설정 여부를 나타냅니다. | True 또는 False
ABS | ABS의 작동 여부를 나타냅니다. | True 또는 False
Timestamp | 데이터 요소가 생성된 타임스탬프 | Date
City | 차량의 위치 | 이 솔루션의 경우 4개 도시: 벨뷰, 레드몬드, 사마미시, 시애틀


차량 모델 참조 데이터 집합에는 모델 매핑에 대한 VIN이 포함되어 있습니다.

VIN | 모델 |
--------------|------------------
FHL3O1SA4IEHB4WU1 | 세단 |
8J0U8XCPRGW4Z3NQE | 하이브리드 |
WORG68Z2PLTNZDBI7 | 가족용 승용차 |
JTHMYHQTEPP4WBMRN | 세단 |
W9FTHG27LZN1YWO0Y | 하이브리드 |
MHTP9N792PHK08WJM | 가족용 승용차 |
EI4QXI2AXVQQING4I | 세단 |
5KKR2VB4WHQH97PF8 | 하이브리드 |
W9NSZ423XZHAONYXB | 가족용 승용차 |
26WJSGHX4MA5ROHNL | 컨버터블 |
GHLUB6ONKMOSI7E77 | 스테이션 웨건 |
9C2RHVRVLMEJDBXLP | 경차 |
BRNHVMZOUJ6EOCP32 | 소형 SUV |
VCYVW0WUZNBTM594J | 스포츠카 |
HNVCE6YFZSA5M82NY | 중형 SUV |
4R30FOR7NUOBL05GJ | 스테이션 웨건 |
WYNIIY42VKV6OQS1J | 대형 SUV |
8Y5QKG27QET1RBK7I | 대형 SUV |
DF6OX2WSRA6511BVG | 쿠페 |
Z2EOZWZBXAEW3E60T | 세단 |
M4TV6IEALD5QDS3IR | 하이브리드 |
VHRA1Y2TGTA84F00H | 가족용 승용차 |
R0JAUHT1L1R3BIKI0 | 세단 |
9230C202Z60XX84AU | 하이브리드 |
T8DNDN5UDCWL7M72H | 가족용 승용차 |
4WPYRUZII5YV7YA42 | 세단 |
D1ZVY26UV2BFGHZNO | 하이브리드 |
XUF99EW9OIQOMV7Q7 | 가족용 승용차
8OMCL3LGI7XNCC21U | 컨버터블 |
……. | |


### 시뮬레이션된 데이터 생성
1.	데이터 시뮬레이터 패키지를 다운로드하려면 차량 텔레매틱스 시뮬레이터 노드의 오른쪽 위에 있는 화살표를 클릭합니다. 파일을 저장하고 컴퓨터에 로컬로 추출합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *그림 3 - 차량 원격 분석 솔루션 청사진*

2.	로컬 컴퓨터에서 차량 텔레매틱스 시뮬레이터 패키지를 추출한 폴더로 이동합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *그림 4 – 차량 텔레매틱스 시뮬레이터 폴더*

3.	응용 프로그램 **CarEventGenerator.exe**를 실행합니다.

### 참조

[차량 텔레매틱스 시뮬레이터 Visual Studio 솔루션](http://go.microsoft.com/fwlink/?LinkId=717075)

[Azure 이벤트 허브](https://azure.microsoft.com/services/event-hubs/)

[Azure 데이터 팩터리](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## 수집
Azure Event Hubs, Stream Analytics 및 Data Factory의 조합을 활용하여 차량 신호, 진단 이벤트 및 실시간 또는 일괄 분석 내용을 수집합니다. 이러한 모든 구성 요소가 솔루션 배포의 일부로 만들고 구성됩니다.

### 실시간 분석
차량 텔레매틱스 시뮬레이터에서 생성된 이벤트는 이벤트 허브 SDK를 사용하여 이벤트 허브에 게시됩니다. Stream Analytics 작업은 이벤트 허브에서 이러한 이벤트를 수집하고 실시간으로 데이터를 처리하여 차량 상태를 분석합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*그림 5 - 이벤트 허브 대시보드*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*그림 6 - 데이터를 처리 중인 스트림 분석 작업*

Stream Analytics 작업

- 이벤트 허브에서 데이터 수집
- 참조 데이터와 조인을 수행하여 차량 VIN을 해당 모델에 매핑
- 충분한 일괄 분석을 위해 Azure Blob Storage에 유지

다음 Stream Analytics 쿼리는 Azure Blob Storage로 데이터를 유지하는 데 사용됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*그림 7 - 데이터 수집을 위한 스트림 분석 작업 쿼리*

### 일괄 분석
또한 더욱 충분한 일괄 분석을 위해 시뮬레이션된 차량 신호와 진단 데이터 집합의 추가 볼륨을 생성합니다. 이 작업은 일괄 처리에 적합한 대표 데이터 볼륨을 보장하는 데 필요합니다. 이 작업을 위해 1년 분량의 시뮬레이션된 차량 신호 및 진단 데이터 집합을 생성하기 위해 Azure Data Factory 워크플로에서 "PrepareSampleDataPipeline"이라는 파이프라인을 사용합니다. 요구 사항에 따라 사용자 지정할 데이터 팩터리 사용자 지정 DotNet Activity Visual Studio 솔루션을 다운로드하려면 [데이터 팩터리 사용자 지정 작업](http://go.microsoft.com/fwlink/?LinkId=717077)을 클릭하세요.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*그림 8 - 일괄 처리 워크플로를 위한 샘플 데이터 준비*

다음과 같이 파이프라인은 사용자 지정 ADF .Net Activity로 구성됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*그림 9 - PrepareSampleDataPipeline*

파이프라인이 성공적으로 실행되고 "RawCarEventsTable" 데이터 집합이 "Ready"로 표시되면 1년 분량의 시뮬레이션된 차량 신호 및 진단 데이터가 생성됩니다. 다음 폴더와 "connectedcar" 컨테이너 아래 저장소 계정에 생성된 파일을 볼 수 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*그림 10 - PrepareSampleDataPipeline 출력*

### 참조

[스트림 수집을 위한 Azure 이벤트 허브 SDK](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Azure Data Factory 데이터 이동 기능](../data-factory/data-factory-data-movement-activities.md) [Azure Data Factory DotNet 작업](../data-factory/data-factory-use-custom-activities.md)

[샘플 데이터 준비를 위한 Azure 데이터 팩터리 DotNet 작업 Visual Studio 솔루션](http://go.microsoft.com/fwlink/?LinkId=717077)


## 데이터 집합 분할

반구조화된 원시 차량 신호 및 진단 데이터 집합은 데이터 준비 단계에서 연/월 형식으로 분할됩니다. 이렇게 분할하면 첫 번째 계정이 가득 찰 때 한 BLOB 계정에서 다른 계정으로 장애 조치할 수 있도록 함으로써 보다 효율적인 쿼리 및 확장성 있는 장기 저장이 가능합니다.

>[AZURE.NOTE] 솔루션의 이 단계는 일괄 처리에만 적용됩니다.

입력 및 출력 데이터 데이터 관리:

- **출력 데이터** (*PartitionedCarEventsTable*로 레이블 지정)는 고객의 "Data Lake"에 기초적이고 "가장 원시적인" 데이터 형식으로 장기간 보관됩니다.
- 이 파이프라인에 대한 **입력 데이터**는 일반적으로 무시됩니다. 출력 데이터가 입력에 대해 완전한 충실도를 포함하며 이후에 사용할 수 있도록 잘 저장(분할)되기 때문입니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*그림 11 - 자동차 이벤트 분할 워크플로*

원시 데이터는 “PartitionCarEventsPipeline”에서 하이브 HDInsight 작업을 사용하여 분할됩니다. 1단계에서 생성한 1년 분량의 샘플 데이터가 연/월로 분할됩니다. 파티션은 1년 동안 각 월(총 12개 파티션)에 해당하는 차량 신호 및 진단 데이터를 생성하는 데 사용됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*그림 12 - PartitionCarEventsPipeline*

"partitioncarevents.hql"이라는 다음의 Hive 스크립트는 분할에 사용되며 다운로드한 zip의 "\\demo\\src\\connectedcar\\scripts" 폴더에 있습니다.


	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*그림 13 - PartitionConnectedCarEvents 하이브 스크립트*

파이프라인이 성공적으로 실행되면 "connectedcar" 컨테이너 아래 저장소 계정에 다음 파티션이 생성된 것을 확인할 수 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*그림 14 - 분할된 출력*

이제 데이터가 최적화되고 더욱 관리가 용이하며 추가 처리가 가능하여 일괄 처리에 대한 폭넓은 이해가 가능합니다.

## 데이터 분석

이 섹션에서는 Azure Stream Analytics, Azure Machine Learning, Azure Data Factory 및 Azure HDInsight의 조합을 차량 상태 및 운전 습관을 어떻게 결합할 수 있는지 살펴봅니다. 다음과 같은 세 개의 하위 섹션이 있습니다.

1.	**기계 학습**: 이 하위 섹션에서는 서비스 유지 관리가 필요한 차량과 안전 문제로 인해 회수가 필요한 차량을 예측하기 위해 이 솔루션에서 사용한 변칙 검색 실험에 대한 정보를 제공합니다.
2.	**실시간 분석**: 이 하위 섹션에서는 Stream Analytics 쿼리 언어를 사용하고 사용자 지정 응용 프로그램을 사용하여 기계 학습 실험을 실시간으로 작동하는 실시간 분석에 대한 정보를 제공합니다.
3.	**일괄 분석**: 이 하위 섹션에서는 Azure Data Factory로 조작 가능한 Azure HDInsight 및 Azure Machine Learning을 사용한 일괄 처리 데이터의 변환 및 처리에 대한 정보를 제공합니다.

### 기계 학습

특정 상태 통계를 기반으로 유지 관리 또는 회수가 필요한 차량을 예측하는 것을 목표로 합니다. 다음을 가정합니다.

- 다음의 3가지 조건 중 하나에 해당되는 경우 차량은 **유지 관리 서비스**가 필요합니다.
	- 타이어 압력이 낮음
	- 엔진 오일 수준이 낮음
	- 엔진 온도가 높음

- 다음 조건 중 하나에 해당되는 경우 차량은 **안전 문제**가 있을 수 있으며 **회수**되어야 합니다.
	- 엔진 온도가 높지만 외부 온도는 낮음
	- 엔진 온도가 낮지만 외부 온도는 높음

이전 요구 사항에 따라 이상을 감지하는 두 개의 별도의 모델을 만들었습니다. 하나는 차량 유지 관리를 감지하고 하나는 차량 회수를 감지합니다. 두 모델에서 변칙 검색에 사용되는 기본 제공 PCA(주성분 분석) 알고리즘을 사용합니다.

**유지 관리 감지 모델**

3가지 표시기(타이어 압력, 엔진 오일 또는 엔진 온도) 중 하나가 해당 조건을 충족하는 경우 유지 관리 감지 모델이 이상을 보고합니다. 따라서 모델을 빌드할 때 이 3가지 변수만 고려하면 됩니다. Azure Machine Learning 실험에서는 먼저 **Select Columns in Dataset** 모듈을 사용하여 이러한 세 변수를 추출합니다. 다음으로, PCA 기반 이상 감지 모듈을 사용하여 이상 감지 모델을 빌드합니다.

PCA(주성분 분석)는 기능 선택, 분류 및 이상 감지에 적용할 수 있는 기계 학습에 설정된 기술입니다. PCA는 가능하면 상관된 변수가 포함된 사례 집합을 주성분이라는 값 집합으로 변환합니다. PCA 기반 모델링의 핵심 아이디어는 데이터를 하위 차원 공간으로 표현하여 해당 기능 및 이상을 보다 쉽게 식별할 수 있도록 하는 것입니다.
 
감지 모델에 대한 각 새로운 입력에 대해 이상 감지기는 먼저 고유 벡터로의 프로젝션을 계산한 다음 정규화된 재구성 오류를 계산합니다. 이 정규화된 오류가 이상 점수입니다. 오류가 높을수록 인스턴스가 더 이상한 것입니다.

유지 관리 감지 문제에서 각 레코드를 타이어 압력, 엔진 오일 및 엔진 온도 좌표로 정의된 3차원 공간의 점으로 간주할 수 있습니다. 이러한 이상을 캡처하려면 3차원 공간의 원본 데이터를 PCA를 사용하여 2차원 공간에 표현하면 됩니다. 따라서 PCA에서 사용할 구성 요소 수 매개 변수를 2로 설정합니다. 이 매개 변수는 PCA 기반 이상 감지를 적용하는 데 중요한 역할을 합니다. PCA를 사용하여 데이터를 표현하면 이러한 이상을 보다 쉽게 식별할 수 있습니다.

**회수 변칙 검색 모델** 회수 이상 감지 모델에서는 Select Columns in Dataset 및 PCA 기반 변칙 검색 모듈을 유사한 방식으로 사용합니다. 특히, 먼저 **Select Columns in Dataset** 모듈을 사용하여 3가지 변수(엔진 온도, 외부 온도 및 속도)를 추출합니다. 또한 엔진 온도는 일반적으로 속도와 상호 관련이 있으므로 속도 변수도 포함합니다. 다음으로 PCA 기반 이상 감지 모듈을 사용하여 3차원 공간의 데이터를 2차원 공간에 표현합니다. 엔진 온도 및 외부 온도가 정반대로 크게 움직이는 경우 회수 조건을 충족하므로 차량을 회수해야 합니다. PCA 기반 이상 감지 알고리즘을 사용하여 PCA를 수행한 후의 이상을 캡처할 수 있습니다.

두 모델을 학습할 때는 유지 관리 또는 회수가 필요 없는 일반 데이터를 입력으로 사용하여 PCA 기반 변칙 검색 모델을 학습해야 합니다. 점수 매기기 실험에서 학습된 변칙 검색 모델을 사용하여 차량에 유지 관리 또는 회수가 필요한지 여부를 감지합니다.


### 실시간 분석

다음 Stream Analytics SQL 쿼리는 차량 속도, 연료 수준, 엔진 온도, 주행 기록계 판독값, 타이어 압력, 엔진 오일 수준 등과 같은 중요한 모든 차량 매개 변수의 평균을 가져오는 데 사용됩니다. 이 평균은 이상을 감지하고, 경고를 생성하며, 특정 지역에서 작동되는 차량의 전반적인 상태를 파악한 다음 이를 인구 통계에 상호 연결하는 데 사용됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

그림 15 - 실시간 처리를 위한 스트림 분석 쿼리

모든 평균은 3초의 TumblingWindow를 통해 계산됩니다. 이 경우 중복되지 않는 연속적인 시간 간격이 필요하므로 TubmlingWindow를 사용합니다.

Azure Stream Analytics에서 모든 “창 작업” 기능에 대한 자세한 내용을 알아보려면 [창 작업(Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx)을 클릭하세요.

**실시간 예측**

응용 프로그램은 실시간으로 기계 학습 모델을 운영할 솔루션의 일부로 포함됩니다. "RealTimeDashboardApp"이라는 응용 프로그램이 솔루션 배포의 일부로 만들어지고 구성됩니다. 이 응용 프로그램은 다음을 수행합니다.

1.	Stream Analytics가 패턴의 이벤트를 계속해서 게시하는 Event Hub 인스턴스를 수신합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*그림 16 - 출력 이벤트 허브 인스턴스에 데이터를 게시하는 Stream Analytics 쿼리*

2.	이 응용 프로그램이 수신하는 모든 이벤트에 대해 다음을 수행합니다.

	- 기계 학습 요청-응답 점수 매기기(RRS) 끝점을 사용하여 데이터를 처리합니다. RRS 끝점이 배포의 일부로 자동으로 게시됩니다.
	- RRS 출력이 푸시 API를 사용하여 PowerBI 데이터 집합에 게시됩니다.

이 패턴은 LOB(기간 업무) 응용 프로그램을 경고, 알림 및 메시징의 시나리오를 위한 실시간 분석 흐름과 통합할 시나리오에도 적용할 수 있습니다.

사용자 지정할 RealtimeDashboardApp Visual Studio 솔루션을 다운로드하려면 [RealtimeDashboardApp 다운로드](http://go.microsoft.com/fwlink/?LinkId=717078)를 클릭하세요.

**실시간 대시보드 응용 프로그램을 실행하려면**

1.	다이어그램 보기에서 PowerBI 노드를 클릭하고 속성 창에서 “실시간 대시보드 응용 프로그램 다운로드” 링크를 클릭합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *그림 17 - PowerBI 대시보드 설정 지침*
2.	로컬에 압축을 풀고 저장합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *그림 18 - RealtimeDashboardApp 폴더*
3.	'RealtimeDashboardApp.exe' 응용 프로그램을 실행합니다.
4.	유효한 Power BI 자격 증명을 제공하고 로그인한 후 동의를 클릭합니다. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png)

*그림 19 - RealtimeDashboardApp: PowerBI에 로그인*

>[AZURE.NOTE] PowerBI 데이터 집합을 플러시하려는 경우 RealtimeDashboardApp을 “flushdata” 매개 변수와 함께 실행합니다.

	RealtimeDashboardApp.exe -flushdata

### 일괄 분석

목표는 Contoso Motors가 Azure 계산 기능으로 빅 데이터를 활용하여 주행 패턴, 사용 동작 및 차량 상태를 폭넓게 이해하는 방법을 보여 주는 것입니다. 이를 통해 다음이 가능합니다.

- 주행 습관 및 연료 효율이 좋은 주행 동작을 파악하여 고객 환경을 개선하고 비용 절감
- 고객 및 고객의 주행 패턴을 사전에 학습하여 비즈니스 의사 결정을 제어하고 동급 최고의 제품 및 서비스 제공

이 솔루션에서는 다음 메트릭을 대상으로 합니다.

1.	**공격적인 주행 동작**: 모델, 위치, 주행 조건 및 계절을 파악하여 공격적인 주행 패턴을 폭넓게 이해합니다. Contoso Motors는 마케팅 캠페인, 주행에 새롭게 개인 설정된 기능 및 사용 현황에 따른 보험에 이를 활용할 수 있습니다.
2.	**연료 효율이 좋은 주행 동작**: 모델, 위치, 주행 조건 및 계절을 파악하여 연료 효율이 좋은 주행 패턴을 폭넓게 이해합니다. Contoso Motors는 마케팅 캠페인, 주행 새 기능 및 운전자에게 비용 효율적이고 환경 친화적인 주행 습관을 사전에 보고하는 데 이를 활용할 수 있습니다.
3.	**회수 모델**: 변칙 검색 기계 학습 실험을 조작하여 회수가 필요한 모델을 식별합니다.

이러한 각 메트릭에 대해 자세히 살펴보겠습니다.


**공격적인 주행 패턴**

분할된 차량 신호 및 진단 데이터가 Hive를 사용하여 "AggresiveDrivingPatternPipeline"이라는 파이프라인에서 처리되어 공격적인 주행 패턴을 나타내는 모델, 위치, 차량 및 주행 상태 등의 매개 변수를 결정합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *그림 20 – 공격적인 주행 패턴 워크플로*

공격적인 주행 조건 패턴을 분석하는 데 사용된 "aggresivedriving.hql"이라는 하이브 스크립트는 다운로드한 zip의 "\\demo\\src\\connectedcar\\scripts" 폴더에 있습니다.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*그림 21 – 공격적인 주행 패턴 하이브 쿼리*

차량의 트랜스미션 기어 위치, 브레이크 페달 상태 및 속도의 조합을 사용하여 고속에서 브레이크 패턴에 따라 난폭하고 공격적인 주행 동작을 감지합니다.

파이프라인이 성공적으로 실행되면 "connectedcar" 컨테이너 아래 저장소 계정에 다음 파티션이 생성된 것을 확인할 수 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*그림 22 - AggressiveDrivingPatternPipeline 출력*


**연료 효율이 좋은 주행 패턴**

분할된 차량 신호 및 진단 데이터가 'FuelEfficientDrivingPatternPipeline'이라는 파이프라인에서 처리됩니다. Hive를 사용하여 연료 효율이 좋은 주행 패턴을 나타내는 모델, 위치, 차량, 주행 상태 및 기타 속성을 결정합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*그림 23 - 연료 효율이 좋은 주행 패턴 워크플로*

공격적인 주행 조건 패턴을 분석하는 데 사용된 "fuelefficientdriving.hql"이라는 하이브 스크립트는 다운로드한 zip의 "\\demo\\src\\connectedcar\\scripts" 폴더에 있습니다.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*그림 24 - 연료 효율이 좋은 주행 패턴 하이브 쿼리*

차량의 트랜스미션 기어 위치, 브레이크 페달 상태, 속도 및 가속 페달 위치의 조합을 사용하여 가속, 브레이크 및 속도 패턴에 따라 연료 효율이 좋은 주행 동작을 감지합니다.

파이프라인이 성공적으로 실행되면 "connectedcar" 컨테이너 아래 저장소 계정에 다음 파티션이 생성된 것을 확인할 수 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*그림 25 - FuelEfficientDrivingPatternPipeline 출력*


**회수 예측**

기계 학습 실험은 솔루션 배포의 일부인 웹 서비스로 프로비전 및 게시됩니다. 일괄 처리 점수 매기기 끝점이 이 워크플로에 활용되고 데이터 팩터리 연결된 서비스로 등록되며 데이터 팩터리 일괄 점수 매기기 활동을 사용하여 조작 가능해집니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*그림 26 - 데이터 팩터리의 연결된 서비스로 등록된 기계 학습 끝점*

등록된 연결된 서비스가 DetectAnomalyPipeline에 사용되며 이상 감지 모델을 사용하여 데이터 점수를 매깁니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*그림 27 – 데이터 팩터리에서 Azure 기계 학습 일괄 점수 매기기 활동*

일괄 점수 매기기 웹 서비스로 조작이 가능하도록 이 파이프라인에서 데이터 준비를 위해 수행된 몇 가지 단계가 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*그림 28 - 회수가 필요한 차량을 예측하기 위한 DetectAnomalyPipeline*

점수 매기기가 완료되면 모델에 의해 확률 점수 0.60 이상으로 이상(anomalies)으로 분류된 데이터를 HDInsight 작업을 사용하여 처리 및 집계합니다.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'


파이프라인이 성공적으로 실행되면 "connectedcar" 컨테이너 아래 저장소 계정에 다음 파티션이 생성된 것을 확인할 수 있습니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*그림 30 – DetectAnomalyPipeline 출력*


## 게시

### 실시간 분석

스트림 분석 작업에 있는 쿼리 중 하나가 이벤트를 출력 이벤트 허브 인스턴스에 게시합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*그림 31 - 출력 이벤트 인스턴스에 게시하는 스트림 분석 작업*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*그림 32 - 출력 이벤트 인스턴스에 게시하는 스트림 분석 쿼리*

이 이벤트 스트림은 솔루션에 포함된 RealTimeDashboardApp이 사용합니다. 이 응용 프로그램은 실시간 점수 매기기를 위한 기계 학습 요청-응답 웹 서비스를 활용하여 사용을 위해 결과 데이터를 PowerBI 데이터 집합에 게시합니다.

### 일괄 분석

일괄 및 실시간 처리의 결과가 사용을 위해 Azure SQL 데이터베이스 테이블에 게시됩니다. Azure SQL Server, 데이터베이스 및 테이블이 설치 스크립트의 일부로 자동으로 만들어집니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*그림 33 - 데이터 마트로 일괄 처리 결과 복사 워크플로*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*그림 34 - 데이터 마트로 게시하는 스트림 분석 작업*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*그림 35 - 스트림 분석 작업에서 데이터 마트 설정*


## 사용

Power BI는 실시간 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공합니다.

PowerBI 보고서 및 대시보드 설정에 관한 자세한 지침을 보려면 여기를 클릭하세요. 최종 대시보드는 다음과 유사하게 표시됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*그림 36 - PowerBI 대시보드*

## 요약

이 문서는 차량 원격 분석 솔루션에 대한 자세한 드릴다운을 포함됩니다. 예측 및 동작과 함께 실시간 및 일괄 분석을 위한 람다 아키텍처 패턴을 설명합니다. 이 패턴은 실행 부하 과다 경로(실시간) 및 콜드 경로(일괄 처리) 분석이 필요한 다양한 사용 사례에 적용됩니다.

<!---HONumber=AcomDC_0914_2016-->