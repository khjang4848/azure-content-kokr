<properties 
   pageTitle="Azure의 Linux VM에 대한 DNS 이름 확인 옵션"
   description="Azure IaaS의 Linux VM에 대한 이름 확인 시나리오(제공된 DNS 서비스, 하이브리드 외부 DNS 및 사용자 고유의 DNS 서버 포함)입니다."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="rclaus" />

# Azure의 Linux VM에 대한 DNS 이름 확인 옵션

Azure는 단일 가상 네트워크 내에 포함된 모든 VM에 대해 기본적으로 DNS 이름 확인을 제공합니다. Azure 호스트된 VM에서 자체 DNS 서비스를 구성하여 사용자 고유의 DNS 이름 확인 솔루션을 구현할 수 있습니다. 다음 시나리오는 특정 상황에 보다 적합한 이름 확인을 선택하는 데 도움이 됩니다.

- [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)

- [자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server)

어떤 방법으로 이름을 확인할지는 사용하는 VM 및 역할 인스턴스가 서로 어떻게 통신해야 하는지에 따라 다릅니다.

**다음 표에 각 시나리오 별로 해당하는 이름 확인 방법이 나와 있습니다.**

| **시나리오** | **솔루션** | **접미사** |
|--------------|--------------|----------|
| 역할 인스턴스 또는 VM이 동일한 가상 네트워크에 있는 경우 | [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)| 호스트 이름 또는 FQDN |
| 서로 다른 네트워크에 위치한 역할 인스턴스 또는 VM 간 이름 확인 | 고객이 관리하는 DNS 서버가 Azure(DNS 프록시)에서 확인을 위해 vnet 간의 쿼리를 전달합니다. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server)을 참조하세요.| FQDN만 |
| Azure의 VM 또는 역할 인스턴스에서 온-프레미스 컴퓨터와 서비스 이름 확인 | 고객이 관리하는 DNS 서버(예: 온-프레미스 도메인 컨트롤러, 로컬 읽기 전용 도메인 컨트롤러 또는 영역 전송을 사용하여 동기화된 DNS 보조)입니다. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server)을 참조하세요.|FQDN만 |
| 온-프레미스 컴퓨터에서 Azure 호스트 이름 확인 | 해당 vnet에서 고객이 관리하는 DNS 프록시 서버에 쿼리를 전달하면 프록시 서버는 이름 확인을 위해 Azure에 쿼리를 전달합니다. [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server)을 참조하세요.| FQDN만 |
| 내부 IP에 대한 역방향 DNS | [자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server) | 해당 없음 |

## Azure에서 제공하는 이름 확인

Azure에서는 공용 DNS 이름 확인과 함께, 동일한 가상 네트워크 내에 있는 VM 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. ARM 기반 가상 네트워크에서는 DNS 접미사가 가상 네트워크에서 일관되며(따라서 FQDN이 필요하지 않음) NIC 또는 VM 모두에 DNS 이름을 할당할 수 있습니다. Azure에서 제공한 이름 확인은 별도로 구성할 필요가 없으나, 위의 표에 나온 바와 같이 모든 배포 서비스에서 선택할 수 있는 것은 아닙니다.

### 기능 및 고려 사항

**기능:**

- 사용 편의성: Azure에서 제공하는 이름 확인을 사용하기 위해 별도로 구성할 필요가 없습니다.

- Azure에서 제공하는 이름 확인 서비스는 항상 사용 가능하며 사용자 고유 DNS 서버의 클러스터를 만들고 관리해야 하는 필요성을 줄여줍니다.

- 온-프레미스와 Azure 호스트 이름을 모두 확인하기 위해 자체 DNS 서버와 함께 사용할 수 있습니다.

- 가상 네트워크의 VM 간에 이름 확인이 제공됩니다. FQDN은 필요 없습니다.

- 자동 생성되는 이름 대신에 배포를 가장 잘 설명해주는 호스트 이름을 사용할 수 있습니다.

**고려 사항:**

- Azure에서 만든 DNS 접미사는 수정할 수 없습니다.

- 사용자 고유의 레코드를 수동으로 등록할 수 없습니다.

- WINS 및 NetBIOS는 지원되지 않습니다.

- 호스트 이름이 DNS와 호환될 수 있어야 합니다. (0-9, a-z 및 '-'만 사용이 가능하며, '-'로 시작하거나 끝날 수 없습니다. RFC 3696 섹션을 2를 참조하세요.)

