<properties
	pageTitle="Azure API 관리에서 VPN 연결을 설정하는 방법"
	description="Azure API 관리에서 VPN 연결을 설정하고 웹 서비스에 액세스하는 방법에 대해 알아봅니다."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2016"
	ms.author="antonba"/>

# Azure API 관리에서 VPN 연결을 설정하는 방법

API 관리의 VPN 지원을 사용하여 API 관리 게이트웨이를 Azure 가상 네트워크(클래식)에 연결할 수 있습니다. 이렇게 하면 API 관리 고객을 온-프레미스이거나 공용 인터넷에 액세스할 수 없는 해당 백 엔드 웹 서비스에 안전하게 연결할 수 있습니다.

>[AZURE.NOTE] Azure API 관리는 클래식 VNET과 함께 작동합니다. 클래식 VNET을 만드는 방법에 대한 정보는 [Azure 포털을 사용하여 가상 네트워크(클래식) 만들기](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)를 참조하세요. ARM VNET에 클래식 VNET을 연결하는 내용은 [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

## <a name="enable-vpn"> </a>VPN 연결 사용

>VPN 연결은 **프리미엄** 및 **개발자** 계층에서만 사용할 수 있습니다. 전환하려면 [Azure 클래식 포털][]에서 API 관리 서비스를 열고 **크기 조정** 탭을 엽니다. **일반** 섹션에서 프리미엄 계층을 선택하고 저장을 클릭합니다.

VPN 연결을 사용하려면 [Azure 클래식 포털][]에서 API 관리 서비스를 열고 **구성** 탭으로 전환합니다.

VPN 섹션에서 **VPN 연결**을 **켜기**로 전환합니다.

![API 관리 인스턴스의 구성 탭][api-management-setup-vpn-configure]

이제 API 관리 서비스가 프로비전되는 모든 지역 목록이 보입니다.

VPN 및 모든 지역에 대한 서브넷을 선택합니다. VPN 목록은 사용자가 구성하고 있는 지역에서 설정되는 Azure 구독에서 사용할 수 있는 가상 네트워크에 기반하여 채워집니다.

![VPN 선택][api-management-setup-vpn-select]

화면 아래쪽에서 **저장**을 클릭합니다. 업데이트하는 동안 Azure 클래식 포털에서 API 관리 서비스의 다른 작업을 수행할 수 없습니다. 서비스 게이트웨이는 여전히 사용할 수 있으며 런타임 호출은 영향을 받지 않습니다.

게이트웨이의 VIP 주소는 VPN이 사용되거나 사용 해제될 때마다 변경됩니다.

## <a name="connect-vpn"> </a>VPN 뒤에서 웹 서비스에 연결

API 관리 서비스가 VPN에 연결되면 가상 네트워크 내에서 웹 서비스 액세스는 공용 서비스 액세스와 다르지 않습니다. 새 API를 만들거나 기존 API를 편집할 때 **웹 서비스 URL** 필드에 웹 서비스의 로컬 주소 또는 호스트 이름(DNS 서버가 Azure 가상 네트워크에 대해 구성된 경우)을 입력하면 됩니다.

![VPN에서 API 추가][api-management-setup-vpn-add-api]

## API 관리 VPN 지원에 필요한 포트

API 관리 서비스 인스턴스가 VNET에 호스트된 경우 다음 표의 포트가 사용됩니다. 이러한 포트가 차단되면 서비스가 제대로 작동하지 않을 수 있습니다. 이러한 포트가 하나 이상 차단되는 것은 VNET에서 API 관리를 사용하는 경우 가장 일반적인 잘못된 구성 문제입니다.

| 포트 | 방향 | 전송 프로토콜 | 목적 | 원본 / 대상 |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443 | 인바운드 | TCP | API 관리에 대한 클라이언트 통신 | 인터넷 / VIRTUAL\_NETWORK |
| 80,443 | 아웃바운드 | TCP | Azure 저장소 및 Azure 서비스 버스에 대한 API 관리 종속성 | VIRTUAL\_NETWORK / 인터넷 |
| 1433 | 아웃바운드 | TCP | SQL에 대한 API 관리 종속성 | VIRTUAL\_NETWORK / 인터넷 |
| 9350, 9351, 9352, 9353, 9354 | 아웃바운드 | TCP | 서비스 버스에 대한 API 관리 종속성 | VIRTUAL\_NETWORK / 인터넷 |
| 5671 | 아웃바운드 | AMQP | 이벤트 허브 정책에 대한 로그의 API 관리 종속성 | VIRTUAL\_NETWORK / 인터넷 |
| 6381, 6382, 6383 | 인바운드/아웃바운드 | UDP | Redis Cache에 대한 API 관리 종속성 | VIRTUAL\_NETWORK / VIRTUAL\_NETWORK |
| 445 | 아웃바운드 | TCP | GIT의 Azure 파일 공유에 대한 API 관리 종속성 | VIRTUAL\_NETWORK / 인터넷 |

## <a name="custom-dns"> </a>사용자 지정 DNS 서버 설정

API Management는 다양한 Azure 서비스에 따라 달라집니다. API Management 서비스 인스턴스가 사용자 지정 DNS 서버를 사용하는 VNET에서 호스팅되면 해당 Azure 서비스의 호스트 이름을 확인할 수 있어야 합니다. 사용자 지정 DNS 설정에 대한 [이](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 지침을 따르세요.

## <a name="related-content"> </a>관련 콘텐츠


* [Azure 클래식 포털을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기][]
* [API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure 클래식 포털]: https://manage.windowsazure.com/

[Azure 클래식 포털을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_0921_2016-->