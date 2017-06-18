
<properties
   pageTitle="응용 프로그램 업그레이드: 업그레이드 매개 변수 | Microsoft Azure"
   description="수행할 상태 확인 및 업그레이드를 자동으로 실행 취소하는 정책 등을 포함하여 서비스 패브릭 응용 프로그램 업그레이드와 관련한 매개 변수를 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# 응용 프로그램 업그레이드 매개 변수

이 문서에서는 Azure 서비스 패브릭 응용 프로그램을 업그레이드하는 동안 적용되는 다양한 매개 변수를 설명합니다. 이 매개 변수는 응용 프로그램의 이름 및 버전을 포함합니다. 업그레이드하는 동안 적용되고 업그레이드에 실패하면 반드시 적용해야 하는 정책을 지정하는 시간 제한 및 상태 확인을 제어하는 노브입니다.


<br>

| 매개 변수 | 설명 |
| --- | --- |
| ApplicationName | 업그레이드될 응용 프로그램의 이름입니다. 예: fabric:/VisualObjects, fabric:/ClusterMonitor |
| TargetApplicationTypeVersion | 업그레이드 대상인 응용 프로그램 형식의 버전입니다. |
| FailureAction | 업그레이드가 실패한 경우 서비스 패브릭이 수행하는 동작입니다. 응용 프로그램이 업데이트 전 버전(롤백)으로 롤백되거나 현재 업그레이드 도메인에서 업그레이드가 중지될 수 있습니다. 후자의 경우 업그레이드 모드도 수동으로 변경됩니다. 허용되는 값은 Rollback 및 Manual입니다. |
| HealthCheckWaitDurationSec | 업그레이드 후 서비스 패브릭이 응용 프로그램의 상태를 평가하기 전에 업그레이드 도메인에서 완료되는 대기 시간(초)입니다. 이 소요 시간은 정상 상태로 간주되기 전에 응용 프로그램을 실행해야 하는 시간으로 간주될 수도 있습니다. 상태 검사를 통과하면 업그레이드 프로세스는 다음 업그레이드 도메인으로 진행합니다. 상태 검사에 실패하면 서비스 패브릭은 HealthCheckRetryTimeout이 도달할 때까지 상태 검사를 다시 시도하기 전의 간격(UpgradeHealthCheckInterval) 동안 대기합니다. 기본 및 권장값은 0초입니다. |
| HealthCheckRetryTimeoutSec | 업그레이드 실패를 선언하기 전에 서비스 패브릭이 상태 평가를 계속 수행하는 소요 시간(초)입니다. 기본값은 600초입니다. 이 소요 시간은 HealthCheckWaitDuration이 도달한 후 시작합니다. 이 HealthCheckRetryTimeout 내에서 서비스 패브릭은 응용 프로그램 상태에 대한 복수의 상태 검사를 수행할 수 있습니다. 기본값은 10분이며, 응용 프로그램에 맞게 적절히 사용자 지정해야 합니다. |
| HealthCheckStableDurationSec | 다음 업그레이드 도메인으로 이행하거나 업그레이드를 종료하기 전에 응용 프로그램이 안정적인지 확인하기 위한 소요 시간(초)입니다. 이 대기 소요 시간은 상태 검사를 수행한 직후에 보호되지 않은 상태 변경을 방지하기 위해 사용됩니다. 기본값은 120초이며, 응용 프로그램에 맞게 적절히 사용자 지정해야 합니다. |
| UpgradeDomainTimeoutSec | 단일 업그레이드 도메인을 업그레이드 하는 최대 시간(초)입니다. 이 시간 제한에 도달하면 UpgradeFailureAction의 설정에 따라 업그레이드가 중지 및 진행됩니다. 기본값은 없음(무한)이며, 응용 프로그램에 맞게 적절히 사용자 지정해야 합니다. |
| UpgradeTimeout | 전체 업그레이드에 적용되는 시간 제한(초)입니다. 이 시간 제한에 도달하면 업그레이드가 중지되고 UpgradeFailureAction이 트리거됩니다. 기본값은 없음(무한)이며, 응용 프로그램에 맞게 적절히 사용자 지정해야 합니다. |
| UpgradeHealthCheckInterval | 상태를 확인하는 빈도입니다. 이 매개 변수는 _클러스터_ _매니페스트_의 ClusterManager 섹션에 지정되고 업그레이드 cmdlet의 일부로 지정되지 않습니다. 기본값은 60초입니다. |






<br>
## 응용 프로그램을 업그레이드 하는 동안 서비스 상태 평가

<br> 상태 평가 조건은 옵션입니다. 업그레이드 시작 시 상태 평가 조건이 지정되지 않으면 서비스 패브릭은 응용 프로그램 인스턴스의 ApplicationManifest.xml에 지정된 응용 프로그램 상태 정책을 사용합니다.


<br>

