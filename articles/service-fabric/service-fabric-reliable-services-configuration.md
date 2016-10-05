<properties
   pageTitle="Azure 서비스 패브릭 Reliable Services 구성의 개요 | Microsoft Azure"
   description="Azure 서비스 패브릭에서 상태 저장 Reliable Services를 구성하는 방법을 알아봅니다."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# 상태 저장 Reliable Services 구성

Reliable Services에는 두 가지 구성 설정 집합이 있습니다. 한 집합은 클러스터의 모든 Reliable Services에 대해 전역인 반면 다른 집합은 특정 Reliable Services에 한정됩니다.

## 전역 구성

전역 Reliable Service 구성은 KtlLogger 섹션 아래 클러스터에 대한 클러스터 매니페스트에 지정됩니다. 공유 로그 위치 및 크기와 로거에 사용되는 전역 메모리 한도를 구성할 수 있습니다. 클러스터 매니페스트는 클러스터의 모든 노드 및 서비스에 적용할 설정 및 구성을 포함하는 단일 XML 파일입니다. 이 파일을 일반적으로 ClusterManifest.xml이라고 합니다. Get-ServiceFabricClusterManifest powershell 명령을 사용하여 클러스터에 대한 클러스터 매니페스트를 확인할 수 있습니다.

### 구성 이름

|이름|단위|기본값|설명|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|킬로바이트|8388608|로거 쓰기 버퍼 메모리 풀에 대해 커널 모드에서 할당되는 최소 KB 수입니다. 이 메모리 풀은 디스크에 쓰기 전에 상태 정보를 캐시하는 데 사용됩니다.|
|WriteBufferMemoryPoolMaximumInKB|킬로바이트|제한 없음|로거 쓰기 버퍼 메모리 풀이 증가할 수 있는 최대 크기입니다.|
|SharedLogId|GUID|""|서비스별 구성에서 SharedLogId를 지정하지 않은 클러스터에 있는 모든 노드에서 모든 Reliable Services에 사용된 기본 공유 로그 파일을 식별하는 데 사용할 고유 GUID를 지정합니다. SharedLogId가 지정된 경우 SharedLogPath도 지정해야 합니다.|
|SharedLogPath|정규화된 경로 이름|""|서비스별 구성에서 SharedLogPath를 지정하지 않은 클러스터에 있는 모든 노드에서 모든 Reliable Services가 공유 로그 파일을 사용하는 정규화된 경로 이름을 지정합니다. 그러나 SharedLogPath가 지정된 경우 SharedLogId도 지정해야 합니다.|
|SharedLogSizeInMB|메가바이트|8192|공유 로그에 대해 정적으로 할당할 디스크 공간(MB) 수를 지정합니다. 값은 2048 이상이어야 합니다.|

### 샘플 클러스터 매니페스트 섹션
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### 설명
로거에는 Reliable Services 복제본에 관한 전용 로그에 기록되기 전에 캐싱 상태 데이터에 대해 노드의 모든 Reliable Services에 사용 가능한 페이징되지 않은 커널 메모리로부터 할당된 전역 메모리 풀이 있습니다. 이 풀 크기는 WriteBufferMemoryPoolMinimumInKB 및 WriteBufferMemoryPoolMaximumInKB 설정으로 제어합니다. WriteBufferMemoryPoolMinimumInKB는 이 메모리 풀의 초기 크기와 메모리 풀을 축소할 수 있는 가장 작은 크기를 지정합니다. WriteBufferMemoryPoolMaximumInKB는 메모리 풀을 확장할 수 있는 가장 큰 크기입니다. 열린 각 Reliable Service 복제본은 시스템에서 결정된 WriteBufferMemoryPoolMaximumInKB까지 메모리 풀 크기를 늘릴 수 있습니다. 메모리 풀에서 사용 가능한 것보다 메모리 요구량이 많은 경우 메모리가 사용 가능해질 때까지 메모리에 대한 요청이 지연됩니다. 따라서 특정 구성에 대한 쓰기 버퍼 메모리 풀이 너무 작으면 성능이 저하될 수 있습니다.

