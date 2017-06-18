<properties
   pageTitle="HDInsight에서 Curl과 Hadoop Sqoop 사용 | Microsoft Azure"
   description="원격으로 Curl을 사용하여 HDInsight에 Sqoop 작업을 제출하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jgao"/>

#Curl을 사용하여 HDInsight에서 Hadoop으로 Sqoop 작업 실행

[AZURE.INCLUDE [sqoop 선택기](../../includes/hdinsight-selector-use-sqoop.md)]

이 문서에서 Curl을 사용하여 Azure HDInsight 클러스터의 Hadoop에서 Sqoop 작업을 실행하는 방법을 알아봅니다.

Sqoop 작업을 실행하고 모니터링하며 결과를 검색하는 원시 HTTP 요청을 사용하여 HDInsight와 상호 작용하는 방법을 설명하는 데 Curl을 사용합니다. 이 Curl은 HDInsight 클러스터에서 제공하는 WebHCat REST API(이전의 Templeton)를 사용하여 작동합니다.

> [AZURE.NOTE] Linux 기반 Hadoop 서버를 익숙하게 사용하지만 HDInsight는 처음인 경우 [HDInsight의 Linux 기반 Hadoop에 대해 알아야 할 정보](hdinsight-hadoop-linux-information.md)를 참조하세요.

##필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터에서 Hadoop(Linux 또는 Windows 기반)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##Curl을 사용하여 Sqoop 작업 제출

> [AZURE.NOTE] WebHCat에서 Curl 또는 다른 모든 REST 통신을 사용하는 경우 HDInsight 클러스터 관리자의 사용자 이름 및 암호를 제공하여 요청을 인증해야 합니다. 또한 클러스터 이름을 서버로 요청을 보내는 데 사용되는 URI(Uniform Resource Identifier)의 일부로 사용해야 합니다.
>
> 이 섹션의 명령에서 **USERNAME**은 클러스터에 대해 인증할 사용자로 바꾸고 **PASSWORD**는 사용자 계정의 암호로 바꿉니다. **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.
>
> REST API는 [기본 인증](http://en.wikipedia.org/wiki/Basic_access_authentication)을 통해 보안됩니다. 자격 증명이 안전하게 서버에 전송되도록 하려면 항상 보안 HTTP(HTTPS)를 사용하여 요청해야 합니다.

1. 명령줄에서 다음 명령을 사용하여 HDInsight 클러스터에 연결할 수 있는지 확인합니다.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    그러면 다음과 같은 응답이 표시됩니다.

        {"status":"ok","version":"v1"}

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-u** - 요청을 인증하는 데 사용되는 사용자 이름 및 암호입니다.
    * **-G** - GET 요청임을 나타냅니다.

    URL 시작 부분인 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에서 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다.

2. 다음을 사용하여 Sqoop 작업을 제출합니다.


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-d** - `-G`는 사용되지 않으므로 요청은 기본적으로 POST 메서드로 설정됩니다. `-d`는 요청과 함께 전송되는 데이터 값을 지정합니다.

        * **user.name** - 명령을 실행하는 사용자입니다.

        * **명령** - 실행할 Sqoop 명령입니다.

        * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

    이 명령은 작업 상태를 확인하는데 사용할 수 있는 작업 ID를 반환해야 합니다.

        {"id":"job_1415651640909_0026"}

3. 작업 상태를 확인하려면 다음 명령을 사용합니다. **JOBID**를 이전 단계에서 반환된 값으로 바꿉니다. 예를 들어 반환 값이 `{"id":"job_1415651640909_0026"}`인 경우 **JOBID**는 `job_1415651640909_0026`이 됩니다.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	작업이 완료되면 상태는 **SUCCEEDED**가 됩니다.

    > [AZURE.NOTE] 이 Curl 요청은 작업에 관한 정보가 있는 JSON(JavaScript Object Notation) 문서를 반환합니다. jq는 상태 값을 검색하는 데만 사용됩니다.

4. 작업 상태가 **SUCCEEDED**로 변경되면 Azure Blob 저장소에서 작업 결과를 검색할 수 있습니다. 쿼리와 함께 전달된 `statusdir` 매개 변수에는 출력 파일의 위치(이 예제의 경우 **wasbs:///example/curl**)가 포함됩니다. 이 주소는 HDInsight 클러스터에서 사용하는 기본 저장소 컨테이너의 **example/curl** 디렉터리에 작업의 출력을 저장합니다.

    [Azure CLI](../xplat-cli-install.md)를 사용하여 이러한 파일을 나열하고 다운로드할 수 있습니다. 예를 들어 **example/curl**에 파일을 나열하려면 다음 명령을 사용합니다.

		azure storage blob list <container-name> example/curl

	파일을 다운로드하려면 다음을 사용합니다.

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] `-a` 및 `-k` 매개 변수를 사용하여 Blob을 포함하는 저장소 계정 이름을 지정하거나 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY** 환경 변수를 설정해야 합니다. 자세한 내용은 <a href="hdinsight-upload-data.md" target="\_blank"를 참조하세요.

##제한 사항

* 대량 내보내기 - Linux 기반 HDInsight와 함께 Microsoft SQL Server 또는 Azure SQL 데이터베이스에 데이터를 내보내는 데 사용된 Sqoop 커넥터도 현재 대량 삽입을 지원하지 않습니다.

* 배치 - Linux 기반 HDInsight와 함께 삽입을 수행할 때 `-batch` 스위치를 사용하는 경우 Sqoop는 삽입 작업을 일괄 처리하는 대신 여러 삽입 작업을 수행합니다.

##요약

이 문서에서 볼 수 있듯이 원시 HTTP 요청을 사용하여 HDInsight 클러스터의 Sqoop 작업을 실행하고 모니터링하며 결과를 확인할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API 가이드</a>를 참조하세요.

##다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Sqoop 사용](hdinsight-use-sqoop.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0914_2016-->