이 문서는 확장성, 가용성, 관리 효율성 및 보안에 주의하면서 Azure에서 Windows VM(가상 컴퓨터)을 실행하기 위한 일련의 검증된 작업 방식을 간략하게 설명합니다.

> [AZURE.NOTE] Azure에는 [Azure Resource Manager][resource-manager-overview] 및 클래식이라는 두 가지 서로 다른 배포 모델이 있습니다. 이 문서에서는 새로운 배포를 위해 Microsoft에서 권장하는 리소스 관리자를 사용합니다.

Azure에는 단일 VM에 대한 가동 시간 SLA(서비스 수준 약정)가 없으므로 프로덕션 워크로드에는 단일 VM을 사용하지 않는 것이 좋습니다. SLA를 가져오려면 [가용성 집합][availability-set]에 여러 VM을 배포해야 합니다. 자세한 내용은 [Azure에서 여러 Windows VM 실행][multi-vm]을 참조하세요.

## 아키텍처 다이어그램

Azure에서 VM을 프로비전할 때에는 VM 자체 이외에도 움직일 부분이 많습니다. 계산, 네트워킹 및 저장소 요소가 있습니다.

![[0]][0]

- **리소스 그룹.** [_리소스 그룹_][resource-manager-overview]은 관련된 리소스를 보유하는 컨테이너입니다. 이 VM에 대한 리소스를 보유할 리소스 그룹을 만듭니다.

- **VM**. 게시된 이미지 목록 또는 Azure Blob 저장소에 업로드된 VHD(가상 하드 디스크) 파일에서 VM을 프로비전할 수 있습니다.

- **OS 디스크.** OS 디스크는 [Azure storage][azure-storage]에 저장된 VHD입니다. 즉, 호스트 컴퓨터가 중단되어도 유지됩니다.

- **임시 디스크.** VM은 임시 디스크를 사용하여 만들어집니다(Windows에서 `D:` 드라이브). 이 디스크는 호스트 컴퓨터의 실제 드라이브에 저장됩니다. Azure storage에는 저장되지 _않으며_ 다시 부팅되는 동안에 그리고 다른 VM의 수명 주기 이벤트 동안에 사라질 수 있습니다. 페이지 또는 스왑 파일과 같은 임시 데이터에 대해서만 이 디스크를 사용합니다.

- **데이터 디스크.** [데이터 디스크][data-disk]는 응용 프로그램 데이터에 사용되는 영구 VHD입니다. 데이터 디스크는 OS 디스크와 같은 Azure 저장소에 저장됩니다.

- **가상 네트워크(VNet) 및 서브넷.** Azure의 모든 VM은 서브넷으로 세분화되는 VNet에 배포됩니다.

- **공용 IP 주소.** 공용 IP 주소는 예를 들어, 원격 데스크톱 (RDP)을 통해 VM과 통신하는 데 필요합니다.

- **NIC(네트워크 인터페이스)**. NIC를 통해 VM을 가상 네트워크와 통신하도록 할 수 있습니다.

- **NSG(네트워크 보안 그룹)**. [NSG][nsg]는 서브넷에 대한 네트워크 트래픽을 허용/거부하는 데 사용됩니다. NSG를 개별 NIC 또는 서브넷에 연결할 수 있습니다. 서브넷에 연결하는 경우 NSG 규칙이 해당 서브넷의 모든 VM에 적용됩니다.
 
- **진단** VM을 관리 및 문제 해결하는 데 진단 로깅이 중요합니다.

## 추천

### VM 권장 사항

- 고성능 컴퓨팅과 같은 특수한 워크로드가 발생하지 않는다면 DS 및 GS 시리즈가 좋습니다. 자세한 내용은 [가상 컴퓨터 크기][virtual-machine-sizes]를 참조하세요. 기존 워크로드를 Azure로 이동할 때 온-프레미스 서버와 가장 근접하게 일치하는 VM 크기부터 사용하기 시작합니다. 그런 다음 CPU, 메모리, 디스크 IOPS(초당 입력/출력 작업 수)에 따라 실제 워크로드의 성능을 측정하고 필요에 따라 크기를 조정합니다. 또한 여러 NIC가 필요한 경우 각 크기에 대한 NIC 제한을 알아야 합니다.

- VM 및 기타 리소스를 프로비전할 경우 위치를 지정해야 합니다. 일반적으로 내부 사용자 또는 고객에게 가장 가까운 위치를 선택합니다. 그러나 일부 위치에서는 일부 VM 크기를 사용할 수 없을 수 있습니다. 자세한 내용은 [지역별 서비스][services-by-region]를 참조하세요. 지정된 위치에서 사용할 수 있는 VM 크기를 나열하려면 다음 Azure CLI(명령줄 인터페이스) 명령을 실행합니다.

    ```
    azure vm sizes --location <location>
    ```

