<properties 
    pageTitle=" Azure 자동화에서 원본 제어 통합 | Microsoft Azure"
    description="이 문서에서는 Azure 자동화에서 GitHub를 사용하는 원본 제어 통합을 설명합니다."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/12/2016"
    ms.author="magoedte;sngun" />

# Azure 자동화에서 원본 제어 통합

원본 제어 통합을 사용하면 GitHub 원본 제어 리포지토리에 자동화 계정의 Runbook을 연결할 수 있습니다. 원본 제어를 사용하면 팀과 쉽게 공동 작업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어 원본 제어를 사용하면 개발, 테스트 또는 프로덕션 자동화 계정에 원본 제어의 여러 분기를 동기화할 수 있어 개발 환경에서 테스트된 코드를 프로덕션 자동화 계정 수준으로 쉽게 올릴 수 있습니다.

원본 제어를 사용하면 Azure 자동화에서 원본 제어로 코드를 푸시하거나 원본 제어에서 Azure 자동화로 Runbook을 끌어올 수 있습니다. 이 문서에서는 Azure 자동화 환경에서 원본 제어를 설치하는 방법을 설명합니다. GitHub 리포지토리에 액세스하도록 Azure 자동화를 구성하는 작업부터 시작하고 원본 제어 통합을 사용하여 수행될 수 있는 여러 작업 과정을 안내합니다.


>[AZURE.NOTE] 원본 제어는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 뿐만 아니라 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)의 끌어오기 및 밀어넣기를 지원합니다. [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)은 아직 지원되지 않습니다.<br><br>


이미 GitHub 계정이 있는 경우 자동화 계정에 대해 원본 제어를 구성하는 데 필요한 간단한 두 가지 단계가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.
## 1단계 - GitHub 리포지토리 만들기

