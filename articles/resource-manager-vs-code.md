<properties
   pageTitle="Resource Manager 템플릿을 포함한 VS Code 사용 | Microsoft Azure"
   description="Visual Studio Code를 설정하여 Azure Resource Manager 템플릿을 만드는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# Visual Studio Code에서 Azure Resource Manager 템플릿으로 작업

Azure Resource Manager 템플릿은 리소스 및 관련 종속성을 설명하는 JSON 파일입니다. 이러한 파일 크거나 복잡할 수 있으므로 도구 지원이 중요합니다. Visual Studio Code는 새롭고 간단한 오픈 소스 크로스 플랫폼 코드 편집기입니다. [새 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)을 통해 Resource Manager 템플릿을 만들고 편집하도록 지원합니다. VS Code는 어디서나 실행할 수 있고 Resource Manager 템플릿을 배포하려는 경우가 아니면 인터넷 액세스가 필요하지 않습니다.

VS Code가 아직 설치되지 않은 경우 [https://code.visualstudio.com/](https://code.visualstudio.com/)에서 설치할 수 있습니다.

## Resource Manager 확장 설치

VS Code에서 JSON 템플릿을 사용하려면 확장을 설치해야 합니다. 다음 단계는 Resource Manager JSON 템플릿에 대한 언어 지원을 다운로드하고 설치합니다.

1. VS Code 시작
2. 빠른 열기 열기(Ctrl+P)
3. 다음 명령을 실행합니다.

        ext install azurerm-vscode-tools

4. 확장을 사용할 수 있다는 메시지가 나타나면 VS Code를 다시 시작합니다.

 작업을 완료했습니다!

## Resource Manager 코드 조각 설정

이전 단계에서는 도구 지원을 설치했지만 이제 VS Code를 구성하여 JSON 템플릿 코드 조각을 사용해야 합니다.

1. [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) 리포지토리에서 파일의 내용을 클립보드에 복사합니다.
2. VS Code 시작
3. VS 코드에서 **파일** -> **기본 설정** -> **사용자 코드 조각** -> **JSON**을 탐색하거나 **기본 설정: 코드 조각**을 선택하기 위해 **F1**을 선택하고 **기본 설정**을 입력하여 JSON 코드 조각 파일을 열 수 있습니다.

    ![기본 설정 코드 조각](./media/resource-manager-vs-code/preferences-snippets.png)

    옵션에서 **JSON**을 선택합니다.

    ![json 선택](./media/resource-manager-vs-code/select-json.png)

4. 마지막 "}"의 앞에서 사용자 코드 조각 파일에 1단계의 파일 내용을 붙여 넣습니다.
5. JSON 항목이 정상으로 보이고 물결 기호가 없는지를 확인합니다.
6. 사용자 코드 조각 파일을 저장하고 닫습니다.

이제 Resource Manager 코드 조각을 사용하여 시작하는 데 필요한 작업을 마쳤습니다. 다음으로 이 설정을 테스트합니다.

## VS Code에서 템플릿 사용

템플릿으로 작업을 시작하는 가장 쉬운 방법은 [Github](https://github.com/Azure/azure-quickstart-templates)에서 사용할 수 있는 빠른 시작 템플릿 중 하나를 선택하거나 고유한 템플릿을 사용하는 것입니다. 포털을 통해 리소스 그룹에 대한 [템플릿을 쉽게 내보낼](resource-manager-export-template.md) 수 있습니다.

1. 리소스 그룹에서 템플릿을 내보낸 경우 VS Code에서 추출된 파일을 엽니다.

    ![파일 표시](./media/resource-manager-vs-code/show-files.png)

2. 몇 가지 추가 리소스를 편집하고 추가할 수 있도록 template.json 파일을 엽니다. **"resources": [** 다음에 enter 키를 눌러서 새 줄을 시작합니다. **arm**을 입력하는 경우 옵션 목록이 제공됩니다. 이러한 옵션은 설치한 템플릿 코드 조각입니다. 다음과 같이 표시되어야 합니다.

    ![코드 조각 표시](./media/resource-manager-vs-code/type-snippets.png)

3. 원하는 코드 조각을 선택합니다. 이 문서의 경우 **arm-ip**를 선택하여 새 공용 IP 주소를 만듭니다. 새로 만든 리소스의 닫는 괄호("}") 다음에 쉼표를 추가하여 템플릿 구문이 유효하도록 합니다.

     ![쉼표 추가](./media/resource-manager-vs-code/add-comma.png)

4. VS Code에는 기본 제공 IntelliSense가 있습니다. 템프릿을 편집할 때 VS Code에서는 사용할 수 있는 값을 제안합니다. 예를 들어 변수 섹션을 템플릿에 추가하려면 **""**(두 개의 큰따옴표)를 추가하고 따옴표 사이에 **Ctrl+스페이스바**를 선택합니다. **변수**를 비롯한 옵션이 나타납니다.

    ![변수 추가](./media/resource-manager-vs-code/add-variables.png)

5. IntelliSense에서는 사용 가능한 값 또는 함수를 제안할 수도 있습니다. 매개 변수 값을 속성으로 설정하려면 **""** 및 **Ctr+스페이스바**를 사용하여 식을 만듭니다. 함수 이름을 입력하기 시작할 수 있습니다. 원하는 함수를 발견하면 **탭**을 선택합니다.

    ![매개 변수 추가](./media/resource-manager-vs-code/select-parameters.png)

6. 함수 내에서 **Ctrl+스페이스바**를 다시 선택하여 템플릿 내에서 사용할 수 있는 매개 변수 목록을 참조합니다.

    ![매개 변수 추가](./media/resource-manager-vs-code/select-avail-parameters.png)

7. 템플릿에 스키마 유효성 검사 문제가 있는 경우 편집기에 친숙한 물결선이 표시됩니다. **Ctrl+Shift+M**을 입력하거나 왼쪽 아래 상태 표시줄에서 문자를 선택하여 오류 및 경고 목록을 볼 수 있습니다.

    ![errors](./media/resource-manager-vs-code/errors.png)

    템플릿의 유효성 검사는 구문 문제를 감지하도록 도울 수 있습니다. 그러나 무시할 수 있는 오류도 나타날 수 있습니다. 경우에 따라 편집기는 최신 상태가 아닌 스키마와 템플릿을 비교합니다. 따라서 정확하다는 사실을 알고 있더라도 오류를 보고합니다. 예를 들어 함수가 최근에 Resource Manager에 추가되었지만 스키마가 업데이트되지 않았다고 가정합니다. 배포하는 동안 함수가 올바르게 작동하지 않았음에도 불구하고 편집기는 오류를 보고합니다.

    ![오류 메시지](./media/resource-manager-vs-code/unrecognized-function.png)

## 새 리소스 배포

템플릿이 준비되면 다음 지침을 따라 새 리소스를 배포할 수 있습니다.

### Windows

1. PowerShell 명령 프롬프트 열기
2. 로그인하려면 다음을 입력합니다.

        Login-AzureRmAccount 

3. 구독이 여러 개인 경우 사용할 구독 목록을 가져옵니다.

        Get-AzureRmSubscription

    그리고 사용할 구독을 선택합니다.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. parameters.json 파일에서 매개 변수 업데이트
5. Azure에서 템플릿을 배포하려면 Deploy.ps1 실행

### OSX/Linux

1. 터미널 창 열기
2. 로그인하려면 다음을 입력합니다.

        azure login 

3. 구독이 여러 개인 경우 사용할 올바른 구독을 선택합니다.

        azure account set <subscriptionNameOrId> 

4. parameters.json 파일에서 매개 변수를 업데이트합니다.
5. 템플릿을 배포하려면 다음을 실행합니다.

        azure group deployment create -f <PathToTemplate> 

## 다음 단계

- 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 템플릿 함수에 대해 알아보려면 [Azure Resource Manager 템플릿 함수](resource-group-template-functions.md)를 참조하세요.
- Visual Studio 코드를 사용한 작업의 더 많은 예제는 [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 연결 [데모](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)의 [Visual Studio Code를 사용한 클라우드 앱 빌드](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code)를 참조하세요. HealthClinic.biz 데모에서 빠른 시작을 더 살펴보려면 [Azure 개발자 도구 빠른 시작](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->