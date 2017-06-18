<properties
	pageTitle="가상 컴퓨터 규모 집합 개요 | Microsoft Azure"
	description="가상 컴퓨터 규모 집합에 대해 자세히 알아봅니다."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="guybo"/>

# 가상 컴퓨터 규모 집합 개요

가상 컴퓨터 규모 집합은 동일한 VM 집합을 배포하고 관리하는데 사용할 수 있는 Azure 계산 리소스입니다. 모든 VM이 동일하게 설정되는 VM 규모 집합은 자동 크기 조정을 충실하게 지원하도록 설계되었습니다. VM 사전 프로비전이 필요하지 않으며, 큰 계산, 빅 데이터 및 컨테이너식 워크로드를 대상으로 하는 대규모 서비스를 쉽게 구축할 수 있도록 합니다.

계산 리소스 크기를 조정해야 하는 응용 프로그램의 경우 크기 조정 작업은 장애 도메인 및 업데이트 도메인 간에 암시적으로 균형이 조정됩니다. VM 규모 집합에 대한 소개는 [Azure 블로그 공지](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)를 참조하세요.

VM 규모 집합에 대한 자세한 정보는 이러한 비디오를 살펴보세요.

 - [Mark Russinovich의 Azure 규모 집합 설명](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)

 - [Guy Bowerman과 가상 컴퓨터 규모 집합](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## VM 규모 집합 만들기 및 관리

_new_를 선택하고 검색 표시줄에 "scale"을 입력하여 [Azure 포털](https://portal.azure.com)에 VM 크기 집합을 만들 수 있습니다. 결과에 "가상 컴퓨터 규모 집합"이 표시됩니다. 여기에서 필수 필드를 입력하여 규모 집합을 사용자 지정하고 배포할 수 있습니다.

VM 규모 집합은 개별적인 Azure Resource Manager VM과 마찬가지로 JSON 템플릿 및 [REST API](https://msdn.microsoft.com/library/mt589023.aspx)를 사용하여 정의하고 배포할 수도 있습니다. 따라서 모든 표준 Azure 리소스 관리자 배포 방법을 사용할 수 있습니다. 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)을 참조하세요.

VM 규모 집합에 대한 예제 템플릿의 집합은 Azure 빠른 시작 템플릿 GitHub 리포지토리 [여기](https://github.com/Azure/azure-quickstart-templates)에서 찾을 수 있습니다. 제목에서 _vmss_가 있는 템플릿을 찾아 보세요.

이 템플릿의 세부 정보 페이지에는 포털 배포 기능으로 연결되는 단추가 있습니다. VM 규모 집합을 배포하려면 단추를 클릭하고 포털에 필요한 매개 변수를 채웁니다. 리소스에서 대문자 또는 대/소문자 혼용이 지원되는지 확실하지 않으면 항상 소문자 매개 변수 값을 사용하는 것이 안전합니다. VM 규모 집합 템플릿에 대한 유용한 분석 동영상도 있습니다.

[VM 규모 집합 템플릿 분석](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## VM 규모 집합 확장 및 축소

VM 규모 집합에서 가상 컴퓨터의 수를 늘리거나 줄이려면, _용량_ 속성만 변경하고 템플릿을 다시 배포합니다. Azure 자동 크기 조정에서 지원되지 않는 사용자 지정 크기 조정(custom scale) 이벤트를 정의하려는 경우 이러한 단순성을 통해 사용자 지정 크기 조정 계층을 쉽게 작성할 수 있습니다.

용량을 변경하기 위해서 템플릿을 다시 배포하는 경우 SKU와 업데이트된 용량만 포함하는 훨씬 더 작은 템플릿을 정의할 수 있습니다. [여기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)에 예제가 나와 있습니다.

자동으로 크기가 조정되는 규모 집합을 만드는 단계를 살펴보려면 [가상 컴퓨터 규모 집합에서 자동으로 컴퓨터 규모 조정](virtual-machine-scale-sets-windows-autoscale.md)을 참조하세요.

## VM 규모 집합 모니터링

[Azure 포털](https://portal.azure.com)은 규모 집합을 표시하고 기본 속성을 제공할 뿐 아니라 집합에 VM 목록을 표시합니다. 자세한 내용은 [Azure Resource Explorer](https://resources.azure.com)를 사용하여 VM 규모 집합을 보는 것이 좋습니다. VM 규모 집합은 Microsoft.Compute의 리소스이므로 이 사이트에서 다음 링크를 확장하여 VM 규모 집합을 볼 수 있습니다.

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## VM 규모 집합 시나리오

이 섹션에서는 몇 가지 일반적인 VM 규모 집합 시나리오를 나열합니다. 높은 수준의 Azure 서비스(예: 배치, 서비스 패브릭, Azure 컨테이너 서비스)에서 이 시나리오를 사용할 예정입니다.

 - **RDP/SSH에서 VM 규모 집합 인스턴스** - VM 규모 집합은 VNET 내부에 생성되며, 규모 집합의 개별 VM에 공용 IP 주소가 할당되지 않습니다. 일반적으로 계산 그리드에 있는 모든 상태 비저장 리소스에 별도의 공용 IP 주소를 할당하는 비용 및 관리 오버헤드를 원하지 않고, 부하 분산 장치 또는 독립 실행형 가상 컴퓨터와 같이 공용 IP 주소가 있는 리소스를 포함하여 VNET의 다른 리소스에서 VM에 쉽게 연결할 수 있기 때문에 이렇게 하는 것이 좋습니다.

 - **NAT 규칙을 사용하여 VM에 연결** - 공용 IP 주소를 만들고, 부하 분산 장치에 할당하고, IP 주소의 포트를 VM 규모 집합에 포함된 VM의 포트에 매핑하는 인바운드 NAT 규칙을 정의할 수 있습니다. 예:
 
	원본 | 원본 포트 | 대상 | 대상 포트
	--- | --- | --- | ---
	공용 IP | 포트 50000 | vmss\_0 | 포트 22
	공용 IP | 포트 50001 | vmss\_1 | 포트 22
	공용 IP | 포트 50002 | vmss\_2 | 포트 22

	하나의 공용 IP를 사용하여 규모 집합에 포함된 모든 VM에 SSH 연결을 사용하도록 설정하기 위해서 NAT 규칙을 사용하는 VM 규모 집합을 만드는 예제가 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)에 있습니다.

	RDP 및 Windows에서 동일한 작업을 수행하는 예제가 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)에 있습니다.

 - **"jumpbox"를 사용하여 VM에 연결** - 동일한 VNET에 VM 규모 집합과 독립 실행형 VM을 만들면, 독립 실행형 VM과 VM 규모 집합 VM은 VNET/Subnet에 정의된 대로 내부 IP 주소를 사용하여 서로 연결할 수 있습니다. 공용 IP 주소를 만들어서 독립 실행형 VM에 할당하면 독립 실행형 VM에 RDP 또는 SSH를 사용할 수 있고 해당 컴퓨터에서 사용자의 VM 규모 집합 인스턴스로 연결할 수 있습니다. 본질적으로 간단한 VM 규모 집합이 기본 구성에 공용 IP 주소를 포함하는 간단한 독립 실행형 VM보다 더 안전하다는 것을 알 수 있습니다.

	[이러한 방식의 예제에서 이 템플릿은 VM 규모 집합 기반 VM 클러스터를 관리하는 독립 실행형 마스터 VM으로 구성된 간단한 Mesos 클러스터를 만듭니다.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **VM 규모 집합 인스턴스에 부하 분산** - "라운드 로빈" 방식을 사용하여 VM 계산 클러스터에 작업을 전달하려면, 그에 맞게 Azure Load Balancer에 부하 분산 규칙을 구성합니다. 지정된 프로토콜, 간격, 요청 경로로 포트를 Ping하여 응용 프로그램이 실행되는지 확인하는 프로브를 정의할 수 있습니다. 또한 Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/)는 보다 복잡한 부하 분산 시나리오와 함께 규모 집합을 지원합니다.

	[IIS 웹 서버를 실행하는 VM으로 구성되는 VM 규모 집합을 만들고 부하 분산 장치를 사용하여 각 VM이 수신하는 부하를 분산하는 예제가 있습니다. 또한 HTTP 프로토콜을 사용하여 각 VM의 특정 URL을 ping합니다.](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (Microsoft.Network/loadBalancers 리소스 종류를 살펴보고 virtualMachineScaleSet의 networkProfile 및 extensionProfile을 살펴보세요.)

 - **VM 규모 집합을 PaaS 클러스터 관리자에서 계산 클러스터로 배포** - VM 규모 집합을 차세대 작업자 역할이라고 설명하기도 합니다. 이 설명이 타당하기는 하지만 규모 집합 기능을 PaaS v1 작업자 역할 기능과 혼동할 위험이 있습니다. VM 규모 집합은 플랫폼/런타임 독립적이고, 사용자 지정이 가능하며, Azure 리소스 관리자 IaaS로 통합되는 일반화된 계산 리소스를 제공한다는 점에서 진정한 “작업자 역할” 또는 작업자 리소스를 제공합니다.

	PaaS v1 작업자 역할은 플랫폼/런타임 지원(Windows 플랫폼 이미지에 대해서만)에 관해서는 제한되어 있지만 VIP 교환, 구성 가능한 업그레이드 설정, VM 규모 집합에 _아직_ 제공되지 않는 또는 서비스 패브릭 같은 다른 높은 수준의 PaaS 서비스에 의해 제공될 예정인 런타임/앱 배포별 설정 같은 서비스를 포함합니다. 이것을 고려하여 VM 규모 집합을 PaaS를 지원하는 인프라의 하나로 주목할 수 있습니다. 즉, 서비스 패브릭 같은 PaaS 솔루션 또는 Mesos 같은 클러스터 관리자는 VM 규모 집합을 기반으로 확장성 있는 계산 계층으로 구축될 수 있습니다.

	[이러한 방식의 예제에서 이 템플릿은 VM 규모 집합 기반 VM 클러스터를 관리하는 독립 실행형 마스터 VM으로 구성된 간단한 Mesos 클러스터를 만듭니다.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) 앞으로 출시할 [Azure 컨테이너 서비스](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) 버전은 VM 규모 집합을 기반으로 더 복잡하고/확정적인 버전의 시나리오를 배포할 예정입니다.

## VM 규모 집합 성능 및 크기 조정 지침

- 여러 개의 VM 규모 집합에 VM을 한번에 500개 이상 만들지 마세요.
- 저장소 계정별로 최대 20개(최대 40개까지 가능하도록 _overprovision_ 속성을 "false"로 설정하지 않은 이상)의 VM에 대해 계획합니다.
- 저장소 계정 이름의 첫 문자를 최대한 골고루 사용합니다. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/)의 예제 VMSS 템플릿에 방법에 대한 예제가 제공되어 있습니다.
- 사용자 지정 VM을 사용하는 경우, 하나의 저장소 계정에 VM 규모 집합당 VM을 40개 이하로 계획합니다. VM 규모 집합 배포를 시작하려면 그 전에 저장소 계정에 이미지를 미리 복사해 두어야 합니다. 자세한 내용은 FAQ를 참조하세요.
- VNET당 VM을 4096개 이하로 계획합니다.
- 만들 수 있는 VM 개수는 배포하려는 지역의 코어 할당량에 따라 제한됩니다. 클라우드 서비스 또는 IaaS v1에 사용할 코어 한도가 높더라도, 계산 할당량 한도를 높이려면 고객 지원팀에 문의해야 합니다. 할당량을 쿼리하려면 Azure CLI 명령 `azure vm list-usage` 및 PowerShell 명령 `Get-AzureRmVMUsage`를 실행합니다. PowerShell 1.0 이전 버전을 사용하는 경우에는 `Get-AzureVMUsage`를 사용합니다.

## VM 규모 집합 질문과 대답

**Q.** VM 규모 집합에 포함할 수 있는 VM의 수는 몇 개입니까?

**A.** 여러 저장소 계정으로 배포될 수 있는 플랫폼 이미지를 사용하는 경우 100개입니다. 현재 사용자 지정 이미지는 단일 저장소 계정으로 제한되어 있으므로 최대 40개까지(_overprovision_ 속성을 "false"로 설정한 경우, 기본값은 20) 사용할 수 있습니다.

**Q.** VM 규모 집합에 존재하는 다른 리소스 제한 사항은 무엇입니까?

**A.** 10분 동안 지역당 다수의 규모 집합에 VM을 500개 이하로 생성하도록 제한됩니다. 기존 [Azure 구독 서비스 제한/](../azure-subscription-service-limits.md)을 적용합니다.

**Q.** VM 규모 집합 내에서 데이터 디스크가 지원됩니까?

**A.** 초기 릴리스에서는 지원되지 않습니다. 데이터 저장 옵션은 다음과 같습니다.

- Azure 파일(SMB 공유 드라이브)

- OS 드라이브

- 임시 드라이브(Azure 저장소에서 지원되지 않는 로컬 드라이브)

- Azure 데이터 서비스(예: Azure 테이블, Azure Blob)

- 외부 데이터 서비스(예: 원격 DB)

**Q.** VM 규모 집합을 지원하는 Azure 지역은 어디인가요?

**A.** Azure 리소스 관리자를 지원하는 지역은 VM 규모 집합을 지원합니다.

**Q.** 사용자 지정 이미지를 사용하여 VM 규모 집합을 어떻게 만드나요?

**A.** vhdContainers 속성을 비워둡니다. 아래 예제를 참조하세요.

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
			},
			"osType": "Windows"
		}
	},


**Q.** VM 규모 집합 용량을 20에서 15로 줄이면, 어떤 VM이 제거되나요?

**A.** 가용성을 최대화하기 위해 업그레이드 도메인과 오류 도메인 간에 균등하도록 가상 컴퓨터가 규모 집합에서 제거 됩니다. ID가 가장 높은 VM이 먼저 제거됩니다.

**Q.** 그런 다음 용량을 15에서 18로 늘리면 어떻게 되나요?

**A.** 용량을 18로 늘리면 3개의 새로운 VM 생성됩니다. VM 인스턴스 ID가 이전의 가장 큰 값(예: 20, 21, 22)에서 증가될 때마다 VM은 FD와 UD에서 균형이 조정됩니다.

**Q.** VM 규모 집합에서 여러 확장을 사용하는 경우, 실행 순서를 강제로 적용할 수 있습니까?

**A.** 직접적으로는 불가능하지만 customScript 확장의 경우 다른 확장이 완료될 때까지 사용자의 스크립트를 대기시킬 수 있습니다([예: 확장 로그 모니터링](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). 확장 시퀀싱에 대한 추가 지침은 블로그 게시물 [Azure VM 크기 집합의 확장 시퀀싱](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)에서 찾을 수 있습니다.

**Q.** VM 규모 집합은 Azure 가용성 집합과 작업이 가능합니까?

**A.** 예. VM 규모 집합은 FD 5개와 UD 5개를 포함하는 암시적인 가용성 집합입니다. virtualMachineProfile에 대해 아무것도 구성할 필요가 없습니다. 앞으로 출시되는 릴리스에서 VM 규모 집합이 여러 테넌트로 확장될 가능성이 있지만 현재 규모 집합은 단일 가용성 집합입니다.

<!---HONumber=AcomDC_0914_2016-->