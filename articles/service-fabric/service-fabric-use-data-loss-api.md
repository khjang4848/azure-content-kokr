<properties
   pageTitle="서비스 패브릭 서비스에 대해 데이터 손실을 호출하는 방법 | Microsoft Azure"
   description="데이터 손실 API를 사용하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# 서비스에 대해 데이터 손실을 호출하는 방법

>[AZURE.WARNING] 이 문서에서는 서비스에서 데이터 손실을 유발하는 방법을 설명합니다. 이 문서는 주의해서 참조해야 합니다.

## 소개
StartPartitionDataLossAsync()를 호출하여 서비스 패브릭 서비스 파티션에서의 데이터 손실을 호출할 수 있습니다. 이 API는 오류 주입 및 분석 서비스를 사용하여 데이터 손실 조건을 일으키는 작업을 수행합니다.

## 오류 주입 및 분석 서비스 사용

오류 주입 및 분석 서비스는 현재 아래 차트에 나오는 다음과 같은 API를 지원합니다. 차트 오른쪽에는 각각에 해당하는 PowerShell cmdlet이 나와 있습니다. 각 API에 대한 자세한 내용은 각 API에 대한 MSDN 설명서를 참조하세요.

| C# API | PowerShell Cmdlet |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync][dl] |[Start-ServiceFabricPartitionDataLoss][psdl] |
|[StartPartitionQuorumLossAsync][ql] |[Start-ServiceFabricPartitionQuorumLoss][psql] |
|[StartPartitionRestartAsync][rp] |[Start-ServiceFabricPartitionRestart][psrp] |

## 명령 실행의 개념적 개요

오류 주입 및 분석 서비스에서는 하나의 API(이 문서에서는 “Start" API)로 명령을 시작해서 최종 상태에 도달하거나 사용자가 취소할 때까지 "GetProgress" API를 통해 해당 명령의 진행 상황을 확인하는 비동기 모델을 사용합니다. 명령을 시작하려면 해당 API에 대해 "Start" API를 호출합니다. 이 API는 오류 주입 및 분석 서비스가 요청을 수락할 때 반환됩니다. 그러나 명령이 얼마나 많이 실행되었는지, 심지어 시작되었는지 자체도 나타내지 않습니다. 명령의 진행 상태를 확인하려면 이전에 호출한 “Start” API에 해당하는 "GetProgress" API를 호출합니다. "GetProgress" API는 State 속성 내에서 명령의 현재 상태를 나타내는 개체를 반환합니다. 명령을 다음이 실행될 때까지 무기한 실행됩니다.

1.	성공적으로 완료됩니다. 이 경우 "GetProgress"를 호출하면 진행 중인 개체의 State는 Completed가 됩니다.
2.	오류가 발생합니다. 이 경우 "GetProgress"를 호출하면 진행 중인 개체의 State는 Faulted가 됩니다.
3.	[CancelTestCommandAsync][cancel] API 또는 [Stop-ServiceFabricTestCommand][cancelps] PowerShell cmdlet을 통해 취소할 수 있습니다. 이 경우 "GetProgress"를 호출하면 진행 중인 개체의 State는 해당 API의 인수에 따라 Cancelled 또는 ForceCancelled가 됩니다. 자세한 내용은 [CancelTestCommandAsync][cancel]에 대한 설명서를 참조하세요.


## 명령 실행의 세부 정보

명령을 시작하려면 예상되는 인수를 사용하여 Start API를 호출합니다. 모든 Start API에는 operationId라는 GUID 인수가 있습니다. operationId 인수는 이 명령의 진행률을 추적하는 데 사용되므로 추적해야 합니다. 그런 후 명령의 진행률을 추적하기 위해 "GetProgress" API에 전달해야 합니다. operationId는 고유해야 합니다.

Start API를 성공적으로 호출하면 반환된 진행 중인 개체의 State 속성이 Completed가 될 때까지 GetProgress API가 반복해서 호출됩니다. 모든 [FabricTransientException][fte] 및 OperationCanceledException이 다시 시도됩니다. 이 명령이 최종 상태(Completed, Faulted 또는 Cancelled)에 도달하면 반환된 진행 중인 개체의 Result 속성은 추가 정보를 제공합니다. 상태가 Completed이면 Result.SelectedPartition.PartitionId는 선택한 파티션 ID를 포함합니다. Result.Exception은 null이 됩니다. 상태가 Faulted인 경우 Result.Exception에는 오류 주입 및 분석 서비스 기능에서 해당 명령이 실패한 이유가 포함됩니다. Result.SelectedPartition.PartitionId는 선택한 파티션 ID가 됩니다. 상황에 따라 파티션을 선택할 만큼 명령이 충분히 진행되지 않았을 수 있습니다. 그런 경우에 PartitionId는 0이 됩니다. 상태가 Cancelled인 경우 Result.Exception은 null이 됩니다. Faulted 사례와 같이, Result.SelectedPartition.PartitionId는 선택된 파티션 ID를 가지고 있지만 명령이 그럴 수 있을 정도로 충분히 진행되지 않은 경우 0이 됩니다. 아래 샘플을 참조하세요.

아래 샘플 코드에서는 명령을 시작한 후 진행 상태를 확인하고 특정 파티션에서 데이터 손실을 일으키는 방법을 보여 줍니다.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

아래 샘플은 PartitionSelector를 사용하여 지정된 서비스의 임의 파티션을 선택하는 방법을 보여 줍니다.

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## 기록 및 잘림

명령이 최종 상태에 도달하면 해당 메타데이터는 특정 시간 동안 오류 주입 및 분석 서비스에 남아 있다가 공간 절약을 위해 제거됩니다. 명령이 제거된 후 명령의 operationId를 사용하여 "GetProgress"가 호출되면 ErrorCode인 KeyNotFound를 사용하여 FabricException을 반환합니다.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx

<!---HONumber=AcomDC_0921_2016-->