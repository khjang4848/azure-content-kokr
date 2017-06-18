<properties
	pageTitle="Azure CLI를 사용하여 Hadoop 클러스터 관리 | Microsoft Azure"
	description="Azure CLI를 사용하여 HDInsight에서 Hadoop 클러스터를 관리하는 방법"
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>

# Azure CLI를 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]

[Azure 명령줄 인터페이스](../xplat-cli-install.md)를 사용하여 Azure HDInsight의 Hadoop 클러스터를 관리하는 방법을 알아봅니다. Azure CLI는 Node.js로 구현되며 Windows, Mac, Linux를 포함하여 Node.js를 지원하는 플랫폼에서 사용할 수 있습니다.

이 문서에서는 HDInsight을 통한 Azure CLI 사용에 대해서만 설명합니다. Azure CLI를 사용하는 방법에 대한 일반 가이드는 [Azure CLI 설치 및 구성][azure-command-line-tools]을 참조하세요.

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **Azure CLI** - 설치 및 구성 정보는 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하세요.
- **Azure에 연결**. 다음 명령을 사용합니다.

		azure login

	회사 또는 학교 계정을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](xplat-cli-connect.md)을 참조하세요.
	
- **Azure 리소스 관리자 모드로 전환**. 다음 명령을 사용합니다.

		azure config mode arm

도움말을 보려면 **-h** 스위치를 사용합니다. 예:

	azure hdinsight cluster create -h
	
##클러스터 만들기

[Azure CLI를 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-azure-cli.md) 참조하세요.

##클러스터 세부 정보 나열 및 표시
클러스터 세부 정보를 나열하고 표시하려면 다음 명령을 사용합니다.

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##클러스터 삭제

클러스터를 삭제하려면 다음 명령을 사용합니다.

	azure hdinsight cluster delete <Cluster Name>

또한 클러스터를 포함하는 리소스 그룹을 삭제하여 클러스터를 삭제할 수도 있습니다. 이렇게 하면 기본 저장소 계정을 포함한 그룹 내 모든 리소스가 삭제됩니다.

	azure group delete <Resource Group Name>

##클러스터 크기 조정

Hadoop 클러스터 크기를 변경하려면

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## 클러스터에 대한 HTTP 액세스 사용/사용 안 함

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## 클러스터에 대한 RDP 액세스 사용/사용 안 함

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##다음 단계
이 문서에서는 HDInsight 클러스터 관리 작업을 수행하는 여러 방법에 대해 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]
* [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
* [Azure HDInsight 시작][hdinsight-get-started]
* [Azure CLI를 사용하는 방법][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "클러스터 나열 및 표시"

<!---HONumber=AcomDC_0914_2016-->