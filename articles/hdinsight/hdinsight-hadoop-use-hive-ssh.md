<properties
   pageTitle="HDInsight(Hadoop)에서 Hive 셸 사용 | Microsoft Azure"
   description="Linux 기반 HDInsight 클러스터에서 Hive 셸을 사용하는 방법을 알아봅니다. SSh를 사용하여 HDInsight에 연결한 다음 Hive 셸을 사용하여 대화형으로 쿼리를 실행하는 방법을 알아봅니다."
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
   ms.date="07/19/2016"
   ms.author="larryfr"/>

#SSH 사용하는 HDInsight에서 Hadoop과 Hive 사용

[AZURE.INCLUDE [hive-선택기](../../includes/hdinsight-selector-use-hive.md)]

이 문서에서는 SSH(Secure Shell)를 사용하여 Azure HDInsight 클러스터의 Hadoop에 연결한 다음 Hive CLI(명령줄 인터페이스)를 사용하여 Hive 쿼리를 대화형으로 제출하는 방법을 배웁니다.

> [AZURE.IMPORTANT] Hive 명령을 Linux 기반 HDInsight 클러스터에서 사용할 수 있지만 Beeline을 사용하는 것이 좋습니다. Beeline은 하이브를 사용하기 위한 최신 클라이언트이고 HDInsight 클러스터에 포함됩니다. 사용에 대한 자세한 내용은 [Beeline을 사용하여 HDInsight에서 Hadoop과 Hive 사용](hdinsight-hadoop-use-hive-beeline.md)을 참조하세요.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* HDInsight 클러스터의 Linux 기반 Hadoop

* SSH 클라이언트. Linux, Unix 및 Mac OS에는 SSH 클라이언트가 함께 제공됩니다. Windows 사용자는 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)와 같은 클라이언트를 다운로드해야 합니다.

##<a id="ssh"></a>SSH를 사용하여 연결

SSH 명령을 사용하여 HDInsight 클러스터의 FQDN(정규화된 도메인 이름)에 연결합니다. FQDN은 지정한 클러스터 이름과 그 뒤에 오는 **.azurehdinsight.net**으로 구성됩니다. 예를 들어 다음 명령은 **myhdinsight**라는 클러스터에 연결합니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**SSH 인증을 위해 인증서 키를 제공한 경우** HDInsight 클러스터를 만들 때 클라이언트 시스템에서 개인 키의 위치를 지정해야 할 수도 있습니다.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**SSH 인증을 위해 암호를 제공한 경우** HDInsight 클러스터를 만들 때 메시지가 표시되면 암호를 제공해야 합니다.

HDInsight에서 SSH 사용에 대한 자세한 내용은 [Linux, OS X 및 Unix에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

###PuTTY(Windows 기반 클라이언트)

Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

##<a id="hive"></a>Hive 명령 사용

2. 연결되면 다음 명령을 사용하여 Hive CLI를 시작합니다.

        hive

3. CLI에서 다음 문을 입력하여 샘플 데이터를 사용하는 **log4jLogs**라는 새 테이블을 만듭니다.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    이러한 문은 다음 작업을 수행합니다.

    * **DROP TABLE** - 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다.
    * **CREATE EXTERNAL TABLE** - Hive에서 새 ‘외부’ 테이블을 만듭니다. 외부 테이블만 테이블 정의를 Hive에 저장합니다. 데이터는 원래 위치에 그대로 유지됩니다.
    * **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
    * **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치(example/data 디렉터리) 및 텍스트로 저장되었음을 Hive에 알립니다.
    * **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 경우 이 값을 포함하는 행이 3개 있으므로 **3** 값이 반환되어야 합니다.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** - .log로 끝나는 파일의 데이터만 반환하도록 Hive에 지시합니다. 데이터를 포함하는 sample.log 파일로 검색을 제한하며, 정의한 스키마와 일치하지 않는 다른 예제 데이터 파일의 데이터가 반환되지 않도록 합니다.

    > [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
    >
    > 외부 테이블을 삭제하면 데이터는 삭제되지 **않고** 테이블 정의만 삭제됩니다.

4. 다음 문을 사용하여 **errorLogs**라는 새 ‘내부' 테이블을 만듭니다.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    이러한 문은 다음 작업을 수행합니다.

    * **CREATE TABLE IF NOT EXISTS** - 테이블이 아직 없는 경우 테이블을 만듭니다. **EXTERNAL** 키워드가 사용되지 않으면 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 내부 테이블입니다.
    * **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
    * **덮어쓰기 삽입... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

    t4 열에 **[ERROR]**가 포함된 행만 **errorLogs** 테이블에 저장되었는지 확인하려면 다음 문을 사용하여 **errorLogs**의 모든 행을 반환합니다.

        SELECT * from errorLogs;

    데이터 중 t4 열에 모두 **[ERROR]**가 포함된 3개 행이 반환되어야 합니다.

    > [AZURE.NOTE] 외부 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Hive 명령을 사용하면 쉽게 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고 출력을 검색할 수 있습니다.

##<a id="nextsteps"></a>다음 단계

HDInsight의 Hive에 대한 일반적인 정보:

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0914_2016-->