SharedLogId 및 SharedLogPath 설정은 항상 함께 사용되며 클러스터의 모든 노드에 대한 기본 공유 로그에 대한 GUID 및 위치를 정의합니다. 기본 공유 로그는 서비스별 settings.xml에 설정을 지정하지 않은 모든 Reliable Services에 사용됩니다. 최상의 성능을 위해 공유 로그 파일에만 사용되는 디스크에 공유 로그 파일을 배치해야 경합이 감소합니다.

SharedLogSizeInMB는 모든 노드에서 기본 공유 로그를 위해 미리 할당할 디스크 공간의 양을 지정합니다. SharedLogSizeInMB를 지정하기 위해 SharedLogId 및 SharedLogPath를 지정하지 않아도 됩니다.


## 서비스별 구성
구성 패키지(구성) 또는 서비스 구현(코드)을 사용하여 상태 저장 신뢰할 수 있는 서비스의 기본 구성을 수정할 수 있습니다.

+ **구성** - 구성 패키지를 통한 구성은 응용 프로그램의 각 서비스에 대한 Microsoft Visual Studio 패키지 루트의 Config 폴더에 생성된 Settings.xml 파일을 변경하여 수행됩니다.
+ **코드** - 코드를 통한 구성은 적절한 옵션 집합을 가진 ReliableStateManagerConfiguration 개체를 사용하는 ReliableStateManager를 만들어 수행됩니다.

기본적으로 Azure 서비스 패브릭 런타임은 settings.xml 파일에서 미리 정의된 섹션 이름을 찾아서 기본 런타임 구성 요소를 만드는 동안 해당 구성 값을 사용합니다.

>[AZURE.NOTE] 코드를 통해 서비스를 구성할 예정이 아니면 Visual Studio 솔루션에서 생성된 "Settings.xml" 파일에서 다음 구성의 섹션 이름을 삭제하지 **않도록** 합니다. ReliableStateManager를 구성할 때 구성 패키지 또는 섹션의 이름을 바꾸려면 코드를 변경해야 합니다.


### 복제자 보안 구성
복제자 보안 구성은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 따라서 서비스는 서로의 복제 트래픽을 볼 수 없으므로 항상 사용 가능하게 설정한 데이터를 안전하게 보호할 수 없습니다. 기본적으로 빈 보안 구성 섹션에서는 복제 보안이 되지 않습니다.

### 기본 섹션 이름
ReplicatorSecurityConfig

>[AZURE.NOTE] 이 섹션의 이름을 변경하려면 이 서비스에 대한 ReliableStateManager를 만들 때 replicatorSecuritySectionName 매개 변수를 ReliableStateManagerConfiguration 생성자로 재정의합니다.


### 복제자 구성
복제자 구성은 상태를 로컬로 복제하고 유지하여 상태 저장 신뢰할 수 있는 서비스의 상태를 매우 안정적으로 만드는 일을 담당하는 복제자를 구성합니다. 기본 구성은 Visual Studio 템플릿에 의해 생성되며 충분해야 합니다. 이 섹션에서는 복제자 조정에 사용할 수 있는 추가 구성에 대해 설명합니다.

### 기본 섹션 이름
ReplicatorConfig

>[AZURE.NOTE] 이 섹션의 이름을 변경하려면 이 서비스에 대한 ReliableStateManager를 만들 때 replicatorSettingsSectionName 매개 변수를 ReliableStateManagerConfiguration 생성자로 재정의합니다.


