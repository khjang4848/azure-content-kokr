<properties
 pageTitle="HDInsight | Microsoft Azure의 Apache Storm을 사용하여 차량 센서 데이터 처리 "
 description="HDInsight의 Apache Storm을 사용하여 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다. DocumentDB에서 모델 데이터를 추가하고 저장소에 대한 출력을 저장합니다."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다.

HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 차량 센서 데이터를 처리하는 방법에 대해 알아봅니다. 이 예제에서는 Azure 이벤트 허브에서 센서 데이터를 읽고, Azure DocumentDB에 저장된 데이터를 참조하여 데이터를 보강하고, 마지막으로 HDFS(Hadoop File System)를 사용하여 Azure 저장소에 데이터를 저장합니다.

![HDInsight 및 IoT(사물 인터넷) 아키텍처 다이어그램](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##개요

차량에 센서를 추가하면 과거 데이터 추세를 기반으로 장비 문제를 예측하고 사용 패턴 분석에 따라 향후 버전을 개선할 수 있습니다. 기존 MapReduce 일괄 처리를 이 분석에 사용할 수 있지만 MapReduce 처리가 발생하려면 먼저 모든 차량의 데이터를 Hadoop으로 빠르고 효율적으로 로드할 수 있어야 합니다. 또한 중요한 고장 경로(엔진 온도, 브레이크 등)에 대한 분석을 실시간으로 수행할 수 있습니다.

Azure 이벤트 허브는 센서에서 생성되는 방대한 양의 데이터를 처리하도록 빌드되며, HDInsight의 Apache Storm은 추가 MapReduce 처리를 위해 HDFS(Azure 저장소에서 지원됨)에 저장하기 전에 데이터를 로드 및 처리하는 데 사용될 수 있습니다.

##해결 방법

엔진 온도, 주변 온도 및 차량 속도에 대한 원격 분석 데이터는 센서에서 기록된 후 차량의 VIN(차량 식별 번호) 및 타임스탬프와 함께 이벤트 허브로 전송됩니다. 그러면 HDInsight의 Apache Storm에서 실행되는 Storm 토폴로지가 데이터를 읽고 처리한 후 HDFS에 저장합니다.

처리하는 동안 VIN은 Azure DocumentDB에서 모델 정보를 검색하는 데 사용됩니다. 이 값은 저장되기 전에 데이터 스트림에 추가됩니다.

Storm 토폴로지에서 사용되는 구성 요소는 다음과 같습니다.

* **EventHubSpout** - Azure 이벤트 허브에서 데이터를 읽습니다.

* **TypeConversionBolt** - 이벤트 허브의 JSON 문자열을 엔진 온도, 대기 온도, 속도, VIN 및 타임스탬프에 대한 개별 데이터 값이 포함된 튜플로 변환합니다.

* **DataReferencBolt** - VIN을 사용하여 DocumentDB에서 차량 모델을 조회합니다.

* **WasbStoreBolt** - HDFS(Azure 저장소)에 데이터를 저장합니다.

다음은 이 솔루션의 다이어그램입니다.

![Storm 토폴로지](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] 이는 간소화된 다이어그램이며, 솔루션의 각 구성 요소에 여러 인스턴스가 있을 수 있습니다. 예를 들어 토폴로지의 각 구성 요소에 대한 여러 인스턴스는 HDInsight 클러스터의 Storm 내 노드 간에 분산됩니다.

##구현

이 시나리오에 대한 전체 자동화된 솔루션은 GitHub에서 [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) 리포지토리의 일부로 제공됩니다. 이 예제를 사용하려면 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)의 단계를 따르세요.

## 다음 단계

Storm 토폴로지에 대한 자세한 내용은 [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->