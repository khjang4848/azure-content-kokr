<properties
	pageTitle="Azure Active Directory v2.0 Android 앱 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정이 있는 사용자로 로그인하고 타사 라이브러리를 사용하여 Graph API를 호출하는 Android 앱을 빌드하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

#  v2.0 끝점을 사용하는 Graph API와 함께 타사 라이브러리를 사용하여 Android 앱에 로그인 추가

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 개발자는 서비스와 통합하려는 모든 라이브러리를 사용할 수 있습니다. 개발자가 플랫폼을 다른 라이브러리와 함께 사용할 수 있도록 돕기 위해, 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습 몇 가지를 작성했습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

이 연습에서 만드는 응용 프로그램을 사용하여 해당 조직에 로그인한 다음 Graph API를 사용하여 조직에서 자신을 검색할 수 있습니다.

OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. 배경 지식을 위해 [2\.0 프로토콜 - OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols-oauth-code.md)을 읽어보는 것이 좋습니다.

> [AZURE.NOTE] 조건부 액세스 및 Intune 정책 관리 등과 같은 OAuth2 또는 OpenID Connect 표준의 식을 사용하는 플랫폼의 일부 기능은 수행하려면 오픈 소스인 Microsoft Azure ID 라이브러리를 사용해야 합니다.

v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다.

> [AZURE.NOTE] v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


