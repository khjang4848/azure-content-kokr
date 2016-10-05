<properties
pageTitle="HDInsight 클러스터를 만드는 동안 Hive 라이브러리 추가| Azure"
description="클러스터를 만드는 동안 HDInsight 클러스터에 Hive 라이브러리(jar 파일)를 추가하는 방법을 알아봅니다."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#HDInsight 클러스터를 만드는 동안 Hive 라이브러리 추가

HDInsight에서 Hive와 함께 자주 사용하는 라이브러리가 있는 경우 이 문서는 클러스터를 만드는 동안 스크립트 작업을 사용하여 라이브러리를 사전 로드하는 정보를 포함합니다. 그러면 라이브러리를 Hive에서 전역적으로 사용할 수 있습니다(로드하기 위해 [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli)을 사용할 필요 없음).

##작동 방법

클러스터를 만들 때 생성된 클러스터 노드에서 스크립트를 실행하는 스크립트 작업을 선택적으로 지정할 수 있습니다. 이 문서의 스크립트는 단일 매개 변수를 수락하며 이는 미리 로드된 라이브러리(jar 파일로 저장됨)가 포함 WASB 위치입니다.

클러스터를 만들 때 스크립트는 파일을 열거하고 헤드 및 작업자 노드의 `/usr/lib/customhivelibs/` 디렉터리에 복사한 다음 `core-site.xml` 파일의 `hive.aux.jars.path` 속성에 추가합니다. 또한 Linux 기반 클러스터에서 파일의 위치로 `hive-env.sh` 파일을 업데이트합니다.

> [AZURE.NOTE] 이 문서의 스크립트 작업을 사용하여 다음과 같은 시나리오에서 라이브러리를 사용할 수 있도록 합니다.
>
> * __Linux 기반 HDInsight__ - __Hive 명령줄__, __WebHCat__(Templeton) 및 __HiveServer2__를 사용하는 경우입니다.
> * __Windows 기반 HDInsight__ - __Hive 명령줄__ 및 __WebHCat__(Templeton)을 사용하는 경우입니다.

##스크립트

__스크립트 위치__

__Linux 기반 클러스터__의 경우: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

__Windows 기반 클러스터__의 경우: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__요구 사항__

* 스크립트는 __헤드 노드__ 및 __작업자 노드__ 모두에 적용해야 합니다.

* 설치하려는 jar을 __단일 컨테이너__에 있는 Azure Blob 저장소에 저장해야 합니다.

* 만드는 동안 jar 파일의 라이브러리를 포함하는 저장소 계정을 HDInsight 클러스터에 연결__해야__ 합니다. 다음 두 가지 방법 중 하나를 사용하여 수행할 수 있습니다.

    * 클러스터에 대한 기본 저장소 계정에 있는 컨테이너에 둡니다.
    
    * 연결된 저장소 컨테이너에 있는 컨테이너에 둡니다. 예를 들어 포털에서 __옵션 구성__, __연결된 저장소 계정__을 사용하여 추가 저장소를 추가할 수 있습니다.

* 컨테이너에 대한 WASB 경로를 스크립트 작업에 대한 매개 변수로 지정해야 합니다. 예를 들어 jar이 __mystorage__ 저장소 계정에 있는 __libs__ 컨테이너에 저장되는 경우 매개 변수는 \_\_wasbs://libs@mystorage.blob.core.windows.net/__입니다.

    > [AZURE.NOTE] 이 문서는 이미 저장소 계정, Blob 컨테이너를 만들고 거기에 파일을 업로드했다고 가정합니다.
    >
    > 저장소 계정을 만들지 않은 경우 [Azure 포털](https://portal.azure.com)을 통해 수행할 수 있습니다. [Azure 저장소 탐색기](http://storageexplorer.com/)와 같은 유틸리티를 사용하여 계정에 새 컨테이너를 만들고 파일을 업로드할 수 있습니다.

##스크립트를 사용하여 클러스터 만들기

> [AZURE.NOTE] 다음 단계는 새 Linux 기반 HDInsight 클러스터를 만듭니다. 새 Windows 기반 클러스터를 만들려면 클러스터를 만들 때 __Windows__를 클러스터 OS로 선택하고 bash 스크립트 대신 Windows(PowerShell) 스크립트를 사용합니다.
> 
> 또한 이 스크립트를 사용하여 클러스터를 만드는 데 Azure PowerShell 또는 HDInsight.NET SDK를 사용할 수도 있습니다. 이 방법을 사용하는 자세한 내용은 [스크립트 동작을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

1. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)의 단계를 사용하여 클러스터를 프로비전하기 시작하지만 프로비전을 완료하지 마세요.

2. **선택적 구성** 블레이드에서 **스크립트 동작**을 선택하고 아래와 같은 정보를 제공합니다.

    * __이름__: 스크립트 동작의 이름을 입력합니다.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __HEAD__: 이 옵션 선택
    * __작업자__: 이 옵션을 선택합니다.
    * __ZOOKEEPER__: 비워둡니다.
    * __매개 변수__: jar을 포함하는 컨테이너 및 저장소 계정에 WASB 주소를 입력합니다. 예: \_\_wasbs://libs@mystorage.blob.core.windows.net/__.

3. **스크립트 동작**의 아래 쪽에서 **선택** 단추를 사용하여 구성을 저장합니다.

4. **옵션 구성** 블레이드에서 __연결된 저장소 계정__을 선택하고 __저장소 키 추가__ 링크를 선택합니다. jar을 포함하는 저장소 계정을 선택한 다음 __선택__ 단추를 사용하여 설정을 저장하고 __옵션 구성__ 블레이드를 반환합니다.

5. **선택적 구성** 블레이드의 아래쪽에서 **선택** 단추를 사용하여 선택적 구성 정보를 저장합니다.

6. [Linux에서 HDInsight 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md#portal)에 설명된 대로 클러스터를 계속 프로비전합니다.

클러스터 생성이 완료되면 `ADD JAR` 문을 사용하지 않고도 Hive에서 이 스크립트를 통해 추가된 jar을 사용할 수 있습니다.

##다음 단계

이 문서에서는 클러스터를 만드는 동안 jar 파일에 포함된 Hive 라이브러리를 HDInsight 클러스터에 추가하는 방법을 알아보았습니다. Hive로 작업하는 방법에 대한 자세한 내용은 [HDInsight로 Hive 사용](hdinsight-use-hive.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->