| 매개 변수 | 설명 |
| --- | --- |
| ConsiderWarningAsError | 기본값은 False입니다. 업그레이드하는 동안 응용 프로그램의 상태를 평가할 때 응용 프로그램에 대한 경고 상태 이벤트를 오류로 처리합니다. 기본적으로 서비스 패브릭은 경고 상태 이벤트를 실패(오류)로 평가하지 않으므로 경고 이벤트가 발생해도 업그레이드를 진행할 수 있습니다. |
| MaxPercentUnhealthyDeployedApplications | 기본 및 권장값은 0입니다. 응용 프로그램이 비정상이고 업그레이드에 실패했다고 간주하기 전에 비정상이 될 수 있는 배포된 응용 프로그램의 최대 수를 지정합니다([상태 섹션](service-fabric-health-introduction.md) 참조). 이 매개 변수는 노드는 응용 프로그램 상태를 정의하고 업그레이드 동안 문제를 감지하는 데 도움을 줍니다. 일반적으로 응용 프로그램의 복제본은 다른 노드에 부하를 분담하기 때문에 응용 프로그램이 정상으로 나타나고 업그레이드를 진행할 수 있습니다. 엄격한 MaxPercentUnhealthyDeployedApplications 상태를 지정하여 서비스 패브릭은 응용 프로그램 패키지의 문제를 빠르게 감지하고 빠른 업그레이드 실패 결과를 얻을 수 있습니다. |
| MaxPercentUnhealthyServices | 기본 및 권장값은 0입니다. 응용 프로그램이 비정상으로 간주되고 업그레이드에 실패하기 전에 비정상이 될 수 있는 응용 프로그램 인스턴스의 서비스 최대 수를 지정합니다. |
| MaxPercentUnhealthyPartitionsPerService | 기본 및 권장값은 0입니다. 서비스가 비정상으로 간주되기 전에 비정상이 될 수 있는 서비스의 파티션 최대 수를 지정합니다. |
| MaxPercentUnhealthyReplicasPerPartition | 기본 및 권장값은 0입니다. 파티션이 비정상으로 간주되기 전에 비정상이 될 수 있는 파티션의 복제본 최대 수를 지정합니다. |
| UpgradeReplicaSetCheckTimeout | **상태 비저장 서비스** - 단일 업그레이드 도메인에서 서비스 패브릭은 서비스의 추가 인스턴스를 사용할 수 있는지 확인하려고 시도합니다. 대상 인스턴스의 수가 둘 이상인 경우 서비스 패브릭은 사용 가능한 인스턴스를 둘 이상, 최대 시간 제한 값까지 기다립니다. 이 제한 시간은 UpgradeReplicaSetCheckTimeout 속성을 사용하여 지정됩니다. 시간 제한이 만료되면 서비스 패브릭은 서비스 인스턴스의 수에 관계 없이 업그레이드를 진행합니다. 대상 인스턴스의 수가 하나인 경우 서비스 패브릭은 대기하지 않고 업그레이드를 즉시 진행합니다. **상태 저장 서비스** - 단일 업그레이드 도메인에서 서비스 패브릭은 복제본 세트에 쿼럼이 있는지 확인하려고 합니다. 서비스 패브릭은 사용 가능한 쿼럼을 최대 시간 제한 값(UpgradeReplicaSetCheckTimeout 속성에 의해 지정됨)까지 기다립니다. 시간 제한이 만료되면 서비스 패브릭은 쿼럼에 관계 없이 업그레이드를 진행합니다. 이 설정은 롤포워드 시 사용 안 함(무한)으로 설정되고, 롤백 시 900초로 설정됩니다. |
| ForceRestart | 서비스 코드를 업데이트하지 않고 구성이나 데이터 패키지를 업그레이드하는 경우, ForceRestart 속성이 true로 설정된 경우에만 서비스가 다시 시작됩니다. 업데이트가 완료되면 서비스 패브릭은 새 구성 패키지 또는 데이터 패키지를 사용할 수 있음을 서비스에 알립니다. 서비스는 변경 내용의 적용을 담당합니다. 필요한 경우 서비스는 자체적으로 다시 시작할 수 있습니다. |



<br> <br> MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService 및 MaxPercentUnhealthyReplicasPerPartition 조건은 응용 프로그램 인스턴스에 대한 서비스 형식별로 지정할 수 있습니다. 이러한 매개 변수를 서비스별로 설정하면 응용 프로그램이 서로 다른 평가 정책을 갖는 다른 서비스 형식을 포함할 수 있습니다. 예를 들어, 상태 비저장 게이트웨이 서비스 형식은 특정 응용 프로그램 인스턴스에 대한 상태 저장 엔진 서비스 형식과는 다른 MaxPercentUnhealthyPartitionsPerService를 가집니다.

## 다음 단계

[Visual Studio를 사용하여 응용 프로그램 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 응용 프로그램 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 응용 프로그램 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 응용 프로그램 업그레이드를 진행하는 방법을 안내합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md) 사용 방법을 익혀 응용 프로그램 업그레이드와 호환되도록 만듭니다.

[고급 항목](service-fabric-application-upgrade-advanced.md)을 참조하여 응용 프로그램을 업그레이드하는 동안 고급 기능을 사용하는 방법에 대해 알아봅니다.

[응용 프로그램 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 응용 프로그램 업그레이드 중 발생하는 일반적인 문제를 해결합니다.
 

<!---HONumber=AcomDC_0921_2016-->