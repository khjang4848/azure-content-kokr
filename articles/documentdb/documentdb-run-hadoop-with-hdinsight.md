<properties
	pageTitle="DocumentDB 및 HDInsight를 사용하여 Hadoop 작업 실행 | Microsoft Azure"
	description="단순 Hive, Pig 및 MapReduce 작업을 DocumentDB 및 Azure HDInsight에서 실행하는 방법을 알아봅니다."
	services="documentdb"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="mimig"
	documentationCenter=""/>


<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="anhoh"/>

#<a name="DocumentDB-HDInsight"></a>DocumentDB 및 HDInsight를 사용하여 Hadoop 작업 실행

이 자습서에서는 DocumentDB의 Hadoop 커넥터를 사용하여 Azure HDInsight에서 [Apache Hive][apache-hive], [Apache Pig][apache-pig] 및 [Apache Hadoop][apache-hadoop] MapReduce 작업을 실행하는 방법을 설명합니다. DocumentDB의 Hadoop 커넥터를 사용하면 DocumentDB가 Hive, Pig 및 MapReduce 작업에 대한 소스 및 싱크로 모두 작동할 수 있습니다. 이 자습서에서는 DocumentDB가 Hadoop 작업에 대한 데이터 원본 및 대상으로 모두 사용됩니다.

이 자습서를 완료하고 나면 다음을 알게 됩니다.

- Hive, Pig 또는 MapReduce 작업을 사용해서 DocumentDB에서 데이터를 로드하는 방법은 무엇인가요?
- DocumentDB에서 Hive, Pig 또는 MapReduce 작업을 사용해서 데이터를 저장하는 방법은 무엇인가요?

시작하려면 먼저 다음 비디오를 보고 DocumentDB 및 HDInsight를 사용해서 Hive 작업을 실행하는 방법을 확인하는 것이 좋습니다.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

그런 다음 이 자습서로 돌아와서 DocumentDB 데이터에 대해 분석 작업을 실행하는 방법을 자세히 살펴보세요.

> [AZURE.TIP] 이 자습서에서는 사용자가 이전에 Apache Hadoop, Hive 및/또는 Pig를 사용한 경험이 있다고 가정합니다. Apache Hadoop, Hive 및 Pig를 처음 사용하는 경우 [Apache Hadoop 설명서][apache-hadoop-doc]를 참조하는 것이 좋습니다. 또한 이 자습서에서는 이전에 DocumentDB를 사용해본 경험이 있으며 DocumentDB 계정이 있다고 가정합니다. DocumentDB를 처음 사용하거나 DocumentDB 계정이 없는 경우 [시작하기][getting-started] 페이지를 참조하세요.

자습서를 완료할 시간이 없고 Hive, Pig 및 MapReduce에 대한 전체 샘플 PowerShell 스크립트를 가져오려는 경우 [여기서][documentdb-hdinsight-samples] 가져오세요. 이 다운로드에는 또한 해당 샘플에 대한 hql, pig 및 java 파일이 포함되어 있습니다.

## <a name="NewestVersion"></a>최신 버전

<table border='1'>
	<tr><th>Hadoop 커넥터 버전</th>
		<td>1.2.0</td></tr>
	<tr><th>스크립트 URI</th>
		<td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
	<tr><th>수정한 날짜</th>
		<td>2016년 4월 26일</td></tr>
	<tr><th>지원되는 HDInsight 버전</th>
		<td>3.1, 3.2</td></tr>
	<tr><th>변경 로그</th>
		<td>DocumentDB Java SDK가 1.6.0으로 업데이트됨</br>
			원본 및 싱크로 분할된 컬렉션에 대한 추가 지원</br>
		</td></tr>
</table>

## <a name="Prerequisites"></a>필수 조건
이 자습서의 지침을 따르기 전에 다음이 있는지 확인하세요.

