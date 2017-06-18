<properties
	pageTitle="분석 플랫폼: Apache Storm과 스트림 분석 비교 | Microsoft Azure"
	description="Apache Storm과 Azure 스트림 분석 비교를 사용하여 클라우드 분석 플랫폼 선택에 대한 지침을 확인하세요. 기능 및 차이점을 이해하세요."
	keywords="분석 플랫폼, 클라우드 분석 플랫폼, storm 비교"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>

# 스트리밍 분석 플랫폼 선택 도움말: Apache Storm과 Azure 스트림 분석 비교

Apache Storm과 Azure 스트림 분석 비교를 사용하여 클라우드 분석 플랫폼 선택에 대한 참고 자료를 확인하세요. Azure HDInsight 기반의 관리 서비스로써 스트림 분석과 Apache Storm이 제공하는 가치 제안을 이해하면 비즈니스에 적합한 솔루션을 선택할 수 있습니다.

두 분석 플랫폼 모두 PaaS 솔루션의 이점을 제공하지만, 서로 구분되는 몇 가지 고유한 기능이 있습니다. 목표를 달성하는 데 필요한 솔루션을 올바르게 선택할 수 있도록 아래에 이러한 서비스의 기능과 제한 사항이 설명되어 있습니다.

## Storm과 스트림 분석 비교: 일반 기능 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 스트림 분석</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight의 Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>공개 소스</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    아니요, Azure 스트림 분석은 Microsoft 등록 제품입니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    예, Apache Storm은 Apache 라이센스가 허가된 기술입니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft 지원</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    예
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    예
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>하드웨어 요구 사항</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    하드웨어 요구 사항이 없습니다. Azure 스트림 분석은  인스턴스 Azure 서비스 입니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    하드웨어 요구 사항이 없습니다. Apache Storm은 Azure 서비스입니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>최상위 수준 단위</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Azure 스트림 분석을 사용하여 고객은 스트리밍 작업을  배포하고 모니터링합니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    HDInsight의 Apache Storm을 사용하여 고객은 다중  Storm 작업과 다른 작업(일괄 처리 포함)을  호스트 할 수 있는 전체 클러스터를 배포하고 모니터링 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>가격</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    스트림 분석은 처리되는 데이터 볼륨 및 스트리밍 단위(작업이 실행되는 시간 당)의 수에 따라 가격이 책정됩니다.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">자세한 가격 정보는 여기를 참조하세요.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    HDInsight의 Apache Storm의 경우 구매 단위는 클러스터 기반이며 클러스터 실행 되는 시간, 독립적으로 배포 하는 작업을 기준으로 요금이 청구 됩니다.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">자세한 가격 정보는 여기를 참조하세요.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 각 분석 플랫폼에서 작성 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 스트림 분석</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight의 Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>기능: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    예, 사용이 편리한 SQL 언어가 지원됩니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    아니요, 사용자는 Java C#으로 코드를 작성하거나 Trident Api를 사용해야 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>기능: 임시 연산자</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    기간 이동 집계 및 임시 조인이 기본적으로 지원 됩니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    임시 연산자는 사용자가 구현해야 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>개발 실험</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    예제 데이터에서 Azure 포털을 통해 대화형으로 작성 및 디버깅을 경험합니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    NET 사용자용 Visual Studio를 통해 개발, 디버깅 및 모니터링 환경이 제공 되며 Java 및 기타 언어 개발자의 경우에는 자신이 선택한 IDE를 사용할 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>디버깅 지원</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    스트림 분석은 디버깅 방식으로 기본 작업 상태 작업 로그를 제공하지만 예를 들어 자세한 정보 표시 모드로그에 무엇이/얼마나 포함되는지에 대한 유연성을 제공하지 않습니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    자세한 로그는 디버깅을 위해 사용할 수 있습니다. 사용자에게 로그를 노출하는 두 가지 방법: visual Studio을 통해 또는 로그에 액세스 하려면 클러스터에 사용자가 RDP할 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>UDF 지원 (사용자 정의 함수)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    현재 UDF에 대한 지원은 없습니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    C#, Java 또는 사용자가 선택한 언어로 UDF를 작성할 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>확장 가능한-사용자 지정 코드 </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    스트림 분석에 확장 가능한 코드에 대한 지원은 없습니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    예, Storm C#, Java 또는 기타 지원 되는 언어로 사용자 지정 코드를 작성하는 가용성이 있습니다.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 데이터 원본 및 출력 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 스트림 분석</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight의 Apache Storm</strong>
                </p>
            </td>
        </tr>
		<tr>
            <td width="174" valign="top">
				<p>
				 <strong> 입력 데이터 원본</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>지원되는 입력 원본은 Azure 이벤트 허브 및 Azure Blob입니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    이벤트 허브, 서비스 버스, Kafka 등에 사용할 수 있는 커넥터가 있습니다. 지원되지 않는 커넥터는 사용자 지정 코드를 통해 구현될 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> 입력 데이터 형식</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    지원되는 입력 형식은 Avro, JSON, CSV입니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    모든 형식은 사용자 지정 코드를 통해 구현될 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>출력</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    스트리밍 작업은 여러 출력일 수 있습니다. 지원되는 출력: Azure 이벤트 허브, Azure Blob 저장소, Azure 테이블, Azure SQL DB 및 PowerBI
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    토폴로지의 많은 출력을 지원하여 각 출력에는 다운스트림 처리에 대한 사용자 지정 논리가 있을 수 있습니다. 기본적으로 Storm은 PowerBI, Azure 이벤트 허브, Azure Blob 저장소, Azure DocumentDB, SQL 및 HBase에 대한 커넥터를 포함합니다. 지원되지 않는 커넥터는 사용자 지정 코드를 통해 구현될 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>데이터 인코딩 형식</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    스트림 분석에는 UTF-8 데이터 형식 을 사용해야 합니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    모든 인코딩 형식은 사용자 지정 코드를 통해 구현될 수 있습니다.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 관리 및 운영 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 스트림 분석</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight의 Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>작업 배포 모델</strong>
                </p>
                <p>
                    - <strong>Azure 포털</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>Powershell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    배포는 Azure 포털, PowerShell 및 REST API를 통해 구현됩니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    배포는 Azure 포털, PowerShell, Visual Studi 및 REST API를 통해 구현됩니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>(통계, 카운터 등)를 모니터링합니다.</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    모니터링은 Azure 포털 및 REST API를 통해 구현됩니다.
                </p>
                <p>
                    사용자 Azure 경고를 구성할 수도 있습니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    모니터링은 Storm UI 및 REST API를 통해 구현됩니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>확장성</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    각 작업에 대한 스트리밍 단위 개수. 각 스트리밍은 최대 1MB/s를 처리합니다. 기본적으로 최대 단위는 50입니다. 제한을 늘리려면 호출하세요.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    HDI Storm 클러스터의 노드 수 노드의 수에 제한이 없습니다.(최고 제한은 사용자의 Azure 할당량으로 정의)  제한을 늘리려면 호출하세요.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>데이터 처리 제한</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    사용자는 데이터 처리를 늘리거나 비용 최적화를 위해 스트리밍 단위 수를 확장 또는 축소 할 수 있습니다.
                </p>
                <p>
                    크기는 최대 1 GB/s 까지 조정됩니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    사용자는 요구에 적합하도록 클러스터 크기를 확장 또는 축소할 수 있습니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>중지/다시 시작</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    중지한 후 중지된 마지막 위치에서 다시 시작 합니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    중지한 후 워터마크에 따라 중지된 마지막 위치에서 다시 시작 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>서비스 및 프레임 워크 업데이트</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    가동 중지 시간 없이 자동 패치
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    가동 중지 시간 없이 자동 패치
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>보장된 SLA와 고가용 서비스를 통한 비즈니스 연속성</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    SLA 가동 시간 99.9%
                </p>
                <p>
                    오류에서 자동 복구
                </p>
                <p>
                    임시 상태 저장 연산자의 복구는 기본으로 제공합니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    스톰 클러스터의 SLA 가동 시간 99.9% Apache Storm 내결함성 스트리밍 플랫폼이지만, 스트리밍 작업이 중단 없이 실행 되도록 하는 것은 고객의 책임입니다.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## 고급 기능 ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 스트림 분석</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>HDInsight의 Apache Storm</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>지연 도착 및 순서가 벗어난 이벤트 처리</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    기본 구성가능 정책으로 순서 조정, 이벤트 삭제 또는 이벤트 시간 조정
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    사용자는 이 시나리오를 처리하기 위한 논리를 구현해야 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>참조 데이터</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    메모리 내 조회 캐시의 100MB의 최대 크기로 Azure Blob에서 참조 데이터를 사용할 수 있습니다. 참조 데이터 새로 고침은 서비스에 의해 관리됩니다.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    데이터 크기에 제한이 없습니다. 커넥터를 HBase, DocumentDB, SQL Server 및 Azure에 사용할 수 있습니다. 지원되지 않는 커넥터는 사용자 지정 코드를 통해 구현될 수 있습니다.
                </p>
                <p>
                    참조 데이터 새로 고침은 사용자 지정 코드로 처리해야 합니다.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>기계 학습과 통합</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    ASA 작업 생성 중 기능으로 게시된 Azure 기계 학습을 구성하여 수행합니다<a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(비공개 미리 보기)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Storm Bolt를 통해 사용할 수 있습니다.
                </p>
            </td>
        </tr>
    </tbody>
</table>

<!---HONumber=AcomDC_0914_2016-->