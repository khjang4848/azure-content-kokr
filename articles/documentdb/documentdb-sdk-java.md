<properties 
	pageTitle="DocumentDB Java API 및 SDK | Microsoft Azure" 
	description="릴리스 날짜, 사용 중지 날짜 및 DocumentDB Java SDK의 각 버전 간의 변경 내용을 포함하는 Java API 및 SDK에 대한 모든 것을 알아봅니다." 
	services="documentdb" 
	documentationCenter="java" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# DocumentDB API 및 SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.JS](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST (영문)](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## DocumentDB Java API 및 SDK

<table>
<tr><td>**SDK 다운로드**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**API 설명서**</td><td>[Java API 참조 설명서](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**SDK에 참여해 보세요.**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**시작**</td><td>[Java SDK 시작](documentdb-java-application.md)</td></tr>
<tr><td>**현재 지원되는 런타임**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## 릴리스 정보

### <a name="1.8.1"/>[1\.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - PartitionKeyDefinitionMap에서 버그를 수정하여 단일 파티션 컬렉션을 캐시하고 추가 인출 파티션 키를 요청하지 않았습니다.
  - 잘못된 파티션 키 값이 제공되는 경우 버그가 다시 시도되지 않도록 수정했습니다.

### <a name="1.8.0"/>[1\.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
  - 최대 재시도 횟수와 최대 재시도 대기 시간을 지정하는 옵션과 함께 정제된 요청의 자동 재시도 지원이 추가되었습니다. RetryOptions와 ConnectionPolicy.getRetryOptions()을 참조하세요.
  - IPartitionResolver 기반 사용자 지정 파티셔닝 코드의 사용이 중단되었습니다. 보다 큰 저장소 및 처리량에는 파티션된 컬렉션을 사용하세요.

### <a name="1.7.1"/>[1\.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- 제한에 대한 재시도 정책 지원이 추가되었습니다.

### <a name="1.7.0"/>[1\.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="1.6.0"/>[1\.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- [분할된 컬렉션](documentdb-partition-data.md) 및 [사용자 정의 성능 수준](documentdb-performance-levels.md)이 구현되었습니다.

### <a name="1.5.1"/>[1\.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- 다른 SDK와 일치하도록 little-endian의 해시 값을 생성하는 HashPartitionResolver의 버그를 수정합니다.

### <a name="1.5.0"/>[1\.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- 여러 파티션 간의 응용 프로그램 분할을 지원하기 위해 해시 및 범위 파티션 해결 프로그램을 추가합니다.

### <a name="1.4.0"/>[1\.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Upsert를 구현합니다. 새로운 upsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
- ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="1.3.0"/>1.3.0
- 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="1.2.0"/>[1\.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 지리 공간 인덱스 지원
- 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 ?, /, #, \\, 문자를 포함하거나 공백으로 끝날 수 없습니다.
- ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name="1.1.0"/>[1\.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- V2 인덱싱 정책 구현

### <a name="1.0.0"/>[1\.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## 릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용한 DocumentDB에 대한 요청은 서비스로부터 거부됩니다.

> [AZURE.WARNING]
**1.0.0** 이전 버전의 Java에 대한 모든 버전의 Azure DocumentDB SDK는 **2016년 2월 29일**에 사용 중지됩니다.

<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 
| ---	  | ---	         | ---
| [1.8.1](#1.8.1) | 2016년 6월 30일 |---
| [1.8.0](#1.8.0) | 2016년 6월 14일 |---
| [1.7.1](#1.7.1) | 2016년 4월 30일 |---
| [1.7.0](#1.7.0) | 2016년 4월 27일 |---
| [1.6.0](#1.6.0) | 2016년 3월 29일 |---
| [1.5.1](#1.5.1) | 2015년 12월 10일 |--- 
| [1\.5.0](#1.5.0) | 2015년 12월 4일 |---
| [1\.4.0](#1.4.0) | 2015년 10월 5일 |---
| [1\.3.0](#1.3.0) | 2015년 10월 5일 |---
| [1\.2.0](#1.2.0) | 2015년 8월 5일 |---
| [1\.1.0](#1.1.0) | 2015년 7월 9일 |---
| [1\.0.1](#1.0.1) | 2015년 3월 15일 |---
| [1\.0.0](#1.0.0) | 2015년 4월 7일 |---
| 0.9.5-prelease | 2015년 3월 9일 | 2016년 2월 29일
| 0.9.4-prelease | 2015년 2월 17일 | 2016년 2월 29일
| 0.9.3-prelease | 2015년 1월 13일 | 2016년 2월 29일
| 0.9.2-prelease | 2014년 12월 19일 | 2016년 2월 29일
| 0.9.1-prelease | 2014년 12월 19일 | 2016년 2월 29일
| 0.9.0-prelease | 2014년 12월 10일 | 2016년 2월 29일

## FAQ
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 참고 항목

DocumentDB에 대해 자세히 알아보려면 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 서비스 페이지를 참조하세요.

<!----HONumber=AcomDC_0810_2016-->
