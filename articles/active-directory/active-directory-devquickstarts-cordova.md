<properties
	pageTitle="Azure AD Cordova 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 Cordova 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter=""
	authors="vibronet"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="vittorib"/>

# Azure AD를 Apache Cordova 앱에 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova를 사용하여 모든 기능이 갖춰진 네이티브 응용 프로그램으로 모바일 장치에서 실행될 수 있는 HTML5/JavaScript 응용 프로그램을 개발할 수 있습니다. Azure AD를 사용하면 Cordova 응용 프로그램에 엔터프라이즈급 인증 기능을 추가할 수 있습니다. iOS, Android, Windows 스토어 및 Windows Phone을 래핑하는 Cordova 플러그 인 덕분에, 사용자의 AD 계정을 사용하는 로그온을 지원하고, Office 365 및 Azure API에 액세스하고, 사용자 고유의 사용자 지정 Web API에 대한 호출도 보호하도록 응용 프로그램을 향상시킬 수 있습니다.

이 자습서에서는 ADAL(Active Directory 인증 라이브러리)에 대한 Apache Cordova 플러그 인을 사용하여 다음 기능으로 간단한 앱을 개선해봅니다.

-	단 몇 줄의 코드로, AD 사용자를 인증하고 Azure AD Graph API를 호출하기 위한 토큰을 가져옵니다.
-	해당 토큰을 사용하여 해당 디렉터리를 쿼리하고 결과를 표시하는 Graph API를 호출합니다.
-	사용자에 대한 인증 프롬프트를 최소화하기 위해 ADAL 토큰 캐시를 활용합니다.

이 작업을 수행하려면 다음 작업이 필요합니다.

2. Azure AD에 응용 프로그램을 등록합니다.
2. 앱에 토큰을 요청하는 코드를 추가합니다.
3. 해당 토큰을 사용하여 Graph API를 쿼리하고 결과를 표시하기 위한 코드를 추가합니다.
4. 대상으로 지정할 모든 플랫폼 및 Cordova ADAL 플러그 인으로 Cordova 배포 프로젝트를 만들고 에뮬레이터에서 솔루션을 테스트합니다.

## *0. 필수 조건*

이 자습서를 완료하려면 다음이 필요합니다.

- 앱 개발 권한이 있는 계정이 있는 Azure AD 테넌트
- Apache Cordova를 사용하도록 구성된 개발 환경

위의 두 항목을 모두 설정한 경우 1단계를 바로 진행하세요.

Azure AD 테넌트가 없는 경우 [여기에서 가져오는 방법에 대한 지침](active-directory-howto-tenant.md)을 찾을 수 있습니다.

시스템에서 Apache Cordova를 설정하지 않으려면 다음을 설치하세요.

- [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/)(NPM 패키지 관리자 `npm install -g cordova`를 통해 쉽게 설치 가능)

이러한 프로그램은 PC와 Mac 둘 다에서 작동합니다.

대상 플랫폼마다 필수 구성 요소가 다릅니다.

- Windows Tablet/PC 또는 Phone 앱 버전을 빌드하여 실행하려면
	- [Windows용 Visual Studio 2013 업데이트 2 이상](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8)(Express 또는 다른 버전)
