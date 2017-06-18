<properties
   pageTitle="HDInsight에서 Curl과 Hadoop Hive 사용 | Microsoft Azure"
   description="원격으로 Curl을 사용하여 HDInsight에 Pig 작업을 제출하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#Curl을 사용하여 HDInsight에서 Hadoop으로 Hive 쿼리 실행

[AZURE.INCLUDE [hive-선택기](../../includes/hdinsight-selector-use-hive.md)]

이 문서에서 Azure HDInsight 클러스터의 Hadoop에서 Hive 쿼리를 실행하기 위해 Curl을 사용하는 방법에 대해 배웁니다.

Curl은 실행, 모니터링 및 Hive 쿼리 검색 결과를 원시 HTTP 요청을 사용하여 HDInsight와 함께 작용하는 방법을 설명하기 위해 사용됩니다. 이 Curl은 HDInsight 클러스터에서 제공하는 WebHCat REST API(이전의 Templeton)를 사용하여 작동합니다.

> [AZURE.NOTE] Linux 기반 Hadoop 서버를 익숙하게 사용하지만 HDInsight는 처음인 경우 [HDInsight의 Linux 기반 Hadoop에 대해 알아야 할 정보](hdinsight-hadoop-linux-information.md)를 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터에서 Hadoop(Linux 또는 Windows 기반)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Curl을 사용하여 Hive 쿼리 실행

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

    URL 시작 부분인 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**은 모든 요청에서 동일합니다. **/status** 경로는 요청이 서버에 대한 WebHCat(Templeton라고도 함)의 상태를 반환하는 경우 나타납니다. 또한 다음 명령을 사용하여 Hive의 버전을 요청할 수 있습니다.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    그러면 다음과 같은 응답이 반환됩니다.

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. 다음을 사용하여 **log4jLogs**라는 새 테이블을 만듭니다.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    이 명령에서 사용된 매개 변수는 다음과 같습니다.

    * **-d** - `-G`는 사용되지 않으므로 요청은 기본적으로 POST 메서드로 설정됩니다. `-d`는 요청과 함께 전송되는 데이터 값을 지정합니다.

        * **user.name** - 명령을 실행하는 사용자입니다.

        * **execute** - 실행할 HiveQL 문입니다.

        * **statusdir** - 이 작업의 상태를 기록할 디렉터리입니다.

    이러한 문은 다음 작업을 수행합니다.

    * **DROP TABLE** - 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.

    * **CREATE EXTERNAL TABLE** - Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블은 테이블 정의만 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.

		> [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
		>
		> 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

    * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.

    * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.

    * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 경우 이 값을 포함하는 행이 3개 있으므로 **3** 값이 반환되어야 합니다.

    > [AZURE.NOTE] Curl과 함께 사용할 경우 HiveQL 문 사이의 공백이 `+` 문자로 바뀝니다. 구분 기호와 같이 공백을 포함하는 따옴표로 묶인 값은 `+`로 바뀌지 않아야 합니다.

    * **INPUT\_\_FILE\_\_NAME LIKE '%25.log'** - .log로 끝나는 파일만 사용하도록 검색을 제한합니다. 없는 경우 Hive는 이 테이블에 대해 정의된 열 스키마와 일치하지 않는 파일을 포함하여 이 디렉터리와 해당 하위 디렉터리에 있는 모든 파일을 검색하려고 합니다.

    > [AZURE.NOTE] %25는 URL로 인코드된 형식의 %이므로 실제 조건은 `like '%.log'`입니다. %를 URL로 인코드해야 하므로 URL에서 특수 문자로 처리됩니다.

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

6. 다음 문을 사용하여 **errorLogs**라는 새 ‘내부' 테이블을 만듭니다.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    이러한 문은 다음 작업을 수행합니다.

    * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 내부 테이블입니다.

		> [AZURE.NOTE] 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

    * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
    * **덮어쓰기 삽입... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.
    * **SELECT** - 새 **errorLogs** 테이블에서 모든 행을 선택합니다.

7. 작업 상태를 확인하려면 반환된 작업 ID를 사용합니다. 작업이 성공하면 이전에 설명한 대로 Mac, Linux 및 Windows용 Azure CLI를 사용하여 결과를 다운로드하고 볼 수 있습니다. 결과는 **[ERROR]**를 포함하여 모두 3줄이어야 합니다.


##<a id="summary"></a>요약

이 문서에서 볼 수 있듯이, 실행, 모니터링 및 HDInsight 클러스터의 Hive 작업의 결과 확인하려면 원시 HTTP 요청을 사용할 수 있습니다.

이 문서에 사용된 REST 인터페이스에 대한 자세한 내용은 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat 참조</a>를 참조하세요.

##<a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

Hive와 함께 Tez를 사용하는 경우 디버깅 정보에 대한 다음 문서를 참조하세요.

* [Windows 기반 HDInsight 클러스터에서 Tez UI 사용](hdinsight-debug-tez-ui.md)

* [Linux 기반 HDInsight에서 Ambari Tez 보기 사용](hdinsight-debug-ambari-tez-view.md)

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