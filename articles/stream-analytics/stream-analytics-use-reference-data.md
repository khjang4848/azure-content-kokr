<properties
	pageTitle="스트림 분석에서 참조 데이터 및 조회 테이블 사용 | Microsoft Azure"
	description="스트림 분석 쿼리에서 참조 데이터 사용"
	keywords="조회 테이블, 참조 데이터"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>

# 스트림 분석 입력 스트림에서 참조 데이터 또는 조회 테이블 사용

참조 데이터(조회 테이블이라고도 함)는 정적이거나 느리게 변경되는 특성을 지닌 한정된 데이터 집합으로, 데이터 스트림을 조회하거나 상관 관계를 지정하는 데 사용됩니다. Azure 스트림 분석 작업에서 참조 데이터를 사용하려면 일반적으로 쿼리에서 [참조 데이터 조인](https://msdn.microsoft.com/library/azure/dn949258.aspx)을 사용합니다. 스트림 분석에서는 참조 데이터에 대한 저장소 계층으로 Azure Blob 저장소를 사용하고 Azure 데이터 팩터리를 통해 참조 데이터로 사용하기 위해 참조 데이터를 [개수에 관계 없이 클라우드 기반 및 온-프레미스 데이터 저장소](../data-factory/data-factory-data-movement-activities.md) 형태로 Azure Blob 저장소로 변환 및/또는 복사할 수 있습니다. 참조 데이터는 BLOB 이름에서 지정한 날짜/시간의 오름차순에 따라 BLOB의 시퀀스(입력 구성에서 정의)로 모델링됩니다. 시퀀스의 마지막 BLOB에서 지정한 것보다 **이후인** 날짜/시간을 사용하여 시퀀스의 마지막에 추가하는 것**만** 지원됩니다.

## 참조 데이터 구성

참조 데이터를 구성하려면 먼저 **참조 데이터** 형식의 입력을 만들어야 합니다. 다음 표에서 설명과 함께 참조 데이터 입력을 만드는 동안 제공해야 하는 각 속성을 설명합니다.

<table>
<tbody>
<tr>
<td>속성 이름</td>
<td>설명</td>
</tr>
<tr>
<td>입력 별칭</td>
<td>이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.</td>
</tr>
<tr>
<td>저장소 계정</td>
<td>Blob 파일이 위치한 저장소 계정의 이름입니다. 스트림 분석 작업과 동일한 구독에 있으면 드롭다운에서 선택할 수 있습니다.</td>
</tr>
<tr>
<td>저장소 계정 키</td>
<td>저장소 계정과 연결된 비밀 키입니다. 저장소 계정이 스트림 분석 작업과 동일한 구독에 있으면 자동으로 채워집니다.</td>
</tr>
<tr>
<td>저장소 컨테이너</td>
<td>컨테이너는 Microsoft Azure Blob 서비스에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Blob 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다.</td>
</tr>
<tr>
<td>경로 패턴</td>
<td>지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로. 경로 내에서 다음 두 변수의 인스턴스 중 하나 이상을 지정하도록 선택할 수도 있습니다.<BR>{date}, {time}<BR>예 1: products/{date}/{time}/product-list.csv<BR>예 2: products/{date}/product-list.csv
</tr>
<tr>
<td>날짜 형식[선택 사항]</td>
<td>지정한 경로 패턴 내에서 {date}를 사용한 경우 파일이 구성되는 날짜 형식을 지원되는 형식 드롭다운에서 선택할 수 있습니다. 예: YYYY/MM/DD</td>
</tr>
<tr>
<td>시간 형식[선택 사항]</td>
<td>지정한 경로 패턴 내에서 {time}을 사용한 경우 파일이 구성되는 시간 형식을 지원되는 형식 드롭다운에서 선택할 수 있습니다. 예: HH</td>
</tr>
<tr>
<td>이벤트 직렬화 형식</td>
<td>쿼리가 예상대로 작동하는지 확인하려면 스트림 분석은 들어오는 데이터 스트림으로 사용 중인 직렬화 형식을 알고 있어야 합니다. 참조 데이터에 대해 지원되는 형식은 CSV 및 JSON입니다.</td>
</tr>
<tr>
<td>인코딩</td>
<td>지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.</td>
</tr>
</tbody>
</table>

## 일정에 따라 참조 데이터 생성

참조 데이터가 느리게 변경되는 데이터 집합인 경우 {date} 및 {time} 대체 토큰을 사용하여 입력 구성의 경로 패턴을 지정하여 참조 데이터 새로 고침 지원을 사용하도록 설정할 수 있습니다. 스트림 분석이 이 경로 패턴에 따라 업데이트된 참조 데이터 정의를 선택합니다. 예를 들어 **“YYYY-MM-DD”**의 날짜 형식 및 **“HH:mm”**의 시간 형식의 `sample/{date}/{time}/products.csv` 패턴은 표준 시간대 2015년 4월 16일 오후 5시 30분에 스트림 분석이 업데이트된 Blob `sample/2015-04-16/17:30/products.csv`를 선택하도록 지시합니다.

> [AZURE.NOTE] 현재 스트림 분석 작업은 컴퓨터 시간이 Blob 이름에 인코딩된 시간으로 진행하는 경우에만 Blob 새로 고침을 찾습니다. 예를 들어 작업은 가능한 빨리 `sample/2015-04-16/17:30/products.csv`을(를) 찾지만 표준 시간대 2015년 4월 16일 오후 5시 30분보다 이르지 않습니다. 발견된 마지막 파일보다 이전에 인코딩된 시간으로 파일을 찾지 *않습니다*.
> 
> 예를 들어 작업이 Blob `sample/2015-04-16/17:30/products.csv`을(를) 발견하면 2015년 4월 16일 오후 5시 30분 이전에 인코딩된 날짜의 모든 파일을 무시합니다. 따라서 늦게 도착하는 `sample/2015-04-16/17:25/products.csv` Blob이 동일한 컨테이너에 만들어지는 경우 작업은 이를 사용하지 않습니다.
> 
> 마찬가지로 `sample/2015-04-16/17:30/products.csv`이(가) 2015년 4월 16일 오후 10시 3분에 생성되었지만 컨테이너에 이전 날짜의 Blob이 없는 경우 작업은 2015년 4월 16일 오후 10시 3분에 시작하는 이 파일을 사용하고 그때까지 이전 참조 데이터를 사용합니다.
> 
> 이에 대한 예외는 시간을 거슬러 데이터를 재처리해야 하는 작업이거나 작업을 최초로 시작할 때입니다. 시작 시점에 작업은 지정된 작업 시작 시간 이전에 생성된 가장 최근 Blob를 찾습니다. 이렇게 하면 작업을 시작할 때 **비어 있지 않은** 참조 데이터가 설정됩니다. 찾을 수 없는 경우 작업은 다음 진단 `Initializing input without a valid reference data blob for UTC time <start time>`을(를) 표시합니다.


스트림 분석에서 참조 데이터 정의를 업데이트하는 데 필요한 업데이트된 Blob를 만드는 작업을 오케스트레이션하는 데 [Azure 데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/)를 사용할 수 있습니다. 데이터 팩터리는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 데이터 팩터리는 [많은 수의 클라우드 기반 및 온-프레미스 데이터 저장소 연결](../data-factory/data-factory-data-movement-activities.md)을 지원하고 사용자가 지정한 정기적인 일정으로 데이터를 쉽게 이동할 수 있도록 지원합니다. 미리 정의된 일정에 따라 새로 고쳐지는 스트림 분석을 위한 참조 데이터를 생성하는 데이터 팩터리 파이프라인 설정 방법에 대한 단계별 지침과 자세한 내용은 이 [GitHub 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)을 확인하세요.

## 참조 데이터 새로고침 팁 ##

1. 참조 데이터 BLOB를 덮어쓰면 스트림 분석이 해당 BLOB를 다시 로드하지 않으며 경우에 따라 작업이 실패할 수 있습니다. 참조 데이터를 변경할 때는 작업 입력에서 정의된 것과 동일한 컨테이너 및 경로 패턴을 사용하여 새 BLOB를 추가하고 시퀀스의 마지막 BLOB에서 지정한 것보다 **나중인** 날짜/시간을 사용하는 것이 좋습니다.
2.	참조 데이터 BLOB는 BLOB의 “마지막 수정" 시간 순서가 아니라 {date} 및 {time}을 대체하여 BLOB 이름에서 지정한 날짜와 시간으로만 순서가 지정됩니다.
3.	드물지만 작업의 시간을 되돌려야 할 경우가 있으므로 참조 데이터 BLOB를 변경 또는 삭제해서는 안 됩니다.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 사용해 보세요.

## 다음 단계
사물 인터넷에서 발생한 데이터에 대한 스트리밍 분석용 관리 서비스, 스트림 분석에 대해 소개하였습니다. 이 서비스에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0921_2016-->