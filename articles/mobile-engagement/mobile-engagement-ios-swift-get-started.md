<properties
	pageTitle="Swift에서 iOS용 Azure Mobile Engagement 시작 | Microsoft Azure"
	description="iOS 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="hero-article"
	ms.date="09/20/2016"
	ms.author="piyushjo" />

# Swift에서 iOS 앱용 Azure Mobile Engagement 시작

[AZURE.INCLUDE [영웅 자습서 전환기](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용법을 이해하고 iOS 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 기본 데이터를 수집하고 APNS(Apple 푸시 알림 시스템)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ MAC 앱 스토어에서 설치할 수 있는 XCode 8
+ [Mobile Engagement iOS SDK]
+ Apple 개발자 센터에서 가져올 수 있는 푸시 알림 인증서(.p12)

> [AZURE.NOTE] 이 자습서에서는 Swift 버전 3.0을 사용합니다.

이 자습서를 완료해야 다른 모든 iOS 앱용 Mobile Engagement 자습서를 진행할 수 있습니다.

> [AZURE.NOTE] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started)을 참조하세요.

##<a id="setup-azme"></a>iOS 앱용 Mobile Engagement 설정

[AZURE.INCLUDE [포털에서 Mobile Engagement 앱 만들기](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement iOS SDK 통합](mobile-engagement-ios-sdk-overview.md)에서 찾을 수 있습니다.

여기서는 통합을 시연하기 위해 XCode를 사용하여 기본적인 앱을 만듭니다.

###새 iOS 프로젝트 만들기

[AZURE.INCLUDE [새 iOS 프로젝트 만들기](../../includes/mobile-engagement-create-new-ios-app.md)]

###Mobile Engagement 백 엔드에 앱 연결

1. [Mobile Engagement iOS SDK]를 다운로드합니다.
2. .tar.gz 파일을 컴퓨터의 폴더로 추출합니다.
3. 프로젝트를 마우스 오른쪽 단추로 클릭하고 "파일을 추가할 위치..."를 선택합니다.

	![][1]

4. SDK를 추출한 폴더로 이동하고 `EngagementSDK` 폴더를 선택한 후 확인을 누릅니다.

	![][2]

5. `Build Phases` 탭을 열고 `Link Binary With Libraries` 메뉴에서 아래와 같이 프레임워크를 추가합니다.

	![][3]

8. 파일 > 새로 만들기 > 파일 > iOS > 소스 > 헤더 파일을 선택하여 SDK의 Objective C API를 사용할 수 있도록 Bridging 헤더를 만듭니다.

	![][4]

9. Mobile Engagement Objective-C 코드를 Swift 코드에 노출하도록 Bridging 헤더 파일을 편집하고 다음 import를 추가합니다.

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEUserNotificationHandler.h"
		#import "AEIdfaProvider.h"

10. 빌드 설정에서 Swift 컴파일러 - 코드 생성 아래의 Objective-C Bridging 헤더 빌드 설정에 이 헤더에 대한 경로가 있는지 확인합니다. 다음은 경로 예입니다. **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h(경로에 따라 다름)**

	![][6]

11. Azure 포털의 앱 *연결 정보* 페이지로 돌아가서 연결 문자열을 복사합니다.

	![][5]

12. 이제 연결 문자열을 `didFinishLaunchingWithOptions` 대리자에 붙여넣습니다.

		func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>실시간 모니터링 사용

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

1. **ViewController.swift** 파일을 열고 **ViewController**의 기본 클래스를 **EngagementViewController**가 되도록 바꿉니다.

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>실시간 모니터링과 앱 연결

[AZURE.INCLUDE [실시간 모니터링과 앱 연결](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

### 앱이 자동 푸시 알림을 받을 수 있도록 설정

[AZURE.INCLUDE [mobile-engagement-ios-자동-푸시](../../includes/mobile-engagement-ios-silent-push.md)]

### 프로젝트에 도달률 라이브러리 추가

1. 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
2. `Add file to ...`을(를) 선택합니다.
3. SDK를 추출한 폴더로 이동합니다.
4. `EngagementReach` 폴더를 선택합니다.
5. 추가를 클릭합니다.
6. Mobile Engagement Objective-C Reach 헤더를 노출하도록 Bridging 헤더 파일을 편집하고 다음 import를 추가합니다.

		/* Mobile Engagement Reach */
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEReachPollQuestion.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### 응용 프로그램 대리자 수정

1. 다음과 같이 `didFinishLaunchingWithOptions` 내부에서 도달률 모듈을 생성하여 기존 참여 초기화 줄에 전달합니다.

		func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
		{
			let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
    		EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###앱이 APNS 푸시 알림을 받을 수 있도록 설정
1. 다음 줄을 `didFinishLaunchingWithOptions` 메서드에 추가합니다.

		if #available(iOS 8.0, *)
		{
			if #available(iOS 10.0, *)
			{
				UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
			}else
			{
				let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
				application.registerUserNotificationSettings(settings)
			}
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
		}

2. 다음과 같이 `didRegisterForRemoteNotificationsWithDeviceToken` 메서드를 추가합니다.

		func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. 다음과 같이 `didReceiveRemoteNotification:fetchCompletionHandler:` 메서드를 추가합니다.

		func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0928_2016-->