- 게시된 VM 이미지를 선택하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터 이미지 탐색 및 선택][select-vm-image]을 참조하세요.

### 디스크 및 저장소 권장 사항

- 최상의 디스크 I/O 성능을 위해서는 SSD(반도체 드라이브)에 데이터를 저장하는 [프리미엄 저장소][premium-storage]가 권장됩니다. 비용은 프로비전된 디스크 크기를 기준으로 산정됩니다. IOPS 및 처리량도 디스크 크기에 따라 달라지므로 디스크를 프로비전할 때 세 가지 요소(용량, IOPS, 처리량)를 모두 고려합니다.

- 저장소 계정 하나가 1~20개의 VM을 지원할 수 있습니다.

- 하나 이상의 데이터 디스크를 추가합니다. 새 VHD를 만들 때 형식은 지정되지 않습니다. 디스크를 포맷하려면 VM에 로그인합니다.

- 데이터 디스크 수가 많은 경우 저장소 계정의 총 I/O 제한에 주의해야 합니다. 자세한 내용은 [가상 컴퓨터 디스크 제한][vm-disk-limits]을 참조하세요.

- 최상의 성능을 위해 진단 로그를 저장할 별도의 저장소 계정을 만듭니다. 표준 LRS(로컬 중복 저장소) 계정은 진단 로그에 충분합니다.

- 가능한 경우 OS 디스크가 아닌 데이터 디스크에 응용 프로그램을 설치합니다. 그러나 일부 레거시 응용 프로그램은 C: 드라이브에 해당 구성 요소를 설치해야 할 수 있습니다. 이 경우 PowerShell을 사용하여 [OS 디스크의 크기를 조정][resize-os-disk]합니다.

### 네트워크 권장 사항

- 이 공용 IP 주소는 동적 또는 정적일 수 있습니다. 기본값은 동적입니다.

    - 변경되지 않는 고정 IP 주소가 필요한 경우 [정적 IP 주소][static-ip]를 예약합니다(예를 들어 DNS에 A 레코드를 만들어야 하는 경우 또는 허용 목록에 추가할 IP 주소가 필요한 경우).

    - IP 주소의 FQDN(정규화된 도메인 이름)을 만들 수도 있습니다. 그런 후 FQDN을 가리키는 DNS에 [CNAME 레코드][cname-record]를 등록할 수 있습니다. 자세한 내용은 [Azure 포털에서 정규화된 도메인 이름 만들기][fqdn]를 참조하세요.

- 모든 NSG에는 모든 인바운드 인터넷 트래픽을 차단하는 규칙을 포함하여 [기본 규칙][nsg-default-rules] 집합이 있습니다. 기본 규칙은 삭제할 수 없으나 다른 규칙으로 재정의할 수 있습니다. 인터넷 트래픽을 사용하도록 설정하려면 특정 포트(예: HTTP용 포트 80)로의 인바운드 트래픽을 허용하는 규칙을 만듭니다.

- RDP를 사용하도록 설정하려면 TCP 포트 3389에 인바운드 트래픽을 허용하는 NSG 규칙을 추가합니다.

## 확장성 고려 사항

- [VM 크기를 변경][vm-resize]하여 VM 규모를 확장하거나 축소할 수 있습니다.

- 규모를 확장하려면 부하 분산 장치를 기준으로 두 개 이상의 VM을 가용성 집합에 추가합니다. 자세한 내용은 [Azure에서 여러 Windows VM 실행][multi-vm]을 참조하세요.

## 가용성 고려 사항

- 앞서 설명한 것처럼 단일 VM에 대한 SLA는 없습니다. SLA를 가져오려면 가용성 집합에 여러 VM을 배포해야 합니다.

- 사용자의 VM은 [계획된 유지 관리][planned-maintenance] 또는 [계획되지 않은 유지 관리][manage-vm-availability]의 영향을 받을 수 있습니다. [VM 다시 부팅 로그][reboot-logs]를 사용하여 VM 재부팅이 계획된 유지 관리로 발생했는지 여부를 결정할 수 있습니다.

- VHD는 [Azure Storage][azure-storage]에 의해 지원되며 내구성 및 가용성을 위해 복제됩니다.

- 정상 작업 중 실수로 인한 데이터 손실(예: 사용자 오류 때문에 발생)을 막기 위해 [Blob 스냅숏][blob-snapshot] 또는 다른 도구를 사용하여 지정 시간 백업도 구현해야 합니다.

