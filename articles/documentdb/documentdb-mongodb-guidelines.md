<properties 
	pageTitle="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB에 대한 개발 지침 미리 보기 | Microsoft Azure" 
	description="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB에 대한 개발 지침 미리 보기에 대해 알아봅니다. 지금 미리 보기로 제공됩니다." 
	services="documentdb" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="anhoh"/>

# MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB에 대한 개발 지침

오픈 소스 MongoDB 클라이언트 [드라이버](https://docs.mongodb.org/ecosystem/drivers/)를 통해 Azure DocumentDB와 통신할 수 있습니다. MongoDB에 대한 프로토콜 지원에서는 MongoDB 클라이언트 드라이버가 MongoDB 2.6 이상 서버 끝점과 통신하고 있다고 가정합니다. DocumentDB는 MongoDB [프로토콜 연결](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/) 버전 2.6에 따라 이를 지원합니다.

DocumentDB는 핵심 MongoDB API 함수가 데이터베이스를 쿼리할 뿐만 아니라 데이터를 만들고 읽고 업데이트하고 삭제(CRUD)할 수 있습니다. 공통 플랫폼, 프레임워크, 도구 및 응용 프로그램 패턴의 요구에 따라 구현된 기능의 우선 순위를 지정했습니다.

## 컬렉션

> [AZURE.IMPORTANT] DocumentDB는 컬렉션 수준에서 예약된 처리량을 사용하여 보장되고 예측 가능한 성능을 제공합니다. 따라서 DocumentDB의 컬렉션은 청구 가능한 엔터티입니다.

응용 프로그램이 시스템에서 가장 낮은 수준인 데이터 컨테이너로 성능을 조정할 수 있도록 컬렉션 수준에서 성능 예약을 적용합니다. 따라서 컬렉션의 가격은 소비된 총 저장소(기가바이트)와 함께 초당 요청 단위로 측정되는 컬렉션의 프로비전된 처리량에 의해 결정됩니다. 프로비전된 처리량은 변화하는 처리 요구에 맞고 응용 프로그램의 패턴에 액세스하는 컬렉션의 수명 동안 조정할 수 있습니다. 자세한 내용은 [DocumentDB 성능 수준](documentdb-performance-levels.md)을 참조하세요.

MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB는 기본적으로 프로비전된 처리량이 1,000RU/s인 표준 가격 책정 계층에서 만들어집니다. [Azure 포털을 사용하여 성능 수준 변경](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal)에 설명된 대로 각 컬렉션의 프로비전된 처리량을 조정할 수 있습니다.

## CRUD 작업

MongoDB 삽입, 읽기, 업데이트, 바꾸기 및 삭제 작업은 완전히 지원됩니다. MongoDB [업데이트 연산자 사양](https://docs.mongodb.org/manual/reference/operator/update/)에 지정된 대로 필드, 배열, 비트 및 격리 업데이트에 대한 지원이 포함됩니다. 여러 문서를 조작해야 하는 업데이트 연산자의 경우 DocumentDB는 스냅샷 격리와 함께 전체 ACID 의미 체계를 제공합니다.

## 쿼리 작업

DocumentDB는 몇 가지 예외를 포함한 MongoDB 쿼리의 전체 문법을 지원합니다. MongoDB 날짜 시간 형식에 대한 지원에 추가하여 JSON 호환 [BSON 형식](https://docs.mongodb.org/manual/reference/bson-types/) 집합에서 작동되는 쿼리가 지원됩니다. JSON이 아닌 형식 특정 연산자를 요구하는 쿼리의 경우 DocumentDB는 GUID 데이터 형식을 지원합니다.

## 포털 환경
MongoDB 프로토콜이 활성화된 계정에 대한 Azure Portal 환경은 MongoDB 프로토콜이 활성화된 계정에 대해 제공됩니다. 환경을 확장하지만 포털 기능이 가장 많이 사용한 부분에 관한 [피드백](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience)이 필요합니다.

## 지원 매트릭스


### CRUD 및 쿼리 작업

기능|지원됨|지원될 예정임
---|---|---
삽입|InsertOne| 
 |InsertMany| 
 |삽입| 
업데이트|UpdateOne| 
 |UpdateMany| 
 |업데이트| 
필드 업데이트|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
배열 업데이트| |-all-
비트| |-all-
격리| |-all-
Replace|ReplaceOne| 
삭제|DeleteOne | 
 |DeleteMany| 
 |제거| 
BulkWrite| |bulkWrite()
비교|-all-| 
논리|-all-| 
요소 쿼리| |-all-
평가|$mod, $regex |$text, $where
GeoSpatial|2dsphere, 2d, polygon|기타 등등
Array|$all, $size, $elemMatch|
비트| |-all-
주석|-all-| 
프로젝션| |-all-


### 데이터베이스 명령

기능|지원됨|지원될 예정임
---|---|---
집계|개수| 
GeoSpatial| |-all-
쿼리 및 쓰기|찾기, 삽입, 업데이트, 삭제, getLastError, getMore, findAndModify| 
인증|getnonce, 로그아웃, 인증| 
관리|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| 
진단|listDatabases, collStats, dbStats| 

## 다음 단계

- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 있는 [MongoChef를 사용](documentdb-mongodb-mongochef.md)하는 방법을 알아봅니다.
- MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 DocumentDB를 탐색합니다.

 

<!---HONumber=AcomDC_0921_2016-->