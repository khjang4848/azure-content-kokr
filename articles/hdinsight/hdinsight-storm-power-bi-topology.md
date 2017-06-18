<properties
 pageTitle="Power BI로 Apache Storm 사용 | Microsoft Azure"
 description="HDInsight의 Apache Storm 클러스터에서 실행 중인 C# 토폴로지로부터 데이터를 사용하여 Power BI 보고서를 만듭니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/16/2016"
 ms.author="larryfr"/>

# Power BI를 사용하여 Apache Storm 토폴로지에서 데이터 시각화

Power BI를 사용하면 데이터를 보고서로 시각적으로 표시할 수 있습니다. HDInsight에서 Storm에 Visual Studio 템플릿을 사용하여 SQL Azure에 대한 HDInsight 클러스터의 Apache Storm에서 실행 중인 토폴로지에서 저장소 데이터를 쉽게 사용할 수 있으며 Power BI를 사용하여 데이터를 시각화 수도 있습니다.

이 문서에서 Power BI를 사용하여 Apache Storm 토폴로지에 의해 생성되고 Azure SQL 데이터베이스에 저장된 데이터에서 보고서를 만드는 방법을 알아봅니다.

## 필수 조건

- Azure 구독. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* [Power BI](https://powerbi.com) 액세스 권한이 있는 Azure Active Directory 사용자

* Visual Studio(다음 버전 중 하나)

    * Visual Studio 2012 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=44921) 또는 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* HDInsight Tools for Visual Studio: 설치 정보는 [HDInsight Tools for Visual Studio 사용 시작](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

## 작동 방법

이 예제에서는 IIS(인터넷 정보 서비스) 로그 데이터를 무작위로 생성하는 C# Storm 토폴로지를 포함합니다. 이 데이터는 SQL 데이터베이스에 기록되고 해당 위치에서 Power BI에 보고서를 생성하는 데 사용됩니다.

다음은 이 예제의 주요 기능을 구현하는 파일 목록입니다.

* **SqlAzureBolt.cs**: SQL 데이터베이스에 대한 Storm 토폴로지에서 생성된 정보를 작성합니다.

* **IISLogsTable.sql**: 데이터가 저장되어 있는 데이터베이스를 생성하는 데 사용되는 Transact-SQL 문입니다.

> [AZURE.WARNING] HDInsight 클러스터에서 토폴로지를 시작하기 전에 SQL 데이터베이스에 테이블을 만들어야 합니다.

## 예제 다운로드

[HDInsight C# Storm Power BI 예제](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)를 다운로드합니다. 다운로드하려면 [git](http://git-scm.com/)를 사용하여 포크/복제하거나, **다운로드** 링크를 사용하여 .zip 보관 파일을 다운로드합니다.

## 데이터베이스 만들기

1. [SQL 데이터베이스 자습서](../sql-database/sql-database-get-started.md) 문서의 단계를 사용하여 새 SQL 데이터베이스를 만듭니다.

2. [Visual Studio와 함께 SQL 데이터베이스에 연결](../sql-database/sql-database-connect-query.md) 문서의 단계를 수행하여 데이터베이스에 연결합니다.

4. 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 __새 쿼리__를 만듭니다. 다운로드한 프로젝트에 포함된 __IISLogsTable.sql__ 파일의 내용을 쿼리 창에 붙여 넣은 다음 Ctrl + Shift + E를 사용하여 쿼리를 실행합니다. 명령이 성공적으로 완료되었다는 메시지를 받아야 합니다.

    이 작업을 완료하면 데이터베이스에 __IISLOGS__라는 새 테이블이 생성됩니다.

## 샘플 구성

1. [Azure 포털](https://portal.azure.com)에서 SQL 데이터베이스를 선택합니다. SQL 데이터베이스 블레이드의 __Essentials__ 섹션에서 __데이터베이스 연결 문자열 표시__를 선택합니다. 표시되는 목록에서 __ADO.NET(SQL 인증)__ 정보를 복사합니다.

1. Visual Studio에서 샘플을 엽니다. **솔루션 탐색기**에서 **App.config** 파일을 열고 다음 항목을 찾습니다.

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    __##TOBEFILLED##__ 값을 이전 단계에서 복사된 데이터베이스 연결 문자열과 바꿉니다. __{your\_username}__ 및 __{your\_password}__를 데이터베이스에 대한 사용자 이름 및 암호와 바꿉니다.

2. 파일을 저장하고 닫습니다.

## 샘플 배포

1. **솔루션 탐색기**에서 **StormToSQL** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다. **Storm 클러스터** 드롭다운 대화 상자에서 HDInsight 클러스터를 선택합니다.

    > [AZURE.NOTE] **Storm 클러스터** 드롭다운을 서버 이름으로 채우는 데 몇 초 정도 걸릴 수 있습니다.
    >
    > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. 토폴로지 제출에 성공하면 클러스터에 대한 Storm 토폴로지가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 SqlAzureWriterTopology 항목을 선택합니다.

    ![토폴로지가 선택된 토폴로지](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    이 보기를 사용하여 토폴로지에 대한 정보를 확인하거나 항목(예: SqlAzureBolt)을 두 번 클릭하여 토폴로지에서 구성 요소와 관련된 정보를 확인할 수 있습니다.

3. 몇 분 동안 토폴로지를 실행한 후에 데이터베이스를 만드는 데 사용한 SQL 쿼리 창으로 돌아갑니다. 기존 문을 다음과 바꿉니다.

        select * from iislogs;
    
    Ctrl + Shift + E를 사용하여 쿼리를 실행하면 다음과 유사한 결과를 수신하게 됩니다.
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    Storm 토폴로지에서 작성된 데이터입니다.

## 보고서 만들기

1. Power BI용 [Azure SQL 데이터베이스 커넥터](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect)에 연결합니다.

2. __데이터베이스__ 내에서 __가져오기__를 선택합니다.

3. __Azure SQL 데이터베이스__를 선택한 다음 __연결__을 선택합니다.

4. 정보를 입력하여 Azure SQL 데이터베이스에 연결합니다. [Azure 포털](https://portal.azure.com)에 방문하고 SQL 데이터베이스를 선택하여 찾을 수 있습니다.

    > [AZURE.NOTE] 연결 대화 상자에서 __고급 옵션 사용__을 사용하여 새로 고침 간격 및 사용자 지정 필터를 설정할 수도 있습니다.

5. 연결한 후에 연결한 데이터베이스와 동일한 이름을 가진 새 데이터 집합이 표시됩니다. 데이터 집합을 선택하여 보고서를 만들기 시작합니다.

3. __필드__에서 __IISLOGS__ 항목을 확장합니다. __URISTEM__에 대한 확인란을 선택합니다. 데이터베이스에 로그인한 URI 형태소(/foo, /bar, 등)을 나열한 새 보고서를 생성합니다.

    ![보고서 만들기](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. 다음으로 __메서드__ 보고서를 끌어옵니다. 보고서는 HTTP 요청에 사용된 형태소 및 해당 HTTP 메서드를 목록에 업데이트합니다.

    ![메서드 데이터 추가](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. __시각화__ 열에서 __필드__ 아이콘을 선택한 다음 __값__ 섹션의 __메서드__ 옆에 있는 아래쪽 화살표를 선택합니다. 표시되는 목록에서 __수__를 선택합니다. 이렇게 하면 보고서가 특정 URI에 액세스하는 횟수를 나열하도록 변경됩니다.

    ![메서드의 수 변경](./media/hdinsight-storm-power-bi-topology/count.png)

6. 다음으로 __누적 세로 막대형 차트__를 선택하여 정보가 표시되는 방식을 변경합니다.

    ![누적된 차트 변경](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. 원하는 대로 보고서가 있는 경우 메뉴에서 __저장__ 항목을 사용하여 이름을 입력하고 보고서를 저장합니다.

## 토폴로지 중지

토폴로지는 사용자가 중지하거나 HDInsight 클러스터에서 Storm을 삭제할 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 단계를 수행합니다.

1. Visual Studio에서 토폴로지 뷰어로 돌아가서 토폴로지를 선택합니다.

2. **Kill** 단추를 선택하여 토폴로지를 중지합니다.

    ![토폴로지 요약의 Kill 단추](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## 클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## 다음 단계

이 문서에서는 Storm 토폴로지에서 SQL 데이터베이스로 데이터를 보낸 다음 Power BI를 사용하여 데이터를 시각화하는 방법을 알아보았습니다. HDInsight에서 Storm을 사용하여 다른 Azure 기술을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->