<properties
pageTitle="공유 액세스 서명을 사용하여 데이터에 대한 HDInsight 액세스 제한"
description="공유 액세스 서명을 사용하여 Azure 저장소 Blob에 저장된 데이터에 대한 HDInsight 액세스를 제한하는 방법에 대해 알아봅니다."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/25/2016"
ms.author="larryfr"/>

#Azure 저장소 공유 액세스 서명을 사용하여 HDInsight에서 데이터 액세스 제한

HDInsight는 데이터 저장소로 Azure 저장소 Blob를 사용합니다. HDInsight는 클러스터에 대한 기본 저장소로 사용되는 Blob에 대한 모든 권한을 보유해야 하지만 사용자가 클러스터에 사용되는 다른 Blob에 저장된 데이터로 권한을 제한할 수 있습니다. 예를 들어 일부 데이터를 읽기 전용으로 설정할 수 있습니다. 공유 액세스 서명을 사용하여 이 작업을 수행할 수 있습니다.

공유 액세스 서명(SAS)은 데이터에 대한 액세스를 제한할 수 있는 Azure 저장소 계정의 기능입니다. 예를 들어 데이터에 대한 읽기 전용 액세스를 제공합니다. 이 문서에서는 SAS를 사용하여 HDInsight의 Blob 컨테이너에 대한 읽기 및 목록 전용 액세스를 사용하도록 설정하는 방법에 대해 배웁니다.

##요구 사항

* Azure 구독

* C# 또는 Python. C# 예제 코드가 Visual Studio 솔루션으로 제공됩니다.

    * Visual Studio는 버전 2013 또는 2015여야 합니다.
    
    * Python은 버전 2.7 이상이어야 합니다.

* Linux 기반 HDInsight 클러스터 또는 [Azure PowerShell][powershell] - 기존 Linux 기반 클러스터가 있는 경우 Ambari를 사용하여 클러스터에 공유 액세스 서명을 추가할 수 있습니다. 그렇지 않으면 Azure PowerShell을 사용하여 새 클러스터를 만들고 클러스터를 만들 때 공유 액세스 서명을 추가합니다.

* [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)의 예제 파일입니다. 이 리포지토리는 다음을 포함합니다.

    * HDInsight에 사용할 저장소 컨테이너, 저장된 정책 및 SAS를 만들 수 있는 Visual Studio 프로젝트
    
    * HDInsight에 사용할 저장소 컨테이너, 저장된 정책 및 SAS를 만들 수 있는 Python 스크립트
    
    * 새 HDInsight 클러스터를 만들고 SAS를 사용하도록 구성할 수 있는 PowerShell 스크립트

##공유 액세스 서명

두 가지 형태의 공유 액세스 서명이 있습니다.

* 애드혹: SAS의 시작 시간, 만료 시간 및 사용 권한이 SAS URI에 모두 지정됩니다. 시작 시간이 생략되는 경우에는 묵시적으로 지정됩니다.

* 저장된 액세스 정책: 저장된 액세스 정책은 리소스 컨테이너(Blob 컨테이너, 테이블, 큐 또는 파일 공유)에서 정의되며, 하나 이상의 공유 액세스 서명에 대한 제약 조건을 관리하는 데 사용할 수 있습니다. SAS를 공유 액세스 정책과 연결할 경우 SAS는 저장된 액세스 정책에 대해 정의된 제약 조건(시작 시간, 만료 시간 및 사용 권한)을 상속합니다.

두 형식의 차이점은 주요 시나리오인 해지에 중요합니다. SAS는 URL이므로 SAS를 시작하도록 요청한 사용자에 상관없이 SAS를 획득한 모든 사용자가 SAS를 사용할 수 있습니다. SAS가 공개적으로 게시된 경우 전 세계의 모든 사용자가 SAS를 사용할 수 있습니다. 분산된 SAS는 다음 네 가지 중 하나에 해당할 때까지 유효합니다.

1. SAS에 지정된 만료 시간에 도달한 경우

2. SAS에서 참조되는 저장된 액세스 정책에 지정된 만료 시간에 도달한 경우(저장된 액세스 정책을 참조하고 이 정책에 만료 시간이 지정된 경우). 시간 간격이 경과하거나 저장된 액세스 정책에서 만료 시간을 과거의 시간으로 수정한 경우에 해당하며, 이는 SAS를 해지하는 한 가지 방법입니다.

