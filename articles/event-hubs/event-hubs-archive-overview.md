<properties
	pageTitle="Azure Event Hubs 보관 | Microsoft Azure"
	description="Azure Event Hubs 보관 기능의 개요입니다."
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Azure Event Hubs 보관

Azure Event Hubs 보관을 사용하면 시간 또는 크기 간격을 선택 지정하여 더 유연하게 Event Hubs의 스트리밍 데이터를 선택한 Blob Storage 계정에 자동으로 전달할 수 있습니다. 보관은 빨리 설정할 수 있고 실행하는 데 관리 비용이 없으며 Event Hubs [처리량 단위](event-hubs-overview.md#capacity-and-security)의 크기를 자동으로 조정합니다. Event Hubs 보관은 스트리밍 데이터를 Azure에 로드하는 가장 쉬운 방법이며 데이터 캡처보다는 데이터 처리에 집중할 수 있습니다.

Azure Event Hubs 보관을 사용하면 동일한 스트림에서 실시간 및 일괄 처리 기반 파이프라인을 처리할 수 있습니다. 이렇게 하면 시간이 지나면서 요구 사항이 늘어날 수 있는 솔루션을 빌드할 수 있습니다. 향후 실시간 처리를 염두에 두고 현재 일괄 처리 기반 시스템을 구축하거나 기존 실시간 솔루션에 효율적인 콜드 경로를 추가하면 Event Hubs 보관을 통해 스트리밍 데이터 작업이 더 쉬워집니다.

## Event Hubs 보관의 작동 방식

Event Hubs는 원격 분석 수신에 대한 시간 보존 지속형 버퍼이며 분산된 로그와 비슷합니다. Event Hubs의 크기를 조정하는 키는 [분할된 소비자 모델](event-hubs-overview.md#partition-key)입니다. 각 파티션은 데이터의 독립적인 세그먼트이며 독립적으로 사용됩니다. 시간이 지나면서 이 데이터는 구성 가능한 보존 기간에 따라 에이징됩니다. 결과적으로 지정된 이벤트 허브는 "꽉 참" 상태가 되지 않습니다.

Event Hubs 보관을 사용하면 보관된 데이터를 저장하는 데 사용될 고유한 Azure Blob Storage 계정 및 Container를 지정할 수 있습니다. 이 계정은 이벤트 허브와 동일한 영역 또는 다른 영역에 있을 수 있으므로 Event Hubs 보관 기능이 보다 유연해집니다.

보관된 데이터는 [Apache Avro][] 형식으로 작성되며, 이는 인라인 스키마가 있는 다양한 데이터 구조를 제공하는 간단하고 빠른 이진 형식입니다. 이 형식은 Stream Analytics 및 Azure Data Factory뿐만 아니라 Hadoop 에코시스템에 널리 사용됩니다. Avro 작업에 대한 자세한 내용은 이 문서의 뒷부분을 참조하세요.

### 보관 기간 이동

Event Hubs 보관을 통해 보관을 제어하는 기간을 설정할 수 있습니다. 이 기간은 첫 번째 트리거에서 보관 작업이 발생되도록 하는 "첫 번째 우선 정책"이 포함된 최소 크기 및 시간 구성입니다. 15분/100MB 보관 기간을 사용하고 1MB/초로 전송할 경우 해당 크기 기간이 시간 기간 전에 트리거됩니다. 각 파티션은 보관 시점에 독립적으로 보관하고 완성된 블록 Blob을 작성합니다. 이는 보관 간격이 발생한 시간에 대해 명명됩니다. 명명 규칙은 다음과 같습니다.

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### 처리량 단위로 크기 조정

Event Hubs 트래픽은 [처리량 단위](event-hubs-overview.md#capacity-and-security)로 제어됩니다. 단일 처리량 단위는 1MB/초 또는 초당 1000개의 이벤트 수신을 허용하고 송신량은 그 두 배입니다. Standard Event Hubs는 1-20개의 처리량 단위로 구성할 수 있으며 할당량 증가 [지원 요청][]을 통해 더 구입할 수 있습니다. 구입한 처리량 단위 범위를 벗어나는 사용량은 제한됩니다. Event Hubs 보관은 처리량 단위 송신 할당량을 무시하고 Stream Analytics 또는 Spark와 같은 다른 처리 판독기에 대한 프로그램 송신을 저장하는 내부 Event Hubs 저장소에서 데이터를 직접 복사합니다.

Event Hubs 보관이 구성되면 첫 번째 이벤트를 전송하는 즉시 자동으로 실행됩니다. 계속해서 항상 실행됩니다. 다운스트림 프로세스가 제대로 작동하는지 쉽게 알 수 있도록 Event Hubs는 데이터가 없을 때 빈 파일을 작성합니다. Event Hubs는 일괄 처리 프로세서를 제공할 수 있는 표식 및 예측 가능한 주기를 제공합니다.

## Event Hubs 보관 설정

Event Hubs 보관은 이벤트 허브를 만들 때 포털 또는 Azure Resource Manager를 통해 구성할 수 있습니다. **켜기** 단추를 클릭하여 간단히 보관을 사용하도록 설정합니다. 블레이드의 **컨테이너** 섹션을 클릭하여 저장소 계정 및 컨테이너를 구성합니다. Event Hubs 보관은 저장소에서 서비스 간 인증을 사용하므로 저장소 연결 문자열을 지정할 필요가 없습니다. 리소스 선택기가 저장소 계정에 대한 리소스 URI를 자동으로 선택합니다. Azure Resource Manager를 사용하는 경우 이 URI를 문자열로 명백히 제공해야 합니다.

기본 시간은 5분입니다. 최소값은 1, 최대값은 15입니다. **크기**의 범위는 10-500MB입니다.

![][1]

## 기존 이벤트 허브에 보관 추가

보관은 Event Hubs 네임스페이스에 있는 기존 Event Hubs에 구성할 수 있습니다. 이 기능은 이전 메시징 또는 혼합 형식 네임스페이스에서 사용할 수 없습니다. 기존 이벤트 허브에서 보관을 사용하거나 보관 설정을 변경하려면 **필수** 블레이드를 로드할 네임스페이스를 클릭한 다음 보관 설정을 사용하거나 변경할 이벤트 허브를 클릭합니다. 마지막으로 다음 그림과 같이 열린 블레이드의 **속성** 섹션을 클릭합니다.

![][2]

Azure Resource Manager 템플릿을 통해 Event Hubs 보관을 구성할 수도 있습니다. 자세한 내용은 [이 문서](event-hubs-resource-manager-namespace-event-hub-enable-archive.md)를 참조하세요.

## 보관 탐색 및 Avro 작업

Event Hubs 보관이 구성되면 구성된 기간에 제공한 Azure Storage 계정 및 컨테이너에 파일을 만듭니다. [Azure Storage 탐색기][]와 같은 도구에서 이러한 파일을 볼 수 있습니다. 파일을 로컬로 다운로드하여 작업할 수 있습니다.

Event Hubs 보관에서 제공한 파일에는 다음 Avro 스키마가 있습니다.

![][3]

Avro 파일을 쉽게 탐색하는 방법은 Apache에서 [Avro 도구][] jar을 사용하는 것입니다. 이 jar을 다운로드한 후 다음 명령을 실행하여 특정 Avro 파일의 스키마를 확인할 수 있습니다.

```
java -jar avro-tools-1.8.1.jar getschema <name of archive file>
```

이 명령은 다음을 반환합니다.

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

또한 Avro 도구를 사용하여 JSON 형식으로 파일을 변환하고 다른 처리를 수행할 수 있습니다.

고급 처리를 수행하려면 선택한 플랫폼에 대한 Avro를 다운로드하고 설치합니다. 이 문서 작성 시점에는 C, C++, C#, Java, NodeJS, Perl, PHP, Python 및 Ruby에 사용 가능한 구현이 있습니다.

Apache Avro에는 [Java][] 및 [Python][]에 대한 전체 시작 가이드가 있습니다. [Event Hubs 보관 시작](event-hubs-archive-python.md) 문서를 참조할 수도 있습니다.

## Event Hubs 보관 요금 부과 방식

Event Hubs 보관은 시간당 요금과 마찬가지로 처리량 단위로 측정됩니다. 요금은 네임스페이스에 대해 구입한 처리량 단위 수에 정비례합니다. 처리량 단위가 증가 및 감소함에 따라 성능이 일치하도록 Event Hubs 보관도 증가 및 감소합니다. 측정은 동시에 발생합니다. Event Hubs 보관 요금은 처리량 단위에 시간당 $0.10이고 미리 보기 기간 동안 50% 할인 가격으로 제공됩니다.

Event Hubs 보관은 실제로 Azure로 데이터를 가져오는 가장 쉬운 방법입니다. Azure Data Lake, Azure Data Factory 및 Azure HDInsight를 통해 규모에 관계없이 친숙한 도구 및 플랫폼을 사용하여 선택한 기타 분석 및 일괄 처리를 수행할 수 있습니다.

## 다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

- [이벤트 허브를 사용하는 샘플 응용 프로그램][] 전체.
- [이벤트 허브를 사용하는 이벤트 처리 확장][] 샘플
- [이벤트 허브 개요][]

[Apache Avro]: http://avro.apache.org/
[지원 요청]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage 탐색기]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro 도구]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[이벤트 허브 개요]: event-hubs-overview.md
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[이벤트 허브를 사용하는 이벤트 처리 확장]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->