## 관리 효율성 고려 사항

- **리소스 그룹.** 동일한 수명 주기를 공유하는 긴밀하게 결합된 리소스를 동일한 [리소스 그룹][resource-manager-overview]에 추가합니다. 리소스 그룹을 사용하여 리소스를 그룹 단위로 배포 및 모니터링하고, 리소스 그룹별로 비용을 청구할 수 있습니다. 리소스를 하나의 집합으로 삭제할 수도 있습니다. 이러한 기능은 테스트 배포에서 매우 유용합니다. 리소스에 의미 있는 이름을 지정합니다. 이렇게 하면 보다 쉽게 특정 리소스를 찾고 역할을 이해할 수 있습니다. [Azure 리소스에 대한 권장되는 명명 규칙][naming conventions]을 참조하세요.

- **VM 진단.** 기본 상태 메트릭, 진단 인프라 로그 및 [부팅 진단][boot-diagnostics]을 모니터링 및 진단을 사용하도록 설정할 수 있습니다. 부팅 진단은 VM이 부팅할 수 없는 상태로 전환되는 경우 부팅 오류를 진단하는 데 도움이 될 수 있습니다. 자세한 내용은 [모니터링 및 진단 사용][enable-monitoring]을 참조하세요. Azure 플랫폼 로그를 수집하고 이를 Azure Storage에 업로드하는 데는 [Azure 로그 수집][log-collector] 확장을 사용합니다.

    다음 CLI 명령을 통해 진단을 사용할 수 있습니다.

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **VM 중지.** Azure에서는 "중지됨"과 "할당 취소됨" 상태를 구분합니다. VM 상태가 "중지됨"이면 비용이 청구됩니다. VM이 할당 취소되면 비용이 청구되지 않습니다.

    VM을 할당 취소하려면 다음 CLI 명령을 사용합니다.

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Azure 포털의 **중지** 버튼 또한 VM 할당을 취소합니다. 그러나 로그인한 상태에서 OS를 통해 종료하면 VM은 중지되지만 할당 취소되지 _않으므로_ 비용이 계속 청구됩니다.

- **VM 삭제.** VM을 삭제하는 경우 VHD는 삭제되지 않습니다. 즉, 데이터 손실 없이 안전하게 VM을 삭제할 수 있습니다. 그러나 저장소에 대한 비용은 계속 청구됩니다. VHD를 삭제하려면 [Blob 저장소][blob-storage]에서 파일을 삭제합니다.

  실수로 삭제하지 않도록 하려면 [리소스 잠금][resource-lock]을 사용하여 전체 리소스 그룹을 잠그거나 VM과 같은 개별 리소스를 잠급니다.

## 보안 고려 사항

- [Azure 보안 센터][security-center]를 사용하여 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다. 보안 센터는 시스템 업데이트, 맬웨어 방지와 같은 잠재적인 보안 문제를 모니터링하고 배포의 보안 상태에 대한 종합적인 그림을 제공합니다.

    - 보안 센터는 각 Azure 구독을 기준으로 구성됩니다. [보안 센터 사용]에 설명된 것처럼 보안 데이터 수집을 사용하도록 설정합니다.

    - 데이터 수집이 사용되도록 설정되면 보안 센터는 해당 구독에서 만든 모든 VM을 자동으로 검색합니다.

- **패치 관리.** 이 기능이 설정된 경우 보안 센터는 보안 및 중요 업데이트 누락 여부를 확인합니다. VM의 [그룹 정책 설정][group-policy] 사용하여 자동 시스템 업데이트를 사용하도록 설정합니다.

- **맬웨어 방지.** 이 기능이 설정되면 보안 센터는 맬웨어 방지 소프트웨어 설치 여부를 확인합니다. 또한 보안 센터를 사용하여 Azure 포털 내에서 맬웨어 방지 소프트웨어를 설치할 수도 있습니다.

- RBAC([역할 기반 액세스 제어][rbac])를 사용하여 배포하는 Azure 리소스에 대한 액세스를 제어합니다. RBAC를 통해 DevOps 팀의 구성원에게 권한 역할을 할당할 수 있습니다. 예를 들어 읽기 권한자 역할은 Azure 리소스를 볼 수 있지만 만들거나 관리하거나 삭제할 수는 없습니다. 일부 역할은 특정 Azure 리소스 유형에 따라 다릅니다. 예를 들어 가상 컴퓨터 참가자 역할은 VM을 다시 시작하거나 할당을 취소하고, 관리자 암호를 재설정하고, 새 VM을 만드는 등의 작업을 수행할 수 있습니다. 이 참조 아키텍처에 유용할 수 있는 기타 [기본 제공 RBAC 역할][rbac-roles]에는 [DevTest Labs 사용자][rbac-devtest] 및 [네트워크 참가자][rbac-network]가 포함됩니다. 한 명의 사용자가 여러 역할에 할당될 수 있으며 좀 더 세분화된 권한의 사용자 지정 역할을 만들 수도 있습니다.

    > [AZURE.NOTE] RBAC는 VM에 로그온한 사용자가 수행할 수 있는 작업을 제한하지 않습니다. 이러한 사용 권한은 게스트 OS의 계정 유형에 따라 결정됩니다.

