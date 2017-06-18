<properties
pageTitle="Azure HDInsight에서 Apache Storm으로 Azure 데이터 레이크 저장소 사용"
description="HDInsight의 Apache Storm 토폴로지에서 Azure 데이터 레이크 저장소로 데이터를 작성하는 방법을 알아봅니다. 이 문서 및 관련된 예제는 HdfsBolt 구성 요소가 데이터 레이크 저장소에 작성하는 데 사용될 수 있는 방법을 보여줍니다."
services="hdinsight"
documentationCenter="na"
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/06/2016"
ms.author="larryfr"/>

#HDInsight에서 Apache Storm으로 Azure 데이터 레이크 저장소 사용

Azure 데이터 레이크 저장소는 데이터에 대한 높은 처리량, 가용성, 내구성 및 안정성을 제공하는 HDFS와 호환 가능한 클라우드 저장소 서비스입니다. 이 문서에서 [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html)를 사용하여 Azure 데이터 레이크 저장소에 데이터를 작성하는 데 Java 기반 Storm 토폴로지를 사용하는 방법을 알아보고 이는 Apache Storm의 일부로 제공됩니다.

> [AZURE.IMPORTANT] 이 문서에 사용된 예제 토폴로지는 HDInsight 클러스터의 Storm에 포함되는 구성 요소에 의존하며 다른 Apache Storm 클러스터와 함께 사용될 때 Azure 데이터 레이크 저장소와 함께 작동하도록 수정이 필요할 수 있습니다.

##필수 조건