3. SAS에서 참조되는 저장된 액세스 정책을 삭제한 경우(SAS를 해지하는 다른 방법). 동일한 이름을 사용하여 저장된 액세스 정책을 다시 만들면 저장된 액세스 정책에 연결된 사용 권한에 따라 모든 기존 SAS 토큰이 다시 유효해집니다(SAS의 만료 시간이 경과하지 않은 것으로 가정). SAS를 해지하기 위해 만료 시간을 미래의 시간으로 지정하여 액세스 정책을 다시 만들 경우 다른 이름을 사용해야 합니다.

4. SAS를 만드는 데 사용된 계정 키를 다시 생성한 경우. 이렇게 하면 해당 계정 키를 사용하는 모든 응용 프로그램 구성 요소는 다른 유효한 계정 키 또는 새로 생성된 계정 키를 사용하도록 업데이트될 때까지 인증되지 않습니다.

> [AZURE.IMPORTANT] 공유 액세스 서명 URI는 서명을 만드는 데 사용된 계정 키 및 저장된 관련 액세스 정책(있는 경우)에 연결됩니다. 저장된 액세스 정책을 지정하지 않는 경우 공유 액세스 서명을 해지하는 방법은 계정 키를 변경하는 것뿐입니다.

항상 저장된 액세스 정책을 사용하여 필요에 따라 서명을 해지하거나 만료 날짜를 연장할 수 있도록 하는 것이 좋습니다. 이 문서의 단계에서는 SAS를 생성하는 데 저장된 액세스 정책을 사용합니다.

공유 액세스 서명에 대한 자세한 내용은 [SAS 모델 이해](../storage/storage-dotnet-shared-access-signature-part-1.md)를 참조하세요.

##저장된 정책을 만들고 SAS를 생성합니다.

현재는 저장된 정책을 프로그래밍 방식으로 만들어야 합니다. 저장된 정책 및 SAS를 만드는 C# 및 Python 예제는 [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)에서 확인할 수 있습니다.

###C#을 사용하여 저장된 정책 및 SAS 만들기

1. Visual Studio에서 솔루션을 엽니다.

2. 솔루션 탐색기에서 __SASToken__ 프로젝트를 마우스 오른쪽 단추로 클릭하고 __속성__을 선택합니다.

