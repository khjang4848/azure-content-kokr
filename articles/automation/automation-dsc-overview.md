<properties 
   pageTitle="Azure 자동화 DSC 개요 | Microsoft Azure" 
   description="Azure 자동화 DSC(필요한 상태 구성)의 개요, 용어 및 알려진 문제" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"
   keywords="PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="05/10/2016"
   ms.author="magoedte;coreyp"/>

# Azure 자동화 DSC 개요 #

##Azure 자동화 DSC란?##
서버 및 응용 프로그램 리소스의 필요한 상태를 배포 및 유지 관리하는 작업은 매우 지루할 수 있고 오류가 발생하기 쉽습니다. Azure 자동화 DSC(원하는 상태 구성)으로 클라우드에서 대규모로 모든 IT 리소스의 원하는 상태를 일관되게 배포하고 안정적으로 모니터링하며 자동으로 업데이트할 수 있습니다.. PowerShell DSC를 작성하여 자동화 DSC는 실제 및 가상 컴퓨터(VM)에서 Windows 또는 Linux를 사용하고 클라우드 또는 온-프레미스에서 컴퓨터 구성을 특정 상태로 정렬할 수 있습니다. 일관된 컨트롤로 지속적인 IT 서비스 배달을 사용하고 다른 유형의 하이브리드 IT 환경에 쉽고 신속하게 변경 사항을 관리할 수 있습니다.

Azure 자동화 DSC는 PowerShell DSC에 도입된 기본 사항을 기반으로 훨씬 간편한 구성 관리 환경을 제공합니다. Azure 자동화 DSC는 현재 Azure 자동화에서 PowerShell 스크립팅을 위해 제공하는 것과 동일한 관리 계층을 [PowerShell 필요한 상태 구성](https://msdn.microsoft.com/powershell/dsc/overview)에 제공합니다.

Azure 자동화 DSC를 사용하면 클라우드에서 [PowerShell 필요한 상태 구성을 작성 및 관리](https://technet.microsoft.com/library/dn249918.aspx)하고, [DSC 리소스](https://technet.microsoft.com/library/dn282125.aspx)를 가져오고, DSC 노드 구성(MOF 문서)을 생성할 수 있습니다. 이러한 DSC 항목은 클라우드 또는 온-프레미스의 대상 노드(예: 실제 및 가상 컴퓨터)에서 선택하고, 해당 노드에서 지정한 필요한 상태를 자동으로 준수하며, 필요한 상태 준수를 Azure 자동화에 다시 보고할 수 있도록 Azure 자동화 [DSC 끌어오기 서버](https://technet.microsoft.com/library/dn249913.aspx)에 배치됩니다.

읽기보다 비디오를 시청하기가 편하신가요? 2015년 5월 Azure 자동화 DSC가 처음 발표될 당시의 비디오를 아래에서 확인하세요. **참고:** 이 비디오에 언급된 개념 및 수명은 정확하지만 이 비디오가 기록된 이후에 Azure 자동화 DSC는 많이 발전되었습니다. 일반적으로 사용할 수 있고 Azure 포털의 UI는 보다 강력해졌으며 더 많은 기능을 제공하고 있습니다.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]



## Azure 자동화 DSC 용어 ##
### 구성 ###
PowerShell DSC에는 구성이라는 새로운 개념이 도입되었습니다. 구성에서는 PowerShell 구문을 통해 사용자 환경의 필요한 상태를 정의할 수 있습니다. DSC를 사용하여 환경을 구성하려면 먼저 구성 키워드를 사용하여 Windows PowerShell 스크립트 블록을 정의한 다음 그 뒤에 식별자와 블록을 구분하는 중괄호({})를 차례로 둡니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_1.png)

구성 블록 내에서 정확히 동일하게 구성해야 하는 사용자 환경의 노드(컴퓨터) 집합에 대한 필요한 구성을 지정하는 노드 구성 블록을 정의할 수 있습니다. 이러한 방식으로 노드 구성은 하나 이상의 노드에 대한 "역할"을 나타냅니다. 노드 구성 블록은 노드 키워드로 시작합니다. 이 키워드 뒤에는 역할 이름(변수 또는 식일 수 있음)이 있습니다. 역할 이름 뒤에는 노드 구성 블록을 구분하는 중괄호({})를 사용합니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_2.png)
 