- DocumentDB 계정, 데이터베이스 및 내부 문서가 포함된 컬렉션. 자세한 내용은 [DocumentDB 시작][getting-started]을 참조하세요. [DocumentDB 가져오기 도구][documentdb-import-data]를 사용하여 DocumentDB 계정으로 샘플 데이터를 가져옵니다.
- 처리량. HDInsight에서의 읽기 및 쓰기는 해당 컬렉션에 할당된 요청 단위로 계산됩니다. 자세한 내용은 [프로비전된 처리량, 요청 단위 및 데이터베이스 작업][documentdb-manage-throughput]을 참조하세요.
- 각 출력 컬렉션 내의 추가 저장 프로시저 용량. 저장 프로시저는 결과 문서를 전송하는 데 사용됩니다. 자세한 내용은 [컬렉션 및 프로비전된 처리량][documentdb-manage-document-storage]을 참조하세요.
- Hive, Pig 또는 MapReduce 작업의 결과 문서 용량. 자세한 내용은 [DocumentDB 용량 및 성능 관리][documentdb-manage-collections]를 참조하세요.
- [*선택 사항*] 추가 컬렉션의 용량입니다. 자세한 내용은 [프로비전된 문서 저장소 및 인덱스 오버헤드][documentdb-manage-document-storage]를 참조하세요.

> [AZURE.WARNING] 작업 중 새 컬렉션이 생성되지 않도록 하려면 결과를 stdout으로 출력하거나, 출력을 WASB 컨테이너로 출력하거나, 기존 컬렉션을 지정할 수 있습니다. 기존 컬렉션을 지정하는 경우에는 새 문서가 컬렉션 내에 만들어지고 기존 문서는 *id*에서 충돌이 있는 경우에만 영향을 받습니다. **커넥터는 id가 충돌하는 기존 문서를 자동으로 덮어씁니다**. 이 기능은 upsert 옵션을 false로 설정해서 해제할 수 있습니다. upsert가 false이고 충돌이 발생하면 Hadoop 작업이 실패하고 id 충돌 오류가 보고됩니다.


## <a name="ProvisionHDInsight"></a>1단계: 새 HDInsight 클러스터 만들기
이 자습서에서는 Azure Portal의 스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정합니다. 이 자습서에서는 Azure Portal을 사용하여 HDInsight 클러스터를 만듭니다. PowerShell cmdlet 또는 HDInsight .NET SDK 사용 방법에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-custom-provision] 문서를 참조하세요.

1. [Azure 포털][azure-portal]에 로그인합니다.

2. 왼쪽 탐색의 맨 위에 있는 **+ 새로 만들기**를 클릭하고 새 블레이드의 맨 위 검색 표시줄에서 **HDInsight**를 검색합니다.

3. **Microsoft**에서 공개한 **HDInsight**는 결과의 위쪽에 표시됩니다. 클릭한 다음 **만들기**를 클릭합니다.

4. 새 HDInsight 클러스터 만들기 블레이드에서 **클러스터 이름**을 입력하고 이 리소스를 프로비전하려는 **구독**을 선택합니다.

	<table border='1'>
		<tr><td>클러스터 이름</td><td>클러스터의 이름<br/>
		DNS 이름은 영숫자 문자로 시작 및 끝나야 하고 대시를 포함할 수 있습니다.<br/>
		이 필드는 3~63자 사이의 문자열이어야 합니다.</td></tr>
		<tr><td>구독 이름</td>
			<td>둘 이상의 Azure 구독이 있는 경우 HDInsight 클러스터를 호스팅할 구독을 선택합니다. </td></tr>
	</table>

5. **클러스터 유형 선택**을 클릭하고 다음 속성을 지정된 값으로 설정합니다.

	<table border='1'>
		<tr><td>클러스터 유형</td><td><strong>Hadoop</strong></td></tr>
		<tr><td>클러스터 계층</td><td><strong>Standard</strong></td></tr>
		<tr><td>운영 체제</td><td><strong>Windows</strong></td></tr>
		<tr><td>버전</td><td>최신 버전</td></tr>
	</table>

	이제 **선택**을 클릭합니다.

	![Hadoop HDInsight 초기 클러스터 세부 정보 제공][image-customprovision-page1]

6. **자격 증명**을 클릭하여 로그인 및 원격 액세스 자격 증명을 설정합니다. **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**를 선택합니다.

	클러스터를 원격으로 사용하려는 경우 블레이드 맨 아래에서 *예*를 선택하고 사용자 이름 및 암호를 제공합니다.

