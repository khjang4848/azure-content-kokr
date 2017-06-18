<properties 
	pageTitle="리소스 관리자 포함 Azure PowerShell | Microsoft Azure" 
	description="Azure PowerShell을 사용하여 여러 리소스를 Azure에 리소스로 배포하는 작업을 소개합니다." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/18/2016" 
	ms.author="tomfitz"/>

# Azure 리소스 관리자로 Azure PowerShell 사용

> [AZURE.SELECTOR]
- [포털](azure-portal/resource-group-portal.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST API](resource-manager-rest-api.md)


Azure Resource Manager는 Azure 리소스 수명 주기 컨트롤에 대한 현대적 접근법을 구현합니다. 개별 리소스를 만들어서 관리하는 대신 블로그, 사진 갤러리, SharePoint 포털, wiki 등의 전체 솔루션을 생각해 보십시오. 템플릿(솔루션의 선언적 표현)을 사용하여 해당 솔루션을 지원하는 데 필요한 모든 리소스가 포함된 리소스 그룹을 만듭니다. 그런 다음 해당 리소스 그룹을 논리 단위로 배포하고 관리합니다.

이 자습서에서는 Azure 리소스 관리자에서 Azure PowerShell을 사용하는 방법에 대해 알아봅니다. 솔루션을 배포하고 해당 솔루션을 사용하는 과정을 안내합니다. Azure PowerShell 및 Resource Manager 템플릿을 사용하여 배포할 것입니다.

- SQL 서버 - 데이터베이스 호스팅
- SQL 데이터베이스 - 데이터 저장
- 방화벽 규칙 - 웹 앱을 데이터베이스에 연결하도록 허용
- 앱 서비스 계획 - 웹 앱의 기능과 비용 정의
- 웹 사이트, 웹 앱 실행
- 웹 구성 - 연결 문자열을 데이터베이스에 저장
- 경고 규칙 - 성능 및 오류 모니터링
- App Insights - 자동 크기 조정 설정

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 계정.
  + [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.
  
  + [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
- Azure PowerShell 1.0 이 릴리스에 대한 정보 및 설치 방법은 [Azure PowerShell 설치 및 구성 방법](powershell-install-configure.md)을 참조하세요.

이 자습서는 PowerShell 초보자용으로 설계되었지만, 모듈, cmdlet, 세션 등과 같은 기본 개념을 잘 알고 있다고 가정합니다.

## Cmdlet에 대한 도움말 보기

이 설명서에 나오는 cmdlet에 대한 자세한 도움말을 보려면 Get-Help cmdlet을 사용합니다.

    Get-Help <cmdlet-name> -Detailed

예를 들어 AzureRmResource Cmdlet에 대한 도움말을 보려면 다음과 같이 입력합니다.

    Get-Help Get-AzureRmResource -Detailed

리소스 모듈의 Cmdlet 목록을 도움말 개요와 함께 가져오려면 다음을 입력합니다.

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

출력은 다음 발췌와 유사합니다.

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

cmdlet에 대한 전체 도움말을 가져오려면 다음 형식으로 명령을 입력합니다.

    Get-Help <cmdlet-name> -Full
  
## Azure 계정에 로그인합니다.

솔루션에서 작업을 하기 전에 본인의 계정에 로그인해야 합니다.

Azure 계정에 로그인하려면 **Add-AzureRmAccount** Cmdlet을 사용합니다.

    Add-AzureRmAccount

Cmdlet가 Azure 계정에 대한 로그인 자격 증명을 유도합니다. 로그인한 다음 Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

계정 설정은 만료되므로 자주 새로 고쳐야 합니다. 계정 설정을 새로 고치려면 **Add-AzureRmAccount**를 다시 실행합니다.

>[AZURE.NOTE] Resource Manager 모듈을 사용하려면 Add-AzureRmAccount가 필요합니다. 게시 설정 파일로는 충분하지 않습니다.

여러 구독이 있는 경우 **Set-AzureRmContext** Cmdlet을 사용하여 배포에 사용할 구독 ID를 입력합니다.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## 리소스 그룹 만들기

구독에 리소스를 배포하려면 리소스를 포함하는 리소스 그룹을 만들어야 합니다.

리소스 그룹을 만들려면 **New-AzureRmResourceGroup** Cmdlet을 사용합니다.

이 명령은 **Name** 매개 변수를 사용하여 리소스 그룹에 대한 이름을 지정하고 **Location** 매개 변수를 사용하여 위치를 지정합니다. 이전 섹션에서 발견한 내용을 바탕으로 “서부 미국"을 위치로 사용합니다.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
다음과 유사하게 출력됩니다.

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

리소스 그룹은 성공적으로 만들어졌습니다.

## 솔루션 배포

이 토픽에서는 템플릿을 만드는 방법을 보여 주거나 템플릿의 구조를 설명하지 않습니다. 해당 정보는 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md) 및 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)의 사전 정의된 [SQL 데이터베이스를 사용하는 웹앱을 프로비전](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) 템플릿을 배포합니다.

