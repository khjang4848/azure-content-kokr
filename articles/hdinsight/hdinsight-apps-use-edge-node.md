<properties
	pageTitle="HDInsight에서 빈 에지 노드 사용 | Microsoft Azure"
	description="클라이언트로 사용할 수 있는 HDInsight 클러스터에 빈 에지 노드를 추가하고 HDInsight 응용 프로그램을 테스트/호스트하는 방법입니다."
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
	ms.date="09/14/2016"
	ms.author="jgao"/>

# HDInsight에서 빈 에지 노드 사용

Linux 기반 HDInsight 클러스터에 빈 에지 노드를 추가하는 방법에 대해 알아봅니다. 빈 에지 노드는 hadoop 서비스가 실행되지 않지만 헤드 노드와 같이 설치되고 구성된 동일한 클라이언트 도구를 사용하는 Linux 가상 컴퓨터입니다. 클러스터에 액세스하고, 클라이언트 응용 프로그램을 테스트하며 클라이언트 응용 프로그램을 호스트하는 데 에지 노드를 사용할 수 있습니다.

빈 에지 노드를 기존 HDInsight 클러스터에 추가할 수 있습니다(클러스터를 만들 때는 새 클러스터에 추가). 빈 에지 노드는 Azure Resource Manager 템플릿을 사용하여 추가합니다. 다음 예제는 템플릿을 사용하여 추가하는 방법을 보여 줍니다.

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
					"roles": ["edgenode"]
				}],
				"uninstallScriptActions": [],
				"httpsEndpoints": [],
				"applicationType": "CustomApplication"
			}
		}
	],

예제에서와 같이 필요에 따라 [스크립트 작업](hdinsight-hadoop-customize-cluster-linux.md)을 호출하여 추가 구성을 수행할 수 있습니다(예: 에지 노드에서 [Apache Hue](hdinsight-hadoop-hue-linux.md) 설치).

에지 노드를 만든 후 SSH를 사용하여 에지 노드에 연결할 수 있고 HDInsight에서 Hadoop 클러스터에 액세스하는 클라이언트 도구를 실행할 수 있습니다.

## 기존 클러스터에 에지 노드 추가

이 섹션에서는 Resource Manager 템플릿을 사용하여 기존 HDInsight 클러스터에 에지 노드를 추가합니다. Resource Manager 템플릿은 [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode)에서 찾을 수 있습니다. Resource Manager 템플릿은 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh에 있는 스크립트 작업 스크립트를 호출합니다. 스크립트는 어떤 작업도 수행하지 않습니다. 이는 Resource Manager 템플릿에서 스크립트 호출 작업을 보여 주기 위한 것입니다.

**기존 클러스터에 빈 에지 노드를 추가하려면**

