<properties
	pageTitle="Azure 앱 서비스 웹 앱에서 사용자 지정 도메인 이름 구입 방법"
	description="Azure 앱 서비스에서 웹 앱으로 사용자 지정 도메인 이름을 구입하는 방법에 대해 알아봅니다."
	services="app-service\web"
	documentationCenter=""
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Azure 앱 서비스에서 사용자 지정 도메인 이름 구입 및 구성

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

웹 앱을 만들면 Azure에서 azurewebsites.net의 하위 도메인에 할당됩니다. 예를 들어 웹 앱의 이름이 **contoso**인 경우 URL은 **contoso.azurewebsites.net**입니다. Azure는 가상 IP 주소도 할당합니다.

프로덕션 웹 앱의 경우 사용자에게 사용자 지정 도메인 이름을 표시할 수 있습니다. 이 문서에서는 [앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)으로 사용자 지정 도메인을 구입 및 구성하는 방법에 대해 설명합니다.

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## 개요

웹앱에 대한 도메인 이름을 없는 경우 [Azure 포털](https://portal.azure.com/)에서 쉽게 구입할 수 있습니다. 구매 과정 중에 WWW 및 루트 도메인의 DNS 레코드가 웹 앱에 자동으로 매핑되도록 선택할 수 있습니다. 또한 Azure 포털 내에서 도메인을 관리할 수도 있습니다.


도메인 이름을 구입하고 웹 앱에 할당하려면 다음 단계를 수행합니다.

1. 브라우저에서 [Azure 포털](https://portal.azure.com/)을 엽니다.

2. **웹앱** 탭에서 웹앱의 이름을 클릭하고 **설정**을 선택한 다음 **사용자 지정 도메인**을 선택합니다.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. **사용자 지정 도메인** 블레이드에서 **도메인 구입**을 클릭합니다.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. **도메인 구입** 블레이드에서 텍스트 상자를 사용하여 구입할 도메인 이름을 입력하고 Enter 키를 누릅니다. 사용 가능한 도메인이 텍스트 상자 아래에 나타납니다. 구입할 도메인을 선택합니다. 여러 도메인을 한 번에 구입할 수 있습니다.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. **연락처 정보**를 클릭하고 도메인의 연락처 정보 양식을 작성합니다.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] It is very important that you fill out all required fields with as much accuracy as possible, especially the email address. In case of purchasing the domain without "Privacy protection", you might be asked to verify your email before the domain becomes active. In some cases, incorrect data for contact information will result in failure to purchase domains. 

6. 이제 다음과 같은 옵션을 선택할 수 있습니다.

	a) 매년 도메인 "자동 갱신"
	
	b) 무료로 구입 가격에 포함된 "개인정보 보호" 옵트인(레지스트리가 개인 정보를 지원하지 않는 TLD 제외. 예: .co.in, .co.uk 등)
	
	c) 현재 웹 앱에 WWW 및 루트 도메인의 "기본 호스트 이름 할당"

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Option C configures DNS bindings and Hostname bindings automatically for you.  This way, your Web App can be accessed using custom domain as soon as the purchase is complete (baring DNS propagation delays in few cases). In case, your Web App is behind Azure Traffic Manager, you will not see an option to assign root domain, as A-Records do not work with the Traffic Manager. 
    You can always assign the domains/sub-domains purchased through one Web App to another Web App and vice-versa. See step 8 for more details. 
	
7. **도메인 구입** 블레이드에서 **선택**을 클릭하면 **구매 확인** 블레이드에 구매 정보가 표시됩니다. 약관에 동의하고 **구입**을 클릭하면 주문이 제출되며, **알림**에서 구매 과정을 모니터링할 수 있습니다. 도메인 구매를 완료하는 데는 몇 분 정도 걸릴 수 있습니다.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. 도메인을 성공적으로 주문했으면 도메인을 관리하고 웹 앱에 할당할 수 있습니다. 도메인 오른쪽의 **"..."**를 클릭합니다. 그러면 **구매 취소** 또는 **도메인 관리**를 수행할 수 있습니다. **도메인 관리**를 클릭하면 **도메인 관리** 블레이드에서 **하위 도메인**을 웹 앱에 바인딩할 수 있습니다. **하위 도메인**을 다른 웹 앱에 바인딩하려는 경우는 해당 웹 앱의 컨텍스트 내에서 이 단계를 수행합니다. 여기서 트래픽 관리자를 드롭다운 메뉴에서 선택하기만 하면 도메인을 트래픽 관리자 끝점에 할당할 수 있습니다(웹 앱이 TM 뒤에 있는 경우). 이 작업을 수행하면 도메인/하위 도메인이 해당 트래픽 관리자 끝점 뒤에 있는 모든 웹 앱에 자동으로 할당됩니다.

	![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] 5일 이내에 "구매 취소"하면 전액 환불이 가능합니다. 5일 후에는 "구매 취소"를 할 수 없으며, 도메인 "삭제" 옵션이 대신 표시됩니다. 도메인을 삭제하면 환불 없이 구독에서 해제되어 사용 가능한 도메인이 됩니다.

