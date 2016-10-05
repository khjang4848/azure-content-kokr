<properties
	pageTitle="Azure Mobile Engagement iOS SDK 도달률 통합 | Microsoft Azure"
	description="Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#iOS에서 Engagement 도달률을 통합하는 방법

이 가이드의 작업을 수행하기 전에 [iOS에서 Engagement를 통합하는 방법](mobile-engagement-ios-integrate-engagement.md) 문서에서 설명하는 통합 절차를 수행해야 합니다.

이 설명서에는 XCode 8이 필요합니다. XCode 7을 사용하는 경우 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)를 사용할 수도 있습니다. iOS 10 장치에서 실행하는 경우 이 이전 버전에는 알려진 버그가 있습니다. 시스템 알림은 작동하지 않습니다. 이 문제를 해결하려면 다음과 같이 앱 대리자에서 더 이상 사용되지 않는 API `application:didReceiveRemoteNotification:`을 구현해야 합니다.

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] 이 iOS API는 더 이상 사용되지 않으므로 예정된(부분적인) iOS 버전 업그레이드에서는 이 동작이 달라질 수 있습니다. 따라서 **이 해결 방법은 권장되지 않습니다**. 가능한 한 빨리 XCode 8로 전환해야 합니다.

### 앱이 자동 푸시 알림을 받을 수 있도록 설정

[AZURE.INCLUDE [mobile-engagement-ios-자동-푸시](../../includes/mobile-engagement-ios-silent-push.md)]

##통합 단계

### iOS 프로젝트에 Engagement 도달률 SDK 포함

-   Xcode 프로젝트에 도달률 SDK를 추가합니다. 이렇게 하려면 Xcode에서 **프로젝트 > 프로젝트에 추가**로 이동하여 `EngagementReach` 폴더를 선택합니다.

### 응용 프로그램 대리자 수정

-   구현 파일 맨 위에서 Engagement 도달률 모듈을 가져옵니다.

		[...]
		#import "AEReachModule.h"

-   이렇게 하려면 `applicationDidFinishLaunching:` 또는 `application:didFinishLaunchingWithOptions:` 메서드 내에서 도달률 모듈을 만든 다음 기존 Engagement 초기화 줄에 전달합니다.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
		  [...]

		  return YES;
		}

-   알림 아이콘에 사용할 이미지 이름으로 **'icon.png'** 문자열을 수정합니다.
-   도달률 캠페인에서 *배지 값 업데이트* 옵션을 사용하거나 네이티브 푸시 </SaaS/Reach API/Campaign format/Native Push> 캠페인을 사용하려는 경우에는 도달률 모듈이 배지 아이콘을 자체적으로 관리하도록 허용해야 합니다. 그러면 응용 프로그램을 시작하거나 포그라운드에 표시할 때마다 응용 프로그램 배지가 자동으로 지워지며 Engagement에서 저장한 값도 다시 설정됩니다. 이렇게 하려면 도달률 모듈 초기화 다음에 아래 줄을 추가합니다.

		[reach setAutoBadgeEnabled:YES];

-   도달률 데이터 푸시를 처리하려면 응용 프로그램 대리자가 `AEReachDataPushDelegate` 프로토콜을 따르도록 지정해야 합니다. 이렇게 하려면 도달률 모듈 초기화 다음에 아래 줄을 추가합니다.

		[reach setDataPushDelegate:self];