리소스 그룹이 있고 템플릿이 있으므로 이제 템플릿에 정의된 인프라를 리소스 그룹에 배포할 준비가 되었습니다. **New-AzureRmResourceGroupDeployment** Cmdlet을 사용하여 리소스를 배포합니다. 템플릿에서 여러 기본값을 지정하고 사용자는 그 값을 그대로 사용하면 되므로 이러한 매개 변수의 값을 지정할 필요가 없습니다. 기본 구문은 다음과 같습니다.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

리소스 그룹 및 템플릿 위치를 지정합니다. 템플릿이 로컬 파일인 경우 **-TemplateFile** 매개 변수를 사용하여 템플릿 경로를 지정합니다. **-Mode** 매개 변수를 **Incremental** 또는 **Complete**로 설정할 수 있습니다. 기본적으로 리소스 관리자는 배포하는 동안 증분 업데이트를 수행하므로 **Incremental**을 원하는 경우 **-Mode**를 설정하지 않아도 됩니다. 이러한 배포 모드 간의 차이점을 이해하려면 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

###동적 템플릿 매개 변수

PowerShell에 익숙한 경우 빼기 기호(-)를 입력하고 TAB 키를 눌러 Cmdlet에 사용할 수 있는 매개 변수를 순환시켜 볼 수 있습니다. 이 기능은 템플릿에 정의하는 매개 변수에 대해서도 작동합니다. 템플릿 이름을 입력하자마자 Cmdlet이 템플릿을 인출하고 분석한 다음 템플릿 매개 변수를 명령에 동적으로 추가합니다. 따라서 템플릿 매개 변수 값을 매우 쉽게 지정할 수 있습니다.

명령을 입력하면 누락된 필수 매개 변수 **administratorLoginPassword**를 묻는 메시지가 표시됩니다. 암호를 입력하면 보안 문자열 값이 가려집니다. 이 전략은 암호를 일반 텍스트로 제공할 위험을 제거합니다.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

템플릿이 해당 템플릿을 배포하는 명령의 매개 변수 중 하나와 일치하는 이름을 가진 매개 변수를 포함하는 경우(예: [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) Cmdlet의 **ResourceGroupName** 매개 변수와 동일한 **ResourceGroupName**이라는 매개 변수를 템플릿에 포함) **FromTemplate** 후위가 있는 매개 변수(예: **ResourceGroupNameFromTemplate**)에 대한 값을 제공하라는 메시지가 표시됩니다. 일반적으로 배포 작업에 사용되는 매개 변수와 동일한 이름을 가진 매개 변수를 명명하지 않음으로써 이러한 혼동이 발생하지 않도록 해야 합니다.

명령이 실행되고 리소스가 만들어질 때 메시지를 반환합니다. 결국 배포 결과를 표시합니다.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

단 몇 개의 단계를 수행하여 복잡한 웹 사이트에 필요한 리소스를 만들어 배포했습니다.

### 로그 디버그 정보

템플릿을 배포할 때, **New-AzureRmResourceGroupDeployment**가 실행 중인 동안 **-DeploymentDebugLogLevel** 매개 변수를 지정하여 요청 및 응답에 대한 추가 정보를 기록할 수 있습니다. 이 정보는 배포 오류를 해결하는 데 도움이 될 수 있습니다. 기본값은 요청 또는 응답 콘텐츠를 기록하지 않는다는 의미의 **없음**입니다. 요청, 응답 또는 둘 모두의 콘텐츠를 기록하도록 지정할 수 있습니다. 배포 문제 해결 및 로깅 디버그 정보에 대한 자세한 내용은 [Azure PowerShell을 사용하여 리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-powershell.md)을 참조하세요. 다음은 배포에 대한 요청 콘텐츠 및 응답 콘텐츠를 기록하는 예입니다.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] DeploymentDebugLogLevel 매개 변수를 설정할 때, 배포하는 동안 전달할 정보 유형을 신중히 고려하세요. 요청 또는 응답에 대한 정보를 로깅하게 되면 배포 작업을 통해 검색되는 중요한 데이터가 노출될 가능성이 있기 때문입니다.


## 리소스 그룹에 대한 정보 가져오기

리소스 그룹을 만든 후 리소스 관리자 모듈에서 Cmdlet을 사용하여 리소스 그룹을 관리할 수 있습니다.

- 구독에서 리소스 그룹을 가져오려면 **Get-AzureRmResourceGroup** Cmdlet를 사용합니다.

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	그러면 다음 정보가 반환됩니다.

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	리소스 그룹 이름을 지정하지 않으면 cmdlet이 구독의 모든 리소스 그룹을 반환합니다.

- 리소스 그룹에서 리소스를 가져오려면 **Get-AzureRmResource** cmdlet와 **ResourceGroupName** 매개 변수를 사용합니다. 매개 변수를 사용하지 않고 Find-AzureRmResource를 입력하면 Azure 구독에서 모든 리소스를 가져옵니다.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     다음과 같은 형식의 리소스 목록이 반환됩니다.
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- 태그를 사용하여 구독의 리소스를 논리적으로 구성하고, **Find-AzureRmResource** 및 **Find-AzureRmResourceGroup** Cmdlet으로 리소스를 검색할 수 있습니다.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      태그를 사용하여 더 많은 작업을 수행할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

