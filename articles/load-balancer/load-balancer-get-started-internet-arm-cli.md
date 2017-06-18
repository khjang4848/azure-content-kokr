<properties 
   pageTitle="Azure CLI를 사용하여 Resource Manager에서 인터넷 연결 부하 분산 장치 만들기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Resource Manager에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Azure CLI를 사용하여 내부 부하 분산 장치 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 이 문서에서는 Resource Manager 배포 모델에 대해 설명합니다. 또한 [클래식 배포를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Azure CLI를 사용하여 솔루션 배포

다음 단계에서는 CLI와 함께 Azure Resource Manager를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 보여 줍니다. Azure Resource Manager를 사용하면 각 리소스가 개별적으로 생성되고 구성된 다음, 함께 사용되어 리소스를 만듭니다.

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성해야 합니다.

- 프런트 엔드 IP 구성 - 들어오는 네트워크 트래픽에 대한 공용 IP 주소를 포함합니다.
- 백 엔드 주소 풀 - 부하 분산 장치의 네트워크 트래픽을 받는 가상 컴퓨터에 대한 NIC(네트워크 인터페이스)를 포함합니다.
- 부하 분산 규칙 - 백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
- 인바운드 NAT 규칙 - 백 엔드 주소 풀에 있는 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.
- 프로브 - 백 엔드 주소 풀의 가상 컴퓨터 인스턴스의 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다.

자세한 내용은 [부하 분산 장치에 대한 Azure Resource Manager 지원](load-balancer-arm.md)을 참조하세요.

## Resource Manager를 사용하도록 CLI 설치

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../../articles/xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.

2. 아래와 같이 **azure config mode** 명령을 실행하여 Resource Manager 모드로 전환합니다.

        azure config mode arm

    예상 출력:

        info:    New mode is arm

## 프런트 엔드 IP 풀에 대한 공용 IP 주소 및 가상 네트워크 만들기

1. *NRPRG* 라는 리소스 그룹을 사용하여 미국 동부 위치에 *NRPVnet*이라는 가상 네트워크(VNet)를 만듭니다.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    *NRPVnet* 에서 10.0.0.0/24의 CIDR 블록으로 *NRPVnetSubnet*이라는 서브넷을 만듭니다.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. DNS 이름이 *loadbalancernrp.eastus.cloudapp.azure.com* 인 프런트 엔드 IP 풀에서 사용할 *NRPPublicIP*라는 공용 IP 주소를 만듭니다. 아래 명령은 정적 할당 형식 및 4분의 유휴 상태 시간 제한을 사용합니다.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT] 부하 분산 장치는 FQDN으로 공용 IP의 도메인 레이블을 사용합니다. 이는 부하 분산 장치 FQDN으로 클라우드 서비스를 사용하는 클래식 배포의 변경입니다. 이 예제에서는 FQDN이 *loadbalancernrp.eastus.cloudapp.azure.com*입니다.

## 부하 분산 장치 만들기

다음 명령은 *미국 동부* Azure 위치의 *NRPRG* 리소스 그룹에 *NRPlb* 라는 부하 분산 장치를 만듭니다.

    azure network lb create NRPRG NRPlb eastus

## 프런트 엔드 IP 풀 및 백 엔드 주소 풀 만들기

이 예제에서는 부하 분산 장치에 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀 및 프런트 엔드 풀이 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀을 만듭니다.

1. 이전 단계에서 만든 공용 IP를 연결하는 프런트 엔드 IP 풀 및 부하 분산 장치를 만듭니다.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. 프런트 엔드 IP 풀에서 들어오는 트래픽을 받는 데 사용되는 백 엔드 주소 풀을 설정합니다.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## LB 규칙, NAT 규칙 및 프로브 만들기

이 예제에서는 다음 항목을 만듭니다.

- 포트 21~포트 22에서 들어오는 모든 트래픽을 변환하는 NAT 규칙<sup>1</sup>
- 포트 23~포트 22에서 들어오는 모든 트래픽을 변환하는 NAT 규칙
- 포트 80~포트 80에서 들어오는 모든 트래픽을 백 엔드 풀에 있는 주소로 분산하는 부하 분산 장치 규칙입니다.
- *HealthProbe.aspx*라는 페이지에 대한 상태를 확인할 프로브 규칙입니다.

