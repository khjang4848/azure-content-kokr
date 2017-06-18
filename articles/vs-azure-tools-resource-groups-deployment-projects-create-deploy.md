<properties
   pageTitle="Azure 리소스 그룹 Visual Studio 프로젝트 | Microsoft Azure"
   description="Visual Studio를 사용하여 Azure 리소스 그룹 프로젝트를 만들고 Azure에 리소스를 배포합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="tomfitz" />

# Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포

Visual Studio 및 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 Azure로 인프라 및 코드를 배포하는 프로젝트를 만들 수 있습니다. 예를 들어 앱에 대한 웹 호스트, 웹 사이트 및 데이터베이스를 정의하고 코드와 함께 해당 인프라를 배포할 수 있습니다. 또는 가상 컴퓨터, 가상 네트워크 및 저장소 계정을 정의하고 가상 컴퓨터에서 실행되는 스크립트와 함께 해당 인프라를 배포할 수 있습니다. **Azure 리소스 그룹** 배포 프로젝트는 하나의 반복 작업에 필요한 모든 리소스를 배포할 수 있습니다. 리소스 배포 및 관리에 대한 자세한 내용은 [Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하세요.

Azure 리소스 그룹 프로젝트는 Azure에 배포한 리소스를 정의하는 Azure Resource Manager JSON 템플릿을 포함합니다. 리소스 관리자 템플릿의 요소에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요. Visual Studio는 이러한 템플릿을 편집할 수 있도록 하고 템플릿으로 작업을 간소화하는 도구를 제공합니다.

이 항목에서는 웹앱 및 SQL Database를 배포합니다. 그러나 모든 종류의 리소스에 대해서도 거의 동일한 단계를 거칩니다. 가상 컴퓨터와 관련된 리소스를 손쉽게 배포할 수 있습니다. Visual Studio는 일반 시나리오를 배포하기 위한 다양한 서로 다른 시작 템플릿을 제공합니다.

이 문서는 Visual Studio 2015 업데이트 2와 .NET 2.9용 Microsoft Azure SDK를 보여 줍니다. Azure SDK 2.9와 함께 Visual Studio 2013을 사용하면 환경이 대부분 동일합니다. Azure SDK 2.6 이상의 버전을 사용할 수 있지만 사용자 인터페이스 환경이 이 문서에 표시된 것과 다를 수 있습니다. 이 단계를 시작하기 전에 최신 버전의 [Azure SDK](https://azure.microsoft.com/downloads/)를 설치하는 것이 좋습니다.

## Azure 리소스 그룹 프로젝트 만들기

이 절차에서 **웹앱 + SQL** 템플릿으로 Azure 리소스 그룹 프로젝트를 만듭니다.

1. Visual Studio에서 **파일**, **새 프로젝트**를 선택하고 **C#** 또는 **Visual Basic**을 선택합니다. 그런 다음 **클라우드**, **Azure 리소스 그룹** 프로젝트를 차례로 선택합니다.

    ![Cloud Deployment 프로젝트](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Azure 리소스 관리자에 배포하려는 템플릿을 선택합니다. 배포하려는 프로젝트의 유형에 따라 다양한 옵션이 있습니다. 이 항목의 경우 **웹앱 + SQL** 템플릿을 선택합니다.

    ![템플릿 선택](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    선택한 템플릿은 시작 지점일 뿐이며 시나리오를 충족하는 리소스를 추가 및 제거할 수 있습니다.

    >[AZURE.NOTE] Visual Studio은 온라인에서 사용 가능한 템플릿 목록을 검색합니다. 목록은 변경될 수 있습니다.

    Visual Studio는 웹앱 및 SQL 데이터베이스에 대한 리소스 그룹 배포 프로젝트를 만듭니다.

1. 만든 항목을 보려면 배포 프로젝트에서 노드를 확장합니다.

    ![노드 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    이 예제에 대한 웹앱 + SQL 템플릿을 선택한 후 다음 파일을 참조합니다.

    |파일 이름|설명|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Azure Resource Manager를 배포할 PowerShell 명령을 호출하는 PowerShell 스크립트입니다.<br />**참고** 이 PowerShell 스크립트는 Visual Studio에서 템플릿을 배포하는 데 사용됩니다. 이 스크립트를 변경하면 Visual Studio의 배포에 영향이 있으므로 신중해야 합니다.|
    |WebSiteSQLDatabase.json|Azure에 배포하려는 인프라를 정의하는 Resource Manager 템플릿 및 배포하는 동안 제공할 수 있는 매개 변수입니다. 또한 Resource Manager가 리소스를 올바른 순서로 배포하도록 리소스 간의 종속성을 정의합니다.|
    |WebSiteSQLDatabase.parameters.json|템플릿에 필요한 값을 포함하는 매개 변수 파일입니다. 각 배포를 사용자 지정하는 값을 전달합니다.|

    모든 리소스 그룹 배포 프로젝트는 이러한 기본 파일을 포함합니다. 다른 프로젝트는 다른 기능을 지원하는 추가 파일을 포함할 수 있습니다.

## 리소스 관리자 템플릿 사용자 지정

배포하려는 리소스를 설명하는 JSON 템플릿 파일을 수정하여 배포 프로젝트를 사용자 지정할 수 있습니다. JSON은 JavaScript Object Notation의 약어로, 쉽게 작업할 수 있는 직렬화된 데이터 형식입니다. JSON 파일은 각 파일의 위쪽에서 참조하는 스키마를 사용합니다. 스키마를 이해하려면 다운로드하여 분석할 수 있습니다. 스키마는 유효한 요소, 필드의 형식 및 포맷, 열거형 값의 가능한 값 등을 정의합니다. Resource Manager 템플릿의 요소에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

템플릿에서 작업하려면 **WebSiteSQLDatabase.json**을 엽니다.

Visual Studio 편집기는 Resource Manager 템플릿 편집에 도움이 되는 도구를 제공합니다. **JSON 개요** 창을 통해 템플릿에 정의된 요소를 쉽게 볼 수 있습니다.

![JSON 개요 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

개요에서 요소 중 하나를 선택하면 템플릿의 해당 부분으로 이동하고 해당 JSON을 강조 표시합니다.

![JSON로 이동](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

JSON 개요 창의 맨 위에 있는 **리소스 추가** 버튼을 선택하거나 **리소스**를 마우스 오른쪽 단추로 클릭하고 **새 리소스 추가**를 선택하여 리소스를 추가할 수 있습니다.

![리소스 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

이 자습서의 경우 **저장소 계정**을 선택하고 이름을 지정합니다. 11개 미만의 문자이며 숫자 및 소문자만을 포함하는 이름을 제공합니다.

![저장소 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

추가된 리소스 뿐만 아니라 형식 저장소 계정에 대한 매개 변수 및 저장소 계정 이름에 대한 변수입니다.

![개요 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

**storageType** 매개 변수는 허용되는 형식 및 기본 형식을 사용하여 미리 정의됩니다. 이러한 값을 유지하거나 시나리오에 대해 편집할 수 있습니다. 이 템플릿을 통해 **Premium\_LRS** 저장소 계정을 배포하지 않으려는 경우 허용된 형식에서 제거합니다.

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio는 또한 템플릿을 편집하는 경우 사용 가능한 속성을 이해할 수 있도록 intellisense를 제공합니다. 예를 들어 앱 서비스 계획에 대한 속성을 편집하려면 **HostingPlan** 리소스로 이동하고 **속성**에 대한 값을 추가합니다. intellisense는 사용 가능한 값을 표시하고 해당 값에 대한 설명을 제공합니다.

![intellisense 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

**numberOfWorkers**를 1로 설정할 수 있습니다.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Azure에 리소스 그룹 프로젝트 배포

이제 프로젝트를 배포할 준비가 되었습니다. Azure 리소스 그룹 프로젝트를 배포할 때 Azure 리소스 그룹에 배포합니다. 리소스 그룹은 공통 수명 주기를 공유하는 리소스의 논리적 그룹화입니다.

1. 배포 프로젝트 노드의 바로 가기 메뉴에서 **배포** > **새 배포**를 선택합니다.

    ![배포, 새 배포 메뉴 항목](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    **리소스 그룹에 배포** 대화 상자가 나타납니다.

    ![리소스 그룹에 배포 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. **리소스 그룹** 드롭다운 상자에서 기존 리소스 그룹을 선택하거나 새 항목을 만듭니다. 리소스 그룹을 만들려면 **리소스 그룹** 드롭다운 상자를 열고 **새로 만들기**를 선택합니다.

    ![리소스 그룹에 배포 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    **리소스 그룹 만들기** 대화 상자가 나타납니다. 그룹에 이름 및 위치를 지정하고 **만들기** 단추를 선택합니다.

    ![리소스 그룹 만들기 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. **매개 변수 편집** 단추를 선택하여 배포에 대한 매개 변수를 편집합니다.

    ![매개 변수 편집 단추](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/edit-parameters.png)

1. 비어 있는 매개 변수에 값을 제공하고 **저장** 단추를 선택합니다. 비어 있는 매개 변수는 **hostingPlanName**, **administratorLogin**, **administratorLoginPassword** 및 **databaseName**입니다.

    **hostingPlanName**은 만들려는 [App Service 계획](./app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)의 이름을 지정합니다.
    
    **administratorLogin**은 SQL Server 관리자의 사용자 이름을 지정합니다. **sa** 또는 **admin**과 같은 일반 관리자 이름을 사용하지 않습니다.
    
    **administratorLoginPassword**는 SQL Server 관리자의 암호를 지정합니다. **암호를 매개 변수 파일에 일반 텍스트로 저장** 옵션은 안전하지 않으므로 이 옵션을 선택하지 않습니다. 암호가 일반 텍스트로 저장되지 않기 때문에 배포하는 동안 다시 이 암호를 제공해야 합니다.
    
    **databaseName**은 만들 데이터베이스의 이름을 지정합니다.

    ![매개 변수 편집 대화 상자](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
1. **배포** 단추를 선택하여 Azure에 프로젝트를 배포합니다. Visual Studio 인스턴스의 외부에서 PowerShell 콘솔이 열립니다. 메시지가 표시되면 PowerShell 콘솔에 SQL Server 관리자 암호를 입력합니다. **PowerShell 콘솔은 다른 항목 뒤에 숨겨지거나 작업 표시줄에서 최소화될 수 있습니다.** 이 콘솔을 찾아서 암호를 제공합니다.

    >[AZURE.NOTE] Visual Studio에서는 Azure PowerShell cmdlet을 설치하도록 요청할 수 있습니다. 리소스 그룹을 성공적으로 배포하려면 Azure PowerShell cmdlet이 필요합니다. 메시지가 표시되면 설치합니다.
    
1. 배포는 몇 분 정도가 걸릴 수 있습니다. **출력** 창에 배포의 상태가 표시됩니다. 배포가 완료되면 마지막 메시지는 다음과 유사한 내용으로 성공적인 배포를 나타냅니다.

        ... 
        18:00:58 - Successfully deployed template 'c:\users\user\documents\visual studio 2015\projects\azureresourcegroup1\azureresourcegroup1\templates\websitesqldatabase.json' to resource group 'DemoSiteGroup'.


1. 브라우저에서 [Azure Portal](https://portal.azure.com/)을 열고 계정에 로그인합니다. 리소스 그룹을 보려면 **리소스 그룹** 및 배포한 리소스 그룹을 선택합니다.

    ![그룹 선택](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. 배포된 리소스가 모두 표시됩니다. 저장소 계정의 이름은 해당 리소스를 추가할 때 지정한 것과 일치하지 않은지 확인합니다. 저장소 계정은 고유해야 합니다. 고유한 이름을 제공하기 위해 템플릿은 자동으로 사용자가 제공한 이름에 문자의 문자열을 추가합니다.

    ![리소스 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. 프로젝트를 변경하고 다시 배포할 경우, Azure 리소스 그룹 프로젝트의 바로 가기 메뉴에서 기존 리소스 그룹을 선택합니다. 바로 가기 메뉴에서 **배포**를 선택한 다음 배포한 리소스 그룹을 선택합니다.

    ![배포된 Azure 리소스 그룹](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## 인프라를 사용하여 코드 배포

이 시점에서 앱에 대한 인프라를 배포했지만 프로젝트와 함께 배포된 실제 코드가 없습니다. 이 항목에서는 배포하는 동안 웹앱 및 SQL Database 테이블을 배포하는 방법을 보여 줍니다. 웹앱 대신 가상 컴퓨터를 배포하는 경우 배포의 일부로 컴퓨터에 일부 코드를 실행하려고 합니다. 웹앱에 대한 코드 배포 또는 가상 컴퓨터 설정을 위한 절차는 거의 동일합니다.

1. Visual Studio 솔루션에 프로젝트를 추가합니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트**를 선택합니다.

    ![프로젝트 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. **ASP.NET 웹 응용 프로그램**을 추가합니다.

    ![웹앱 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. 리소스 그룹 프로젝트는 해당 작업을 수행하므로 **MVC**를 선택하고 **클라우드에서 호스트**에 대한 필드를 선택 취소합니다.

    ![MVC 선택](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Visual Studio에서 웹앱을 만든 후에 솔루션에 두 프로젝트가 모두 표시됩니다.

    ![프로젝트 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. 이제, 리소스 그룹 프로젝트가 새 프로젝트를 인식하는지 확인해야 합니다. 리소스 그룹 프로젝트(AzureResourceGroup1)로 돌아갑니다. **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

    ![참조 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. 만든 웹앱 프로젝트를 선택합니다.

    ![참조 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    참조를 추가하여 리소스 그룹 프로젝트에 웹앱 프로젝트에 연결하고 자동으로 세 가지 주요 속성을 설정합니다. 참조를 위한 **속성** 창에서 이러한 속성을 확인합니다.

      ![참조 보기](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
    
    속성은 다음과 같습니다.

    - **추가 속성**은 Azure Storage에 푸시되는 웹 배포 패키지 준비 위치를 포함합니다. 폴더(ExampleApp) 및 파일(package.zip)을 적어둡니다. 앱을 배포할 때 이러한 값을 매개 변수로 제공합니다.
    - **파일 경로 포함**은 패키지를 만들 경로를 포함합니다. **대상 포함**은 배포가 실행할 명령을 포함합니다.
    - **빌드;패키지**의 기본값을 통해 배포는 웹 배포 패키지(package.zip)를 빌드하고 만들 수 있습니다.
    
    배포는 패키지를 만드는 속성에서 필요한 정보를 얻게 되므로 게시 프로필이 필요하지 않습니다.
      
1. 템플릿에 리소스를 추가합니다.

    ![리소스 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. 이번에는 **Web Apps에 대한 웹 배포**를 선택합니다.

    ![웹 배포 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. 리소스 그룹에 리소스 그룹 프로젝트를 다시 배포합니다. 이번에는 몇 가지 새로운 매개 변수가 있습니다. 자동으로 생성되므로 **\_artifactsLocation** 또는 **\_artifactsLocationSasToken**에 대한 값을 제공할 필요가 없습니다. 그러나 폴더와 파일 이름을 배포 패키지를 포함하는 경로로 설정해야 합니다(다음 이미지에서 **ExampleAppPackageFolder** 및 **ExampleAppPackageFileName**로 나타남). 앞서 참조 속성에서 확인한 값을 제공합니다(**ExampleApp** 및 **package.zip**).

    ![웹 배포 추가](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    **아티팩트 저장소 계정**의 경우 이 리소스 그룹과 함께 배포된 계정을 선택합니다.
    
1. 배포가 완료된 후에 포털에서 웹앱을 선택합니다. URL을 선택하여 사이트를 찾습니다.

    ![사이트 찾아보기](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. 기본 ASP.NET 앱을 성공적으로 배포했습니다.

    ![배포된 앱 표시](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## 다음 단계

- 포털을 통한 리소스 관리에 대한 내용은 [Azure Portal을 사용하여 Azure 리소스 관리](./azure-portal/resource-group-portal.md)를 참조하세요.
- 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->