## 리소스 그룹 추가

리소스 그룹에 리소스를 추가하려면 **New-AzureRmResource** cmdlet를 사용하면 됩니다. 그러나 이 방법으로 리소스를 추가하면 새 리소스가 템플릿에 존재하지 않기 때문에 나중에 혼동을 일으킬 수 있습니다. 기존 템플릿을 다시 배포하는 경우 불완전한 솔루션을 배포할 수 있습니다. 자주 배포하는 경우 새 리소스를 템플릿에 추가하고 다시 배포하는 것이 더 쉽고 안정됩니다.

## 리소스 이동

기존 리소스를 새 리소스 그룹으로 이동할 수 있습니다. 예제를 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## 템플릿 내보내기

기존 리소스 그룹(PowerShell 또는 포털을 비롯한 다른 방법 중 하나를 통해 배포된)의 경우 리소스 그룹에 대 한 Resource Manager 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

1. 모든 인프라가 템플릿에 정의되어 있기 때문에 향후 솔루션 배포를 간단하게 자동화할 수 있습니다.

2. 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해질 수 있습니다.

PowerShell을 통해 리소스 그룹의 현재 상태를 나타내는 템플릿을 생성하거나 특정 배포에 사용된 템플릿을 검색할 수 있습니다.

리소스 그룹을 변경했고 현재 상태의 JSON 표현을 검색해야 하는 경우에는 리소스 그룹에 대한 템플릿을 내보내는 것이 좋습니다. 그러나 생성된 템플릿에는 최소한의 매개 변수만 포함되고 변수는 포함되지 않습니다. 템플릿의 값은 대부분 하드 코드됩니다. 생성된 템플릿을 배포하기 전에, 다양한 환경에 맞게 배포를 사용자 지정할 수 있도록 더 많은 값을 매개 변수로 변환할 수 있습니다.

리소스를 배포하는 데 사용된 실제 템플릿을 살펴보아야 하는 경우에는 특정 배포에 대한 템플릿을 내보내는 것이 좋습니다. 이 템플릿에는 원래 배포에 대해 정의된 모든 매개 변수와 변수가 포함됩니다. 그러나 조직 내 다른 사람이 템플릿에 정의된 범위를 넘어서 리소스 그룹을 변경할 경우 이 템플릿은 리소스 그룹의 현재 상태를 나타내지 않습니다.

> [AZURE.NOTE] 템플릿 내보내기 기능은 미리 보기 버전이며, 템플릿 내보내기를 지원하지 않는 리소스 유형도 있습니다. 템플릿 내보내기를 시도할 때 일부 리소스를 내보내지 못했다는 오류가 표시될 수 있습니다. 필요한 경우 템플릿을 다운로드한 후 템플릿에서 이러한 리소스를 수동으로 정의할 수 있습니다.

###리소스 그룹에서 템플릿 내보내기

리소스 그룹에 대한 템플릿을 보려면 **Export-AzureRmResourceGroup** Cmdlet을 실행합니다.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###배포에서 템플릿 다운로드

특정 배포에 사용된 템플릿을 다운로드하려면 **Save-AzureRmResourceGroupDeploymentTemplate** Cmdlet을 실행합니다.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## 리소스 또는 리소스 그룹 삭제

- 리소스 그룹에서 리소스를 삭제하려면 **Remove-AzureRmResource** cmdlet를 사용합니다. 이 cmdlet은 리소스를 삭제하지만 리소스 그룹은 삭제하지 않습니다.

	이 명령은 TestRG1 리소스 그룹에서 TestSite 웹 사이트를 제거합니다.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- 리소스 그룹을 삭제하려면 **Remove-AzureRmResourceGroup** cmdlet를 사용합니다. 이 cmdlet은 리소스 그룹과 해당 리소스를 삭제합니다.

		Remove-AzureRmResourceGroup -Name TestRG1
		
	삭제를 확인하라는 메시지가 표시됩니다.
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## 배포 스크립트

이 토픽의 이전 배포 예제는 Azure에 리소스를 배포하는 데 필요한 개별 cmdlet만 보여 주었습니다. 다음 예제는 리소스 그룹을 만들고 리소스를 배포하는 배포 스크립트를 보여 줍니다.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## 다음 단계

- 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)을 참조하세요.
- 템플릿 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](./resource-group-template-deploy.md)를 참조하세요.
- 프로젝트 배포의 자세한 예제를 보려면 [Azure에서 예측 가능한 방식으로 microservices 배포](app-service-web/app-service-deploy-complex-application-predictably.md)를 참조하세요.
- 실패한 배포 문제 해결에 대해 알아보려면 [Azure에서 리소스 그룹 배포 문제 해결](./resource-manager-troubleshoot-deployments-powershell.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->