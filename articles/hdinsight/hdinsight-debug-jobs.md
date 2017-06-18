<properties
	pageTitle="HDInsight에서 Hadoop 디버그: 로그 보기 및 오류 메시지 해석 | Microsoft Azure"
	description="PowerShell을 사용하여 HDInsight를 관리할 때 표시될 수 있는 오류 메시지와 복구를 위해 수행할 수 있는 단계에 대해 알아봅니다."
	services="hdinsight"
	tags="azure-portal"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="jgao"/>

# HDInsight 로그 분석

Azure HDInsight의 Hadoop 클러스터 각각에는 기본 파일 시스템으로 사용되는 Azure 저장소 계정이 있습니다. 이 저장소 계정을 기본 저장소 계정이라고 합니다. 클러스터는 기본 저장소 계정의 Azure 테이블 저장소 및 Blob 저장소를 사용하여 로그를 저장합니다. 클러스터용 기본 저장소 계정을 알아보려면 [HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-management-portal.md#find-the-default-storage-account)를 참조하세요. 로그는 클러스터를 삭제한 후에 저장소 계정에 유지됩니다.

##Azure 테이블에 기록된 로그

Azure 테이블에 기록된 로그는 HDInsight 클러스터에 발생한 사항에 대한 일정한 통찰력을 제공합니다.

HDInsight 클러스터를 만들 때 6개 테이블은 기본 테이블 저장소에서 Linux 기반 클러스터에 대해 자동으로 만들어집니다.

- hdinsightagentlog
- syslog
- daemonlog
- hadoopservicelog
- ambariserverlog
- ambariagentlog

Windows 기반 클러스터에 3개의 테이블을 만듭니다.

- setuplog: HDInsight 클러스터를 프로비전/설정할 때 발생한 이벤트/예외의 로그입니다.
- hadoopinstalllog: 클러스터에 Hadoop를 설치하는 경우 발생한 이벤트/예외의 로그입니다. 이 테이블은 사용자 지정 매개 변수를 사용하여 만든 클러스터와 관련된 문제를 디버깅하는 데 유용할 수 있습니다.
- hadoopservicelog: 모든 Hadoop 서비스에 의해 기록된 이벤트/예외의 로그입니다. 이 테이블은 HDInsight 클러스터의 작업 실패와 관련된 문제를 디버깅하는 데 유용할 수 있습니다.

테이블 파일 이름은 **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**입니다.

이 테이블은 다음 필드를 포함합니다.

- ClusterDnsName
- ComponentName
- EventTimestamp
- 호스트
- MALoggingHash
- Message
- N
- PreciseTimeStamp
- 역할
- RowIndex
- 테넌트
- TIMESTAMP
- TraceLevel

### 로그에 액세스하기 위한 도구

이 테이블의 데이터에 액세스하기 위해 사용할 수 있는 여러 도구가 있습니다.

-  Visual Studio
-  Azure 저장소 탐색기
-  Excel용 파워 쿼리

#### Excel용 파워 쿼리 사용

파워 쿼리는 [www.microsoft.com/ko-KR/download/details.aspx?id=39379](http://www.microsoft.com/ko-KR/download/details.aspx?id=39379)에서 설치될 수 있습니다. 시스템 요구 사항은 다운로드 페이지를 참조하세요.

**파워 쿼리를 사용하여 서비스 로그 열기 및 분석**

1. **Microsoft Excel**을 엽니다.
2. **파워 쿼리** 메뉴에서 **Azure에서**를 클릭한 다음 **Microsoft Azure 테이블 저장소에서**를 클릭합니다.
 
	![HDInsight Hadoop Excel 파워 쿼리는 Azure 테이블 저장소를 엽니다](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. 저장소 계정 이름을 입력하세요. 이름은 짧은 이름 또는 FQDN일 수 있습니다.
4. 저장소 계정 키를 입력합니다. 테이블의 목록이 표시됩니다.

	![Azure 테이블 저장소에 저장된 HDInsight Hadoop 로그](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. **탐색기** 창에서 hadoopservicelog 테이블을 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다. 4개의 열이 표시됩니다. 필요에 따라 **파티션 키**, **행 키** 및 **타임스탬프** 열을 선택한 다음 리본 메뉴에 있는 **열 제거**를 클릭하여 옵션에서 삭제합니다.
6. 콘텐츠 열에서 확장 아이콘을 클릭하여 Excel 스프레드시트에 가져오려는 열을 선택합니다. 이 데모에서 TraceLevel 및 ComponentName을 선택한 경우 문제가 있는 구성 요소에 대한 일부 기본 정보를 줄 수 있습니다.

	![HDInsight Hadoop 로그는 열을 선택합니다](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. **확인**을 클릭하여 데이터를 가져옵니다.
8. **TraceLevel**, 역할 및 **ComponentName** 열을 선택한 다음 리본 메뉴에서 **그룹별** 컨트롤을 클릭합니다.
9. 그룹별 대화 상자에서 **확인**을 클릭합니다.
10. **적용 및 닫기**를 클릭합니다.
 
이제 Excel을 사용하여 필요에 따라 필터링하고 정렬할 수 있습니다. 물론, 문제가 발생할 때 문제를 드릴 다운하기 위해 다른 열(예: 메시지)을 포함하려하지만 위에서 설명한 열을 선택하고 그룹화하면 Hadoop 서비스를 사용하여 발생하는 사항에 훌륭한 그림을 제공합니다. Setuplog 및 hadoopinstalllog 테이블에 동일한 개념을 적용할 수 있습니다.

#### Visual Studio 사용

**Visual Studio를 사용하려면**

1. Visual Studio를 엽니다.
2. **보기** 메뉴에서 **클라우드 탐색기**를 클릭합니다. 또는 **CTRL+\\, CTRL+X**를 클릭하기만 하면 됩니다.
3. **클라우드 탐색기**에서 **리소스 유형**을 선택합니다. 사용 가능한 다른 옵션은 **리소스 그룹**입니다.
4. **저장소 계정**, 클러스터에 대한 기본 저장소 계정, **테이블**을 차례로 확장합니다.
5. **hadoopservicelog**를 두 번 클릭합니다.
6. 필터를 추가합니다. 예:
	
		TraceLevel eq 'ERROR'

	![HDInsight Hadoop 로그는 열을 선택합니다](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)

	필터를 생성하는 방법에 대한 자세한 내용은 [테이블 디자이너에 필터 문자열 생성](../vs-azure-tools-table-designer-construct-filter-strings.md)을 참조하세요.
 
##Azure Blob 저장소에 기록된 로그

[Azure 테이블에 기록된 로그](#log-written-to-azure-tables)는 HDInsight 클러스터에 발생한 사항에 대한 일정한 통찰력을 제공합니다. 그러나 이러한 테이블은 문제 발생 시에 추가 드릴에 도움이 될 수 있는 작업 수준의 로그를 제공하지 않습니다. 다음 수준의 세부 정보를 제공하려면 HDInsight 클러스터는 Templeton을 통해 제출된 작업에 대해 Blob 저장소 계정에 작업 로그를 기록하도록 구성됩니다. 즉, 실질적으로 클러스터에 대한 RDP/명령줄 액세스를 통해 제출된 작업이 아니라 Microsoft Azure PowerShell cmdlet 또는 .NET 작업 제출 API를 사용하여 작업을 제출합니다.

로그를 보려면 [Linux 기반 HDInsight에서 YARN 응용 프로그램 로그에 액세스](hdinsight-hadoop-access-yarn-app-logs-linux.md)를 참조하세요.

응용 프로그램 로그에 대한 자세한 내용은 [YARN에서 사용자 로그 관리 및 액세스 단순화](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)를 참조하세요.
 
 
## 클러스터 상태 및 작업 로그 보기

###Hadoop UI에 액세스

Azure 포털에서 HDInsight 클러스터 이름을 클릭하여 클러스터 블레이드를 엽니다. 클러스터 블레이드에서 **대시보드**를 클릭합니다.

![클러스터 대시보드 시작](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

메시지가 표시되면 클러스터 관리자 자격 증명을 입력합니다. 열리는 쿼리 콘솔에서 **Hadoop UI**를 클릭합니다.

![Hadoop UI 시작](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)


###Yarn UI에 액세스

Azure 포털에서 HDInsight 클러스터 이름을 클릭하여 클러스터 블레이드를 엽니다. 클러스터 블레이드에서 **대시보드**를 클릭합니다. 메시지가 표시되면 클러스터 관리자 자격 증명을 입력합니다. 열리는 쿼리 콘솔에서 **YARN UI**를 클릭합니다.

YARN UI를 사용하여 다음을 수행할 수 있습니다.

* **클러스터 상태를 가져옵니다**. 왼쪽 창에서 **클러스터**를 확장하고 **정보**를 클릭합니다. 이 현재 클러스터는 전체 할당된 메모리, 사용된 코어, 클러스터 리소스 관리자의 상태, 클러스터 버전 등을 자세히 설명합니다.

    ![클러스터 대시보드 시작](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

* **노드 상태를 가져옵니다**. 왼쪽 창에서 **클러스터**를 확장하고 **노드**를 클릭합니다. 클러스터의 모든 노드, 각 노드의 HTTP 주소, 각 노드에 할당된 리소스 등을 나열합니다.

* **작업 상태를 모니터링**합니다. 왼쪽 창에서 **클러스터**를 확장하고 **응용 프로그램**을 클릭하여 클러스터의 모든 작업을 나열합니다. 특정 상태(새로움, 제출됨, 실행 중과 같은)에 있는 작업을 확인하려면 **응용 프로그램**에서 적절한 링크를 클릭합니다. 더 자세한 내용을 보려면 작업 이름을 클릭하여 출력, 로그 등을 비롯하여 작업에 대해 자세히 확인할 수 있습니다.

###HBase UI에 액세스

Azure 포털에서 HDInsight HBase 클러스터 이름을 클릭하여 클러스터 블레이드를 엽니다. 클러스터 블레이드에서 **대시보드**를 클릭합니다. 메시지가 표시되면 클러스터 관리자 자격 증명을 입력합니다. 열리는 쿼리 콘솔에서 **HBase UI**를 클릭합니다.

## HDInsight 오류 코드

이 섹션의 항목별 오류 메시지를 통해 Azure HDInsight의 Hadoop 사용자는 Azure PowerShell을 사용하여 서비스를 관리할 때 발생할 수 있는 가능한 오류 상태에 대해 이해할 수 있으며, 오류를 복구하기 위해 수행할 수 있는 단계에 대해 도움을 받을 수 있습니다.

이러한 오류 메시지 중 일부는 Azure 포털에서 HDInsight 클러스터를 관리할 때도 발생할 수 있습니다. 하지만 이때 발생할 수 있는 다른 오류 메시지는 해당 컨텍스트에서 가능한 수정 작업의 제약 조건으로 인해 세분화하기 어렵습니다. 다른 오류 메시지는 뚜렷한 해결 방법이 있는 경우 제공됩니다.

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **설명**: Hive 및 Oozie 메타스토어에 사용자 지정 설정을 사용하려면 하나 이상의 구성 요소에 대한 Azure SQL 데이터베이스 세부 정보를 제공하세요.
- **해결 방법**: 사용자가 올바른 SQL Azure 메타스토어를 제공하고 요청을 다시 시도해야 합니다.

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **설명**: *nameOfYourRegion* 지역에서 클러스터를 만들 수 없습니다. 올바른 HDInsight 지역을 사용하여 요청을 다시 시도하세요.
- **해결 방법**: 고객이 현재 지원되는 클러스터 지역(동남아시아, 서유럽, 북유럽, 미국 동부 또는 미국 서부)을 만들어야 합니다.

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **설명**: 서버에서 요청된 클러스터 기록을 찾을 수 없습니다.
- **해결 방법**: 작업을 다시 시도하세요.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **설명**: 클러스터 DNS 이름 *yourDnsName*이 잘못되었습니다. 이름이 영숫자로 시작하고 끝나는지 확인하세요. 이름에는 '-' 특수 문자만 포함할 수 있습니다.
- **해결 방법**: 클러스터에 이름의 시작과 끝이 영숫자이고 대시('-') 이외의 특수 문자가 포함되지 않은 올바른 DNS 이름을 사용했는지 확인한 후 작업을 다시 시도하세요.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **설명**: *yourClusterName* 클러스터 이름을 사용할 수 없습니다. 다른 이름을 선택하세요.
- **해결 방법**: 사용자가 고유하며 기존에 존재하지 않던 클러스터 이름을 지정한 후 다시 시도해야 합니다. 사용자가 포털을 사용하는 경우 클러스터 이름을 만드는 단계에서 이미 사용 중인 이름을 선택하면 UI에서 이를 알려 줍니다.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **설명**: 클러스터 암호가 잘못되었습니다. 암호는 길이가 10자 이상이어야 하고, 숫자, 대문자, 소문자 및 특수 문자를 적어도 하나씩 포함해야 하고, 공백이 없어야 하며, 사용자 이름을 포함하지 않아야 합니다.
- **해결 방법**: 올바른 클러스터 암호를 지정한 후 작업을 다시 시도하세요.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **설명**: 클러스터 사용자 이름이 잘못되었습니다. 사용자 이름에 특수 문자 또는 공백이 포함되지 않았는지 확인하세요.
- **해결 방법**: 올바른 클러스터 사용자 이름을 지정한 후 작업을 다시 시도하세요.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **설명**: *yourDnsClusterName* 클러스터 DNS 이름이 잘못되었습니다. 이름이 영숫자로 시작하고 끝나는지 확인하세요. 이름에는 '-' 특수 문자만 포함할 수 있습니다.
- **해결 방법**: 올바른 DNS 클러스터 사용자 이름을 지정한 후 작업을 다시 시도하세요.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **설명**: URI *yourcontainerURI*의 컨테이너 이름과 요청 본문의 DNS 이름 *yourDnsName*이 동일해야 합니다.
- **해결 방법**: 컨테이너 이름과 DNS 이름이 동일한지 확인하고 작업을 다시 시도하세요.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **설명**: 클러스터 구성이 잘못되었습니다. 노드 크기에서 데이터 노드 정의를 찾을 수 없습니다.
- **해결 방법**: 작업을 다시 시도하세요.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **설명**: 클러스터에서 배포를 삭제하지 못했습니다.
- **해결 방법**: 삭제 작업을 다시 시도하세요.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **설명**: 서비스 구성 오류입니다. 필수 DNS 매핑 정보를 찾지 못했습니다.
- **해결 방법**: 클러스터를 삭제하고 새 클러스터를 만드세요.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **설명**: 클러스터 컨테이너 만들기 시도가 중복되었습니다. *컨테이너이름*의 레코드가 존재하지만 Etags가 일치하지 않습니다.
- **해결 방법**: 컨테이너에 고유한 이름을 지정한 후 만들기 작업을 다시 시도하세요.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **설명**: 호스티드 서비스 *nameOfYourHostedService*에 이미 클러스터가 포함되어 있습니다. 호스티드 서비스에는 여러 클러스터를 포함할 수 없습니다.
- **해결 방법**: 클러스터를 다른 호스티드 서비스에서 호스트하세요.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **설명**: 서버에서 클러스터 배포 상태를 업데이트할 수 없습니다.
- **해결 방법**: 작업을 다시 시도하세요. 이 오류가 여러 번 발생하는 경우 CSS에 문의하세요.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **설명**: *yourClusterName* 클러스터가 유지 관리 과정에서 삭제되었습니다. 클러스터를 다시 만드세요.
- **해결 방법**: 클러스터를 다시 만드세요.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **설명**: 클러스터 구성이 잘못되었습니다. 노드 크기에서 필수 헤드 노드 구성을 찾을 수 없습니다.
- **해결 방법**: 작업을 다시 시도하세요.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **설명**: 호스티드 서비스 *nameOfYourHostedService*을(를) 만들 수 없습니다. 요청을 다시 시도하세요.
- **해결 방법**: 요청을 다시 시도하세요.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **설명**: 호스티드 서비스 *nameOfYourHostedService*에 이미 프로덕션 배포가 있습니다. 호스티드 서비스에는 여러 프로덕션 배포를 포함할 수 없습니다. 다른 클러스터 이름으로 요청을 다시 시도하십시오.
- **해결 방법**: 다른 클러스터 이름을 사용하여 요청을 다시 시도하세요.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **설명**: 클러스터의 호스티드 서비스인 *nameOfYourHostedService*을(를) 찾을 수 없습니다.
- **해결 방법**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **설명**: *nameOfYourHostedService* 호스티드 서비스에 연결된 배포가 없습니다.
- **해결 방법**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **설명**: SubscriptionId인 *yourSubscriptionId*에 *yourClusterName* 클러스터를 만드는 데 필요한 코어가 남아 있지 않습니다. 필수: *resourcesRequired*, 사용 가능: *resourcesAvailable*.
- **해결 방법**: 구독에서 리소스를 해제하거나 구독에서 사용할 수 있는 리소스를 늘린 후 클러스터를 다시 만들어 보세요.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **설명**: 구독 ID *yourSubscriptionId*에 *yourClusterName* 클러스터를 만들 새 HostedService에 필요한 할당량이 없습니다.
- **해결 방법**: 구독에서 리소스를 해제하거나 구독에서 사용할 수 있는 리소스를 늘린 후 클러스터를 다시 만들어 보세요.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **설명**: 서버에 내부 오류가 발생했습니다. 요청을 다시 시도하세요.
- **해결 방법**: 요청을 다시 시도하세요.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **설명**: Azure 저장소 위치인 *dataRegionName*이(가) 올바른 위치가 아닙니다. 지역이 올바른지 확인하고 요청을 다시 시도하세요.
- **해결 방법**: HDInsight를 지원하는 저장소 위치를 선택하고 클러스터가 공동 배치되었는지 확인한 후 작업을 다시 시도하세요.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **설명**: 데이터 노드의 VM 크기가 잘못되었습니다. '모든 데이터 노드에서 큰 VM' 크기만 지원됩니다.
- **해결 방법Mitigation**: 데이터 노드에서 지원되는 노드 크기를 지정한 후 작업을 다시 시도하세요.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **설명**: 헤드 노드의 VM 크기가 잘못되었습니다. 헤드 노드에서는 '매우 큰 VM' 크기만 지원됩니다.
- **해결 방법**: 헤드 노드에서 지원되는 노드 크기를 지정하고 작업을 다시 시도하세요.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **설명**: 사용 중인 구독 ID *yourSubscriptionId*에 *yourClusterName* 클러스터에 대한 삭제 작업을 실행할 수 있는 충분한 권한이 없습니다.
- **해결 방법**: 클러스터가 오류 상태인 경우 클러스터를 삭제한 후 다시 시도하세요.

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **설명**: 외부 저장소 계정 blob 컨테이너 이름 *yourContainerName*이(가) 잘못되었습니다. 이름이 문자로 시작되며 이름에 소문자, 숫자 및 대시만 포함되어 있는지 확인하세요.
- **해결 방법**: 올바른 저장소 계정 blob 컨테이너 이름을 지정한 후 작업을 다시 시도하세요.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **설명**: 외부 저장소 계정 *yourStorageAccountName*의 구성에서 비밀 키 세부 정보가 설정되어 있어야 합니다.
- **해결 방법**: 저장소 계정에 올바른 비밀 키를 지정한 후 작업을 다시 시도하세요.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **설명**: 버전 헤더 *yourVersionHeader*이(가) 올바른 형식(yyyy-mm-dd)이 아닙니다.
- **해결 방법**: 버전 헤더에 올바른 형식을 지정한 후 요청을 다시 시도하세요.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **설명**: 클러스터 구성이 잘못되었습니다. 헤드 노드 구성이 두 개 이상 검색되었습니다.
- **해결 방법**: 하나의 헤드 노드만 지정하도록 구성을 편집하세요.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **설명**: 허용된 시간 또는 가능한 최대 재시도 횟수 내에 작업을 완료할 수 없습니다. 요청을 다시 시도하세요.
- **해결 방법**: 요청을 다시 시도하세요.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **설명**: *yourParameterName* 매개 변수는 Null이거나 비워 둘 수 없습니다.
- **해결 방법**: 매개 변수에 올바른 값을 지정하세요.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **설명**: 하나 이상의 클러스터 만들기 요청 입력이 잘못되었습니다. 입력 값이 올바른지 확인하고 요청을 다시 시도하세요.
- **해결 방법**: 입력 값이 올바른지 확인하고 요청을 다시 시도하세요.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **설명**: *yourRegionName* 지역 및 구독 ID *yourSubscriptionId*에 사용할 수 없는 지역 정보 값입니다.
- **해결 방법**: HDInsight 클러스터를 지원하는 지역을 지정하세요. 공식적으로 지정되는 지역은 동남아시아, 서유럽, 북유럽, 미국 동부 또는 미국 서부입니다.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **설명**: 저장소 계정 *yourStorageAccountName*이(가) *currentRegionName* 지역에 있습니다. 지역은 클러스터 지역인 *클러스터지역이름*과(와) 동일해야 합니다.
- **해결 방법**: 클러스터가 있는 지역과 동일한 지역에서 저장소 계정을 지정하거나, 사용 중인 데이터가 저장소 계정에 이미 존재하는 경우 기존 저장소 계정과 동일한 지역에서 새 클러스터를 만드세요. 포털을 사용하는 경우 UI에서 이 문제에 대해 사전에 알려 줍니다.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **설명**: 지정된 구독 ID *yourSubscriptionId*이(가) 활성이 아닙니다.
- **해결 방법**: 구독을 다시 활성화하거나 올바른 새 구독을 가져오세요.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **설명**: 구독 ID인 *yourSubscriptionId*을(를) 찾을 수 없습니다.
- **해결 방법**: 구독 ID가 올바른지 확인한 후 작업을 다시 시도하세요.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **설명**: DNS *yourDnsUrl*을(를) 확인할 수 없습니다. Blob 끝점에 정규화된 URL을 지정했는지 확인하세요.
- **해결 방법**: 올바른 Blob URL을 제공하세요. URL은 전체가 유효해야 하며, *http://*로 시작하고 *.com*으로 끝나야 합니다.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **설명**: *yourDnsUrl* 리소스의 위치를 확인할 수 없습니다. Blob 끝점에 정규화된 URL을 지정했는지 확인하세요.
- **해결 방법**: 올바른 Blob URL을 제공하세요. URL은 전체가 유효해야 하며, *http://*로 시작하고 *.com*으로 끝나야 합니다.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **설명**: *specifiedVersion* 버전 및 구독 ID *yourSubscriptionId*에서 사용할 수 없는 버전 정보 값입니다.
- **해결 방법**: 사용할 수 있는 버전을 선택한 후 작업을 다시 시도하세요.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **설명**: *specifiedVersion* 버전은 지원되지 않습니다.
- **해결 방법**: 지원되는 버전을 선택한 후 작업을 다시 시도하세요.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **설명**: *specifiedVersion* 버전은 Azure 지역 *specifiedRegion*에서 사용할 수 없습니다.
- **해결 방법**: 지정된 지역에서 지원되는 버전을 선택한 후 작업을 다시 시도하세요.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **설명**: 클러스터 구성이 잘못되었습니다. 필수 WASB 계정 구성을 외부 계정에서 찾을 수 없습니다.
- **해결 방법**: 계정이 존재하며 구성에서 올바르게 지정되었는지 확인한 후 작업을 다시 시도하세요.

## 다음 단계

- [HDInsight에서 Ambari 뷰를 사용하여 Tez 작업 디버깅](hdinsight-debug-ambari-tez-view.md)
- [Linux 기반 HDInsight에서 Hadoop 서비스에 힙 덤프 사용](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
- [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0914_2016-->