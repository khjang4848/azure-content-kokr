<properties
   pageTitle="Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열"
   description="Azure Active Directory 갤러리에서 Single Sign-On을 지원하는 응용 프로그램을 나열하는 방법 | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램 나열

[Azure AD 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)에서 Azure Active Directory를 사용하여 Single Sign-On을 지원하는 응용 프로그램을 나열하려면 먼저 응용 프로그램은 다음의 통합 모드 중 하나를 구현해야 합니다.

* **OpenID Connect** - 인증을 위한 OpenID Connect 및 구성을 위한 Azure AD 동의 API를 사용하는 Azure AD과 통합을 지원합니다. 통합을 시작하고 응용 프로그램이 SAML을 지원하지 않으면 이것이 권장 모드입니다.

* **SAML** – 응용 프로그램에는 이미 SAML 프로토콜을 사용하여 타사 ID 공급자를 구성할 기능이 있습니다.

각 모드에 대한 요구 사항을 나열하면 다음과 같습니다.

##OpenID Connect 통합

응용 프로그램과 Azure AD를 통합하려면 [개발자 지침](active-directory-authentication-scenarios.md)을 수행합니다. 그런 다음 아래 질문을 완료하여 waadpartners@microsoft.com으로 보냅니다.

* Azure AD 팀에서 사용할 수 있는 응용 프로그램을 사용하는 테스트 테넌트 또는 계정에 대한 자격 증명을 제공하여 통합을 테스트합니다.

* Azure AD 팀이 [Azure AD 동의 프레임워크](active-directory-integrating-applications.md#overview-of-the-consent-framework)를 사용하여 로그인하고 응용 프로그램에 Azure AD의 인스턴스를 연결하는 방법에 대한 지침을 제공합니다.

* 응용 프로그램을 사용하여 Single Sign-On을 테스트하기 위해 Azure AD 팀에 필요한 추가 지침을 제공합니다.

* 아래의 정보를 제공합니다.

> 회사 이름:
> 
> 회사 웹 사이트:
> 
> 응용 프로그램 이름:
> 
> 응용 프로그램 설명(256자 제한):
> 
> 응용 프로그램 웹 사이트 (정보):
> 
> 응용 프로그램 기술 지원 웹 사이트 또는 연락처 정보:
> 
> 응용 프로그램 정보(https://manage.windowsazure.com)에 표시된 응용 프로그램의 클라이언트 ID:
> 
> 고객이 응용 프로그램에 등록하거나 구입하기 위해 이동하는 응용 프로그램 등록 URL:
> 
> 응용 프로그램을 나열할 범주를 최대 세 가지 선택합니다.(사용 가능한 범주는 Azure Active Directory 마켓플레이스 참조)
> 
> 응용 프로그램 연결 작은 아이콘(PNG 파일, 45x45px, 단색 배경):
> 
> 응용 프로그램 연결 큰 아이콘(PNG 파일, 215x215px, 단색 배경):
> 
> 응용 프로그램 연결 로고(PNG 파일, 150x122px, 투명 배경색):

##SAML 통합

SAML 2.0을 지원하는 앱은 [사용자 지정 응용 프로그램을 추가하기 위해 다음 지침](active-directory-saas-custom-apps.md)을 사용하여 Azure AD 테넌트로 직접 통합될 수 있습니다. 응용 프로그램 통합이 Azure AD를 사용하여 작동하는지 테스트하면 다음 정보를 <waadpartners@microsoft.com>로 보냅니다.

* Azure AD 팀에서 사용할 수 있는 응용 프로그램을 사용하는 테스트 테넌트 또는 계정에 대한 자격 증명을 제공하여 통합을 테스트합니다.

* [여기](active-directory-saas-custom-apps.md)에 설명한 대로 응용 프로그램에 대한 SAML 로그온 URL, 발급자 URL(엔터티 ID) 및 회신 URL(어설션 소비자 서비스) 값을 제공합니다. 일반적으로 SAML 메타데이터 파일의 일부로 이러한 값을 제공하는 경우 또한 해당 파일을 보냅니다.

* SAML 2.0을 사용하여 응용 프로그램에서 Azure AD를 ID 공급자로 구성하는 방법에 대한 간략한 설명을 제공합니다. 응용 프로그램에서 셀프 서비스 관리 포털을 통해 Azure AD를 ID 공급자로 구성하도록 지원하는 경우 위에 제공된 자격 증명이 이를 설정할 기능을 포함하도록 합니다.

* 아래의 정보를 제공합니다.

> 회사 이름:
> 
> 회사 웹 사이트:
> 
> 응용 프로그램 이름:
> 
> 응용 프로그램 설명(256자 제한):
> 
> 응용 프로그램 웹 사이트 (정보):
> 
> 응용 프로그램 기술 지원 웹 사이트 또는 연락처 정보:
> 
> 고객이 응용 프로그램에 등록하거나 구입하기 위해 이동하는 응용 프로그램 등록 URL:
> 
> 응용 프로그램을 나열할 범주를 최대 세 가지 선택합니다(사용 가능한 범주는 [Azure Active Directory 마켓플레이스](https://azure.microsoft.com/marketplace/active-directory/) 참조).
> 
> 응용 프로그램 연결 작은 아이콘(PNG 파일, 45x45px, 단색 배경):
> 
> 응용 프로그램 연결 큰 아이콘(PNG 파일, 215x215px, 단색 배경):
> 
> 응용 프로그램 연결 로고(PNG 파일, 150x122px, 투명 배경색):

<!---HONumber=AcomDC_0921_2016-->