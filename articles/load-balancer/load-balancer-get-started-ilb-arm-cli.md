<properties
   pageTitle="Resource Manager에서 Azure CLI를 사용하여 내부 부하 분산 장치 만들기 | Microsoft Azure"
   description="Resource Manager에서 Azure CLI를 사용하여 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
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

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [클래식 배포 모델](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

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

## 내부 부하 분산 장치 만들기 단계별 지침

1. Azure에 로그인

        azure login

    메시지가 표시되면 Azure 자격 증명을 입력합니다.

2. 명령 도구를 Azure Resource Manager 모드로 변경합니다.

    azure config mode arm

## 리소스 그룹 만들기

Azure Resource Manager의 모든 리소스는 리소스 그룹에 연결됩니다. 리소스 그룹을 만들지 않았으면 리소스 그룹을 만듭니다.

    azure group create <resource group name> <location>

## 내부 부하 분산 장치 집합을 만듭니다.

1. 내부 부하 분산 장치 만들기

    다음 시나리오에서 nrprg라는 이름의 리소스 그룹이 미국 동부 지역에 만들어집니다.

        azure network lb create -n nrprg -l eastus

    >[AZURE.NOTE] 가상 네트워크 및 가상 네트워크 서브넷 같은 내부 부하 분산 장치의 모든 리소스는 동일한 리소스 그룹 및 동일한 지역에 속해야 합니다.

2. 내부 부하 분산 장치의 프런트 엔드 IP 주소를 만듭니다.

    사용되는 IP 주소는 가상 네트워크의 서브넷 범위 안에 있어야 합니다.

        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

    사용된 매개 변수:

    * **-g** - 리소스 그룹
    * **-l** - 내부 부하 분산 장치 집합의 이름
    * **-n** - 프런트 엔드 IP의 이름
    * **-a** - 서브넷 범위의 개인 IP 주소입니다.
    * **-e** - 서브넷 이름
    * **-m** - 가상 네트워크 이름

3. 백 엔드 주소 풀을 만듭니다.

        azure network lb address-pool create -g nrprg -l ilbset -n beilb

    사용된 매개 변수:

    * **-g** - 리소스 그룹
    * **-l** - 내부 부하 분산 장치 집합의 이름
    * **-n** - 백 엔드 주소 풀의 이름

    프런트 엔드 IP 주소 및 백 엔드 주소 풀을 정의한 후에 부하 분산 장치 규칙, 인바운드 NAT 규칙을 만들고 상태 프로브를 사용자 지정할 수 있습니다.

4. 내부 부하 분산 장치에 대한 부하 분산 장치 규칙을 만듭니다.

    위의 시나리오에 따라, 이 명령은 프런트 엔드 풀의 1433 포트를 수신 대기하고, 역시 1433 포트를 사용하여 백 엔드 주소 풀에 부하 분산 네트워크 트래픽을 보내는 부하 분산 장치 규칙을 만듭니다.

        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

    사용된 매개 변수:

    * **-g** - 리소스 그룹
    * **-l** - 내부 부하 분산 장치 집합의 이름
    * **-n** - 부하 분산 장치 규칙의 이름
    * **-p** - 규칙에 사용된 프로토콜
    * **-f** - 부하 분산 장치 프런트 엔드에서 수신 네트워크 트래픽을 수신 대기하는 포트
    * **-b** - 백 엔드 주소 풀에서 네트워크 트래픽을 수신하는 포트

5. 인바운드 NAT 규칙을 만듭니다.

    인바운드 NAT 규칙은 특정 가상 컴퓨터 인스턴스로 이동할 부하 분산 장치에 끝점을 만드는 데 사용됩니다. 위의 예제에 따라, 원격 데스크톱 액세스를 위해 2개의 NAT 규칙을 만듭니다.

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

    사용된 매개 변수:

    * **-g** - 리소스 그룹
    * **-l** - 내부 부하 분산 장치 집합의 이름
    * **-n** - 인바운드 NAT 규칙의 이름
    * **-p** - 규칙에 사용된 프로토콜
    * **-f** - 부하 분산 장치 프런트 엔드에서 수신 네트워크 트래픽을 수신 대기하는 포트
    * **-b** - 백 엔드 주소 풀에서 네트워크 트래픽을 수신하는 포트

5. 부하 분산 장치에 대한 상태 프로브를 만듭니다.

    상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 컴퓨터 인스턴스를 검사합니다. 프로브 검사에 실패한 가상 컴퓨터 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다.

        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

    사용된 매개 변수:

    * **-g** - 리소스 그룹
    * **-l** - 내부 부하 분산 장치 집합의 이름
    * **-n** - 상태 프로브의 이름
    * **-p** - 상태 프로브에 사용되는 프로토콜
    * **-i** - 프로브 간격(초)
    * **-c** - 검사 횟수

    >[AZURE.NOTE] Microsoft Azure 플랫폼에서는 다양한 관리 시나리오에 공개적으로 라우팅할 수 있는 고정 IPv4 주소를 사용합니다. IP 주소는 168.63.129.16입니다. 이 IP 주소를 방화벽으로 차단하면 안 됩니다. 예기치 않은 동작이 발생할 수 있습니다. Azure 내부 부하 분산과 관련하여 이 IP 주소는 부하 분산된 집합에서 가상 컴퓨터의 상태를 확인하기 위해 부하 분산 장치에서 프로브를 모니터링하는 데 사용됩니다. 내부적으로 부하 분산된 집합의 Azure 가상 컴퓨터로 트래픽을 제한하는 네트워크 보안 그룹이 사용된 경우 168.63.129.16의 트래픽을 허용하도록 네트워크 보안 규칙을 추가해야 합니다.

## NIC 만들기

NIC를 만들고(또는 기존 NIC 수정) NAT 규칙, 부하 분산 장치 규칙 및 프로브에 연결해야 합니다.

1. *lb-nic1-be*라는 NIC를 만들고 *rdp1* NAT 규칙 및 *beilb* 백 엔드 주소 풀과 연결합니다.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

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

2. *lb-nic2-be*라는 NIC를 만들고 *rdp2* NAT 규칙 및 *beilb* 백 엔드 주소 풀과 연결합니다.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. *DB1*이라는 VM(가상 컴퓨터)을 만들고 *lb-nic1-be*라는 NIC에 연결합니다. *web1nrp*라는 저장소 계정은 아래 명령을 실행하기 전에 만들어졌습니다.

        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] 부하 분산 장치의 VM은 동일한 가용성 집합에 있어야 합니다. `azure availset create`을(를) 사용하여 가용성 집합을 만듭니다.

4. *DB2*이라는 VM(가상 컴퓨터)을 만들고 *lb-nic2-be*라는 NIC에 연결합니다. *web1nrp*라는 저장소 계정은 아래 명령을 실행하기 전에 만들어졌습니다.

        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## 부하 분산 장치 삭제

부하 분산 장치를 제거하려면 다음 명령을 사용합니다.

    azure network lb delete -g nrprg -n ilbset

여기서 **nrprg**는 리소스 그룹이고 **ilbset**은 내부 부하 분산 장치 이름입니다


## 다음 단계

[원본 IP 선호도를 사용하여 부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->