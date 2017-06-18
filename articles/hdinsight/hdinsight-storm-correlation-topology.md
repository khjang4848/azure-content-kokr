<properties
 pageTitle="HDInsight에서 Storm 및 HBase를 사용하여 시간 별로 이벤트의 상관 관계 지정"
 description="HDInsight에서 Storm 및 HBase를 사용함으로써 다른 시간에 도착하는 이벤트의 상관관계를 지정하는 방법에 대해 알아봅니다."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/05/2016"
 ms.author="larryfr"/>

# HDInsight에서 Storm 및 HBase를 사용하여 시간 별로 이벤트의 상관 관계 지정

Apache Storm으로 영구적인 데이터 저장소를 사용하여 다른 시간에 도착하는 데이터 항목의 상관 관계를 지정할 수 있습니다. 예를 들어, 사용자 세션에 대한 로그인 및 로그아웃 이벤트를 연결하여 세션이 얼마나 지속되었는지 계산할 수 있습니다.

이 문서에서는 사용자 세션에 대한 로그인 및 로그아웃 이벤트를 추적하는 기본 C# 스톰 토폴로지를 만드는 방법을 배우고 세션의 지속 시간을 계산합니다. 토폴로지는 영구 데이터 저장소로 HBase를 사용합니다. HBase는 또한 기록 데이터에 대해 배치 쿼리를 수행시켜서 특정 기간에 얼마나 많은 사용자 세션이 시작 또는 종료했는지와 같은 추가적인 통찰력을 생성해줍니다.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## 필수 조건

-	Visual Studio 및 HDInsight Tools for Visual Studio: 설치 정보는 [HDInsight Tools for Visual Studio 사용 시작](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)을 참조하세요.

-	HDInsight 클러스터의 Apache Storm(Windows 기반). 들어오는 데이터를 처리하고 HBase에 저장하는 Storm 토폴로지를 실행합니다.

-	HDInsight 클러스터의 Apache HBase(Windows 또는 Linux 기반). 이 예제의 데이터 저장소입니다.

## 아키텍처

![토폴로지를 통한 데이터 흐름 다이어그램](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

이벤트의 상관 관계를 지정하려면 이벤트 원본에 대한 공통 식별자가 필요합니다. 예를 들어 사용자 ID, 세션 ID 또는 a) 고유하고 b) 스톰으로 전송된 모든 데이터에 포함된 데이터의 다른 부분이 있습니다. 이 예제는 GUID 값을 사용하여 세션 ID를 나타냅니다.

이 예제는 다음 두 개의 HDInsight 클러스터로 이루어져 있습니다.

-	HBase: 기록 데이터에 대한 영구 데이터 저장소

-	Storm: 들어오는 데이터를 수집하는 데 사용

데이터는 Storm 토폴로지에 의해 임의로 생성되고 다음 항목으로 구성됩니다.

-	세션 ID: 각 세션을 고유하게 식별하는 GUID

-	이벤트: 이벤트 시작(START) 또는 종료(END) 이 예에서는 START가 항상 END 전에 발생합니다.

-	시간: 이벤트의 시간입니다.

이 데이터는 HBase에서 처리 및 저장됩니다.

### Storm 토폴로지

세션이 시작되면 **START** 이벤트가 토폴로지에서 수신되고 HBase에 로그됩니다. **END** 이벤트가 수신되면 토폴로지가 **START** 이벤트를 검색하며 두 이벤트 사이에서 시간을 계산합니다. 그 다음 이 **기간** 값이 **END** 이벤트 정보와 함께 HBase에 저장됩니다.

> [AZURE.IMPORTANT] 이 토폴로지는 기본 패턴을 보여주며 프로덕션 솔루션은 다음과 같은 시나리오에 대한 디자인을 가져와야 합니다.
>
> - 이벤트가 잘못된 순서로 도착
> - 중복된 이벤트
> - 삭제된 이벤트

샘플 토폴로지는 다음 구성 요소로 구성됩니다.

- 	Session.cs: 임의 세션 ID, 시작 시간 및 세션이 지속되는 시간을 생성하여 사용자 세션을 시뮬레이션