7. **데이터 원본**을 클릭하여 데이터 액세스의 기본 위치를 설정합니다. **선택 방법**을 선택하고 기존 저장소 계정을 지정하거나 새로 만듭니다.

8. 동일한 블레이드에서 **기본 컨테이너** 및 **위치**를 지정합니다. 그리고 **선택**을 클릭합니다.

	> [AZURE.NOTE] 성능 향상을 위해 DocumentDB 계정 지역에 가까운 위치를 선택합니다.

8. **가격 책정**을 클릭하여 노드의 수와 유형을 선택합니다. 기본 구성을 유지하고 나중에 작업자 노드 수를 확장할 수 있습니다.

9. 선택적 구성 블레이드에서 **옵션 구성** 및 **스크립트 작업**을 차례로 클릭합니다.

	스크립트 작업에서 다음 정보를 입력하여 HDInsight 클러스터 사용자 지정합니다.

	<table border='1'>
		<tr><th>속성</th><th>값</th></tr>
		<tr><td>이름</td>
			<td>스크립트 작업의 이름을 지정합니다.</td></tr>
		<tr><td>스크립트 URI</td>
			<td>클러스터를 사용자 지정하기 위해 호출되는 스크립트에 URI를 지정합니다.</br></br>
			다음을 입력합니다. </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
		<tr><td>헤드</td>
			<td>이 확인란을 클릭하여 헤드 노드에 대한 PowerShell 스크립트를 실행합니다.</br></br>
			<strong>이 확인란을 선택합니다</strong>.</td></tr>
		<tr><td>작업자</td>
			<td>이 확인란을 클릭하여 작업자 노드에 대한 PowerShell 스크립트를 실행합니다.</br></br>
			<strong>이 확인란을 선택합니다</strong>.</td></tr>
		<tr><td>Zookeeper</td>
			<td>확인란을 클릭하여 Zookeeper에 대한 PowerShell 스크립트를 실행합니다.</br></br>
			<strong>필요하지 않습니다</strong>.
			</td></tr>
		<tr><td>매개 변수</td>
			<td>스크립트에 필요한 경우 매개 변수를 지정합니다.</br></br>
			<strong>필요한 매개 변수가 없습니다</strong>.</td></tr>
	</table>

10. Azure 구독에서 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 사용합니다.

11. 이제 **대시보드에 고정**을 선택하여 배포를 추적하고 **만들기**를 클릭합니다.

## <a name="InstallCmdlets"></a>2단계: Azure PowerShell 설치 및 구성

1. Azure PowerShell을 설치합니다. 자세한 내용은 [여기서][powershell-install-configure] 확인할 수 있습니다.

	> [AZURE.NOTE] 또는 Hive 쿼리만 해당하는 경우 HDInsight의 온라인 Hive 편집기를 사용할 수 있습니다. 이렇게 하려면 [Azure Portal][azure-portal]에 로그인하고 왼쪽 창에서 **HDInsight**를 클릭하여 HDInsight 클러스터 목록을 확인합니다. Hive 쿼리를 실행하려는 클러스터를 클릭한 후 **쿼리 콘솔**을 클릭합니다.

2. Azure PowerShell 통합 스크립팅 환경을 엽니다.
	- Windows 8 또는 Windows Server 2012 이상을 실행하는 컴퓨터에서는 기본 제공되는 검색 기능을 사용할 수 있습니다. 시작 화면에서 **powershell ise**를 입력하고 **Enter** 키를 클릭합니다.
	- Windows 8 또는 Windows Server 2012 이전 버전을 실행하는 컴퓨터에서 시작 메뉴를 사용합니다. 시작 메뉴에서 검색 상자에 **명령 프롬프트**를 입력한 후 결과 목록에서 **명령 프롬프트**를 클릭합니다. 명령 프롬프트에서 **powershell\_ise**를 입력하고 **Enter** 키를 클릭합니다.

