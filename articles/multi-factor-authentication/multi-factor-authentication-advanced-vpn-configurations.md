<properties
	pageTitle="Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오"
	description="이 페이지는 Azure MFA와 타사 제품의 단계별 설정 구성에 대한 정보를 제공합니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban" 
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오
Azure Multi-Factor Authentication은 다양한 타사 VPN 솔루션과 원활하게 연결하는 데 사용할 수 있습니다. 여기에는 Cisco ® ASA VPN 어플라이언스, Citrix NetScaler SSL VPN 어플라이언스 및 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 어플라이언스가 포함되어 있습니다.

## Cisco ASA VPN 어플라이언스 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Cisco AnyConnect ® VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Cisco ® ASA VPN 어플라이언스와 원활하게 통합됩니다. 이는 LDAP 또는 RADIUS 프로토콜을 사용하여 수행할 수 있습니다. 자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

구성 가이드 | 설명
------------- | ------------- |
[Anyconnect VPN을 사용한 Cisco ASA 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Cisco ASA VPN 어플라이언스 및 LDAP를 사용하는 Azure MFA의 원활한 통합|
[Anyconnect VPN을 사용한 Cisco ASA 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Cisco ASA VPN 어플라이언스 및 RADIUS를 사용하는 Azure MFA의 원활한 통합

## Citrix NetScaler SSL VPN 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Citrix NetScaler SSL VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Citrix NetScaler SSL VPN 어플라이언스와 원활하게 통합됩니다. 이는 LDAP 또는 RADIUS 프로토콜을 사용하여 수행할 수 있습니다. 자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

구성 가이드 | 설명
------------- | ------------- |
[Citrix NetScaler SSL VPN 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Citrix NetScaler SSL VPN 어플라이언스 및 LDAP를 사용하는 Azure MFA의 원활한 통합|
[Citrix NetScaler SSL VPN 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Citrix NetScaler SSL VPN 어플라이언스 및 RADIUS를 사용하는 Azure MFA의 원활한 통합

##Juniper/Pulse Secure SSL VPN 어플라이언스 및 Azure Multi-Factor Authentication
Azure Multi-factor Authentication은 Juniper/Pulse Secure SSL VPN 로그인 및 포털 액세스에 대한 보안을 추가하기 위해 Juniper/Pulse Secure SSL VPN 어플라이언스와 원활하게 통합됩니다. 이는 LDAP 또는 RADIUS 프로토콜을 사용하여 수행할 수 있습니다. 자세한 단계별 구성 가이드를 다운로드하려면 다음 중 하나를 선택합니다.

구성 가이드 | 설명
------------- | ------------- |
[Juniper/Pulse Secure SSL VPN 및 LDAP를 위한 Azure MFA 구성](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Juniper/Pulse Secure SSL VPN 어플라이언스 및 LDAP를 사용하는 Azure MFA의 원활한 통합|
[Juniper/Pulse Secure SSL VPN 및 RADIUS를 위한 Azure MFA 구성](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Juniper/Pulse Secure SSL VPN 어플라이언스 및 RADIUS를 사용하는 Azure MFA의 원활한 통합

<!---HONumber=AcomDC_0921_2016-->