<properties 
   pageTitle="Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리 | Azure" 
   description="데이터 레이크 분석 계정, 데이터 원본, 사용자, 작업을 관리하는 방법에 대해 알아봅니다." 
   services="data-lake-analytics" 
   documentationCenter="" 
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

# Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure 포털을 사용하여 Azure 데이터 레이크 분석 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법에 대해 알아봅니다. 다른 도구를 사용하여 관리 항목을 보려면 위의 탭 선택기를 클릭합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

<!-- ################################ -->
<!-- ################################ -->
## 계정 관리

데이터 레이크 분석 작업을 실행하려면 데이터 레이크 분석 계정이 있어야 합니다. Azure HDInsight와 달리 작업을 실행하지 않는 경우 분석 계정에 대해 비용을 지불하지 않습니다. 작업이 실행되는 시간에 대해서만 비용을 지불합니다. 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

**데이터 레이크 분석 계정을 만들려면**

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **새로 만들기**, **데이터 + 분석** 및 **데이터 레이크 분석**을 차례로 클릭합니다.
6. 다음 값을 입력하거나 선택합니다.

	![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **이름**: 분석 계정의 이름을 지정합니다.
	- **데이터 레이크 저장소**: 각 데이터 레이크 분석 계정에는 종속 Azure 데이터 레이크 저장소 계정이 있습니다. 데이터 레이크 분석 계정 및 종속 데이터 레이크 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다. 지침을 따라 새 데이터 레이크 저장소 계정을 만들거나 기존 계정을 선택합니다.
	- **구독**: 분석 계정에 사용할 Azure 구독을 선택합니다.
	- **리소스 그룹**. 기존 Azure 리소스 그룹을 선택하거나 리소스 그룹을 새로 만듭니다. Azure 리소스 관리자(ARM)를 사용하면 응용 프로그램에서 리소스를 그룹으로 사용할 수 있습니다. 자세한 내용은 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.
	- **위치**. 데이터 레이크 분석 계정에 대한 Azure 데이터 센터를 선택합니다.

8. **만들기**를 클릭합니다. 포털 홈 화면으로 이동됩니다. 새 타일이 "Azure 데이터 레이크 분석 배포" 레이블을 사용하여 시작 보드에 추가됩니다. 데이터 레이크 분석 계정을 만드는 데 몇 분 정도 걸립니다. 계정이 만들어지면 포털이 새 블레이드에 해당 계정을 엽니다.

	![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **데이터 레이크 분석 계정 액세스/열기**

1. 새로운 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **찾아보기**를 클릭하고 **데이터 레이크 분석**을 클릭합니다.
3. 액세스하려는 데이터 레이크 분석 계정을 클릭합니다. 새 블레이드에 계정이 열립니다.

**데이터 레이크 분석 계정 삭제**

1. 삭제할 데이터 레이크 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. 블레이드 위쪽의 단추 메뉴에서 **삭제**를 클릭합니다.
3. 계정 이름을 입력하고 **삭제**를 클릭합니다.

분석 계정을 삭제해도 종속 데이터 레이크 저장소 계정은 삭제되지 않습니다. 데이터 레이크 저장소 계정 삭제에 대한 자세한 내용은 [데이터 레이크 저장소 계정 삭제](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account)를 참조하세요.




























<!-- ################################ -->
<!-- ################################ -->
## 계정 데이터 원본 관리

데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

- [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
- [Azure 저장소](../storage/storage-introduction.md)

데이터 레이크 분석 계정을 만들 때 Azure 데이터 레이크 저장소 계정이 기본 저장소 계정이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 데이터 레이크 분석 계정을 만든 후 데이터 레이크 저장소 계정 및/또는 Azure 저장소 계정을 더 추가할 수 있습니다.

<a name="default-adl-account"></a>**기본 데이터 레이크 저장소 계정 찾기**

- 관리하려는 데이터 레이크 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요. 기본 데이터 레이크 저장소는 **Essential**에 표시됩니다.

	![Azure 데이터 레이크 분석 데이터 원본 추가](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**데이터 원본 더 추가하기**

1. 관리하려는 데이터 레이크 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. **설정**을 클릭한 다음 **데이터 원본**을 클릭합니다. 기본 데이터 레이크 저장소 계정이 나열됩니다.
3. **데이터 원본 추가**를 클릭합니다.

	![Azure 데이터 레이크 분석 데이터 원본 추가](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

	Azure 데이터 레이크 저장소 계정을 추가하려면 계정 이름이 필요하고 쿼리를 할 수 있으려면 계정에 대한 액세스가 필요합니다. Azure Blob 저장소를 추가하려면 저장소 계정 및 계정 키가 필요하며 이 정보는 포털에서 저장소 계정으로 이동하여 찾을 수 있습니다.

<a name="explore-data-sources"></a>**데이터 원본 탐색**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. **설정**을 클릭한 다음 **데이터 탐색기**를 클릭합니다.
 
	![Azure 데이터 레이크 분석 데이터 탐색기](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
	
3. 데이터 레이크 저장소 계정을 클릭하여 엽니다.

	![Azure 데이터 레이크 분석 데이터 탐색기 저장소 계정](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
	
	각 데이터 레이브 저장소 계정에 할 수 있는 작업은 다음과 같습니다.
	
	- **새 폴더**: 새 폴더를 추가합니다.
	- **업로드**: 사용자의 워크스테이션에서 저장소 계정에 파일을 업로드합니다.
	- **액세스**: 액세스 권한을 구성합니다.
	- **폴더 이름 바꾸기**: 폴더의 이름을 바꿉니다.
	- **폴더 속성**: WASB 경로, WEBHDFS 경로, 마지막 수정 시간 등과 같은 파일 또는 폴더 속성을 표시합니다.
	- **폴더 삭제**: 폴더를 삭제합니다.

<a name="upload-data-to-adls"></a> **데이터 레이크 저장소 계정에 파일 업로드**

1. 포털의 왼쪽 메뉴에서 **찾아보기**를 클릭한 후 **데이터 레이크 저장소**를 클릭합니다.
2. 데이터를 업로드할 데이터 레이크 저장소 계정을 클릭합니다. 기본 데이터 레이크 저장소 계정을 찾으려면 [여기](#default-adl-account)를 참조합니다.
3. 위쪽 메뉴에서 **데이터 탐색기**를 클릭합니다.
4. 새 폴더를 만들려면 **새 디렉터리**를 클릭하거나 폴더를 변경하려면 폴더 이름을 클릭합니다.
6. 파일을 업로드하려면 위쪽 메뉴에서 **업로드**를 클릭합니다.


<a name="upload-data-to-wasb"></a> **Azure Blob 저장소 계정에 파일 업로드**

[HDInsight에서 Hadoop 작업용 데이터 업로드](../hdinsight/hdinsight-upload-data.md)를 참조하세요. 데이터 레이크 분석에 적용되는 정보입니다.


## 사용자 관리

데이터 레이크 분석은 Azure Active Directory와 역할 기반 액세스 제어를 사용합니다. 데이터 레이크 분석 계정을 만들 때 "구독 관리자" 역할이 계정에 추가됩니다. 사용자와 다음과 같은 역할의 보안 그룹을 더 추가할 수 있습니다.

|역할|설명|
|----|-----------|
|소유자|리소스에 대한 액세스를 포함한 모든 것을 관리하도록 허용합니다.|
|참여자|포털에 액세스하고 작업을 제출하고 모니터링합니다. 작업을 제출할 수 있으려면 참여자 역시 데이터 레이크 저장소 계정에 대한 읽기 또는 쓰기 권한이 필요합니다.|
|DataLakeAnalyticsDeveloper | 작업을 제출하고, 모든 작업을 모니터링할 수 있지만 자신의 작업만 취소할 수 있습니다. 자신의 계정을 관리할 수 없습니다. 예를 들어 사용자 추가, 권한 변경 또는 계정 삭제를 할 수 없습니다. 작업을 실행할 수 있으려면 데이터 레이크 저장소 계정에 대한 읽기 또는 쓰기 액세스가 필요합니다. | 
|리더|모든 것을 볼 수 있지만 변경은 할 수 없습니다.|  
|DevTest Lab 사용자|모든 항목을 볼 수 있으며 가상 컴퓨터를 연결, 시작, 다시 시작 및 종료하도록 허용합니다.|  
|사용자 액세스 관리자|Azure 리소스에 대한 사용자 액세스를 관리할 수 있습니다.|  

Azure Active Directory 사용자 및 보안 그룹 만들기에 대한 자세한 내용은 [Azure Active Directory란?](../active-directory/active-directory-whatis.md)을 참조하세요.

**분석 계정에 사용자 또는 보안 그룹 추가**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. **설정**을 클릭한 다음 **사용자**를 클릭합니다. 다음 스크린샷에 표시된 것처럼 **Essentials** 제목 표시줄의 **액세스**를 클릭할 수도 있습니다.

	![Azure 데이터 레이크 분석 계정 사용자 추가](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. **사용자** 블레이드에서 **추가**를 클릭합니다.
4. 역할을 선택하고 사용자를 추가한 후 **확인**을 클릭합니다.

**참고: 이 사용자 또는 보안 그룹이 작업을 제출해야 하는 경우에는 데이터 레이크 저장소에 대한 권한이 부여되어야 합니다. 자세한 내용은 [데이터 레이크 저장소에 저장된 데이터 보호](../data-lake-store/data-lake-store-secure-data.md)를 참조하세요.**





<!-- ################################ -->
<!-- ################################ -->
## 작업 관리

모든 작업을 실행하기 전에 데이터 레이크 분석 계정이 있어야 합니다. 자세한 내용은 [데이터 레이크 분석 계정 관리](#manage-data-lake-analytics-accounts)를 참조하세요.

<a name="create-job"></a>**작업 만들기**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. **새 작업**을 클릭합니다.

	![Azure 데이터 레이크 분석 U-SQL 작업 만들기](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

	다음과 유사한 새 블레이드가 표시됩니다.

	![Azure 데이터 레이크 분석 U-SQL 작업 만들기](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

	각 작업에 대해 구성할 수 있는 내용은 다음과 같습니다.


	|이름|설명|
	|----|-----------|
	|작업 이름|작업의 이름을 입력합니다.|
	|우선 순위|번호가 낮을 수록 우선 순위가 높습니다. 두 작업이 모두 큐에 대기 중이면 우선 순위가 낮은 작업이 먼저 실행됩니다.|
	|병렬 처리 |동시에 발생할 수 있는 최대 계산 프로세스 수입니다. 이 번호를 높이면 성능이 향상될 수 있지만 비용도 증가할 수 있습니다.|
	|스크립트|작업에 대한 U-SQL 스크립트를 입력합니다.|

	동일한 인터페이스를 사용하여 연결된 데이터 원본을 탐색하고 연결된 데이터 원본에 파일을 추가할 수 있습니다.
3. 작업을 제출하려면 **작업 제출**을 클릭합니다.

**작업을 제출하려면**

[데이터 레이크 분석 작업 만들기](#create-job)를 참조하세요.

<a name="monitor-jobs"></a>**작업을 모니터링하려면**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요. 작업 관리 패널에 기본 작업 정보가 표시됩니다.

	![Azure 데이터 레이크 분석 U-SQL 작업 관리](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. 이전 스크린샷에 표시된 것처럼 **작업 관리**를 클릭합니다.

	![Azure 데이터 레이크 분석 U-SQL 작업 관리](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. 목록에서 작업을 클릭합니다. 또는 스스로 작업을 찾으려면 **필터**를 클릭합니다.

	![Azure 데이터 레이크 분석 U-SQL 작업 필터](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

	**시간 범위**, **작업 이름**, **작성자**를 조건으로 작업을 필터링할 수 있습니다.
5. 작업을 다시 제출하려면 **다시 제출**을 클릭합니다.

**작업을 다시 제출하려면**

[데이터 레이크 분석 작업 모니터링](#monitor-jobs)을 참조하세요.

##계정 사용 현황 모니터링

**계정 사용 현황을 모니터링하려면**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요. 계정 사용 현황 패널에는 사용 현황이 표시됩니다.

	![Azure 데이터 레이크 분석 사용 현황 모니터링](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. 자세한 내용을 보려면 창을 두 번 클릭합니다.

##U-SQL 카탈로그 보기

[U-SQL 카탈로그](data-lake-analytics-use-u-sql-catalog.md)는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드의 구조를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. Azure 포털을 통해 U-SQL 카탈로그를 볼 수 있습니다.

**U-SQL 카탈로그를 탐색하려면**

1. 관리하려는 분석 계정을 엽니다. 자세한 내용은 [데이터 레이크 분석 계정 액세스](#access-adla-account)를 참조하세요.
2. 위쪽 메뉴에서 **데이터 탐색기**를 클릭합니다.
3. **카탈로그**, **마스터**, **Tables**를 차례로 확장하거나 테이블 반환 함수 또는 **어셈블리**를 확장합니다. 다음 스크린샷은 테이블 반환 함수를 보여줍니다.

	![Azure 데이터 레이크 분석 데이터 탐색기 저장소 계정](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## Azure 리소스 관리자 그룹 사용

응용 프로그램은 일반적으로 웹앱, 데이터베이스, 데이터베이스 서버, 저장소 및 타사 서비스 등 많은 구성 요소로 구성됩니다. ARM(Azure 리소스 관리자)을 사용하면 Azure 리소스 그룹이라고 하는 그룹으로 응용 프로그램에서 리소스와 함께 사용할 수 있습니다. 응용 프로그램에 대한 모든 리소스의 배포, 업데이트, 모니터링 또는 삭제를 조정된 단일 작업으로 수행할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 전체 그룹에 대한 롤업 비용을 확인하여 조직에 요금 청구를 명확히 할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자 개요](../resource-group-overview.md)를 참조하세요.

데이터 레이크 분석 서비스는 다음 구성 요소를 포함할 수 있습니다.

- Azure 데이터 레이크 분석 계정
- 필수 기본 Azure 데이터 레이크 저장소 계정
- 추가 Azure 데이터 레이크 저장소 계정
- 추가 Azure 저장소 계정

이러한 모든 구성을 쉽게 관리할 수 있도록 하나의 ARM 그룹 아래 만들 수 있습니다.

![Azure 데이터 레이크 분석 계정 및 저장소](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

데이터 레이크 분석 계정 및 종속 저장소 계정은 동일한 Azure 데이터 센터에 있어야 합니다. 그러나 ARM 그룹은 다른 데이터 센터에 있을 수 있습니다.



##참고 항목 

- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-powershell.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->