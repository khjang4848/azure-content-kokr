<properties
   pageTitle="Azure 로그 통합 FAQ | Microsoft Azure"
   description="이 FAQ는 Azure 로그 통합에 대한 질문에 답변합니다."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="TomSh"/>

# Azure 로그 통합 FAQ(질문과 대답)

이 FAQ는 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템에 통합할 수 있게 해주는 서비스인 Azure 로그 통합에 대한 질문에 답변합니다. 이 통합은 응용 프로그램과 관련된 보안 이벤트를 집계하고, 상관 관계를 설정하고, 분석하고, 경고할 수 있도록 온-프레미스 또는 클라우드의 모든 자산에 대한 통합 대시보드를 제공합니다.

## Azure 로그 통합이 Azure VM 로그를 가져오는 저장소 계정을 어떻게 볼 수 있습니까?

**azlog source list** 명령을 실행합니다.

## 프록시 구성을 업데이트하려면 어떻게 해야 합니까?

프록시 설정에서 Azure 저장소에 대한 직접 액세스를 허용하지 않으면 **c:\\Program Files\\Microsoft Azure Log Integration**에서 **AZLOG.EXE.CONFIG** 파일을 엽니다. 조직의 프록시 주소를 사용하여 **defaultProxy** 섹션을 포함하도록 이 파일을 업데이트합니다. 업데이트가 완료되면 **net stop azlog** 및 **net start azlog** 명령을 사용하여 서비스를 중지했다가 다시 시작합니다.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## Windows 이벤트에서 구독 정보를 확인하려면 어떻게 해야 합니까?

소스를 추가 하는 동안 이름에 **subscriptionid**를 추가합니다.

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  

이벤트 XML은 구독 id를 포함하여 아래와 같이 메타데이터를 포함하고 있습니다.

![이벤트 XML][1]

## 오류 메시지

### **azlog createazureid** 명령을 실행할 때 다음 오류가 발생하는 이유는 무엇인가요?

오류:

  *AAD 응용 프로그램을 만들 수 없음 - 테넌트 72f988bf-86f1-41af-91ab-2d7cd011db37 - 이유 = '금지됨' - 메시지 = '권한이 부족하여 작업을 완료할 수 없습니다.'*

**Azlog createazureid** 명령은 Azure 로그인이 액세스 권한을 갖고 있는 구독에 대한 모든 Azure AD 테넌트에 서비스 주체를 만들려고 시도합니다. 사용자의 Azure 로그인이 해당 Azure AD 테넌트의 게스트 사용자인 경우 ‘권한이 부족하여 작업을 완료할 수 없습니다’ 오류와 함께 명령이 실패합니다. 계정을 테넌트에 사용자로 추가해 달라고 테넌트 관리자에게 요청하세요.

### **azlog authorize** 명령을 실행할 때 다음 오류가 발생하는 이유는 무엇인가요?

오류:

  *역할 할당 만들기 경고 - 권한 부여 실패: 개체 id가 'fe9e03e4-4dad-4328-910f-fd24a9660bd2'인 janedo@microsoft.com 클라이언트가 '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없습니다.*

**Azlog authorize** 명령은 Azure AD 서비스 주체(**Azlog createazureid** 명령을 사용하여 만든)의 읽기 권한자 역할을 제공된 구독에 할당합니다. Azure 로그인이 구독의 공동 관리자 또는 소유자가 아닌 경우 "권한 부여 실패" 오류 메시지와 오류가 발생합니다. 이 작업을 완료하려면 공동 관리자 또는 소유자의 Azure RBAC(역할 기반 액세스 제어)가 필요합니다.

## 감사 로그의 속성 정의는 어디서 찾을 수 있나요?

다음을 참조하세요.

- [리소스 관리자로 작업 감사](../resource-group-audit.md)
- [Azure Insights REST API에서 구독의 관리 이벤트 나열](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## Azure 보안 센터 경고에 대한 세부 정보는 어디서 찾을 수 있나요?

[Azure 보안 센터에서 보안 경고 관리 및 대응](../security-center/security-center-managing-and-responding-alerts.md)을 참조하세요.

## VM 진단을 통해 수집되는 항목을 어떻게 수정할 수 있나요?

Windows Azure 진단*(WAD)* 구성을 가져오고, 수정하고, 설정하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)을 참조하세요. 다음은 샘플입니다.

### WAD 구성 가져오기

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### WAD 구성 수정

다음은 보안 이벤트 로그에서 EventID 4624 및 EventId 4625만 수집되는 구성 예입니다. 시스템 이벤트 로그에서 Microsoft 맬웨어 방지 이벤트가 수집됩니다. XPath 식의 사용에 대한 자세한 내용은 [이벤트 사용](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)을 참조하세요.

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### WAD 구성 설정

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

구성을 변경한 후에는 저장소 계정을 검사하여 올바른 이벤트가 수집되는지 확인합니다.

Azure 로그 통합에 대한 질문이 있으면 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)으로 전자 메일을 보내주세요.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

<!---HONumber=AcomDC_0921_2016-->