### 구성 이름
|이름|단위|기본값|설명|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|초|0\.015|작업을 수신한 후 주 복제본에 대한 승인을 다시 보내기 전에 보조 복제본의 복제자가 대기하는 시간. 이 간격 내에서 처리하는 작업에 대해 보낼 나머지 승인은 모두 하나의 응답으로 전송됩니다.|
|ReplicatorEndpoint|해당 없음|기본값 없음--필수 매개 변수|주/보조 복제자가 복제본 세트의 다른 복제자와 통신하는 데 사용할 IP 주소 및 포트. 서비스 매니페스트의 TCP 리소스 끝점을 참조해야 합니다. 서비스 매니페스트에서 끝점 리소스를 정의하는 방법에 대한 자세한 내용은 [서비스 매니페스트 리소스](service-fabric-service-manifest-resources.md)를 참조하세요. |
|MaxPrimaryReplicationQueueSize|작업의 수|8192|기본 큐의 최대 작업 수. 작업은 주 복제자가 모든 보조 복제자로부터 승인을 받은 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다.|
|MaxSecondaryReplicationQueueSize|작업의 수|16384|보조 큐의 최대 작업 수. 작업은 지속성을 통해 상태를 항상 사용 가능하도록 설정한 후 해제됩니다. 이 값은 64보다 크고 2의 제곱이어야 합니다.|
|CheckpointThresholdInMB|MB|50|상태가 검사점이 된 후의 로그 파일 공간 크기|
|MaxRecordSizeInKB|KB|1024|복제자가 로그에 기록할 수 있는 최대 레코드 크기. 이 값은 4의 배수이고 16보다 커야 합니다.|
|SharedLogId|GUID|""|이 복제본과 함께 사용되는 공유 로그 파일을 식별하는 데 사용할 고유한 GUID를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 됩니다. 그러나 SharedLogId가 지정된 경우 SharedLogPath도 지정해야 합니다.|
|SharedLogPath|정규화된 경로 이름|""|이 복제본의 공유 로그 파일을 생성할 정규화된 경로를 지정합니다. 일반적으로 서비스는 이 설정을 사용해서는 안 됩니다. 그러나 SharedLogPath가 지정된 경우 SharedLogId도 지정해야 합니다.|
|SlowApiMonitoringDuration|초|300|관리되는 API 호출에 대한 모니터링 간격을 설정합니다. 예: 사용자 제공 백업 콜백 함수. 이 간격이 지나면 상태 경고 보고서가 상태 관리자로 전송됩니다.|

### 코드를 통한 샘플 구성
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### 샘플 구성 파일
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### 설명
BatchAcknowledgementInterval은 복제 대기 시간을 제어합니다. '0' 값은 처리량을 희생하여 가장 낮은 대기 시간을 제공합니다(더 많은 승인 메시지를 보내고 처리해야 하므로 각각에 포함된 승인은 적음). BatchAcknowledgementInterval의 값이 클수록 전체적인 복제 처리량은 높아지고 작업 대기 시간은 더욱 길어집니다. 이 값은 트랜잭션 커밋의 대기 시간으로 직접 변환됩니다.

CheckpointThresholdInMB 값은 복제자가 복제자의 전용 로그 파일에 상태 정보를 저장하는 데 사용할 수 있는 디스크 공간의 크기를 제어합니다. 새 복제본을 집합에 추가할 때 기본값보다 더 높은 값으로 늘리면 재구성 시간을 단축할 수 있습니다. 이는 로그의 더 많은 작업 기록을 사용함으로써 상태 전송이 부분적으로 발생하기 때문입니다. 이로 인해 크래시 후 복제본의 복구 시간이 증가할 수 있습니다.

MaxRecordSizeInKB 설정은 복제자가 로그 파일에 쓸 수 있는 레코드의 최대 크기를 정의합니다. 대부분의 경우 기본 1024KB 레코드 크기가 최적입니다. 그러나 서비스에서 더 큰 데이터 항목이 상태 정보의 일부가 되도록 할 경우 이 값을 늘려야 할 수 있습니다. MaxRecordSizeInKB를 1024보다 작은 값으로 설정하면 작은 레코드만 작은 레코드에 필요한 공간을 사용하므로 이점이 적습니다. 이 값은 드문 경우에만 변경되어야 합니다.

SharedLogId 및 SharedLogPath 설정은 항상 함께 사용되며 서비스가 노드에 대한 기본 공유 로그에서 별도의 공유 로그를 사용하도록 합니다. 최상의 효율성을 위해 최대한 많은 서비스가 동일한 공유 로그를 지정해야 합니다. 공유 로그 파일에만 사용되는 디스크에 공유 로그 파일을 배치해야 헤드 이동 경합이 감소합니다. 이 값은 드문 경우에만 변경되어야 합니다.

## 다음 단계
 - [Visual Studio에서 서비스 패브릭 응용 프로그램 디버깅](service-fabric-debugging-your-application.md)
 - [신뢰할 수 있는 서비스에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0921_2016-->