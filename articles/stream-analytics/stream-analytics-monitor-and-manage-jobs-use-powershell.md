<properties 
	pageTitle="PowerShell을 사용하여 스트림 분석 작업 모니터링 및 관리 | Microsoft Azure" 
	description="Azure PowerShell 및 cmdlet을 사용하여 스트림 분석 작업을 모니터링하고 관리하는 방법에 대해 알아봅니다." 
	keywords="azure powershell, azure powershell cmdlet, powershell 명령, powershell 스크립팅"	
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
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"/>


# Azure PowerShell cmdlet을 사용하여 스트림 분석 작업 모니터링 및 관리

기본 스트림 분석 작업을 실행하는 Azure PowerShell cmdlet 및 PowerShell 스크립팅을 사용하여 스트림 분석 리소스를 모니터링 및 관리하는 방법을 알아봅니다.

## 스트림 분석에 Azure PowerShell cmdlet을 실행하기 위한 필수 조건

 - 구독에서 Azure 리소스 그룹을 만듭니다. 다음은 샘플 Azure PowerShell 스크립트입니다. Azure PowerShell 정보는 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

Azure PowerShell 0.9.8:

 		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
		Select-AzureSubscription -SubscriptionName <subscription name>
 
		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

		# Create an Azure resource group
		New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0.

 		# Log in to your Azure account
		Login-AzureRmAccount

		# Select the Azure subscription you want to use to create the resource group.
		Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

		# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
		#Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
		
		# Create an Azure resource group
		New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
		


> [AZURE.NOTE] 프로그래밍 방식으로 만든 스트림 분석 작업은 기본적으로 모니터링이 설정되어 있지 않습니다. 작업의 모니터 페이지로 이동하고 사용 버튼을 클릭하여 Azure 포털에서 수동으로 모니터링을 설정하거나 [Azure 스트림 분석 - 프로그래밍 방식으로 스트림 분석 작업 모니터링](stream-analytics-monitor-jobs.md)의 단계를 수행하여 이를 프로그래밍 방식으로 수행할 수 있습니다.

## 스트림 분석용 Azure PowerShell cmdlet
다음 Azure PowerShell cmdlet은 Azure 스트림 분석 작업을 모니터링하고 관리하는 데 사용할 수 있습니다. Azure PowerShell에는 여러 버전이 있습니다. **나열된 예제에서 첫 번째 명령은 Azure PowerShell 0.9.8에 적용되고, 두 번째 명령은 Azure PowerShell 1.0에 적용됩니다.** Azure PowerShell 1.0 명령에는 항상 "AzureRM"이 있습니다.

### Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Azure 구독 또는 지정한 리소스 그룹에 정의된 모든 스트림 분석 작업을 나열하거나 리소스 그룹 내의 특정 작업에 대한 작업 정보를 가져옵니다.

**예 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsJob

이 PowerShell 명령은 Azure 구독의 모든 스트림 분석 작업에 대한 정보를 반환합니다.

**예 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

이 PowerShell 명령은 리소스 그룹 StreamAnalytics-Default-Central-US의 모든 스트림 분석 작업에 대한 정보를 반환합니다.

**예 3**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

이 PowerShell 명령은 리소스 그룹 StreamAnalytics-Default-Central-US의 스트림 분석 작업 StreamingJob에 대한 정보를 반환합니다.

### Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
지정한 스트림 분석 작업에 정의된 모든 입력을 나열하거나 특정 입력에 대한 정보를 가져옵니다.

**예 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

이 PowerShell 명령은 StreamingJob 작업에 정의된 모든 입력에 대한 정보를 반환합니다.

**예 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

이 PowerShell 명령은 StreamingJob 작업에 정의된 EntryStream이라는 입력에 대한 정보를 반환합니다.

### Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
지정한 스트림 분석 작업에 정의된 모든 출력을 나열하거나 특정 출력에 대한 정보를 가져옵니다.

**예 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

이 PowerShell 명령은 StreamingJob 작업에 정의된 모든 출력에 대한 정보를 반환합니다.

**예 2**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

이 PowerShell 명령은 StreamingJob 작업에 정의된 Output이라는 출력에 대한 정보를 반환합니다.

### Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
지정한 지역의 스트리밍 단위 할당량에 대한 정보를 가져옵니다.

**예 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

이 PowerShell 명령은 미국 중부 지역의 스트리밍 단위 할당량 및 사용에 대한 정보를 반환합니다.

### Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
스트림 분석 작업에 정의된 특정 변환에 대한 정보를 가져옵니다.

**예 1**

Azure PowerShell 0.9.8:

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0.

	Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

이 PowerShell 명령은 StreamingJob 작업에 정의된 StreamingJob이라는 변환에 대한 정보를 반환합니다.

### New-AzureStreamAnalyticsInput | New-AzureRMStreamAnalyticsInput
스트림 분석 작업 내에서 새 입력을 만들거나 지정한 기존 입력을 업데이트합니다.
  
