<properties
 pageTitle="HDInsight에서 Apache Storm 토폴로지 예제 | Microsoft Azure"
 description="이벤트 허브와 함께 작동하며 기본 C# 및 Java 토폴로지를 포함하여 HDInsight에서 Apache Storm을 통해 생성 및 테스트된 Storm 토폴로지 예제 목록입니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# HDInsight의 Apache Storm에 대한 Storm 토폴로지 및 구성 요소 예제

다음은 HDInsight에서 Apache Storm과 함께 사용하기 위해 Microsoft에서 만들고 유지 관리하는 예제 목록입니다. 이러한 예제에서는 기본 C# 및 Java 토폴로지 만들기부터 이벤트 허브, DocumentDB, Power BI, SQL 데이터베이스, HDInsight의 HBase, Azure 저장소 등의 Azure 서비스 작업에 이르는 다양한 항목을 다룹니다. 또한 일부 예제에서는 SignalR 및 Socket.IO와 같은 Azure가 아니거나 Microsoft가 아닌 기술로 작업하는 방법을 보여 줍니다.

| 설명 | 데모 | 언어/프레임워크 |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Apache Storm에서 Azure 데이터 레이크 저장소에 쓰기](hdinsight-storm-write-data-lake-store.md) | Azure 데이터 레이크 저장소에 쓰기 | Java |
| [이벤트 허브 Spout 및 Bolt 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | 이벤트 허브 Spout 및 Bolt에 대한 소스 | Java |
| [HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지 개발][5797064f] | Maven | Java |
| [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발][16fce2d1] | Visual Studio용 HDInsight 도구 | C#, Java |
| [C# Storm 토폴로지에서 여러 데이터 스트림 만들기][ec5a4064] | 여러 스트림 | C# |
| [HDInsight에서 Storm을 사용하여 Twitter 추세 항목 확인][3c86c7c8] | Trident | Java, Trident |
| [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리][844d1d81] | 이벤트 허브(영문) | C# 및 Java |
| [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)](hdinsight-storm-develop-java-event-hub-topology.md) | 이벤트 허브(영문) | Java |
| [Power BI를 사용하여 Storm 토폴로지에서 데이터 시각화][94d15238] | Power BI | C# |
| [Storm 및 HDInsight의 HBase를 사용하여 센서 데이터 분석][ab894747] | 이벤트 허브, HBase, Socket.IO, 웹 대시보드 | C#, Java, JavaScript, HTML |
| [HDInsight의 Storm을 사용하여 이벤트 허브에서 차량 센서 데이터 처리][246ee964] | 이벤트 허브, DocumentDb, Azure 저장소 Blob(WASB) | C#, Java |
| [HDInsight의 Storm을 사용하여 Azure 이벤트 허브에서 HBase로 ETL(추출, 변환 및 로드)][b4b68194] | 이벤트 허브, HBase | C# |
| [HDInsight의 Storm에서 Azure 서비스로 작업하는 데 사용되는 템플릿 C# Storm 토폴로지 프로젝트][ce0c02a2] | 이벤트 허브, DocumentDb, SQL 데이터베이스, HBase, SignalR | C#, Java |
| [HDInsight의 Storm을 사용하여 Azure 이벤트 허브에서 읽기에 대한 확장성 벤치마크][d6c540e3] | 메시지 처리량, 이벤트 허브, SQL 데이터베이스 | C#, Java |
| [HDInsight에서 Storm 및 HBase를 사용하여 이벤트의 상관 관계 지정](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [HDInsight에서 Storm으로 Python 사용](hdinsight-storm-develop-python-topology.md) | Storm 토폴로지 기반 Java 및 Clojure를 사용하여 Python 구성 요소 | Python |

## 다음 단계

* [HDInsight에서 Apache Storm 시작][2b8c3488]

* [HDInsight의 Storm을 사용하여 Storm 토폴로지를 배포 및 관리하는 방법에 대해 알아봅니다.][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "HDInsight 클러스터에서 Storm을 만들고 Storm Dashboard를 사용하여 예제 토폴로지를 배포하는 방법에 대해 알아봅니다."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "배포 및 웹 기반 Storm 대시보드 및 Storm UI 또는 HDInsight 도구를 사용 하 여 Visual Studio에 대 한 토폴로지를 관리하는 방법에 알아봅니다."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "HDInsight Tools for Visual Studio를 사용하여 C# Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
  [5797064f]: hdinsight-storm-develop-java-topology.md "기본 단어 개수 토폴로지를 만들어 Maven을 사용하여 Java로 Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
  [94d15238]: hdinsight-storm-power-bi-topology.md "C# 토폴로지에서 Power BI에 데이터를 기록한 다음 데이터에서 차트 및 대시보드를 만드는 방법을 보여 줍니다."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "C#으로 구현된 단어 개수를 수행하는 기본 Storm 토폴로지를 보여 줍니다. 또한 C# 토폴로지 내에서 여러 데이터 스트림을 만드는 방법을 보여 줍니다."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "HDInsight의 Storm을 사용하여 Azure 이벤트 허브에서 데이터를 읽고 쓰는 방법에 대해 알아봅니다."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 센서 데이터를 처리하고, D3.js를 사용하여 시각화하고, 필요한 경우 HBase에 저장하는 방법에 대해 알아봅니다."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Twitter에서 추세 항목(해시 태그에 따라)을 결정하는 Storm 토폴로지를 만들기 위해 Trident를 사용 하는 방법에 대해 알아봅니다."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Storm 토폴로지를 사용하여 Azure 이벤트 허브에서 메시지를 읽고, 데이터 참조를 위해 Azure DocumentDB에서 문서를 읽고, Azure 저장소에 데이터를 저장하는 방법에 대해 알아봅니다."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 읽고 SQL 데이터베이스에 저장할 때의 처리량을 보여 주는 여러 토폴로지입니다."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Azure 이벤트 허브에서 데이터를 읽고, 데이터를 집계 및 변환한 다음, HDInsight의 HBase에 저장하는 방법에 대해 알아봅니다."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "이 프로젝트에는 이벤트 허브, DocumentDB 및 SQL 데이터베이스와 같은 다양한 Azure 서비스와 상호 작용하는 토폴로지에 대한 Spout, Bolt 및 템플릿이 포함되어 있습니다."
 

<!---HONumber=AcomDC_0914_2016-->