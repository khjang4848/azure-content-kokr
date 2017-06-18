<properties
	pageTitle="Data Factory 서비스에 대한 모든 항목 | Microsoft Azure"
	description="http://azure.microsoft.com/documentation/articles/에 있는 Data Factory라는 Azure 서비스에 대한 모든 항목, 제목 및 설명에 대한 표입니다."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="data-factory"
	ms.workload="data-factory"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/21/2016"
	ms.author="spelluru"/>


# Azure Data Factory 서비스에 대한 모든 항목

이 항목에서는 **Data Factory** 서비스에 직접 적용되는 모든 항목을 나열합니다. 이 웹 페이지에서 **Ctrl+F**를 사용해 키워드를 검색하여 현재 관심 있는 항목을 찾을 수 있습니다.




## 새로 만들기

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 1 | [데이터 팩터리 REST API를 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-rest-api.md) | 이 자습서에서는 데이터 팩터리 REST API를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다. |
| 2 | [자습서: REST API를 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-rest-api.md) | 이 자습서에서는 REST API를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다. |
| 3 | [데이터 팩터리 복사 마법사](data-factory-copy-wizard.md) | 데이터 팩터리 복사 마법사를 사용하여 지원되는 데이터 소스의 데이터를 싱크로 복사하는 방법에 대해 알아보세요. |
| 4 | [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) | 데이터 게이트웨이를 설정하여 온-프레미스와 클라우드 간에 데이터를 이동합니다. Azure Data Factory의 데이터 관리 게이트웨이를 사용하여 데이터를 이동합니다. |
| 5 | [Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터 이동](data-factory-onprem-cassandra-connector.md) | Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터를 이동하는 방법을 알아봅니다. |
| 6 | [Azure Data Factory를 사용하여 MongoDB에서 데이터 이동](data-factory-on-premises-mongodb-connector.md) | Azure Data Factory를 사용하여 MongoDB 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 7 | [Azure Data Factory를 사용하여 Salesforce에서 데이터 이동](data-factory-salesforce-connector.md) | Azure Data Factory를 사용하여 Salesforce에서 데이터를 이동하는 방법에 대해 알아봅니다. |


## 업데이트된 문서

이 섹션에서는 최근에 대규모로 수행되거나 중요하게 업데이트된 문서가 목록으로 표시됩니다. 업데이트된 각 문서에 대해 추가된 markdown 텍스트의 대략적인 코드 조각이 표시됩니다. 날짜 범위 **2016-07-26**부터 **2016-08-21**까지 문서가 업데이트되었습니다.

| &nbsp; | 문서 | 업데이트된 텍스트, 코드 조각 |
| --: | :-- | :-- |
| 8 | [데이터 팩터리 .NET SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리](data-factory-create-data-factories-programmatically.md) | **팝업 대화 상자 없이 로그인** 위의 샘플 코드는 대화 상자를 시작하여 Azure 자격 증명을 입력하도록 합니다. 대화 상자를 사용하지 않고 프로그래밍 방식으로 로그인해야 하는 경우 Azure Resource Manager를 사용하여 서비스 주체 인증을 참조하세요(resource-group-authenticate-service-principal.md authenticate-service-principal-with-certificate---powershell). **예제** 아래 표시된 대로 GetAuthorizationHeaderNoPopup 메서드 만들기: public static string GetAuthorizationHeaderNoPopup() { var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings "ActiveDirectoryTenantId" ); var context = new AuthenticationContext(authority.AbsoluteUri); var credential = new ClientCredential(ConfigurationManager.AppSettings "AdfClientId" , ConfigurationManager.AppSettings "AdfClientSecret" ); AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings "WindowsManagementUri" , credential).Result; |
| 9 | [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md) | **지원되는 파일 형식** 복사 작업은 Azure Blob, 파일 시스템 및 HDFS(File System, and Hadoop Distributed File System)와 같은 두 파일 기반 데이터 저장소 사이에서 파일을 그대로 복사할 수 있습니다. 이렇게 하려면 입력 및 출력 데이터 집합 정의의 형식 섹션(data-factory-create-datasets.md)을 건너뛸 수 있고 직렬화/역직렬화 과정 없이 데이터가 복사됩니다. 복사 작업은 또한 텍스트, Avro, ORC 및 JSON 등 지정된 형식의 파일을 읽고 씁니다. 수행할 수 있는 복사 작업의 몇 가지 예는 다음과 같습니다. / Azure Blob에서 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL에 기록 / 파일 시스템 온-프레미스에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식의 Azure Blob에 기록 / Azure SQL Database의 데이터를 복사하여 ORC 형식의 HDFS 온-프레미스에 기록 ** .a name="global"../a.전역적으로 사용 가능한 데이터 이동** Azure Data Factory는 미국 서부, 미국 동부 및 북유럽 지역에서만 사용할 수 있지만, 다음과 같은 지역 및 지리에서는 복사 작업을 지원하는 서비스를 전역적으로 사용할 수 있습니다.|
| 10 | [Azure Data Factory를 사용하여 OData 소스에서 데이터 이동](data-factory-odata-connector.md) | **온-프레미스 OData 소스에 액세스하는 Windows 인증 사용** { "name": "inputLinkedService", "properties": { "type": "OData", "typeProperties": { "url": ".endpoint of on-premises OData source e.g. Dynamics CRM.", "authenticationType": "Windows", "username": "domain\\user", "password": "password", "gatewayName": "mygateway" } } } |





