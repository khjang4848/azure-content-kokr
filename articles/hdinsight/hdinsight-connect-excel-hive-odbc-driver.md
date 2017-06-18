<properties
   pageTitle="Hive ODBC 드라이버로 Hadoop에 Excel 연결 | Microsoft Azure"
   description="Excel용 Microsoft Hive ODBC 드라이버를 설정하고 HDInsight 클러스터에서 데이터를 쿼리하는 데 사용하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

#Microsoft Hive ODBC 드라이버로 Hadoop에 Excel 연결

[AZURE.INCLUDE [ODBC JDBC 선택기](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft의 빅 데이터 솔루션은 Microsoft BI(비즈니스 인텔리전스) 구성 요소를 Azure HDInsight가 배포한 Apache Hadoop 클러스터와 통합합니다. 이 통합의 예로 Microsoft Hive ODBC(Open Database Connectivity) 드라이버를 사용하여 HDInsight Hadoop 클러스터의 Hive 데이터 웨어하우스에 Excel을 연결하는 기능을 들 수 있습니다.

Excel에서 Microsoft Excel용 파워 쿼리 추가 기능을 사용하여 HDInsight 클러스터 및 기타 데이터 원본(예: 기타(비 HDInsight) Hadoop 클러스터)과 연결된 데이터를 연결할 수도 있습니다. 파워 쿼리 설치 및 사용에 대한 자세한 내용은 [HDInsight에 파워 쿼리로 Excel 연결][hdinsight-power-query]을 참조하세요.

> [AZURE.NOTE] 이 문서의 단계는 Linux 또는 Windows 기반 HDInsight 클러스터와 함께 사용할 수 있지만, Windows는 클라이언트 워크스테이션에 필요합니다.

**필수 조건**:

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **HDInsight 클러스터**. 만들려면 [Azure HDInsight 시작][hdinsight-get-started]을 참조하세요.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone 또는 Office 2010 Professional Plus가 있는 **워크스테이션**


##Microsoft Hive ODBC 드라이버 설치

[다운로드 센터][hive-odbc-driver-download]에서 Microsoft Hive ODBC 드라이버를 다운로드하고 설치합니다.

이 드라이버는 32비트 또는 64비트 버전의 Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 및 Windows Server 2012에 설치할 수 있으며 Azure HDInsight(버전 1.6 이상) 및 Azure HDInsight Emulator(v.1.0.0.0 이상)에 연결할 수 있게 합니다. ODBC 드라이버를 사용할 응용 프로그램 버전에 맞는 버전을 설치해야 합니다. 이 자습서에서는 Office Excel에서 드라이버를 사용합니다.

##Hive ODBC 데이터 원본 만들기

다음 단계에 따라 Hive ODBC 데이터 원본을 만들 수 있습니다.

1. Windows 8 또는 Windows 10에서 Windows 키를 눌러 시작 화면을 연 후 **data sources**를 입력합니다.
2. Office 버전에 따라 **ODBC 데이터 원본 설정(32비트)** 또는 **ODBC 데이터 원본 설정(64비트)**을 클릭합니다. Windows 7을 사용하는 경우 **관리 도구**에서 **ODBC 데이터 원본(32비트)** 또는 **ODBC 데이터 원본(64비트)**을 선택합니다. **ODBC 데이터 원본 관리자** 대화 상자가 시작됩니다.

	![OBDC 데이터 원본 관리자][img-hdi-simbahiveodbc-datasource-admin]

3. 사용자 DNS에서 **추가**를 클릭하여 **새 데이터 원본 만들기** 마법사를 엽니다.
4. **Microsoft Hive ODBC 드라이버**를 선택한 후 **마침**을 클릭합니다. **Microsoft Hive ODBC Driver DNS Setup** 대화 상자가 시작됩니다.

5. 다음 값을 입력하거나 선택합니다.

    속성|설명
    ---|---
    데이터 원본 이름|데이터 원본에 이름 지정
    호스트|&lt;HDInsightClusterName>.azurehdinsight.net을 입력합니다. 예를 들면 myHDICluster.azurehdinsight.net과 같습니다.
    포트|<strong>443</strong>을 사용합니다. (이 포트는 563에서 443으로 변경됨)
    데이터베이스|<strong>기본값</strong>을 사용합니다.
    Hive 서버 유형|<strong>Hive 서버 2</strong> 선택
    메커니즘|<strong>Azure HDInsight Service</strong> 선택
    HTTP 경로|비워 둠
    사용자 이름|HDInsight 클러스터 사용자의 사용자 이름 입력. 클러스터 프로비전 프로세스 동안 만들어진 사용자 이름입니다. 빠른 생성 옵션을 사용한 경우 기본 사용자 이름은 <strong>admin</strong>입니다.
    암호|HDInsight 클러스터 사용자 암호 입력
    </table>

    **고급 옵션**을 클릭할 때 알아야 할 중요한 매개 변수가 몇 가지 있습니다.

    매개 변수|설명
    ---|---
    Use Native Query|선택하면 ODBC 드라이버가 TSQL을 HiveQL로 변환하지 않습니다. 순수 HiveQL 문을 제출하는 것이 확실한 경우에만 이 옵션을 사용합니다. SQL Server 또는 Azure SQL 데이터베이스에 연결하는 경우에는 이 옵션을 선택 취소한 상태로 둬야 합니다.
    Rows fetched per block|대량의 레코드를 가져오는 경우 최적의 성능을 위해 이 매개 변수를 조정해야 할 수 있습니다.
    Default string column length, Binary column length, Decimal column scale|데이터 형식 길이 및 정밀도는 데이터가 반환되는 방식에 영향을 줄 수 있습니다. 정밀도 손실 및/또는 잘림으로 인해 잘못된 정보가 반환될 수 있습니다.


	![고급 옵션][img-HiveOdbc-DataSource-AdvancedOptions]

6. **테스트**를 클릭하여 데이터 원본을 테스트합니다. 원본이 올바르게 구성된 경우 *테스트를 성공적으로 완료했습니다.*가 표시됩니다.
7. **확인**을 클릭하여 테스트 대화 상자를 닫습니다. 이제 새 데이터 원본이 **ODBC 데이터 원본 관리자**에 나열됩니다.
8. **확인**을 클릭하여 마법사를 종료합니다.

##HDInsight에서 Excel로 데이터 가져오기

다음 단계에 따라 위 단계에서 만든 ODBC 데이터 원본을 사용하여 hive 테이블에서 Excel 통합 문서로 데이터를 가져올 수 있습니다.

1. Excel에서 새 통합 문서나 기존 통합 문서를 엽니다.
2. **데이터** 탭에서 **다른 데이터 원본에서**를 클릭한 다음 **데이터 연결 마법사에서**를 클릭하여 **데이터 연결 마법사**를 시작합니다.

	![데이터 연결 마법사 열기][img-hdi-simbahiveodbc.excel.dataconnection]

3. 데이터 원본으로 **ODBC DSN**을 선택한 후 **다음**을 클릭합니다.
4. ODBC 데이터 원본에서 이전 단계에서 만든 데이터 원본 이름을 선택한 후 **다음**을 클릭합니다.
5. 마법사에서 클러스터의 암호를 다시 입력한 후, 필요한 경우 **테스트**를 클릭하여 구성을 다시 확인합니다.
6. **확인**을 클릭하여 테스트 대화 상자를 닫습니다.
7. **확인**을 클릭합니다. **데이터베이스 및 테이블 선택** 대화 상자가 열릴 때까지 기다립니다. 몇 초 정도 걸릴 수 있습니다.
8. 가져올 테이블을 선택한 후 **다음**을 클릭합니다. *hivesampletable*은 HDInsight 클러스터와 함께 제공되는 샘플 hive 테이블입니다. 만들지 않은 경우 선택할 수 있습니다. Hive 쿼리 실행 및 Hive 테이블 만들기에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.
8. **마침**을 클릭합니다.
9. **데이터 가져오기** 대화 상자에서 쿼리를 변경하거나 지정할 수 있습니다. 이렇게 하려면 **속성**을 클릭합니다. 몇 초 정도 걸릴 수 있습니다.
10. **정의** 탭을 클릭한 후 **명령 텍스트** 텍스트 상자에서 Hive SELECT 문에 **LIMIT 200**을 추가합니다. 이렇게 수정하면 반환되는 레코드 제한을 200으로 설정합니다.

	![연결 속성][img-hdi-simbahiveodbc-excel-connectionproperties]

11. **확인**을 클릭하여 연결 속성 대화 상자를 닫습니다.
12. **확인**을 클릭하여 **데이터 가져오기** 대화 상자를 닫습니다.
13. 암호를 다시 입력한 후 **확인**을 클릭합니다. 데이터를 Excel로 가져올 때까지 몇 초 정도 걸립니다.

##다음 단계

이 문서에서는 Microsoft Hive ODBC 드라이버를 사용하여 HDInsight Service에서 Excel로 데이터를 가져오는 방법을 알아보았습니다. 마찬가지로 HDInsight Service에서 SQL 데이터베이스로 데이터를 가져올 수 있습니다. 데이터를 HDInsight Service에 업로드할 수도 있습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]
- [HDInsight에 데이터 업로드][hdinsight-upload-data]
- [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png

<!---HONumber=AcomDC_0914_2016-->