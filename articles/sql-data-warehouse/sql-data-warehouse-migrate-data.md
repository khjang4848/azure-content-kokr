<properties
   pageTitle="SQL 데이터 웨어하우스로 데이터 마이그레이션| Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스로 데이터를 마이그레이션하기 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# 데이터 마이그레이션
다양한 도구를 사용하여 다양한 원본의 데이터를 SQL 데이터 웨어하우스로 이동할 수 있습니다. 이 작업을 위해 ADF Copy, SSIS 및 bcp를 모두 사용할 수 있습니다. 그러나 데이터 크기가 증가하면 단계별로 데이터 마이그레이션 프로세스 세분화를 고려해야 합니다. 부드러운 데이터 마이그레이션이 되도록 성능 및 복원 모두를 위한 각 단계를 최적화하는 기회를 제공합니다.

이 문서에서는 먼저 ADF Copy, SSIS 및 bcp의 간단한 마이그레이션 시나리오를 설명합니다. 그런 다음 마이그레이션이 최적화되는 방법을 좀 더 깊게 살펴봅니다.

## Azure 데이터 팩터리(ADF) 복사본
[ADF 복사본][]은 [Azure 데이터 팩터리][]의 일부입니다. SQL 데이터 웨어하우스로 직접 또는 Azure blob 저장소에 있는 원격 플랫 파일, 로컬 저장소에 상주하는 플랫 파일로 데이터를 내보내는 데 ADF 복사본을 사용할 수 있습니다.

데이터가 플랫 파일에서 생성되는 경우 SQL Data Warehouse로 데이터 로드를 시작하기 전에 먼저 Azure Storage Blob에 데이터를 전송해야 합니다. 데이터가 Azure blob 저장소로 전송되면 [ADF 복사본][]을 다시 사용하도록 선택하여 SQL 데이터 웨어하우스로 데이터를 푸시합니다.

PolyBase는 데이터 로드를 위한 고성능 옵션도 제공합니다. 그러나 하나 대신 두 개의 도구를 사용하는 것을 의미합니다. 최상의 성능이 필요한 경우 PolyBase를 사용합니다. 단일 도구 환경을 원하는 경우(및 데이터가 크지 않음) ADF가 답입니다.

> [AZURE.NOTE] PolyBase를 사용하려면 데이터 파일이 UTF-8이어야 합니다. 변경된 것이 아무 것도 없으므로 ADF 복사본의 기본 인코딩입니다. ADF 복사본의 기본 동작을 변경하지 말라고 알리는 것일 뿐입니다.

일부 훌륭한 [ADF 샘플][]에 대한 다음 문서를 읽어 보세요.

## Integration Services ##
Integration Services(SSIS)는 강력하고 유연한 변환 및 로드(ETL) 도구로, 복잡한 워크플로, 데이터 변환 및 여러 데이터 로드 옵션을 지원합니다. SSIS를 사용하여 광범위한 마이그레이션의 일부로 또는 Azure로 데이터를 전송합니다.

> [AZURE.NOTE] SSIS는 파일에 바이트 순서 표시가 없이 UTF-8로 내보낼 수 있습니다. 구성하려면 먼저 파생된 열 구성 요소를 사용하여 데이터 흐름의 문자 데이터를 변환하고 65001 UTF-8 코드 페이지를 사용해야 합니다. 열이 변환되면, 데이터를 플랫 파일 대상 어댑터에 작성하여 65001이 파일의 코드 페이지로 선택됩니다.

SSIS는 SQL Server 배포에 연결한 것처럼 SQL 데이터 웨어하우스에 연결합니다. 그러나 ADO.NET 연결 관리자를 사용하여 수를 연결해야 합니다. 또한 주의해서 "사용 가능한 경우 대량 삽입 사용" 설정을 구성하여 처리량을 최대화합니다. 이 속성에 대해 자세히 알아보려면 [ADO.NET 대상 어댑터][] 문서를 참조하세요.

> [AZURE.NOTE] OLEDB를 사용한 Azure SQL 데이터 웨어하우스 연결은 지원되지 않습니다.

