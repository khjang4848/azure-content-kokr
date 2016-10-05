<properties
	pageTitle="가상 컴퓨터 크기 집합에 앱 배포 | Microsoft Azure"
	description="가상 컴퓨터 크기 집합에 앱 배포"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>


# 가상 컴퓨터 크기 집합 업그레이드

이 문서에서는 가동 중단 시간 없이 OS 업데이트를 Azure VM 크기 집합에 롤아웃할 수 있는지 설명합니다. 이 컨텍스트에서 OS 업데이트는 OS의 버전/SKU를 변경하거나 사용자 지정 이미지의 URI를 변경합니다. 가동 중지 시간 없이 업데이트한다는 것은 모두 한꺼번에 업데이트하지 않고 한 번에 하나씩 또는 그룹으로(예" 한 번에 하나의 장애 도메인) 업데이트하는 것을 의미합니다. 이렇게 하면 업그레이드되지 않는 모든 VM은 계속 실행될 수 있습니다.

모호성을 피하기 위해 수행하려는 세 가지 유형의 OS 업데이트 작업을 구분해 보겠습니다.

1. 플랫폼 이미지의 버전 또는 SKU 변경. 예를 들어 Ubuntu 14.04.2-LTS 버전을 14.04.201506100에서 14.04.201507060으로 변경하거나 Ubuntu 15.10/최신 SKU를 16.04.0-LTS/최신으로 변경할 수 있습니다. 이 시나리오는 이 문서에서 설명합니다.

2. 새 버전의 사용자 지정 이미지를 작성했으며 이미지를 가리키는 URI를 변경하려고 합니다(속성-> virtualMachineProfile->storageProfile->storageProfile->osDisk->image->uri). 이 시나리오는 이 문서에서 설명합니다.

3. VM 내에서 운영 체제 패치(예: 보안 패치 설치, Windows 업데이트 사용 등). 이 시나리오는 지원되지만 이 문서에서는 다루지 않습니다.

처음 2가지가 지원되는 요구 사항입니다. 세 번째의 경우 적어도 당분간은 이 작업을 위해 새 크기 집합을 만들어야 합니다. 이 문서에서는 옵션 1 및 2에 대해 설명합니다. 참고: [서비스 패브릭](https://azure.microsoft.com/services/service-fabric/) 클러스터의 일부로 배포되는 VM 크기 집합은 여기에서 다루지 않습니다.

플랫폼 이미지의 OS 버전/SKU나 사용자 지정 이미지의 URI를 변경하기 위한 기본 순서는 다음과 같습니다.

* VMSS 모델을 가져옵니다.

* 모델에서 버전, SKU 또는 URI 값을 변경합니다.

* 모델을 업데이트합니다.

* 크기 집합의 VM에 대해 manualUpgrade 호출을 수행합니다. 이 단계는 크기 집합의 upgradePolicy 속성이 "수동"으로 설정되어야만 적합합니다. "자동"으로 설정되면 모든 VM이 한꺼번에 업그레이드되므로 가동 중지 시간이 발생합니다.


이 배경 정보를 염두에 두고, PowerShell에서 및 REST API를 사용하여 크기 집합의 버전을 업데이트하는 방법을 알아 보겠습니다. 이러한 예제는 플랫폼 이미지의 경우를 포함하지만, 이 프로세스를 사용자 지정 이미지에 맞게 조정하기 위한 충분한 정보도 제공되었기 바랍니다.

## PowerShell

이 예제에서는 Windows VM 크기 집합을 새 버전인 "4.0.20160229"로 업데이트합니다. 모델을 업데이트한 후 한 번에 하나의 VM 인스턴스를 업데이트합니다.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the VMSS model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

플랫폼 이미지 버전을 변경하는 대신, 사용자 지정 이미지에 대한 URI를 업데이트한다면 "새 버전 설정" 줄이 다음과 같이 바뀌어 표시될 것입니다.

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## REST API 사용

다음은 Azure REST API를 사용하여 OS 버전 업데이트를 롤아웃하는 Python의 몇 가지 예제입니다. 두 가지 모두 Azure REST API 래퍼 함수의 경량 [azurerm](https://pypi.python.org/pypi/azurerm) 라이브러리를 사용하여 크기 집합 모델에 대해 GET을 수행한 다음 업데이트된 모델에 대해 PUT을 수행합니다. 또한 업데이트 도메인으로 VM을 식별하기 위해 VM 인스턴스 보기도 확인합니다.

### vmssupgrade

vmssupgrade는 한 번에 한 업데이트 도메인씩, 실행 중인 VM 크기 집합으로 OS 업그레이드를 롤아웃하기 위한 Python 스크립트입니다. [여기](https://github.com/gbowerman/vmsstools)에서 찾을 수 있습니다.

![vmssupgrade 스크린 샷](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

이 스크립트는 업데이트할 특정 VM을 선택하거나 업데이트 도메인을 지정할 수 있도록 하며, 플랫폼 이미지 버전 변경 또는 사용자 지정 이미지의 URI 변경을 지원합니다.

### vmsseditor

이 도구는 한 행이 하나의 업데이트 도메인을 나타내는 heatmap으로 VM 상태를 표시하는 VM 크기 집합에 대한 범용 편집기입니다. 무엇보다도 새 버전의 SKU 또는 사용자 지정 이미지 URI로 VMSS에 대한 모델을 업데이트한 다음 업그레이드할 장애 도메인을 선택할 수 있습니다. 이렇게 하면 해당 업데이트 도메인에 있는 모든 VM이 새 모델로 업그레이드됩니다. 또는 선택한 일괄 처리 크기에 따라 롤링 업그레이드를 수행할 수 있습니다. vmsseditor는 다음 [github 리포지토리](https://github.com/gbowerman/vmssdashboard)에서 찾을 수 있습니다.

예를 들어 크기 집합의 모델을 Ubuntu 14.04-2LTS 버전 14.04.201507060으로 업데이트했습니다. 이 스크린샷은 이전 내용을 포함하고 있습니다. 이후에 이 도구에 많은 옵션이 더 추가되었습니다.

![vmsseditor 스크린샷 1](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

업그레이드를 클릭하고 세부 정보 가져오기를 다시 클릭하면 UD 0의 VM이 업데이트되기 시작합니다.

![vmsseditor 스크린샷 2](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

<!---HONumber=AcomDC_0921_2016-->