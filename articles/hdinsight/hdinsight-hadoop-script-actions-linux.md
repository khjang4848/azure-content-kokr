<properties
    pageTitle="Linux 기반 HDInsight를 사용하여 스크립트 작업 개발 | Microsoft Azure"
    description="스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터를 사용자 지정하는 방법을 알아봅니다."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="larryfr"/>

# HDInsight를 사용하여 스크립트 작업 개발

스크립트 작업은 클러스터 구성 설정을 지정하거나 클러스터에서 추가 서비스, 도구 또는 기타 소프트웨어를 설치하여 Azure HDInsight 클러스터를 사용자 지정하는 방법입니다. 클러스터를 생성하는 동안 또는 실행 중인 클러스터에 대해 스크립트 작업을 사용할 수 있습니다.

> [AZURE.NOTE] 이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 클러스터로 스크립트 작업 사용에 대한 정보는 [HDInsight를 사용하여 스크립트 작업 개발](hdinsight-hadoop-script-actions.md)을 참조하세요.

## 스크립트 작업 정의

스크립트 작업은 Azure가 구성을 변경하거나 소프트웨어를 설치하기 위해 클러스터 노드에서 실행하는 Bash 스크립트입니다. 스크립트 작업은 루트로 실행되며 클러스터 노드에 대한 모든 액세스 권한을 제공합니다.

스크립트 작업은 다음 방법을 통해 적용될 수 있습니다.

| 이 방법을 사용하여 스크립트를 적용... | 클러스터를 생성하는 동안... | 실행 중인 클러스터에서... |
| ----- |:-----:|:-----:|
| Azure 포털 | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| Azure CLI | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Azure Resource Manager 템플릿 | ✓ | &nbsp; |

이러한 방법을 사용하여 스크립트 작업을 적용하는 데 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="bestPracticeScripting"></a>스크립트 개발을 위한 모범 사례

HDInsight 클러스터용으로 사용자 지정 스크립트를 개발할 때 유의해야 하는 몇 가지 모범 사례는 다음과 같습니다.