- DNS 쿼리 트래픽은 각 VM에 대해 제한됩니다. 이 대부분의 응용 프로그램에 영향을 주지 않아야 합니다. 요청 제한이 확인되는 경우 클라이언트쪽 캐싱이 사용하도록 설정되었는지 확인합니다. 자세한 내용은 참조 [Azure에서 제공하는 이름 확인 활용](#Getting-the-most-from-Azure-provided-name-resolution)을 참조하세요.


### Azure에서 제공하는 이름 확인 활용
**클라이언트쪽 캐싱:**

모든 DNS 쿼리를 네트워크를 통해 전송해야 하는 것은 아닙니다. 클라이언트쪽 캐싱을 사용하면 대기 시간을 줄이고 로컬 캐시에서 되풀이되는 DNS 쿼리를 확인하여 네트워크 블립에 대한 복원력을 개선하는 데 도움이 됩니다. DNS 레코드는 레코드 새로 고침에 영향을 주지 않으면서 캐시가 가능한 오랫동안 레코드를 저장할 수 있도록 하는 TTL(Time-To-Live)을 포함하므로 클라이언트쪽 캐싱은 대부분의 상황에 적합합니다.

일부 Linux 배포판은 기본적으로 캐싱을 포함하지 않습니다. 각 Linux VM에 캐싱을 추가하는 것이 좋습니다(로컬 캐시가 아직 없다는 것을 확인한 후).

dnsmasq와 같은 다양한 여러 다양한 DNS 캐싱이 제공되며 여기서는 가장 일반적인 배포판에 dnsmasq를 설치하는 단계를 설명합니다.

- **Ubuntu(resolvconf 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo apt-get install dnsmasq").
- **SUSE(netconf 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo zypper install dnsmasq").
	- dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
	- dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
	- "/etc/sysconfig/network/config"를 편집하고 NETCONFIG\_DNS\_FORWARDER=""를 "dnsmasq"로 변경합니다.
	- 캐시를 로컬 DNS 확인자로 설정하기 위해 resolv.conf("netconfig update")를 업데이트합니다.
- **OpenLogic(NetworkManager 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo yum install dnsmasq").
	- dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
	- dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
	- "prepend domain-name-servers 127.0.0.1;"을 "/etc/dhclient-eth0.conf"에 추가합니다.
	- 캐시를 로컬 DNS 확인자로 설정하기 위해 네트워크 서비스("service network restart")를 다시 시작합니다.

> [AZURE.NOTE]\: 'dnsmasq' 패키지는 여러 DNS 캐시 중에 Linux에 사용할 수 있는 유일한 캐시입니다. 사용하기 전에 특정 요구 사항에 대한 적합성을 확인하고 다른 캐시가 설치되어 있지 않은지 확인합니다.

**클라이언트쪽 재시도:**

DNS는 주로 UDP 프로토콜입니다. UDP 프로토콜은 메시지 배달을 보장하지 않으므로 DNS 프로토콜 자체에서 재시도 논리가 처리됩니다. 각 DNS 클라이언트(운영 체제)는 작성자의 기본 설정에 따라 서로 다른 재시도 논리를 나타낼 수 있습니다.

 - Windows 운영 체제는 1초 후 재시도한 후 2초, 4초 후 다시 재시도하고 또 다시 4초 후 재시도합니다.
 - 기본 Linux 설정에서는 5초 후 재시도합니다. 1초 간격으로 5번 재시도하도록 설정을 변경하는 것이 좋습니다.

Linux VM에서 현재 설정을 확인하려면 'cat /etc/resolv.conf'에서 'options' 줄을 확인합니다. 예를 들면 다음과 같습니다.

	options timeout:1 attempts:5

resolv.conf 파일은 자동으로 생성되며 편집할 수 없습니다. 'options' 줄을 추가하는 구체적인 단계는 배포판마다 다릅니다.

- **Ubuntu**(resolvconf 사용):
	- options 줄을 '/etc/resolveconf/resolv.conf.d/head'에 추가합니다.
	- 'resolvconf -u'를 실행하여 업데이트합니다.
- **SUSE**(netconf 사용):
	- 'timeout:1 attempts:5'를 '/etc/sysconfig/network/config'의 NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" 매개 변수에 추가합니다.
	- 'netconfig update'를 실행하여 업데이트합니다.
- **OpenLogic**(NetworkManager 사용):
	- 'echo "options timeout:1 attempts:5"'를 '/etc/NetworkManager/dispatcher.d/11-dhclient'에 추가합니다.
	- 'service network restart'를 실행하여 업데이트합니다.

## 자체 DNS 서버를 이용한 이름 확인
이름 확인 요구 사항이 Azure에서 제공하는 기능을 벗어날 수 있는 여러 상황이 있습니다. 예를 들어 vnet(가상 네트워크) 간의 DNS 확인이 요구될 때와 같은 상황입니다. 이 시나리오를 해결하기 위해 Azure는 자체 DNS 서버를 사용할 수 있는 기능을 제공합니다.

가상 네트워크 내에서 DNS 서버는 해당 가상 네트워크 내에서 호스트 이름을 확인하기 위해 Azure의 재귀 확인자에게 DNS 쿼리를 전달할 수 있습니다. 예를 들어 Azure에서 실행 중인 DNS 서버는 자체 DNS 영역 파일에 대한 DNS 쿼리에 응답하고 Azure에 다른 모든 쿼리를 전달할 수 있습니다. 이렇게 하면 VM은 영역 파일의 항목 및 Azure에서 제공하는 호스트 이름(전달자를 통해)을 확인할 수 있습니다. Azure의 재귀 확인자에 대한 액세스는 가상 IP 168.63.129.16을 통해 제공됩니다.

또한 DNS 전달로 vnet 간 DNS 확인이 가능하며 온-프레미스 컴퓨터는 Azure에서 제공하는 호스트 이름을 확인할 수 있습니다. VM의 호스트 이름을 확인하려면 DNS 서버 VM이 동일한 가상 네트워크에 있어야 하며 Azure에 호스트 이름 쿼리를 전달하도록 구성되어야 합니다. DNS 접미사는 각 vnet마다 다르기 때문에 확인을 위해 올바른 vnet에 DNS 쿼리를 보내도록 조건부 전달 규칙을 사용할 수 있습니다. 다음 이미지에서는 두 vnet과 온-프레미스 네트워크가 이 메서드를 사용하여 vnet 간 DNS 확인을 수행하는 것을 보여 줍니다.

![Vnet 간 DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Azure에서 제공하는 이름 확인을 사용하는 경우 DHCP를 사용하여 각 VM에 내부 DNS 접미사를 제공합니다. 자체 이름 확인 솔루션을 사용하는 경우 이 접미사는 다른 DNS 아키텍처에 방해가 되기 때문에 VM에 제공되지 않습니다. FQDN으로 컴퓨터를 참조하거나 VM에 접미사를 구성하려면 PowerShell 또는 API를 사용하여 접미사를 확인할 수 있습니다.

-  Azure 리소스 관리에서 관리되는 vnet의 경우 [네트워크 인터페이스 카드](https://msdn.microsoft.com/library/azure/mt163668.aspx) 리소스를 통해 접미사를 사용하거나 `azure network public-ip show <resource group> <pip name>` 명령을 실행하여 공용 IP 세부 정보(NIC의 FQDN 포함)를 표시할 수 있습니다.


Azure에 전달하는 쿼리가 사용자 요구에 적합하지 않은 경우 자체 DNS 솔루션을 제공해야 합니다. DNS 솔루션은 다음을 수행해야 합니다:

-  예를 들어 [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md)를 통해 적절한 호스트 이름 확인을 제공해야 합니다. DDNS를 사용하는 경우 Azure의 DHCP 임대는 매우 길고 청소는 DNS 레코드를 중간에 제거할 수 있기 때문에 DNS 레코드 청소를 사용하지 않도록 설정해야 합니다.
-  외부 도메인 이름을 확인할 수 있도록 적절한 재귀 확인을 제공해야 합니다.
-  제공하는 클라이언트에서 액세스 가능해야 하고(포트 53에서 TCP 및 UDP) 인터넷에 액세스할 수 있어야 합니다.
-  외부 에이전트로 인해 나타나는 위험을 완화하기 위해 인터넷의 액세스로부터 보호되어야 합니다.

> [AZURE.NOTE] 최상의 성능을 위해 DNS 서버로 Azure VM을 사용할 때는 IPv6을 사용하지 않도록 설정하고 [인스턴스 수준 공용 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)를 각 DNS 서버 VM에 할당해야 합니다.

<!---HONumber=AcomDC_0914_2016-->