구성이 완료되면 사용자 지정 도메인 이름이 웹 앱의 **호스트 이름 바인딩** 섹션에 표시됩니다.

이때 브라우저에서 사용자 지정 도메인 이름을 입력해야 웹 앱으로 이동하는지 확인할 수 있습니다.
 
## 구입한 사용자 지정 도메인은 어떻게 되나요?

구입한 사용자 지정 도메인은 **사용자 지정 도메인 및 SSL** 블레이드에서 Azure 구독에 연결됩니다. 이 사용자 지정 도메인은 Azure 리소스로서 처음 도메인을 구입한 앱 서비스 앱과 별도이며 독립적입니다. 이는 다음을 의미합니다.

- Azure 포털 내에서 사용자 지정 도메인을 처음 구입한 앱뿐 아니라 둘 이상의 앱 서비스 앱에 대해 사용자 지정 도메인을 사용할 수 있습니다.
- 해당 구독에서 *모든* 앱 서비스 앱의 **사용자 지정 도메인 및 SSL** 블레이드로 이동하여 Azure 구독에서 구입한 모든 사용자 지정 도메인을 관리할 수 있습니다.
- 동일한 Azure 구독에서 해당 사용자 지정 도메인 내의 하위 도메인에 앱 서비스 앱을 할당할 수 있습니다.
- 앱 서비스 앱을 삭제하려고 할 때 해당 앱 서비스 앱이 바인딩된 사용자 지정 도메인을 다른 앱에서 계속 사용하려는 경우 삭제하지 않도록 선택할 수 있습니다.

## 구입한 사용자 지정 도메인이 표시되지 않는 경우

**사용자 지정 도메인 및 SSL** 블레이드 내에서 사용자 지정 도메인을 구입했지만 **관리되는 도메인**에 해당 사용자 지정 도메인이 표시되지 않는 경우 다음을 확인하세요.

- 사용자 지정 도메인 만들기 작업이 완료되지 않았을 수 있습니다. Azure 포털의 위쪽에서 진행 상황에 대한 알림 벨을 확인합니다.
- 사용자 지정 도메인 만들기가 몇 가지 이유로 실패했을 수 있습니다. Azure 포털의 위쪽에서 진행 상황에 대한 알림 벨을 확인합니다.
- 사용자 지정 도메인 만들기가 성공했지만 아직 새로 고쳐지지 않았을 수 있습니다. **사용자 지정 도메인 및 SSL** 블레이드를 다시 열어 봅니다.
- 일부 지점에서 사용자 지정 도메인을 삭제했을 수 있습니다. 앱의 주 블레이드에서 **설정** > **감사 로그**를 클릭하여 감사 로그를 확인합니다.
- 표시된 **사용자 지정 도메인 및 SSL** 블레이드가 다른 Azure 구독에서 생성된 앱에 속해 있을 수 있습니다. 다른 구독의 다른 앱으로 전환하고 해당 **사용자 지정 도메인 및 SSL** 블레이드를 확인합니다. 포털 내에서는 해당 앱과 다른 Azure 구독에서 만든 사용자 지정 도메인을 보거나 관리할 수 없습니다. 그러나 도메인의 **도메인 관리** 블레이드에서 **고급 관리**를 클릭하면 해당 도메인 공급자의 웹 사이트로 리디렉션되며, 여기에서 다른 Azure 구독에서 만든 앱에 대해 [외부 사용자 지정 도메인과 같은 사용자 지정 도메인을 수동으로 구성](web-sites-custom-domain-name.md)할 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->