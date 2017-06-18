<properties
	pageTitle="v2.0 앱 등록 | Microsoft Azure"
	description="v2.0 끝점을 사용하여 Microsoft 서비스 로그인 및 액세스를 사용하도록 설정하기 위해 Microsoft에 앱을 등록하는 방법"
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

# v2.0 끝점을 사용하여 앱을 등록하는 방법

MSA 와 Azure AD 로그인 모두를 허용하는 앱을 빌드하려면, 먼저 Microsoft에 앱을 등록해야 합니다. 지금은 Azure AD나 MSA를 사용하여 가지고 있는 기존의 앱은 사용할 수 없습니다. - 새 앱을 만들어야 합니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## Microsoft 앱 등록 포털 방문
가장 먼저 해야할 일입니다. - [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)로 이동합니다. 이곳은 Microsoft 앱을 관리할 수 있는 새로운 앱 등록 포털입니다.

개인, 직장 또는 학교 Microsoft 계정 중 하나로 로그인 합니다. 계정이 하나라도 없다면, 새로운 개인 계정을 등록합니다. 그렇게 오래 걸리지 않으니 등록하세요 - 기다리겠습니다.

등록했나요? 아마도 비어있을 Microsoft 앱의 목록을 보고 있을 것입니다. 변경을 시작해봅시다.

**앱 추가**를 클릭하여 이름을 지정합니다. 포털은 코드에서 나중에 사용하는 전역적으로 고유한 응용 프로그램 ID를 앱에 할당합니다. 앱이 API(예: Office, Azure 또는 사용자의 고유한 웹 API)를 호출하는 액세스 토큰이 필요한 서버 측 구성 요소를 포함한 경우, **응용 프로그램 암호** 또한 만들어야 합니다.
<!-- TODO: Link for app secrets -->

다음으로, 앱에서 사용할 플랫폼을 추가합니다.

- 웹 기반 앱의 경우 로그인 메시지를 보낼 수 있는 **리디렉션 URI**를 제공합니다.
- 모바일 앱의 경우 자동으로 만들어진 기본 리디렉션 URI를 적어둡니다.

필요에 따라 프로필 섹션에서 로그인 페이지의 디자인을 사용자 할당할 수 있습니다. 다음 단계로 넘어가기 전에 **저장**을 클릭하십시오.

> [AZURE.NOTE] [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com)을 사용하여 응용 프로그램을 만드는 경우 응용 프로그램은 포털에 로그인하는 데 사용하는 계정의 홈 테넌트에 등록됩니다. 즉, 개인 Microsoft 계정을 사용하여 Azure AD 테넌트에 응용 프로그램을 등록할 수 없습니다. 응용 프로그램을 특정 테넌트에 명시적으로 등록하려면 해당 테넌트에 원래 만든 계정을 사용하여 로그인합니다.

## 빠른 시작 앱 빌드하기
이제 Microsoft 앱을 가지고 있으므로 v2.0 빠른 시작 자습서 중 하나를 완료할 수 있습니다. 몇가지 권장 사항입니다.

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

<!---HONumber=AcomDC_0921_2016-->