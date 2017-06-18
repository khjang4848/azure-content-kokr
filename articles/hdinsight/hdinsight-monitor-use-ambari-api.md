<properties
	pageTitle="Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링 | Microsoft Azure"
	description="Hadoop 클러스터를 생성, 관리 및 모니터링하는 데 Apache Ambari API를 사용합니다. 직관적 운영자 도구와 API를 사용하면 Hadoop의 복잡한 작업을 간편하게 수행할 수 있습니다."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	editor="cgronlun"
	manager="jhubbard"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jgao"/>

# Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링

Ambari API를 사용하여 HDInsight 클러스터를 모니터링하는 방법을 알아봅니다.

> [AZURE.NOTE] 이 문서의 정보는 주로 Windows 기반 HDInsight 클러스터에 대한 것이며 Ambari REST API의 읽기 전용 버전을 제공합니다. Linux 기반 클러스터는 [Ambari를 사용하여 Hadoop 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## Ambari 정의

[Apache Ambari][ambari-home]는 Apache Hadoop 클러스터를 프로비전, 관리 및 모니터링하는 데 사용됩니다. Hadoop의 복잡성을 숨기고 클러스터 작업을 단순화하는 직관적인 연산자 도구 모음 및 강력한 API 집합이 포함되어 있습니다. API에 대한 자세한 내용은 [Ambari API 참조][ambari-api-reference]를 참조하세요.

HDInsight는 현재 Ambari 모니터링 기능만 지원합니다. Ambari API 1.0은 HDInsight 클러스터 버전 3.0 및 2.1 클러스터에서만 지원됩니다. 이 문서에서는 HDInsight 버전 3.1 및 2.1 클러스터에서 Ambari API에 액세스하는 방법에 대해 설명합니다. 두 버전 간의 가장 큰 차이점은 작업 기록 서버와 같은 새 기능이 도입되면서 일부 구성 요소가 변경되었다는 것입니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure PowerShell이 포함된 워크스테이션**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (선택 사항) [cURL][curl]. 설치하려면 [cURL 릴리스 및 다운로드][curl-download]를 참조하세요.

	>[AZURE.NOTE] Windows에서 cURL 명령을 사용할 때는 옵션 값에 작은따옴표 대신 큰따옴표를 사용합니다.

- **Azure HDInsight 클러스터**. 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 사용 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 자습서를 완료하려면 다음 데이터가 필요합니다.

    클러스터 속성|Azure PowerShell 변수 이름|값|설명
    ---|---|---|---
    HDInsight 클러스터 이름|$clusterName||HDInsight 클러스터의 이름입니다.
    클러스터 사용자 이름|$clusterUsername||클러스터 사용자 이름은 클러스터를 만들 때 지정됩니다.
    클러스터 암호|$clusterPassword||클러스터 사용자 암호입니다.

    >[AZURE.NOTE] 테이블의 채우기 값입니다. 이 자습서를 완료하는 데 유용합니다.

## 신속한 시작

Ambari를 사용하여 HDInsight 클러스터를 모니터링하는 몇 가지 방법이 있습니다.

**Azure PowerShell 사용**

다음은 *HDInsight 3.1 클러스터*에서 MapReduce 작업 추적기 정보를 가져오는 Azure PowerShell 스크립트입니다. 가장 큰 차이점은 MapReduce가 아니라 YARN 서비스에서 이러한 세부 정보를 가져온다는 것입니다.

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'yarn.queueMetrics'

다음은 *HDInsight 2.1 클러스터*에서 MapReduce 작업 추적기 정보를 가져오는 Azure PowerShell 스크립트입니다.

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"

	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

	$response.metrics.'mapred.JobTracker'

출력은 다음과 같습니다.

![Jobtracker 출력][img-jobtracker-output]

**cURL 사용**

다음은 cURL을 사용하여 클러스터 정보를 가져오는 예입니다.

	curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

출력은 다음과 같습니다.

	{"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
	 "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
	 "services"[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
	    "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
	 "hosts":[
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0"}},
	   {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
	    "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**2014/10/8 릴리스**:

Ambari 끝점 "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}"을 사용할 때 *host\_name* 필드에서 호스트 이름만이 아니라 노드의 FQDN(정규화된 도메인 이름)을 반환합니다. 10/8/2014 릴리스 이전 버전에서는 이 예가 "**headnode0**"만 반환했습니다. 10/8/2014 릴리스부터는 위의 예에 나와 있는 것처럼 FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**"이 반환됩니다. 이 변경은 HBase, Hadoop 등의 여러 클러스터 유형을 VNET(가상 네트워크) 하나에 배포할 수 있는 시나리오를 원활하게 수행하기 위해 필요한 작업이었습니다. 예를 들어 Hadoop의 백 엔드 플랫폼으로 HBase를 사용하는 등의 경우 이 변경이 적용됩니다.

## Ambari 모니터링 API

다음 테이블은 가장 일반적으로 사용되는 Ambari 모니터링 API 호출을 나열합니다. API에 대한 자세한 내용은 [Ambari API 참조][ambari-api-reference]를 참조하세요.

모니터링 API 호출|URI|설명
---|---|---
클러스터 가져오기|`/api/v1/clusters`|
클러스터 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|클러스터, 서비스, 호스트
서비스 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|서비스에 포함: hdfs, mapreduce
서비스 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
서비스 구성 요소 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker
구성 요소 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, host-components, 메트릭
호스트 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
호스트 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
호스트 구성 요소 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
호스트 구성 요소 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, 구성 요소, 호스트, 메트릭
구성 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|구성 유형: core-site, hdfs-site, mapred-site, hive-site
구성 정보 가져오기|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|구성 유형: core-site, hdfs-site, mapred-site, hive-site


##다음 단계

Ambari 모니터링 API 호출을 사용하는 방법을 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [Azure 포털을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-portal]
- [Azure PowerShell을 사용하여 HDInsight 클러스터 관리][hdinsight-admin-powershell]
- [명령줄 인터페이스를 사용하여 HDInsight 클러스터 관리][hdinsight-admin-cli]
- [HDInsight 설명서][hdinsight-documentation]
- [HDInsight 시작][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png

<!---HONumber=AcomDC_0914_2016-->