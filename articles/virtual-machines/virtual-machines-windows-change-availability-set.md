<properties
	pageTitle="VM 가용성 집합 변경 | Microsoft Azure"
	description="Azure PowerShell 및 Resource Manager 배포 모델을 사용하여 가상 컴퓨터에 대한 가용성 집합을 변경하는 방법에 대해 알아봅니다."
	keywords=""
	services="virtual-machines-windows"
	documentationCenter=""
	authors="Drewm3"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="drewm"/>



# Windows VM에 대한 가용성 집합 변경

다음 단계에서는 Azure PowerShell을 사용하여 VM의 가용성 집합을 변경하는 방법을 설명합니다. VM은 생성될 때만 가용성 집합에 추가될 수 있습니다. 가용성 집합을 변경하려면 가상 컴퓨터를 삭제했다가 다시 만들어야 합니다.

## PowerShell을 사용하여 가용성 집합 변경

1. 수정할 VM에서 다음 주요 세부 정보를 캡처합니다.

	VM의 이름
	
	```powershell
	$vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
	$vm.Name
	```
 
	VM 크기
	
	```powershell
	$vm.HardwareProfile.VmSize
	```

	VM에 있는 경우 네트워크 기본 네트워크 인터페이스 및 선택적 네트워크 인터페이스
	
	```powershell
	$vm.NetworkProfile.NetworkInterfaces[0].Id
	```

	OS 디스크 프로필

	```powershell
	$vm.StorageProfile.OsDisk.OsType
	$vm.StorageProfile.OsDisk.Name
	$vm.StorageProfile.OsDisk.Vhd.Uri
	```

	각 데이터 디스크에 대한 디스크 프로필
	
	```powershell
	$vm.StorageProfile.DataDisks[<index>].Lun
	$vm.StorageProfile.DataDisks[<index>].Vhd.Uri
	```

	설치된 VM 확장
	
	```powershell
	$vm.Extensions
	```

2. 디스크 또는 네트워크 인터페이스 중 어느 것도 삭제하지 않고 VM을 삭제합니다.

	```powershell
	Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
	```

3. 아직 없는 경우 가용성 집합을 만듭니다.

	```powershell
	New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
	```

4. 새 가용성 집합을 사용하여 VM을 다시 만듭니다.

	```powershell
	$vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>

	Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]

	Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 

	New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
	``` 

5. 데이터 디스크 및 확장을 추가합니다. 자세한 내용은 [VM에 데이터 디스크 연결](virtual-machines-windows-attach-disk-portal.md) 및 [확장 구성 샘플](virtual-machines-windows-extensions-configuration-samples.md)을 참조하세요. PowerShell 또는 Azure CLI를 사용하여 VM에 데이터 디스크 및 확장을 추가할 수 있습니다.

## 예제 스크립트

다음 스크립트는 필요한 정보를 수집하고, 원본 VM을 삭제한 다음 새 가용성 집합에서 다시 만드는 예제를 제공합니다.

```powershell
	#set variables
	$rg = "demo-resource-group"
	$vmName = "demo-vm"
	$newAvailSetName = "demo-as"
	$outFile = "C:\temp\outfile.txt"

	#Get VM Details
	$OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

	#Output VM details to file
	"VM Name: " | Out-File -FilePath $outFile 
	$OriginalVM.Name | Out-File -FilePath $outFile -Append

	"Extensions: " | Out-File -FilePath $outFile -Append
	$OriginalVM.Extensions | Out-File -FilePath $outFile -Append

	"VMSize: " | Out-File -FilePath $outFile -Append
	$OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

	"NIC: " | Out-File -FilePath $outFile -Append
	$OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

	"OSType: " | Out-File -FilePath $outFile -Append
	$OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

	"OS Disk: " | Out-File -FilePath $outFile -Append
	$OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

	if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
	}

	#Remove the original VM
	Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

	#Create new availability set if it does not exist
	$availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
	if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
	}

	#Create the basic configuration for the replacement VM
	$newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
	Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

	#Add Data Disks
	foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
	}

	#Add NIC(s)
	foreach ($nic in $OriginalVM.NetworkInterfaceIDs) {
		Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic
	}

	#Create the VM
	New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## 다음 단계

[데이터 디스크](virtual-machines-windows-attach-disk-portal.md)를 더 추가하여 VM에 저장소를 좀 더 추가합니다.

<!---HONumber=AcomDC_0921_2016-->