- 	Spout.cs: 100개의 세션을 만들고 START 이벤트를 내보내고 각 세션에 대한 임의 시간 제한을 대기하고 END 이벤트를 내보냅니다. 그런 다음 종료한 세션을 재순환하여 새로운 세션을 생성합니다.

-	HBaseLookupBolt.cs: 세션 ID를 사용하여 HBase에서 세션 정보를 조회합니다. END 이벤트가 처리되면 해당되는 START 이벤트를 찾아서 세션의 소요 시간을 계산합니다.

-	HBaseBolt.cs: HBase에 정보를 저장합니다.

-	TypeHelper.cs: HBase에서 읽기/쓰기할 때 형식 변환을 지원합니다.

### HBase 스키마

HBase에서 데이터가 다음 스키마/설정을 사용 하여 테이블에 저장됩니다.

-	행 키: 이 테이블에서 세션 ID가 행에 대한 키로 사용됩니다.

-	열 제품군: 제품군 이름은 'cf'입니다. 이 제품군에 저장되는 열은:

	- 	이벤트: START 또는 END

	- 	시간: 이벤트가 발생한 시간(밀리초)

	-	소요 시간: 이벤트 START와 END 사이의 길이

-	버전: 'cf' 제품군은 각 행의 5가지 버전을 유지하도록 설정됩니다.

	> [AZURE.NOTE] 버전은 특정 행의 키에 대해 저장된 이전 값의 로그입니다. 기본적으로 HBase는 행의 가장 최근 버전에 대한 값만 반환합니다. 이 경우 같은 행은 모든 이벤트(START, END)에 대해 사용됩니다. 행의 각 버전은 타임 스탬프 값에 의해 식별됩니다. 이렇게 하면 특정 ID에 대해 기록된 이벤트의 기록 보기가 제공됩니다.

## 프로젝트를 다운로드합니다.

샘플 프로젝트는 [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation)에서 다운로드할 수 있습니다.

이 다운로드에는 다음 C# 프로젝트가 포함됩니다.

-	CorrelationTopology: 사용자 세션에 대해 시작 및 종료 이벤트를 임의로 내보내는 C# Storm 토폴로지입니다. 각 세션은 1분에서 5분 사이까지 지속됩니다.

-	SessionInfo: HBase 테이블을 생성하고 저장된 세션 데이터에 대한 정보를 반환하는 예제 쿼리를 제공하는 C# 콘솔 응용 프로그램입니다.

## 테이블 만들기

1. Visual Studio에서 **SessionInfo** 프로젝트를 엽니다.

