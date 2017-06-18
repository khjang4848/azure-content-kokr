<properties 
   pageTitle="Azure 검색 서비스의 사용량 및 통계 모니터링 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
   description="Microsoft Azure에서 호스팅되는 검색 서비스인 Azure 검색에 대해 리소스 소비 및 인덱스 크기를 추적합니다." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="jhubbard" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="05/17/2016"
   ms.author="heidist"/>

# Azure 검색의 사용량 및 통계 모니터링

인덱스 및 문서 크기의 증가를 추적하면 서비스에 규정한 상한에 도달하기 전에 용량을 조정할 수 있습니다.

[Azure 포털](https://portal.azure.com)에서는 서비스에 대한 리소스 사용량, 횟수 및 통계의 모니터링을 쉽게 확인할 수 있지만, 사용자 지정 서비스 관리 도구를 빌드하는 경우에는 정보 프로그래밍 방식으로 확인할 수도 있습니다. 이 문서는 두 기술 모두에 대한 단계를 설명합니다.

또한 새 검색 트래픽 분석 기능을 사용하여 인덱스 수준에서 작업에 대한 정보를 얻을 수 있습니다. 시작하려면 [Azure 검색을 위한 검색 트래픽 분석](search-traffic-analytics.md)을 방문하세요.

##포털의 뷰 횟수 및 메트릭 

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. Azure 검색 서비스의 서비스 대시보드를 엽니다. 홈페이지에서 서비스에 대한 타일을 찾거나 표시줄에 있는 찾아보기에서 서비스를 탐색할 수 있습니다. [서비스 만들기](search-create-service-portal.md)의 단계별 지침을 참조하십시오.

사용 섹션에는 사용 가능한 리소스의 어떤 부분이 현재 사용 중인지 알려주는 측정기가 포함됩니다.

  ![][1]

공유 서비스는 각각 최대 하나의 복제본과 파티션을 가집니다. 또한 총 10,000개의 문서와 데이터 50MB 중 먼저인 것만 지원합니다.

##REST API를 사용하여 인덱스 통계 가져오기

Azure 검색 REST API와 .NET SDK는 모두 서비스 메트릭에 대한 프로그래밍 방식 액세스를 제공합니다. [인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx)를 사용하여 Azure SQL 데이터베이스나 DocumentDB에서 인덱스를 로드하는 경우, 추가 API를 사용하여 필요한 숫자를 얻을 수 있습니다.

  + [인덱스 통계 가져오기](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [문서 수 계산](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [인덱서 상태 가져오기](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## 다음 단계

기존 용량이 충분하지 않은 경우 [제한 및 용량](search-limits-quotas-capacity.md)을 검토하여 필요한 파티션과 복제본의 조합을 확인합니다.

서비스 관리에 대한 자세한 내용은 [Microsoft Azure에서 검색 서비스 관리](search-manage.md)를 방문하십시오.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 

<!---HONumber=AcomDC_0914_2016-->