3. Azure 계정을 추가합니다.
	1. 콘솔 창에서 **Add-AzureAccount**를 입력하고 **Enter** 키를 클릭합니다.
	2. Azure 구독과 연관된 메일 주소를 입력하고 **계속**을 클릭합니다.
	3. Azure 구독의 암호를 입력합니다.
	4. **로그인**을 클릭합니다.

4. 다음 다이어그램은 Azure PowerShell 스크립팅 환경에서 중요한 부분을 보여 줍니다.

	![Azure PowerShell에 대한 다이어그램][azure-powershell-diagram]

## <a name="RunHive"></a>3단계: DocumentDB 및 HDInsight를 사용하여 Hive 작업 실행

> [AZURE.IMPORTANT] < >로 표시된 모든 변수는 구성 설정을 사용하여 입력해야 합니다.

1. PowerShell 스크립트 창에서 다음 변수를 설정합니다.

		# Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide the HDInsight cluster name where you want to run the Hive job.
		$clusterName = "<HDInsightClusterName>"

2. <p>쿼리 문자열 생성부터 시작합니다. 여기에서는 모든 문서 시스템에서 생성된 타임스탬프(_ts) 및 고유 ID(_rid)를 DocumentDB 컬렉션에서 가져오고, 모든 문서에 해당 시간을 기록한 후 결과를 다시 새로운 DocumentDB 컬렉션에 저장하는 Hive 쿼리를 작성합니다.</p>

    <p>먼저 DocumentDB 컬렉션에서 Hive 테이블을 만듭니다. PowerShell 스크립트 창에서 다음 코드 조각을 #1의 코드 조각 <strong>다음에</strong> 추가합니다. 문서를 _ts 및 _rid로만 정리하려면 선택적인 DocumentDB.query 매개 변수를 포함해야 합니다.</p>

    > [AZURE.NOTE] **DocumentDB.inputCollections 이름 지정은 실수가 아니었습니다.** 입력으로 여러 컬렉션을 추가할 수 있도록 허용합니다. </br>

		'*DocumentDB.inputCollections*' = '\*<DocumentDB Input Collection Name 1>*,*\<DocumentDB Input Collection Name 2>*' A1A</br> The collection names are separated without spaces, using only a single comma.


		# Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
		$queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  그런 다음 출력 컬렉션에 대한 HIve 테이블을 만듭니다. 출력 문서 속성은 월, 일, 시간, 분 및 총 발생 횟수가 됩니다.

	> [AZURE.NOTE] **하지만 다시 DocumentDB.outputCollections 이름 지정은 실수가 아니었습니다.** 입력으로 여러 컬렉션을 추가할 수 있도록 허용합니다. </br> 
	'*DocumentDB.outputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> 컬렉션 이름은 공백 없이 단일 쉼표만 사용해서 구분되었습니다. </br></br>
	문서는 여러 컬렉션 간에 라운드 로빈 방식으로 분산됩니다. 문서 일괄 처리는 하나의 컬렉션에 저장되며, 문서의 두 번째 일괄 처리는 그 다음 컬렉션에 저장됩니다.

		# Create a Hive table for the output data to DocumentDB.
	    $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. 끝으로, 문서를 월, 일, 시간 및 분으로 표시하고 결과를 다시 출력 HIve 테이블에 삽입합니다.

		# GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. 다음 스크립트 코드 조각을 추가해서 이전 쿼리로부터 HIve 작업 정의를 만듭니다.

		# Create a Hive job definition.
		$queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	-File 스위치를 사용하여 HDFS에서 HiveQL 스크립트 파일을 지정할 수도 있습니다.

6. 다음 코드 조각을 추가해서 시작 시간을 저장하고 Hive 작업을 제출합니다.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. 다음을 추가해서 Hive 작업이 완료될 때까지 기다립니다.

		# Wait for the Hive job to complete.
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. 다음을 추가해서 표준 출력과 시작 및 종료 시간을 인쇄합니다.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. 새 스크립트를 **실행**합니다. 녹색 실행 단추를 **클릭**합니다.

