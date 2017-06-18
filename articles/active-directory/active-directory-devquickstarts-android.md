<properties
	pageTitle="Azure AD Android 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 Android 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter="android"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# Android 응용 프로그램에 Azure AD 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

데스크톱 응용 프로그램을 개발하는 경우 Azure AD를 사용하면 간단하고 편리하게 Active Directory 계정을 사용하여 사용자를 인증할 수 있습니다. 또한 응용 프로그램에서 Office 365 API 또는 Azure API와 같이 Azure AD를 통해 보호되는 웹 API를 안전하게 사용할 수 있습니다.

보호된 리소스에 액세스해야 하는 Android 클라이언트의 경우 Azure AD는 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Android To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 To-Do List API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	사용자의 할 일 목록을 가져옵니다.
-	사용자를 로그아웃합니다.

시작하려면 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트가 필요합니다. 테넌트가 아직 없는 경우 [얻는 방법을 알아보세요](active-directory-howto-tenant.md).

## 1단계: Node.js REST API TODO 샘플 서버 다운로드 및 실행

이 샘플은 Microsoft Azure Active Directory용 단일 테넌트 To-Do REST API를 빌드하기 위한 기존 샘플에서도 작동하도록 특수하게 작성되었습니다. 다음은 빠른 시작을 위한 필수 구성 요소입니다.

설정 방법에 대한 자세한 내용은 다음에서 기존 샘플을 참조하세요.

* [Node.js에 대한 Microsoft Azure Active Directory 샘플 REST API 서비스](active-directory-devquickstarts-webapi-nodejs.md)

## 2단계: Microsoft Azure AD 테넌트에 Web API 등록

**수행할 작업**

*Microsoft Active Directory는 두 가지 유형의 응용 프로그램 추가를 지원합니다. 하나는 사용자에게 서비스를 제공하는 Web API이고, 다른 하나는 해당 Web API에 액세스하는 응용 프로그램(웹 또는 장치에서 실행 중인 응용 프로그램)입니다. 이 단계에서는 이 샘플을 테스트하기 위해 로컬로 실행하는 Web API를 등록할 것입니다. 일반적으로 이 Web API는 앱에서 액세스할 기능을 제공하는 REST 서비스가 됩니다. Microsoft Azure Active Directory는 모든 끝점을 보호할 수 있습니다.*

*여기서는 위에 참조된 TODO REST API를 등록하게 되지만 Azure Active Directory를 통해 보호하려는 어떤 Web API도 마찬가지로 등록할 수 있습니다.*

Microsoft Azure AD에 Web API를 등록하는 단계

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 Active Directory를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 디렉터리 테넌트를 클릭합니다.
4. 응용 프로그램 탭을 클릭합니다.
5. 서랍에서 추가를 클릭합니다.
6. “내 조직에서 개발 중인 응용 프로그램 추가”를 클릭합니다.
7. 응용 프로그램의 이름(예: "TodoListService")을 입력하고 “웹 응용 프로그램 및/또는 Web API”를 선택한 후 다음을 클릭합니다.
8. 로그온 URL로는 샘플의 기준 URL(기본적으로 `https://localhost:8080`임)을 입력합니다.
9. 앱 ID URI로는 `https://<your_tenant_name>/TodoListService`를 입력하고 `<your_tenant_name>`을 해당 Azure AD 테넌트 이름으로 바꿉니다. 등록을 완료하려면 확인을 클릭합니다.
10. 계속 Azure 포털에 있는 상태에서 응용 프로그램의 구성 탭을 클릭합니다.
11. **클라이언트 ID 값을 찾아 따로 복사합니다.** 나중에 응용 프로그램을 구성할 때 필요합니다.

## 3단계: 샘플 Android 네이티브 클라이언트 응용 프로그램 등록

웹 응용 프로그램을 등록하는 것이 첫 번째 단계입니다. 다음으로 Azure Active Directory에 응용 프로그램에 대해 알려야 합니다. 이렇게 하면 응용 프로그램이 방금 등록한 Web API와 통신할 수 있습니다.

**수행할 작업**

