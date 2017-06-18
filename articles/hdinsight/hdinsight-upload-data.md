<properties
	pageTitle="HDInsight에서 Hadoop 작업용 데이터 업로드 | Microsoft Azure"
	description="Azure CLI, Azure 저장소 탐색기, Azure PowerShell, Hadoop 명령줄 또는 Sqoop을 사용하여 HDInsight에서 Hadoop 작업 데이터를 업로드 및 액세스하는 방법에 대해 알아봅니다."
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>



#HDInsight에서 Hadoop 작업용 데이터 업로드

Azure HDInsight는 Azure Blob 저장소를 통해 모든 기능을 갖춘 HDFS(Hadoop Distributed File System)를 제공합니다. 고객에게 원활한 환경을 제공하기 위해 HDFS를 확장하여 설계되었습니다. 이를 통해 Hadoop 에코 시스템에서 구성 요소의 전체 집합이 관리하는 데이터에서 직접 작동하도록 합니다. Azure Blob 저장소와 HDFS는 데이터의 저장소 및 해당 데이터의 계산을 최적화하는 별개의 파일 시스템입니다. Azure Blob 저장소 사용의 이점에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

**필수 구성 요소**

시작하기 전에 다음 요구 사항을 확인하세요:

* Azure HDInsight 클러스터를 만듭니다. 자세한 내용은 [Azure HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

##Blob 저장소

Azure HDInsight 클러스터는 일반적으로 MapReduce 작업 실행을 위해 배포되며 이러한 작업이 완료되면 삭제됩니다. 계산이 완료된 후 HDFS 클러스터에 데이터를 유지하면 데이터 저장에 비용이 많이 들 수 있습니다. Azure Blob 저장소는 HDInsight를 사용하여 처리할 데이터를 위한 옵션으로서 확장성, 가용성, 용량이 뛰어나고 경제적이며 공유할 수 있습니다. Blob에 데이터를 저장하면 계산에 사용할 HDInsight 클러스터를 데이터 손실 없이 안전하게 릴리스할 수 있습니다.

###디렉터리

Azure Blob 저장소 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 "/" 문자를 사용할 수 있습니다. HDInsight는 실제 디렉토리처럼 볼 수 있습니다.

예를 들어 Blob의 키 이름을 *input/log1.txt*로 지정할 수 있습니다. 실제로 "input" 디렉터리는 없지만 키 이름에 "/" 문자가 있으므로 파일 경로처럼 보입니다.

이 때문에, Azure 탐색기 도구를 사용하면 몇몇 0바이트 파일을 발견할 수 있습니다. 이러한 파일의 목적은 두 가지입니다.

- 빈 폴더가 있으면 폴더의 존재를 표시합니다. Azure Blob 저장소는 지능적이어서 foo/bar라는 Blob이 있으면 **foo**라는 폴더도 있다는 사실을 압니다. 그러나 **foo**라는 빈 폴더를 보여주려는 경우 유일한 방법은 이 특별한 0바이트 파일을 만들어 두는 것입니다.

- Hadoop 파일 시스템에 필요한 몇몇 특수 메타데이터, 특히 폴더에 대한 사용 권한 및 소유자를 보유합니다.

##명령줄 유틸리티

Microsoft는 Azure Blob 저장소에서 작업할 다음 유틸리티를 제공합니다.

| 도구 | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Azure 명령줄 인터페이스][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Hadoop 명령](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] 모든 Azure CLI, Azure PowerShell 및 AzCopy는 외부 Azure에서 사용될 수 있지만, Hadoop 명령만 HDInsight 클러스터에서 사용할 수 있으며 데이터를 로컬 파일 시스템에서 Azure Blob 저장소로 로드할 수 있습니다.

###<a id="xplatcli"></a>Azure CLI

Azure CLI는 Azure 서비스를 관리할 수 있도록 하는 크로스 플랫폼 도구입니다. Azure Blob 저장소에 데이터를 업로드하려면 다음 단계를 사용합니다:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Mac, Linux 및 Windows용 Azure CLI의 설치 및 구성](../xplat-cli-install.md)

2. 명령 프롬프트, bash 또는 다른 셸을 열고 다음을 사용하여 Azure 구독을 인증합니다.

		azure login

	메시지가 표시되면 구독하려는 사용자 이름 및 암호를 입력합니다.

