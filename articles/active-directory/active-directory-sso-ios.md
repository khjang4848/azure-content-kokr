<properties
	pageTitle="ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정하는 방법 | Microsoft Azure"
	description="ADAL SDK의 기능을 사용하여 응용 프로그램에서 Single Sign On을 활성화하는 방법입니다. "
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>


# ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정하는 방법


사용자가 자격 증명을 한 번만 입력하고 응용 프로그램에서 자동으로 작동되도록 SSO(Single Sign-on)를 제공하는 것은 이제 고객에게 필요합니다. 종종 전화 통화 또는 문자로 전송하는 코드와 같은 추가 요소(2FA)로 결합된 작은 화면에서 자신의 사용자 이름 및 암호를 입력하는 어려움은 사용자가 제품에 대해 이를 한 번 이상 수행해야 하는 경우 빠른 불만족을 가져 옵니다.

또한 다른 응용 프로그램이 office365에서 Microsoft 계정 또는 회사 계정을 사용할 수 있는 ID 플랫폼을 활용하는 경우 고객은 공급 업체에 관계 없이 모든 응용 프로그램에서 자격 증명을 사용할 수 있기를 기대합니다.

Microsoft ID SDK와 함께 Microsoft ID 플랫폼은 이 모든 어려운 작업을 수행하며 자체 응용 프로그램 제품 내의 SSO 또는 전체 장치에서 브로커 기능 및 Authenticator 응용 프로그램으로 고객을 만족시키는 기능을 제공합니다.

이 연습에서는 고객에게 이 혜택을 제공하도록 응용 프로그램 내에서 SDK를 구성하는 방법을 알려줍니다.

이 연습은 다음에 적용됩니다.

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory 조건부 액세스


아래 문서는 [Azure Active Directory에 대한 레거시 포털에서 응용 프로그램을 프로비전](active-directory-how-to-integrate.md)하는 방법을 알고 있으며 응용 프로그램을 [Microsoft ID iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc)와 통합했다고 가정합니다.

## Microsoft ID 플랫폼의 SSO 개념

### Microsoft ID Broker

Microsoft는 다른 공급 업체의 응용 프로그램에서 자격 증명의 브리징을 제공할 뿐만 아니라 자격 증명을 확인하는 위치에서 단일 보안 위치가 필요한 특수한 향상된 기능을 허용하는 모든 모바일 플랫폼에 대한 응용 프로그램을 제공합니다. 이를 **브로커**라고 합니다. iOS 및 Android에서 고객이 독립적으로 설치하거나 해당 직원에 대한 일부 또는 모든 장치를 관리하는 회사에서 장치에 푸시할 수 있는 다운로드 가능한 응용 프로그램을 통해 제공됩니다. 이러한 브로커는 일부 응용 프로그램 또는 IT 관리자가 원하는 기능에 따라 전체 장치에 대해서만 보안 관리를 지원합니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다.

이러한 브로커를 사용하는 방법 및 고객이 Microsoft ID 플랫폼에 대한 로그인 흐름에서 이를 보는 방법을 이해하려면 자세한 내용을 읽어 보세요.

### 모바일 장치에 로그인하기 위한 패턴

장치에서 자격 증명에 대한 액세스는 Microsoft ID 플랫폼에 대해 두 가지 기본 패턴을 따릅니다.

* 비 브로커 지원 로그인
* 브로커 지원 로그인

#### 비 브로커 지원 로그인

비 브로커 지원 로그인은 응용 프로그램과 함께 인라인을 발생하고 해당 응용 프로그램에 대한 장치에서 로컬 저장소를 사용하는 로그인 환경입니다. 이 저장소는 응용 프로그램 간에 공유될 수 있지만 자격 증명은 해당 자격 증명을 사용하는 앱 또는 앱의 제품군에 밀접하게 바인딩됩니다. 이는 응용 프로그램 자체 내에서 사용자 이름 및 암호를 입력하는 많은 모바일 응용 프로그램에서 경험할 가능성이 가장 큰 환경입니다.

이러한 로그인은 다음과 같은 이점이 있습니다.

-  사용자 환경은 응용 프로그램 내에 완전히 존재합니다.
-  응용 프로그램 제품군에 Single Sign-On 환경을 제공하여 동일한 인증서로 서명하는 응용 프로그램에서 자격 증명을 공유할 수 있습니다.
-  로그인 환경 주위의 제어는 로그인 이전 및 이후에 응용 프로그램에 제공됩니다.

이러한 로그인은 다음과 같은 단점이 있습니다.