10. 결과를 확인합니다. [Azure 포털][azure-portal]에 로그인합니다.
	1. 왼쪽 패널에서 <strong>찾아보기</strong>를 클릭합니다. </br>
	2. 찾아보기 패널의 오른쪽 상단에서 <strong>모두</strong>를 클릭합니다. </br>
	3. <strong>DocumentDB 계정</strong>을 찾아서 클릭합니다. </br>
	4. 그런 후 <strong>DocumentDB 계정</strong>을 찾고, Hive 쿼리에 지정된 출력 컬렉션과 연관된 <strong>DocumentDB 데이터베이스</strong> 및 <strong>DocumentDB 컬렉션</strong>을 찾습니다.</br>
	5. 끝으로, <strong>개발자 도구</strong> 아래에서 <strong>문서 탐색기</strong>를 클릭합니다.</br></p>

	Hive 쿼리 결과가 표시됩니다.

	![Hive 쿼리 결과][image-hive-query-results]

## <a name="RunPig"></a>4단계: DocumentDB 및 HDInsight를 사용하여 Pig 작업 실행

> [AZURE.IMPORTANT] < >로 표시된 모든 변수는 구성 설정을 사용하여 입력해야 합니다.

1. PowerShell 스크립트 창에서 다음 변수를 설정합니다.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>쿼리 문자열 생성부터 시작합니다. 여기에서는 모든 문서 시스템에서 생성된 타임스탬프(_ts) 및 고유 ID(_rid)를 DocumentDB 컬렉션에서 가져오고, 모든 문서에 해당 시간을 기록한 후 결과를 다시 새로운 DocumentDB 컬렉션에 저장하는 Pig 쿼리를 작성합니다.</p>
    <p>먼저 DocumentDB의 문서를 HDInsight에 로드합니다. PowerShell 스크립트 창에서 다음 코드 조각을 #1의 코드 조각 <strong>다음에</strong> 추가합니다. 문서를 just _ts 및 _rid로만 정리하려면 DocumentDB 쿼리를 선택적인 DocumentDB 쿼리 매개 변수에 추가해야 합니다.</p>

    > [AZURE.NOTE] 입력으로 여러 컬렉션을 추가할 수 있도록 허용합니다. </br> 
    '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br> 컬렉션 이름은 공백 없이 단일 쉼표만 사용하여 구분되었습니다. </b>

	문서는 여러 컬렉션 간에 라운드 로빈 방식으로 분산됩니다. 문서 일괄 처리는 하나의 컬렉션에 저장되며, 문서의 두 번째 일괄 처리는 그 다음 컬렉션에 저장됩니다.

		# Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  그런 다음 문서에 월, 일, 시간, 분 및 총 발생 횟수를 표시합니다.

		# GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. 끝으로, 결과를 새 출력 컬렉션에 저장합니다.

    > [AZURE.NOTE] 입력으로 여러 컬렉션을 추가할 수 있도록 허용합니다. </br>
    '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> 컬렉션 이름은 공백 없이 단일 쉼표만 사용해서 구분되었습니다.</br>
    문서는 여러 컬렉션 간에 라운드 로빈으로 분산됩니다. 문서 일괄 처리는 하나의 컬렉션에 저장되며, 문서의 두 번째 일괄 처리는 그 다음 컬렉션에 저장됩니다.

		# Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. 다음 스크립트 코드 조각을 추가해서 이전 쿼리로부터 Pig 작업 정의를 만듭니다.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

	-File 스위치를 사용하여 HDFS에서 Pig 스크립트 파일을 지정할 수도 있습니다.

6. 다음 코드 조각을 추가해서 시작 시간을 저장하고 Pig 작업을 제출합니다.

		# Save the start time and submit the job to the cluster.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. 다음을 추가해서 Pig 작업이 완료될 때까지 기다립니다.

		# Wait for the Pig job to complete.
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. 다음을 추가해서 표준 출력과 시작 및 종료 시간을 인쇄합니다.

		# Print the standard error, the standard output of the Hive job, and the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. 새 스크립트를 **실행**합니다. 녹색 실행 단추를 **클릭**합니다.