- 로컬 관리자 암호를 재설정하려면 `vm reset-access` Azure CLI 명령을 실행합니다.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- [감사 로그][audit-logs]를 사용하여 프로비전 동작 및 기타 VM 이벤트를 확인합니다.

- OS 및 데이터 디스크를 암호화해야 하는 경우 [Azure 디스크 암호화][disk-encryption]를 고려하세요.

## 솔루션 구성 요소

샘플 솔루션 스크립트인 [Deploy-ReferenceArchitecture.ps1][solution-script]을 이 문서에 설명된 권장 사항에 따라 아키텍처를 구현하는 데 사용할 수 있습니다. 이 스크립트는 [Resource Manager][ARM-Templates] 템플릿을 사용합니다. 템플릿은 각각이 VNet 만들기 또는 NSG 구성 등의 특정 동작을 수행하는 기본 구성 요소 집합으로 사용할 수 있습니다. 스크립트의 목적은 템플릿 배포를 오케스트레이션하는 것입니다.

템플릿은 매개 변수화되며 해당 매개 변수는 별도의 JSON 파일에 포함됩니다. 이러한 파일의 매개 변수를 수정하여 사용자의 요구에 맞게 배포를 구성할 수 있습니다. 템플릿 자체를 수정할 필요는 없습니다. 매개 변수 파일에 있는 개체의 스키마는 변경하지 말아야 합니다.

템플릿을 편집할 때는 [Azure 리소스에 대한 권장되는 명명 규칙][naming conventions]에 설명된 명명 규칙을 따르는 개체를 만듭니다.

스크립트는 다음 매개 변수 파일을 참조하여 VM 및 주변 인프라를 빌드합니다.