*앞서 설명한 것처럼 Microsoft Azure Active Directory는 두 가지 유형의 응용 프로그램 추가를 지원합니다. 하나는 사용자에게 서비스를 제공하는 Web API이고, 다른 하나는 해당 Web API에 액세스하는 응용 프로그램(웹 또는 장치에서 실행 중인 응용 프로그램)입니다. 이 단계에서는 이 샘플에 응용 프로그램을 등록합니다. 이 응용 프로그램이 방금 등록한 Web API에 대한 액세스를 요청하려면 이 작업을 수행해야 합니다. 등록되지 않은 응용 프로그램의 경우 로그인을 요청하더라도 Azure Active Directory에서 거부합니다. 이것은 모델 보안의 한 부분입니다.*

*여기서는 위에 참조된 이 샘플 응용 프로그램을 등록하게 되지만 개발하는 어떤 앱도 등록할 수 있습니다.*

**한 테넌트에 응용 프로그램과 Web API를 모두 배치하는 이유는 무엇인가요?**

*짐작할 수에 있는 것처럼 다른 테넌트에서 Azure Active Directory에 등록된 외부 API에 액세스하는 응용 프로그램을 빌드할 수 있습니다. 이렇게 하면 응용 프로그램에서 API 사용에 동의할지 묻는 메시지가 표시됩니다. 다행인 것은 iOS용 Active Directory 인증 라이브러리가 자동으로 동의 과정을 진행한다는 것입니다. 고급 기능을 좀 더 살펴보게 되면 다른 서비스 공급자는 물론 Azure 및 Office에서 Microsoft API 모음에 액세스하는 데 필요한 작업의 중요한 부분이라는 것을 알 수 있게 될 것입니다. 이제 Web API와 응용 프로그램을 동일한 테넌트에서 등록했으므로 동의를 묻는 메시지는 표시되지 않습니다. 일반적으로 회사를 위해서만 응용 프로그램을 개발하는 경우가 여기에 해당합니다.*

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 왼쪽 탐색 창에서 Active Directory를 클릭합니다.
3. 샘플 응용 프로그램을 등록할 디렉터리 테넌트를 클릭합니다.
4. 응용 프로그램 탭을 클릭합니다.
5. 서랍에서 추가를 클릭합니다.
6. “내 조직에서 개발 중인 응용 프로그램 추가”를 클릭합니다.
7. 응용 프로그램의 이름(예: "TodoListClient-Android")을 입력하고 “네이티브 클라이언트 응용 프로그램”을 선택한 후 다음을 클릭합니다.
8. 리디렉션 URI로 `http://TodoListClient`를 입력합니다. 마침을 클릭합니다.
9. 응용 프로그램의 구성 탭을 클릭합니다.
10. 클라이언트 ID 값을 찾아 따로 복사합니다. 나중에 응용 프로그램을 구성할 때 필요합니다.
11. “다른 응용 프로그램에 대한 권한”에서 “응용 프로그램 추가”를 클릭합니다. “표시” 드롭다운에서 “기타”를 선택하고 위쪽 확인 표시를 클릭합니다. TodoListService를 찾아 클릭하고 아래쪽 확인 표시를 클릭하여 응용 프로그램을 추가합니다. “위임된 권한” 드롭다운에서 "Access TodoListService"를 선택하고 구성을 저장합니다.



