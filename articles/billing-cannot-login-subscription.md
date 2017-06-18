<properties
	pageTitle="내 Azure 구독 관리를 위해 로그인할 수 없음 | Microsoft Azure"
	description="몇 가지 일반적인 Azure 구독 로그인 문제에 대한 문제 해결 정보를 설명합니다."
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="genli"/>

# 내 Azure 구독 관리를 위해 로그인할 수 없음

이 문서는 로그인 문제를 해결하기 위한 가장 일반적인 방법 중 일부를 안내합니다.

> [AZURE.NOTE] 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## Azure 포털

| 이름 | 설명 | URL |
|---|---|---|
| Azure 포털 |Azure 리소스를 프로비전하고 관리할 수 있는 중앙 위치입니다. | [https://portal.azure.com/](https://portal.azure.com) |
| Azure 클래식 포털 | 이전 Azure 포털 |[https://manage.windowsazure.com](https://manage.windowsazure.com) |
| Azure 계정 센터 | Azure 사용량을 추적하고 구독을 관리할 수 있는 중앙 위치입니다. |[https://account.windowsazure.com/Subscriptions](https://account.windowsazure.com/Subscriptions) ||

## 증상: 페이지가 로딩 상태에서 중지됨

이 문제는 인터넷 브라우저에 영향을 주는 문제 때문일 수 있습니다.

이 문제를 해결하려면 다음 방법을 지정된 순서로 시도해 보세요. 각 방법을 수행한 후 포털에서 로그인 페이지에 다시 연결해 보세요.

-	페이지를 새로 고칩니다.
-	다른 인터넷 브라우저를 사용합니다.
-	Microsoft Internet Explorer를 사용하는 경우 InPrivate 브라우징 모드를 사용하여 Azure 포털로 이동합니다. 이렇게 하려면 다음 단계를 수행하세요.

	A. **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **보안** > **InPrivate 브라우징**을 클릭합니다.

	B. [Azure 포털](https://portal.azure.com) 또는 [Azure 클래식 포털](https://manage.windowsazure.com)로 이동한 후 포털에 로그인합니다.

## 증상: "구독을 찾을 수 없음" 오류 메시지

이 문제는 계정에 충분한 사용자 권한이 없는 경우에 발생할 수 있습니다. 계정 관리자는 [계정 센터](https://account.windowsazure.com/)에만 액세스할 수 있는 반면 SA(서비스 관리자) 및 CA(공동 관리자)는 [Azure 포털](https://portal.azure.com) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에 대한 액세스 권한이 있습니다.

**시나리오 1: Azure 포털 또는 Azure 클래식 포털에서 오류 메시지를 받음**

이 문제를 해결하려면 계정에 대해 [공동 관리자 또는 소유자 역할을 추가](billing-add-change-azure-subscription-administrator.md)합니다.

**시나리오 2: Azure 계정 센터에서 오류 메시지를 받음**

사용한 계정이 계정 관리자인지 확인합니다. 계정 관리자가 누구인지 확인하려면 다음 단계를 따르세요.

1.	[Azure 포털](https://portal.azure.com)에 로그인합니다.
2.	허브 메뉴에서 **구독**을 선택합니다.
3.	확인하려는 구독을 선택한 다음 **설정**을 선택합니다.
4.	**속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.

## 증상: 다른 사용자로 자동으로 로그인됨

인터넷 브라우저에서 둘 이상의 사용자 계정을 사용 중인 경우 이 문제가 발생할 수 있습니다.

이 문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.

- 포털에서 로그아웃한 후 사용하려는 계정으로 다시 로그인합니다.
-	캐시의 선택을 취소하고 인터넷 쿠키를 삭제합니다. Internet Explorer에서 이 작업을 수행하려면 **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **인터넷 옵션** > **삭제**를 클릭하고 임시 파일, 쿠키, 암호 및 검색 기록에 대한 확인란이 선택되었는지 확인한 후 삭제를 클릭합니다.

-	Internet Explorer 설정을 재설정하여 수행한 모든 개인 설정을 되돌립니다. 이 작업을 수행하려면 **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png)> **인터넷 옵션** > **고급** >을 클릭하고 **개인 설정 삭제** 상자 > **재설정**을 선택합니다.

-	InPrivate 브라우징 모드에서 Azure 포털로 이동합니다. 이 작업을 수행하려면 **도구** ![도구 단추](./media/billing-cannot-login-subscription/Toolsbutton.png) > **보안** > **InPrivate 브라우징**을 클릭합니다.

## Microsoft 계정 및 조직 계정

Microsoft 계정은 사용하는 메일 주소이며 암호와 함께 사용하여 Outlook, Hotmail, MSN 또는 OneDrive와 같은 Windows Live 프로그램 또는 서비스에 로그인합니다. 회사 메일을 비롯하여 사용하는 모든 메일 주소로 Microsoft 계정을 설정할 수 있습니다. 자세한 내용은 [www.microsoft.com/account](http://www.microsoft.com/account)를 참조하세요.

계정이 조직 계정에 연결된 경우 아래와 같이 올바른 로그인 옵션을 선택하세요. 조직 계정 사용에 대한 자세한 내용은 [조직으로 Azure에 등록](./active-directory/sign-up-organization.md)을 참조하세요.

![로그인 페이지](./media/billing-cannot-login-subscription/signin.png)

> [AZURE.NOTE] 계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

<!---HONumber=AcomDC_0914_2016-->