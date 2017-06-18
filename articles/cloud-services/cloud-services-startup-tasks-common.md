<properties 
pageTitle="클라우드 서비스에 대한 일반적인 시작 작업 | Microsoft Azure" 
description="클라우드 서비스 웹 역할 또는 작업자 역할에서 수행하려는 경우 일반적인 시작 작업의 몇 가지 예를 제공합니다." 
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

# 일반적인 클라우드 서비스 시작 작업

이 문서에서는 클라우드 서비스에서 수행하려는 경우 일반적인 시작 작업의 몇 가지 예를 제공합니다. 시작 작업을 사용하여 역할이 시작되기 전에 작업을 수행할 수 있습니다. 수행하려는 작업은 구성 요소 설치, COM 구성 요소 등록, 레지스트리 키 설정 또는 장기 실행 프로세스를 시작을 포함합니다.

[이 문서](cloud-services-startup-tasks.md)를 참조하여 시작 작업이 작동 하는 방법 및 시작 작업을 정의하는 항목을 만드는 방법을 구체적으로 이해할 수 있습니다.

여기에서 다양한 작업은 다음을 사용합니다.

>[AZURE.NOTE] 시작 작업은 가상 컴퓨터에 적용되지 않으며 클라우드 서비스 웹 및 작업자 역할에만 적용됩니다.


## 역할이 시작되기 전에 환경 변수를 정의합니다.

특정 태스크에 대해 정의된 환경 변수가 필요한 경우 [태스크] 요소 내부의 [환경] 요소를 사용합니다.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

변수는 [유효한 Azure XPath 값](cloud-services-role-config-xpath.md)을 사용하여 배포에 대한 정보를 참조할 수도 있습니다. `value` 특성을 사용하는 대신 [RoleInstanceValue] 자식 요소를 정의합니다.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Appcmd.exe를 사용하여 IIS 시작을 구성합니다.

[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) 명령줄 도구는 Azure에서 시작 시 IIS 설정을 관리하는데 사용할 수 있습니다. *AppCmd.exe*는 Azure에서 시작 작업에 사용하기 위한 구성 설정에 대한 편리한 명령줄 액세스를 제공합니다. *AppCmd.exe*를 사용하면 응용 프로그램 및 사이트에 대해 웹사이트 설정을 추가, 수정하거나 제거할 수 있습니다.

그러나 시작 작업으로 *AppCmd.exe* 사용에 대해 주의해야 할 사항이 몇 가지 있습니다.

- 시작 작업은 재부팅 사이 두 번 이상 실행할 수 있습니다. 이는 예를 들어 역할이 재활용하는 경우에 발생할 수 있습니다.
- 일부 *AppCmd.exe* 작업은 두 번 이상 수행되는 경우 오류를 생성할 수 있습니다. *Web.config*에 섹션을 두 번 추가하려는 경우 오류가 생성될 수 있습니다.
- 0이 아닌 종료 코드 또는 **errorlevel**을 반환하는 경우 시작 작업이 실패합니다. *AppCmd.exe*가 오류를 생성하는 경우 발생할 수 있습니다.

나열된 이유 때문에 *AppCmd.exe*를 호출한 후 **errorlevel**을 확인하는 것이 좋습니다. *.cmd* 파일로 *AppCmd.exe*에 호출을 래핑하는 경우 수행하기 쉽습니다. 알려진 **errorlevel** 응답을 발견하는 경우 무시하거나 그렇지 않으면 반환할 수 있습니다. 이는 아래 예제에 나와 있습니다.

*AppCmd.exe*로 반환되는 errorlevel은 winerror.h 파일에 나열되어 있으며 [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx)에서 볼 수도 있습니다.

### 예

이 예에서는 오류 처리 및 로깅으로 JSON에 대한 압축 섹션 및 압축 항목을 *Web.config* 파일에 추가합니다.

[ServiceDefinition.csdef] 파일의 관련 섹션은 여기에 표시되어 있으며 *AppCmd.exe*에 *Web.config* 파일에서 설정을 변경할 충분한 권한을 부여하도록 [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) 특성을 `elevated`에 설정하는 것을 포함합니다.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Startup.cmd 배치 파일은 AppCmd.exe를 사용하여 JSON에 대한 압축 섹션 및 압축 항목을 *Web.config* 파일에 추가합니다. 예상된 183의 **errorlevel**은 VERIFY.EXE 명령줄 프로그램을 사용하여 0으로 설정됩니다. 예기치 않은 errorlevel은 StartupErrorLog.txt에 로깅됩니다.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## 방화벽 규칙 추가

