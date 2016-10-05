<properties
   pageTitle="Linux 기반 HDInsight에서 Hadoop을 사용 하기 위한 팁 | Microsoft Azure"
   description="Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Linux 기반 HDInsight(Hadoop) 클러스터를 사용하기 위한 구현 팁을 제공합니다."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# Linux에서 HDInsight 사용에 관한 정보

Linux 기반 Azure HDInsight 클러스터는 Azure 클라우드에서 실행되는 친숙한 Linux 환경에서 Hadoop을 제공합니다. 대부분의 작업에 대해 Linux 설치에서 모든 다른 Hadoop으로 정확하게 작동해야 합니다. 이 문서를 알고 있어야 하는 특정 차이점을 호출합니다.

##필수 조건

이 문서의 단계 대부분은 많은 시스템에 설치해야 할 수 있는 다음과 같은 유틸리티를 사용합니다.

* [cURL](https://curl.haxx.se/) - 웹 기반 서비스와 통신하는 데 사용됩니다
* [jq](https://stedolan.github.io/jq/) -JSON 문서를 구문 분석하는 데 사용됩니다
* [Azure CLI](../xplat-cli-install.md) - Azure 서비스를 원격으로 관리 하는 데 사용됩니다.

	[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## 도메인 이름

인터넷에서 클러스터에 연결할 때 사용하려는 FQDN(정규화된 도메인 이름)은 **&lt;clustername>.azurehdinsight.net** 또는 **&lt;clustername-ssh>.azurehdinsight.net**(SSH인 경우만)입니다.

내부적으로 클러스터의 각 노드 이름은 클러스터 구성 중에 할당됩니다. 클러스터 이름을 찾으려면 Ambari 웹 UI에서 __호스트__ 페이지를 방문하거나 다음을 통해 Ambari REST API에서 호스트 목록을 반환합니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

__PASSWORD__를 관리 계정의 암호로 바꾸고 __CLUSTERNAME__을 클러스터의 이름으로 바꿉니다. 그러면 클러스터의 호스트 목록이 포함된 JSON 문서를 반환한 다음 jq가 클러스터의 각 호스트에 대한 `host_name` 요소 값을 추출합니다.

특정 서비스에 대한 노드의 이름을 찾으려면 해당 구성 요소에 대해 Ambari를 쿼리하면 됩니다. 예를 들어 HDFS 이름 노드에 대한 호스트를 찾으려면 다음을 사용합니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

그러면 서비스를 설명하는 JSON 문서를 반환한 다음 jq가 해당 호스트에 대한 `host_name` 값만 추출합니다.

## 서비스에 대한 원격 액세스

* **Ambari(웹)** - https://&lt;clustername>. azurehdinsight.net

	클러스터 관리자 계정 및 암호를 사용하여 인증하고 Ambari에 로그인합니다. 여기에서도 클러스터 관리자 계정 및 암호를 사용합니다.

	인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

	> [AZURE.IMPORTANT] 인터넷을 통해 직접 액세스할 수 있는 클러스터의 Ambari의 경우, 일부 기능은 클러스터에서 사용하는 내부 도메인 이름으로 노드에 액세스해야 합니다. 공용이 아닌 내부 도메인 이름이어야 하므로 인터넷을 통해 일부 기능에 액세스하면 "서버를 찾을 수 없음" 오류가 발생합니다.
	>
	> Ambari 웹 UI의 모든 기능을 사용하려면 프록시 웹 트래픽에 대한 SSH 터널을 클러스터 헤드 노드에 사용합니다. [SSH 터널링을 사용하여 Ambari 웹 UI, ResourceManager, JobHistory, NameNode, Oozie 및 기타 웹 UI에 액세스](hdinsight-linux-ambari-ssh-tunnel.md)를 참조하세요.

* **Ambari(REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	>
	> 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **WebHCat(Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] 클러스터 관리자 계정 및 암호를 사용하여 인증합니다.
	>
	> 인증은 일반 텍스트입니다. 항상 HTTPS를 사용하여 연결의 보안을 유지합니다.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net(포트 22 또는 23) 포트 22는 기본 헤드 노드에 연결하는 데 사용되는 반면 포트 23은 보조 헤드 노드에 연결하는 데 사용됩니다. 헤드 노드에 대한 자세한 내용은 [HDInsight에서 Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)을 참조하세요.

	> [AZURE.NOTE] 클라이언트 컴퓨터에서 SSH를 통해 클러스터 헤드 노드에 액세스할 수 있습니다. 연결한 후 헤드 노드에서 SSH를 사용하여 작업자 노드에 액세스할 수 있습니다.

## 파일 위치

Hadoop 관련 파일은 `/usr/hdp`의 클러스터 노드에서 찾을 수 있습니다. 이 디렉터리에는 다음과 같은 하위 디렉터리가 포함됩니다.

* __2.2.4.9-1__: 이 디렉터리 이름은 HDInsight에서 사용되는 Hortonworks Data Platform 버전에 따라 지정되므로 사용 중인 클러스터의 번호가 여기 나열된 번호와 다를 수 있습니다.
* __current__: 이 디렉터리는 __2.2.4.9-1__ 디렉터리 아래의 디렉터리에 대한 링크를 포함하며 파일에 액세스할 때마다 버전 번호(변경될 수 있음)를 입력하지 않아도 됩니다.

예제 데이터 및 JAR 파일은 HDFS(Hadoop Distributed File System) 또는 Azure Blob 저장소('/example' 또는 'wasbs:///example')에서 찾을 수 있습니다.

## HDFS, Azure Blob 저장소 및 저장소 모범 사례

대부분의 Hadoop 배포판에서 HDFS는 클러스터의 컴퓨터에서 로컬 저장소에 의해 되돌아갑니다. 효율적이지만 계산 리소스에 시간당 또는 분당 비용이 부과되는 클라우드 기반 솔루션에 대한 비용이 많이 들 수 있습니다.

HDInsight는 다음과 같은 이점을 제공하는 기본 저장소로 Azure Blob 저장소를 사용합니다.

* 저렴한 장기 저장소

* 웹 사이트, 파일 업로드/다운로드 유틸리티, 다양한 언어 SDK 및 웹 브라우저와 같은 외부 서비스에 액세스할 수 있음

HDInsight에 대한 기본 저장소가 된 이후 일반적으로 사용하기 위해 아무런 동작도 수행할 필요가 없습니다. 예를 들어 다음 명령은 Azure Blob 저장소에 저장된 **/example/data** 폴더의 파일을 나열합니다.

	hdfs dfs -ls /example/data

일부 명령은 Blob 저장소를 사용할지 지정해야 할 수 있습니다. 이러한 경우 **wasb://** 또는 **wasbs://**를 명령 앞에 붙일 수 있습니다.

HDInsight은 클러스터와 여러 개의 Blob 저장소 계정을 연결할 수도 있습니다. 기본이 아닌 Blob 저장소 계정 데이터에 액세스하려면 **wasbs://&lt;container-name>@&lt;account-name>.blob.core.windows.net/** 형식을 사용할 수 있습니다. 예를 들어 다음은 지정된 컨테이너 및 Blob 저장소 계정에 대한 **/example/data** 디렉터리 콘텐츠를 나열합니다.

	hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### Blob 저장소는 어떤 클러스터를 사용하나요?

클러스터를 만드는 동안 기존 Azure 저장소 계정 및 컨테이너를 사용할지 아니면 새로 만들지를 선택했습니다. 그런 다음 잊어버릴 수 있습니다. Ambari REST API를 사용하여 기본 저장소 계정 및 컨테이너를 찾을 수 있습니다.

1. curl을 사용하여 HDFS 구성 정보를 검색하도록 다음 명령을 사용하고 [jq](https://stedolan.github.io/jq/)를 사용하여 필터링합니다.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 할 수도 있습니다.

    다음과 유사한 값을 반환하며 여기서 __CONTAINER__는 기본 컨테이너이고 __ACCOUNTNAME__은 Azure 저장소 계정 이름입니다.

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. 저장소 계정에 대한 리소스 그룹을 가져오고 [Azure CLI](../xplat-cli-install.md)를 사용합니다. 다음 명령에서 __ACCOUNTNAME__을 Ambari에서 검색한 저장소 계정 이름으로 대체합니다.

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    계정에 대한 리소스 그룹 이름을 반환합니다.
    
    > [AZURE.NOTE] 이 명령에서 아무 것도 반환되지 않는 경우 Azure CLI를 Azure 리소스 관리자 모드로 변경하고 명령을 다시 실행해야 합니다. Azure 리소스 관리자 모드로 전환하려면 다음 명령을 사용합니다.
    >
    > `azure config mode arm`
    
2. 저장소 계정에 대한 키를 가져옵니다. __GROUPNAME__을 이전 단계의 리소스 그룹으로 대체합니다. __ACCOUNTNAME__을 저장소 계정 이름으로 대체합니다.

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    계정에 대한 기본 키를 반환합니다.

또한 Azure 포털을 사용하여 저장소 정보를 찾을 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에서 HDInsight 클러스터를 선택합니다.

2. __필수__ 섹션에서 __모든 설정__을 선택합니다.

3. __설정__에서 __Azure 저장소 키__를 선택합니다.

4. __Azure 저장소 키__에서 나열된 저장소 계정 중 하나를 선택합니다. 그러면 저장소 계정에 대한 정보가 표시됩니다.

5. 키 아이콘을 선택합니다. 그러면 저장소 계정에 대한 키가 표시됩니다.

### Blob 저장소에 액세스하려면 어떻게 해야 합니까?

클러스터에서 Hadoop 명령을 통하는 것 외에 blob에 액세스할 수 있는 다양한 방법이 있습니다.

* [Mac, Linux 및 Windows용 Azure CLI](../xplat-cli-install.md): Azure로 작업하기 위한 명령줄 인터페이스 명령입니다. 설치 후 저장소 사용에 대한 도움말은 `azure storage`를 참조하고 Blob 관련 명령에 대한 도움말은 `azure blob`을 참조하세요.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Azure 저장소의 Blob 작업을 위한 python 스크립트입니다.

* 다양한 SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.JS](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [저장소 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>클러스터 크기 조정

클러스터 크기 조정 기능을 사용하여 클러스터를 삭제한 후 다시 생성하지 않고 Azure HDInsight에서 실행되는 클러스터에서 사용되는 데이터 노드 수를 변경합니다.

클러스터에서 다른 작업 또는 프로세스가 실행되는 동안 크기 조정 작업을 수행할 수 있습니다.

다른 클러스터 종류는 다음과 같이 크기 조정에 영향을 받습니다.

* __Hadoop__: 클러스터의 노드 수를 축소할 때 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 작업이 완료되면 작업을 다시 제출할 수 있습니다.

* __HBase__: 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 지역 서버를 수동으로 조정하려면 다음 단계를 사용합니다.

	1. SSH를 사용하여 HDInsight 클러스터에 연결합니다. HDInsight에서 SSH를 사용하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

		* [Linux, Unix 및 Mac OS X에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Windows에서 HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. 다음을 사용하여 HBase 셸을 시작합니다.

			hbase shell

	2. HBase 셸이 로드되면 다음을 사용하여 지역 서버를 수동으로 조정합니다.

			balancer

* __Storm__: 크기 조정 작업을 수행한 후 실행 중인 모든 Storm 토폴로지 균형을 다시 조정해야 합니다. 토폴로지를 새 클러스터의 노드 수에 따라 병렬 처리 설정을 다시 조정할 수 있습니다. 실행 중인 토폴로지의 균형을 다시 조정하려면 다음 옵션 중 하나를 사용합니다.

	* __SSH__: 서버에 연결하고 다음 명령을 사용하여 토폴로지 균형을 다시 조정합니다.

			storm rebalance TOPOLOGYNAME

		매개 변수를 지정하여 원래 토폴로지로 제공된 병렬 처리 힌트를 재정의할 수도 있습니다. 예를 들어 `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10`은 토폴로지를 5 작업자 프로세스, 파란색 spout 구성 요소에 대해 3 실행자 및 노란색 bolt 구성 요소에 대해 10 실행자로 다시 구성합니다.

	* __Storm UI__: Storm UI를 사용하여 토폴로지의 균형을 다시 조정하려면 다음 단계를 사용합니다.

		1. 웹 브라우저에서 \_\_https://CLUSTERNAME.azurehdinsight.net/stormui__을 엽니다. 여기서 CLUSTERNAME은 Storm 클러스터의 이름입니다. 메시지가 표시되면 클러스터를 만들 때 지정한 HDInsight 클러스터 관리자(관리자) 이름 및 암호를 입력합니다.

		3. 균형을 다시 조정하려는 토폴로지를 선택한 다음 __균형 다시 맞추기__ 단추를 선택합니다. 균형 재조정 작업이 수행되기 전에 지연 시간을 입력합니다.

HDInsight 클러스터 크기 조정에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 포털을 사용하여 HDInsight에서 Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md#scaling)

* [Azure PowerShell을 사용하여 HDInsight의 Hadoop 클러스터 관리](hdinsight-administer-use-command-line.md#scaling)

## Hue(또는 다른 Hadoop 구성 요소)를 어떻게 설치합니까?

HDInsight는 관리되는 서비스로 문제가 발견되면 클러스터의 노드가 자동으로 소멸되고 다시 프로비전됩니다. 이 때문에 클러스터 노드에 프로그램을 수동으로 직접 설치하는 것은 좋지 않습니다. 대신 다음을 설치해야 하는 경우 [HDInsight 스크립트 동작](hdinsight-hadoop-customize-cluster.md)을 사용합니다.

* Spark 또는 Hue와 같은 서비스 또는 웹 사이트입니다.
* 클러스터의 여러 노드에 대해 구성 변경을 필요로 하는 구성 요소입니다. 예를 들어 필수 환경 변수, 로깅 디렉터리 만들기 또는 구성 파일 만들기입니다.

스크립트 작업은 클러스터가 프로비전되는 동안 실행되는 Bash 스크립트이며 클러스터에 추가 구성 요소를 설치 및 구성하는 데 사용할 수 있습니다. 다음 구성 요소를 설치하기 위한 예제 스크립트가 제공됩니다.

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

사용자 고유의 스크립트 작업 개발에 대한 정보는 [HDInsight를 사용하여 스크립트 작업 개발](hdinsight-hadoop-script-actions-linux.md)을 참조하세요.

###Jar 파일

일부 Hadoop 기술은 MapReduce 작업의 일부로 또는 Pig 또는 Hive 내부에서 사용되는 함수를 포함하는 자체 포함된 jar 파일에 제공됩니다. 스크립트 작업을 사용하여 설치할 수 있는 반면 종종 설치가 필요하지 않고 프로비전한 후에 클러스터에 업로드하고 직접 사용할 수 있습니다. 구성 요소를 클러스터의 이미지로 다시 설치하려는 경우 WASB에 jar 파일을 저장할 수 있습니다.

예를 들어 [DataFu](http://datafu.incubator.apache.org/)의 최신 버전을 사용하려는 경우 프로젝트가 포함된 jar을 다운로드하고 HDInsight 클러스터에 업로드할 수 있습니다. 그런 다음 Pig 또는 Hive를 사용하는 방법에 대한 DataFu 설명서를 수행합니다.

> [AZURE.IMPORTANT] 독립 실행형 jar 파일인 구성 요소 일부는 HDInsight와 함께 제공되지만 경로에 있지 않습니다. 특정 구성 요소를 찾으려면 다음을 사용하여 클러스터에서 검색할 수 있습니다.
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 이렇게 하면 jar 파일에 일치하는 경로를 반환합니다.

클러스터가 구성 요소의 버전을 독립 실행형 jar 파일로 제공하지만 다른 버전을 사용하려는 경우 클러스터에 새 버전의 구성 요소를 업로드하고 작업에 사용해 볼 수 있습니다.

> [AZURE.WARNING] HDInsight 클러스터와 함께 제공된 구성 요소는 완전히 지원되며 Microsoft 지원에서 이러한 구성 요소와 관련된 문제를 해결하는 데 도움을 드릴 것입니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. 지원을 통해 문제를 해결하거나 해당 기술에 대한 전문 지식이 있는, 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요구할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/ko-KR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [http://apache.org](http://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/)).

## 다음 단계

* [Windows 기반 HDInsight에서 Linux 기반 HDInsight로 마이그레이션](hdinsight-migrate-from-windows-to-linux.md)
* [HDInsight에서 Hive 사용](hdinsight-use-hive.md)
* [HDInsight에서 Pig 사용](hdinsight-use-pig.md)
* [HDInsight에서 MapReduce 작업 사용](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0921_2016-->