<properties
	pageTitle="앱 등록 포털 도움말 항목 | Microsoft Azure"
	description="Microsoft 앱 등록 포털의 다양한 기능을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# 앱 등록 참조
이 문서에서는 Microsoft 앱 등록 포털 [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)에 있는 다양한 기능에 대한 컨텍스트 및 설명을 제공합니다.

## 내 응용 프로그램
이 목록에는 Azure AD v2.0 끝점에서 사용하도록 등록된 모든 응용 프로그램이 포함되어 있습니다. 이러한 응용 프로그램에는 Microsoft 계정의 개인 계정 및 Azure Active Directory의 회사/학교 계정을 모두 사용하여 사용자로 로그인할 수 있습니다. Azure AD v2.0 끝점에 대한 자세한 내용은 [v2.0 개요](active-directory-appmodel-v2-overview.md)를 참조하세요. 이러한 응용 프로그램을 사용하여 Microsoft 계정 인증 끝점인 `https://login.live.com`과 통합할 수도 있습니다.

## Live SDK 응용 프로그램
이 목록에는 Microsoft 계정으로만 사용하도록 등록된 모든 응용 프로그램이 포함되어 있습니다. Azure Active Directory로 사용할 방법은 전혀 없습니다. 여기에서는 이전에 `https://account.live.com/developers/applications`에서 MSA 개발자 포털에 등록된 모든 응용 프로그램을 확인할 수 있습니다. 이전에 `https://account.live.com/developers/applications`에서 수행한 모든 함수를 이제 이 새 포털 `https://apps.dev.microsoft.com`에서 수행할 수 있습니다. Microsoft 계정 응용 프로그램에 대한 추가적인 질문이 있으면 문의하세요.

## 응용 프로그램 암호
응용 프로그램 암호는 Azure AD에서 응용 프로그램에서 신뢰할 수 있는 [클라이언트 인증](http://tools.ietf.org/html/rfc6749#section-2.3)이 되도록 하는 자격 증명입니다. OAuth 및 OpenID Connect에서 응용 프로그램 암호는 일반적으로 `client_secret`이라고 합니다. v2.0 프로토콜에서, 웹 주소 지정 가능한 위치에서 `https` 구성표를 사용하여 보안 토큰을 받는 모든 응용 프로그램에서는 해당 보안 토큰 상환 시 Azure AD에서 자신을 식별할 수 있도록 응용 프로그램 암호를 사용해야 합니다. 또한 장치에 대한 토큰을 받는 모든 기본 클라이언트에서는 응용 프로그램 암호를 사용하여 클라이언트 인증을 수행할 수 없도록 하여 안전하지 않은 환경에서 암호의 저장을 차단합니다.

각 앱에는 해당 시점에 유효한 두 개의 응용 프로그램 암호가 포함될 수 있습니다. 두 개의 암호를 유지하여 응용 프로그램의 전체 환경에서 정기적으로 키 롤오버를 수행할 수 있습니다. 응용 프로그램 전체를 새 암호로 마이그레이션하면 이전 암호를 삭제하고 새 암호를 프로비전할 수 있습니다.

이때 두 가지 유형의 응용 프로그램 암호만 앱 등록 포털에서 허용됩니다. **새 암호 생성**을 선택하면 응용 프로그램에 사용할 수 있는 해당 데이터 저장소에 공유 암호가 생성되어 저장됩니다. **새 키 쌍 생성**을 선택하면 Azure AD에 대한 클라이언트 인증을 위해 다운로드하여 사용할 수 있는 새 공개/개인 키를 만듭니다.

## 프로필
앱 등록 포털의 프로필 섹션에서는 응용 프로그램에 대한 로그인 페이지를 사용자 지정할 수 있습니다. 이때 로그인 페이지의 응용 프로그램 로고, 서비스 계약 URL 및 개인 정보 취급 방침을 변경할 수 있습니다. 로고는 15KB 이하인 GIP, PNG 또는 JPEG 파일로 된 투명한 48 x 48 또는 50 x 50 픽셀 이미지여야 합니다. 값을 변경하고 결과 로그인 페이지를 확인해 보세요!

## Live SDK 지원
"Live SDK 지원"을 사용하도록 설정하면 만든 응용 프로그램 암호 모두가 Azure AD 및 Microsoft 계정 데이터 저장소 모두에 프로비전됩니다. 그러면 응용 프로그램을 Microsoft 계정 서비스(login.live.com)와 직접 통합할 수 있습니다. Azure AD v2.0 끝점이 아니라 Microsoft 계정을 직접 사용하여 앱을 빌드하려면 Live SDK 지원을 사용하도록 설정되었는지 확인해야 합니다.

Live SDK 지원을 사용하지 않도록 설정하면 응용 프로그램 암호가 Azure AD 데이터 저장소에만 기록됩니다. Azure AD 데이터 저장소는 FISMA 규정 준수 등의 특정 표준을 충족하는 엔터프라이즈급 규정을 포함합니다. Live SDK 지원을 사용하도록 설정하면 응용 프로그램이 이러한 일부 표준을 준수하지 못할 수 있습니다.

Azure AD v2.0 끝점만 사용하려면 안전하게 Live SDK 지원을 사용하지 않도록 설정할 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->