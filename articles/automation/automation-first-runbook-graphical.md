<properties
    pageTitle="Azure 자동화의 내 첫 번째 그래픽 Runbook | Microsoft Azure"
    description="자습서는 간단한 그래픽 runbook의 생성, 테스트, 게시를 단계별로 안내합니다."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
	keywords="runbook, runbook 템플릿, runbook 자동화, azure runbook"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/06/2016"
    ms.author="magoedte;bwren"/>

# 내 첫 번째 그래픽 Runbook

> [AZURE.SELECTOR] - [Graphical](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

이 자습서는 Azure 자동화에서 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. 런북 작업의 상태를 추적하는 방법을 설명하는 동안 테스트하고 게시할 단순한 런북부터 시작하겠습니다. 그 다음, 런북을 실제로 Azure 리소스 관리를 통해 수정합니다. 이 경우에는 Azure 가상 컴퓨터를 가동합니다. 그리고 runbook 매개 변수 및 조건부 링크를 추가하여 runbook을 더욱 강력하게 만듭니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

-	동작합니다. 계정이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 <a href="/pricing/free-account/" target="_blank">[무료 계정을 등록](https://azure.microsoft.com/free/)할 수 있습니다.
-	runbook을 보관하고 Azure 리소스를 인증할 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md). 이 계정은 가상 컴퓨터를 시작하고 중지할 수 있는 권한이 있어야 합니다.
-	Azure 가상 컴퓨터. 프로덕션이 되지 않게하기 위해 이 가상 컴퓨터를 중지하고 시작합니다.


## 1 단계-새 runbook 만들기

먼저 *Hello World*라는 텍스트를 출력하는 단순한 Runbook을 생성할 것입니다.

1.	Azure 포털에서 자동화 계정을 엽니다. 자동화 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자는 일부 자산이 이미 있어야 합니다. 대부분의 자산들은 새 자동화 계정에 자동적으로 포함되 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.
2.	**Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.<br>![Runbook 컨트롤](media/automation-first-runbook-graphical/runbooks-control.png)
3.	**runbook 추가**버튼을 클릭하고 **새 runbook 만들기**를 클릭하여 새 runbook을 만듭니다.
4.	Runbook 이름을 *MyFirstRunbook-Graphical*로 지정합니다.
5.	이 경우 **Runbook 형식**으로 **그래픽**을 선택하여 [그래픽 Runbook](automation-graphical-authoring-intro.md)을 만듭니다.<br> ![새 runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.	**만들기**를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## 2 단계 - runbook에 활동을 추가 합니다.

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. **Write-Output** cmdlet을 추가하여 runbook의 텍스트를 출력합니다.

1.	라이브러리 컨트롤에서 검색 텍스트 상자를 클릭하고 **Write-Output**을 입력합니다. 검색 결과가 아래에 표시됩니다. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.	목록의 아래로 스크롤하십시오. **Write-Output**을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하거나 cmdlet 옆의 줄임표를 클릭하고 **캔버스에 추가**를 선택합니다.
3.	캔버스에서 **Write-Output** 활동을 클릭합니다. 활동을 구성할 수 있게 해주는 구성 제어판 블레이드가 열립니다.
4.	**레이블**은 기본적으로 cmdlet의 이름이지만 보다 친숙하게 바꿀 수 있습니다. *Hello World 출력을 위해 쓰기*로 바꿉니다.
5.	**매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다. 일부 cmdlet는 여러 매개 변수 집합이 있으며, 사용자는 사용하고자 하는 것을 선택해야 합니다. 이 경우 **Write-Output**에는 매개 변수 집합이 하나만 있으므로 선택하지 않아도 됩니다.<br>![쓰기 출력 속성](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.	**InputObject** 매개 변수를 선택합니다. 출력 스트림으로 보낼 텍스트를 지정하는 매개 변수입니다.
7.	**데이터 원본** 드롭다운에서 **PowerShell 식**을 선택합니다. **데이터 원본** 드롭다운은 매개 변수 값을 채우는 데 사용하는 다른 소스들을 제공합니다. 다른 활동, 자동화 자산, 혹은 Power Shell식 같은 특정 소스들로부터 출력을 사용할 수 있습니다. 여기서는 *Hello World* 텍스트만 출력하려고 합니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.
8.	**식** 상자에 *"Hello World"*를 입력한 후 **확인**을 두 번 클릭하여 캔버스로 돌아갑니다.<br>![PowerShell 식](media/automation-first-runbook-graphical/expression-hello-world.png)
9.	**저장**을 클릭하여 Runbook을 저장합니다.<br>![Runbook을 저장 합니다.](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## 3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시하기 전에 제대로 작동하는지 확인할 수 있게 테스트합니다. Runbook을 테스트할 때 **초안**버전을 실행하고 해당 출력을 대화형으로 봅니다.

1.	**테스트 창**을 클릭하여 테스트 블레이드를 엽니다.<br> ![테스트 창](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.	**시작**을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3.	이 창에서 [Runbook 작업](automation-runbook-execution.md)이 생성되고 해당 상태가 표시됩니다. 작업 상태는 클라우드의 runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중*으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.
4.	Runbook 작업이 완료되면 해당 출력이 표시됩니다. 여기서는 *Hello World*가 표시됩니다.<br>![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
5.	캔버스로 돌아가려면 테스트 블레이드를 닫습니다.

## 4 단계 - runbook 게시 및 시작

방금 만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 사용자의 경우, 방금전에 runbook을 만들었기 때문에 게시 버전이 아직 없습니다.

1.	**게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.<br>![게시](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.	**Runbook** 블레이드의 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
3.	오른쪽으로 다시 스크롤하면 **MyFirstRunbook** 블레이드가 표시됩니다. 위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나, 미래의 특정 시간에 시작하도록 예약하거나, [webhook](automation-webhooks.md)을 생성하여 HTTP 호출을 통해 시작할 수 있습니다.
4.	여기서는 Runbook을 시작하기만 하면 되므로 **시작**을 클릭하고 확인 메시지가 표시되면 **예**를 클릭합니다.<br>![Runbook 시작](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.	우리가 방금 만들었던 runbook작업에 대한 작업 블레이드가 열립니다. 블레이드를 닫을 수 있지만, 이 경우에는 작업의 진행 상황을 보기 위해 열어둡니다.
6.	작업 상태가 **작업 요약**에 표시되며 Runbook을 테스트할 때의 상태와 일치합니다.<br>![작업 요약](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.	Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. **출력** 블레이드가 열리고 창에서 *Hello World*를 볼 수 있습니다.<br> ![작업 요약](media/automation-first-runbook-graphical/runbook-job-output.png)
8.	출력 블레이드를 닫습니다.
9.	**모든 로그**를 클릭하여 runbook 작업에 대한 스트림 블레이드를 엽니다. 출력 스트림에 *Hello World*만 표시되어야 하지만 Runbook이 자세한 정보 표시 및 오류와 같은 Runbook 작업에 대한 다른 스트림에 쓰는 경우 해당 스트림이 표시될 수 있습니다.<br> ![작업 요약](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10.	모든 로그 블레이드와 작업 블레이드를 닫고 MyFirstRunbook 블레이드로 돌아갑니다.
11.	**작업**을 클릭하여 이 runbook에 대한 작업 블레이드를 엽니다. runbook으로 만든 모든 작업을 나열합니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.<br> ![작업](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12.	runbook을 시작했을 때 우리가 봤던 동일한 작업창을 열려면 이 작업을 클릭합니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## 5 단계 - 변수 자산 만들기

지금까지 runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하고자 합니다. 인증할 runbook을 구성하기 전에 구독 ID를 보유할 변수를 만들어서 아래 6단계에서 인증할 활동을 설정한 후에 참조합니다. 구독 컨텍스트에 대한 참조를 포함하면 여러 구독 간에서 쉽게 작업할 수 있습니다. 계속하기 전에, 탐색 창의 구독 옵션에서 구독 ID를 복사합니다.

1. 자동화 계정 블레이드에서 **자산** 타일 클릭하면 **자산** 블레이드가 열립니다.
2. 자산 블레이드에서 **변수** 타일을 클릭합니다.
3. 변수 블레이드에서 **변수 추가**를 클릭합니다.<br>![자동화 변수](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. 새로운 변수 블레이드의 **이름** 상자에 **AzureSubscriptionId**를 입력하고 **값** 상자에 사용자의 구독 ID를 입력합니다. **형식**에 대한 *문자열* 및 **암호화**에 대한 기본 값을 유지합니다.
5. **만들기**를 클릭하여 변수를 만듭니다.


## 6 단계 - Azure 리소스를 관리하는 인증 추가

이제 구독 ID를 보유한 변수가 있으므로 [필수 조건](#prerequisites)에 나온 실행 자격 증명을 사용하여 인증하도록 runbook을 구성할 수 있습니다. Azure 실행 연결 **자산** 및 **Add-AzureRMAccount** cmdlet을 캔버스에 추가하여 수행합니다.

1.	MyFirstRunbook 블레이드에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.<br> ![Runbook 편집](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.	**Write Hello World to output**은 더 이상 필요하지 않으므로 마우스 오른쪽 버튼을 클릭하고 **삭제**를 선택합니다.
3.	라이브러리 컨트롤에서 **연결**을 확장하고 **캔버스에 추가**를 선택하여 **AzureRunAsConnection**을 캔버스에 추가합니다.
4.	캔버스에서 **AzureRunAsConnection**을 선택하고 구성 제어판의 **레이블** 텍스트 상자에 **실행 연결 가져오기**를 입력합니다. 연결입니다.
5.	라이브러리 컨트롤에서 검색 텍스트 상자에 **Add-AzureRmAccount**을 입력합니다.
6.	캔버스에 **Add-AzureRmAccount**를 추가합니다.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.	도형 아래에 원이 나타날 때까지 **실행 연결 가져오기**를 마우스로 가리킵니다. 원을 클릭하고 화살표를 **Add-AzureRmAccount**로 끌어 놓습니다. 방금 만든 화살표는 *링크*입니다. runbook은 **실행 연결 가져오기**를 시작한 다음 **Add-AzureRmAccount**를 실행합니다.<br> ![활동 간 링크 만들기](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.	캔버스에서 **Add-AzureRmAccount**를 선택하고 구성 제어판의 **레이블** 텍스트 상자에 **Azure에 로그인**을 입력합니다.
9.	**매개 변수**를 클릭하면 활동 매개 변수 구성 블레이드가 표시됩니다.
10.  **Add-AzureRmAccount**에는 여러 매개 변수 집합이 있으므로 매개 변수 값을 입력하기 전에 하나를 선택해야 합니다. **매개 변수 집합**을 클릭한 후 **ServicePrincipalCertificate** 매개 변수 집합을 선택합니다.
11.  매개 변수 집합을 선택하면, Activity Parameter Configuration(활동 매개 변수 구성) 블레이드에 매개 변수가 표시 됩니다. **APPLICATIONID**를 클릭합니다.<br> ![Azure RM 계정 매개 변수 추가](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  매개 변수 값 블레이드에서 **데이터 원본**에 대해 **활동 출력**을 선택하고 목록에서 **실행 연결 가져오기**를 선택한 다음 **파일 경로** 텍스트 상자에 **ApplicationId**를 입력한 다음 **확인**을 클릭합니다. 활동에서 여러 속성을 가진 개체를 출력하기 때문에 필드 경로에 대한 속성의 이름을 지정합니다.
13.  **CERTIFICATETHUMBPRINT**를 클릭하고 매개 변수 값 블레이드에서 **데이터 원본**에 대한 **활동 출력**을 선택합니다. 목록에서 **실행 연결 가져오기**를 선택하고 **필드 경로** 텍스트 상자에서 **CertificateThumbprint**를 입력한 다음 **확인**을 클릭합니다.
14.  **SERVICEPRINCIPAL**을 클릭하고 매개 변수 값 블레이드에서 **데이터 원본**에 대한 **ConstantValue**를 선택하고 **True** 옵션을 클릭한 다음 **확인**을 클릭합니다.
15.  **TENANTID**를 클릭하고 매개 변수 값 블레이드에서 **데이터 원본**에 대한 **활동 출력**을 선택합니다. 목록에서 **실행 연결 가져오기**를 선택하고 **필드 경로** 텍스트 상자에서 **TenantId**를 입력한 다음 **확인**을 두 번 클릭합니다.
16.  라이브러리 컨트롤에서 검색 텍스트 상자에 **Set-AzureRmContext**을 입력합니다.
17.	 캔버스에 **Set-AzureRmContext**를 추가합니다.
18.	 캔버스에서 **Set-AzureRmContext**를 선택하고 구성 제어판의 **레이블** 텍스트 상자에 **구독 ID 지정**을 입력합니다.
19.	 **매개 변수**를 클릭하면 활동 매개 변수 구성 블레이드가 표시됩니다.
20. **Set-AzureRmContext**에는 여러 매개 변수 집합이 있으므로 매개 변수 값을 입력하기 전에 하나를 선택해야 합니다. **매개 변수 집합**을 클릭한 후 **SubscriptionId** 매개 변수 집합을 선택합니다.
21.	 매개 변수 집합을 선택하면, Activity Parameter Configuration(활동 매개 변수 구성) 블레이드에 매개 변수가 표시 됩니다. **SubscriptionID**를 클릭합니다.
22.	 매개 변수 값 블레이드에서 **데이터 원본**에 대해 **변수 자산**을 선택하고 목록에서 **AzureSubscriptionId**을 선택한 다음 **확인**을 두 번 클릭합니다.
23.  아래에 원 모양이 나타날 때까지 **Azure에 로그인**을 마우스로 가리킵니다. 원을 클릭하고 화살표를 **구독 ID 지정**으로 끌어 놓습니다.


runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-auth-config.png)

## 7 단계 - 가상 컴퓨터를 시작하는 활동 추가

이제 가상 컴퓨터를 시작하기 위한 **Start-AzureRmVM** 활동을 추가합니다. Azure 구독에서 모든 가상 컴퓨터를 선택 할 수 있지만 지금은 cmdlet의 이름을 하드코딩합니다.

1. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Start-AzureRm**을 입력합니다.
2. **Start-AzureRmVM**을 캔버스에 추가한 다음 클릭하여 **구독 ID 지정** 아래로 끌어 놓습니다.
3. 도형 아래에 원이 나타날 때까지 **구독 ID 지정**을 마우스로 가리킵니다. 원을 클릭하고 화살표를 **Start-AzureRmVM**으로 끌어 놓습니다.
4.	**Start-AzureRmVM**을 선택합니다. **매개 변수**와 **매개 변수 집합**을 차례로 클릭하여 **Start-AzureRmVM**에 대한 집합을 표시합니다. **ResourceGroupNameParameterSetName** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **이름** 옆에는 느낌표가 있습니다. 이것은 매개 변수가 필요하다는 것을 나타냅니다. 또한, 양 쪽 모두에 문자열 값이 필요합니다.
5.	**Name**을 선택합니다. **데이터 원본**에 대해 **PowerShell 식** 을 선택하고 이runbook으로 시작될 가상 컴퓨터의 이름을 큰따옴표 안에 입력합니다. **확인**을 클릭합니다.<br>![Start-AzureRmVM 이름 매개 변수 값](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.	**ResourceGroupName**을 선택합니다. **데이터 원본**에 **PowerShell 식**을 사용하고 리소스 그룹의 이름을 큰따옴표 안에 입력합니다. **확인**을 클릭합니다.<br>![Start-AzureRmVM 매개 변수](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.	runbook을 테스트할 수 있도록 테스트 창을 클릭합니다.
9.	**시작**을 클릭하여 테스트를 시작합니다. 완료되면, 가상 컴퓨터가 시작되었다는 것을 확인합니다.

runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-startvm.png)

## 8 단계 - runbook에 입력 매개 변수 추가

이 runbook은 현재 **Start-AzureRmVM** cmdlet에 지정한 리소스 그룹의 가상 컴퓨터를 시작하지만 runbook이 시작될 때 리소스 그룹과 가상 컴퓨터를 모두 지정할 수 있을 경우 유용성이 향상됩니다. 이제 해당 기능을 제공하도록 Runbook에 입력 매개 변수를 추가합니다.

1. **MyFirstRunbook** 창에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.
2. **입력 및 출력**과 **입력 추가**를 차례로 클릭하여 Runbook 입력 매개 변수 창을 엽니다.<br>![Runbook 입력 및 출력](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. **Name**에 대해 *VMName*을 지정합니다. **형식**에 대해 *문자열*을 유지하고 **필수**를 *예*로 변경합니다. **확인**을 클릭합니다.
4. *ResourceGroupName*이라는 두 번째 필수 입력 매개 변수를 만든 다음 **확인**을 클릭하여 **입력 및 출력** 창을 닫습니다.<br> ![Runbook 입력 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. **Start-AzureRmVM** 활동을 선택하고 **매개 변수**를 클릭합니다.
6. **이름**에 대한 **데이터 원본**을 **Runbook 입력**으로 변경한 다음 **VMName**을 선택합니다.<br>
7. **ResourceGroupName**에 대한 **데이터 원본**을 **Runbook 입력**으로 변경한 다음 **ResourceGroupName**을 선택합니다.<br> ![Start-azurevm 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Runbook을 저장하고 테스트 창을 엽니다. 사용자는 테스트에 사용될 두 개의 입력변수에 대한 값을 제공할 수 있음을 참고하세요.
9. 창을 닫습니다.
10.	**게시**를 클릭하여 Runbook의 새 버전을 게시합니다.
11.	이전 단계에서 실행시킨 가상 컴퓨터를 중지합니다.
12.	**시작**을 클릭하여 runbook을 시작합니다. 시작하려는 가상 컴퓨터의 **VMName** 및**ResourceGroupName**을 입력합니다.<br> ![Runbook 시작](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13.	Runbook이 완료되면 가상 컴퓨터가 시작되었다는 것을 확인합니다.

## 9 단계 - 조건부 링크 만들기

이미 시작되지 않은 경우 가상 컴퓨터를 시작하도록 시도하기 위해 runbook을 수정합니다. 가상 컴퓨터의 인스턴스 수준 상태를 가져올 runbook에 **Get-AzureRmVM** cmdlet을 추가하여 이 작업을 수행합니다. 그런 다음 가상 컴퓨터 상태가 실행 중인지 또는 중지되었는지 확인하기 위해 PowerShell 코드 조각과 함께 **Get Status**라는 PowerShell 워크플로 코드 모듈을 추가합니다. **Get Status** 모듈의 조건부 링크는 현재 실행 중인 상태가 중지된 경우에만 **Start-AzureRmVM**을 실행합니다. 마지막으로 VM이 시작되었거나 PowerShell Write-Output cmdlet을 사용하지 않는 경우에 알려줄 메시지를 출력합니다.

1. 그래픽 편집기에서 **MyFirstRunbook**을 엽니다.
2. **구독 ID 지정**과 **Start-AzureRmVM** 간의 링크를 클릭하고 *삭제* 키를 눌러 제거합니다.
3. 라이브러리 컨트롤에서 검색 텍스트 상자에 **Get-AzureRm**을 입력합니다.
4. 캔버스에 **Get-AzureRmVM**을 추가합니다.
5. **Get-AzureRmVM**과 **매개 변수 집합**을 차례로 선택하여 **Get-AzureRmVM**에 대한 집합을 표시합니다. **GetVirtualMachineInResourceGroupNameParamSet** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **이름** 옆에는 느낌표가 있습니다. 이것은 매개 변수가 필요하다는 것을 나타냅니다. 또한, 양 쪽 모두에 문자열 값이 필요합니다.
6. **이름**에 대한 **데이터 원본**에서 **Runbook 입력**을 선택한 다음 **VMName**을 선택합니다. **확인**을 클릭합니다.
7. **ResourceGroupName**에 대한 **데이터 원본**에서 **Runbook 입력**을 선택한 다음 **ResourceGroupName**을 선택합니다. **확인**을 클릭합니다.
8. **상태**에 대한 **데이터 원본**에 **상수 값**을 선택한 다음 **True**를 클릭합니다. **확인**을 클릭합니다.
9. **구독 ID 지정**에서 **Get-AzureRmVM**에 대한 링크를 만듭니다.
10. 라이브러리 컨트롤에서 **Runbook 컨트롤**을 확장하고 캔버스에 **코드**를 추가합니다.
11. **Get-AzureRmVM**에서 **코드**에 대한 링크를 만듭니다.
12. **코드**를 클릭하고 구성 창에서 레이블을 **상태 가져오기**로 변경합니다.
13. **코드** 매개 변수를 선택하면 **코드 편집기** 블레이드가 나타납니다.
14. 코드 편집기에 다음 코드 조각을 붙여 넣습니다.

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. **상태 가져오기**에서 **Start-AzureRmVM**에 대한 링크를 만듭니다.<br> ![코드 모듈과 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)
16. 링크를 선택하고 구성 창에서 **조건 적용**을 **예**로 변경합니다. 링크가 파선으로 바뀌면서 조건이 True로 확인되는 경우에만 목표 활동이 실행된다는 것을 나타냅니다.
17. **조건 식**에 *$ActivityOutput['Get Status'] -eq "Stopped"*를 입력합니다. 이제 **Start-AzureRmVM**은 가상 컴퓨터가 중지된 경우에만 실행됩니다.
18.	라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.
19.	캔버스에 **Write-Output**을 두 번 추가합니다.<br> ![쓰기 출력으로 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. 첫 번째 **Write-Output** 컨트롤에서 **매개 변수**를 클릭하고 **레이블** 값을 *VM 시작 알림*으로 변경합니다.
21. **InputObject**에 대해, **데이터 원본**을 **PowerShell 식**으로 변경하고 *"$VMName successfully started"* 식을 입력합니다.
22. 두 번째 **Write-Output** 컨트롤에서 **매개 변수**를 클릭하고 **레이블** 값을 *VM 시작 실패 알림*으로 변경합니다.
23. **InputObject**에서 **데이터 원본**을 **PowerShell 식**으로 변경하고 *"$VMName could not start"* 식을 입력합니다.
24. **Start-AzureRmVM**에서 **VM 중지 알림** 및 **VM 시작 실패 알림**에 대한 링크를 만듭니다.
25. **VM 시작 알림** 링크를 선택하고 **조건 적용**을 **True**로 변경합니다.
26. **조건 식**에 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*를 입력합니다. 이제 Write-Output 컨트롤은 가상 컴퓨터가 성공적으로 시작된 경우에만 실행됩니다.
27. **VM 시작 실패 알림** 링크를 선택하고 **조건 적용**을 **True**로 변경합니다.
28. **조건 식**에 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*를 입력합니다. 이제 Write-Output 컨트롤은 가상 컴퓨터가 성공적으로 시작되지 않은 경우에만 실행됩니다.
29.	Runbook을 저장하고 테스트 창을 엽니다.
30.	중지된 가상 컴퓨터로 runbook을 시작합니다.

## 다음 단계

-	그래픽 작성에 대해 자세히 알아보려면 [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
-	PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
-	PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.

<!---HONumber=AcomDC_0928_2016-->