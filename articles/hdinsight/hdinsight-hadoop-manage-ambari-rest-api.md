<properties
   pageTitle="Apache Ambari REST API를 사용하여 HDInsight 클러스터 모니터링 및 관리 | Microsoft Azure"
   description="Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari REST API를 사용하는 방법을 배웁니다."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#Ambari REST API를 사용하여 HDInsight 클러스터 관리

[AZURE.INCLUDE [ambari 선택기](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari는 손쉬운 웹 UI 및 REST API 사용을 제공하여 Hadoop 클러스터의 관리 및 모니터링을 간소화합니다. Ambari는 Linux 기반 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다. 이 문서에서는 cURL을 사용하는 일반적인 태스크를 수행하여 Ambari REST API를 사용하는 기본 사항을 알아봅니다.

##필수 조건

* [cURL](http://curl.haxx.se/): cURL은 명령줄에서 REST API와 함께 작동하도록 사용할 수 있는 크로스 플랫폼 유틸리티입니다. 이 문서에서 Ambari REST API와 통신하는데 사용됩니다.
* [jq](https://stedolan.github.io/jq/): jq는 JSON 문서 작업에 대한 크로스 플랫폼 명령줄 유틸리티입니다. 이 문서에서 Ambari REST API에서 반환된 JSON 문서를 구문 분석하는데 사용됩니다.
* [Azure CLI](../xplat-cli-install.md): Azure 서비스 작업에 대한 플랫폼 간 명령줄 유틸리티입니다.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a id="whatis"></a>Ambari 정의

[Apache Ambari](http://ambari.apache.org)에서는 Hadoop 클러스터를 프로비전, 관리 및 모니터링하는 데 사용할 수 있는 편리한 웹 UI를 제공하여 쉽게 Hadoop을 관리할 수 있습니다. 개발자는 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 사용하여 자신의 응용 프로그램에 이러한 기능을 통합할 수 있습니다.

Ambari는 Linux 기반 HDInsight 클러스터를 기본으로 제공합니다.

##REST API

HDInsight에서 Ambari REST API에 대한 기본 URI는 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME이며 여기서 __CLUSTERNAME__은 클러스터의 이름입니다.

> [AZURE.IMPORTANT] URI의 FQDN(정규화된 도메인 이름) 부분에 있는 클러스터 이름(CLUSTERNAME.azurehdinsight.net)은 대/소문자를 구분하지 않지만 URI의 다른 항목은 대/소문자를 구분합니다. 예를 들어 클러스터 이름이 MyCluster인 경우 올바른 URI는 다음과 같습니다.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> 이름의 두 번째 항목에 대/소문자가 잘못되었기 때문에 다음 URI는 오류를 반환합니다.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster` `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

HTTPS를 요구하는 HDInsight에서 Ambari로 연결 연결을 인증할 경우 클러스터를 만들 때 제공한 관리자 계정 이름(기본값: __admin__)과 암호를 사용해야 합니다.

다음 예제에서는 cURL을 사용하여 REST API에 대한 GET 요청을 수행합니다. __PASSWORD__를 클러스터의 관리자 암호로 바꿉니다. __CLUSTERNAME__을 클러스터 이름으로 바꿉니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
응답은 다음과 유사한 정보로 시작하는 JSON 문서입니다.

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

이는 JSON이므로 JSON 파서를 사용하여 데이터를 사용하는 것이 더 쉽습니다. 예를 들어, 다음 예제에서는 jq를 사용하여 `health_report` 요소만을 표시합니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##예: 클러스터 노드의 FQDN 가져오기

HDInsight에서 작업할 때 클러스터 노드의 정규화된 도메인 이름(FQDN)에 대해 알아야 할 수도 있습니다. 다음을 사용하여 클러스터의 다양한 노드에 대한 FQDN을 쉽게 검색할 수 있습니다.

* __헤드 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __작업자 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper 노드__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

이러한 예제는 각각 동일한 패턴을 따릅니다.

1. 해당 노드에서 실행되는 것으로 알고 있는 구성 요소를 쿼리합니다.
2. 이러한 노드에 대한 FQDN을 포함하는 `host_name` 요소를 검색합니다.

반환 문서의 `host_components` 요소는 여러 항목을 포함합니다. `.host_components[]`을(를) 사용한 다음 요소 내에서 경로 지정은 각 항목을 반복하고 특정 경로에서 값을 끌어냅니다. 첫 번째 FQDN 항목과 같은 하나의 값만 원하는 경우 컬렉션으로 항목을 반환하고 특정 항목을 선택할 수 있습니다.

    jq '[.host_components[].HostRoles.host_name][0]'

컬렉션에서 첫 번째 FQDN을 반환합니다.

##예: 기본 저장소 계정 및 컨테이너 가져오기

HDInsight 클러스터를 만드는 경우 Azure 저장소 계정 및 Blob 컨테이너를 클러스터에 대한 기본 저장소로 사용해야 합니다. 클러스터를 만든 후 Ambari를 사용하여 이 정보를 검색할 수 있습니다. 예를 들어 프로그래밍 방식으로 데이터를 컨테이너에 직접 쓰려는 경우입니다.

다음은 클러스터 기본 저장소의 WASB URI를 검색합니다.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] 서버(`service_config_version=1`)에 적용된 첫 번째 구성을 반환하며 이 정보를 포함합니다. 클러스터를 만든 후에 수정된 값을 검색하는 경우 구성 버전을 나열하고 최신 버전을 검색해야 합니다.

다음 예제와 유사한 값을 반환하며 여기서 __CONTAINER__는 기본 컨테이너이고 __ACCOUNTNAME__은 Azure Storage 계정 이름입니다.

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

그런 다음 [Azure CLI](../xplat-cli-install.md)로 이 정보를 사용하여 컨테이너에서 데이터를 업로드하거나 다운로드할 수 있습니다.

1. 저장소 계정에 대한 리소스 그룹을 가져옵니다. __ACCOUNTNAME__을 Ambari에서 검색한 저장소 계정 이름으로 대체합니다.

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    계정에 대한 리소스 그룹 이름을 반환합니다.
    
    > [AZURE.NOTE] 이 명령에서 아무 것도 반환되지 않는 경우 Azure CLI를 Azure 리소스 관리자 모드로 변경하고 명령을 다시 실행해야 합니다. Azure Resource Manager 모드로 전환하려면 다음 명령을 사용합니다.
    >
    > `azure config mode arm`
    
2. 저장소 계정에 대한 키를 가져옵니다. __GROUPNAME__을 이전 단계의 리소스 그룹으로 대체합니다. __ACCOUNTNAME__을 저장소 계정 이름으로 대체합니다.

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    이 예제는 계정에 대한 기본 키를 반환합니다.
    
3. 업로드 명령을 사용하여 컨테이너에 파일을 저장합니다.

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    __ACCOUNTNAME__을 저장소 계정 이름으로 대체합니다. __ACCOUNTKEY__를 이전에 검색된 키로 대체합니다. __FILEPATH__는 업로드하려는 파일에 대한 경로이며 __BLOBPATH__는 컨테이너의 경로입니다.

    예를 들어 wasbs://example/data/filename.txt에서 HDInsight에 파일을 표시하려면 __BLOBPATH__는 `example/data/filename.txt`이(가) 됩니다.

##예: Ambari 구성 업데이트

1. Ambari에서 "필요한 구성"으로 저장하는 현재 구성을 가져옵니다.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    이 예제는 클러스터에 설치된 구성 요소에 대한 현재 구성이 포함된 JSON 문서(_태그_ 값으로 식별됨)를 반환합니다. 다음 예제는 Spark 클러스터 형식에서 반환된 데이터에서 발췌한 것입니다.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    이 목록에서 구성 요소의 이름(예: __spark\_thrift\_sparkconf__) 및 __tag__ 값을 복사해야 합니다.
    
2. 다음 명령을 사용하여 구성 요소 및 태그의 구성을 검색합니다. __spark-thrift-sparkconf__ 및 __INITIAL__을 구성을 검색할 구성 요소 및 태그로 바꿉니다.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl이 JSON 문서를 검색한 다음 jq는 템플릿을 만들기 위해 데이터를 수정하는 데 사용됩니다. 템플릿은 구성 값을 추가/수정하는 데 사용됩니다. 특히 다음 작업을 수행합니다.
    
    * 문자열 "version" 및 날짜를 포함하는 고유 값을 만듭니다. 이 값은 __newtag__에 저장됩니다.
    * 필요한 새 구성의 루트 문서를 만듭니다.
    * `.items[]` 배열의 내용을 가져와서 __desired\_config__ 요소에 추가합니다.
    * __href__, __version__ 및 __Config__ 요소는 새 구성을 제출하는 데 필요하지 않으므로 삭제합니다.
    * 새 __태그__ 요소를 추가하고 해당 값을 __version#################__으로 설정합니다. 숫자 부분은 현재 날짜를 기반으로 합니다. 각 구성에 고유한 태그가 있어야 합니다.
    
    마지막으로 데이터가 __newconfig.json__ 문서에 저장됩니다. 문서 구조는 다음 예제와 유사하게 표시되어야 합니다.
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. __newconfig.json__ 문서를 열고 __properties__ 개체에서 값을 수정/추가합니다. 다음 예제에서는 __"spark.yarn.am.memory"__ 값을 __"1g"__에서 __"3g"__로 변경하고, 값이 __"256m"__인 __"spark.kryoserializer.buffer.max"__의 새 요소를 추가합니다.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    수정을 완료했으면 파일을 저장합니다.

4. 다음 명령을 사용하여 업데이트된 구성을 Ambari에 제출합니다.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    이 명령은 __newconfig.json__ 파일의 내용을 curl 요청에 파이프합니다. 그러면 필요한 새 구성으로 클러스터에 제출됩니다. cURL 요청은 JSON 문서를 반환합니다. 이 문서의 __versionTag__ 요소는 제출한 버전과 일치해야 하며, __configs__ 개체에는 요청한 구성 변경 내용이 포함됩니다.

###예: 서비스 구성 요소 다시 시작

이제 새 구성을 적용하려면 먼저 Spark 서비스를 다시 시작해야 한다는 메시지가 Ambari 웹 UI에 표시됩니다. 다음 단계를 사용하여 서비스를 다시 시작합니다.

1. 다음을 사용하여 Spark 서비스에 대한 유지 관리 모드를 사용하도록 설정합니다.

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    이 명령은 서버에 JSON 문서를 전송하며(`echo` 문에 포함) 그러면 유지 관리 모드가 켜집니다. 이제 다음 요청을 사용하여 서비스가 유지 관리 모드인지 확인할 수 있습니다.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    `"ON"` 값이 반환됩니다.

3. 다음을 사용하여 서비스를 해제합니다.

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    이 명령은 다음과 유사한 응답을 반환합니다.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    이 URI에서 반환된 `href` 값은 클러스터 노드의 내부 IP 주소를 사용합니다. 클러스터 외부에서 이를 사용하려면 '10.0.0.18:8080' 부분을 클러스터의 FQDN으로 바꿉니다. 예를 들어 다음 명령은 요청 상태를 검색합니다.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    `"COMPLETED"` 값을 반환하면 요청이 완료된 것입니다.

4. 이전 요청이 완료되면 다음을 사용하여 서비스를 시작합니다.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    서비스가 다시 시작되면 새 구성 설정이 됩니다.

5. 마지막으로, 다음을 사용하여 유지 관리 모드를 해제합니다.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##다음 단계

REST API의 모든 참조 문서를 보려면 [Ambari API 참조 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)을 참조하세요.

> [AZURE.NOTE] HDInsight 클라우드 서비스에 의해 관리되는 일부 Ambari 기능(예: 클러스터에서 호스트 추가 또는 제거 또는 새 서비스 추가)은 사용할 수 없습니다.

<!---HONumber=AcomDC_0921_2016-->