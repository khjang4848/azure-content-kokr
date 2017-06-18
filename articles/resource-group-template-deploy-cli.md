<properties
   pageTitle="Azure CLI 및 템플릿으로 리소스 배포 | Microsoft Azure"
   description="Azure Resource Manager와 Azure CLI를 사용하여 Azure에 리소스를 배포합니다. 리소스는 Resource Manager 템플릿에 정의됩니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# 리소스 관리자 템플릿과 Azure CLI로 리소스 배포

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [포털](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

이 토픽에서는 리소스 관리자 템플릿과 Azure CLI를 사용하여 Azure에 리소스를 배포하는 방법을 설명합니다.

> [AZURE.TIP] 배포 중 발생하는 오류 디버깅에 대한 도움을 받으려면 다음을 참조하세요.
>
> - [Azure CLI를 사용한 배포 작업 보기](resource-manager-troubleshoot-deployments-cli.md)에서 오류를 해결하는 데 유용한 정보를 알 수 있습니다.
> - [Azure Resource Manager로 Azure에 리소스를 배포할 때 발생하는 일반적인 오류 해결](resource-manager-common-deployment-errors.md)에서 일반적인 배포 오류를 해결하는 방법을 알 수 있습니다.

템플릿은 로컬 파일이거나 URI를 통해 사용 가능한 외부 파일일 수 있습니다. 템플릿이 저장소 계정에 상주하는 경우, 템플릿에 대한 액세스를 제한하고 배포 중에 공유 액세스 서명(SAS) 토큰을 제공할 수 있습니다.

## 배포에 대한 빠른 단계

이 문서에는 배포하는 동안 사용할 수 있는 다양한 옵션을 모두 설명합니다. 그러나 간단한 두 개의 명령만 필요한 경우가 많습니다. 신속하게 배포를 시작하려면 다음 명령을 사용합니다.

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

시나리오에 더 적합할 수 있는 배포 옵션에 대한 자세한 내용은 이 문서를 계속 진행합니다.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Azure CLI를 사용하여 배포

이전에 리소스 관리자에서 Azure CLI를 사용하지 않은 경우 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하세요.

1. Azure 계정에 로그인 자격 증명을 제공하면 로그인 결과가 반환됩니다.

        azure login
  
        ...
        info:    login command OK

2. 여러 구독이 있는 경우 배포에 사용할 구독 ID를 제공합니다.

        azure account set <YourSubscriptionNameOrId>

3. Azure 리소스 관리자 모듈로 전환합니다. 새 모드에 대한 확인 메시지가 나타납니다.

        azure config mode arm
   
        info:     New mode is arm

4. 기본 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다. 솔루션에 필요한 위치 및 리소스 그룹의 이름을 제공합니다. 그룹 리소스가 리소스에 대한 메타데이터를 저장하기 때문에 리소스 그룹에 대한 위치를 제공해야 합니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

        azure group create -n ExampleResourceGroup -l "West US"

     새 리소스 그룹에 대한 요약이 반환됩니다.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. 배포를 실행하기 전에 **azure group template validate** 명령을 실행하여 배포 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다(다음 단계에 표시됨).

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. 리소스 그룹에 리소스를 배포하려면 다음 명령을 실행하고 필요한 매개 변수를 제공합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름, 템플릿의 경로 또는 URL 및 시나리오에 필요한 기타 매개 변수가 포함됩니다.
   
     매개 변수 값을 제공하는 옵션에는 다음 세 가지 옵션이 있습니다.

     1. 인라인 매개 변수 및 로컬 템플릿을 사용합니다. 각 매개 변수는 `"ParameterName": { "value": "ParameterValue" }` 형식을 사용합니다. 다음 예제에서는 이스케이프 문자를 사용한 매개 변수를 보여 줍니다.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. 인라인 매개 변수 및 템플릿 링크를 사용합니다.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. 매개 변수 파일을 사용합니다. 템플릿 파일에 대한 정보는 [매개 변수 파일](./#parameter-file)을 참조하세요.
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     위 세 가지 방법 중 하나를 통해 리소스를 배포하면 배포 요약이 표시됩니다.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     전체 배포를 실행하려면 **mode**를 **Complete**로 설정합니다. 이 모드를 사용할 때는 템플릿에 없는 리소스를 실수로 삭제할 수 있으므로 주의해야 합니다.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. 배포 오류를 해결하는 데 유용한 배포 관련 추가 정보를 기록하고 싶다면 **debug-setting** 매개 변수를 사용합니다. 요청 콘텐츠와 응답 콘텐츠 중 하나 또는 둘 다가 배포 작업과 함께 기록되도록 지정할 수 있습니다.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## SAS 토큰으로 저장소에서 템플릿 배포

SAS 토큰으로 배포 중에 저장소 계정에 템플릿을 추가하고 이를 연결할 수 있습니다.

> [AZURE.IMPORTANT] 아래 단계를 따르면 템플릿을 포함한 blob은 계정 소유자만 액세스할 수 있습니다. 그러나 blob용 SAS 토큰을 생성하면 해당 blob은 해당 URI를 가진 사람이면 누구나 액세스할 수 있습니다. 다른 사용자가 URI를 가로채는 경우, 그 사용자가 템플릿에 액세스할 수 있습니다. SAS 토큰을 사용하는 것은 템플릿에 액세스를 제한하는 좋은 방법이지만 템플릿에 암호와 같은 민감한 데이터를 직접 입력하지 말아야 합니다.

### 저장소 계정에 개인 템플릿 추가

다음 단계에서는 템플릿용 저장소 계정을 설정합니다.

1. 리소스 그룹을 만듭니다.

        azure group create -n "ManageGroup" -l "westus"

2. 저장소 계정을 만듭니다. 저장소 계정 이름은 Azure에 1개뿐이어야 하므로 해당 계정에 고유한 이름을 입력합니다.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. 저장소 계정과 키 변수를 설정합니다.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. 컨테이너를 만듭니다. 권한은 **Off**로 설정합니다. 즉, 이 컨테이너는 소유자만 액세스할 수 있습니다.

        azure storage container create --container templates -p Off 
        
4. 컨테이너에 템플릿을 주가합니다.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### 배포하는 동안 SAS 토큰 제공

저장소 계정에 개인 템플릿을 배포하려면 SAS 토큰을 검색하고 해당 템플릿의 URI에 포함합니다.

1. 읽기 권한과 만료 기간이 있는 SAS 토큰을 만들어서 액세스를 제한합니다. 배포를 완료할 만큼 충분한 여유를 두고 만료 기간을 설정합니다. SAS 토큰을 포함한 템플릿의 전체 URI를 검색합니다.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. SAS 토큰을 포함한 URI를 제공하여 템플릿을 배포합니다.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

연결된 템플릿에 SAS 토큰을 사용하는 예제는 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## 다음 단계
- .NET 클라이언트 라이브러리를 통한 리소스 배포의 예제를 보려면 [.NET 라이브러리 및 템플릿을 사용하여 리소스 배포](virtual-machines/virtual-machines-windows-csharp-template.md)를 참조하세요.
- 템플릿에서 매개 변수를 정의하려면 [템플릿 작성](resource-group-authoring-templates.md#parameters)을 참조하세요.
- 다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.
- 보안 값을 전달하기 위한 KeyVault 참조를 사용하는 방법에 관한 자세한 내용은 [배포 중 보안 값 전달](resource-manager-keyvault-parameter.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->