Azure 자동화에 연결하려는 GitHub 계정 및 리포지토리가 이미 있는 경우 기존 계정에 로그인하고 아래의 2단계부터 시작합니다. 그렇지 않은 경우 [GitHub](https://github.com/)로 이동하여 새 계정을 등록하고 [새 리포지토리를 만듭니다](https://help.github.com/articles/create-a-repo/).


## 2단계 - Azure 자동화에서 원본 제어 설정

1. Azure 포털의 자동화 계정 블레이드에서 **원본 제어 설정**을 클릭합니다.
 
    ![원본 제어 설정](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)

2. **원본 제어** 블레이드가 열리면 여기서 GitHub 계정 세부 정보를 구성할 수 있습니다. 아래는 구성할 매개 변수의 목록입니다.

    |**매개 변수** |**설명** |
    |:---|:---| 
    |원본 선택 | 원본을 선택합니다. 현재는 **GitHub**만 지원됩니다. |
    |권한 부여 | **권한 부여** 단추를 클릭하여 GitHub 리포지토리에 Azure 자동화 액세스 권한을 부여합니다. 이미 다른 창에서 GitHub 계정에 로그인했다면 해당 계정의 자격 증명이 사용됩니다. 권한이 성공적으로 부여되면 블레이드는 **권한 부여 속성**에서 GitHub 사용자 이름을 표시합니다. |
    |리포지토리 선택 | 사용 가능한 리포지토리 목록에서 GitHub 리포지토리를 선택합니다. |
    |분기 선택 | 사용 가능한 분기 목록에서 분기를 선택합니다. 분기를 만들지 않은 경우에는 **마스터** 분기만 표시됩니다. |
    |Runbook 폴더 경로 | Runbook 폴더 경로는 코드를 푸시하거나 끌어오려는 GitHub 리포지토리에 경로를 지정합니다. **/foldername/subfoldername** 형식으로 입력되어야 합니다. Runbook 폴더 경로의 Runbook만 자동화 계정에 동기화됩니다. Runbook 폴더 경로의 하위 폴더에 있는 Runbook은 동기화되지 **않습니다**. **/**를 사용하여 리포지토리 아래의 모든 Runbook을 동기화합니다. |


3. 예를 들어 **RootFolder**라는 폴더를 포함하는 **PowerShellScripts**라는 리포지토리가 있는 경우 이는 **SubFolder**라는 폴더를 포함합니다. 다음 문자열을 사용하여 각 폴더 수준을 동기화할 수 있습니다.

    1. **리포지토리**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */* 입니다.
    2. **RootFolder**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */RootFolder* 입니다.
    3. **SubFolder**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */RootFolder/SubFolder* 입니다.
  

4. 매개 변수를 구성한 후에 **원본 제어 설정 블레이드**에 표시됩니다.
 
    ![블레이드 구성](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


5. 확인을 클릭하면 원본 제어 통합은 자동화 계정에 대해 구성되고 GitHub 정보로 업데이트되어야 합니다. 이 부분을 클릭하여 모든 원본 제어 동기화 작업 기록을 볼 수 있습니다.

    ![리포지토리 값](media/automation-source-control-integration/automation_03_RepoValues.png)

6. 원본 제어를 설정한 다음 자동화 리소스가 자동화 계정에 만들어집니다. 두 개의 [변수 자산](automation-variables.md)이 만들어집니다.
      
    * **Microsoft.Azure.Automation.SourceControl.Connection** 변수는 아래와 같이 연결 문자열의 값을 포함합니다.

    |**매개 변수** |**값** |
    |:---|:---|
    | 이름 | Microsoft.Azure.Automation.SourceControl.Connection |
    | 형식 | 문자열 |
    | 값 | {"Branch":<*Your branch name*>,"RunbookFolderPath":<*Runbook folder path*>,"ProviderType":<*has a value 1 for GitHub*>,"Repository":<*Name of your repository*>,"Username":<*Your GitHub user name*>} | <br>


    * **Microsoft.Azure.Automation.SourceControl.OAuthToken** 변수는 OAuthToken의 안전하고 암호화된 값을 포함합니다.

    |**매개 변수** |**값** |
    |:---|:---|
    | 이름 | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | 형식 | 알수 없음(암호화됨) |
    | 값 | <*암호화된 OAuthToken*> |  

    ![변수](media/automation-source-control-integration/automation_04_Variables.png)

    * **자동화 원본 제어**는 GitHub 계정에 권한이 부여된 응용 프로그램으로 추가됩니다. 응용 프로그램을 보려면 GitHub 홈페이지에서 **프로필** > **설정** > **응용 프로그램**으로 이동합니다. 이 응용 프로그램을 사용하면 Azure 자동화가 자동화 계정에 GitHub 리포지토리를 동기화할 수 있습니다.

    ![Git 응용 프로그램](media/automation-source-control-integration/automation_05_GitApplication.png)


## 자동화에서 원본 제어 사용


### Azure 자동화에서 원본 제어로 runbook 체크 인

Runbook 체크 인을 사용하면 Azure 자동화에서 Runbook에 대한 변경 내용을 원본 제어 리포지토리로 푸시할 수 있습니다. runbook을 체크 인하는 단계는 다음과 같습니다.

1. 자동화 계정에서 [새 텍스트 Runbook 만들기](automation-first-runbook-textual.md) 또는 [기존 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 수행합니다. 이 runbook은 PowerShell 워크플로 또는 PowerShell 스크립트 runbook일 수 있습니다.

2. Runbook을 편집한 후에 저장하고 **편집** 블레이드에서 **체크 인**을 클릭합니다.

    ![체크 인 단추](media/automation-source-control-integration/automation_06_CheckinButton.png)


     >[AZURE.NOTE] Azure 자동화에서 체크 인은 원본 제어에 현재 존재하는 코드를 덮어씁니다. 체크 인할 Git와 동등한 명령줄 지침은 **git 추가 + git 커밋 + git 푸시**입니다.

3. **체크 인**을 클릭하면 확인 메시지가 표시되고 계속하려면 예를 클릭합니다.

    ![체크 인 메시지](media/automation-source-control-integration/automation_07_CheckinMessage.png)

4. 체크 인은 원본 제어 Runbook을 시작합니다. **Sync-MicrosoftAzureAutomationAccountToGitHubV1** 이 runbook은 GitHub에 연결하고 Azure 자동화에서 리포지토리에 변경 내용을 푸시합니다. 체크 인 작업 기록을 보려면 **원본 제어 통합** 탭으로 다시 이동하고 리포지토리 동기화 블레이드를 클릭하여 엽니다. 이 블레이드는 모든 원본 제어 작업을 표시합니다. 보려는 작업을 선택하고 클릭하여 세부 정보를 봅니다.

    ![체크 인 Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)

    >[AZURE.NOTE] 원본 제어 Runbook은 보거나 편집할 수 없는 특별한 자동화 Runbook입니다. Runbook 목록에 표시되지 않지만 작업 목록에서 동기화 작업을 확인할 수 있습니다.
 
5. 수정된 runbook의 이름은 입력 매개 변수로 체크 인 runbook에 전송됩니다. **리포지토리 동기화** 블레이드에서 Runbook을 확장하여 [작업 세부 정보를 볼](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal) 수 있습니다.

    ![체크 인 입력](media/automation-source-control-integration/automation_09_CheckinInput.png)

6. 변경 내용을 보기 위해 작업이 완료되면 GitHub 리포지토리를 새로 고칩니다. 커밋 메시지**(Azure 자동화에서 업데이트된 *Runbook 이름* )**와 함께 리포지토리에 커밋이 있어야 합니다.



### 원본 제어에서 Azure 자동화에 runbook 동기화 

리포지토리 동기화 블레이드에서 동기화 단추를 사용하면 리포지토리의 runbook 폴더 경로에 있는 모든 runbook을 자동화 계정으로 가져올 수 있습니다. 동일한 리포지토리는 둘 이상의 자동화 계정에 동기화될 수 있습니다. Runbook을 동기화하는 단계는 아래와 같습니다.

1. 원본 제어를 설정한 자동화 계정에서 **원본 제어 통합/리포지토리 동기화 블레이드**를 열고 **동기화**를 클릭한 다음 확인 메시지가 표시될 때 **예**를 클릭하여 계속합니다.

    ![동기화 단추](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)

2. 동기화는 Runbook을 시작합니다. **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. 이 Runbook은 GitHub에 연결하고 리포지토리에서 Azure 자동화로 변경 내용을 끌어옵니다. 이 작업에 대한 **리포지토리 동기화** 블레이드에 새 작업이 표시됩니다. 동기화 작업에 대한 세부 정보를 보려면 클릭하여 작업 세부 정보 블레이드를 엽니다.
 
    ![동기화 Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
    >[AZURE.NOTE] 원본 제어의 동기화는 현재 원본 제어에 있는 **모든** Runbook에 대한 자동화 계정에 현재 존재하는 Runbook의 초안 버전을 덮어씁니다. 동기화할 Git와 동등한 명령줄 명령은 **git 끌어오기**입니다.


## 원본 제어 문제 해결

체크 인 또는 동기화 작업에서 오류가 발생하면 작업 상태는 일시 중단되어야 하고 작업 블레이드에서 오류에 대한 자세한 정보를 볼 수 있습니다. **모든 로그** 부분에 해당 작업에 연결된 모든 PowerShell 스트림이 표시됩니다. 체크 인 또는 동기화 문제를 해결하는 데 필요한 세부 정보가 제공됩니다. 또한 Runbook을 동기화하거나 체크 인하는 동안 발생한 작업의 순서가 표시됩니다.

![AllLogs 이미지](media/automation-source-control-integration/automation_13_AllLogs.png)

## 원본 제어 연결 끊기

GitHub 계정에서 연결을 끊으려면 리포지토리 동기화 블레이드를 열고 **연결 끊기**를 클릭합니다. 원본 제어 연결을 끊으면 이전에 동기화된 Runbook은 자동화 계정에 그대로 있지만 리포지토리 동기화 블레이드는 사용할 수 없습니다.

  ![연결 끊기 단추](media/automation-source-control-integration/automation_12_Disconnect.png)



## 다음 단계

원본 제어 통합에 대한 자세한 내용은 다음 리소스를 참조하세요.
- [Azure 자동화: Azure 자동화에서 원본 제어 통합](https://azure.microsoft.com/blog/azure-automation-source-control-13/)
- [즐겨 찾는 원본 제어 시스템에 대한 투표](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)
- [Azure 자동화: Visual Studio Team Services를 사용하여 Runbook 원본 제어 통합](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=AcomDC_0914_2016-->