- 사용자는 Microsoft ID를 사용하는 모든 앱이 아닌 응용 프로그램이 소유하고 구성한 해당 Microsoft ID 간에서만 Single Sign-On을 경험할 수 있습니다.
- 조건부 액세스와 같은 고급 비즈니스 기능과 함께 응용 프로그램을 사용하거나 InTune 제품군을 사용할 수 없습니다.
- 응용 프로그램은 비즈니스 사용자에 대한 인증서 기반 인증을 지원할 수 없습니다.

Microsoft ID SDK가 응용 프로그램의 공유 저장소와 작업하여 SSO를 활성화하는 방법에 대한 표현은 다음과 같습니다.

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### 브로커 지원 로그인

브로커 지원 로그인은 브로커 응용 프로그램 내에서 발생하고 Microsoft ID 플랫폼을 활용하는 장치의 모든 응용 프로그램에서 자격 증명을 공유하도록 브로커의 저장소와 보안을 사용하는 로그인 환경입니다. 즉, 응용 프로그램은 사용자가 로그인할 수 있도록 브로커를 사용합니다. iOS 및 Android에서 고객이 독립적으로 설치하거나 사용자에 대한 장치를 관리하는 회사에서 장치에 푸시할 수 있는 다운로드 가능한 응용 프로그램을 통해 제공됩니다. 이 응용 프로그램 유형의 예는 iOS에서 Azure Authenticator 응용 프로그램입니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다. 환경은 플랫폼별로 다르며 올바르게 관리되지 않는 경우 사용자에게 작업 중단이 발생할 수 있습니다. Facebook 응용 프로그램을 설치하고 다른 응용 프로그램에서 Facebook 로그인 기능을 사용하는 경우 아마도 이 패턴과 가장 친숙할 것입니다. Microsoft ID 플랫폼은 동일한 패턴을 활용합니다.

iOS의 경우 이는 Azure Authenticator 응용 프로그램이 사용자가 로그인하려는 계정을 선택하도록 포그라운드로 오는 동안 응용 프로그램이 백그라운드로 전송되는 "전환" 애니메이션이 됩니다.

Android 및 Windows의 경우 계정 선택기가 사용자에게 덜 방해가 되는 응용 프로그램 맨 위에 표시됩니다.

#### 브로커를 호출하는 방법

호환되는 브로커가 Azure Authenticator 응용 프로그램과 마찬가지로 장치에 설치된 경우 Microsoft ID SDK는 사용자가 Microsoft ID 플랫폼에서 계정을 사용하여 로그인하려는 것을 나타내는 경우 브로커 호출 작업을 자동으로 수행합니다. 이는 개인 Microsoft 계정, 회사 또는 학교 계정 또는 B2C 및 B2B 제품을 사용하여 Azure에 제공 및 호스트하는 계정일 수 있습니다. 매우 안전한 알고리즘 및 암호화를 사용하여 안전한 방법으로 응용 프로그램에 자격 증명이 요청되고 다시 제공됩니다. 이러한 메커니즘의 정확한 기술적인 세부 사항은 게시되지 않지만 Apple 및 Google과 공동 작업으로 개발되었습니다.

**개발자는 Microsoft ID SDK가 브로커를 호출할지 비 브로커 지원 흐름을 사용할지를 선택할 수 있습니다.** 그러나 개발자가 브로커 지원 흐름을 사용하지 않도록 선택하는 경우 사용자가 장치에 이미 추가했을 뿐만 아니라 조건부 액세스, Intune 관리 기능 및 인증서 기반 인증과 같은 Microsoft가 고객에게 제공하는 비즈니스 기능으로 응용 프로그램이 사용되는 것을 예방하는 SSO 자격 증명 활용의 이점을 잃게 됩니다.

이러한 로그인은 다음과 같은 이점이 있습니다.

-  사용자는 공급 업체에 관계 없이 모든 응용 프로그램에서 SSO를 경험합니다.
-  응용 프로그램은 조건부 액세스와 같은 고급 비즈니스 기능을 활용하거나 InTune 제품군을 사용할 수 있습니다.
-  응용 프로그램은 비즈니스 사용자에 대한 인증서 기반 인증을 지원할 수 있습니다.
- 응용 프로그램 및 사용자의 ID로서 더 안전한 로그인 환경은 추가 보안 알고리즘 및 암호화로 브로커 응용 프로그램에 의해 확인됩니다.

이러한 로그인은 다음과 같은 단점이 있습니다.

- iOS에서 사용자는 자격 증명을 선택하는 동안 응용 프로그램의 환경 밖으로 전환됩니다.
- 응용 프로그램 내에서 고객에 대한 로그인 환경을 관리하는 기능이 손실됩니다.