2. **솔루션 탐색기**에서 **SessionInfo** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

	![선택한 속성을 가진 메뉴의 스크린샷](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. **설정**을 선택하고 다음 값을 설정합니다.

	- 	HBaseClusterURL: HBase 클러스터에 대한 URL입니다. 위치(예:https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: 클러스터에 대한 관리/HTTP 사용자 계정

	- 	HBaseClusterPassword: 관리/HTTP 사용자 계정에 대한 암호

	- 	HBaseTableName: 이 예제와 함께 사용하기 위한 테이블 이름

	-	HBaseTableColumnFamily: 열 패밀리 이름

	![설정 대화 상자 이미지](./media/hdinsight-storm-correlation-topology/settings.png)

5. 솔루션을 실행합니다. 메시지가 표시되면 'c' 키를 선택하여 HBase 클러스터에 테이블을 생성합니다.

## Storm 토폴로지 빌드 및 배포

1.	Visual Studio에서 **CorrelationTopology** 솔루션을 엽니다.

2.	**솔루션 탐색기**에서 **CorrelationTopology** 프로젝트를 마우스 오른쪽 단추로 클릭하고 속성을 선택합니다.

3.	속성 창에서 **설정**을 선택하고 다음 정보를 제공합니다. 처음 5개는 **SessionInfo** 프로젝트에서 사용되는 동일한 값이어야 합니다.

	- 	HBaseClusterURL: HBase 클러스터에 대한 URL입니다. 위치(예:https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName: 클러스터에 대한 관리/HTTP 사용자 계정

	- 	HBaseClusterPassword: 관리/HTTP 사용자 계정에 대한 암호

	- 	HBaseTableName: 이 예제와 함께 사용하기 위한 테이블 이름. 여기에는 SessionInfo 프로젝트에 사용되는 것과 동일한 테이블 이름이 포함되어야 합니다.

	- 	HBaseTableColumnFamily: 열 제품군 이름. 여기에는 SessionInfo 프로젝트에 사용되는 것과 동일한 열 제품군 이름이 포함되어야 합니다.

	> [AZURE.IMPORTANT] 기본값이 **SessionInfo**에서 데이터 검색에 사용되는 이름이므로 HBaseTableColumnNames를 변경하지 마십시오.

4.  속성을 저장한 다음 프로젝트를 빌드합니다.

5.	**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다. 메시지가 표시되면 Azure 구독에 대한 자격 증명을 입력합니다.

	![Storm 메뉴 항목에 제출 이미지](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.	**토폴로지 제출** 대화 상자에서 이 토폴로지를 실행할 Storm 클러스터를 선택합니다.

	> [AZURE.NOTE] 처음으로 토폴로지를 제출하면 HDInsight 클러스터의 이름을 검색하는 데 몇 초가 걸릴 수도 있습니다.

7.	토폴로지가 업로드되고 클러스터에 제출되면 **Storm 토폴로지 보기**가 실행 중인 토폴로지를 열고 표시합니다. **CorrelationTopology**를 선택하고 페이지의 상단 오른쪽에 있는 새로 고침 단추를 사용하여 토폴로지 정보를 새로 고칩니다.

	![토폴로지 보기 이미지](./media/hdinsight-storm-correlation-topology/topologyview.png)

	토폴로지가 데이터의 생성을 시작하면 **내보낸** 열의 값이 증가합니다.

	> [AZURE.NOTE] **Storm 토폴로지 보기**가 자동으로 열리지 않으면 다음 단계를 이용하여 여십시오.
	>
	> 1. **솔루션 탐색기**에서 **Azure**를 확장하고 **HDInsight**를 확장합니다.
	>
	> 2. 토폴로지가 실행되는 Storm 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **Storm 토폴로지 보기**를 선택합니다.

## 데이터 쿼리

데이터를 내보낸 후 다음 단계를 사용하여 데이터를 쿼리합니다.

1. **SessionInfo** 프로젝트로 돌아갑니다. 실행되고 있지 않다면 새로운 인스턴스를 시작합니다.

2. 메시지가 표시되면 **s**를 선택하여 START 이벤트를 검색합니다. 시간 범위를 정의하기 위해 시작 및 종료 시간을 입력하라는 메시지가 표시됩니다. 이 두 시간 사이의 이벤트만 반환됩니다.

	시작 및 종료 시간 입력 시 다음 형식을 사용합니다. HH:MM 및 'am' 또는 'pm'. 예: 11:20pm.

	토폴로지가 방금 시작되었으므로 배포되기 전의 시작 시간과 현재의 종료 시간을 사용합니다. 시작 시 생성되었던 START 이벤트의 대부분이 캡처되어야 합니다. 쿼리를 실행할 때 다음과 유사한 항목의 목록이 표시됩니다.

		Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

END 이벤트의 검색은 START 이벤트의 검색과 동일하게 동작합니다. 하지만, END 이벤트는 START 이벤트 후 1분과 5분 사이에 임의로 생성됩니다. 따라서 END 이벤트를 찾기 위해서는 범위를 몇 차례 시도해야 할 수 있습니다. END 이벤트에는 또한 START 이벤트 시간과 END 이벤트 시간 사이의 차이인 세션의 소요 시간도 포함됩니다. 다음은 END 이벤트에 대한 데이터 예제입니다.

	Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] 입력한 시간 값은 현지 시간이지만 쿼리로부터 반환된 시간은 UTC입니다.

##토폴로지 중지

토폴로지의 중지가 준비되면 Visual Studio에서 **CorrelationTopology** 프로젝트로 돌아갑니다. **Storm 토폴로지 보기**에서 토폴로지를 선택하고 토폴로지의 상단에 있는 **Kill** 단추를 사용합니다.

##클러스터 삭제

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##다음 단계

더 많은 Storm 예제는 [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)를 참조하십시오.
 

<!---HONumber=AcomDC_0914_2016-->