## GitHub에서 코드 다운로드
이 자습서에 대한 코드는 [GitHub](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

샘플을 다운로드할 수도 있고 지금 바로 시작할 수도 있습니다.

```
git@github.com:Azure-Samples/active-directory-android-native-oidcandroidlib-v2.git
```

## 앱 등록
[응용 프로그램 등록 포털](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 [v2.0 끝점을 사용하여 앱을 등록하는 방법](active-directory-v2-app-registration.md)의 자세한 단계를 따르세요. 다음을 수행해야 합니다.

- 앱에 할당된 **응용 프로그램 ID**는 곧 필요하므로 적어둡니다.
- 앱용 **Mobile** 플랫폼을 추가합니다.

> 참고: 응용 프로그램 등록 포털은 **리디렉션 URI** 값을 제공합니다. 그러나 이 예제에서 `https://login.microsoftonline.com/common/oauth2/nativeclient`의 기본값을 사용해야 합니다.


## NXOAuth2 타사 라이브러리 다운로드 및 작업 영역 만들기

이 연습에서는 Google의 OpenID Connect 코드를 기반으로 하는 OAuth2 라이브러리인 GitHub의 OIDCAndroidLib을 사용합니다. 네이티브 응용 프로그램 프로필을 구현하고 사용자의 권한 부여 끝점을 지원합니다. 이것이 바로 Microsoft ID 플랫폼과 통합하기 위해 필요한 모든 것입니다.

컴퓨터에 OIDCAndroidLib 리포지토리를 복제합니다.

```
git@github.com:kalemontes/OIDCAndroidLib.git
```

![androidStudio](media/active-directory-android-native-oidcandroidlib-v2/emotes-url.png)

## Android Studio 환경 설정

1. 새 Android Studio 프로젝트를 만들고 마법사의 기본값을 그대로 적용합니다.

	![Android Studio에서 새 프로젝트 만들기](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample1.PNG)

	![대상 Android 장치](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample2.PNG)

	![모바일에 작업 추가](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample3.PNG)

2. 프로젝트 모듈을 설정하려면 복제된 리포지토리를 프로젝트 위치로 이동합니다. 또한 프로젝트를 만든 다음 프로젝트 위치로 직접 복제할 수도 있습니다.

	![프로젝트 모듈](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4_1.PNG)

3. 상황에 맞는 메뉴를 사용하거나 Ctrl+Alt+Maj+S 바로 가기를 사용하여 프로젝트 모듈 설정을 엽니다.

	![프로젝트 모듈 설정](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample4.PNG)

4. 프로젝트 컨테이너 설정만 원한다면 기본 앱 모듈을 제거합니다.

	![기본 앱 모듈](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample5.PNG)

5. 모듈을 복제 리포지토리에서 현재 프로젝트로 가져옵니다.

	![Gradle 프로젝트 가져오기](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample6.PNG) ![새 모듈 페이지 만들기](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample7.PNG)

6. `oidlib-sample` 모듈에 대해 이들 단계를 반복합니다.

7. `oidlib-sample` 모듈에서 Oidclib 종속성을 확인합니다.

	![oidlib-sample 모듈의 Oidclib 종속성](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8.PNG)

8. **확인**을 클릭하고 Gradle 동기화를 대기합니다.

	설정은 gradle처럼 보여야 합니다.

	![settings.gradle의 스크린샷](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample8_1.PNG)

9. 샘플 앱을 빌드하여 샘플이 제대로 실행되는지 확인합니다.

	아직은 이것을 Azure Active Directory와 함께 사용할 수 없습니다. 먼저 일부 끝점을 구성해야 합니다. 이는 샘플 앱을 사용자에 맞게 설정하기 전에 Android Studio 문제가 없도록 하기 위해서입니다.

10. `oidlib-sample`을 Android Studio의 대상으로 빌드하고 실행합니다.

	![oidlib-sample 빌드의 진행률](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample9.png)

11. 프로젝트에서 모듈을 제거할 때 AndroidStudio가 안전을 위해 삭제하지 않아 남은 `app ` 디렉터리를 삭제합니다.

	![앱 디렉터리를 포함하는 파일 구조](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample12.PNG)

12. 또한 **구성 편집** 메뉴를 열어 프로젝트에서 모듈을 제거할 때 남겨진 실행 구성도 제거할 수 있습니다.

	![구성 편집 메뉴](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample10.PNG) ![앱의 구성 실행](media/active-directory-android-native-oidcandroidlib-v2/SetUpSample11.PNG)

## 샘플의 끝점 구성

이제 `oidlib-sample`이 성공적으로 실행됨으로 Azure Active Directory와 작동되도록 일부 끝점을 편집합니다.

### oidc\_clientconf.xml 파일을 편집하여 클라이언트 구성

1. 토큰을 가져오고 Graph API를 호출하기 위해 OAuth2 흐름만 사용하기 때문에 OAuth2만 수행하도록 클라이언트를 설정합니다. OIDC에 대해서는 향후 예제에서 다룹니다.

	```xml
	    <bool name="oidc_oauth2only">true</bool>
	```

2. 등록 포털에서 받은 클라이언트 ID를 구성합니다.

	```xml
	    <string name="oidc_clientId">86172f9d-a1ae-4348-aafa-7b3e5d1b36f5</string>
	    <string name="oidc_clientSecret"></string>
	```

3. 아래의 ID로 리디렉션 URI를 구성합니다.

	```xml
	    <string name="oidc_redirectUrl">https://login.microsoftonline.com/common/oauth2/nativeclient</string>
	```

4. Graph API에 액세스하기 위해 필요한 범위를 구성합니다.

	```xml
	    <string-array name="oidc_scopes">
	        <item>openid</item>
	        <item>https://graph.microsoft.com/User.Read</item>
	        <item>offline_access</item>
	    </string-array>
	```

`oidc_scopes`의 `User.Read` 값을 사용하여 로그인한 사용자의 기본 프로필을 읽을 수 있습니다. [Microsoft Graph 권한 범위](https://graph.microsoft.io/docs/authorization/permission_scopes)에서 사용 가능한 모든 범위에 대해 알아볼 수 있습니다.

`openid` 또는 `offline_access`를 OpenID Connect의 범위로 구분해서 설명하는 내용을 보려면 [2\.0 프로토콜 - OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols-oauth-code.md)을 참조하세요.

### oidc\_endpoints.xml 파일을 편집하여 클라이언트 끝점 구성

- `oidc_endpoints.xml` 파일을 열어 다음과 같이 변경합니다.

	```xml
	<!-- Stores OpenID Connect provider endpoints. -->
	<resources>
	    <string name="op_authorizationEnpoint">https://login.microsoftonline.com/common/oauth2/v2.0/authorize</string>
	    <string name="op_tokenEndpoint">https://login.microsoftonline.com/common/oauth2/v2.0/token</string>
	    <string name="op_userInfoEndpoint">https://www.example.com/oauth2/userinfo</string>
	    <string name="op_revocationEndpoint">https://www.example.com/oauth2/revoketoken</string>
	</resources>
	```

OAuth2를 프로토콜로 사용하는 경우 이러한 끝점은 변경되어서는 안됩니다.

> [AZURE.NOTE]
`userInfoEndpoint` 및 `revocationEndpoint`에 대한 끝점은 현재 Azure Active Directory에서 지원되지 않습니다. 이러한 기본 example.com 값 상태로 두면 샘플에서 사용할 수 없다는 것에 유의하세요.


## Graph API 호출 구성

- `HomeActivity.java` 파일을 열어 다음과 같이 변경합니다.

	```Java
	   //TODO: set your protected resource url
	    private static final String protectedResUrl = "https://graph.microsoft.com/v1.0/me/";
	```

여기에 나오는 간단한 Graph API 호출이 예제 정보를 반환합니다.

여기에 나오는 내용만 변경하면 됩니다. `oidlib-sample` 응용 프로그램을 실행하고 **로그인**을 클릭합니다.

성공적으로 인증했으므로 Graph API로 호출을 테스트하려면 **보호된 리소스 요청** 단추를 선택합니다.

## 당사 제품에 대한 보안 업데이트 가져오기

[Security TechCenter](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->