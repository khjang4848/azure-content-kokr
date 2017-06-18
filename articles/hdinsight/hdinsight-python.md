<properties
	pageTitle="HDInsight에서 Hive 및 Pig와 함께 Python 사용 | Microsoft Azure"
	description="HDInsight의 Hive 및 Pig에서 Python UDF(사용자 정의 함수)를 사용하는 방법, Azure에서의 Hadoop 기술 스택에 대해 알아봅니다."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/07/2016" 
	ms.author="larryfr"/>

#HDInsight에서 Hive 및 Pig와 함께 Python 사용

Hive 및 Pig는 HDInsight의 데이터 작업에 적합하지만 보다 일반적인 언어가 필요한 경우도 있습니다. Hive 및 Pig를 통해 다양한 프로그래밍 언어를 사용하여 UDF(사용자 정의 함수)를 만들 수 있습니다. 이 문서에서는 Hive 및 Pig에서 Python UDF를 사용하는 방법을 알아봅니다.

##요구 사항

* HDInsight 클러스터(Windows 또는 Linux 기반)

* 텍스트 편집기

    > [AZURE.IMPORTANT] Linux 기반 HDInsight 서버를 사용하지만 Windows 클라이언트에서 Python 파일을 만드는 경우 LF를 줄 끝으로 사용하는 편집기를 사용해야 합니다. 편집기에서 LF 또는 CRLF를 사용하는지 여부가 확실하지 않은 경우 [문제 해결](#troubleshooting) 섹션에서 HDInsight 클러스터에서 유틸리티를 사용하여 CR 문자를 제거하는 단계를 참조하세요.
    
##<a name="python"></a>HDInsight의 Python

Python2.7은 기본적으로 HDInsight 3.0 이상의 클러스터에 설치됩니다. Hive는 이 버전의 Python과 함께 사용하여 스트림을 처리할 수 있습니다(데이터는 STDOUT/STDIN을 사용하여 Hive와 Python 간에 전달됨).

HDInsight에는 Java로 작성된 Python 구현인 Jython도 포함되어 있습니다. Pig는 스트리밍을 사용하지 않고도 Jython과 통신하는 방법을 인식하므로, Pig를 사용할 때 Jython이 선호됩니다. 그러나 일반적인 Python (C Python,)도 마찬가지로 Pig와 사용할 수 있습니다.

##<a name="hivepython"></a>Hive 및 Python

Python은 HiveQL **TRANSFORM** 문을 통해 Hive의 UDF로 사용할 수 있습니다. 예를 들어 다음 HiveQL은 **streaming.py** 파일에 저장된 Python 스크립트를 호출합니다.

**Linux 기반 HDInsight**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'python streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

**Windows 기반 HDInsight**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] Windows 기반 HDInsight 클러스터에서는 **USING** 절에서 python.exe의 전체 경로를 지정해야 합니다. 이는 항상 `D:\Python27\python.exe`입니다.

다음은 이 예제에서 수행하는 작업입니다.

1. 파일의 시작 부분에 있는 **add file** 문이 분산 캐시에 **streaming.py** 파일을 추가하므로, 클러스터의 모든 노드에서 액세스할 수 있습니다.

2. **SELECT TRANSFORM ... USING** 문은 **hivesampletable**의 데이터를 선택하고 clientid, devicemake 및 devicemodel을 **streaming.py** 스크립트로 전달합니다.

3. **AS** 절은 **streaming.py**에서 반환된 필드를 설명합니다.

<a name="streamingpy"></a> 다음은 HiveQL 예제에 사용된 **streaming.py** 파일입니다.

	#!/usr/bin/env python

	import sys
	import string
	import hashlib

	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break

	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

스트리밍을 사용하고 있으므로, 이 스크립트는 다음을 수행해야 합니다.

1. STDIN에서 데이터를 읽습니다. 이 예제에서는 `sys.stdin.readline()`을 사용하여 데이터를 읽습니다.

2. 텍스트 데이터만 필요하고 줄의 끝 표시자는 필요하지 않으므로 후행 줄 바꿈 문자는 `string.strip(line, "\n ")`를 사용하여 제거합니다.

2. 스트림 처리를 할 때 모든 값과 각 값 사이의 탭 문자가 한 줄에 포함됩니다. 따라서 `string.split(line, "\t")`를 사용하여 각 탭의 입력을 분할하여 필드만 반환할 수 있습니다.

3. 처리가 완료되면 출력을 단일 행(각 필드 사이에 탭 포함)으로 STDOUT에 작성해야 합니다. `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`을 사용하여 그렇게 할 수 있습니다.

