<properties 
	pageTitle="Azure 포털을 사용하여 Azure 리소스 배포 | Microsoft Azure" 
	description="Azure 포털과 Azure 리소스 관리를 사용하여 리소스를 배포합니다." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="tomfitz"/>

# 리소스 관리자 템플릿과 Azure 포털로 리소스 배포

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [포털](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

이 항목에서는 [Azure Resource Manager](resource-group-overview.md)를 포함한 [Azure 포털](https://portal.azure.com)을 사용하여 Azure 리소스를 배포하는 방법을 보여 줍니다. 리소스 관리에 관한 자세한 내용은 [포털을 통한 Azure 리소스 관리](./azure-portal/resource-group-portal.md)를 참조하세요.

현재 일부 서비스에서만 포털이나 리소스 관리자를 지원합니다. 이러한 서비스의 경우 [클래식 포털](https://manage.windowsazure.com)을 사용해야 합니다. 각 서비스의 상태는 [Azure 포털 가용성 차트](https://azure.microsoft.com/features/azure-portal/availability/)를 참조하세요.

## 리소스 그룹 만들기

1. 빈 리소스 그룹을 만들려면 **새로 만들기** > **관리** > **리소스 그룹**을 선택합니다.

    ![빈 소스 그룹 만들기](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. 이름과 위치를 지정하고 필요한 경우 구독을 선택합니다. 그룹 리소스가 리소스에 대한 메타데이터를 저장하기 때문에 리소스 그룹에 대한 위치를 제공해야 합니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

    ![그룹 값 설정](./media/resource-group-template-deploy-portal/set-group-properties.png)

## 마켓플레이스에서 리소스 배포

리소스 그룹을 만들면 마켓플레이스에서 리소스 그룹으로 리소스를 배포할 수 있습니다. 마켓플레이스는 일반적인 시나리오에 사전 정의된 솔루션을 제공합니다.

1. 배포를 시작하려면 **새로 만들기**와 배포할 리소스 종류를 선택합니다. 그런 다음 배포할 특정 버전의 리소스를 찾습니다.

    ![리소스 배포](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. 배포하려는 특정 솔루션이 보이지 않는다면 마켓플레이스를 검색할 수 있습니다.

    ![마켓플레이스 검색](./media/resource-group-template-deploy-portal/search-resource.png)

3. 선택한 리소스 유형에 따라 배포하기 전에 관련 속성의 컬렉션을 설정합니다. 리소스 유형에 따라 달라지므로 해당 옵션은 여기에 표시되지 않습니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 웹앱을 만들고 앞에서 만든 리소스 그룹에 배포하는 방법을 보여 줍니다.

    ![리소스 그룹 만들기](./media/resource-group-template-deploy-portal/select-existing-group.png)

    또는 리소스를 배포할 때 리소스 그룹을 만들도록 결정할 수 있습니다. **새로 만들기**를 선택하고 리소스 그룹에 이름을 지정합니다.

    ![새 리소스 그룹 만들기](./media/resource-group-template-deploy-portal/select-new-group.png)

4. 배포가 시작됩니다. 배포에 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 알림이 표시됩니다.

    ![알림 보기](./media/resource-group-template-deploy-portal/view-notification.png)

5. 리소스를 배포한 후에 리소스 그룹 블레이드에서 **추가** 명령으로 더 많은 리소스를 추가할 수 있습니다.

    ![리소스 추가](./media/resource-group-template-deploy-portal/add-resource.png)

## 사용자 지정 템플릿에서 배포

배포를 실행하려고 하지만 마켓플레이스에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.

1. 포털을 통해 사용자 지정된 템플릿을 배포하려면 **새로 만들기**를 선택하고 옵션 중에서 선택할 수 있을 때까지 **템플릿 배포**를 검색합니다.

    ![템플릿 배포 찾기](./media/resource-group-template-deploy-portal/search-template.png)

2. 사용 가능한 리소스에서 **템플릿 배포**를 선택합니다.

    ![템플릿 배포 선택](./media/resource-group-template-deploy-portal/select-template.png)

3. 템플릿 배포를 실행한 후 사용자 지정이 가능한 빈 템플릿을 엽니다.

    ![템플릿 만들기](./media/resource-group-template-deploy-portal/show-custom-template.png)

    편집기에서 배포하고자 하는 리소스를 정의하는 JSON 구문을 추가합니다. 완료되면 **저장**을 선택합니다. JSON 구문 작성에 대한 지침은 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요.

    ![템플릿 편집](./media/resource-group-template-deploy-portal/edit-template.png)

4. 또는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)에서 기존 템플릿을 선택할 수 있습니다. 이러한 템플릿은 커뮤니티에서 제공합니다. 커뮤니티에서는 여러 일반적인 시나리오를 다루며, 여러분이 배포하려는 것과 비슷한 템플릿을 누군가가 이미 추가했을 수도 있습니다. 템플릿을 검색하여 현재 시나리오와 일치하는 항목을 찾을 수 있습니다.

    ![빠른 시작 템플릿 선택](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    편집기에서 선택한 템플릿을 볼 수 있습니다.

5. 다른 모든 값을 입력하고 **만들기**를 선택하여 템플릿을 배포합니다.

    ![템플릿 배포](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## 계정에 저장된 템플릿에서 리소스 배포

포털을 사용하면 Azure 계정에 템플릿을 저장했다가 나중에 다시 배포할 수 있습니다. 저장된 템플릿에 관한 자세한 내용은 [Azure 포털에서 개인 템플릿 시작하기](./marketplace-consumer/mytemplates-getstarted.md)를 참조하세요.

1. 저장된 템플릿을 찾으려면 **찾아보기** > **템플릿**을 선택합니다.

    ![템플릿 찾아보기](./media/resource-group-template-deploy-portal/browse-templates.png)

2. 계정에 저장된 템플릿 목록에서 작업하고자 하는 템플릿을 선택합니다.

    ![저장된 템플릿](./media/resource-group-template-deploy-portal/saved-templates.png)

3. **배포**를 선택하고 저장된 템플릿을 다시 배포합니다.

    ![저장된 템플릿 배포](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## 다음 단계

- 감사 로그를 보려면 [Resource Manager로 작업 감사](resource-group-audit.md)를 참조하세요.
- 배포 오류를 해결하려면 [Azure 포털을 사용하여 리소스 그룹 배포 문제 해결](resource-manager-troubleshoot-deployments-portal.md)을 참조하세요.
- 배포 또는 리소스 그룹에서 템플릿을 검색하려면 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

<!---HONumber=AcomDC_0921_2016-->