- [Hadoop 버전 대상](#bPS1)
- [스크립트 리소스에 대한 안정적인 링크 제공](#bPS2)
- [사전 컴파일한 리소스 사용](#bPS4)
- [클러스터 사용자 지정 스크립트가 멱등원인지 확인](#bPS3)
- [클러스터 아키텍처의 고가용성 확인](#bPS5)
- [Azure Blob 저장소를 사용하도록 사용자 지정 구성 요소 구성](#bPS6)
- [STDOUT 및 STDERR에 정보 쓰기](#bPS7)
- [줄 끝을 LF인 파일을 ASCII로 저장](#bps8)
- [다시 시도 논리를 사용하여 일시적 오류에서 복구](#bps9)

> [AZURE.IMPORTANT] 스크립트 작업은 60분 이내에 완료하지 않으면 시간이 초과됩니다. 노드 프로비전 중에는 스크립트가 다른 설정 및 구성 프로세스와 동시에 실행됩니다. CPU 시간 또는 네트워크 대역폭 등의 리소스에 대한 경합으로 인해 스크립트 실행이 개발 환경에서보다 더 오래 걸릴 수 있습니다.

### <a name="bPS1"></a>Hadoop 버전 대상

HDInsight의 서로 다른 버전에는 설치된 Hadoop 서비스 및 구성 요소의 서로 다른 버전이 있습니다. 스크립트가 특정 버전의 서비스 또는 구성 요소를 기대하는 경우 필수 구성 요소를 포함하는 HDInsight의 버전으로 스크립트를 사용해야 합니다. [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md) 문서를 사용하여 HDInsight돠 포함된 구성 요소 버전에 대한 정보를 찾을 수 있습니다.

### <a name="bPS2"></a>스크립트 리소스에 대한 안정적인 링크 제공

사용자는 스크립트에서 사용되는 모든 스크립트와 리소스가 클러스터의 전체 수명 동안 사용 가능한 상태로 유지되고 이러한 파일의 버전이 이 기간 동안 변경되지 않는지 확인해야 합니다. 이러한 리소스는 크기 조정 작업 중 새 노드가 클러스터에 추가되는 경우에 필요합니다.

구독의 Azure Storage 계정에 모든 것을 다운로드하고 보관하는 것이 좋습니다.

> [AZURE.IMPORTANT] 사용된 저장소 계정은 클러스터의 기본 저장소 계정 또는 다른 모든 저장소 계정의 공용 읽기 전용 컨테이너에 있어야 합니다.

예를 들어 Microsoft에서 제공하는 샘플은 HDInsight 팀에서 유지 관리하는 공용, 읽기 전용 컨테이너를 저장소 계정인 [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/)에 저장됩니다.

### <a name="bPS4"></a>사전 컴파일한 리소스 사용

스크립트 실행 시간을 줄이려면 소스 코드로부터 리소스를 컴파일하는 작업은 실행하지 않습니다. 대신 리소스를 사전에 컴파일하여 Azure Blob 저장소에 이진으로 저장하면 스크립트로부터 신속하게 클러스터에 다운로드할 수 있습니다.

### <a name="bPS3"></a>클러스터 사용자 지정 스크립트가 멱등원인지 확인

스크립트는 idempotent 상태가 되도록 설계해야 합니다. 즉, 스크립트가 여러 번 실행될 경우 클러스터가 매번 실행될 때와 동일한 상태로 돌아가는지 확인해야 합니다.

예를 들어 사용자 지정 스크립트가 처음 실행될 때와 이후 실행될 때마다 응용 프로그램을 /usr/local/bin에 설치 설치한 경우 스크립트에서는 응용 프로그램이 이미 /usr/local/bin 위치에 있는지 확인한 이후에 스크립트의 다른 단계를 진행해야 합니다.

### <a name="bPS5"></a>클러스터 아키텍처의 고가용성 확인

Linux 기반 HDInsight 클러스터는 클러스터 내에서 활성화 되는 두 노드를 제공하며 스크립트 동작은 두 노드에서 실행됩니다. 설치할 구성 요소가 헤드 노드를 하나라고 예상하는 경우 클러스터의 두 헤드 노드 중 하나에만 구성 요소를 설치하는 스크립트를 디자인해야 합니다.

> [AZURE.IMPORTANT] HDInsight의 일부로 설치된 기본 서비스는 필요에 따라 두 헤드 노드 간에 장애 조치를 하도록 설계되지만 이 기능이 확장되어 스크립트 동작을 통해 설치된 구성 요소를 사용자지정하지 않습니다. 스크립트 동작을 통해 설치된 구성 요소가 가용성이 높아야 하는 경우 사용 가능한 두 헤드 노드를 사용하는 장애 조치 메커니즘을 구현해야 합니다.

### <a name="bPS6"></a>Azure Blob 저장소를 사용하도록 사용자 지정 구성 요소 구성

클러스터에 설치하는 구성 요소에는 HDFS(Hadoop 분산 파일 시스템) 저장소를 사용하기 위한 기본 구성이 있을 수 있습니다. HDInsight는 WASB(Azure Blob 저장소)를 기본 저장소로 사용합니다. 이를 통해 클러스터가 삭제되는 경우에도 데이터가 지속되는 HDFS 호환 가능 파일 시스템을 제공합니다. 설치하는 구성 요소가 HDFS 대신 WASB를 사용하도록 구성해야 합니다.

예를 들어 다음은 giraph examples.jar 파일을 로컬 파일 시스템에서 WASB로 복사합니다.

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>STDOUT 및 STDERR에 정보 쓰기

스크립트 실행 동안 STDOUT 및 STDERR에 기록된 정보는 로깅되고 Ambari 웹 UI를 사용하여 볼 수 있습니다.

> [AZURE.NOTE] Ambari는 클러스터를 정상적으로 만든 경우에만 사용할 수 있습니다. 클러스터를 만드는 동안 스크립트 작업을 사용하며 만들기에 실패하는 경우 문제 해결 섹션 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)에서 로깅된 정보에 액세스하는 다른 방법을 확인해보세요.

대부분의 유틸리티 및 설치 패키지는 STDOUT 및 STDERR에 정보를 쓸 수 있지만 추가 로깅을 추가하려 할 수도 있습니다. 사용할 텍스트를 STDOUT에 보내려면 `echo`를 사용합니다. 예:

        echo "Getting ready to install Foo"

기본적으로 `echo`은 STDOUT에 문자열을 보냅니다. STDERR에 전달하려면 `echo` 앞에 `>&2`를 추가합니다. 예:

        >&2 echo "An error occurred installing Foo"

STDERR(2), STDOUT(1, 여기에 나와있지 않은 기본 형식임)로 전송된 정보를 리디렉션합니다. IO 리디렉션에 대한 자세한 내용은 [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html)를 참조하세요.

스크립트 동작에서 기록된 정보 보기에 대한 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)을 참조하세요.

###<a name="bps8"></a>줄 끝을 LF인 파일을 ASCII로 저장

Bash 스크립트는 LF에서 종료한 줄을 사용하여 ASCII 형식으로 저장되어야 합니다. 파일이 파일의 시작 부분에 바이트 순서 표시를 포함할 수 있는 UTF-8로 저장된 경우 또는 줄 끝이 Windows 편집기에 공통되는 CRLF인 경우 스크립트는 다음과 유사한 오류로 실패합니다.

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a> 다시 시도 논리를 사용하여 일시적 오류에서 복구

파일을 다운로드할 때 apt-get 또는 인터넷을 통해 데이터를 전송하는 기타 작업을 사용하여 패키지를 설치하면 일시적인 네트워킹 오류로 인해 작업이 실패할 수 있습니다. 예를 들어 통신하는 원격 리소스가 백업 노드로의 장애 조치(failover) 중일 수 있습니다.

스크립트를 일시적인 오류에 대해 탄력적으로 만들려면 다시 시도 논리를 구현할 수 있습니다. 다음은 전달된 모든 명령을 실행하고(명령이 실패할 경우) 최대 3번 다시 시도하는 함수 예제입니다. 각 다시 시도 사이에는 2초 동안 대기됩니다.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

다음은 이 함수 사용 예제입니다.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>사용자 지정 스크립트에 대한 도우미 메서드

스크립트 작업 도우미 메서드는 사용자 지정 스크립트를 쓰는 동안 사용할 수 있는 유틸리티입니다. 이 메서드는 [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)에 정의되어 있으며 다음을 사용하여 스크립트에 포함할 수 있습니다.

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

이렇게 하면 스크립트에서 사용하기 위해 도우미를 사용할 수 있게 됩니다.

| 도우미 사용 | 설명 |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | 소스 URL에서 지정된 파일 경로로 파일을 다운로드합니다. 기본적으로 기존 파일을 덮어쓰지 않습니다. |
| `untar_file TARFILE DESTDIR` | (`-xf`을 사용하여)대상 디렉터리에 Tar 파일을 추출합니다. |
| `test_is_headnode` | 클러스터 헤드 노드에서 실행되는 경우 1을 반환하고 그렇지 않으면 0을 반환합니다. |
| `test_is_datanode` | 현재 노드가 데이터(작업자) 노드인 경우 1을 반환합니다. 그렇지 않으면 0을 반환합니다. |
| `test_is_first_datanode` | 현재 노드가 첫 번째 데이터(작업자) 노드(workernode0라는 이름)인 경우 1을 반환합니다. 그렇지 않으면 0을 반환합니다. |
| `get_headnodes` | 클러스터에서 헤드 노드의 정규화된 도메인 이름을 반환합니다. 이름은 쉼표로 구분됩니다. 빈 문자열이 오류에 반환됩니다. |
| `get_primary_headnode` | 기본 헤드 노드의 정규화된 도메인 이름을 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_secondary_headnode` | 보조 헤드 노드의 정규화된 도메인 이름을 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_primary_headnode_number` | 기본 헤드 노드의 숫자 접미사를 가져옵니다. 빈 문자열이 오류에 반환됩니다. |
| `get_secondary_headnode_number` | 보조 헤드 노드의 숫자 접미사를 가져옵니다. 빈 문자열이 오류에 반환됩니다. |

## <a name="commonusage"></a>일반적인 사용 패턴

이 섹션에서는 사용자 고유의 사용자 지정 스크립트를 작성하는 동안 실행할 수 있는 일반적인 사용 패턴 중 일부를 구현하는 방법에 대한 지침을 제공합니다.

### 스크립트에 매개 변수 전달

경우에 따라 스크립트에 매개 변수가 필요할 수 있습니다. 예를 들어 Ambari REST API에서 정보를 검색하기 위해 클러스터에 대한 관리자 암호가 필요할 수 있습니다.

스크립트에 전달된 매개 변수는 _위치 매개 변수_로 알려져 있으며 첫 번째 매개 변수의 경우 `$1`, 두 번째는 `$2`에 할당됩니다. `$0`는 스크립트 자체의 이름을 포함합니다.

스크립트에 매개 변수로 전달된 값은 작은 따옴표(')로 묶어야 하므로 전달된 값은 리터럴로 처리되고 특별한 처리는 '!'와 같은 문자를 포함하도록 지정되지 않습니다.

### 환경 변수 설정

환경 변수의 설정은 다음으로 수행됩니다.

    VARIABLENAME=value

여기서 VARIABLENAME은 변수의 이름입니다. 이 변수에 액세스하려면 `$VARIABLENAME`를 사용합니다. 예를 들어 위치 매개 변수에서 제공하는 값을 PASSWORD라는 환경 변수로 할당하려면 다음을 사용합니다.

    PASSWORD=$1

이후 정보에 액세스하려면 `$PASSWORD`를 사용합니다.

스크립트 내에서 설정된 환경 변수는 스크립트의 범위 내에만 존재합니다. 경우에 따라 스크립트가 완료된 후에 유지되는 시스템 전체 범위의 환경 변수를 추가해야 할 수 있습니다. 그래서 일반적으로 SSH 통해 클러스터에 연결된 사용자는 스크립트에서 설치한 구성 요소를 사용할 수 있습니다. 환경 변수를 `/etc/environment`에 추가하여 이를 수행할 수 있습니다. 예를 들어 다음은 __HADOOP\_CONF\_DIR__를 추가합니다.

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### 사용자 지정 스크립트가 저장된 위치 액세스

클러스터를 사용자 지정하는 데 사용되는 스크립트는 클러스터의 기본 저장소 계정 또는 다른 모든 저장소 계정의 경우 공용 읽기 전용 컨테이너에 있어야 합니다. 또한 스크립트에서 다른 곳에 있는 리소스에 액세스할 경우 이러한 리소스는 공개적으로 액세스 가능(최소한 공용 읽기 전용)해야 합니다. 예를 들어 `download_file`을 사용하여 클러스터에 파일을 다운로드하려고 할 수 있습니다.

클러스터(예: 기본 저장소 계정)에서 액세스할 수 있는 Azure 저장소 계정에 파일을 저장하면 이 저장소가 Azure 네트워크 내에 있기 때문에 빠른 액세스를 제공합니다.

## <a name="deployScript"></a>스크립트 작업 배포를 위한 검사 목록

이러한 스크립트 배포를 준비할 때 수행하는 단계는 다음과 같습니다.

- 사용자 지정 스크립트가 포함된 파일을 배포 중 클러스터 노드에서 액세스할 수 있는 위치에 배치합니다. 기본 또는 클러스터 배포 시 지정된 추가 저장소 계정, 또는 공개적으로 액세스할 수 있는 저장소 컨테이너가 될 수 있습니다.

- 스크립트가 동일한 노드에서 여러 번 실행될 수 있도록 멱등원으로 실행되는지 확인하는 검사를 스크립트에 추가합니다.

- 임시 파일 디렉터리 /tmp를 사용하여 스크립트에서 사용되는 다운로드된 파일을 보관하고 스크립트가 실행된 후 이 파일을 정리합니다.

- OS 수준 설정이나 Hadoop 서비스 구성 파일이 변경된 경우에는 HDInsight 서비스에서 스크립트에 설정된 환경 변수와 같은 OS 수준 설정을 선택할 수 있도록 해당 서비스를 다시 시작할 수 있습니다.

## <a name="runScriptAction"></a>스크립트 작업을 실행하는 방법

스크립트 작업을 사용하여 Azure 포털, Azure PowerShell, ARM(Azure Resource Manager) 템플릿 또는 HDInsight .NET SDK로 HDInsight 클러스터를 사용자 지정할 수 있습니다. 자세한 내용은 [스크립트 작업 사용 방법](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

## <a name="sampleScripts"></a>사용자 지정 스크립트 샘플

Microsoft에서는 HDInsight 클러스터에 구성 요소를 설치하는 샘플 스크립트를 제공합니다. 예제 스크립트 및 사용하는 방법에 대한 지침은 다음 링크에서 찾을 수 있습니다.

- [HDInsight에서 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md)
- [HDInsight Hadoop 클러스터에 R 설치 및 사용](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight 클러스터에 Solr 설치 및 사용](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight 클러스터에 Giraph 설치 및 사용](hdinsight-hadoop-giraph-install-linux.md)

> [AZURE.NOTE] 위에 링크된 문서는 Linux 기반 HDInsight 클러스터에 한정됩니다. Windows 기반 HDInsight로 작동하는 스크립트는 [HDInsight를 사용하여 스크립트 작업 개발(Windows)](hdinsight-hadoop-script-actions.md)을 참조하거나 각 문서의 위쪽에 사용할 수 있는 링크를 사용합니다.

##문제 해결

다음은 개발한 스크립트를 사용할 때 발생할 수 있는 오류입니다.

__오류__: `$'\r': command not found`. 때로는 `syntax error: unexpected end of file`이 이어집니다.

_원인_:이 오류는 스크립트에서 줄이 CRLF로 끝날 때 발생합니다. Unix 시스템은 줄 끝에 LF이 필요합니다.

스크립트가 Windows 환경에서 작성된 경우 CRLF은 Windows의 많은 텍스트 편집기에서 줄 끝에 흔하게 쓰이기 때문에 이 문제가 가장 자주 발생합니다.

_해상도_: 텍스트 편집기에서 옵션인 경우 줄 끝에 LF 또는 Unix 형식을 선택합니다. 또한 Unix 시스템에서 다음 명령을 사용하여 CRLF는 LF로 변경할 수 있습니다.

> [AZURE.NOTE] 다음 명령은 CRLF 줄 끝을 LF으로 변경해야 하는 것과 거의 동일합니다. 시스템에서 사용할 수 있는 유틸리티에 따라 하나를 선택합니다.

| 명령 | 참고 사항 |
| ------- | ----- |
| `unix2dos -b INFILE` | 원본 파일이 BAK 확장으로 백업됩니다. |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE는 끝이 LF인 버전만 포함합니다. |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | 새 파일을 만들지 않고 직접 파일을 수정합니다. |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE는 끝이 LF인 버전만 포함합니다.

__오류__: `line 1: #!/usr/bin/env: No such file or directory`.

_원인_: 스크립트가 바이트 순서 표시(BOM)를 사용하여 UTF-8로 저장될 때 이 오류가 발생합니다.

_해상도_: ASCII로 또는 BOM을 사용하지 않고 UTF-8로 파일을 저장합니다. Linux 또는 Unix 시스템에서 다음 명령을 사용하여 BOM을 사용하지 않고 새 파일을 만들 수 있습니다.

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

위의 명령의 경우 BOM을 포함하는 파일로 __INFILE__을 대체합니다. __OUTFILE__은 BOM을 사용하지 않고 스크립트를 포함하는 새 파일 이름이어야 합니다.

## <a name="seeAlso"></a>다음 단계

* [스크립트 작업을 사용하여 HDInsight 클러스터를 사용자 지정하는 방법](hdinsight-hadoop-customize-cluster-linux.md)을 알아봅니다.

* [HDInsight.NET SDK 참조](https://msdn.microsoft.com/library/mt271028.aspx)를 사용하여 HDInsight를 관리하는 .NET 응용 프로그램을 만드는 방법을 알아봅니다.

* [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx)를 사용하여 REST를 통해 HDInsight 클러스터에서 관리 작업을 수행하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->