10. 결과를 확인합니다. [Azure 포털][azure-portal]에 로그인합니다.
	1. 왼쪽 패널에서 <strong>찾아보기</strong>를 클릭합니다. </br>
	2. 찾아보기 패널의 오른쪽 상단에서 <strong>모두</strong>를 클릭합니다. </br>
	3. <strong>DocumentDB 계정</strong>을 찾아서 클릭합니다. </br>
	4. 그런 후 <strong>DocumentDB 계정</strong>을 찾고, Pig 쿼리에 지정된 출력 컬렉션과 연관된 <strong>DocumentDB 데이터베이스</strong> 및 <strong>DocumentDB 컬렉션</strong>을 찾습니다.</br>
	5. 끝으로, <strong>개발자 도구</strong> 아래에서 <strong>문서 탐색기</strong>를 클릭합니다.</br></p>

	Pig 쿼리 결과가 표시됩니다.

	![Pig 쿼리 결과][image-pig-query-results]

## <a name="RunMapReduce"></a>5단계: DocumentDB 및 HDInsight를 사용하여 MapReduce 작업 실행

1. PowerShell 스크립트 창에서 다음 변수를 설정합니다.

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

2. 여기에서는 DocumentDB 컬렉션에서 각 문서 속성의 발생 횟수를 기록하는 MapReduce 작업을 실행합니다. 이 스크립트 코드 조각을 위 코드 조각 **다음에** 추가합니다.

		# Define the MapReduce job.
		$TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

	> [AZURE.NOTE] TallyProperties-v01.jar은 DocumentDB Hadoop 커넥터의 사용자 지정 설치로 제공됩니다.

3. 다음 명령을 실행하여 MapReduce 작업을 제출합니다.

		# Save the start time and submit the job.
		$startTime = Get-Date
		Select-AzureSubscription $subscriptionName
		$TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	MapReduce 작업 정의뿐 아니라 MapReduce 작업을 실행하려는 HDInsight 클러스터 이름 및 자격 증명도 제공합니다. Start-AzureHDInsightJob은 비동기 호출입니다. 작업 완료를 확인하려면 *Wait-AzureHDInsightJob* cmdlet을 사용합니다.

4. 다음 명령을 추가하여 MapReduce 작업 실행과 관련한 오류를 확인합니다.

		# Get the job output and print the start and end time.
		$endTime = Get-Date
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
		Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. 새 스크립트를 **실행**합니다. 녹색 실행 단추를 **클릭**합니다.

6. 결과를 확인합니다. [Azure 포털][azure-portal]에 로그인합니다.
	1. 왼쪽 패널에서 <strong>찾아보기</strong>를 클릭합니다.
	2. 찾아보기 패널의 오른쪽 상단에서 <strong>모두</strong>를 클릭합니다.
	3. <strong>DocumentDB 계정</strong>을 찾아서 클릭합니다.
	4. 그런 후 <strong>DocumentDB 계정</strong>을 찾고, MapReduce 작업에 지정된 출력 컬렉션과 연관된 <strong>DocumentDB 데이터베이스</strong> 및 <strong>DocumentDB 컬렉션</strong>을 찾습니다.
	5. 끝으로, <strong>개발자 도구</strong> 아래에서 <strong>문서 탐색기</strong>를 클릭합니다.

	MapReduce 작업 결과가 표시됩니다.

	![MapReduce 쿼리 결과][image-mapreduce-query-results]

## <a name="NextSteps"></a>다음 단계

축하합니다. 지금까지 Azure DocumentDB 및 HDInsight를 사용해서 처음으로 Hive, Pig 및 MapReduce 작업을 실행했습니다.

Hadoop 커넥터는 소스가 공개되어 있습니다. 관심이 있으면 [GitHub][documentdb-github]에서 참여할 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.

- [Documentdb로 Java 응용 프로그램 개발][documentdb-java-application]
- [HDInsight의 Hadoop용 Java MapReduce 프로그램 개발][hdinsight-develop-deploy-java-mapreduce]
- [휴대폰 사용을 분석하기 위해 HDInsight에서 Hive와 함께 Hadoop 사용 시작][hdinsight-get-started]
- [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]
- [HDInsight에서 Hive 사용][hdinsight-use-hive]
- [HDInsight에서 Pig 사용][hdinsight-use-pig]
- [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0921_2016-->