또한 제한 또는 네트워크 문제로 인해 패키지가 실패할 수 있는 가능성은 항상 있습니다. 실패하기 전에 완료된 작업을 다시 실행하지 않고 실패 지점에서 재개할 수 있도록 패키지를 디자인합니다.

자세한 내용은 [SSIS 설명서][]를 참조하세요.

## bcp
bcp는 플랫 파일 데이터 가져오기 및 내보내기를 위해 설계된 명령줄 유틸리티입니다. 일부 변환은 데이터를 내보내는 중에 수행될 수 있습니다. 수행하려면 간단한 변환은 쿼리를 사용하여 데이터를 선택하고 변환합니다. 내보낸 후 플랫 파일이 직접 대상 SQL 데이터 웨어하우스 데이터베이스로 로드될 수 있습니다.

> [AZURE.NOTE] 대개 원본 시스템의 보기에서 데이터 내보내기 중에 사용되는 변환을 캡슐화하는 것이 좋습니다. 이렇게 하면 논리가 유지되며 과정이 반복됩니다.

bcp의 이점은 다음과 같습니다.

- 단순성입니다. bcp 명령은 간단하게 작성되고 실행됩니다.
- 다시 시작 가능한 로드 프로세스입니다. 일단 내보내면, 임의의 횟수만큼 로드를 실행할 수 있습니다.

bcp의 제한 사항은 다음과 같습니다.

- bcp는 탭 형식의 플랫 파일에만 작동합니다. xml 또는 JSON 등의 파일에서는 작동하지 않습니다.
- bcp는 UTF-8로 내보내기를 지원하지 않습니다. bcp 내보낸 데이터에서 PolyBase를 사용하지 못할 수 있습니다.
- 데이터 변환 기능은 내보내기 단계로만 제한되며 기본적으로 단순합니다.
- bcp는 인터넷을 통해 데이터를 로드할 때 강력한 것으로 조정되지 않았습니다. 모든 네트워크 불안정으로 로드 오류가 발생할 수 있습니다.
- bcp는 로드하기 전에 대상 데이터베이스에 표시되는 스키마를 사용합니다.

자세한 내용은 [bcp를 사용하여 SQL 데이터 웨어하우스로 데이터 로드][]를 참조하세요.

## 데이터 마이그레이션 최적화
불연속 세 단계로 SQLDW 데이터 마이그레이션 프로세스를 효과적으로 나눌 수 있습니다.

1. 원본 데이터의 내보내기
2. Azure에 데이터 전송
3. 대상 SQLDW 데이터베이스로 로드

단계마다 성능을 최대화하는 강력하고 다시 시작 가능하며 복원력 있는 마이그레이션 프로세스를 만들도록 각 단계를 개별적으로 최적화할 수 있습니다.

## 데이터 로드 최적화
잠시 반대 순서로 살펴보면 데이터를 로드하는 가장 빠른 방법은 PolyBase를 통해서입니다. PolyBase 로드 프로세스를 최적화하는 경우 이전 단계에서 필수 구성 요소를 충족해야 하므로, 이 작업은 미리 숙지해 두는 것이 좋습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

1. 데이터 파일의 인코딩
2. 데이터 파일의 형식
3. 데이터 파일의 위치

### 인코딩
PolyBase를 사용하려면 데이터 파일이 UTF-8로 인코딩되어야 합니다. 이 데이터를 내보낼 때 이 요구 사항을 준수해야 함을 의미합니다. 데이터가 기본 ASCII 문자(확장 ASCII가 아닌)만을 함한 경우, UTF-8 표준으로 직접 맵핑되어 인코딩에 대해 많이 염려할 필요가 없습니다. 그러나 데이터가 움라우트, 악센트나 기호와 같은 특수 문자를 포함하거나 데이터가 비라틴 언어를 지원하는 경우, 내보내기 파일이 UTF-8로 제대로 인코딩되었는지 확인해야 합니다.

> [AZURE.NOTE] bcp는 데이터를 UTF-8로 내보내기를 지원하지 않습니다. 따라서 데이터 내보내기에 대해 Integration Services 또는 ADF 복사본 중 하나를 사용하는 것이 가장 좋은 방법입니다. UTF-8 바이트 순서 표시(BOM)가 데이터 파일에는 필요하지 않습니다.