3. __설정__을 선택하고 다음 항목에 대한 값을 추가합니다.

    * StorageConnectionString: 저장된 정책 및 SAS를 만들 저장소 계정에 대한 연결 문자열입니다. 형식은 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`여야 하며 여기서 `myaccount`는 사용자의 저장소 계정 이름이고 `mykey`는 저장소 계정에 대한 키입니다.
    
    * ContainerName: 액세스를 제한할 저장소 계정의 컨테이너입니다.
    
    * SASPolicyName: 만들 저장된 정책에 대해 사용할 이름입니다.
    
    * FileToUpload: 컨테이너에 업로드할 파일에 대한 경로입니다.
    
4. 프로젝트를 실행합니다. 콘솔 창이 나타나고 SAS가 생성되면 다음과 유사한 정보가 표시됩니다.

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    저장소 계정을 HDInsight 클러스터에 연결하는 데 필요하므로 SAS 정책 토큰을 저장합니다. 저장소 계정 이름과 컨테이너 이름이 필요합니다.
    
###Python을 사용하여 저장된 정책 및 SAS 만들기

1. SASToken.py 파일을 열고 다음 값을 변경합니다.

    * policy\_name: 만들 저장된 정책에 대해 사용할 이름입니다.
    
    * storage\_account\_name: 저장소 계정의 이름입니다.
    
    * storage\_account\_key: 저장소 계정의 키입니다.
    
    * storage\_container\_name: 액세스를 제한할 저장소 계정의 컨테이너입니다.
    
    * example\_file\_path: 컨테이너에 업로드할 파일에 대한 경로입니다.

2. 스크립트를 실행합니다. 스크립트가 완료되면 다음과 유사한 SAS 토큰이 표시됩니다.

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    저장소 계정을 HDInsight 클러스터에 연결하는 데 필요하므로 SAS 정책 토큰을 저장합니다. 저장소 계정 이름과 컨테이너 이름이 필요합니다.
    
##HDInsight에 SAS 사용

HDInsight 클러스터를 만들 때 기본 저장소 계정을 지정해야 하며 필요에 따라 추가 저장소 계정을 지정할 수 있습니다. 저장소를 추가하는 이 두 가지 방법 모두에 사용된 저장소 계정 및 컨테이너에 대한 모든 권한이 필요합니다.

공유 액세스 서명을 사용하여 컨테이너에 대한 액세스를 제한하려면, 클러스터에 대한 __core-site__ 구성에 사용자 지정 항목을 추가해야 합니다.

* __Windows 기반__ 또는 __Linux 기반__ HDInsight 클러스터의 경우 PowerShell을 사용하여 클러스터를 만드는 동안 이 작업을 수행할 수 있습니다.

* __Linux 기반__ HDInsight 클러스터의 경우 Ambari를 사용하여 클러스터를 만든 후 구성을 변경합니다.

###SAS를 사용하는 새 클러스터 만들기

SAS를 사용하는 HDInsight 클러스터를 만드는 예제는 리포지토리의 `CreateCluster` 디렉터리에 있습니다. 이를 사용하려면 다음 단계를 수행합니다.

1. 텍스트 편집기에서 `CreateCluster\HDInsightSAS.ps1` 파일을 열고 문서 맨 앞에 있는 다음 값을 수정합니다.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    예를 들어 `'mycluster'`를 만들려는 클러스터의 이름으로 변경합니다. SAS 값은 저장소 계정 및 SAS 토큰을 만들 때 이전 단계에서 사용한 값과 일치해야 합니다.
    
    값을 변경한 후 파일을 저장합니다.

1. 새 Azure PowerShell 프롬프트를 엽니다. Azure PowerShell에 익숙하지 않거나 설치되지 않은 경우 [Azure PowerShell 설치 및 구성][powershell]을 참조하세요.

2. 프롬프트에서 다음 명령을 사용하여 Azure 구독에 대해 인증합니다.

        Login-AzureRmAccount
    
    메시지가 표시되면 Azure 구독 계정으로 로그인합니다.
    
    로그인이 여러 Azure 구독과 연결되는 경우 `Select-AzureRmSubscription`을 사용하여 사용할 구독을 선택해야 합니다.

2. 프롬프트에서 디렉터리를 HDInsightSAS.ps1 파일이 있는 `CreateCluster` 디렉터리로 변경합니다. 그런 후 다음을 사용하여 스크립트를 실행합니다.
        
        .\HDInsightSAS.ps1
    
    스크립트를 실행하면 리소스 그룹 및 저장소 계정이 생성되면서 출력이 PowerShell 프롬프트에 기록됩니다. 그런 다음 HDInsight 클러스터에 대한 HTTP 사용자를 입력하라는 메시지가 표시됩니다. 클러스터에 대한 보안 HTTP/s 액세스를 보호하는 데 사용되는 사용자 계정입니다.
    
    Linux 기반 클러스터를 만드는 경우 SSH 사용자 계정 이름 및 암호를 묻는 메시지도 표시됩니다. 클러스터에 원격으로 로그인하는 데 사용됩니다.
    
    > [AZURE.IMPORTANT] HTTP/s 또는 SSH 사용자 이름 및 암호를 묻는 메시지가 나타나면 다음 조건을 충족하는 암호를 제공해야 합니다.
    >
    > - 길이가 10자 이상이어야 함
    > - 숫자를 1개 이상 포함해야 함
    > - 영숫자가 아닌 문자를 1개 이상 포함해야 함
    > - 대문자 또는 소문자를 1개 이상 포함해야 함


이 스크립트를 완료하는 데는 일반적으로 약 15분이 소요됩니다. 스크립트가 오류 없이 완료되면 클러스터가 만들어진 것입니다.

###SAS를 사용하도록 기존 클러스터 업데이트

기존 Linux 기반 클러스터가 있는 경우 다음 단계에 따라 __core-site__ 구성에 SAS를 추가할 수 있습니다.

1. 클러스터에 대한 Ambari 웹 UI를 엽니다. 이 페이지에 대 한 주소는 https://YOURCLUSTERNAME.azurehdinsight.net입니다. 메시지가 표시되면 클러스터를 만들 때 사용한 관리자 이름(admin)과 암호를 사용하여 클러스터를 인증합니다.

2. Ambari 웹 UI의 왼쪽에서 __HDFS__를 선택한 다음 페이지 중간에서 __구성__ 탭을 선택합니다.

3. __고급__ 탭을 선택한 다음 __사용자 지정 core-site__ 섹션이 나올 때까지 스크롤합니다.

4. __사용자 지정 core-site__ 섹션을 확장한 후 끝까지 스크롤하여 __속성 추가...__ 링크를 선택합니다. __키__ 및 __값__ 필드에 다음 값을 사용합니다.

    * __키__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __값__: 이전에 실행한 C# 또는 Python 응용 프로그램에서 반환된 SAS
    
    __CONTAINERNAME__을 C# 또는 SAS 응용 프로그램에서 사용한 컨테이너 이름으로 바꿉니다. __STORAGEACCOUNTNAME__을 사용한 저장소 계정 이름으로 바꿉니다.

5. __추가__ 단추를 클릭하여 이 키 및 값을 저장한 후 __저장__ 단추를 클릭하여 구성 변경을 저장합니다. 메시지가 나타나면 변경에 대한 설명(예: "SAS 저장소 액세스 추가")을 추가하고 __저장__을 클릭합니다.

    변경이 완료되었으면 __확인__을 클릭합니다.

    > [AZURE.IMPORTANT] 이렇게 하면 구성 변경 내용이 저장되지만 변경 내용을 적용하기 위해서는 여러 서비스를 다시 시작해야 합니다.

6. Ambari 웹 UI의 왼쪽 목록에서 __HDFS__를 선택한 다음 오른쪽의 __서비스 작업__ 드롭다운 목록에서 __모두 다시 시작__을 선택합니다. 메시지가 나타나면 __유지 관리 모드 설정__을 선택한 다음 "모두 다시 시작 확인"을 선택합니다.

    페이지의 왼쪽에 있는 목록에서 MapReduce2 및 YARN 항목에 대해 이 과정을 반복합니다.

7. 다시 시작한 후 각각 선택하고 __서비스 작업__ 드롭다운에서 유지 관리 모드를 비활성화합니다.

##제한된 액세스 테스트

액세스를 제한을 확인하려면 다음 방법을 사용합니다.

* __Windows 기반__ HDInsight 클러스터의 경우 원격 데스크톱을 사용하여 클러스터에 연결합니다. 자세한 내용은 [RDP를 사용하여 HDInsight에 연결](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)을 참조하세요.

    연결된 후에는 바탕 화면의 __Hadoop 명령줄__ 아이콘을 사용하여 명령 프롬프트를 엽니다.

* __Linux 기반__ HDInsight 클러스터의 경우 SSH를 사용하여 클러스터에 연결합니다. Linux 기반 클러스터에서 SSH 사용에 대한 자세한 내용은 다음 중 하나를 참조하세요.

    * [Linux, OS X 및 Unix의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)
    
클러스터에 연결되면 다음 단계에 따라 SAS 저장소 계정의 항목에 대한 읽기 및 목록 전용 권한이 있는지 확인합니다.

1. 프롬프트에 다음을 입력합니다. __SASCONTAINER__를 SAS 저장소 계정에 대해 만든 컨테이너의 이름으로 바꿉니다. __SASACCOUNTNAME__을 SAS에 사용된 저장소 계정의 이름으로 바꿉니다.

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    그러면 컨테이너의 내용이 나열되며 여기에는 컨테이너 및 SAS를 만들 때 업로드한 파일도 포함됩니다.
    
2. 다음을 사용하여 파일의 내용을 읽을 수 있는지 확인합니다. 이전 단계처럼 __SASCONTAINER__ 및 __SASACCOUNTNAME__을 바꿉니다. __FILENAME__을 이전 명령에 표시된 파일 이름으로 바꿉니다.

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    그러면 파일의 내용이 나열됩니다.
    
3. 파일을 로컬 파일 시스템에 다운로드하려면 다음을 사용합니다.

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    그러면 파일이 __testfile.txt__라는 로컬 파일에 다운로드됩니다.

4. 다음을 사용하여 로컬 파일을 SAS 저장소의 새 __testupload.txt__ 파일에 업로드합니다.

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    그러면 다음과 유사한 메시지가 표시됩니다.
    
        put: java.io.IOException
        
    저장소 위치가 읽기 + 목록 전용이므로 이 오류가 발생합니다. 다음을 사용하여 쓰기 가능한 클러스터의 기본 저장소에 데이터를 저장합니다.
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    이때 작업이 성공적으로 완료되어야 합니다.
    
##문제 해결

###작업이 취소됨

__증상__: PowerShell 스크립트를 사용하여 클러스터를 만들 때 다음과 같은 오류 메시지가 나타날 수 있습니다.

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__원인__: 클러스터의 admin/HTTP 사용자 또는 Linux 기반 클러스터인 경우 SSH 사용자에 대해 암호를 사용하는 경우 이 오류가 발생할 수 있습니다.

__해결 방법__: 다음 조건을 충족하는 암호를 사용합니다.

- 길이가 10자 이상이어야 함
- 숫자를 1개 이상 포함해야 함
- 영숫자가 아닌 문자를 1개 이상 포함해야 함
- 대문자 또는 소문자를 1개 이상 포함해야 함

##다음 단계

이제 HDInsight 클러스터에 액세스가 제한된 저장소를 추가하는 방법을 배웠으므로 클러스터에서 데이터에 대해 작업하는 다른 방법에 알아보겠습니다.

* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)

* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0914_2016-->