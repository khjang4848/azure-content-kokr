<properties 
pageTitle="Azure 클라우드 서비스에서 시작 작업 실행 | Microsoft Azure" 
description="시작 작업을 통해 응용 프로그램에 대한 클라우드 서비스 환경을 준비합니다. 시작 작업의 작동 방법 및 만드는 방법을 배웁니다." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# 클라우드 서비스에 대한 시작 작업 구성 및 실행 방법

시작 작업을 사용하여 역할이 시작되기 전에 작업을 수행할 수 있습니다. 수행하려는 작업은 구성 요소 설치, COM 구성 요소 등록, 레지스트리 키 설정 또는 장기 실행 프로세스를 시작을 포함합니다.

>[AZURE.NOTE] 시작 작업은 가상 컴퓨터에 적용되지 않으며 클라우드 서비스 웹 및 작업자 역할에만 적용됩니다.

## 시작 작업 작동 방법

시작 작업은 [시작] 요소 내의 [작업] 요소를 사용하여 역할이 시작되고 [ServiceDefinition.csdef] 파일에서 정의되기 전에 수행되는 동작입니다. 시작 작업은 흔히 배치 파일이지만 PowerShell 스크립트를 시작하는 콘솔 응용 프로그램 또는 배치 파일일 수도 있습니다.

환경 변수는 시작 작업으로 정보를 전달하고 로컬 저장소는 시작 작업에서 정보를 전달하는데 사용할 수 있습니다. 예를 들어 환경 변수는 설치하려는 프로그램에 경로를 지정할 수 있고 사용자 역할에 의해 나중에 읽을 수 있는 로컬 저장소에 파일을 쓸 수 있습니다.

시작 작업은 **TEMP** 환경 변수로 지정된 디렉터리에 정보 및 오류를 로깅할 수 있습니다. 시작 작업 중 **TEMP** 환경 변수는 클라우드에서 실행되는 경우 *C:\\Resources\\temp\\[guid]. [ rolename] \\RoleTemp* 디렉터리로 확인됩니다.

시작 작업은 재부팅 사이에 여러 번 실행할 수도 있습니다. 예를 들어 시작 작업은 역할 재활용 때마다 실행되고 역할 재활용은 재부팅을 항상 포함하지 않을 수도 있습니다. 시작 작업은 문제 없이 여러 번 실행할 수 있도록 하는 방식으로 작성되어야 합니다.

시작 작업은 완료할 시작 프로세스에 대해 0의 **errorlevel**(또는 종료 코드)로 끝나야 합니다. 시작 작업이 0이 아닌 **errorlevel**로 끝나는 경우 역할이 시작되지 않습니다.


## 역할 시작 순서

다음은 Azure에서 역할 시작 절차를 나열합니다.

1. 인스턴스는 **시작**으로 표시되고 트래픽을 받지 않습니다.

2. 모든 시작 작업은 해당 **taskType** 특성에 따라 실행됩니다.
    - **간단한** 작업이 한 번에 하나씩 동기적으로 실행됩니다.
    - **백그라운드** 및 **포그라운드** 작업은 비동기적으로 시작 작업에 병렬로 시작됩니다.
       
    > [AZURE.WARNING] IIS는 시작 프로세스에서 시작 작업 단계 중 완전하게 구성되지 않을 수도 있으므로 역할 지정 데이터를 사용할 수도 없습니다. 역할 지정 데이터가 필요한 시작 작업은 [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx)를 사용해야 합니다.

3. 역할 호스트 프로세스가 시작되고 사이트가 IIS에서 만들어집니다.

4. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 메서드가 호출됩니다.

5. 인스턴스는 **준비**로 표시되고 트래픽이 인스턴스로 라우팅됩니다.

6. [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) 메서드가 호출됩니다.


## 시작 작업의 예

시작 작업은 **작업** 요소의 [ServiceDefinition.csdef] 파일에서 정의됩니다. **commandLine** 특성은 시작 배치 파일 또는 콘솔 명령의 이름 및 매개 변수를 지정하고 **executionContext** 특성은 시작 작업의 권한 수준을 지정하며 **taskType** 특성은 작업이 실행될 방법을 지정합니다.

이 예에서는 환경 변수 **MyVersionNumber**가 시작 작업에 대해 생성되고 값 "**1.0.0.0**"으로 설정됩니다.

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

다음 예에서는 **Startup.cmd** 배치 파일이 TEMP 환경 변수로 지정된 디렉터리의 StartupLog.txt 파일에 줄 "현재 버전은 1.0.0.0입니다"를 작성합니다. `EXIT /B 0` 줄은 시작 작업이 0의 **errorlevel**로 끝나는지 확인합니다.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] Visual Studio에서 시작 배치 파일에 대한 **출력 디렉터리로 복사** 속성은 시작 배치 파일이 Azure의 프로젝트에 제대로 배포되었는지 확인하도록 **항상 복사**로 설정되어야 합니다(웹 역할의 경우**approot\\bin**, 작업자 역할의 경우 **approot**).

## 작업 특성 설명

다음은 [ServiceDefinition.csdef] 파일의 **작업** 요소 특성을 설명합니다.

**commandLine** - 시작 작업에 대한 명령줄을 지정합니다.