UTF-16을 사용하여 인코딩된 모든 파일은 데이터 전송 ***이전***에 다시 작성해야 합니다.

### 데이터 파일의 형식
PolyBase는 \\n 또는 새 줄의 고정된 행 종결자를 규정합니다. 데이터 파일은 이 표준을 준수해야 합니다. 문자열 또는 열 종결자에 제한이 없습니다.

PolyBase에서 외부 테이블의 일부로 파일에서 모든 열을 정의해야 합니다. 내보낸 모든 열이 필요하고 해당 형식은 필요한 표준을 준수해야 합니다.

지원되는 데이터 형식은 [스키마 마이그레이션] 문서를 다시 참조하세요.

### 데이터 파일의 위치
SQL 데이터 웨어하우스는 PolyBase를 사용하여 Azure blob 저장소에 데이터를 독점적으로 로드합니다. 따라서 데이터가 먼저 blob 저장소로 전송되어야 합니다.

## 데이터 전송 최적화
데이터 마이그레이션의 느린 부분 중 하나는 Azure에 데이터를 전송하는 것입니다. 네트워크 대역폭 문제가 발생할 수 뿐만 아니라 네트워크 안정성 진행률 심각하게 방해가 될 수도 있습니다. 기본적으로 데이터를 Azure로 마이그레이션하는 전송 오류 발생 가능성 합리적으로 가능성이 있으므로 인터넷을 통해 됩니다. 그러나 이러한 오류는 데이터를 전부 또는 일부를 다시 보내어야 할 수 있습니다.

다행히 속도 및 이 프로세스의 복원력을 향상시키는 여러 옵션이 있습니다.

### [Express 경로][]
[Express 경로][]를 사용하여 전송의 속도를 높일 수 있습니다. [Express 경로][]는 Azure에 대한 개인 연결을 설정하므로 공용 인터넷을 통해 연결되지 않습니다. 이 단계는 필수 단계는 아닙니다. 그러나 온-프레미스 또는 공동 배치 위치에서 데이터를 Azure로 푸시하는 경우, 처리량이 향상됩니다.

[Express 경로][] 사용의 이점은 다음과 같습니다.

1. 향상된 안정성
2. 더 빨라진 네트워크 속도
3. 줄어든 네트워크 대기 시간
4. 강화된 네트워크 보안

[Express 경로][]는 마이그레이션뿐만이 아니라 다양한 시나리오에 대해 효과적입니다.

관심이 있나요? 자세한 내용 및 가격은 [Express 경로 설명서][]를 참조하세요.

### Azure 가져오기 및 내보내기 서비스
Azure 가져오기 및 내보내기 서비스는 큰(GB++) 데이터에서 대용량(TB++) 데이터까지 Azure로 전송하기 위해 설계된 데이터 전송 프로세스입니다. 디스크에 데이터 작성 및 Azure 데이터 센터에 배송이 포함됩니다. 사용자를 대신해 Azure Storage Blob에 디스크 콘텐츠가 로드됩니다.

아래에는 가져오기/내보내기 프로세스가 대략적으로 표시되어 있습니다.

1. 데이터를 수신하도록 Azure Blob 저장소 컨테이너를 구성합니다.
2. 로컬 저장소로 데이터 내보내기
2. [Azure 가져오기/내보내기 도구]를 사용하여 3.5 인치 SATA II/III 하드 디스크 드라이브에 데이터 복사
3. [Azure 가져오기/내보내기 도구]에서 생성된 저널 파일을 제공하는 Azure 가져오기 및 내보내기 서비스를 사용하여 가져오기 작업 만들기
4. 지정된 Azure 데이터 센터로 디스크 배송
5. Azure Blob 저장소 컨테이너로 데이터 전송
6. PolyBase를 사용하여 SQLDW로 데이터 로드