Microsoft ID SDK가 브로커 응용 프로그램과 작업하여 SSO를 활성화하는 방법에 대한 표현은 다음과 같습니다.

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```
              
이 배경 정보를 사용하여 Microsoft ID 플랫폼 및 SDK를 사용하여 응용 프로그램 내에서 SSO를 더 잘 이해하고 구현할 수 있어야 합니다.


## ADAL을 사용하여 앱 간 SSO 활성화

여기에서 ADAL iOS SDK를 사용하여 다음 작업을 수행합니다.

- 앱 제품군에 대한 비 브로커 지원 SSO 설정
- 브로커 지원 SSO에 대한 지원 설정

### 비 브로커 지원 SSO에 대한 SSO 설정

응용 프로그램에서 비 브로커 지원 SSO의 경우 Microsoft ID SDK는 SSO의 복잡성을 관리합니다. 캐시에서 올바른 사용자를 찾고 쿼리할 로그인된 사용자의 목록을 유지 관리하는 것을 포함합니다.

소유하고 있는 응용 프로그램에서 SSO를 활성화하려면 다음을 수행해야 합니다.

1. 모든 응용 프로그램 사용자가 동일한 클라이언트 ID 또는 응용 프로그램 ID인지 확인합니다.
* 모든 응용 프로그램이 키 집합을 공유할 수 있도록 Apple에서 동일한 서명 인증서를 공유하는지 확인합니다.
* 각 응용 프로그램에 대한 동일한 키 집합 권한 부여를 요청합니다.
* Microsoft ID SDK에 사용했으면 하는 공유 키 집합을 제공합니다.

#### 앱 제품군에서 모든 응용 프로그램에 대한 동일한 클라이언트 ID / 응용 프로그램 ID 사용

Microsoft ID 플랫폼이 응용 프로그램에서 토큰을 공유하도록 허용하는지 알려면 각 응용 프로그램은 동일한 클라이언트 ID 또는 응용 프로그램 ID를 공유해야 합니다. 포털에 첫 번째 응용 프로그램을 등록했던 경우에 제공된 고유 식별자입니다.

동일한 응용 프로그램 ID를 사용하는 경우 다른 앱을 Microsoft ID 서비스에 식별하는 방법을 궁금해 할 수 있습니다. **리디렉션 URI**를 사용하면 됩니다. 각 응용 프로그램에는 등록 포털에 등록한 여러 개의 리디렉션 URI가 있을 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 표시되는 모양의 예는 다음과 같습니다.

App1 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

이는 동일한 클라이언트 ID / 응용 프로그램 ID 아래에 중첩되며 SDK 구성에서 우리에게 반환하는 리디렉션 URI에 따라 조회됩니다.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*이러한 리디렉션 URI의 형식은 아래에 설명되어 있습니다. 브로커를 지원하려고 하는 한 모든 리디렉션 URI를 사용할 수 있습니다. 이 경우 위와 같이 표시되어야 합니다.*



#### 응용 프로그램 간에 공유되는 키 집합 만들기

키 집합 공유 활성화는 이 문서의 범위를 벗어나며 Apple에서 해당 문서 [기능 추가](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)에 포함됩니다. 중요한 것은 사용자가 키 집합이 불리는 이름을 결정하는 것과 모든 응용 프로그램에 해당 기능을 추가하는 것입니다.

올바르게 설정된 권한 부여를 가진 경우 다음처럼 보이는 항목이 포함된 `entitlements.plist`의 자격 있는 프로젝트 디렉터리에 파일이 보입니다.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>keychain-access-groups</key>
	<array>
		<string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
		<string>$(AppIdentifierPrefix)com.myapp.mycache</string>
	</array>
</dict>
</plist>
```

각 응용 프로그램에서 키 집합 권한 부여를 활성화하면 SSO를 사용할 준비가 되었으며 다음 설정으로 `ADAuthenticationSettings`에서 다음 설정을 사용하여 Microsoft ID SDK에 키 집합에 대해 제공합니다.

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING] 
응용 프로그램에서 키 집합을 공유하는 경우 모든 응용 프로그램은 사용자를 삭제하거나 더 심한 경우 응용 프로그램에서 모든 토큰을 삭제할 수 있습니다. 백그라운드 작업을 하기 위해 토큰을 사용하는 응용 프로그램이 있는 경우에 특히 미치는 영향이 커집니다. 키 집합을 공유하면 Microsoft ID SDK를 통한 모든 제거 작업에 특히 주의해야 합니다.

이것으로 끝입니다. 이제 Microsoft ID SDK는 모든 응용 프로그램에서 자격 증명을 공유합니다. 사용자 목록도 응용 프로그램 인스턴스 간에 공유됩니다.