노드 구성 블록 내에서 특정 DSC 리소스를 구성할 리소스 블록을 정의할 수 있습니다. 리소스 블록은 리소스 이름으로 시작하며, 그 뒤에 해당 블록에 대해 지정할 식별자와 블록을 구분하는 중괄호({})가 차례로 옵니다.

![대체 텍스트](./media/automation-dsc-overview/AADSC_3.png)

구성 키워드에 대한 자세한 내용은 [필요한 상태 구성의 구성 키워드 이해](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "필요한 상태 구성의 구성 키워드 이해")를 참조하세요.

DSC 구성을 실행(컴파일)하면 DSC 노드에서 필요한 상태를 준수하기 위해 적용하는 하나 이상의 DSC 노드 구성(MOF 문서)이 생성됩니다.

Azure 자동화 DSC를 사용하면 Azure 자동화에서 Runbook을 가져오고, 작성하고, 시작하는 것과 유사한 방법으로 DSC 구성을 가져오고, 작성하고, 컴파일할 수 있습니다.

>[AZURE.IMPORTANT] 구성은 해당 구성과 이름과 동일한 구성 블록을 Azure 자동화 DSC에 하나 이상 포함해야 합니다.


###노드 구성###

DSC 구성이 컴파일되면 구성의 노드 블록에 따라 하나 이상의 노드 구성이 생성됩니다. 노드 구성은 “MOF” 또는 “구성 문서”(해당 PS DSC 용어에 친숙한 경우)와 동일하고 웹 서버 또는 작업자와 같은 “역할”(예: 웹 서버 또는 작업자)을 나타내며 이는 하나 이상의 노드가 준수하기 위해 가정하고 확인해야 하는 필요한 상태입니다. Azure 자동화 DSC의 노드 구성 이름은 “Configuration Name.NodeConfigurationBlockName” 형식을 취합니다.

PS DSC 노드는 DSC 밀어넣기 또는 끌어오기 메서드를 통해 적용해야 하는 노드 구성을 인식하게 됩니다. Azure 자동화 DSC는 노드가 Azure 자동화 DSC 끌어오기 서버에서 적용해야 하는 노드 구성을 요청하는 DSC 끌어오기 메서드를 기반으로 합니다. 노드는 Azure 자동화 DSC에 요청하기 때문에 방화벽 뒤에 있을 수 있으며 모든 인바운드 포트가 닫혀 있을 수 있습니다. (직접 또는 프록시를 통해)인터넷에 대한 아웃바운드 액세스가 필요합니다.


###노드###

DSC 노드는 해당 구성이 DSC에 의해 관리되는 컴퓨터입니다. 즉, Windows 또는 Linux Azure VM, 온-프레미스 VM/실제 호스트 또는 다른 공용 클라우드의 컴퓨터일 수 있습니다. 노드는 해당 노드에서 정의한 필요한 상태를 준수하고 이를 유지 관리하기 위해 노드 구성을 적용하며, 해당 구성 상태 및 준수와 필요한 상태를 보고 서버에 다시 보고할 수 있습니다.

Azure 자동화 DSC는 Azure 자동화 DSC의 관리를 위한 노드 등록을 용이하게 하며, 각 노드의 서버 쪽에 할당된 노드 구성의 변경을 허용합니다. 따라서 다음에 노드가 서버에서 지침을 확인할 때는 다른 역할을 수행한다고 가정하고 구성하는 방법 및 일치하도록 보고해야 하는 준수 상태를 변경합니다.