Maven으로 빌드하려면 최상위 수준에서 pom.xml을 사용할 수 있습니다.

  * 선택한 디렉터리에 다음 리포지토리를 복제합니다.

  `$ git clone git@github.com:AzureADSamples/NativeClient-Android.git`

  * [Android용 maven 설정을 위한 필수 구성 요소 섹션](https://github.com/MSOpenTech/azure-activedirectory-library-for-android/wiki/Setting-up-maven-environment-for-Android)의 단계를 수행합니다.
  * SDK 19를 사용하여 에뮬레이터를 설치합니다.
  * 리포지토리를 복제한 루트 폴더로 이동합니다.
  * mvn clean install 명령을 실행합니다.
  * cd samples\\hello를 실행하여 빠른 시작 샘플로 디렉터리를 변경합니다.
  * mvn android:deploy android:run 명령을 실행합니다.
  * 앱이 시작되는 것을 볼 수 있습니다.
  * 테스트 사용자 자격 증명을 입력하여 사용해 보세요.

aar 패키지 외에 Jar 패키지도 제출됩니다.

### 4단계: Android ADAL을 다운로드한 후 Eclipse 작업 영역에 추가

사용자가 Android 프로젝트에서 이 라이브러리를 사용하기 위한 여러 옵션을 사용할 수 있도록 구현했습니다.

* 소스 코드를 사용하여 Eclipse에 이 라이브러리를 가져온 후 응용 프로그램에 연결할 수 있습니다.
* Android Studio를 사용하는 경우 *aar* 패키지 형식을 사용하고 이 이진 파일을 참조할 수 있습니다.

####옵션 1: 소스 Zip 가져오기

소스 코드의 복사본을 다운로드하려면 페이지 오른쪽에서 "ZIP 다운로드"를 클릭하거나 [여기](https://github.com/AzureAD/azure-activedirectory-library-for-android/archive/v1.0.9.tar.gz)를 클릭하세요.

####옵션 2: Git를 통해 소스 가져오기

git를 통해 SDK 소스 코드를 가져오려면 다음을 입력합니다.

    git clone git@github.com:AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

####옵션 3: Gradle을 통해 이진 파일 가져오기

Maven 중앙 리포지토리에서 이진 파일을 가져올 수 있습니다. AAR 패키지는 AndroidStudio의 프로젝트에 다음과 같이 포함할 수 있습니다.

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

####옵션 4: Maven 통해 aar 가져오기

Eclipse의 m2e 플러그 인을 사용하는 경우 pom.xml 파일에 대한 종속성을 지정할 수 있습니다.

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


####옵션 5: libs 폴더 안에 jar 패키지 가져오기
maven에서 리포지토리로 jar 파일을 가져와 프로젝트의 *libs* 폴더에 놓을 수 있습니다. Jar 패키지에는 필수 리소스가 들어 있지 않으므로 이러한 필수 리소스를 프로젝트에 복사해야 합니다.


### 5단계: 프로젝트에 Android ADAL에 대한 참조 추가


2. 프로젝트에 대한 참조를 추가하고 Android 라이브러리로 지정합니다. 이 작업 방법을 잘 모르는 경우 [여기를 클릭하여 자세한 내용을 확인하세요](http://developer.android.com/tools/projects/projects-eclipse.html).

3. 디버깅하기 위해 프로젝트 종속성을 프로젝트 설정에 추가합니다.

4. 다음을 포함하도록 프로젝트의 AndroidManifest.xml 파일을 업데이트합니다.

    ```Java
      <uses-permission android:name="android.permission.INTERNET" />
      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
      <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
      ....
      <application/>
    ```

7. 기본 활동에서 AuthenticationContext의 인스턴스를 만듭니다. 이 호출의 세부 정보는 이 추가 정보 파일에서 다루지 않지만 [Android 네이티브 클라이언트 샘플](https://github.com/AzureADSamples/NativeClient-Android)에서 유용한 정보를 얻을 수 있습니다. 예제는 아래와 같습니다.

    ```Java
    // Authority is in the form of https://login.windows.net/yourtenant.onmicrosoft.com
    mContext = new AuthenticationContext(MainActivity.this, authority, true); // This will use SharedPreferences as            default cache
    ```
  * 참고: mContext는 활동의 필드입니다.

8. 사용자가 자격 증명을 입력하고 권한 부여 코드를 받은 후에 AuthenticationActivity의 끝을 처리하도록 이 코드 블록을 복사합니다.

    ```Java
     @Override
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
         super.onActivityResult(requestCode, resultCode, data);
         if (mContext != null) {
             mContext.onActivityResult(requestCode, resultCode, data);
         }
     }
    ```

9. 토큰을 요청하려면 콜백을 정의합니다.

    ```Java
    private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };
    ```
10. 마지막으로 해당 콜백을 사용하여 토큰을 요청합니다.

    ```Java
     mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                    callback);
    ```

매개 변수에 대한 설명:

  * Resource는 필수 항목이며 액세스하려는 리소스를 나타냅니다.
  * Clientid는 필수 항목이며 AzureAD 포털에서 제공됩니다.
  * redirectUri를 패키지 이름으로 설정할 수 있습니다. acquireToken 호출의 경우에는 제공하지 않아도 됩니다.
  * PromptBehavior는 자격 증명을 요청하여 캐시 및 쿠키를 건너뛰는 데 도움이 됩니다.
  * 권한 부여 코드가 토큰과 교환되면 Callback이 호출됩니다.

  이 Callback이 호출되면 accesstoken, 만료 날짜 및 idtoken 정보를 포함하는 AuthenticationResult의 개체가 생성됩니다.

옵션: **acquireTokenSilent**

**acquireTokenSilent**를 호출하여 캐싱 및 토큰 새로 고침을 처리할 수 있습니다. 동기화 버전도 제공됩니다. userid가 매개 변수로 수락됩니다.

    ```java
     mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

11. **Broker**: 
  Microsoft Intune의 회사 포털 앱은 broker 구성 요소를 제공합니다. ADAL은 broker 계정을 사용하며, 사용자 계정이 하나만 만들어지면 인증자 및 개발자는 해당 계정을 건너뛰지 않도록 선택합니다. 개발자는 다음을 사용해서 broker 사용자를 건너뛸 수 있습니다.

    ```java
     AuthenticationSettings.Instance.setSkipBroker(true);
    ```

 개발자는 broker 사용을 위해 특별한 redirectUri를 등록해야 합니다. RedirectUri는 msauth://packagename/Base64UrlencodedSignature 형식입니다. "brokerRedirectPrint.ps1" 스크립트를 사용하여 앱에 대한 redirecturi를 가져오거나 API 호출 mContext.getBrokerRedirectUri를 사용할 수 있습니다. 서명은 인증서 서명과 관련이 있습니다.

 현재 broker 모델은 단일 사용자에 대한 것입니다. AuthenticationContext는 broker 사용자를 가져오기 위한 API 메서드를 제공합니다.

 ```java
 String brokerAccount =  mContext.getBrokerUser();
```
계정이 유효하면 Broker 사용자가 반환됩니다.

 앱 매니페스트에는 AccountManager 계정을 사용할 수 있는 권한이 있어야 합니다. http://developer.android.com/reference/android/accounts/AccountManager.html

 * GET_ACCOUNTS
 * USE_CREDENTIALS
 * MANAGE_ACCOUNTS


이 연습 과정을 수행하여 Azure Active Directory에 성공적으로 통합하는 데 필요한 기술과 지식을 얻을 수 있습니다. 이 작업의 추가 예제를 보려면 GitHub의 AzureADSamples/ 리포지토리를 방문하세요.

## 중요 정보

### 사용자 지정

응용 프로그램 리소스에서 라이브러리 프로젝트 리소스를 덮어쓸 수 있습니다. 앱을 빌드하는 동안 이러한 동작이 발생합니다. 이러한 이유로 인증 활동 레이아웃 원하는 방식으로 사용자 지정할 수 있습니다. ADAL이 사용하는 컨트롤의 ID(Webview)를 유지해야 합니다.

### Broker

Broker 구성 요소는 Microsoft Intune의 회사 포털 앱과 함께 전달됩니다. 계정은 계정 관리자에서 생성됩니다. 계정 형식은 "com.microsoft.workaccount"입니다. 단일 SSO 계정만 허용됩니다. 앱 중 하나에 대한 장치 챌린지를 완료한 후에 이 사용자에 대한 SSO 쿠키를 만듭니다.

### 기관 Url 및 ADFS

ADFS는 프로덕션 STS로 인식되지 않으므로 인스턴스 검색을 해제하고 AuthenticationContext 생성자에 false를 전달해야 합니다.

기관 url에는 STS 인스턴스 및 테넌트 이름이 필요합니다. https://login.windows.net/yourtenant.onmicrosoft.com

### 캐시 항목 쿼리

ADAL은 일부 간단한 캐시 쿼리 함수를 사용하여 SharedPrefrecens에서 기본 캐시를 제공합니다. 다음을 사용하여 AuthenticationContext에서 현재 캐시를 가져올 수 있습니다.
```Java
 ITokenCacheStore cache = mContext.getCache();
```
사용자 지정하려는 경우 캐시 구현을 제공할 수도 있습니다.
```Java
mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);
```

### PromptBehavior

ADAL은 프롬프트 동작을 지정하기 위한 옵션을 제공합니다. 새로 고침 토큰이 올바르지 않고 사용자 자격 증명이 필요한 경우 PromptBehavior.Auto가 UI를 팝업합니다. PromptBehavior.Always는 캐시 사용을 건너뛰고 항상 UI를 표시합니다.

### 캐시 및 새로 고침의 자동 토큰 요청

이 방법은 UI 팝업을 사용하지 않으며 활동을 요구하지 않습니다. 사용 가능한 경우 캐시에서 토큰이 반환됩니다. 토큰이 만료되면 새로 고침이 시도됩니다. 새로 고침 토큰이 만료되거나 실패하면 AuthenticationException이 반환됩니다.

    ```Java
    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );
    ```

또한 이 방법과 함께 메서드를 동기화를 호출할 수도 있습니다. callback에 대해 null을 설정하거나 acquireTokenSilentSync를 사용할 수 있습니다.

### 진단

다음은 문제를 진단하기 위한 정보의 기본 출처입니다.

+ 예외
+ 로그
+ 네트워크 추적

또한 라이브러리에 포함된 상관 관계 ID가 진단에 핵심적이라는 점도 알아두세요. ADAL을 코드의 다른 작업과 상호 연관 지으려면 요청별로 상관 관계 ID를 설정할 수 있습니다. 상관 관계 ID를 설정하지 않으면 ADAL은 임의 ID를 생성하며 모든 로그 메시지와 네트워크 호출이 해당 상관 관계 ID로 스탬프됩니다. 자체 생성된 ID는 요청마다 변경됩니다.

#### 예외

분명히 첫 번째 진단입니다. 저희는 유용한 오류 메시지를 제공하기 위해 노력하고 있습니다. 유용하지 않은 오류 메시지가 표시되면 문제를 정리한 후 알려주세요. 또한 모델 및 SDK#와 같은 장치 정보도 제공해 주세요.

#### 로그

문제를 진단하는 데 도움을 주기 위해 사용할 수 있는 로그 메시지를 생성하도록 라이브러리를 구성할 수 있습니다. 다음을 호출하여 ADAL에서 생성되는 각 로그 메시지를 전달하는 데 사용하는 콜백을 구성하도록 로깅을 구성합니다.


 ```Java
 Logger.getInstance().setExternalLogger(new ILogger() {
     @Override
     public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) {
      ...
      // You can write this to logfile depending on level or errorcode.
      writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);
     }
 }
```
아래와 같이 사용자 지정 로그 파일에 메시지를 쓸 수 있습니다. 그러나 장치에서 로그를 얻는 표준 방법은 없습니다. 이 작업에 도움이 되는 몇 가지 서비스가 있습니다. 또한 서버에 파일을 보내는 것과 같은 자체 방법을 개발할 수도 있습니다.

```Java
private syncronized void writeToLogFile(Context ctx, String msg) {
       File directory = ctx.getDir(ctx.getPackageName(), Context.MODE_PRIVATE);
       File logFile = new File(directory, "logfile");
       FileOutputStream outputStream = new FileOutputStream(logFile, true);
       OutputStreamWriter osw = new OutputStreamWriter(outputStream);
       osw.write(msg);
       osw.flush();
       osw.close();
}
```

##### 로깅 수준

+ 오류(예외)
+ 경고(경고)
+ 정보(정보 제공용)
+ 자세한 정보 표시(추가 정보)

다음과 같이 로그 수준을 설정할 수 있습니다.
```Java
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);
 ```

 모든 로그 메시지는 사용자 지정 로그 콜백 외에 logcat으로도 전송됩니다. 표시된 belog처럼 로그를 파일 형식 logcat으로 가져올 수 있습니다.

 ```
  adb logcat > "C:\logmsg\logfile.txt"