3. 다음 명령을 사용하여 구독하려는 저장소 계정을 나열합니다.

		azure storage account list

4. 작업하려는 blob이 포함된 저장소 계정을 선택한 후에 다음 명령을 사용하여 이 계정에 대한 키를 검색합니다.

		azure storage account keys list <storage-account-name>

	**기본** 및 **보조** 키가 반환되어야 합니다. 다음 단계에서 사용되므로 **기본** 키 값을 복사합니다.

5. 저장소 계정 내에서 blob 컨테이너의 목록을 검색하려면 다음 명령을 사용합니다:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. 파일을 blob에 업로드 및 다운로드하려면 다음 명령을 사용합니다:

	* 파일을 업로드하려면:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* 파일을 다운로드하려면:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 항상 동일한 저장소 계정으로 작업하는 경우 모든 명령에 대한 키를 지정하는 대신 다음의 환경 변수를 설정할 수 있습니다:
>
> * **AZURE\_STORAGE\_ACCOUNT**: 저장소 계정 이름
>
> * **AZURE\_STORAGE\_ACCESS\_KEY**: 저장소 계정 키

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 스크립팅 환경입니다. Azure PowerShell을 실행하기 위해 워크스테이션을 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Azure Blob 저장소에 로컬 파일을 업로드하려면**

1. [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)의 지침에 따라 Azure PowerShell 콘솔을 엽니다.
2. 다음 스크립트에서 처음 5개의 변수 값을 설정합니다.

		$resourceGroupName = "<AzureResourceGroupName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		# Get the storage account key
		$storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Azure PowerShell 콘솔에 스크립트를 붙여넣고 실행하여 파일을 복사합니다.