- **[virtualNetwork.parameters.json][vnet-parameters]**. 이 파일은 이름, 주소 공간, 서브넷 및 필요한 모든 DNS 서버의 주소 등과 같은 VNet 설정을 정의합니다. 서브넷 주소는 VNet의 주소 공간에 포함되어야 합니다.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json#L4-L21 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg",
        "addressPrefixes": [
          "172.17.0.0/16"
        ],
        "subnets": [
          {
            "name": "ra-single-vm-sn",
            "addressPrefix": "172.17.0.0/24"
          }
        ],
        "dnsServers": [ ]
      }
    }
  }
	```

- **[networkSecurityGroups.parameters.json][nsg-parameters]**. 이 파일에는 NSG의 정의 및 NSG 규칙이 포함되어 있습니다. `virtualNetworkSettings` 블록의 `name` 매개 변수는 NSG가 연결된 VNet을 지정합니다. `networkSecurityGroupSettings` 블록의 `subnets` 매개 변수는 VNet에서 NSG 규칙을 적용하는 모든 서브넷을 식별합니다. 이러한 항목은 **virtualNetwork.parameters.json** 파일에 정의되어 있습니다.

	예제에 표시되는 기본 보안 규칙을 사용하여 RDP(원격 데스크톱) 연결을 통해 VM에 연결할 수 있습니다. `securityRules` 배열에 항목을 더 추가하여 추가 포트를 열거나 특정 포트를 통해 액세스를 거부할 수 있습니다.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json#L4-L36 -->
	```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "networkSecurityGroupsSettings": {
      "value": [
        {
          "name": "ra-single-vm-nsg",
          "subnets": [
            "ra-single-vm-sn"
          ],
          "networkInterfaces": [
          ],
          "securityRules": [
            {
              "name": "RDPAllow",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      ]
    }
  }
	```

- **[virtualMachineParameters.json][vm-parameters]**. 이 파일은 VM의 이름 및 크기, 관리자의 보안 자격 증명, 만들 디스크 및 이러한 디스크를 저장할 저장소 계정을 포함하여 VM 자체에 대한 설정을 정의합니다.

	`imageReference` 섹션에서 이미지를 지정해야 합니다. 아래 표시된 값은 Windows Server 2012 R2 Datacenter의 최신 빌드를 사용하여 VM을 만듭니다. 다음 Azure CLI 명령을 사용하여 지역에서 사용 가능한 모든 Windows 이미지의 목록을 가져올 수 있습니다(이 예제에서는 westus 영역 사용).

	```text
	azure vm image list westus MicrosoftWindowsServer WindowsServer
	```

	`nics` 섹션의 `subnetName` 매개 변수가 VM에 대한 서브넷을 지정합니다. 마찬가지로 `virtualNetworkSettings`의 `name` 매개 변수는 사용할 VNet을 식별합니다. 이러한 값은 **virtualNetwork.parameters.json** 파일에 정의된 서브넷 및 VNet의 이름입니다.

	저장소 계정을 공유하거나 `buildingBlockSettings` 섹션에서 설정을 수정하여 자신의 저장소 계정을 사용하여 여러 VM을 만들 수 있습니다. 여러 VM을 만드는 경우 `availabilitySet` 섹션에서 사용하거나 만들 가용성 집합의 이름을 지정해야 합니다.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json#L4-L64 -->
	```json
   "parameters": {
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-single-vm",
        "computerNamePrefix": "cn",
        "size": "Standard_DS1_v2",
        "osType": "windows",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "sshPublicKey": "",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "true",
            "subnetName": "ra-single-vm-sn",
            "privateIPAllocationMethod": "dynamic",
            "publicIPAllocationMethod": "dynamic",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2012-R2-Datacenter",
          "version": "latest"
        },
        "dataDisks": {
          "count": 2,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": ""
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 1,
        "vmCount": 1,
        "vmStartIndex": 0
      }
    }
  }
	```

## 솔루션 배포

솔루션에서는 다음과 같은 필수 구성 요소를 가정합니다.

- 리소스 그룹을 만들 수 있는 기존 Azure 구독이 있습니다.

- Azure PowerShell의 최신 빌드를 다운로드하고 설치했습니다. 지침은 [여기][azure-powershell-download]를 참조하세요.

솔루션을 배포하는 스크립트를 실행하려면

1. `Scripts` 및 `Templates`라는 하위 폴더를 포함하는 폴더를 만듭니다.

2. Templates 폴더에 Windows라는 다른 하위 폴더를 만듭니다.

3. [Deploy-ReferenceArchitecture.ps1][solution-script] 파일을 Scripts 폴더에 다운로드합니다.

4. 다음 파일을 Templates/Windows 폴더에 다운로드합니다.

	- [virtualNetwork.parameters.json][vnet-parameters]

	- [networkSecurityGroups.parameters.json][nsg-parameters]

	- [virtualMachineParameters.json][vm-parameters]

5. Scripts 폴더에서 Deploy-ReferenceArchitecture.ps1 파일을 편집하고 다음 줄을 변경하여 스크립트에서 만들어진 VM 및 리소스를 포함하기 위해 만들거나 사용해야 하는 리소스 그룹을 지정합니다.

	<!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1#L37 -->
	```powershell
	$resourceGroupName = "ra-single-vm-rg"
	```
6. 위의 솔루션 구성 요소 섹션에 설명된 대로 Templates/Windows 폴더의 각 JSON 파일을 편집하여 가상 네트워크, NSG 및 VM에 대한 매개 변수를 설정합니다.

	>[AZURE.NOTE] virtualMachineParameters.json 파일의 `virtualNetworkSettings` 섹션에서 `resourceGroup` 매개 변수를 Deploy-ReferenceArchitecture.ps1 스크립트 파일에 지정한 값과 동일하게 설정해야 합니다.

7. PowerShell 창을 열고, Scripts 폴더로 이동한 후 다음 명령을 실행합니다.

	```powershell
	.\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Windows
	```

	`<subscription id>`를 Azure 구독 ID로 바꿉니다.

	`<location>`에 대해 `eastus` 또는 `westus`와 같은 Azure 지역을 지정합니다.

8. 스크립트가 완료되면 Azure 포털을 사용하여 네트워크, NSG 및 VM이 제대로 만들어졌는지 확인합니다.

## 다음 단계

[가상 컴퓨터에 대한 SLA][vm-sla]를 적용하려면 가용성 집합에서 두 개 이상의 인스턴스를 배포해야 합니다. 자세한 내용은 [Azure에서 여러 VM 실행][multi-vm]을 참조하세요.

<!-- links -->

[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/ko-KR/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/ko-KR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[보안 센터 사용]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualNetwork.parameters.json
[nsg-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/networkSecurityGroups.parameters.json
[vm-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/windows/virtualMachine.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure의 단일 Windows VM 아키텍처"

<!---HONumber=AcomDC_0831_2016-->