Azure에는 사실상 두 개의 방화벽이 있습니다. 첫 번째 방화벽은 가상 컴퓨터와 외부 세계 간의 연결을 제어합니다. 이는 [ServiceDefinition.csdef] 파일의 [끝점] 요소에 의해 제어됩니다.

두 번째 방화벽은 가상 컴퓨터와 해당 가상 컴퓨터 내의 프로세스 간의 연결을 제어합니다. 이는 `netsh advfirewall firewall` 명령줄 도구에 의해 제어되며 이 문서에서 중점적으로 설명합니다.

Azure는 역할 내에서 시작되는 프로세스에 대한 방화벽 규칙을 만듭니다. 예를 들어 서비스 또는 프로그램을 시작하면 Azure는 필요한 방화벽 규칙을 만들어 해당 서비스가 인터넷과 통신할 수 있도록 합니다. 그러나 사용자의 역할(예: COM + 서비스 또는 Windows 스케줄러를 사용하여 시작하는 프로그램) 외부 프로세스에 의해 시작되는 서비스를 만드는 경우 해당 서비스에 대한 액세스를 허용하는 방화벽 규칙을 수동으로 만들어야 합니다. 이러한 방화벽 규칙은 시작 작업을 사용하여 만들 수 있습니다.

방화벽 규칙을 만드는 시작 작업은 **상승된** [executionContext][Task]가 있어야 합니다. 다음 시작 작업을 [ServiceDefinition.csdef] 파일에 추가합니다.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

방화벽 규칙을 추가하려면 시작 배치 파일에서 적절한 `netsh advfirewall firewall` 명령을 사용해야 합니다. 이 예에서는 시작 작업은 TCP 포트 80에 대한 보안 및 암호화가 필요합니다.

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## 특정 IP 주소 차단

IIS **web.config** 파일을 수정하고 **ApplicationHost.config** 파일의 **ipSecurity** 의 섹션을 차단 해제하는 명령줄을 만들어 지정된 IP 주소 집합에 대한 Azure 웹 역할 액세스를 제한할 수 있습니다.

먼저 **ApplicationHost.config** 파일의 **ipSecurity** 섹션을 차단 해제하고 역할이 시작될 때 실행하는 명령 파일을 만듭니다. **시작**이라는 웹 역할의 루트 수준에서 새로운 폴더를 만들고 이 폴더 내에서 **startup.cmd**라는 배치 파일을 만듭니다. 이 파일의 속성을 **항상 복사**로 설정하여 배포할 수 있도록 합니다.

다음 시작 작업을 [ServiceDefinition.csdef] 파일에 추가합니다.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

이 명령을 **startup.cmd** 파일에 추가합니다.

    @echo off
    @echo Installing "IPv4 Address and Domain Restrictions" feature 
    powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
    @echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

이렇게 하면 **startup.cmd** 배치 파일이 웹 역할이 초기화될 때마다 실행되어 필요한 **ipSecurity** 섹션이 잠금 해제되도록 합니다.

마지막으로 웹 역할의 **web.config** 파일의 [system.webServer 섹션](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005)을 수정하여 다음 예와 같이 액세스 권한이 부여된 IP 주소의 목록을 추가합니다.

이 샘플 구성은 모든 IP가 두 정의를 제외하고 서버에 액세스하도록 **허용**합니다.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

이 샘플 구성은 모든 IP가 두 정의를 제외하고 서버에 액세스하는 것을 **거부**합니다.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## PowerShell 시작 작업 만들기

Windows PowerShell 스크립트는 [ServiceDefinition.csdef] 파일에서 직접 호출할 수 없지만 시작 배치 파일 내에서 호출할 수 있습니다.

PowerShell은 기본적으로 서명되지 않은 스크립트를 실행하지 않습니다 . 스크립트를 서명하지 않는 한 서명되지 않은 스크립트를 실행하려면 Windows PowerShell을 구성해야 합니다. 서명되지 않은 스크립트를 실행하려면 **ExecutionPolicy**을 **Unrestricted**로 설정해야 합니다. 사용하는 **ExecutionPolicy** 설정은 Windows PowerShell의 버전에 기반합니다.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
        
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


PowerShell 2.0 또는 1.0을 실행하는 게스트 OS를 사용하는 경우 버전 2를 강제로 실행할 수 있으며 사용할 수 없는 경우 버전 1을 사용합니다.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## 시작 작업에서 로컬 저장소에 파일을 만듭니다.

로컬 저장소 리소스를 사용하여 응용 프로그램에 의해 나중에 액세스하는 시작 작업에서 만든 파일을 저장할 수 있습니다.

로컬 저장소 리소스를 만들려면 [LocalResources] 섹션을 [ServiceDefinition.csdef] 파일에 추가한 다음 [LocalStorage] 자식 요소를 추가합니다. 로컬 저장소 리소스에 고유한 이름을 부여하고 시작 작업에 대한 적절한 크기를 부여합니다.

