<properties
pageTitle="HDInsight로 Ambari Tez 뷰 사용 | Azure"
description="Ambari Tez 뷰를 사용하여 HDInsight에서 Tez 작업을 디버깅하는 방법을 알아봅니다."
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
ms.date="07/19/2016"
ms.author="larryfr"/>

# HDInsight에서 Ambari 뷰를 사용하여 Tez 작업 디버깅

HDInsight용 Ambari 웹 UI는 Tez를 실행 엔진으로 사용하는 작업을 이해하고 디버깅하는 데 사용될 수 있는 Tez 뷰를 포함합니다. Tez 뷰를 사용하면 연결된 항목의 그래프로 작업을 시각화하고 각 항목을 자세히 알아보며 통계 및 로깅 정보를 검색할 수 있습니다.

> [AZURE.NOTE] 이 문서에 있는 정보는 Linux 기반 HDInsight 클러스터에 지정됩니다. Windows 기반 HDInsight를 사용하여 Tez 작업을 디버깅하는 방법에 대한 자세한 내용은 [Windows 기반 HDInsight에서 Tez UI를 사용하여 Tez 작업 디버깅](hdinsight-debug-tez-ui.md)을 참조하세요.

##필수 조건

* Linux 기반 HDInsight 클러스터입니다. 새 클러스터를 만드는 단계는 [Linux 기반 HDInsight 사용 시작](hdinsight-hadoop-linux-tutorial-get-started.md)을 참조하세요.

* HTML5를 지원하는 최신 웹 브라우저

##Tez 이해

Tez는 기존의 MapReduce 처리보다 빠른 속도를 제공하는 Hadoop의 데이터 처리에 대해 확장 가능한 프레임워크입니다. Linux 기반 HDInsight 클러스터에서는 Hive에 대한 기본 엔진입니다.

작업을 Tez에 제출하는 경우 작업에 필요한 동작의 실행 순서를 설명하는 DAG(방향성 비순환 그래프)를 만듭니다. 개별 동작은 꼭짓점을 호출하고 전체 작업의 일부를 실행합니다. 꼭짓점에서 설명하는 작업의 실제 실행을 태스크라고 하며 클러스터의 여러 노드에 분산될 수 있습니다.

###Tez 뷰 이해

Tez 뷰는 실행 중이거나 Tez를 사용하여 이전에 실행된 프로세스에 대한 정보를 제공합니다. Tez에 의해 생성된 DAG를 볼 수 있고 태스크 및 꼭짓점에서 사용된 메모리와 오류 정보와 같은 카운터인 클러스터에 배포되는 방법입니다. 다음과 같은 시나리오에서 유용한 정보를 제공할 수 있습니다.

* 장기 실행 처리를 모니터링하고 맵의 진행 상황을 보며 태스크를 줄입니다.

* 처리를 향상시킬 방법 또는 실패한 이유를 알아보기 위해 성공 또는 실패한 처리에 대한 기록 데이터를 분석합니다.

##DAG 생성

Tez 뷰는 Tez 엔진을 사용하는 작업이 현재 실행되거나 이전에 실행된 경우에만 데이터를 포함합니다. 간단한 Hive 쿼리는 Tez를 사용하지 않고 해결될 수 있지만 필터링, 그룹화, 정렬, 조인 등을 수행하는 복잡한 쿼리는 Tez가 필요합니다.

Tez를 사용하여 실행하는 Hive 쿼리를 실행하기 위해 다음 단계를 사용합니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net로 이동합니다. 여기서 __CLUSTERNAME__은 HDInsight 클러스터의 이름입니다.