### [AZCopy][] 유틸리티
[AZCopy][] 유틸리티는 Azure 저장소 Blob으로 데이터를 전송하기 위한 훌륭한 도구입니다. 작은 데이터 전송(MB++)에서부터 매우 큰 데이터 전송(GB++)까지 설계되어 있습니다. [AZCopy]도 좋은 복원력 있는 처리량 등 Azure에 데이터를 전송 하는 경우에 데이터 전송 단계에 대한 훌륭한 선택이 제공하도록 설계되었습니다. 한 번 전송되면, PolyBase를 사용하여 SQL 데이터 웨어하우스로 데이터를 로드할 수 있습니다. 또한 "프로세스 실행" 작업을 사용하여 SSIS 패키지로 AZCopy를 통합할 수 있습니다.

AZCopy를 사용하려면 먼저 다운로드하고 설치해야 합니다. [프로덕션 버전][] 및 [미리 보기 버전][]을 사용할 수 있습니다.

파일 시스템에서 파일을 업로드하려면 아래와 같은 명령이 필요합니다.

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

이 프로세스는 대략적으로 다음과 같이 요약할 수 있습니다.

1. 데이터를 수신하도록 Azure 저장소 Blob 컨테이너 구성
2. 로컬 저장소로 데이터 내보내기
3. Azure Blob 저장소 컨테이너의 데이터 AZCopy
4. PolyBase를 사용하여 SQL 데이터 웨어하우스로 데이터 로드

사용 가능한 전체 설명서는 [AZCopy][]입니다.

## 데이터 내보내기 최적화
내보내기가 PolyBase에서 설계한 배치 요구 사항에 맞는지 확인하는 것 외에도 데이터 내보내기를 최적화하여 프로세스가 더 향상되도록 시도할 수도 있습니다.

> [AZURE.NOTE] PolyBase를 사용하려면 데이터가 UTF-8 형식이어야 하므로 bcp를 사용하여 데이터 내보내기를 수행합니다. bcp는 UTF-8로의 데이터 파일 출력을 지원하지 않습니다. SSIS 또는 ADF 복사본은 이러한 종류의 데이터 내보내기를 수행하는 데 훨씬 더 적합합니다.

### 데이터 압축
PolyBase는 gzip 압축된 데이터를 읽을 수 있습니다. gzip 파일로 데이터를 압축할 수 있는 경우 네트워크를 통해 푸시되는 데이터의 양을 최소화합니다.

### 여러 파일
대형 테이블을 여러 파일로 분할하면 내보내기 속도를 향상시킬 수 뿐만 아니라 전송을 다시 시작 및 Azure blob 저장소에서의 전반적인 데이터 관리 효율성에도 도움이 됩니다. PolyBase의 여러 유용한 기능 중 하나는 폴더 내 모든 파일을 읽고 하나의 테이블로 처리된다는 점입니다. 따라서 각 테이블의 파일을 자체 폴더로 격리하는 것이 좋습니다.

또한 PolyBase는 "재귀적 폴더 이동"이라는 기능을 지원합니다. 내보낸 데이터의 조직의 데이터 관리를 개선하도록 이 기능을 사용할 수 있습니다.

PolyBase 사용한 데이터 로드에 대해 자세히 알려면 [PolyBase를 사용하여 SQL 데이터 웨어하우스로 데이터 로드][]를 참조하세요.


## 다음 단계
마이그레이션에 대한 자세한 내용은 [SQL 데이터 웨어하우스로 솔루션 마이그레이션][]을 참조하세요. 더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[ADF 복사본]: ../data-factory/data-factory-data-movement-activities.md
[ADF 샘플]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[개발 개요]: sql-data-warehouse-overview-develop.md
[SQL 데이터 웨어하우스로 솔루션 마이그레이션]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[bcp를 사용하여 SQL 데이터 웨어하우스로 데이터 로드]: sql-data-warehouse-load-with-bcp.md
[PolyBase를 사용하여 SQL 데이터 웨어하우스로 데이터 로드]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure 데이터 팩터리]: http://azure.microsoft.com/services/data-factory/
[Express 경로]: http://azure.microsoft.com/services/expressroute/
[Express 경로 설명서]: http://azure.microsoft.com/documentation/services/expressroute/

[프로덕션 버전]: http://aka.ms/downloadazcopy/
[미리 보기 버전]: http://aka.ms/downloadazcopypr/
[ADO.NET 대상 어댑터]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS 설명서]: https://msdn.microsoft.com/library/ms141026.aspx

<!---HONumber=AcomDC_0831_2016-->