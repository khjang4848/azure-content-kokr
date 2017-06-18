<properties
	pageTitle="Azure AD 페더레이션 호환성 목록"
	description="이 페이지에 Single Sign-On을 구현하는 데 사용할 수 있는 타사 ID 공급자가 있습니다."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="billmath"/>

# Azure AD 페더레이션 호환성 목록
Azure Active Directory에서는 임의 타사 솔루션을 요구하지 않고 Office 365용 Single Sign-On과 강화된 응용 프로그램 액세스 보안 및 하이브리드와 클라우드 전용 구현에 대한 기타 Microsoft Online Services를 제공합니다. 대부분의 Microsoft Online Services와 마찬가지로 Office 365는 디렉터리 서비스, 인증 및 권한 부여에 대해 Azure Active Directory와 통합되어 있습니다. 또한 Azure Active Directory는 수천 개의 SaaS 응용 프로그램 및 온-프레미스 웹 응용 프로그램에도 Single Sign-On을 제공합니다. 지원되는 SaaS 응용 프로그램에 대한 Azure Active Directory 응용 프로그램 갤러리를 참조하세요.

타사 페더레이션 솔루션에 투자한 조직의 경우 이 항목에는 해당 조직의 Windows Server Active Directory 사용자를 위해 아래 "Azure Active Directory 페더레이션 호환성 목록"의 타사 ID 공급자를 사용하여 Microsoft Online Services로 Single Sign-On을 구성하는 지침이 포함되어 있습니다.


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/)은 Microsoft를 대신하여 이러한 Single Sign-On 환경을 Azure Active Directory와 공통된 사용 사례 집합에 대해 타사 ID 공급자를 사용하여 테스트했습니다.

여기에 나열된 타사 ID 공급자를 가져오는 방법에 대한 내용은 Oxford Computer Group([idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com))에 문의하세요.

>[AZURE.IMPORTANT] Oxford Computer Group은 이러한 Single Sign-On 시나리오의 페더레이션 기능만을 테스트했습니다. Oxford Computer Group은 이러한 Single Sign-On 시나리오의 동기화, 2단계 인증 등 구성 요소에 대한 테스트는 수행하지 않았습니다.

>UPN에 대한 대체 ID 로그인의 사용도 이 프로그램에서 테스트되지 않았습니다.