4. 이 작업은 모두 `while` 루프에서 발생하며, 이 루프는 `line`이 읽히지 않아서 `break` 지점이 루프를 종료하고 스크립트도 종료될 때까지 반복됩니다.

그 이후에는 스크립트가 `devicemake` 및 `devicemodel`의 입력 값을 연결하고, 연결된 값의 해시를 계산합니다. 단순하게 말하면 Hive에서 호출된 Python 스크립트가 기능을 수행하는 방식의 기본 내용을 설명합니다. Python 스크립트의 기능은 루프, 입력이 더 이상 없을 때까지 입력 읽기, 탭에서 각 입력 줄 구분, 처리, 탭으로 구분된 출력을 한 줄에 쓰기입니다.

HDInsight 클러스터에서 이 예제를 실행하는 방법에 대해서는 [예제 실행](#running)을 참조하세요.

##<a name="pigpython"></a>Pig 및 Python

**GENERATE** 문을 통해 Python 스크립트를 Pig의 UDF로 사용할 수 있습니다. 이렇게 수행하려면 Jython (Java 가상 컴퓨터에 구현된 Python)과 C Python (일반 Python)을 사용하는 두 가지 방법이 있습니다.

Jython이 JVM에서 실행되고, JVM에서도 실행되는 Pig로부터 고유하게 호출된다는 것이 주요 차이점입니다. C Python은 C 언어로 작성된 외부 프로세스입니다. 따라서 JVM에서 Pig로부터 호출된 데이터는 Python 프로세스에서 실행 중인 스크립트로 전송된 다음 해당 데이터의 출력이 Pig로 다시 전송됩니다.

Pig에서 Jython 또는 C Python을 사용하여 스크립트를 실행하는지를 확인하려면 Pig Latin에서 Python 스크립트를 참조 할 때 __register__를 사용합니다. 이는 사용할 인터프리터와 스크립트에 대해 만들려는 별칭을 Pig에 알려줍니다. 다음 예제에서는 Pig as __myfuncs__를 사용하여 스크립트를 등록합니다.

* __Jython 사용__: `register '/path/to/pig_python.py' using jython as myfuncs;`
* __C Python 사용__: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [AZURE.IMPORTANT] Jython을 사용할 경우 pig\_jython 파일에 대한 경로는 로컬 경로 또는 WASB:// 경로입니다. 그러나 C Python을 사용할 경우에는 Pig 작업을 제출하는 데 사용하는 노드의 로컬 파일 시스템에 있는 파일을 참조해야 합니다.

등록하기 전이라면 두 경우에 대한 이 예제의 Pig Latin은 다음과 같이 모두 동일합니다.

    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

다음은 이 예제에서 수행하는 작업입니다.

1. 첫 번째 줄의 **LOGS**에 **sample.log** 샘플 데이터 파일을 로드합니다. 이 로그 파일에 일관된 스키마가 없으므로 각 레코드(이 경우, **LINE**)를 **chararray**로 정의합니다. Chararray는 결과적으로 문자열입니다.

2. 다음 줄에서는 모든 Null 값을 필터링하여 **LOG**에 작업 결과를 저장합니다.

3. 그런 다음 **LOG**의 레코드에 대해 반복하고 **GENERATE**를 사용하여 **create\_structure** 메서드를 호출합니다. 이 메서드는 **myfuncs**로 로드된 Python/Jython 스크립트에 포함되어 있습니다. **LINE**은 현재 레코드를 함수에 전달하는 데 사용됩니다.

4. 마지막으로 출력은 **DUMP** 명령을 사용하여 STDOUT로 덤프됩니다. 따라서 작업이 완료되면 바로 결과가 표시됩니다. 실제 스크립트에서는 보통 데이터를 새 파일에 **저장**합니다.

C Python과 Jython 간에 실제 Python 스크립트 파일은 유사하며, C Python을 사용하는 경우 __pig\_util__로부터 가져와야 한다는 것이 유일한 차이점입니다. . 다음은 __pig\_python.py__ 스크립트입니다.

<a name="streamingpy"></a>

    # Uncomment the following if using C Python
    #from pig_util import outputSchema

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail

> [AZURE.NOTE] 'pig\_util'은 설치에 대해 걱정할 필요 없이 자동으로 스크립트에 사용할 수 있는 것입니다.

입력에 대한 일관된 스키마가 없으므로, 앞에서 **LINE** 입력을 chararray로 정의했습니다. Python 스크립트는 데이터를 출력에 대한 일관된 스키마로 변환하는 것입니다. 다음과 같이 작업합니다.

1. **@outputSchema** 문은 Pig에 반환되는 데이터의 형식을 정의합니다. 이 경우 Pig 데이터 형식은 **데이터 모음**입니다. 모음에는 모두 chararray(문자열)인 다음과 같은 필드가 포함됩니다.

	* date - 로그 항목이 생성된 날짜
	* time - 로그 항목이 생성된 시간
	* classname - 항목이 생성된 클래스 이름
	* level - 로그 수준
	* detail - 로그 항목에 대한 세부 정보

2. 그런 다음 **def create\_structure(input)**가 Pig에서 줄 항목을 전달할 함수를 정의합니다.

3. 예제 데이터인 **sample.log**는 대개 date, time, classname, level 및 detail(반환을 원하는 필드) 스키마를 준수합니다. 그러나 '*java.lang.Exception*' 문자열로 시작하며 스키마와 일치하도록 수정해야 하는 몇 개의 줄도 포함되어 있습니다. **if** 문이 이러한 줄을 확인한 후 '*java.lang.Exception*' 문자열을 끝으로 이동하고, 원하는 출력 스키마에 따라 인라인 데이터를 가져오는 입력 데이터를 전달합니다.

4. 그런 다음 **split** 명령을 사용하여 첫 4개 공백 문자에서 데이터를 분할합니다. 그 결과, 5개 값이 생성되어 **date**, **time**, **classname**, **level** 및 **detail**에 할당됩니다.

5. 마지막으로 값은 Pig로 반환됩니다.

데이터가 Pig로 반환되면 **@outputSchema** 문에 정의된 일관된 스키마를 포함합니다.

##<a name="running"></a>예제 실행

Linux 기반 HDInsight 클러스터를 사용하는 경우 아래의 **SSH** 단계를 사용합니다. Windows 기반 HDInsight 클러스터 및 Windows 클라이언트를 사용하는 경우 아래의 **PowerShell** 단계를 사용합니다.

###SSH

SSH 사용에 대한 자세한 내용은 <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용</a> 또는 <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용</a>을 참조하세요.

1. Python 예제인 [streaming.py](#streamingpy)와 [pig\_python.py](#jythonpy)를 사용하여 개발 컴퓨터에 파일의 로컬 사본을 만듭니다.

2. `scp`를 사용하여 파일을 HDInsight 클러스터에 복사합니다. 예를 들어 다음은 **mycluster**라는 클러스터에 파일을 복사합니다.

		scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. SSH를 사용하여 클러스터에 연결합니다. 예를 들어 다음은 **myuser**라는 사용자로 **mycluster**라는 클러스터에 연결합니다.

		ssh myuser@mycluster-ssh.azurehdinsight.net

4. SSH 세션에서 이전에 업로드된 python 파일을 클러스터의 WASB 저장소에 추가합니다.

		hdfs dfs -put streaming.py /streaming.py
		hdfs dfs -put pig_python.py /pig_python.py

파일을 업로드한 후 다음 단계를 사용하여 Hive 및 Pig 작업을 실행합니다.

####Hive

1. `hive` 명령을 사용하여 Hive 셸을 시작합니다. 셸이 로드되면 `hive>` 프롬프트가 한 번 표시됩니다.

2. `hive>` 프롬프트에 다음을 입력합니다.

		add file wasbs:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'python streaming.py' AS
		  (clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. 마지막 줄을 입력하면 작업이 시작됩니다. 최종적으로 다음과 유사한 출력이 반환됩니다.

		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

1. `pig` 명령을 사용하여 셸을 시작합니다. 셸이 로드되면 `grunt>` 프롬프트가 한 번 표시됩니다.

2. Jython 인터프리터를 사용하는 Python 스크립트를 실행하도록 `grunt>` 프롬프트에서 다음 문을 입력합니다.

		Register wasbs:///pig_python.py using jython as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

3. 다음 줄을 입력하면 작업이 시작됩니다. 최종적으로 다음과 유사한 출력이 반환됩니다.

		((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
		((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
		((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
		((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
		((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. `quit`를 사용하여 Grunt 셸을 종료한 후 다음을 사용하여 로컬 파일 시스템에 있는 pig\_python.py 파일을 편집합니다.

    nano pig\_python.py

5. 편집기에서 다음 줄의 시작 부분에 있는 `#` 문자를 제거하여 해당 줄의 주석 처리를 제거합니다.

        #from pig_util import outputSchema

    변경했으면 Ctrl+X를 사용하여 편집기를 종료합니다. Y를 선택한 다음 enter 키를 눌러 변경 내용을 저장합니다.

6. `pig` 명령을 사용하여 셸을 다시 시작합니다. `grunt>` 프롬프트에서 다음 문을 사용하여 Jython 인터프리터를 사용하는 Python 스크립트를 실행합니다.

        Register 'pig_python.py' using streaming_python as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

    이 작업이 완료되면 이전에 Jython을 사용하여 스크립트를 실행한 때와 같은 출력이 표시됩니다.

###PowerShell

이 단계에서는 Azure PowerShell을 사용합니다. 이 도구를 아직 개발 컴퓨터에 설치하여 구성하지 않은 경우 다음 단계를 사용하기 전에 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Python 예제인 [streaming.py](#streamingpy)와 [pig\_python.py](#jythonpy)를 사용하여 개발 컴퓨터에 파일의 로컬 사본을 만듭니다.

2. 다음 PowerShell 스크립트를 사용하여 **streaming.py**와 **pig\_python.py** 파일을 서버에 업로드합니다. 스크립트의 첫 3개 줄에서 Azure HDInsight 클러스터의 이름과 **streaming.py** 및 **pig\_python.py** 파일의 경로를 바꿉니다.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\pig_python.py"

		$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

		#Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
		
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "pig_python.py" `
            -Container $container `
            -Context $context

	이 스크립트는 HDInsight 클러스터의 정보를 검색한 후 계정 및 기본 저장소 계정의 키를 추출하고 컨테이너의 루트에 파일을 업로드합니다.

	> [AZURE.NOTE] 그 외에 스크립트를 업로드하는 방법은 [HDInsight에서 Hadoop 작업용 데이터 업로드](hdinsight-upload-data.md) 문서에서 찾아볼 수 있습니다.

파일을 업로드한 후 다음 PowerShell 스크립트를 사용하여 작업을 시작합니다. 작업이 완료되면 출력이 PowerShell 콘솔에 작성됩니다.

####Hive

다음 스크립트는 __streaming.py__ 스크립트를 실행합니다. 실행에 앞서 HDInsight 클러스터의 HTTPs/관리자 계정 정보를 입력하라는 메시지가 표시됩니다.

    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    # If using a Windows-based HDInsight cluster, change the USING statement to:
    # "USING 'D:\Python27\python.exe streaming.py' AS " +
	$HiveQuery = "add file wasbs:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'python streaming.py' AS " +
	               "(clientid string, phoneLabel string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"

	$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
    #   -Clustername $clusterName `
    #   -JobId $job.JobId `
    #   -DefaultContainer $container `
    #   -DefaultStorageAccountName $storageAccountName `
    #   -DefaultStorageAccountKey $storageAccountKey `
    #   -HttpCredential $creds `
    #   -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

**Hive** 작업의 출력은 다음과 유사하게 표시됩니다.

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig (Jython)

다음에서는 Jython 인터프리터를 사용하는 __pig\_python.py__을 사용합니다. 실행에 앞서 HDInsight 클러스터의 HTTPs/관리자 정보를 입력하라는 메시지가 표시됩니다.

> [AZURE.NOTE] PowerShell을 사용하는 작업을 원격으로 제출하는 경우 C Python을 인터프리터로사용할 수 없습니다.

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$PigQuery = "Register wasbs:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"

	$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

**Pig** 작업의 출력은 다음과 유사하게 표시됩니다.

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>문제 해결

###작업 실행 중 오류 발생

하이브 작업 실행 중 다음과 유사한 오류가 발생할 수 있습니다.

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
    
이 문제는 streaming.py 파일의 줄 끝 때문에 발생할 수 있습니다. 많은 Windows 편집기에서는 기본적으로 CRLF를 줄 끝으로 사용하지만 Linux 응용 프로그램에서는 보통 LF를 사용합니다.

LF 줄 끝을 만들 수 없는 편집기를 사용 중이거나 어떤 줄 끝을 사용하는지 확실하지 않은 경우 HDInsight에 파일을 업로드하기 전에 다음 PowerShell 문을 사용하여 CR 문자를 제거합니다.

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

###PowerShell 스크립트

이 예제를 실행하는 데 사용된 두 가지 예제 PowerShell 스크립트는 작업의 오류 출력을 표시하는 주석 처리된 줄을 포함합니다. 작업의 필요한 출력이 표시되지 않으면 다음 줄의 주석 처리를 제거하고 오류 정보가 문제를 나타내는지 확인합니다.

	# Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

오류 정보(STDERR) 및 작업의 결과(STDOUT)도 다음 위치의 클러스터용 기본 Blob 컨테이너에 로깅됩니다.

이 작업의 경우|Blob 컨테이너에서 이러한 파일을 찾습니다.
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>다음 단계

기본적으로 제공되지 않는 Python 모듈을 로드해야 하는 경우 수행 방법의 예제에 대해서는 [Azure HDInsight에 모듈을 배포하는 방법](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)(영문)을 참조하세요.

Pig 및 Hive를 사용하고 MapReduce 사용에 대해 배우는 다른 방법은 다음을 참조하세요.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->