<sup>1</sup> NAT 규칙은 부하 분산 장치 뒤에 특정 가상 컴퓨터 인스턴스와 관련이 있습니다. 포트 21에 도착하는 네트워크 트래픽은 이 NAT 규칙과 연결된 포트 22의 특정 가상 컴퓨터에 보내집니다. NAT 규칙에 대한 프로토콜(UDP 또는 TCP)을 지정해야 합니다. 두 프로토콜을 모두 동일한 포트에 할당할 수 없습니다.

1. NAT 규칙을 만듭니다.

        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

    매개 변수:
    * **-g** - 리소스 그룹 이름
    * **-l** - 부하 분산 장치 이름
    * **-n** - nat 규칙, 프로브 또는 LB 규칙 여부의 리소스 이름입니다.
    * **-p** -프로토콜(TCP 또는 UDP일 수 있음)
    * **-f** 사용될 프런트 엔드 포트(프로브 명령은 -f를 사용하여 프로브 경로를 정의합니다.)
    * **-b** - 사용될 백 엔드 포트

2. 부하 분산 장치를 만듭니다.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. 상태 프로브를 만듭니다.

        azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

    매개 변수:
    * **-g** - 리소스 그룹
    * **-n** - 부하 분산 장치 집합의 이름
    * **-n** - 상태 프로브의 이름
    * **-p** - 상태 프로브에 사용되는 프로토콜
    * **-i** - 프로브 간격(초)
    * **-c** - 검사 횟수

4. 설정을 확인합니다.

        azure network lb show nrprg nrplb

    예상 출력:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## NIC 만들기

NIC를 만들고(또는 기존 NIC 수정) NAT 규칙, 부하 분산 장치 규칙 및 프로브에 연결해야 합니다.

1. *lb-nic1-be* 라는 NIC를 만들고 *rdp1* NAT 규칙 및 *NRPbackendpool* 백 엔드 주소 풀과 연결합니다.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    매개 변수:

    * **-g** - 리소스 그룹 이름
    * **-n** - NIC 리소스 이름
    * **-subnet-name** - 서브넷 이름
    * **-서브넷-vnet-이름--subnet-vnet-name** -가상 네트워크 이름
    * **-d** - /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>로 시작하는 백 엔드 풀 리소스의 ID
    * **-e** - /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>으로 시작하는 NIC 리소스에 연결될 NAT 규칙의 ID

    예상 출력:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. *lb-nic2-be* 라는 NIC를 만들고 *rdp2* NAT 규칙 및 *NRPbackendpool* 백 엔드 주소 풀과 연결합니다.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. *web1* 이라는 VM(가상 컴퓨터)을 만들고 *lb-nic1-be* 라는 NIC에 연결합니다. *web1nrp*라는 저장소 계정은 아래 명령을 실행하기 전에 만들어졌습니다.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] 부하 분산 장치의 VM은 동일한 가용성 집합에 있어야 합니다. `azure availset create`을(를) 사용하여 가용성 집합을 만듭니다.

    다음과 유사하게 출력되어야 합니다.

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] 부하 분산 장치에 대해 만든 NIC가 부하 분산 장치 공용 IP 주소를 사용하여 인터넷에 연결되기 때문에 정보 메시지 **publicIP 구성 없는 NIC입니다** 가 나타납니다.

    *lb-nic1-be* NIC는 *rdp1* NAT 규칙에 연결되어 있으므로 부하 분산 장치의 포트 3441을 통해 RDP를 사용하여 *web1*에 연결할 수 있습니다.

4. *web2* 라는 VM(가상 컴퓨터)을 만들고 *lb-nic2-be* 라는 NIC에 연결합니다. *web1nrp*라는 저장소 계정은 아래 명령을 실행하기 전에 만들어졌습니다.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## 기존 부하 분산 장치 업데이트

기존 부하 분산 장치를 참조하는 규칙을 추가할 수 있습니다. 다음 예제에서는 새 부하 분산 장치 규칙은 기존 부하 분산 장치 **NRPlb** 에 추가됩니다.

    azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

매개 변수:

* **-g** - 리소스 그룹 이름
* **-l** - 부하 분산 장치 이름
* **-n** - 부하 분산 장치 규칙 이름
* **-p** - 프로토콜
* **-f** - 프런트 엔드 포트
* **-b** - 백 엔드 포트
* **-t** - 프런트 엔드 풀 이름
* **-b** - 백 엔드 풀 이름

## 부하 분산 장치 삭제

다음 명령을 사용하여 부하 분산 장치 제거:

    azure network lb delete -g nrprg -n nrplb

여기서 **nrprg**는 리소스 그룹이고 **nrplb**는 부하 분산 장치 이름입니다.

## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-cli.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!----HONumber=AcomDC_0914_2016-->