- [Azure Active Directory](#azure-active-directory)
- [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services)
- [PingFederate 6.11](#pingfederate-611)
- [PingFederate 7.2](#pingfederate-72)
- [PingFederate 8.x](#pingfederate-8x)
- [Centrify](#centrify)
- [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622)
- [SecureAuth IdP 7.2.0](#secureauth-idp-720)
- [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4)
- [RadiantOne CFS 3.0](#radiantone-cfs-30)
- [Okta](#okta)
- [OneLogin](#onelogin)
- [NetIQ Access Manager 4.0.1](#netiq-access-manager-401)
- [BIG-IP with Access Policy Manager BIG-IP 버전 11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x)
- [VMware Workspace Portal 버전 2.1](#vmware-workspace-portal-version-21)
- [Sign&go 5.3](#signampgo-53)
- [IceWall Federation 버전 3.0](#icewall-federation-version-30)
- [CA Secure Cloud](#ca-secure-cloud)
- [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71)
- [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)

>[AZURE.IMPORTANT] 이러한 제품은 타사 제품이므로 Microsoft는 배포, 구성, 문제 해결, 모범 사례 등 이러한 ID 공급자에 관한 문제 및 질문에 대한 지원을 제공하지 않습니다. 이러한 ID 공급자에 관한 지원 및 질문은 타사 담당자에게 직접 문의하세요.

>이러한 타사 ID 공급자는 Microsoft 클라우드 서비스와의 상호 운용성에 대해 WS-Federation 및 WS-Trust 프로토콜만 사용하여 테스트되었습니다. SAML 프로토콜을 사용한 테스트는 포함되지 않았습니다.

## Azure Active Directory 
Azure Active Directory는 온-프레미스 Active Directory와 페더레이션하거나 암호 동기화 사용을 통해 온-프레미스 페더레이션 서버 없이 사용자를 인증할 수 있습니다.

다음은 이 로그온 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|
|Office 2016과 같은 ADAL을 사용하는 최신 응용 프로그램| 지원됨|없음|

AD FS를 통해 Azure Active Directory를 사용하는 방법에 대한 자세한 내용은 [ADFS(Active Directory Federation Services)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)를 참조하세요.

암호 동기화를 통해 Azure Active Directory를 사용하는 방법에 대한 자세한 내용은 [Azure AD Connect](active-directory-aadconnect.md)를 참조하세요.


## Optimal IDM Virtual Identity Server Federation Services 
Optimal IDM Virtual Identity Server Federation Services는 고객의 온-프레미스 Active Directory에 있는 사용자를 인증할 수 있습니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |클라이언트 액세스 정책에 대한 자세한 내용은 [클라이언트 위치 기반 Office 365 서비스에 대한 액세스 제한](https://technet.microsoft.com/library/hh526961.aspx)을 참조하세요.|



## PingFederate 6.11 

PingFederate 6.11은 널리 사용되는 WS Federation ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음(이전 버전은 6.11로 업그레이드해야 함)|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

Active Directory 사용자에게 Single Sign-On 환경을 제공하도록 이 STS를 구성하는 방법에 대한 PingFederate 지침은 [여기](http://go.microsoft.com/fwlink/?LinkID=266321)서 pdf 파일을 다운로드하세요.

## PingFederate 7.2 
PingFederate 7.2는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

Active Directory 사용자에게 Single Sign-On 환경을 제공하도록 이 STS를 구성하는 방법에 대한 PingFederate 지침은 [여기](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)를 참조하세요.

## PingFederate 8.x 
PingFederate 8.x는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

Active Directory 사용자에게 Single Sign-On 환경을 제공하도록 이 STS를 구성하는 방법에 대한 PingFederate 지침은 [여기](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)를 참조하세요.

## Centrify 
Centrify는 온-프레미스 페더레이션 서버를 호스트할 필요 없이 Office 365에 대한 페더레이션된 Single Sign-On 환경을 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |클라이언트 액세스 제어는 지원되지 않습니다. 

Centrify에 대한 자세한 내용은 [여기](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)를 참조하세요.

## IBM Tivoli Federated Identity Manager 6.2.2 
Microsoft Applications 1.4용 IBM Security Access Manager가 포함된 IBM Tivoli Federated Identity Manager 6.2.2는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

IBM Tivoli Federated Identity Manager에 대한 자세한 내용은 [Microsoft 응용 프로그램용 IBM Security Access Manager](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)를 참조하세요.

## SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 환경 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

SecureAuth에 대한 자세한 내용은 [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)를 참조하세요.

## CA SiteMinder 12.52 SP1 누적 릴리스 4
CA SiteMinder Federation 12.52는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

CA SiteMinder에 대한 자세한 내용은 [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html)을 참조하세요.

## RadiantOne CFS 3.0 
RadiantOne CFS(Cloud Federation Service) 3.0은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

RadiantOne CFS에 대한 자세한 내용은 [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/)를 참조하세요.


## Okta 
Okta는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.


| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증에는 추가 웹 서버 및 Okta 응용 프로그램 설치가 필요합니다.|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

Okta에 대한 자세한 내용은 [Okta](https://www.okta.com/)를 참조하세요.
 
## OneLogin 
2014년 5월에 테스트된 OneLogin은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

OneLogin에 대한 자세한 내용은 [OneLogin](https://www.onelogin.com/)을 참조하세요.

## NetIQ Access Manager 4.0.1 
NetIQ Access Manager 4.0.1은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |*Kerberos 계약 지원됨|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

*NetIQ는 Kerberos 계약 구성을 통해 Kerberos 인증을 지원합니다. 이 구성에 지원이 필요한 경우 NetIQ에 문의하거나 설치 가이드를 확인하세요. NetIQ Access Manager에 대한 자세한 내용은 [NetIQ Access Manager](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)를 참조하세요.

## BIG-IP with Access Policy Manager BIG-IP 버전 11.3x – 11.6x 
BIG-IP with Access Policy Manager(APM) BIG-IP 버전 11.3x – 11.6x는 널리 사용되는 SAML ID 표준을 구현하여 Single Sign-On 환경 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원되지 않음 |지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

BIG-IP Access Policy Manager에 대한 자세한 내용은 [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager)를 참조하세요.

Active Directory 사용자에게 Single Sign-On 환경을 제공하도록 이 STS를 구성하는 방법에 대한 BIG-IP Access Policy Manager 지침은 [여기](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)서 pdf 파일을 다운로드하세요.

## VMware Workspace Portal 버전 2.1 
VMware Workspace Portal 버전 2.1은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

VMware Workspace Portal 버전 2.1에 대한 자세한 내용은 [여기](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)서 pdf 파일을 다운로드하세요.

## Sign&go 5.3 
Sign&go 5.3은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Kerberos 계약 지원됨 |
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|


Sign&go 5.3은 Kerberos 계약 구성을 통해 Kerberos 인증을 지원합니다. 이 구성에 지원이 필요한 경우 Ilex에 문의하거나 [여기](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)서 설치 가이드를 확인하세요.


## IceWall Federation 버전 3.0 
IceWall Federation 버전 3.0은 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

IceWall Federation에 대한 자세한 내용은 [여기](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) 및 [여기](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)를 참조하세요.

## CA Secure Cloud 

CA Secure Cloud는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

CA Secure Cloud에 대한 자세한 내용은 [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx)를 참조하세요.

## Dell One Identity Cloud Access Manager v7.1 
Dell One Identity Cloud Access Manager는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |없음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |없음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|

Dell One Identity Cloud Access Manager에 대한 자세한 내용은 [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager)를 참조하세요.

 Office 365 사용자에게 Single Sign-On 환경을 제공하도록 이 STS를 구성하는 방법에 대한 지침은 [Office 365 사용자 구성](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365)을 참조하세요.

## AuthAnvil Single Sign On 4.5 
AuthAnvil Single Sign On 4.5는 널리 사용되는 WS Federation/WS-Trust ID 표준을 구현하여 Single Sign-On 및 특성 교환 프레임워크를 제공합니다.

다음은 이 Single Sign-On 환경에 대한 시나리오 지원 매트릭스입니다.

| 클라이언트 |지원 |예외|
| --------- | --------- |--------- |
| Exchange Web Access 및 SharePoint Online과 같은 웹 기반 클라이언트 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Lync, Office Subscription, CRM과 같은 리치 클라이언트 응용 프로그램 | 지원됨 |Windows 통합 인증은 지원되지 않음|
| Outlook 및 ActiveSync와 같은 메일 리치 클라이언트 | 지원됨 |없음|


자세한 내용은 [AuthAnvil Single Sign-On](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->