1. 아직 없는 경우 HDInsight 클러스터를 만듭니다. [Hadoop 자습서: HDInsight에서 Linux 기반 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.
2. Azure에 로그인하여 Azure Portal에서 Azure Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 다음 속성을 구성합니다.

	- CLUSTERNAME: 기존 HDInsight 클러스터의 이름을 입력합니다.
	- EDGENODESIZE: VM 크기 중 하나를 선택합니다.
	- EDGENODEPREFIX: 기본값은 **new**입니다. 에지 노드 이름의 기본값은 **new-edgenode**가 사용됩니다. 포털에서 접두사를 사용자 지정할 수 있습니다. 템플릿에서 전체 이름을 사용자 지정할 수도 있습니다.


4. **확인**을 클릭하여 변경 내용을 저장합니다.
5. **리소스 그룹**에서 리소스 그룹을 선택합니다.
6. **약관 검토**를 클릭한 다음 **구입**을 클릭합니다.
7. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다.

## 클러스터를 만들 때 에지 노드 추가

이 섹션에서는 Resource Manager 템플릿을 사용하여 에지 노드로 HDInsight 클러스터를 만듭니다. Resource Manager 템플릿은 공용 [Azure Blob Storage 컨테이너](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)에서 찾을 수 있습니다. Resource Manager 템플릿은 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh에 있는 스크립트 작업 스크립트를 호출합니다. 스크립트는 어떤 작업도 수행하지 않습니다. 이는 Resource Manager 템플릿에서 스크립트 호출 작업을 보여 주기 위한 것입니다.

**기존 클러스터에 빈 에지 노드를 추가하려면**

1. 아직 없는 경우 HDInsight 클러스터를 만듭니다. [Hadoop 자습서: HDInsight에서 Linux 기반 Hadoop 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.
2. Azure에 로그인하여 Azure Portal에서 Azure Resource Manager 템플릿을 열려면 다음 이미지를 클릭합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 다음 속성을 구성합니다.
		
	- CLUSTERNAME: 만들려는 새 클러스터의 이름을 입력합니다.
	- CLUSTERLOGINUSERNAME: Hadoop HTTP 사용자 이름을 입력합니다. 기본 이름은 **admin**입니다.
	- CLUSTERLOGINPASSWORD: Hadoop HTTP 사용자 암호를 입력합니다.
	- SSHUSERNAME: SSH 사용자 이름을 입력합니다. 기본 이름은 **sshuser**입니다.
	- SSHPASSWORD: SSH 사용자 암호를 입력합니다.
	- LOCATION: 리소스 그룹 이름, 클러스터 및 기본 저장소 계정 위치를 입력합니다.
	- CLUSTERTYPE: 기본값은 **hadoop**입니다.
	- CLUSTERWORKERNODECOUNT: 기본값은 2입니다.
	- EDGENODESIZE: VM 크기 중 하나를 선택합니다.
	- EDGENODEPREFIX: 기본값은 **new**입니다. 에지 노드 이름의 기본값은 **new-edgenode**가 사용됩니다. 포털에서 접두사를 사용자 지정할 수 있습니다. 템플릿에서 전체 이름을 사용자 지정할 수도 있습니다.

4. **확인**을 클릭하여 변경 내용을 저장합니다.
5. **리소스 그룹**에서 새 리소스 그룹 이름을 입력합니다.
6. **약관 검토**를 클릭한 다음 **구입**을 클릭합니다.
7. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다.


## 에지 노드 액세스

에지 노드 ssh 끝점은 <EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22입니다. 예: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22

에지 노드는 Azure Portal에서 응용 프로그램으로 나타납니다. 포털은 SSH를 사용하여 에지 노드에 액세스하는 정보를 제공합니다.

**에지 노드 SSH 끝점을 확인하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 에지 노드를 사용하여 HDInsight 클러스터를 엽니다.
3. 클러스터 블레이드에서 **응용 프로그램**을 클릭합니다. 에지 노드가 표시됩니다. 기본 이름은 **new-edgenode**입니다.
4. 에지 노드를 클릭합니다. SSH 끝점이 표시됩니다.

**에지 노드에서 Hive를 사용하려면**

5. SSH를 사용하여 에지 노드에 연결합니다. [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md) 또는 [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.
6. SSH를 사용하여 에지 노드를 연결한 후 다음 명령을 사용하여 Hive 콘솔을 엽니다.

		hive
7. 클러스터의 Hive 테이블을 표시하려면 다음 명령을 실행합니다.

		show tables;

## 에지 노드를 삭제합니다.

Azure Portal에서 에지 노드를 삭제할 수 있습니다.

**에지 노드에 액세스하려면**

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 에지 노드를 사용하여 HDInsight 클러스터를 엽니다.
3. 클러스터 블레이드에서 **응용 프로그램**을 클릭합니다. 에지 노드의 목록이 표시됩니다.
4. 삭제할 에지 노드를 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
5. **예**를 클릭하여 확인합니다.

## 다음 단계

이 문서에서 에지 노드를 추가하는 방법과 에지 노드에 액세스하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

- [HDInsight 응용 프로그램 설치](hdinsight-apps-install-applications.md): HDInsight 응용 프로그램을 클러스터에 설치하는 방법을 알아봅니다.
- [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
- [HDInsight 응용 프로그램 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 응용 프로그램을 Azure Marketplace에 게시하는 방법을 알아봅니다.
- [MSDN: HDInsight 응용 프로그램 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 응용 프로그램을 정의하는 방법을 알아봅니다.
- [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
- [Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.

<!----HONumber=AcomDC_0914_2016-->