시작 작업에서 로컬 저장소 리소스를 사용하려면 로컬 저장소 리소스 위치를 참조하는 환경 변수를 만들어야 합니다. 그러면 시작 작업 및 응용 프로그램이 로컬 저장소 리소스에 파일을 읽고 쓸 수 있게 됩니다.

**ServiceDefinition.csdef** 파일의 관련 섹션은 다음과 같습니다.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        
        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>
        
        ...
        
        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>
        
        ...
        
        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

한 예로 이 **Startup.cmd** 배치 파일은 **PathToStartupStorage** 환경 변수를 사용하여 로컬 저장소 위치에 파일 **MyTest.txt**를 만듭니다.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

[GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 메서드를 사용하여 Azure SDK에서 로컬 저장소에 액세스할 수 있습니다. 그 후 표준 파일 읽기 및 쓰기 작업은 로컬 저장소 리소스의 내용을 읽고 쓸 수 있도록 작동합니다.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## 에뮬레이터와 클라우드에서 실행 간 구별

계산 에뮬레이터에 있을 때에 비해 클라우드에서 작동하는 경우 시작 작업이 다른 단계를 수행하도록 할 수 있습니다. 예를 들어 에뮬레이터에서 실행하는 경우 SQL 데이터의 새 복사본을 사용하려고 할 수도 있습니다. 또는 에뮬레이터에서 실행하는 경우 수행할 필요가 없는 클라우드에 대한 일종의 성능 최적화를 수행하려고 할 수도 있습니다.

계산 에뮬레이터와 클라우드에서 서로 다른 동작을 수행하도록 하는 이 기능은 [ServiceDefinition.csdef] 파일에서 환경 변수를 만든 다음 시작 작업에서 환경 변수를 테스트하여 수행될 수 있습니다.

환경 변수를 만들려면 [변수]/[RoleInstanceValue] 요소를 추가하고 `/RoleEnvironment/Deployment/@emulated`의 XPath 값을 만듭니다. **%ComputeEmulatorRunning%** 환경 변수의 값은 계산 에뮬레이터에서 실행 되는 경우 `"true"`이(가) 되고 클라우드에서 실행 되는 경우 `"false"`이(가) 됩니다.


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...
        
        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

모든 작업 실행은 이제 **%ComputeEmulatorRunning%** 환경 변수를 사용하여 역할이 클라우드 또는 에뮬레이터에서 실행되는지 여부에 따라 다른 작업을 수행할 수 있습니다. 해당 환경 변수를 검사하는 .cmd 셸 스크립트는 다음과 같습니다.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
        
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
        
    )


## 작업이 이미 실행되었는지 감지

재부팅 없이 역할이 재활용되어 시작 작업이 다시 실행될 수 있습니다. 호스팅 VM에서 이미 실행된 작업을 나타내는 플래그가 있습니다. 여러 번 실행되는 것과 상관 없는 일부 작업이 있을 수 있습니다. 그러나 작업이 두 번 이상 실행되지 않도록 해야 하는 상황에 직면할 수도 있습니다.

작업이 이미 실행되었는지 감지하는 가장 간단한 방법은 작업이 성공하고 작업 시작 시 검색할 경우 **%TEMP%** 폴더에 파일을 만드는 것입니다. 해당 작업을 수행하는 예제 cmd 셸 스크립트는 다음과 같습니다.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
      
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0

## 작업 모범 사례
웹 또는 작업자 역할에 대한 작업을 구성할 때 따라야 하는 몇 가지 모범 사례는 다음과 같습니다.

### 항상 시작 작업 기록

Visual Studio는 배치 파일을 통해 단계에 디버거를 제공하지 않으므로 배치 파일 작업 시 가능한 많은 데이터를 가져오는 것이 좋습니다. **stdout** 및 **stderr** 배치 파일의 출력 로깅은 배치 파일을 디버깅하고 수정하려고 할 때 중요한 정보를 제공할 수 있습니다. **stdout** 및 **stderr** 모두를 **% TEMP %** 환경 변수가 가리킨 디렉터리의 StartupLog.txt 파일에 로깅하려면 텍스트 `>>  "%TEMP%\\StartupLog.txt" 2>&1`을(를) 로깅하려면 특정 줄의 끝에 추가합니다. 예를 들어 **%PathToApp1Install%** 디렉터리의 setup.exe를 실행하려면:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

