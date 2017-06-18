<properties
 pageTitle="스케줄러 PowerShell Cmdlet 참조"
 description="스케줄러 PowerShell Cmdlet 참조"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# 스케줄러 PowerShell Cmdlet 참조

다음 표에서는 Azure 스케줄러의 주요 cmdlet을 설명하고 참조 페이지에 연결합니다.

Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

[Azure Resource Manager cmdlet](https://msdn.microsoft.com/library/mt125356(v=azure.200).aspx)에 대한 자세한 내용은 [Azure Resource Manager로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

|Cmdlet|Cmdlet 설명|
|---|---|
[Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133(v=azure.200).aspx) |작업 컬렉션을 비활성화합니다. 
[Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135(v=azure.200).aspx) |작업 컬렉션을 활성화합니다.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125(v=azure.200).aspx) |Scheduler 작업을 가져옵니다.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132(v=azure.200).aspx) |작업 컬렉션을 가져옵니다.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126(v=azure.200).aspx) |작업 내역을 가져옵니다.
[New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136(v=azure.200).aspx) |HTTP 작업을 만듭니다.
[New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141(v=azure.200).aspx) |작업 컬렉션을 만듭니다.
[New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134(v=azure.200).aspx) |Service Bus 큐 작업을 만듭니다.
[New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142(v=azure.200).aspx) |Service Bus 토픽 작업을 만듭니다.
[New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127(v=azure.200).aspx) |Storage 큐 작업을 만듭니다. 
[Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140(v=azure.200).aspx) |Scheduler 작업을 삭제합니다.  
[Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131(v=azure.200).aspx) |작업 컬렉션을 삭제합니다. 
[Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130(v=azure.200).aspx) |Scheduler HTTP 작업을 수정합니다.
[Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129(v=azure.200).aspx) |작업 컬렉션을 수정합니다. 
[Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143(v=azure.200).aspx) |Service Bus 큐 작업을 수정합니다.  
[Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137(v=azure.200).aspx) |Service Bus 토픽 작업을 수정합니다. 
[Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128(v=azure.200).aspx) |Storage 큐 작업을 수정합니다.   

자세한 내용을 보려면, 다음 중 원하는 cmdlet을 실행하세요.

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## 참고 항목


 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure 스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0914_2016-->