###리소스###
DSC 리소스는 Windows PowerShell DSC(필요한 상태 구성) 구성을 정의하는 데 사용할 수 있는 구성 요소입니다. DSC는 파일 및 폴더, 서버 기능 및 역할, 레지스트리 설정, 환경 변수, 서비스 및 프로세스 등 기본 제공 리소스 집합과 함께 제공됩니다. 기본 제공 DSC 리소스의 전체 목록 및 이를 사용하는 방법에 대한 자세한 내용은 [기본 제공 Windows PowerShell 필요한 상태 구성 리소스](https://technet.microsoft.com/library/dn249921.aspx)를 참조하세요.

DSC 리소스를 PowerShell 모듈의 일부로 가져와 기본 제공 DSC 리소스 집합을 확장할 수도 있습니다. DSC 노드는 적용해야 하는 노드 구성에 기본이 아닌 리소스에 대한 참조가 포함된 경우 DSC 끌어오기 서버에서 이러한 리소스를 끌어올 수 있습니다. 사용자 지정 리소스를 만드는 방법에 대한 자세한 내용은 [사용자 지정 Windows PowerShell 필요한 상태 구성 리소스 빌드](https://technet.microsoft.com/library/dn249927.aspx)를 참조하세요.

Azure 자동화 DSC는 PS DSC와 모두 동일한 기본 제공 DSC 리소스와 함께 제공됩니다. 리소스가 포함된 PowerShell 모듈을 Azure 자동화로 가져와 Azure 자동화 DSC에 리소스를 추가할 수 있습니다.


###컴파일 작업###
Azure 자동화 DSC의 컴파일 작업은 하나 이상의 노드 구성을 만드는 구성 컴파일 인스턴스입니다. 이는 노드 구성을 만드는 것 외에는 실제로 아무 작업도 수행하지 않는다는 점을 제외하고는 Azure 자동화 Runbook 작업과 유사합니다. 컴파일 작업에서 만든 모든 노드 구성은 Azure 자동화 DSC 끌어오기 서버에 자동으로 배치되며, 이전 버전의 노드 구성(이 구성에 대해 존재하는 경우)을 덮어씁니다. 컴파일 작업에서 만들어진 노드 구성 이름은 “ConfigurationName.NodeConfigurationBlockName” 형식을 취합니다. 예를 들어 아래의 구성을 컴파일하면 "MyConfiguration.webserver"라는 단일 노드 구성이 생성됩니다.


![대체 텍스트](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE] Runbook과 마찬가지로 구성을 게시할 수 있습니다. 이는 DSC 항목을 Azure 자동화 DSC 끌어오기 서버에 두는 것과 관련이 없습니다. 컴파일 작업을 수행하면 DSC 항목이 Azure 자동화 DSC 끌어오기 서버에 배치됩니다. Azure 자동화의 "게시"에 대한 자세한 내용은 [Runbook 게시](https://msdn.microsoft.com/library/dn903765.aspx)를 참조하세요.


##Azure 자동화 DSC 수명 주기##
빈 자동화 계정부터 올바르게 구성된 관리 노드 집합까지에는 구성을 정의하고 해당 구성을 노드 구성으로 전환하며 온보딩 노드를 Azure 자동화 DSC 및 해당 노드 구성으로 전환하는 프로세스 집합이 포함됩니다. 다음 다이어그램에서는 Azure 자동화 DSC 수명 주기를 보여줍니다.

![대체 텍스트](./media/automation-dsc-overview/DSCLifecycle.png)


다음 이미지는 DSC의 수명 주기에서 자세한 단계별 프로세스를 보여줍니다. 구성을 Azure 자동화의 노드에 가져오고 적용하는 두 가지 방법, 온-프레미스 컴퓨터가 DSC를 지원하는 데 필요한 구성 요소 및 다른 구성 요소 간의 상호 작용을 포함합니다.

![DSC 아키텍처](./media/automation-dsc-overview/dsc-architecture.png)

##알려진 문제##

- WMF 5 RTM로 업그레이드할 때 컴퓨터가 Azure 자동화 DSC의 노드로 이미 등록되어 있으면 Azure 자동화 DSC에서 등록을 취소하고 WMF 5 RTM 업그레이드 후에 다시 등록합니다.

- Azure 자동화 DSC는 현재 부분 또는 복합 DSC 구성을 지원하지 않습니다. 그러나 DSC 복합 리소스는 로컬 PowerShell과 마찬가지로 가져와서 Azure Automation DSC 구성에서 사용될 수 있으며 이는 구성을 다시 사용하도록 합니다.

- Windows용 PowerShell DSC 에이전트가 Azure 자동화와 통신하려면 최신 버전의 WMF 5가 설치되어 있어야 합니다. 최신 버전 Linux용 PowerShell DSC 에이전트가 Azure 자동화와 통신하려면 Linux용으로 설치되어야 합니다.

- 기존 PowerShell DSC 끌어오기 서버의 경우 **ModuleName\_Version.zip”** 형식의 모듈 압축 파일이 있어야 합니다. Azure 자동화에서 **ModuleName.zip** 형식의 이름으로 PowerShell 모듈을 가져와야 합니다. Azure 자동화로 모듈을 가져오는 데 필요한 통합 모듈 형식에 대한 자세한 내용은 [이 블로그 게시물](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/)을 참조하세요.

- Azure 자동화로 가져온 PowerShell 모듈은 .doc 또는.docx 파일을 포함할 수 없습니다. DSC 리소스를 포함하는 일부 PowerShell 모듈은 도움말을 위해 이러한 파일을 포함하고 있습니다. Azure 자동화로 가져오기 전에 모듈에서 이러한 파일을 제거해야 합니다.

- Azure 자동화 계정에 노드를 처음 등록하거나 다른 노드 구성 서버 쪽에 매핑되도록 노드를 변경한 경우 노드의 상태가 현재 매핑된 노드 구성을 실제로 준수하지 않는 경우에도 해당 상태가 ‘준수’하는 것으로 표시됩니다. 노드에서 등록 후 또는 노드 구성 맵핑 변경 후 첫 번째 보고서를 보내면 노드 상태를 신뢰할 수 있습니다.

- 직접 등록 메서드를 사용하여 Azure 자동화 DSC에서 관리를 위해 Azure VM을 온보딩할 경우 VM이 Azure 자동화의 DSC 노드로 나타날 때까지 최대 1시간 정도가 걸립니다. 이것은 Azure 자동화 DSC의 VM을 온보드하기 위해 필요한 Azure VM DSC 확장에 의한 VM의 Windows 관리 프레임워크 5.0의 설치 때문에 꼭 필요한 일입니다.

- 등록 후에는 1년 후 만료되는 인증에 대해 각 노드가 자동으로 고유의 인증서를 협상합니다. 현재 PowerShell DSC 등록 프로토콜이 만료가 임박한 인증서를 자동으로 갱신할 수 없으므로 1년 기한 후 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 Windows Management Framework 5.0 RTM을 실행 중인지 확인합니다. 노드의 인증 인증서가 만료되고 노드가 다시 등록되지 않은 경우, 노드가 Azure 자동화와 통신할 수 없으며 '응답 없음'으로 표시됩니다. 재등록은 노드를 처음 등록할 때와 같은 방식으로 수행됩니다. 노드 만료 시점으로부터 90일 안이나, 인증서 만료 이후에 재등록을 수행하면 새 인증서를 생성하여 사용하게 됩니다.

- WMF 5 RTM로 업그레이드할 때 컴퓨터가 Azure 자동화 DSC의 노드로 이미 등록되어 있으면 Azure 자동화 DSC에서 등록을 취소하고 WMF 5 RTM 업그레이드 후에 다시 등록합니다. 다시 등록하기 전에 $env:windir\\system32\\configuration\\DSCEngineCache.mof 파일을 삭제합니다.

- PowerShell DSC cmdlet은 WMF 5 프로덕션 미리 보기 위에 WMF 5 RTM가 설치된 경우 작동하지 않을 수 있습니다. 이 문제를 해결하려면 관리자 권한 PowerShell 세션(관리자 권한으로 실행)에서 다음 명령을 실행합니다. `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`
 

##관련 문서##

- [Azure 자동화 DSC를 통한 관리를 위한 컴퓨터 온보드](../automation/automation-dsc-onboarding.md)
- [Azure 자동화 DSC에서 구성을 컴파일](../automation/automation-dsc-compile.md)
- [Azure 자동화 DSC cmdlets](https://msdn.microsoft.com/library/mt244122.aspx)
- [Azure 자동화 DSC 가격 책정](https://azure.microsoft.com/pricing/details/automation/)
- [Azure 자동화 DSC 및 Chocolatey를 사용하여 IaaS VM에 연속 배포](automation-dsc-cd-chocolatey.md)

<!---HONumber=AcomDC_0921_2016-->