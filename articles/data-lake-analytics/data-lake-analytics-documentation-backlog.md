<properties
   pageTitle="Azure 데이터 레이크 분석 설명서 백로그 | Microsoft Azure"
   description="데이터 레이크 분석은 Azure 빅 데이터 계산 서비스로, 이를 통해 위치 및 크기에 관계없이 클라우드의 데이터에서 얻은 통찰력을 통해 데이터를 비즈니스 운영에 사용할 수 있습니다. 데이터 레이크 분석을 사용하면 가장 간단하고 확장성이 뛰어나면서도 가장 경제적인 방법으로 이 작업을 수행할 수 있습니다. 이 페이지는 당사 설명서 작성에 대한 백로그입니다. "
   documentationCenter="na"
   services="data-lake-analytics"
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Azure 데이터 레이크 분석 설명서 백로그

Azure 데이터 레이크 분석을 서비스 개발로 지원하고 일반 사용 사례 및 옵션에 대해 중점적으로 설명하는 새 문서가 계속 추가되고 있습니다. 이 페이지에서는 현재 계획된 설명서를 나열합니다. 이 문서에 의견 추가, GitHub에서 끌어오기 요청 제출 또는 메일을 보내는 방법을 통해 어떤 곳에 초점을 맞추었으면 좋겠는지에 대한 의견을 제시할 수 있습니다.

## 설명서 백로그

영역 |항목 | 우선 순위 | 가동 상태 | 할당 대상 | 요청자 
------------- | ------------- | -------------- | -------------- | --------------- | --------------
부여 | 콘텐츠 재설계 | 1 | 진행 중 | [@edmacauley](https://github.com/edmacauley) |
U-SQL | 분산 쿼리 | 1 | 보류 | [@edmacauley](https://github.com/edmacauley) |
U-SQL | WASB와 ADLS 간의 데이터 이동 | 1 | Pending | tbd | Ye Xing(ml)
U-SQL | UDO 모델에 대한 단위 테스트 지침 | 1 | Pending | tbd | [@ddobric](https://github.com/ddobric) 
U-SQL | JSON 처리 | 2 | Pending | [@mumian](https://github.com/mumian) | [@mwinkle](https://github.com/mwinkle)
부여 | Curl 조작 | 3 |Pending |[@mumian](https://github.com/mumian)
U-SQL | 분할된 테이블 |1 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | 성능 튜닝 | 2 | Pending | [@MikeRys](https://github.com/mikerys) | 
U-SQL | U-SQL UDO 프로그래머 가이드 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | U-SQL 프로그래머 가이드 | 2 | Pending | [@edmacauley](https://github.com/edmacauley) | 
U-SQL | T-SQL 백그라운드에서 U-SQL 시작 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) | [@MikeRys](https://github.com/mikerys)
U-SQL | Hive 백그라운드에서 U-SQL 시작 | 1 | Pending | [@mumian](https://github.com/mumian)| [@MikeRys](https://github.com/mikerys)
U-SQL | 문법 레일로드 다이어그램 | 1 | 진행 중, [이 웹 사이트](http://bottlecaps.de/rr/ui) 사용 | [@mwinkle](https://github.com/mwinkle) | [@mwinkle](https://github.com/mwinkle)
부여 | 데이터 레이크 분석의 작업, 데이터 및 테이블 보안 |1| 진행 중 | [@mumian](https://github.com/mumian) | [@MikeRys](https://github.com/mikerys)
부여 | Data Factory를 사용하여 작업 출력을 SQL 데이터 웨어하우스로 이동 |1 | 진행 중 | [@mumian](https://github.com/mumian) | 
도구 | 진단 도구 사용 | 1 | Pending | [@mumian](https://github.com/mumian) | 
U-SQL | 범위 기반 JOIN을 수행 하기 위한 기술 설명 | 1 | Pending | [@MikeRys](https://github.com/mikerys) |[@saveenr](https://github.com/saveenr) 
U-SQL | ADLS 인증 옵션 | 1 | Pending | [@ArindamC](https://github.com/mikerys) |[@saveenr](https://github.com/saveenr) 
U-SQL | 플랫폼 간 CLI 작업 제출 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@mwinkle](https://github.com/mwinkle) 
U-SQL | 감소 및 결합에 대한 참조 문서 업데이트 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@MikeRys](https://github.com/mikerys)
도구 | Visual Studio용 U-SQL UDF Azure Data Lake 도구의 단위 테스트 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@xiaoyong](https://github.com/zxzxy1988)
도구 | Visual Studio용 Azure Data Lake 도구의 작업 그래프 및 정점 실행 보기 | 1 | Pending | [@edmacauley](https://github.com/edmacauley) |[@xiaoyong](https://github.com/zxzxy1988)



## 백로그에서 작성된 문서

영역 |항목 | location
------------- | ------------- | -------------- 
부여 | ARM 템플릿을 사용하여 데이터 레이크 분석의 배포를 다른 Azure 서비스와 조정 | [위치](data-lake-analytics-manage-use-powershell.md)

## 백로그에서 피드백을 제공하는 방법
백로그에서 피드백을 제공하는 몇 가지 옵션이 있습니다.

* 아래에 의견 추가
* [Azure 콘텐츠 리포지토리](https://github.com/Azure/azure-content/blob/master/articles/data-lake-analytics/data-lake-analytics-documentation-backlog.md)에서 이 문서에 대한 끌어오기 요청 제출
* [microsoft.com의 adlafeedback](mailto:adlafeedback@microsoft.com?subject=DocBacklog)으로 메일 보내기

<!---HONumber=AcomDC_0914_2016-->