- 시작 작업을 시작하는 옵션 명령줄 매개 변수가 있는 명령입니다.
- 자주 .cmd 또는 .bat 배치 파일의 파일 이름입니다.
- 작업은 배포에 대한 AppRoot\\Bin 폴더에 상대적입니다. 환경 변수는 작업의 경로 및 파일을 결정하는데 확장되지 않습니다. 환경 확장이 필요한 경우 시작 작업을 호출하는 작은 .cmd 스크립트를 만들 수 있습니다.
- 콘솔 응용 프로그램 또는 [PowerShell 스크립트](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)를 시작하는 배치 파일일 수 있습니다.

**executionContext** -시작 작업에 대한 권한 수준을 지정합니다. 권한 수준은 제한되거나 상승될 수 있습니다.

- **제한** 시작 작업이 역할과 동일한 권한으로 실행됩니다. [런타임] 요소에 대한 **executionContext** 특성 또한 **제한**인 경우 사용자 권한이 사용됩니다.

- **상승** 시작 작업이 관리자 권한으로 실행됩니다. 이를 통해 시작 작업이 역할 자체의 권한 수준을 높이지 않고 프로그램을 설치하고 IIS 구성을 변경하고 기타 관리자 수준 작업을 수행할 수 있습니다.

> [AZURE.NOTE] 시작 작업의 권한 수준을 역할 자체와 동일하게 할 필요가 없습니다.

**taskType** -시작 작업이 실행되는 방식을 지정합니다.

- **간단** 작업이 동기적으로 한 번에 하나씩 [ServiceDefinition.csdef] 파일에 지정된 순서로 실행됩니다. 하나의 **간단** 시작 작업이 0의 **errorlevel**로 끝나는 경우 다음 **간단** 시작 작업이 실행됩니다. 더 이상 실행할 **간단** 시작 작업이 없는 경우 역할 자체가 시작됩니다.

    > [AZURE.NOTE] **간단** 작업이 0이 아닌 **errorlevel**로 끝나는 경우 인스턴스가 차단됩니다. 후속 **간단** 시작 작업과 역할 자체는 시작되지 않습니다.

    배치 파일이 0의 **errorlevel**로 끝나는지 확인하려면 배치 파일 프로세스의 끝에 명령 `EXIT /B 0`을(를) 실행합니다.

- **백그라운드** 작업이 비동기적으로 역할의 시작과 병렬로 실행됩니다.

- **포그라운드** 작업이 비동기적으로 역할의 시작과 병렬로 실행됩니다. **포그라운드** 및 **백그라운드** 작업 사이의 주요 차이점은 **포그라운드** 작업은 작업이 종료될 때까지 작업이 재활용 또는 종료되는 것을 방지합니다. **백그라운드** 작업은 이러한 제한이 없습니다.

## 환경 변수

환경 변수는 시작 작업에 정보를 전달하는 방법입니다. 예를 들어 설치할 프로그램을 포함하는 Blob에 경로를 배치하거나 역할이 사용할 포트 번호 또는 시작 작업의 기능을 제어할 설정을 배치할 수 있습니다.

시작 작업에 대한 환경 변수에는 [RoleEnvironment] 클래스의 멤버에 따라 동적 환경 변수 및 환경 변수 두 가지가 있습니다. 두 변수는 모두 [ServiceDefinition.csdef] 파일의 [환경] 섹션에 있으며 모두 [변수] 요소 및 **이름** 특성을 사용합니다.

정적 환경 변수는 [변수] 요소의 **값** 특성을 사용합니다. 위의 예제에서는 "**1.0.0.0**"의 정적 값이 있는 환경 변수 **MyVersionNumber**를 만듭니다. 다른 예제는 "**준비**"또는"**프로덕션**"의 값을 수동으로 설정하여 **StagingOrProduction** 환경 변수의 값에 따라 다른 시작 작업을 수행할 수 있는 **StagingOrProduction** 환경 변수를 만드는 것입니다.

RoleEnvironment 클래스의 멤버를 기반으로 한 환경 변수는 [변수] 요소의 **값** 특성을 사용하지 않습니다. 대신 적절한 **XPath** 특성 값이 있는 [RoleInstanceValue] 자식 요소는 [RoleEnvironment] 클래스의 특정 멤버에 따라 환경 변수를 만드는데 사용됩니다. 다양한 [RoleEnvironment] 값에 액세스하는 **XPath** 특성에 대한 값은 [여기](cloud-services-role-config-xpath.md)에서 찾을 수 있습니다.



예를 들어 인스턴스가 계산 에뮬레이터에서 실행되는 경우 "**true**"이고 클라우드에서 실행되는 경우 "**false**"인 환경 변수를 만들려면 다음 [변수] 및 [RoleInstanceValue] 요소를 사용합니다.

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## 다음 단계
클라우드 서비스를 사용하여 일부 [일반적인 시작 작업](cloud-services-startup-tasks-common.md)을 수행하는 방법을 알아봅니다.

클라우드 서비스를 [포장합니다](cloud-services-model-and-package.md).


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[작업]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[시작]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[런타임]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[환경]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[변수]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx

<!---HONumber=AcomDC_0914_2016-->