.json 파일 또는 명령줄에서 입력의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 입력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 입력을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 입력 이름을 지정하면 확인 없이 입력이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [스트림 분석 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [입력 만들기(Azure 스트림 분석)][msdn-rest-api-create-stream-analytics-input]을 참조하세요.

**예 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

이 PowerShell 명령은 Input.json 파일에 새 입력을 만듭니다. 입력 정의 파일에 지정된 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

이 PowerShell 명령은 EntryStream이라는 작업에 새 입력을 만듭니다. 이 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예 3**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

이 PowerShell 명령은 EntryStream이라는 기존 입력 소스의 정의를 파일에 있는 정의로 바꿉니다.

### New-AzureStreamAnalyticsJob | New-AzureRMStreamAnalyticsJob
Microsoft Azure에 새 스트림 분석 작업을 만들거나 지정한 기존 작업의 정의를 업데이트합니다.

.json 파일 또는 명령줄에서 작업의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 작업 이름을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 작업을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 작업 이름을 지정하면 확인 없이 작업 정의가 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [스트림 분석 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [스트림 분석 작업 만들기][msdn-rest-api-create-stream-analytics-job] 섹션을 참조하세요.

**예 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

이 PowerShell 명령은 JobDefinition.json의 정의에서 새 작업을 만듭니다. 작업 정의 파일에 지정된 이름의 기존 작업이 이미 정의되어 있으면 cmdlet에서 해당 작업을 바꿀지 여부를 묻습니다.

**예 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

이 PowerShell 명령은 StreamingJob에 대한 작업 정의를 바꿉니다.

### New-AzureStreamAnalyticsOutput | New-AzureRMStreamAnalyticsOutput
스트림 분석 작업 내에서 새 출력을 만들거나 기존 출력을 업데이트합니다.

.json 파일 또는 명령줄에서 출력의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 출력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 출력을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 출력 이름을 지정하면 확인 없이 출력이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [스트림 분석 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [출력 만들기(Azure 스트림 분석)][msdn-rest-api-create-stream-analytics-output]을 참조하세요.

**예 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

이 PowerShell 명령은 StreamingJob 작업에 "output"이라는 새 출력을 만듭니다. 이 이름의 기존 출력이 이미 정의되어 있으면 cmdlet에서 해당 출력을 바꿀지 여부를 묻습니다.

**예 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

이 PowerShell 명령은 StreamingJob 작업에서 "output"의 정의를 바꿉니다.

### New-AzureStreamAnalyticsTransformation | New-AzureRMStreamAnalyticsTransformation
스트림 분석 작업 내에서 새 변환을 만들거나 기존 변환을 업데이트합니다.
  
.json 파일 또는 명령줄에서 변환의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 변환을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 변환을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 변환 이름을 지정하면 확인 없이 변환이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [스트림 분석 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [변환 만들기(Azure 스트림 분석)][msdn-rest-api-create-stream-analytics-transformation]을 참조하세요.

**예 1**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

이 PowerShell 명령은 StreamingJob 작업에 StreamingJobTransform이라는 새 변환을 만듭니다. 이 이름의 기존 변환이 이미 정의되어 있으면 cmdlet에서 해당 변환을 바꿀지 여부를 묻습니다.

**예 2**

Azure PowerShell 0.9.8:

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0.

	New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 이 PowerShell 명령은 StreamingJob 작업에서 StreamingJobTransform의 정의를 바꿉니다.

### Remove-AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Microsoft Azure의 스트림 분석 작업에서 특정 입력을 비동기적으로 삭제합니다. –Force 매개 변수를 지정하면 확인 없이 입력이 삭제됩니다.

**예 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0.

	Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

이 PowerShell 명령은 StreamingJob 작업에서 EventStream 입력을 제거합니다.

### Remove-AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Microsoft Azure에서 특정 스트림 분석 작업을 비동기적으로 삭제합니다. –Force 매개 변수를 지정하면 확인 없이 작업이 삭제됩니다.

**예 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0.

	Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

이 PowerShell 명령은 StreamingJob 작업을 제거합니다.

### Remove-AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Microsoft Azure의 스트림 분석 작업에서 특정 출력을 비동기적으로 삭제합니다. –Force 매개 변수를 지정하면 확인 없이 출력이 삭제됩니다.

**예 1**

Azure PowerShell 0.9.8:

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0.

	Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

이 PowerShell 명령은 StreamingJob 작업에서 Output 출력을 제거합니다.

### Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Microsoft Azure에 스트림 분석 작업을 비동기적으로 배포하고 시작합니다.

**예 1**

Azure PowerShell 0.9.8:

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0.

	Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

이 PowerShell 명령은 사용자 지정 출력 시작 시간이 2012년 12월 12일 12:12:12 UTC로 설정되어 StreamingJob 작업을 시작합니다.


### Stop-AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Microsoft Azure에서 실행 중인 스트림 분석 작업을 비동기적으로 중지하고 사용하던 리소스를 할당 취소합니다. 작업 정의와 메타데이터는 작업을 편집하고 다시 시작할 수 있도록 Azure 포털과 관리 API를 통해 구독 내에서 계속 사용할 수 있습니다. 중지됨 상태의 작업에 대해서는 요금이 부과되지 않습니다.

**예 1**

Azure PowerShell 0.9.8:

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0.

	Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

이 PowerShell 명령은 StreamingJob 작업을 중단합니다.

### Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
스트림 분석이 지정한 입력에 연결할 수 있는지 테스트합니다.

**예 1**

Azure PowerShell 0.9.8:

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0.

	Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

이 PowerShell 명령은 StreamingJob에서 EntryStream 입력의 연결 상태를 테스트합니다.

###Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
스트림 분석이 지정한 출력에 연결할 수 있는지 테스트합니다.

**예 1**

Azure PowerShell 0.9.8:

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0.

	Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

이 PowerShell 명령은 StreamingJob에서 Output 출력의 연결 상태를 테스트합니다.

## 지원 받기
추가 지원이 필요한 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics)을 참조하세요.


## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](stream-analytics-get-started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=AcomDC_0921_2016-->