예를 들어, HDInsight와 함께 작동하도록 만들진 PowerShell 스크립트는 [HDInsight 도구](https://github.com/blackmist/hdinsight-tools)를 참조하세요.

###<a id="azcopy"></a>AzCopy

AzCopy는 데이터를 Azure 저장소 계정으로 보내고 받는 작업을 간소화하도록 설계된 명령줄 도구입니다. 이 유틸리티는 독립 실행형 도구로 사용할 수도 있고 기존 응용 프로그램에 통합할 수도 있습니다. [AzCopy를 다운로드][azure-azcopy-download]하세요.

AzCopy 구문은 다음과 같습니다.

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

자세한 내용은 [AzCopy - Azure Blob용 파일 업로드/다운로드][azure-azcopy]를 참조하세요.


###<a id="commandline"></a>Hadoop 명령줄

데이터가 클러스터 헤드 노드에 존재하는 경우 Hadoop 명령줄은 blob 저장소에 데이터를 저장하는데만 유용합니다.

Hadoop 명령을 사용하려면 먼저 다음 방법 중 하나를 사용하여 헤드 노드에 연결해야 합니다.

* **Windows 기반 HDInsight**: [원격 데스크톱을 사용하여 연결](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **Linux 기반 HDInsight**: SSH를 사용하여 연결([SSH 명령](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) 또는 [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

연결된 후에 저장소에 파일을 업로드하려면 다음 구문을 사용할 수 있습니다.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

위치(예:`hadoop fs -copyFromLocal data.txt /example/data/data.txt`

HDInsight의 기본 파일 시스템은 Azure Blob 저장소에 있으므로 /example/datadavinci.txt는 실제로 Azure Blob 저장소에 있습니다. 파일이 다음과 같을 수도 있습니다.

	wasbs:///example/data/data.txt

또는

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

파일과 함께 작동하는 다른 Hadoop 명령의 목록은 [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)을 참조하세요.

##그래픽 클라이언트

Azure 저장소를 사용하기 위한 그래픽 인터페이스를 제공하는 몇몇 응용 프로그램이 있습니다. 다음은 이러한 응용 프로그램 중 일부의 목록입니다.

| 클라이언트 | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [HDInsight 용 Microsoft Visual Studio Tools](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) | ✔ | ✔ | ✔ |
| [Azure 저장소 탐색기](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [클라우드 저장소 스튜디오 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure 탐색기](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###HDInsight 용 Visual Studio Tools

자세한 내용은 [연결된 리소스 탐색](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources)을 참조하세요.

###<a id="storageexplorer"></a>Azure 저장소 탐색기

*Azure 저장소 탐색기*는 Blob의 데이터를 검사하고 변경하는 데 유용한 도구입니다. [http://storageexplorer.com/](http://storageexplorer.com/)에서 다운로드할 수 있는 무료 오픈 소스 도구입니다. 소스 코드도 이 링크에서 사용할 수 있습니다.

이 도구를 사용하기 전에 Azure 저장소 계정 이름과 계정 키를 알아야 합니다. 더 자세한 정보를 얻을 지침을 보려면 [저장소 계정 생성, 관리 또는 삭제][azure-create-storage-account]에서 "방법: 저장소 액세스 키 보기, 복사 및 다시 생성” 섹션을 참조하세요.

1. Azure 저장소 탐색기를 실행합니다. 저장소 탐색기를 처음 실행하는 경우 ___저장소 계정 이름_\_ 및 __저장소 계정 키__를 묻는 메시지가 표시됩니다. 이전에 실행한 적이 있는 경우 __추가\_\_ 단추를 사용하여 새 저장소 계정 이름과 키를 추가합니다.

    HDinsight 클러스터에서 사용하는 저장소 계정에 대한 이름과 키를 입력한 다음 __저장 및 열기__를 선택합니다.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. 인터페이스 왼쪽의 컨테이너 목록에서 HDInsight 클러스터와 연결된 컨테이너의 이름을 클릭합니다. 기본적으로 HDInsight 클러스터의 이름이지만 클러스터를 만들 때 특정 이름을 입력한 경우에는 다를 수 있습니다.

6. 도구 모음에서 업로드 아이콘을 선택합니다.

    ![업로드 아이콘이 강조 표시된 도구 모음](./media/hdinsight-upload-data/toolbar.png)

7. 업로드할 파일을 지정한 다음 **열기**를 클릭합니다. 메시지가 표시되면 __업로드__를 선택하여 저장소 컨테이너의 루트에 파일을 업로드합니다. 특정 경로에 파일을 업로드하려는 경우 __대상__ 필드에서 경로를 입력하고 __업로드__를 선택합니다.

    ![파일 업로드 대화 상자](./media/hdinsight-upload-data/fileupload.png)
    
    파일 업로드가 완료되면 HDInsight 클러스터의 작업에서 사용할 수 있습니다.

##Azure Blob 저장소를 로컬 드라이브로 탑재

[Azure Blob 저장소를 로컬 드라이브로 탑재](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)를 참조하세요.

##서비스

###Azure 데이터 팩터리

Azure 데이터 팩터리 서비스는 데이터 저장소, 처리 및 이동 서비스를 효율적이고 확장 가능하며 안정적인 데이터 프로덕션 파이프라인으로 구성하기 위한 완전히 관리되는 서비스입니다.

Azure 데이터 팩터리를 사용하여 Azure Blob 저장소로 데이터를 이동하거나 Hive 및 Pig와 같은 HDInsight 기능을 직접 사용하는 데이터 파이프라인을 만들 수 있습니다.

자세한 내용은 [Azure 데이터 팩터리 설명서](https://azure.microsoft.com/documentation/services/data-factory/)를 참조하세요.

###<a id="sqoop"></a>Apache Sqoop

Sqoop은 Hadoop과 관계형 데이터베이스 간 데이터 전송을 위해 설계된 도구입니다. 이 도구를 사용하면 SQL Server, MySQL, Oracle 등의 관계형 데이터베이스 관리 시스템(RDBMS)에서 Hadoop 분산형 파일 시스템(HDFS)으로 데이터를 가져오고, MapReduce 또는 Hive로 Hadoop의 데이터를 변환한 후 데이터를 RDBMS로 다시 내보낼 수 있습니다.

자세한 내용은 [HDInsight에서 Sqoop 사용][hdinsight-use-sqoop]을 참조하세요.

##개발 SDK

Azure Blob 저장소는 다음 프로그래밍 언어에서 Azure SDK를 사용하여 액세스할 수 있습니다.

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK 설치에 대한 자세한 내용은 [Azure 다운로드](https://azure.microsoft.com/downloads/)를 참조하세요.


## 다음 단계
이제 HDInsight로 데이터를 가져오는 방법을 익혔으니 다음 문서를 읽고 분석을 수행하는 방법을 알아보세요:

* [Azure HDInsight 시작][hdinsight-get-started]
* [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0914_2016-->