- iOS용 버전을 빌드하여 실행하려면
	-   Xcode 5.x 이상 http://developer.apple.com/downloads 또는 [Mac 앱 스토어](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)에서 다운로드
	-   [ios sim](https://www.npmjs.org/package/ios-sim) – 명령줄에서 iOS 시뮬레이터로 iOS 앱을 시작할 수 있습니다(`npm install -g ios-sim` 터미널을 통해 쉽게 설치 가능).

- Android용 응용 프로그램을 빌드하고 실행하려면
	- [JDK(Java Development Kit) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 이상을 설치합니다. JDK 설치 경로(예: C:\\Program Files\\Java\\jdk1.7.0\_75)에 따라 `JAVA_HOME`(환경 변수)이 올바르게 설정되어 있는지 확인합니다.
	- [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools)를 설치하고 `<android-sdk-location>\tools` 위치(예: C:\\tools\\Android\\android-sdk\\tools)를 `PATH` 환경 변수에 추가합니다.
	- Android SDK Manager를 열고(예를 들어 `android` 터미널을 통해) 설치합니다.
    - *Android 5.0.1(API 21)* 플랫폼 SDK
    - *Android SDK Build-tools* 버전 19.1.0 이상
    - *Android 지원 리포지토리*(기타)

  Android sdk는 기본 에뮬레이터 인스턴스를 제공하지 않습니다. 에뮬레이터에서 Android 앱을 실행하려는 경우 터미널에서 `android avd`를 실행하고 *만들기...*를 선택하여 새 에뮬레이터를 만듭니다. 권장 *API 수준*은 19 이상입니다. Android 에뮬레이터 및 만들기 옵션에 대한 자세한 내용은 [AVD Manager](http://developer.android.com/tools/help/avd-manager.html)(영문)를 참조하세요.


## *1. Azure AD에 응용 프로그램 등록*

참고: 이 __단계는 선택 사항__입니다. 이 자습서는 자체 테넌트에서 프로비전을 수행하지 않고도 작동하는 샘플을 볼 수 있도록 하는 미리 프로비전된 값을 제공합니다. 그러나 자체 응용 프로그램을 만들 때 필요하므로 이 단계를 수행하고 프로세스에 익숙해지는 것이 좋습니다.

Azure AD는 알려진 응용 프로그램으로만 토큰을 발급합니다. 앱에서 Azure AD를 사용하려면 먼저 테넌트에서 해당 항목을 만들어야 합니다. 테넌트에 새 응용 프로그램을 등록하려면

-	[Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다. Cordova 앱은 HTML 기반이지만 여기에 네이티브 클라이언트 응용 프로그램을 만들므로 `Native Client Application` 옵션을 선택해야 합니다. 그러지 않으면 응용 프로그램이 작동하지 않습니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 URI**는 앱에 토큰을 반환하는 데 사용되는 URI입니다. `http://MyDirectorySearcherApp`을 입력합니다.

등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에 필요하며, 새로 만든 앱의 **구성** 탭에서 찾을 수 있습니다.

`DirSearchClient Sample`을 실행하기 위해 새로 만든 앱에 _Azure AD Graph API_를 쿼리하는 권한을 부여합니다.
-	**구성** 탭에서 "다른 응용 프로그램에 대한 권한" 섹션을 찾습니다. "Azure Active Directory" 응용 프로그램의 경우 **위임된 권한**에서 **로그인한 사용자로 디렉터리 액세스** 권한을 추가합니다. 이렇게 하면 응용 프로그램은 Graph API에서 사용자를 쿼리할 수 있습니다.

## *2. 자습서에 필요한 샘플 앱 리포지토리 복제*

셸 또는 명령줄에서 다음 명령을 입력합니다.

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## *3. Cordova 앱 만들기*

여러 가지 방법으로 Cordova 응용 프로그램을 만들 수 있습니다. 이 자습서에서는 Cordova CLI(명령줄 인터페이스)를 사용합니다. 셸 또는 명령줄에서 다음 명령을 입력합니다.


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

이렇게 하면 Cordova 프로젝트에 대한 폴더 구조 및 스캐폴딩이 생성되고, 시작 프로젝트의 콘텐츠가 www 하위 폴더로 복사됩니다. 새 DirSearchClient 폴더로 이동합니다.

    cd .\DirSearchClient

Graph API를 호출하는 데 필요한 허용 목록 플러그 인을 추가합니다.

     cordova plugin add cordova-plugin-whitelist

그럼 후 지원할 모든 플랫폼을 추가합니다. 작업 예제를 사용하려면 아래 명령 중 하나 이상을 실행해야 합니다. Windows에서는 iOS를, Mac에서는 Windows/Windows Phone을 에뮬레이트할 수 없습니다.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

마지막으로 Cordova용 ADAL 플러그 인을 프로젝트에 추가할 수 있습니다.

    cordova plugin add cordova-plugin-ms-adal

## *3. 사용자를 인증하고 AAD에서 토큰을 가져오기 위한 코드 추가*

이 자습서에서 개발하는 응용 프로그램은 기본적인 디렉터리 검색 기능 틀을 제공합니다. 최종 사용자는 디렉터리에 임의 사용자의 별칭을 입력하고 일부 기본 특성을 시각화할 수 있습니다. 시작 프로젝트에는 앱의 기본 사용자 인터페이스 정의(www/index.html), 기본 앱 이벤트 주기를 연결하는 스캐폴딩의 정의, 사용자 인터페이스 바인딩 및 결과 표시 논리(www/js/index.js)가 포함되어 있습니다. 따라서 사용자는 ID 작업을 구현하는 논리만 추가하면 됩니다.

가장 먼저 해야 할 작업은 앱 및 대상 리소스를 식별하기 위해 AAD에서 사용되는 프로토콜 값을 코드에 추가하는 것입니다. 이러한 값은 나중에 토큰 요청을 생성하는 데 사용됩니다. Index.js 파일의 맨 위에 아래의 코드 조각을 삽입합니다.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` 및 `clientId` 값은 AAD에서 앱을 설명하는 값과 일치해야 합니다. 이 자습서 앞부분에 나오는 1단계에서 설명한 것처럼 Azure 포털의 구성 탭에서 이러한 값을 찾을 수 있습니다. 참고: 자신의 테넌트에 새 앱을 등록하지 않기로 한 경우 위의 미리 구성된 값을 있는 그대로 붙여 넣어도 됩니다. 프로덕션에 사용하려는 앱의 경우 항상 자체 항목을 만들어야 하지만 미리 구성된 값을 사용해도 샘플이 실행되는 것을 확인할 수 있습니다.

다음으로 실제 토큰 요청 코드를 추가해야 합니다. `search `및 `renderdata `정의 사이에 다음 코드 조각을 삽입합니다.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
두 주요 부분에서 분석하여 해당 함수를 검토해 보겠습니다. 이 샘플은 특정 테넌트에만 국한되지 않고 어떤 테넌트에서도 작동하도록 디자인되어 있습니다. 이 샘플은 인증 시에 사용자가 임의 계정을 입력하도록 하고 계정이 속하는 테넌트로 요청을 전달하는 "/common" 끝점을 사용합니다. 이 메서드의 첫 번째 부분에서는 ADAL 캐시를 조사하여 저장된 토큰이 이미 있는지 확인합니다. 토큰이 있으면 ADAL을 다시 초기화하기 위해 해당 토큰이 있던 테넌트를 사용합니다. "/common"을 사용하면 항상 사용자에게 새 계정을 입력하라는 메시지가 표시되므로 불필요한 확인 메시지가 표시되지 않도록 해야 합니다.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
이 메서드의 두 번째 부분에서는 적절한 토큰 요청을 수행합니다. `acquireTokenSilentAsync` 메서드는 UX를 표시하지 않고 ADAL에 지정된 리소스에 대한 토큰의 반환을 요청합니다. 캐시에 이미 적합한 액세스 토큰이 저장되어 있거나 확인 메시지를 표시하지 않고 새 액세스 토큰을 가져오는 데 사용할 수 있는 새로 고침 토큰이 있는 경우 이러한 작업이 진행될 수 있습니다. 이 시도가 실패하면 사용자에게 인증을 요구하는 메시지를 표시하는 `acquireTokenAsync`로 대체할 것입니다.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
이제 토큰이 있으므로 마지막으로 Graph API를 호출하고 원하는 검색 쿼리를 수행할 수 있습니다. `authenticate` 정의 바로 아래에 다음 코드 조각을 삽입합니다.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
시작점 파일은 텍스트 상자에 사용자의 별칭을 입력하기 위한 기본 UX를 제공했습니다. 이 메서드는 해당 값을 사용하여 쿼리를 생성하고, 액세스 토큰에 결합하고, Graph로 보내고, 결과를 구문 분석합니다. 시작점 파일에 이미 있는 renderData 메서드는 결과를 시각화하려고 합니다.

## *4. 실행*
앱을 실행할 준비가 되었습니다. 작동은 매우 간단합니다. 앱이 시작되면 텍스트 상자에 찾으려는 사용자의 별칭을 입력하고 단추를 클릭합니다. 인증을 위한 메시지가 표시됩니다. 성공적으로 인증하고 검색하면 검색된 사용자의 특성이 표시됩니다. 이전에 가져온 토큰이 캐시에 있기 때문에 후속 검색은 인증을 요구하지 않고 진행됩니다. 앱을 실행하는 구체적인 단계는 플랫폼에 따라 다릅니다.

####Windows 10:

   태블릿/PC: `cordova run windows --archs=x64 -- --appx=uap`

   모바일(Windows 10 Mobile 장치가 PC에 연결되어야 함): `cordova run windows --archs=arm -- --appx=uap --phone`

   __참고__: 처음 실행할 때는 개발자 라이선스를 확인하기 위해 로그인하라는 메시지가 표시될 수 있습니다. 자세한 내용은 [개발자 라이선스](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)를 참조하세요.

####Windows 8.1 태블릿/PC:

   `cordova run windows`

   __참고__: 처음 실행할 때는 개발자 라이선스를 확인하기 위해 로그인하라는 메시지가 표시될 수 있습니다. 자세한 내용은 [개발자 라이선스](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)를 참조하세요.

####Windows Phone 8.1:

   연결된 장치에서 실행하려면: `cordova run windows --device -- --phone`

   기본 에뮬레이터에서 실행하려면: `cordova emulate windows -- --phone`

   사용 가능한 모든 대상을 보려면 `cordova run windows --list -- --phone`을 사용하고, 특정 장치 또는 에뮬레이터에서 응용 프로그램을 실행하려면 `cordova run windows --target=<target_name> -- --phone`을 사용합니다(예: `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####Android:

   연결된 장치에서 실행하려면: `cordova run android --device`

   기본 에뮬레이터에서 실행하려면: `cordova emulate android`

   __참고__: *필수 구성 요소* 섹션에 표시된 것처럼 *AVD Manager*를 사용하여 에뮬레이터 인스턴스를 만들었는지 확인합니다.

   사용 가능한 모든 대상을 보려면 `cordova run android --list`을 사용하고, 특정 장치 또는 에뮬레이터에서 응용 프로그램을 실행하려면 `cordova run android --target=<target_name>`을 사용합니다(예: `cordova run android --target="Nexus4_emulator"`).

####iOS:

   연결된 장치에서 실행하려면: `cordova run ios --device`

   기본 에뮬레이터에서 실행하려면: `cordova emulate ios`

   __참고__: `ios-sim` 패키지가 에뮬레이터에서 실행되도록 설치되어 있는지 확인합니다. 자세한 내용은 *필수 구성 요소* 섹션을 참조하세요.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

`cordova run --help`를 사용하여 추가 빌드 및 실행 옵션을 참조하세요.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient)에 제공됩니다. 이제 좀 더 자세하고 좀 더 흥미로운 시나리오를 진행해 보겠습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 Node.js Web API 보안 유지 >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->