### 브로커 지원 SSO에 대한 SSO 설정

장치에 설치되어 있는 모든 브로커를 사용하는 응용 프로그램에 대한 기능은 **기본적으로 해제되어** 있습니다. 브로커와 함께 응용 프로그램을 사용하려면 몇 가지 추가 구성을 수행하고 응용 프로그램에 코드를 추가해야 합니다.

수행할 단계는 다음과 같습니다.

1. MS SDK에 대한 응용 프로그램 코드의 호출에서 브로커 모드를 활성화합니다.
2. 새 리디렉션 URI를 설정하고 앱과 앱 등록에 이를 제공합니다.
3. URL 구성표 등록.
4. iOS9 지원: info.plist 파일에 권한을 추가합니다.


#### 1단계: 응용 프로그램에서 브로커 모드 활성화
"컨텍스트" 또는 인증 개체의 초기 설정을 만들 때 브로커를 사용하는 응용 프로그램에 대한 기능은 설정되어 있습니다. 코드에서 자격 증명 형식을 설정하여 이 작업을 수행합니다.

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` 설정을 사용하면 Microsoft ID SDK는 브로커에 호출하려고 시도할 수 있으며 `AD_CREDENTIALS_EMBEDDED`은(는) Microsoft ID SDK가 브로커에 호출할 수 없도록 합니다.

#### 2단계: URL 구성표 등록
Microsoft ID 플랫폼은 URL을 사용하여 브로커를 호출한 다음 응용 프로그램에 제어를 다시 반환합니다. 해당 왕복을 완료하려면 Microsoft ID 플랫폼이 아는 응용 프로그램에 등록된 URL 구성표가 필요합니다. 이전에 응용 프로그램에 이미 등록했을 수 있는 다른 앱 구성표 외에도 될 수 있습니다.

> [AZURE.WARNING] 
다른 앱이 동일한 URL 구성표를 사용할 가능성을 최소화하기 위해 URL 구성표를 매우 고유하게 만드는 것이 좋습니다. Apple은 앱 스토어에 등록된 URL 구성표의 고유성을 적용하지 않습니다.

다음은 프로젝트 구성에 나타나는 예입니다. 또한 XCode에서도 이를 수행할 수 있습니다.

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### 3단계: URL 구성표와 함께 새 리디렉션 URI 설정

올바른 응용 프로그램에 자격 증명 토큰을 항상 반환하는지 확인하려면 iOS 운영 체제에서 확인할 수 있는 방식으로 응용 프로그램에 다시 호출하는지 확인해야 합니다. iOS 운영 체제는 호출하는 응용 프로그램의 번들 ID를 Microsoft 브로커 응용 프로그램에 보고합니다. 불량 응용 프로그램에서 이를 스푸핑할 수 없습니다. 따라서 토큰이 올바른 응용 프로그램에 반환되는지 확인하도록 브로커 응용 프로그램의 URI와 함께 활용합니다. 이 고유한 리디렉션 URI를 응용 프로그램 및 개발자 포털에서 리디렉션 URI로 설정해야 합니다.

리디렉션 URI는 다음의 적절한 형식이어야 합니다.

`<app-scheme>://<your.bundle.id>`

예: *x-msauth-mytestiosapp://com.myapp.mytestapp*

[Azure 클래식 포털](https://manage.windowsazure.com/)을 사용하여 앱 등록에 이 리디렉션 URI를 지정해야 합니다. Azure AD 앱 등록에 대한 자세한 내용은 [Azure Active Directory와 통합](active-directory-how-to-integrate.md)을 참조하세요.


##### 3a단계: 인증서 기반 인증을 지원하도록 앱 및 개발자 포털에 리디렉션 URI 추가

인증서 기반 인증을 지원하기 위해 응용 프로그램에 해당 지원을 추가하려는 경우 두 번째 "msauth"를 응용 프로그램 및 [Azure 클래식 포털](https://manage.windowsazure.com/)에 등록하여 인증서 인증을 처리해야 합니다.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

예: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### 4단계: iOS9: 앱에 구성 매개 변수 추가

ADAL은 canOpenURL을 사용하여 브로커가 장치에 설치되어 있는지 확인합니다. iOS 9에서 Apple은 응용 프로그램에서 쿼리할 수 있는 구성표를 잠궜습니다. `info.plist file`의 LSApplicationQueriesSchemes 섹션에 "Msauth"를 추가해야 합니다.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string> </array>

### SSO를 구성했습니다.

이제 Microsoft ID SDK는 자동으로 응용 프로그램에서 자격 증명을 공유하고 장치에 있는 경우 브로커를 호출합니다.

<!---HONumber=AcomDC_0921_2016-->