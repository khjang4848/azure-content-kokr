<properties
   pageTitle="HDInsight에서 R 서버 시작(미리 보기) | Azure"
   description="R 서버(미리 보기)를 포함하는 HDInsight(Hadoop) 클러스터에서 Apache Spark를 만든 다음 클러스터에서 R 스크립트를 제출하는 방법을 알아봅니다."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="08/19/2016"
   ms.author="jeffstok"
/>

# HDInsight에서 R 서버 사용 시작(미리 보기)

HDInsight용 Premium 계층 제품에는 HDInsight(미리 보기) 클러스터의 일부로 R 서버가 포함됩니다. 이를 통해 R 스크립트에서 MapReduce 및 Spark를 사용하여 분산된 계산을 실행할 수 있습니다. 이 문서에서는 HDInsight에서 새 R 서버를 만든 다음 R 스크립트를 실행하여 분산된 R 계산에 Spark를 사용하는 방법을 알아봅니다.

![이 문서에 대한 워크플로의 다이어그램](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## 필수 조건

* __Azure 구독__: 이 자습서를 시작하기 전에 Azure 구독이 있어야 합니다. 자세한 내용은 [Azure 무료 평가판 얻기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

* __SSH(보안 셸) 클라이언트__: SSH 클라이언트는 HDInsight 클러스터에 원격으로 연결하여 클러스터에서 직접 명령을 실행하는 데 사용됩니다. Linux, Unix 및 OS X 시스템에서 `ssh` 명령을 통해 SSH 클라이언트를 제공합니다. Windows 시스템의 경우 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 사용하는 것이 좋습니다.

    * __SSH 키(선택 사항)__: 암호 또는 공개 키를 사용하여 클러스터에 연결할 때 사용되는 SSH 계정을 보호할 수 있습니다. 암호를 사용하면 작업이 보다 간편하며, 공개/개인 키 쌍을 만들지 않고도 시작할 수 있지만 키를 사용하는 것이 더 안전합니다.
    
        이 문서의 단계에서는 암호를 사용하는 것으로 가정합니다. SSH 키를 만들어 HDInsight에서 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
        
        * [Linux, Unix 또는 OS X 클라이언트에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Windows 클라이언트에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

## 클러스터 만들기

> [AZURE.NOTE] 이 문서의 단계에서는 기본 구성 정보를 사용하여 HDInsight에서 R 서버를 만듭니다. 기타 클러스터 구성 설정(예: 저장소 계정 추가, Azure 가상 네트워크 사용 또는 Hive용 Metastore 만들기)에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. __새로 만들기__, __데이터 + 분석__, __HDInsight__를 차례로 선택합니다.

    ![새 클러스터를 만드는 과정의 이미지](./media/hdinsight-getting-started-with-r/newcluster.png)

3. __클러스터 이름__ 필드에 클러스터의 이름을 입력합니다. Azure 구독이 여러 개 있는 경우 __구독__ 항목을 사용하여 사용할 구독을 선택합니다.

    ![클러스터 이름 및 구독 선택](./media/hdinsight-getting-started-with-r/clustername.png)

4. __클러스터 유형 선택__을 선택합니다. __클러스터 유형__ 블레이드에서 다음 옵션을 선택합니다.

    * __클러스터 유형__: Spark의 R 서버
    
    * __클러스터 계층__: Premium

    다른 옵션은 기본값을 그대로 두고 __선택__ 단추를 사용하여 클러스터 유형을 저장합니다.
    
    ![클러스터 유형 블레이드 스크린샷](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] 클러스터 유형을 선택한 다음 __Premium__을 선택하여 다른 HDInsight 클러스터 유형(예: Hadoop 또는 HBase)에 R 서버를 추가할 수도 있습니다.

5. **리소스 그룹**을 선택하여 기존 리소스 그룹 목록을 표시한 다음 클러스터를 만들 리소스 그룹을 선택합니다. 또는 **새로 만들기**를 선택한 다음 새 리소스 그룹의 이름을 입력할 수 있습니다. 새 그룹 이름을 사용할 수 있는지 여부를 나타내는 녹색 확인 표시가 나타납니다.

    > [AZURE.NOTE] 사용할 수 있는 경우 이 항목은 기존 리소스 그룹 중 하나로 기본 설정됩니다.
    
    __선택__ 단추를 사용하여 리소스 그룹을 저장합니다.

6. **자격 증명**을 선택한 다음 **클러스터 로그인 사용자 이름** 및 **클러스터 로그인 암호**를 입력합니다.

    __SSH 사용자 이름__을 입력합니다. __SSH(Secure Shell)__ 클라이언트를 사용하여 클러스터에 원격으로 연결하는 데 SSH를 사용합니다. 클러스터(클러스터에 대한 구성 탭)를 만든 후에 이 대화 상자에서 SSH 사용자를 지정할 수 있습니다. R Server는 "remoteuser"라는 __SSH 사용자 이름__를 사용하도록 구성됩니다. 다른 사용자 이름을 사용하면 클러스터를 만든 후에 추가 단계를 수행해야 합니다.
    
    ![자격 증명 블레이드](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    __SSH 인증 유형__: 공개 키를 사용하려는 경우가 아니면 __암호__를 인증 입력으로 선택합니다. RTVS, RStudio 또는 다른 데스크톱 IDE와 같은 원격 클라이언트를 통해 클러스터에서 R Server에 액세스하려는 경우 공개/개인 키 쌍이 필요합니다.

	공개/개인 키 쌍을 만들고 사용하려면 '공개 키'를 선택하고 다음을 수행합니다. 이러한 지침에서는 ssh-keygen을 포함한 Cygwin 또는 이와 동등한 프로그램이 설치되어 있다고 가정합니다.

	-    랩톱 컴퓨터의 명령 프롬프트에서 공개/개인 키 쌍을 생성합니다.
	  
		    ssh-keygen -t rsa -b 2048 –f <private-key-filename>
      
    -    이렇게 하면 <private-key-filename>.pub이라는 이름으로 개인 키 파일 및 공개 키 파일이 만들어집니다(예: davec 및 davec.pub). HDI 클러스터 자격 증명을 할당할 때 공개 키 파일(*.pub)을 지정합니다.
      
		![자격 증명 블레이드](./media/hdinsight-getting-started-with-r/publickeyfile.png)
      
	-    랩톱 컴퓨터의 개인 키 파일에 대한 사용 권한 변경
      
			chmod 600 <private-key-filename>
      
	-    원격 로그인을 위해 SSH와 함께 개인 키 파일을 사용합니다.
	  
			ssh –i <private-key-filename> remoteuser@<hostname public ip>
      
	  클라이언트에서 R Server에 대한 Hadoop Spark 계산 컨텍스트의 정의의 일부로 사용합니다(온라인 [RevoScaleR Hadoop Spark 시작 가이드](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)의 [Spark용 계산 컨텍스트 만들기](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) 섹션에서 Hadoop 클라이언트인 Microsoft R Server 만들기를 참조).

7. **데이터 원본**을 선택하여 클러스터의 데이터 원본을 선택합니다. __저장소 계정 선택__을 선택한 다음 계정을 선택하여 기존 저장소 계정을 선택하거나, __저장소 계정 선택__ 섹션에서 __새로 만들기__ 링크를 사용하여 새 계정을 만듭니다.

    __새로 만들기__를 선택한 경우 새 저장소 계정의 이름을 입력해야 합니다. 이름이 허용되는 경우 녹색 확인 표시가 나타납니다.

    __기본 컨테이너__는 기본적으로 클러스터의 이름으로 설정됩니다. 이 값을 그대로 둡니다.
    
    __위치__를 선택하여 저장소 계정을 만들 지역을 선택합니다.
    
    > [AZURE.IMPORTANT] 기본 데이터 원본의 위치를 선택하면 HDInsight 클러스터의 위치도 설정됩니다. 클러스터와 기본 데이터 원본은 같은 지역에 있어야 합니다.

    **선택** 단추를 사용하여 데이터 원본 구성을 저장합니다.
    
    ![데이터 원본 블레이드](./media/hdinsight-getting-started-with-r/datastore.png)

8. **노드 가격 책정 계층**을 선택하여 이 클러스터에 대해 만들어질 노드에 대한 정보를 표시합니다. 더 큰 클러스터가 필요한 경우가 아니면 작업자 노드 수를 기본값(`4`)으로 그대로 둡니다. 클러스터의 예상 비용이 블레이드 내에 표시됩니다.

	> [AZURE.NOTE] 필요한 경우 나중에 포털(클러스터-> 설정 -> 클러스터 크기 조정)을 통해 다시 클러스터의 크기를 조정하여 작업자 노드 수를 늘리거나 줄일 수 있습니다. 클러스터를 사용하지 않을 때 유휴 상태로 유지하거나 대규모 태스크의 요구에 맞게 용량을 추가하는 데 유용할 수 있습니다.

	클러스터, 데이터 노드 및 에지 노드의 크기를 조정할 때 염두할 몇 가지 요인은 다음과 같습니다.
   
    - Spark에서 분산된 R 서버 분석의 성능은 데이터가 클 경우 작업자 노드 수에 비례합니다.
    - R 서버 분석의 성능은 분석 중인 데이터의 크기에 비례합니다. 예:
        - 작은 데이터부터 보통 크기의 데이터의 경우 성능은 에지 노드의 로컬 계산 컨텍스트에서 분석할 때 가장 뛰어납니다. 로컬 및 Spark 계산 컨텍스트가 가장 잘 작동하는 시나리오에 대한 자세한 내용은 HDInsight에서 R Server에 대한 계산 컨텍스트 옵션을 참조하세요.<br>
        - 에지 노드에 로그인하고 거기서 R 스크립트를 실행하는 경우 메모리와 에지 노드의 코어 수를 적절하게 조정하도록 에지 노드에서 ScaleR rx 함수를 제외한 모든 항목을 <strong>로컬로</strong> 실행합니다. HDI의 R Server를 랩톱 컴퓨터의 원격 계산 컨텍스트로 사용할 경우에도 동일하게 적용됩니다.
    
    ![노드 가격 책정 계층 블레이드](./media/hdinsight-getting-started-with-r/pricingtier.png)

    **선택** 단추를 사용하여 노드 가격 책정 구성을 저장합니다.
    
9. **새 HDInsight 클러스터** 블레이드에서 **시작 보드에 고정**이 선택되어 있는지 확인한 다음 **만들기**를 선택합니다. 그러면 클러스터가 만들어지고 Azure 포털의 시작 보드에 클러스터 타일이 추가됩니다. 아이콘은 클러스터를 만드는 중임을 나타내고 생성이 완료되면 변경되어 HDInsight 아이콘을 표시합니다.

    | 만드는 동안 | 만들기 완료 |
    | ------------------ | --------------------- |
    | ![시작 보드에 표시기 만들기](./media/hdinsight-getting-started-with-r/provisioning.png) | ![만든 클러스터 타일](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] 클러스터를 만드는데 약간의 시간이 걸리며, 일반적으로 약 15분이 소요됩니다. 시작 보드에 있는 타일 또는 페이지 왼쪽에 있는 **알림** 항목을 사용하여 생성 프로세스를 확인합니다.

## R 서버 가장자리 노드에 연결

SSH를 사용하여 HDInsight 클러스터의 R 서버 가장자리 노드에 연결합니다.

    ssh USERNAME@r-server.CLUSTERNAME-ssh.azurehdinsight.net
    
> [AZURE.NOTE] 클러스터를 선택한 다음 __모든 설정__, __앱__, __R Server__를 차례로 선택하여 Azure 포털에서 `R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 주소를 찾을 수도 있습니다. 그러면 가장자리 노드에 대한 SSH 끝점 정보가 표시됩니다.
>
> ![가장자리 노드에 대한 SSH 끝점의 이미지](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
SSH 사용자 계정을 보호하도록 암호를 사용한 경우 암호를 묻는 메시지가 나타납니다. 공용 키를 사용하는 경우, `-i` 매개 변수를 사용하고 일치하는 개인 키를 지정합니다. 예: `ssh -i ~/.ssh/id_rsa USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net`
    
Linux 기반 HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

연결되면 다음과 유사한 프롬프트가 나타납니다.

    username@ed00-myrser:~$

## R 콘솔 사용

1. SSH 세션에서 다음 명령을 사용하여 R 콘솔을 시작합니다.

        R
    
    다음과 유사한 출력이 표시됩니다.
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. `>` 프롬프트에서 R 코드를 입력할 수 있습니다. R 서버에는 Hadoop과 쉽게 상호 작용하고 분산된 계산을 실행할 수 있는 패키지가 포함되어 있습니다. 예를 들어, 다음 명령을 사용하여 HDInsight 클러스터에 대한 기본 파일 시스템의 루트를 볼 수입니다.

        rxHadoopListFiles("/")
    
    WASB 스타일 주소 지정을 사용할 수도 있습니다.
    
        rxHadoopListFiles("wasbs:///")

## Microsoft R Server 또는 Microsoft R 클라이언트의 원격 인스턴스에서 HDI의 R Server 사용

클러스터에 액세스하기 위한 공개/개인 키 쌍 사용과 관련하여 위의 섹션에서 데스크톱 또는 랩톱 컴퓨터 상에 실행되는 Microsoft R Server 또는 Microsoft R 클라이언트의 원격 인스턴스에서 HDI Hadoop Spark 계산 컨텍스트에 대한 액세스를 설정할 수 있습니다(온라인 [RevoScaleR Hadoop Spark 시작 가이드](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)의 [Spark용 계산 컨텍스트 만들기](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) 섹션에서 Hadoop 클라이언트인 Microsoft R Server 만들기를 참조). 이렇게 하려면 랩톱 컴퓨터에서 RxSpark 계산 컨텍스트를 정의할 때 hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches, and sshProfileScript와 같은 옵션을 지정해야 합니다. 예:

    
    myNameNode <- "default"
    myPort <- 0 
 
    mySshHostname  <- 'rkrrehdi1-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key
 
    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")
 
    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

    
 
## 계산 컨텍스트 사용

계산 컨텍스트를 사용하여 계산을 가장자리 노드에서 로컬로 수행할지 여부 또는 HDInsight 클러스터의 노드 간에 분산할지 여부를 제어할 수 있습니다.
        
1. R 콘솔에서 다음을 사용하여 HDInsight의 기본 저장소에 예제 데이터를 로드합니다.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. 그런 다음 데이터로 작업할 수 있도록 몇 가지 데이터 정보를 만들고 두 개의 데이터 원본을 정의해 보겠습니다.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. 로컬 계산 컨텍스트를 사용하여 데이터에 대해 로지스틱 회귀를 실행해 보겠습니다.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    다음과 유사한 줄로 끝나는 출력이 나타나야 합니다.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7

4. 이번에는 Spark 컨텍스트를 사용하여 동일한 로지스틱 회귀를 실행해 보겠습니다. Spark 컨텍스트는 HDInsight 클러스터의 모든 작업자 노드에서 처리를 분산시킵니다.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)

    > [AZURE.NOTE] MapReduce를 사용하여 클러스터 노드 간에 계산을 분산시킬 수도 있습니다. 계산 컨텍스트에 대한 자세한 내용은 [HDInsight Premium의 R Server에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)을 참조하세요.

## 여러 노드에 R 코드 분산

R Server를 사용하면 손쉽게 기존 R 코드를 가져와 `rxExec`를 통해 클러스터의 여러 노드에서 실행할 수 있습니다. 이는 매개 변수 스윕 또는 시뮬레이션을 수행할 때 유용합니다. 다음은 `rxExec`를 사용하는 방법에 대한 예입니다.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
여전히 Spark 또는 MapReduce 컨텍스트를 사용하는 경우 코드(`Sys.info()["nodename"]`)가 실행된 작업자 노드의 nodename 값이 반환됩니다. 예를 들어, 4노드 클러스터의 경우 다음과 유사한 출력이 나타날 수 있습니다.

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

## R 패키지 설치

SSH를 통해 에지 노드에 연결한 경우 에지 노드에 추가 R 패키지를 설치하려면 R 콘솔 내에서 직접 `install.packages()`를 사용할 수 있습니다. 그러나 클러스터의 작업자 노드에 R 패키지를 설치해야 하는 경우에는 스크립트 작업을 사용해야 합니다.

스크립트 작업은 HDInsight 클러스터에 대한 구성을 변경하거나 추가 소프트웨어를 설치하는 데 사용되는 Bash 스크립트입니다. 여기서는 추가 R 패키지를 설치하는 데 사용됩니다. 스크립트 작업을 사용하여 추가 패키지를 설치하려면 다음 단계를 사용하세요.

> [AZURE.IMPORTANT] 추가 R 패키지를 설치하는 데 스크립트 작업을 사용하려는 경우 클러스터가 만들어진 후에만 사용할 수 있습니다. 클러스터를 만드는 동안에 사용해서는 안 됩니다. 스크립트는 완전히 설치되고 구성된 R 서버에 의존하기 때문입니다.

1. [Azure 포털](https://portal.azure.com)에서 HDInsight 클러스터의 R Server를 선택합니다.

2. 클러스터 블레이드에서 __모든 설정__, __스크립트 작업__을 차례로 선택합니다. __스크립트 작업__ 블레이드에서 __새로운 항목 제출__을 선택하여 새 스크립트 작업을 제출합니다.

    ![스크립트 작업 블레이드의 이미지](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. __스크립트 작업 제출__ 블레이드에서 다음 정보를 입력합니다.

  - __이름__: 이 스크립트를 식별하는 친숙한 이름
  - __Bash 스크립트 URI__: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`
  - __헤드__: __선택 취소__
  - __작업자__: __선택__
  - __Zookeeper__: __선택 취소__
  - __매개 변수__: 설치할 R 패키지 예: `bitops stringr arules`
  - __이 스크립트 유지...__: __선택__해야 합니다.

    > [AZURE.NOTE] 1. 기본적으로 모든 R 패키지는 설치된 R Server의 버전과 일치하는 Microsoft MRAN 리포지토리의 스냅숏에서 설치됩니다. 그러나 최신 버전의 패키지를 설치하면 호환되지 않을 위험이 있는 경우 `useCRAN`을 패키지 목록의 첫 번째 요소(예: `useCRAN bitops, stringr, arules`)로 지정하여 수행할 수 있습니다.
    > 2. 일부 R 패키지에는 추가 Linux 시스템 라이브러리가 필요합니다. 편의상 가장 인기 있는 상위 100개 R 패키지에서 필요한 종속성을 미리 설치했습니다. 그러나 설치한 R 패키지에 더 많은 라이브러리가 필요한 경우 여기에서 사용된 기본 스크립트를 다운로드하고 시스템 라이브러리를 설치할 단계를 추가해야 합니다. 그런 다음 수정된 스크립트를 Azure 저장소의 공용 blob 컨테이너에 업로드하고 수정된 스크립트를 사용하여 패키지를 설치해야 합니다. 스크립트 작업 개발에 대한 자세한 내용은 [스크립트 작업 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

    ![스크립트 작업 추가](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. __만들기__를 선택하여 스크립트를 실행합니다. 스크립트가 완료되면 모든 작업자 노드에서 R 패키지를 사용할 수 있습니다.
    
## 다음 단계

이제 R 서버를 포함하는 새 HDInsight 클러스터를 만드는 방법 및 SSH 세션에서 R 콘솔 사용 기본 사항을 이해했으므로 다음을 사용하여 HDInsight에서 R 서버로 작업하는 다른 방법을 확인할 수 있습니다.

- [HDInsight Premium에 RStudio 서버 추가](hdinsight-hadoop-r-server-install-r-studio.md)

- [HDInsight Premium의 R 서버에 대한 계산 컨텍스트 옵션](hdinsight-hadoop-r-server-compute-contexts.md)

- [HDInsight Premium의 R 서버에 대한 Azure 저장소 옵션](hdinsight-hadoop-r-server-storage.md)

### Azure 리소스 관리자 템플릿

Azure Resource Manager 템플릿을 사용하여 HDInsight에서 R 서버를 자동으로 만드는 데 관심이 있는 경우 다음 예제 템플릿을 참조하세요.

* [SSH 공개 키를 사용하여 HDInsight 클러스터에서 R 서버 만들기](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [SSH 암호를 사용하여 HDInsight 클러스터에서 R 서버 만들기](http://go.microsoft.com/fwlink/p/?LinkID=780810)

두 템플릿 모두 새 HDInsight 클러스터 및 연결된 저장소 계정을 만들며, Azure CLI, Azure PowerShell 또는 Azure 포털에서 사용할 수 있습니다.

Azure Resource Manager 템플릿을 사용하는 일반적인 정보는 [Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->