<properties
	pageTitle="Azure Mobile Engagement iOS SDK 통합 | Microsoft Azure"
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

#IOS에서 Engagement를 통합하는 방법

> [AZURE.SELECTOR]
- [Windows 범용](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

이 절차에서는 iOS 응용 프로그램에서 Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

Engagement SDK에는 iOS6 이상 및 Xcode 8이 필요합니다. 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

> [AZURE.NOTE]
XCode 7을 사용하는 경우 [iOS Engagement SDK v3.2.4](https://aka.ms/r6oouh)를 사용할 수도 있습니다. iOS 10 장치에서 실행되는 경우 이 이전 버전의 도달률 모듈에서는 알려진 버그가 있습니다. 자세한 내용은 [도달률 모듈 통합](mobile-engagement-ios-integrate-engagement-reach.md)을 참조하세요. SDK v3.2.4를 사용하도록 선택하는 경우 다음 단계의 `UserNotifications.framework` 가져오기로 건너뜁니다.

다음 단계만 수행하면 사용자, 세션, 활동, 작동 중단 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그 보고를 활성화할 수 있습니다. 이벤트, 오류, 작업 등의 기타 통계는 응용 프로그램별로 다르므로, 해당 통계를 계산하는 데 필요한 로그 보고는 Engagement API를 사용하여 수동으로 수행해야 합니다. 관련 설명은 [iOS 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-ios-use-engagement-api.md)을 참조하세요.

##IOS 프로젝트에 Engagement SDK를 포함합니다.

- [여기](http://aka.ms/qk2rnj)에서 iOS SDK를 다운로드합니다.

- iOS 프로젝트에 Engagement SDK를 추가합니다. Xcode에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **"Add files to ..."**를 선택하고 `EngagementSDK` 폴더를 선택합니다.

- Engagement가 작동하려면 추가 프레임워크가 필요합니다. 프로젝트 탐색기에서 프로젝트 창을 열고 올바른 대상을 선택합니다. 그런 다음 **"Build phases"** 탭을 열고 **"Link Binary With Libraries"** 메뉴에서 다음 프레임워크를 추가합니다.

	-   `UserNotifications.framework` - 링크를 `Optional`(으)로 설정합니다.
	-   `AdSupport.framework` - 링크를 `Optional`(으)로 설정합니다.
	-   `SystemConfiguration.framework`
	-   `CoreTelephony.framework`
	-   `CFNetwork.framework`
	-   `CoreLocation.framework`
	-   `libxml2.dylib`

> [AZURE.NOTE] AdSupport 프레임워크는 제거할 수 있습니다. Engagement에서 IDFA를 수집하려면 이 프레임워크가 필요합니다. 그러나 이 ID와 관련된 새 Apple 정책을 준수하기 위해 IDFA 컬렉션을 비활성화할 수 있습니다(<ios-sdk-engagement-idfa>).

##Engagement SDK 초기화

다음과 같이 응용 프로그램 대리자를 수정해야 합니다.

-   구현 파일의 맨 위에서 Engagement 에이전트를 가져옵니다.

		[...]
		#import "EngagementAgent.h"

-   '**applicationDidFinishLaunching:**' 또는 '**application:didFinishLaunchingWithOptions:**' 메서드에서 Engagement를 초기화합니다.

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		  [...]
		  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
		  [...]
		}

##기본 보고

### 권장 방법: `UIViewController` 클래스 오버로드

Engagement에서 사용자, 세션, 활동, 크래시 및 기술 통계를 계산하기 위해 필요한 모든 로그의 보고서를 활성화하려면 모든 `UIViewController` 하위 클래스에서 `EngagementViewController` 클래스를 상속하도록 설정하면 됩니다(`UITableViewController`->`EngagementTableViewController`에 대한 동일한 규칙).

**Engagement 사용 안 함:**

	#import <UIKit/UIKit.h>

	@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Engagement 사용:**

	#import <UIKit/UIKit.h>
	#import "EngagementViewController.h"

	@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
	  UITextField* myTextField1;
	  UITextField* myTextField2;
	}

	@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
	@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### 대체 메서드: 수동으로 `startActivity()` 호출

`UIViewController` 클래스를 오버로드할 수 없거나 하지 않으려는 경우 `EngagementAgent`의 메서드를 직접 호출하여 작업을 시작할 수 있습니다.

> [AZURE.IMPORTANT] 응용 프로그램이 닫힐 때 iOS SDK에서 `endActivity()` 메서드를 자동으로 호출합니다. 따라서 사용자 활동이 변경될 때마다 `startActivity` 메서드를 호출하는 것이 *상당히* 좋으며 `endActivity` 메서드는 호출하지 *않는* 것이 좋습니다. 이 메서드를 호출하면 현재 세션이 강제로 종료되기 때문입니다.

##위치 보고

Apple 서비스 약관에서는 응용 프로그램이 통계 용도로만 위치 추적 사용을 사용하도록 허용하지 않습니다. 따라서 응용 프로그램이 다른 이유로도 추적 위치를 사용하는 경우에만 보고서 위치를 활성화하는 것이 좋습니다.

iOS 8부터는 앱의 Info.plist 파일에서 [NSLocationWhenInUseUsageDescription] 또는 [NSLocationAlwaysUsageDescription] 키에 대한 문자열을 설정하여 앱이 위치 서비스를 사용하는 방법에 대한 설명을 제공해야 합니다. Engagement로 백그라운드에서 위치를 보고하려는 경우 NSLocationAlwaysUsageDescription 키를 추가합니다. 다른 모든 경우에는 NSLocationWhenInUseUsageDescription 키를 추가합니다.

### 지연 영역 위치 보고

지연 영역 위치 보고를 통해 국가, 지역 및 장치와 연결된 위치를 보고할 수 있습니다. 이러한 유형의 위치 보고에서는 네트워크 위치(셀 ID 또는 WIFI 기반)만 사용합니다. 장치 영역은 세션당 한번 이하로 보고됩니다. GPS는 전혀 사용되지 않으므로 이러한 위치 보고는 배터리에 거의 영향을 미치지 않습니다.

보고된 영역은 사용자, 세션, 이벤트 및 오류에 대한 지리적 통계를 계산 하는 데 사용됩니다. 이 영역은 도달률 캠페인의 기준으로도 사용할 수 있습니다. [장치 API] 덕분에 장치에 대해 마지막으로 알려진 영역을 검색할 수 있습니다.

지연 영역 위치 보고를 사용하려면 Engagement 에이전트를 초기화한 후 다음 줄을 추가합니다.

	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	{
	  [...]
	  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
	  [...]
	}

### 실시간 위치 보고

실시간 위치 보고를 통해 장치와 연결된 위도와 경도를 보고할 수 있습니다. 기본적으로 이 유형의 위치 보고에서는 네트워크 위치(셀 ID 또는 WIFI 기반)만 사용하고, 보고는 응용 프로그램이 포그라운드로 실행될 때(즉, 세션 중)만 활성화됩니다.

실시간 위치는 통계를 계산하는 데 사용되지 *않습니다*. 유일한 용도는 도달률 캠페인에서 실시간 지리적 펜스 <Reach-Audience-geofencing> 사용을 허용하는 것입니다.

실시간 위치 보고를 활성화하려면 Engagement 에이전트를 초기화한 후 다음 줄을 추가합니다.

	[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### GPS 기반 보고

기본적으로 실시간 위치 보고에서는 네트워크 기반 위치만 사용합니다. 훨씬 더 정밀한 GPS 기반 위치의 사용을 설정하려면 다음을 추가합니다.

	[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### 백그라운드 보고

기본적으로 실시간 위치 보고는 응용 프로그램이 포그라운드로 실행되는 경우(즉, 세션 중)에만 활성 상태입니다. 백그라운드에서도 보고를 활성화하려면 다음을 추가합니다.

	[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] 응용 프로그램이 백그라운드에서 실행될 때 GPS를 활성화한 경우에도 네트워크 기반 위치만 보고됩니다.

이 함수의 구현은 응용 프로그램이 백그라운드로 전환되는 경우 [startMonitoringSignificantLocationChanges]를 호출합니다. 새 위치 이벤트가 도달하는 경우 응용 프로그램을 백그라운드로 자동으로 다시 시작합니다.

##고급 보고

필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고하려는 경우 `EngagementAgent` 클래스의 메서드를 통해 Engagement API를 사용해야 합니다. `[EngagementAgent shared]` 정적 메서드를 호출하여 이 클래스의 개체를 검색할 수 있습니다.

Engagement API는 모든 Engagement의 고급 기능 사용을 허용하며 iOS의 Engagement API 사용 방법(및 `EngagementAgent` 클래스의 기술 문서)에 자세히 설명되어 있습니다.

##IDFA 컬렉션 비활성화

기본적으로 Engagement에서 [IDFA]를 사용하여 사용자를 고유하게 식별합니다. 그러나 앱의 다른 위치에서 알림을 사용하지 않는 경우 앱 스토어 검토 프로세스를 통해 거부될 수 있습니다. pch 파일에서(또는 응용 프로그램의 `Build Settings`에서) 전처리기 매크로 `ENGAGEMENT_DISABLE_IDFA`을(를) 추가하여 IDFA 컬렉션을 비활성화할 수 있습니다. 따라서 응용 프로그램 빌드에서 `ASIdentifierManager`, `advertisingIdentifier` 또는 `isAdvertisingTrackingEnabled`에 대한 참조가 없다는 것을 확인할 수 있습니다.

**prefix.pch** 파일의 통합:

	#define ENGAGEMENT_DISABLE_IDFA
	...

Engagement 테스트 로그를 확인하여 응용 프로그램에서 IDFA 컬렉션이 제대로 비활성화되었는지 확인할 수 있습니다. 자세한 내용은 통합 테스트 <ios-sdk-engagement-test-idfa> 설명서를 참조하세요.

##로그 보고 사용 안 함

### 메서드 호출 사용

Engagement에서 로그 전송을 중지하려면 다음을 호출할 수 있습니다.

	[[EngagementAgent shared] setEnabled:NO];

이 호출은 영구적이며, `NSUserDefaults`을(를) 사용하여 정보를 저장합니다.

또한 `YES`(으)로 동일한 함수를 호출하여 로그 보고를 다시 활성화할 수 있습니다.

### 설정 번들의 통합

이 함수를 호출하지 않고, 기존 `Settings.bundle` 파일에서 이 설정을 직접 통합할 수 있습니다. 문자열 `engagement_agent_enabled`은(는) 기본 설정 식별자로 사용해야 하며 토글 스위치(`PSToggleSwitchSpecifier`)와 연결해야 합니다.

`Settings.bundle`의 다음 예제에서는 구현 방법을 보여 줍니다.

	<dict>
	    <key>PreferenceSpecifiers</key>
	    <array>
	        <dict>
	            <key>DefaultValue</key>
	            <true/>
	            <key>Key</key>
	            <string>engagement_agent_enabled</string>
	            <key>Title</key>
	            <string>Log reporting enabled</string>
	            <key>Type</key>
	            <string>PSToggleSwitchSpecifier</string>
	        </dict>
	    </array>
	    <key>StringsTable</key>
	    <string>Root</string>
	</dict>

<!-- URLs. -->
[장치 API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]: https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]: http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]: https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!---HONumber=AcomDC_0921_2016-->