## 자습서

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 11 | [자습서: Hadoop 클러스터를 사용하여 데이터를 처리하는 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md) | 이 Azure 데이터 팩터리 자습서에서는 Hadoop 클러스터에서 Hive 스크립트를 사용하여 데이터를 처리하는 데이터 팩터리를 만들고 예약하는 방법을 보여 줍니다. |
| 12 | [자습서: Azure Resource Manager 템플릿을 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-arm.md) | 이 자습서에서는 Azure 리소스 관리자 템플릿을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다. |
| 13 | [Azure 포털/데이터 팩터리 편집기를 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-editor.md) | 이 자습서에서는 Azure 포털의 데이터 팩터리 편집기를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다. |
| 14 | [Azure PowerShell을 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-powershell.md) | 이 자습서에서는 Azure PowerShell을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다. |
| 15 | [Microsoft Visual Studio를 사용하여 첫 번째 Azure Data Factory 빌드](data-factory-build-your-first-pipeline-using-vs.md) | 이 자습서에서는 Visual Studio를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다. |
| 16 | [자습서: 데이터 팩터리 편집기를 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-azure-portal.md) | 이 자습서에서는 Azure 포털의 데이터 팩터리 편집기를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다. |
| 17 | [자습서: Azure PowerShell을 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-powershell.md) | 이 자습서에서는 Azure PowerShell을 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다. |
| 18 | [자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-visual-studio.md) | 이 자습서에서는 Visual Studio를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다. |
| 19 | [데이터 팩터리를 사용하여 Blob 저장소에서 SQL 데이터베이스로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | 이 자습서에서는 Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Blob 저장소에서 SQL 데이터베이스로 데이터를 복사하는 방법을 보여 줍니다. |
| 20 | [자습서: 데이터 팩터리 복사 마법사를 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md) | 이 자습서에서는 데이터 팩터리가 지원하는 복사 마법사를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다. |



## 데이터 이동

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 21 | [Azure Data Factory를 사용하여 Azure Blob 간 데이터 이동](data-factory-azure-blob-connector.md) | Azure Data Factory에서 Blob 데이터를 복사하는 방법을 알아봅니다. 샘플 사용: Azure Blob 저장소 및 Azure SQL 데이터베이스 간에 데이터를 복사하는 방법입니다. |
| 22 | [Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터 이동](data-factory-azure-datalake-connector.md) | Azure 데이터 팩터리를 사용하여 Azure 데이터 레이크 저장소 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 23 | [Azure 데이터 팩터리를 사용하여 DocumentDB 간 데이터 이동](data-factory-azure-documentdb-connector.md) | Azure 데이터 팩터리를 사용하여 Azure DocumentDB 컬렉션 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 24 | [Azure 데이터 팩터리를 사용하여 Azure SQL 데이터베이스 간 데이터 이동](data-factory-azure-sql-connector.md) | Azure Data Factory를 사용하여 Azure SQL 데이터베이스 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 25 | [Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터 이동](data-factory-azure-sql-data-warehouse-connector.md) | Azure 데이터 팩터리를 사용하여 Azure SQL 데이터 웨어하우스 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 26 | [Azure 데이터 팩터리를 사용하여 Azure 테이블 간 데이터 이동](data-factory-azure-table-connector.md) | Azure Data Factory를 사용하여 Azure 테이블 저장소 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 27 | [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) | 복사 작업을 통한 Azure Data Factory의 데이터 이동의 성능에 영향을 주는 주요 요소에 대해 알아봅니다. |
| 28 | [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md) | 데이터 팩터리 파이프라인에서 데이터 이동에 대해 알아보기: 클라우드 저장소 간 및 온-프레미스 및 클라우드 간의 데이터 마이그레이션. 복사 작업 사용 |
| 29 | [데이터 관리 게이트웨이에 대한 릴리스 정보](data-factory-gateway-release-notes.md) | 데이터 관리 게이트웨이 릴리스 정보 |
| 30 | [Azure 데이터 팩터리를 사용하여 온-프레미스 HDFS에서 데이터 이동](data-factory-hdfs-connector.md) | Azure 데이터 팩터리를 사용하여 온-프레미스 HDFS에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 31 | [새 모니터링 및 관리 앱을 사용하여 Azure 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) | 모니터링 및 관리 앱을 사용하여 Azure 데이터 팩터리 및 파이프라인을 모니터링하고 관리하는 방법을 알아봅니다. |
| 32 | [온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) | 데이터 게이트웨이를 설정하여 온-프레미스와 클라우드 간에 데이터를 이동합니다. Azure Data Factory의 데이터 관리 게이트웨이를 사용하여 데이터를 이동합니다. |
| 33 | [Azure Data Factory를 사용하여 OData 소스에서 데이터 이동](data-factory-odata-connector.md) | Azure 데이터 팩터리를 사용하여 OData 소스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 34 | [Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터 이동](data-factory-odbc-connector.md) | Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 35 | [Azure 데이터 팩터리를 사용하여 DB2에서 데이터 이동](data-factory-onprem-db2-connector.md) | Azure 데이터 팩터리를 사용하여 DB2 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 36 | [Azure Data Factory를 사용하여 온-프레미스 파일 시스템 간 데이터 이동](data-factory-onprem-file-system-connector.md) | Azure Data Factory를 사용하여 온-프레미스 파일 시스템 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 37 | [Azure 데이터 팩터리를 사용하여 MySQL에서 데이터 이동](data-factory-onprem-mysql-connector.md) | Azure 데이터 팩터리를 사용하여 MySQL 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 38 | [Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle 간 데이터 이동](data-factory-onprem-oracle-connector.md) | Azure 데이터 팩터리를 사용하여 온-프레미스 Oracle 데이터베이스 간 데이터를 이동하는 방법에 대해 알아봅니다. |
| 39 | [Azure 데이터 팩터리를 사용하여 PostgreSQL에서 데이터 이동](data-factory-onprem-postgresql-connector.md) | Azure 데이터 팩터리를 사용하여 PostgreSQL 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 40 | [Azure 데이터 팩터리를 사용하여 Sybase에서 데이터 이동](data-factory-onprem-sybase-connector.md) | Azure 데이터 팩터리를 사용하여 Sybase 데이터베이스에서 데이터를 이동하는 방법에 대해 알아봅니다. |
| 41 | [Azure 데이터 팩터리를 사용하여 Teradata에서 데이터 이동](data-factory-onprem-teradata-connector.md) | Teradata 데이터베이스에서 데이터를 이동시킬 수 있는 데이터 팩터리 서비스용 Teradata 커넥터에 대해 알아봅니다. |
| 42 | [Azure 데이터 팩터리를 사용하여 온-프레미스 또는 IaaS(Azure VM) SQL Server 간 데이터 이동](data-factory-sqlserver-connector.md) | Azure 데이터 팩터리를 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스 간에 데이터를 이동하는 방법에 대해 알아봅니다. |
| 43 | [Azure Data Factory를 사용하여 웹 테이블 원본에서 데이터 이동](data-factory-web-table-connector.md) | Azure Data Factory를 사용하여 온-프레미스 웹 페이지의 테이블로 데이터를 이동하는 방법에 대해 알아봅니다. |



## 데이터 변환

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 44 | [Azure 기계 학습 작업을 사용하여 예측 파이프라인 만들기](data-factory-azure-ml-batch-execution-activity.md) | Azure Data Factory 및 Azure 기계 학습을 사용하여 예측 파이프라인을 만드는 방법을 설명합니다. |
| 45 | [연결된 서비스 계산](data-factory-compute-linked-services.md) | 데이터의 변환/처리를 위해 Azure 데이터 팩터리 파이프라인에서 사용할 수 있는 계산 환경을 알아봅니다. |
| 46 | [데이터 팩터리 및 배치를 사용하여 대규모 데이터 집합 처리](data-factory-data-processing-using-batch.md) | Azure 배치의 병렬 처리 기능을 사용하여 Azure Data Factory 파이프라인에서 대용량 데이터를 처리하는 방법을 설명합니다. |
| 47 | [Azure 데이터 팩터리의 데이터를 변환하고 분석하는 방법에 대해 알아보기](data-factory-data-transformation-activities.md) | Azure 데이터 팩터리에서 데이터를 변환하는 방법에 대해 알아봅니다. Azure HDInsight 클러스터 또는 Azure 배치에서 데이터를 변환하고 처리합니다. |
| 48 | [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md) | Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행하는 방법에 대해 알아봅니다. |
| 49 | [Hive 작업](data-factory-hive-activity.md) | Azure 데이터 공장에서 Hive 활동을 사용하여 필요 시/사용자 고유의 HDInsight 클러스터에서 Hive 쿼리를 실행하는 방법을 알아봅니다. |
| 50 | [데이터 팩터리에서 MapReduce 프로그램 호출](data-factory-map-reduce.md) | Azure HDInsight 클러스터에서 Azure 데이터 팩터리의 MapReduce 프로그램을 실행하여 데이터를 처리하는 방법을 알아봅니다. |
| 51 | [Pig 작업](data-factory-pig-activity.md) | Azure Data Factory에서 Pig 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Pig 스크립트를 실행하는 방법을 알아봅니다. |
| 52 | [Data Factory에서 Spark 프로그램 호출](data-factory-spark.md) | MapReduce 작업을 사용하여 Azure Data Factory에서 Spark 프로그램을 호출하는 방법에 대해 알아봅니다. |
| 53 | [SQL Server 저장 프로시저 작업](data-factory-stored-proc-activity.md) | SQL Server 저장 프로시저 작업을 사용하여 데이터 팩터리 파이프라인으로 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 저장 프로시저를 호출하는 방법을 알아봅니다. |
| 54 | [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용](data-factory-use-custom-activities.md) | 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법에 대해 알아봅니다. |
| 55 | [Azure Data Factory의 Azure 데이터 레이크 분석에서 U-SQL 스크립트 실행](data-factory-usql-activity.md) | Azure Data Lake Analytics 계산 서비스에서 U-SQL 스크립트를 실행하여 데이터를 처리하는 방법에 대해 알아봅니다. |



## 샘플

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 56 | [Azure 데이터 팩터리 - 샘플](data-factory-samples.md) | Azure Data Factory 서비스와 함께 제공 되는 샘플에 대 한 세부 정보를 제공 합니다. |



## 사용 사례

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 57 | [고객 사례 연구](data-factory-customer-case-studies.md) | 고객들이 Azure 데이터 팩터리를 어떻게 사용하고 있는지 알아봅니다. |
| 58 | [사용 사례 - 고객 프로파일링](data-factory-customer-profiling-usecase.md) | Azure 데이터 팩터리로 데이터 기반 워크플로를(파이프라인) 만들어 게임 고객을 프로파일링하는 방법을 알아봅니다. |
| 59 | [사용 사례 - 제품 추천](data-factory-product-reco-usecase.md) | Azure Data Factory 및 기타 서비스로 구현한 사용 사례에 대해 알아봅니다. |



## 모니터링 및 관리

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 60 | [Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) | Azure 포털과 Azure PowerShell을 사용하여 사용자가 만든 Azure Data Factory와 파이프라인을 모니터링하고 관리하는 방법에 대해 알아봅니다. |



## SDK)

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 61 | [Azure 데이터 팩터리 - .NET API 변경 로그](data-factory-api-change-log.md) | Azure Data Factory에 대한 특정 .NET API 버전의 주요 변경 내용, 기능 추가 사항, 버그 수정 등을 설명합니다. |
| 62 | [데이터 팩터리 .NET SDK를 사용하여 Azure Data Factory 만들기, 모니터링 및 관리](data-factory-create-data-factories-programmatically.md) | 데이터 팩터리 SDK를 사용하여 프로그래밍 방식으로 Azure Data Factory를 만들고, 모니터링하고, 관리하는 방법을 알아봅니다. |
| 63 | [Azure 데이터 팩터리 개발자 참조](data-factory-sdks.md) | Azure 데이터 팩터리를 만들고 모니터링하고 관리하는 다양한 방법을 알아봅니다. |



## 기타

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 64 | [Azure 데이터 팩터리 - 질문과 대답](data-factory-faq.md) | Azure 데이터 팩터리에 대한 질문과 대답입니다. |
| 65 | [Azure 데이터 팩터리 - 함수 및 시스템 변수](data-factory-functions-variables.md) | Azure 데이터 팩터리 함수 및 시스템 변수 목록을 제공합니다. |
| 66 | [Azure 데이터 팩터리 - 이름 지정 규칙](data-factory-naming-rules.md) | 데이터 팩터리 엔터티에 대한 이름 지정 규칙을 설명합니다. |
| 67 | [데이터 팩터리 문제 해결](data-factory-troubleshoot.md) | Azure 데이터 팩터리 사용과 관련된 문제를 해결하는 방법에 대해 알아봅니다. |

<!---HONumber=AcomDC_0914_2016-->