-   그리고 나면 응용 프로그램 대리자에서 `onDataPushStringReceived:` 및 `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` 메서드를 구현할 수 있습니다.

		-(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
		{
		   NSLog(@"String data push message with category <%@> received: %@", category, body);
		   return YES;
		}

		-(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
		{
		   NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
		   // Do something useful with decodedBody like updating an image view
		   return YES;
		}

### Category

데이터 푸시 캠페인을 만들 때는 필요에 따라 범주 매개 변수를 포함할 수 있으며, 그러면 데이터 푸시를 필터링할 수 있습니다. 이 매개 변수는 여러 종류의 `Base64` 데이터를 푸시하고 해당 데이터를 구문 분석하기 전에 데이터 형식을 식별하려는 경우에 유용합니다.

**이제 응용 프로그램이 도달률 콘텐츠를 받고 표시할 수 있습니다.**

##언제든지 알림과 설문 조사를 받는 방법

Engagement에서는 Apple 푸시 알림 서비스를 사용하여 언제든지 최종 사용자에게 도달률 알림을 보낼 수 있습니다.

이 기능을 사용하도록 설정하려면 Apple 푸시 알림을 받도록 응용 프로그램을 준비하고 응용 프로그램 대리자를 수정해야 합니다.

### Apple 푸시 알림을 받도록 응용 프로그램 준비

[Apple 푸시 알림을 받도록 응용 프로그램을 준비하는 방법](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6) 가이드를 따르십시오.

### 필요한 클라이언트 코드 추가

*이때 응용 프로그램에는 Engagement 프런트 엔드에는 등록된 Apple 푸시 인증서가 포함되어 있어야 합니다.*

해당 인증서가 없는 경우에는 응용 프로그램이 푸시 알림을 받도록 등록해야 합니다. 응용 프로그램 시작 시 다음 줄을 추가합니다. 일반적으로는 `application:didFinishLaunchingWithOptions:`에 이 줄을 추가합니다.

	if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
	  	[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
	  	[application registerForRemoteNotifications];
	}
	else {
	  	[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
	}

그런 다음 Apple 서버에서 반환하는 장치 토큰을 Engagement에 제공해야 합니다. 응용 프로그램 대리자의 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 메서드에서 이 작업을 수행합니다.

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	    [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

마지막으로 응용 프로그램이 원격 알림을 받을 때 Engagement SDK에 알림을 보내야 합니다. 이렇게 하려면 응용 프로그램 대리자에서 메서드 `applicationDidReceiveRemoteNotification:fetchCompletionHandler:`를 호출하면 됩니다.

	- (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

> [AZURE.NOTE] 위의 메서드는 iOS 7에서에서 도입되었습니다. iOS 7 이전 버전을 대상으로 하는 경우 응용 프로그램 대리자에서 메서드 `application:didReceiveRemoteNotification:`를 구현하고 `handler` 인수 대신에 nil을 전달하여 EngagementAgent에서 `applicationDidReceiveRemoteNotification`를 호출해야 합니다.

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] 기본적으로 Engagement 도달률이 completionHandler를 제어합니다. 코드의 `handler` 블록에 수동으로 응답하려는 경우 `handler` 인수에 대한 nil을 전달하고 완료 블록을 제어할 수 있습니다. 가능한 값의 목록에 대한 `UIBackgroundFetchResult` 형식을 참조하세요.


### 전체 예제

통합의 전체 예제는 다음과 같습니다.

	#pragma mark -
	#pragma mark Application lifecycle

	- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
	{
	  /* Reach module */
	  AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	  [reach setAutoBadgeEnabled:YES];

	  /* Engagement initialization */
	  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
	  [[EngagementAgent shared] setPushDelegate:self];

	  /* Views */
	  [window addSubview:[tabBarController view]];
	  [window makeKeyAndVisible];

	  [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
	  return YES;
	}

	- (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
	{
	  [[EngagementAgent shared] registerDeviceToken:deviceToken];
	}

	- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
	{
		[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
	}

### 사용자 고유의 UNUserNotificationCenterDelegate 구현이 있는 경우

SDK에도 자체적으로 UNUserNotificationCenterDelegate 프로토콜이 구현되어 있습니다. 이 프로토콜은 iOS 10 이상에서 실행되는 장치의 Engagement 알림 수명 주기를 모니터링하기 위해 SDK에서 사용됩니다. SDK에서 대리자를 검색하는 경우 응용 프로그램당 UNUserNotificationCenter 대리자가 하나만 있기 때문에 자체 구현을 사용하지 않습니다. 즉, 자체 대리자에 Engagement 논리를 추가해야 합니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

SDK에 대리자 호출 전달

	#import <UIKit/UIKit.h>
	#import "EngagementAgent.h"
	#import <UserNotifications/UserNotifications.h>


	@interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
	@end

	@implementation MyAppDelegate

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
	}
	@end

또는 `AEUserNotificationHandler` 클래스에서 상속

	#import "AEUserNotificationHandler.h"
	#import "EngagementAgent.h"

	@interface CustomUserNotificationHandler :AEUserNotificationHandler
	@end

	@implementation CustomUserNotificationHandler

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}

	@end

> [AZURE.NOTE] 해당 `userInfo` 사전을 에이전트 `isEngagementPushPayload:` 클래스 메서드에 전달하여 알림이 Engagement에서 온 것인지를 확인할 수 있습니다.

##캠페인을 사용자 지정하는 방법

### 알림

알림에는 시스템 알림과 앱 내 알림의 두 가지 유형이 있습니다.

시스템 알림은 iOS에서 처리되며 사용자 지정은 가능하지 않습니다.

앱 내 알림은 현재 응용 프로그램 창에 동적으로 추가되는 보기로 구성됩니다. 이 보기를 알림 오버레이라고 합니다. 알림 오버레이를 사용하는 경우 응용 프로그램에서 뷰를 수정할 필요가 없으므로 통합을 빠르게 수행할 수 있습니다.

#### 레이아웃

앱 내 알림의 모양을 수정하려는 경우 파일 `AENotificationView.xib`만 필요한 대로 수정하면 됩니다. 이때 기존 하위 뷰의 태그 값과 형식은 유지해야 합니다.

기본적으로 앱 내 알림은 화면 맨 아래에 표시됩니다. 앱 내 알림을 화면 맨 위에 표시하려면 제공된 `AENotificationView.xib`을(를) 편집하고 기본 뷰가 상위 뷰의 맨 위에 유지되도록 기본 뷰의 `AutoSizing` 속성을 변경합니다.

#### 범주

제공된 레이아웃을 수정하면 모든 알림의 모양이 수정됩니다. 범주를 사용하면 알림의 여러 대상 모양을 정의할 수 있으며 경우에 따라서는 동작도 정의할 수 있습니다. 도달률 캠페인을 만들 때 범주를 지정할 수 있습니다. 범주를 사용하면 알림과 설문 조사도 사용자 지정할 수 있습니다. 여기에 대해서는 이 문서의 뒷부분에서 설명합니다.

알림의 범주 처리기를 등록하려면 도달률 모델을 초기화한 후 호출을 추가해야 합니다.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:@"my_category"];
	...

`myNotifier`은(는) 프로토콜 `AENotifier`을(를) 따르는 개체 인스턴스여야 합니다.

프로토콜 메서드를 직접 구현할 수도 있고 이미 대부분의 작업을 수행하고 있는 기존 클래스 `AEDefaultNotifier`을(를) 다시 구현할 수도 있습니다.

예를 들어 특정 범주의 알림 뷰를 다시 정의하려는 경우 다음 예제를 따르면 됩니다.

	#import "AEDefaultNotifier.h"
	#import "AENotificationView.h"
	@interface MyNotifier : AEDefaultNotifier
	@end

	@implementation MyNotifier

	-(NSString*)nibNameForCategory:(NSString*)category
	{
	  return "MyNotificationView";
	}

	@end

이 간단한 범주 예제에서는 `MyNotificationView.xib` 파일이 주 응용 프로그램 번들에 포함되어 있다고 가정합니다. 메서드가 해당하는 `.xib`을(를) 찾을 수 없으면 알림이 표시되지 않으며 Engagement에서 콘솔에 메시지를 출력합니다.

제공된 nib 파일은 다음 규칙을 따라야 합니다.

-   파일에는 뷰가 하나만 포함되어야 합니다.
-   하위 뷰는 제공된 nib 파일 `AENotificationView.xib` 내의 하위 뷰와 같은 형식이어야 합니다.
-   하위 뷰는 제공된 nib 파일 `AENotificationView.xib` 내의 하위 뷰와 같은 태그를 포함해야 합니다.

> [AZURE.TIP] 제공된 nib 파일 `AENotificationView.xib`을(를) 복사하여 해당 위치에서 작업을 시작하면 됩니다. 그러나 이 nib 파일 내의 뷰는 클래스 `AENotificationView`에 연결되어 있습니다. 이 클래스는 컨텍스트에 따라 하위 뷰를 이동하고 크기를 조정하도록 메서드 `layoutSubViews`을(를) 다시 정의합니다. 해당 메서드를 `UIView` 또는 사용자 지정 뷰 클래스로 바꿀 수 있습니다.

코드에서 뷰를 직접 로드하려는 등의 경우 알림을 보다 상세하게 사용자 지정하려면 제공된 소스 코드와 `Protocol ReferencesDefaultNotifier` 및 `AENotifier`의 클래스 설명서를 확인하는 것이 좋습니다.

여러 범주에 대해 같은 알림 구성 요소를 사용할 수 있습니다.

다음과 같은 기본 알림 구성 요소를 다시 정의할 수도 있습니다.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### 알림 처리

기본 범주를 사용할 때는 통계를 보고하고 캠페인 상태를 업데이트하기 위해 `AEReachContent` 개체에서 일부 수명 주기 메서드를 호출합니다.

-   알림이 응용 프로그램에 표시될 때 `handleNotification:`이(가) `YES`을(를) 반환하면 통계를 보고하는 `displayNotification` 메서드가 `AEReachModule`을(를) 통해 호출됩니다.
-   알림을 해제하면 `exitNotification` 메서드가 호출되고 통계가 보고되며 다음 캠페인을 처리할 수 있게 됩니다.
-   알림을의 클릭하면 `actionNotification`이(가) 호출되고 통계가 보고되며 연결된 작업이 수행됩니다.

만약 `AENotifier` 구현이 기본 동작을 무시하는 경우에는 이러한 수명 주기 메서드를 직접 호출해야 합니다. 다음 예제에서는 기본 동작을 무시하는 몇 가지 경우를 보여 줍니다.

-   범주 처리를 처음부터 구현한 경우처럼 `AEDefaultNotifier`을(를) 확장하지 않은 경우
-   `prepareNotificationView:forContent:`을(를) 재정의한 경우. 이 경우에는 `onNotificationActioned` 또는 `onNotificationExited`을(를) 하나 이상 UI 컨트롤에 매핑해야 합니다.

> [AZURE.WARNING] `handleNotification:`에서 예외를 throw하는 경우 콘텐츠가 삭제되고 `drop`이(가) 호출되며 이 예외가 통계에 보고됩니다. 그리고 나면 다음 캠페인을 처리할 수 있습니다.

#### 기존 뷰의 일부로 알림 포함

오버레이는 통합을 빠르게 수행하는 데 유용하지만 사용이 불편하거나 원치 않는 부작용을 야기하는 경우도 있습니다.

일부 뷰의 오버레이 시스템이 효율적이지 않은 경우에는 해당 뷰에 대해 오버레이 시스템을 사용자 지정할 수 있습니다.

기존 뷰에 알림 레이아웃을 포함할 수 있습니다. 이렇게 하려는 경우 두 가지 구현 스타일을 사용할 수 있습니다.

1.  인터페이스 작성기를 사용하여 알림 뷰 추가

	-   *인터페이스 작성기*를 엽니다.
	-   알림을 표시할 320x60(iPad의 경우 768x60) 크기의 `UIView`을(를) 배치합니다.
	-   이 뷰의 태그 값 설정: **36822491**

2.  프로그래밍 방식으로 알림 뷰를 추가합니다. 이렇게 하려면 뷰가 초기화된 후 다음 코드만 추가하면 됩니다.

		UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
		notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
		[self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG` 매크로는 `AEDefaultNotifier.h`에 있습니다.

> [AZURE.NOTE] 기본 알림 구성 요소는 이 뷰에 알림 레이아웃이 포함되어 있음을 자동으로 검색하여 해당 뷰에 대한 오버레이를 추가하지 않습니다.

### 알림 및 설문 조사

#### 레이아웃

기존 하위 뷰의 태그 값과 형식만 유지하면 `AEDefaultAnnouncementView.xib` 및 `AEDefaultPollView.xib` 파일을 수정할 수 있습니다.

#### 범주

##### 대체 레이아웃

알림과 마찬가지로 캠페인의 경우에도 범주를 사용하여 알림 및 설문 조사의 대체 레이아웃을 적용할 수 있습니다.

알림의 범주를 만들려면 도달률 모듈이 초기화된 후 **AEAnnouncementViewController**를 확장한 다음 등록해야 합니다.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] 사용자가 "my\_category" 범주의 알림에 대한 알림을 클릭하면 메서드 `initWithAnnouncement:`을(를) 호출하여 등록된 뷰 컨트롤러(여기서는 `MyCustomAnnouncementViewController`)를 초기화한 다음 현재 응용 프로그램 창에 뷰를 추가합니다.

`AEAnnouncementViewController` 클래스 구현에서는 하위 뷰를 초기화하려면 속성 `announcement`을(를) 읽어야 합니다. 두 개의 레이블이 `AEReachAnnouncement` 클래스의 `title` 및 `body` 속성을 사용하여 초기화되는 아래 예제를 살펴보세요.

	-(void)loadView
	{
	    [super loadView];

	    UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    titleLabel.font = [UIFont systemFontOfSize:32.0];
	    titleLabel.text = self.announcement.title;

	    UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
	    bodyLabel.font = [UIFont systemFontOfSize:24.0];
	    bodyLabel.text = self.announcement.body;

	    [self.view addSubview:titleLabel];
	    [self.view addSubview:bodyLabel];
	}

뷰를 직접 로드하지 않고 기본 알림 뷰 레이아웃만 다시 사용하려는 경우 사용자 지정 뷰 컨트롤러가 제공된 `AEDefaultAnnouncementViewController` 클래스를 확장하도록 설정하기만 하면 됩니다. 이 경우에는 nib 파일 `AEDefaultAnnouncementView.xib`을(를) 복제한 다음 사용자 지정 뷰 컨트롤러가 로드할 수 있도록 이름을 바꿉니다. 컨트롤러 `CustomAnnouncementViewController`의 경우에는 nib 파일 `CustomAnnouncementView.xib`을(를) 호출해야 합니다.

알림의 기본 범주를 바꾸려면 `kAEReachDefaultCategory`에 정의된 범주에 대해 사용자 지정 뷰 컨트롤러를 등록하기만 하면 됩니다.

	[reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

같은 방식으로 설문 조사도 사용자 지정할 수 있습니다.

	AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
	[reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

이번에는 제공된 `MyCustomPollViewController`이(가) `AEPollViewController`을(를) 확장해야 합니다. 기본 컨트롤러인 `AEDefaultPollViewController`에서 확장할 수도 있습니다.

> [AZURE.IMPORTANT] 뷰 컨트롤러가 해제되기 전에 `action`(사용자 지정 설문 조사 뷰 컨트롤러의 경우 `submitAnswers:`) 또는 `exit` 메서드를 호출해야 합니다. 이렇게 하지 않으면 통계가 전송되지 않아 캠페인이 분석되지 않습니다. 또한 응용 프로그램 프로세스를 다시 시작할 때까지 다음 캠페인에 알림이 전달되지 않습니다.

##### 구현 예제

이 구현에서는 사용자 지정 알림 뷰를 외부 xib 파일에서 로드합니다.

고급 알림 사용자 지정과 마찬가지로 표준 구현의 소스 코드를 확인하는 것이 좋습니다.

`CustomAnnouncementViewController.h`

	//Interface
	@interface CustomAnnouncementViewController : AEAnnouncementViewController {
	  UILabel* titleLabel;
	  UITextView* descTextView;
	  UIWebView* htmlWebView;
	  UIButton* okButton;
	  UIButton* cancelButton;
	}

	@property (nonatomic, retain) IBOutlet UILabel* titleLabel;
	@property (nonatomic, retain) IBOutlet UITextView* descTextView;
	@property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
	@property (nonatomic, retain) IBOutlet UIButton* okButton;
	@property (nonatomic, retain) IBOutlet UIButton* cancelButton;

	-(IBAction)okButtonClicked:(id)sender;
	-(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

	//Implementation
	@implementation CustomAnnouncementViewController
	@synthesize titleLabel;
	@synthesize descTextView;
	@synthesize htmlWebView;
	@synthesize okButton;
	@synthesize cancelButton;

	-(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
	{
	  self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
	  if (self != nil) {
	    self.announcement = anAnnouncement;
	  }
	  return self;
	}

	- (void) dealloc
	{
	  [titleLabel release];
	  [descTextView release];
	  [htmlWebView release];
	  [okButton release];
	  [cancelButton release];
	  [super dealloc];
	}

	- (void)viewDidLoad {
	  [super viewDidLoad];

	  /* Init announcement title */
	  titleLabel.text = self.announcement.title;

	  /* Init announcement body */
	  if(self.announcement.type == AEAnnouncementTypeHtml)
	  {
	    titleLabel.hidden = YES;
	    htmlWebView.hidden = NO;
	    [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
	  }
	  else
	  {
	    titleLabel.hidden = NO;
	    htmlWebView.hidden = YES;
	    descTextView.text = self.announcement.body;
	  }

	  /* Set action button label */
	  if([self.announcement.actionLabel length] > 0)
	    [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

	  /* Set exit button label */
	  if([self.announcement.exitLabel length] > 0)
	    [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
	}

	#pragma mark Actions

	-(IBAction)okButtonClicked:(id)sender
	{
	    [self action];
	}

	-(IBAction)cancelButtonClicked:(id)sender
	{
	    [self exit];
	}

	@end

<!---HONumber=AcomDC_0921_2016-->