```
adb cmds에 대한 추가 예제: https://developer.android.com/tools/debugging/debugging-log.html#startingLogcat

#### 네트워크 추적

ADAL이 생성하는 HTTP 트래픽을 캡처하기 위해 다양한 도구를 사용할 수 있습니다. OAuth 프로토콜을 잘 알고 있거나 Microsoft 또는 기타 지원 채널에 진단 정보를 제공해야 할 경우에 이러한 도구를 사용하면 아주 유용합니다.

Fiddler는 가장 쉬운 HTTP 추적 도구입니다. 다음 링크에서 ADAL 네트워크 트래픽을 올바르게 기록하도록 설정할 수 있습니다. 편리한 작업을 위해 fiddler 또는 Charles와 같은 다른 도구가 암호화되지 않은 SSL 트래픽을 기록하도록 구성해야 할 수 있습니다. 참고: 이 방식으로 생성된 추적에는 액세스 토큰, 사용자 이름 및 암호와 같은 높은 권한이 필요한 정보가 포함될 수 있습니다. 프로덕션 계정을 사용하는 경우 이러한 추적 정보를 제3자와 공유하지 않도록 하세요. 지원을 얻기 위해 누군가에게 추적 정보를 제공해야 하는 경우 공유해도 괜찮은 사용자 이름 및 암호를 사용한 임시 계정으로 문제를 재현하도록 합니다.

+ [Android용 Fiddler 설정(영문)](http://docs.telerik.com/fiddler/configure-fiddler/tasks/ConfigureForAndroid)
+ [ADAL에 대한 Fiddler 규칙 구성(영문)](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/How-to-listen-to-httpUrlConnection-in-Android-app-from-Fiddler)


### 대화 상자 모드
활동 없는 acquireToken 메서드는 대화 상자 프롬프트를 지원합니다.

### 암호화

ADAL은 기본적으로 토큰을 암호화한 후 SharedPreferences에 저장합니다. StorageHelper 클래스를 확인하여 세부 정보를 볼 수 있습니다. Android에서는 개인 키의 보안 저장소인 4.3(API18)용 AndroidKeyStore를 도입했습니다. ADAL은 API18 이상에 이 저장소를 사용합니다. 더 낮은 SDK 버전에 ADAL을 사용하려는 경우 AuthenticationSettings.INSTANCE.setSecretKey에서 비밀 키를 제공해야 합니다.

### Oauth2 전달자 챌린지

AuthenticationParameters 클래스는 authorization\_uri Oauth2 전달자 챌린지를 가져오기 위한 기능을 제공합니다.

### Webview의 세션 쿠키

앱이 닫힌 후에 Android webview가 세션 쿠키를 지우지 않습니다. 아래 샘플 코드로 이 문제를 처리할 수 있습니다.
```java
CookieSyncManager.createInstance(getApplicationContext());
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.removeSessionCookie();
CookieSyncManager.getInstance().sync();
```
쿠키에 대한 추가 정보: http://developer.android.com/reference/android/webkit/CookieSyncManager.html

### 리소스 재정의

ADAL 라이브러리에는 다음 두 ProgressDialog 메시지에 대한 영어 문자열이 포함되어 있습니다.

지역화된 문자열이 적합한 경우 응용 프로그램이 덮어씁니다.

```Java
<string name="app_loading">Loading...</string>
<string name="broker_processing">Broker is processing</string>
<string name="http_auth_dialog_username">Username</string>
<string name="http_auth_dialog_password">Password</string>
<string name="http_auth_dialog_title">Sign In</string>
<string name="http_auth_dialog_login">Login</string>
<string name="http_auth_dialog_cancel">Cancel</string>
```

=======

### NTLM 대화 상자
Adal 버전 1.1.0은 WebViewClient의 onReceivedHttpAuthRequest 이벤트를 통해 처리되는 NTLM 대화 상자를 지원합니다. 대화 상자 레이아웃 및 문자열은 사용자 지정할 수 있습니다.

### 앱 간 SSO
[ADAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법](active-directory-sso-android.md) 알아보기


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->