2. 페이지 위쪽에 있는 메뉴에서 __보기__ 아이콘을 선택합니다. 일련의 사각형처럼 보입니다. 나타나는 드롭다운에서 __Hive 보기__를 선택합니다.

    ![Hive 뷰 선택](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Hive 보기가 로드될 때 다음을 쿼리 편집기에 붙여넣은 다음 __실행__을 클릭합니다.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    작업이 완료되면 __쿼리 프로세스 결과__ 섹션에 표시된 출력이 표시되어야 합니다. 결과는 다음과 유사합니다.
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. __로그__ 탭을 선택합니다. 다음과 유사한 정보가 표시됩니다.
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    이 다음 섹션에서 사용되는 것처럼 __앱 ID__ 값을 저장합니다.

##Tez 뷰 사용

1. 페이지 위쪽에 있는 메뉴에서 __보기__ 아이콘을 선택합니다. 나타나는 드롭다운에서 __Tez 뷰__를 선택합니다.

    ![Tez 뷰 선택](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Tez 뷰가 로드되면 클러스터에서 현재 실행 중이거나 실행된 DAG의 목록이 표시됩니다. 기본 보기는 DAG 이름, ID, 제출자, 상태, 시작 시간, 종료 시간, 기간, 응용 프로그램 ID 및 큐를 포함합니다. 페이지의 오른쪽에 있는 기어 아이콘을 사용하여 많은 열을 추가할 수 있습니다.

    ![모든 DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. 항목이 하나만 있으면 이전 섹션에서 실행한 쿼리에 대한 것입니다. 여러 항목이 있는 경우 __응용 프로그램 ID__ 필드에 응용 프로그램 ID를 입력하여 검색한 다음 엔터를 누를 수 있습니다.

4. __DAG 이름__을 선택합니다. DAG에 대한 정보 뿐만 아니라 DAG에 대한 정보가 포함된 JSON 파일의 zip을 다운로드할 수 있는 옵션이 표시됩니다.

    ![DAG 세부 정보](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. 위의 __DAG 세부 정보__는 DAG에 대한 정보를 표시하는 데 사용될 수 있는 여러 링크입니다.

    * __DAG 카운터__는 이 DAG에 대한 카운터 정보를 표시합니다.
    
    * __그래픽 보기__는 이 DAG의 그래픽 표시를 나타냅니다.
    
    * __모든 꼭짓점__은 이 DAG의 꼭짓점의 목록을 표시합니다.
    
    * __모든 태스크__는 이 DAG에서 꼭짓점에 대한 태스크의 목록을 표시합니다.
    
    * __모든 TaskAttempts__는 이 DAG에 태스크를 실행하려는 시도에 대한 정보를 표시합니다.
    
    > [AZURE.NOTE] 꼭짓점, 태스크 및 TaskAttempts에 대한 열 표시를 스크롤하는 경우 각각의 행에 대한 __카운터__ 및 __로그 보기 또는 다운로드__를 보는 링크가 있습니다.

    작업에 오류가 발생한 경우 DAG 세부 정보는 실패한 태스크에 대한 정보의 링크와 함께 실패 상태가 표시됩니다. 진단 정보는 DAG 세부 정보 아래에 표시됩니다.
    
    ![오류를 자세히 설명하는 DAG 세부 정보 화면](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. __그래픽 보기__를 선택합니다. 이 DAG의 그래픽 표시를 나타냅니다. 보기에서 각 꼭짓점 위로 마우스를 두어 그에 대한 정보를 표시할 수 있습니다.

    ![그래픽 보기](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. 꼭짓점을 클릭하면 해당 항목에 대한 __꼭짓점 세부 정보__를 로드합니다. __맵 1__ 꼭짓점을 클릭하면 이 항목에 대한 세부 정보를 표시합니다.

    ![꼭짓점 세부 정보](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. 이제 꼭짓점 및 태스크에 관련된 페이지의 위쪽에 링크가 있습니다.

    > [AZURE.NOTE] 또한 __DAG 세부 정보__로 다시 이동하고 __꼭짓점 세부 정보__를 선택한 다음 __맵 1__ 꼭짓점을 선택하여 이 페이지로 이동할 수 있습니다

    * __꼭짓점 카운터__는 이 꼭짓점에 대한 카운터 정보를 표시합니다.
    
    * __태스크__은 이 꼭짓점에 대한 태스크를 표시합니다.
    
    * __태스크 시도__는 이 꼭짓점에 태스크를 실행하려는 시도에 대한 정보를 표시합니다.
    
    * __원본 및 싱크__는 이 꼭짓점에 대한 데이터 원본 및 싱크를 표시합니다.

    > [AZURE.NOTE] 이전 메뉴 작업으로 태스크, 태스크 시도, 원본 및 싱크\_\_에 대한 열 표시를 스크롤하여 각 항목에 대한 자세한 정보의 링크를 표시할 수 있습니다.

10. __태스크__를 선택하고 __00_000000_\_라는 항목을 선택합니다. 이 태스크에 대한 __태스크 세부 정보__를 표시합니다. 이 화면에서 __태스크 카운터__ 및 __태스크 시도\_\_를 볼 수 있습니다.

    ![태스크 세부 정보](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##다음 단계

이제 Tez 뷰를 사용하는 방법을 배웠으므로 [HDInsight에서 Hive 사용](hdinsight-use-hive.md)에 대해 자세히 알아봅니다.

Tez에서 자세한 기술 정보는 [Hortonworks의 Tez 페이지](http://hortonworks.com/hadoop/tez/)를 참조하세요.

HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->