* [Java JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 이상
* [Maven 3.x](https://maven.apache.org/download.cgi)
* Azure 구독
* HDInsight 클러스터 버전 3.2의 Storm. HDInsight 클러스터에서 새 Storm을 만들려면 [Azure를 사용하는 Data Lake 저장소로 HDInsight 사용](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) 문서의 단계를 따릅니다. 이 문서의 단계는 새 HDInsight 클러스터 및 Azure 데이터 레이크 저장소를 만드는 과정을 안내합니다.

    > [AZURE.IMPORTANT] HDInsight 클러스터를 만들 때 __Storm__을 클러스터 유형으로 선택하고 버전으로 __3.2__를 선택해야 합니다. OS는 Windows 또는 Linux일 수 있습니다.

###환경 변수 구성

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* __JAVA\_HOME__ - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`과 유사한 값이어야 합니다.

* __PATH__ - 다음 경로를 포함해야 합니다.

    * __JAVA\_HOME__ 또는 그와 동등한 경로
    
    * __JAVA\_HOME\\bin__ 또는 그와 동등한 경로
    
    * Maven이 설치된 디렉터리

##토폴로지 구현

이 문서에 사용된 예제는 Java로 작성되었으며 다음과 같은 구성 요소를 사용합니다.

* __TickSpout__: 토폴로지의 다른 구성 요소에서 사용하는 데이터를 생성합니다.

* __PartialCount__: TickSpout에 의해 생성된 이벤트를 계산합니다.

* __FinalCount__: PartialCount에서 개수 데이터를 집계합니다.

* __ADLStoreBolt__: [HdfsBolt](http://storm.apache.org/javadoc/apidocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) 구성 요소를 사용하여 Azure Data Lake 저장소에 데이터를 작성합니다.

이 토폴로지를 포함하는 프로젝트는 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)에서 다운로드로 제공됩니다.

###ADLStoreBolt 이해

ADLStoreBolt는 Azure 데이터 레이크를 작성하는 토폴로지에서 HdfsBolt 인스턴스에 사용되는 이름입니다. Microsoft에서 만든 HdfsBolt의 특별한 버전은 아닙니다. 그러나 데이터 레이크와의 통신에 Azure HDInsight와 함께 포함된 Hadoop 구성 요소 뿐만 아니라 핵심 사이트 구성 값에 의존합니다.

특히 HDInsight 클러스터를 만들 때 Azure 데이터 레이크 저장소와 연결할 수 있습니다. 선택한 데이터 레이크 저장소에 대한 핵심 사이트에 항목을 작성하며 이는 hadoop 클라이언트 및 hadoop-hdfs와 같은 구성 요소가 데이터 레이크 저장소와 통신할 수 있도록 하는 데 사용됩니다.

> [AZURE.NOTE] Microsoft에는 Azure 데이터 레이크 저장소 및 Azure Blob 저장소와 통신할 수 있는 Apache Hadoop 및 Storm 프로젝트에 기여한 코드가 있지만 이 기능은 다른 Hadoop 및 Storm 배포에 기본적으로 포함될 수 없습니다.

토폴로지의 HdfsBolt에 대한 구성은 다음과 같습니다.

    // 1. Create sync and rotation policies to control when data is synched
    //    (written) to the file system and when to roll over into a new file.
    SyncPolicy syncPolicy = new CountSyncPolicy(1000);
    FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
    // 2. Set the format. In this case, comma delimited
    RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
    // 3. Set the directory name. In this case, '/stormdata/'
    FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
    // 4. Create the bolt using the previously created settings,
    //    and also tell it the base URL to your Data Lake Store.
    // NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
    HdfsBolt adlsBolt = new HdfsBolt()
		.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
      	.withRecordFormat(recordFormat)
      	.withFileNameFormat(fileNameFormat)
      	.withRotationPolicy(rotationPolicy)
      	.withSyncPolicy(syncPolicy);
    // 4. Give it a name and wire it up to the bolt it accepts data
    //    from. NOTE: The name used here is also used as part of the
    //    file name for the files written to Data Lake Store.
    builder.setBolt("ADLStoreBolt", adlsBolt, 1)
      .globalGrouping("finalcount");
      
HdfsBolt 사용에 익숙한 경우 URL을 제외하고 모두 표준 구성임을 확인할 수 있습니다. URL은 Azure 데이터 레이크 저장소의 루트에 대한 경로를 제공합니다.

데이터 레이크 저장소에 대한 작성 작업은 HdfsBolt를 사용하고 URL 변경이기 때문에 HdfsBolt를 사용하여 HDFS 또는 WASB를 작성하는 기존 토폴로지를 가져와서 Azure 데이터 레이크 저장소를 사용하도록 쉽게 변경할 수 있습니다.

##토폴로지 빌드 및 패키지

1. [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)에서 개발 환경에 예제 프로젝트를 다운로드합니다.

2. 편집기에서 `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` 파일을 열고 `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`을 포함하는 줄을 찾습니다. __MYDATALAKE__를 HDInsight 서버를 만들 때 사용한 Azure Data Lake 저장소의 이름으로 변경합니다.

3. 명령 프롬프트, 터미널 또는 셸 세션에서 다운로드한 프로젝트의 루트에 디렉터리를 변경하고 다음 명령을 실행하여 토폴로지를 빌드하고 패키지합니다.

        mvn compile
        mvn package
    
    빌드 및 패키징이 완료되면 `target`이라는 새 디렉터리가 있고 이는 `StormToDataLakeStore-1.0-SNAPSHOT.jar`라고 명명된 파일을 포함합니다. 컴파일된 토폴로지를 포함합니다.

##Linux 기반 HDInsight에 배포 및 실행

HDInsight 클러스터에서 Linux 기반 Storm을 만든 경우 다음 단계를 사용하여 토폴로지를 배포하고 실행합니다.

1. 다음 명령을 사용하여 HDInsight 클러스터에 토폴로지를 복사합니다. __USER__를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. __CLUSTERNAME__을 클러스터 이름으로 바꿉니다.

        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
    
    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
    
    > [AZURE.NOTE] 개발을 위해 Windows 클라이언트를 사용하는 경우 `scp` 명령이 없을 수 있습니다. 그렇다면 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 `pscp`를 사용할 수 있습니다.

2. 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결하도록 다음을 사용합니다. __USER__를 클러스터를 만들 때 사용한 SSH 사용자 이름으로 대체합니다. __CLUSTERNAME__을 클러스터 이름으로 바꿉니다.

        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net

    메시지가 표시되면 클러스터에 대한 SSH 사용자를 만들 때 사용한 암호를 입력합니다. 암호 대신 공용 키를 사용하는 경우 `-i` 매개 변수를 사용하여 개인 키와 일치하는 경로를 지정합니다.
    
    > [AZURE.NOTE] 개발을 위해 Windows 클라이언트를 사용하는 경우 PuTTY 클라이언트를 사용하여 클러스터에 연결하는 정보는 [Windows에서 SSH로 Linux 기반 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-windows.md)에 있는 내용을 수행합니다.
    
3. 연결되면 다음을 사용하여 토폴로지를 시작합니다.

        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar com.microsoft.example.StormToDataLakeStore datalakewriter
    
    토폴로지를 `datalakewriter`라는 친근한 이름으로 시작합니다.

##Windows 기반 HDInsight에 배포 및 실행

1. 웹 브라우저를 열고 HTTPS://CLUSTERNAME.azurehdinsight.net으로 이동합니다. 여기서 __CLUSTERNAME__은 HDInsight 클러스터의 이름입니다. 메시지가 표시되면 클러스터를 만들 때 이 계정에 사용한 관리 사용자 이름(`admin`) 및 암호를 제공합니다.

2. Storm 대시보드의 __Jar 파일__ 드롭다운에서 __찾아보기__를 선택한 다음 `target` 디렉터리에서 StormToDataLakeStore-1.0-SNAPSHOT.jar 파일을 선택합니다. 폼에서 다른 항목에 다음 값을 사용합니다.

    * 클래스 이름: com.microsoft.example.StormToDataLakeStore
    * 추가 매개 변수: datalakewriter
    
    ![Storm 대시보드의 이미지](./media/hdinsight-storm-write-data-lake-store/submit.png)

3. __전송__ 단추를 선택하여 토폴로지를 업로드하고 시작합니다. 토폴로지가 시작되면 아래 결과 필드에서 __전송__ 단추는 다음과 유사한 정보를 표시해야 합니다.

        Process exit code: 0
        Currently running topologies:
        Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
        -------------------------------------------------------------------
        datalakewriter       ACTIVE     68         8            10        

##출력 데이터 보기

데이터를 보는 몇 가지 방법이 있습니다. 이 섹션에서는 Azure 포털 및 `hdfs` 명령을 사용하여 데이터를 볼 수 있습니다.

> [AZURE.NOTE] 토폴로지가 출력 데이터를 검사하기 전에 몇 분 동안 실행할 수 있도록 해야 합니다. 그러면 데이터가 Azure 데이터 레이크 저장소의 여러 파일에 동기화됩니다.

* __[Azure 포털](https://portal.azure.com)에서__: 포털에서 HDInsight와 함께 사용하는 Azure Data Lake 저장소를 선택합니다.

    > [AZURE.NOTE] Azure 포털 대시보드에 Data Lake 저장소를 고정하지 않은 경우 왼쪽 목록 맨 아래에서 __찾아보기__를 선택한 다음 __Azure Data Lake__ 마지막으로 저장소를 선택하여 찾을 수 있습니다.
    
    Data Lake 저장소의 맨 위에 있는 아이콘에서 __데이터 탐색기__를 선택합니다.
    
    ![데이터 탐색 아이콘](./media/hdinsight-storm-write-data-lake-store/dataexplorer.png)
    
    다음으로 __stormdata__ 폴더를 선택합니다. 텍스트 파일의 목록을 표시해야 합니다.
    
    ![텍스트 파일](./media/hdinsight-storm-write-data-lake-store/stormoutput.png)
    
    파일의 내용 중 하나를 선택하여 내용을 봅니다.

* __클러스터에서__: SSH(Linux 클러스터) 또는 원격 데스크톱 (Windows 클러스터)를 사용하여 HDInsight 클러스터에 연결한 경우 데이터를 보려면 다음을 사용할 수 있습니다. __DATALAKE__를 Data Lake 저장소의 이름으로 바꾸기

        hdfs dfs -cat adl://DATALAKE.azuredatalakestore.net/stormdata/*.txt

    디렉터리에 저장된 텍스트 파일을 연결하고 다음과 유사한 정보를 표시합니다.
    
        406000000
        407000000
        408000000
        409000000
        410000000
        411000000
        412000000
        413000000
        414000000
        415000000
        
##토폴로지 중지

Storm 토폴로지가 중지될 때까지 실행거나 클러스터가 삭제됩니다. 토폴로지를 중지하려면 다음 정보를 사용합니다.

__Linux 기반 HDInsight의 경우__:

SSH 세션에서 클러스터로 다음 명령을 사용합니다.

    storm kill datalakewriter

__Windows 기반 HDInsight의 경우__:

1. Storm 대시보드(https://CLUSTERNAME.azurehdinsight.net,)에서 페이지의 위쪽에 있는 __Storm UI__를 선택합니다.

2. Storm UI가 로드되면 __datalakewriter__ 링크를 선택합니다.

    ![datalakewriter에 연결](./media/hdinsight-storm-write-data-lake-store/selecttopology.png)

3. __토폴로지 동작__ 섹션에서 __중단__을 선택한 다음 표시되는 대화 상자에서 확인을 선택합니다.

    ![토폴로지 동작](./media/hdinsight-storm-write-data-lake-store/topologyactions.png)

## 클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##다음 단계

이제 Storm을 사용하여 Azure Data Lake 저장소에 작성하는 방법을 알아보았으므로 다른 [HDInsight에 대한 Storm 예제](hdinsight-storm-example-topology.md)를 검색합니다.

<!---HONumber=AcomDC_0914_2016-->