`>> "%TEMP%\StartupLog.txt" 2>&1`을(를) 추가하지 않고 시작 작업에서 모든 출력을 각 줄의 끝에 로깅하려면 두 시작 배치 파일이 필요합니다. 첫 번째 배치 파일은 리디렉션을 사용하여 두 번째 배치 파일의 모든 활동을 로깅하도록 두 번째 배치 파일을 호출합니다. 이는 적절한 리디렉션을 발생시키기 위해 필요합니다.

다음은 시작 배치 파일의 모든 출력을 리디렉션하는 방법을 보여줍니다. 이 예에서 ServerDefinition.csdef 파일은 Startup1.cmd를 호출하는 시작 작업을 만듭니다. Startup1.cmd는Startup2.cmd를 호출하여 %TEMP%\\StartupLog.txt에 모든 출력을 리디렉션합니다.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd:

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd:

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### 시작 작업에 적절하게 executionContext 설정

시작 작업에 대한 권한을 적절하게 설정합니다. 경우에 따라 역할이 일반 권한으로 실행되더라도 시작 작업을 상승된 권한으로 실행해야 합니다.

[executionContext][Task] 특성은 시작 작업의 권한 수준을 설정합니다. `executionContext="limited"` 사용은 시작 작업이 역할과 동일한 권한 수준을 갖는 것을 의미합니다. `executionContext="elevated"` 사용은 시작 작업이 관리자 권한을 갖게 되므로 역할에 관리자 권한을 부여하지 않고 시작 작업이 관리자 작업을 수행하도록 하는 것을 의미합니다.

상승된 권한이 필요한 시작 작업의 예로 **AppCmd.exe**를 사용하여 IIS를 구성하는 시작 작업을 들 수 있습니다. **AppCmd.exe**는 `executionContext="elevated"`이(가) 필요합니다.

### 적절한 taskType 사용

[taskType][Task] 특성은 시작 작업이 실행되는 방식을 결정합니다. **간단**, **백그라운드** 및 **포그라운드**의 세 가지 값이 있습니다. 백그라운드 및 포그라운드 작업은 비동기적으로 시작된 다음 간단 작업이 한 번에 하나씩 동기적으로 실행됩니다.

**간단** 시작 작업의 경우 ServiceDefinition.csdef 파일에 나열된 작업의 순서에 따라 작업이 발생할 순서를 설정할 수 있습니다. **간단** 작업이 0이 아닌 종료 코드로 끝나는 경우 시작 절차가 중지되고 역할이 시작되지 않습니다.

**백그라운드** 시작 작업과 **포그라운드** 시작 작업의 차이는 **포그라운드** 작업은 **포그라운드** 작업이 끝날 때까지 역할이 실행되도록 유지하는 것입니다. 또한 는 **포그라운드** 작업이 중단되거나 충돌되는 경우 **포그라운드** 작업이 강제로 닫힐 때까지 역할이 재활용되지 않는 것을 의미합니다. 이러한 이유로 **백그라운드** 작업은 **포그라운드** 작업의 해당 기능이 필요하지 않는 경우 비동기적 시작 작업에 사용하는 것이 좋습니다.

### EXIT /B 0를 사용하여 배치 파일 끝내기

각 간단 시작 작업에서 **errorlevel**이 0인 경우 역할이 시작합니다. 모든 프로그램이 **errorlevel**(종료 코드)을 올바르게 설정하지 않으므로 모든 항목이 올바르게 실행된 경우 배치 파일은 `EXIT /B 0`(으)로 끝나야 합니다.

시작 배치 파일의 끝에 누락된 `EXIT /B 0`은(는) 시작하지 않는 역할의 일반적인 이유입니다.

### 시작 작업이 두 번 이상 실행되도록 예상

모든 역할 재활용은 재부팅을 포함하지 않으므로 모든 역할 재활용은 모든 시작 작업 실행을 포함합니다. 이는 시작 작업이 문제 없이 재부팅 사이 여러 번 실행될 수 있어야 함을 의미합니다. 이는 [위에서](#detect-that-your-task-has-already-run) 설명되었습니다.

### 로컬 저장소를 사용하여 역할에 액세스할 수 있어야 하는 파일을 저장합니다.

그런 다음 역할에 액세스할 수 있는 시작 작업 중 파일을 복사하거나 만들려는 경우 해당 파일이 로컬 저장소에 배치되어 있어야 합니다. 위의 [섹션](#create-files-in-local-storage-from-a-startup-task)을 참조하세요.

## 다음 단계

클라우드 [서비스 모델 및 패키지](cloud-services-model-and-package.md) 검토

[작업](cloud-services-startup-tasks.md) 작동 방법에 대해 자세히 알아보세요.

클라우드 서비스 패키지를 [만들고 배포하세요](cloud-services-how-to-create-deploy-portal.md).


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[태스크]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[환경]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[변수]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[끝점]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_0914_2016-->