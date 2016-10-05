
<properties
   pageTitle="서비스 패브릭 클러스터 설정 및 패브릭 업그레이드 정책 사용자 지정 | Microsoft Azure"
   description="이 문서에서는 사용자 지정할 수 있는 패브릭 설정 및 패브릭 업그레이드 정책에 대해 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="chackdan"/>

# 서비스 패브릭 클러스터 설정 및 패브릭 업그레이드 정책 사용자 지정

이 문서에서는 서비스 패브릭 클러스터에 대한 다양한 패브릭 설정 및 패브릭 업그레이드 정책을 사용자 지정하는 방법을 설명합니다. 포털에서 또는 Resource Manager 템플릿을 사용하여 사용자 지정할 수 있습니다.

## 사용자 지정할 수 있는 패브릭 설정


다음은 사용자 지정할 수 있는 패브릭 설정입니다.

### 섹션 이름: Security

|**매개 변수**|**허용되는 값**|**지침 또는 간단한 설명**|
|-----------------------|--------------------------|--------------------------|
|ClusterProtectionLevel|None 또는 EncryptAndSign| 비보안 클러스터의 경우 None(기본값), 보안 클러스터의 경우 EncryptAndSign |

### 섹션 이름: Hosting

|**매개 변수**|**허용되는 값**|**지침 또는 간단한 설명**|
|-----------------------|--------------------------|--------------------------|
|ServiceTypeRegistrationTimeout|시간(초), 기본값: 300| ServiceType을 패브릭에 등록하는 데 허용되는 최대 시간|
|ServiceTypeDisableFailureThreshold|정수, 기본값: 1| 해당 노드에서 서비스 유형을 사용하지 않도록 설정하고 다른 노드에 배치를 시도하도록 FM(FailoverManager)에 알리기까지 허용되는 실패 횟수에 대한 임계값입니다.|
|ActivationRetryBackoffInterval|시간(초), 기본값: 5|모든 활성화 오류의 백오프 간격. 모든 연속 활성화 실패 시 시스템은 MaxActivationFailureCount까지 활성화를 다시 시도합니다. 모든 시도에서 다시 시도 간격은 연속 활성화 실패와 활성화 백오프 간격을 곱한 값입니다.|
|ActivationMaxRetryInterval|시간(초), 기본값: 300| 모든 연속 활성화 실패 시 시스템은 ActivationMaxFailureCount까지 활성화를 다시 시도합니다. ActivationMaxRetryInterval은 모든 활성화 실패 후 다시 시도하기 전에 대기 시간 간격을 지정합니다. |
|ActivationMaxFailureCount|정수, 기본값: 10| 시스템이 포기하기 전에 실패한 활성화를 다시 시도하는 횟수 |

### 섹션 이름: FailoverManager

|**매개 변수**|**허용되는 값**|**지침 또는 간단한 설명**|
|-----------------------|--------------------------|--------------------------|
|PeriodicLoadPersistInterval|시간(초), 기본값: 10| FM이 새 부하 보고서를 확인하는 빈도를 결정합니다.|

### 섹션 이름: Federation

|**매개 변수**|**허용되는 값**|**지침 또는 간단한 설명**|
|-----------------------|--------------------------|--------------------------|
|LeaseDuration|시간(초), 기본값: 30|노드 및 해당 이웃 노드 간에 임대가 지속되는 기간|
|LeaseDurationAcrossFaultDomain|시간(초), 기본값: 30|장애 도메인에서 노드 및 해당 이웃 노드 간에 임대가 지속되는 기간|

### 섹션 이름: ClusterManager

|**매개 변수**|**허용되는 값**|**지침 또는 간단한 설명**|
|-----------------------|--------------------------|--------------------------|
|UpgradeStatusPollInterval|시간(초), 기본값: 60|응용 프로그램 업그레이드 상태에 대한 풀링 간격. 이 값은 GetApplicationUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다.|
|UpgradeHealthCheckInterval|시간(초), 기본값: 60|모니터링되는 응용 프로그램 업그레이드 동안 시스템 상태 확인 빈도|
|FabricUpgradeStatusPollInterval|시간(초), 기본값: 60|패브릭 업그레이드 상태에 대한 풀링 간격. 이 값은 GetFabricUpgradeProgress 호출에 대한 업데이트 속도를 결정합니다. |
|FabricUpgradeHealthCheckInterval|시간(초), 기본값: 60|모니터링되는 패브릭 업그레이드 동안 시스템 상태 확인 빈도|



## 다음 단계

클러스터 관리에 대한 자세한 내용은 다음 문서를 읽어보세요.

[Azure 클러스터에서 인증서 추가, 롤오버, 제거 ](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->