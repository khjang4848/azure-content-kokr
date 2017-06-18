<properties
	pageTitle="Azure AD iOS 시작 | Microsoft Azure"
	description="로그인을 위해 Azure AD와 통합되고 OAuth를 사용하여 Azure AD로 보호되는 API를 호출하는 iOS 응용 프로그램 빌드 방법"
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# iOS 앱에 Azure AD 통합

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD는 보호된 리소스에 액세스해야 하는 iOS 클라이언트의 경우 Active Directory 인증 라이브러리 또는 ADAL을 제공합니다. ADAL의 유일한 용도는 앱이 쉽게 액세스 토큰을 가져오도록 하는 것입니다. 액세스 토큰을 얼마나 쉽게 가져올 수 있는지 보여 주기 위해 다음을 수행하는 Objective C To-Do List 응용 프로그램을 빌드해 보겠습니다.

-	[OAuth 2.0 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn645545.aspx)을 사용하여 Azure AD Graph API를 호출하기 위한 액세스 토큰을 가져옵니다.
-	지정된 별칭을 가진 사용자를 디렉터리에서 검색합니다.

완전하게 작동하는 응용 프로그램을 빌드하려면 다음 작업이 필요합니다.

2. Azure AD에 응용 프로그램을 등록합니다.
3. ADAL을 설치 및 구성합니다.
5. ADAL을 사용하여 Azure AD에서 토큰을 가져옵니다.

시작하려면 [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)하세요. 또한 사용자를 만들고 응용 프로그램을 등록할 수 있는 Azure AD 테넌트도 필요합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](active-directory-howto-tenant.md).

## *1. iOS에 대한 리디렉션 URI 결정*

특정 SSO 시나리오에서 응용 프로그램을 안전하게 시작하려면, 특정 형식으로 **리디렉션 URI**를 만들어야 합니다. 리디렉션 URI는 토큰을 요청하는 올바른 응용 프로그램에 반환하는데 사용됩니다.

리디렉션 URI에 대한 iOS 형식은 다음과 같습니다.

```
<app-scheme>://<bundle-id>
```

- 	**aap-scheme** - XCode 프로젝트에 등록됩니다. 다른 응용 프로그램이 사용자를 호출할 수 있는 방법입니다. Info.plist -> URL 형식 -> URL ID에 있을 수 있습니다. 하나 이상이 구성되어 있지 않으면 하나를 만들어야 합니다.
- 	**bundle-id** - XCode의 프로젝트 설정의 "ID" 아래에 있는 번들 ID입니다.

이 빠른 시작 코드에 대한 예는 다음과 같습니다.***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2. DirectorySearcher 응용 프로그램 등록*
앱에서 토큰을 가져올 수 있게 하려면 먼저 Azure AD 테넌트에 등록하고 Azure AD Graph API에 액세스할 수 있는 권한을 부여해야 합니다.

-	Azure 관리 포털에 로그인합니다.
-	왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
-	응용 프로그램을 등록할 테넌트를 선택합니다.
-	**응용 프로그램** 탭을 클릭하고 아래쪽 서랍에서 **추가**를 클릭합니다.
-	프롬프트에 따라 새 **네이티브 클라이언트 응용 프로그램**을 만듭니다.
    -	응용 프로그램의 **이름**은 최종 사용자에게 응용 프로그램을 설명하는 항목입니다.
    -	**리디렉션 Uri**는 Azure AD가 토큰 응답을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 위의 정보를 기반으로 응용 프로그램에 특정한 값을 입력합니다.
-	등록이 끝나면 AAD는 앱에 고유한 클라이언트 식별자를 할당합니다. 이 값은 다음 섹션에서 필요하므로 **구성** 탭에서 복사해둡니다.
- 또한 **구성** 탭에서 “다른 응용 프로그램에 대한 권한” 섹션을 찾습니다. "Azure Active Directory" 응용 프로그램의 경우 **위임된 권한**에서 **조직 디렉터리 액세스** 권한을 추가합니다. 이렇게 하면 응용 프로그램은 Graph API에서 사용자를 쿼리할 수 있습니다.

## *3. ADAL 설치 및 구성*
Azure AD에서 응용 프로그램이 있으므로 ADAL을 설치하고 ID 관련 코드를 작성할 수 있습니다. ADAL이 Azura AD와 통신할 수 있게 하려면, 앱 등록에 관한 일부 정보를 제공해야 합니다.
-	먼저 Cocapods로 DirectorySearcher 프로젝트에 ADAL을 추가하여 시작합니다.

```
$ vi Podfile
```
이 podfile에 다음을 추가합니다.

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

이제 cocoapods를 사용하여 podfile를 로드합니다. 로드하려는 새 XCode 작업 영역을 만듭니다.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-	빠른 시작 프로젝트에서.plist 파일`settings.plist`을 엽니다. Azure 포털에 입력한 값을 반영하도록 섹션의 요소 값을 바꿉니다. 코드는 ADAL을 사용할 때마다 이러한 값을 참조합니다.
    -	`tenant`는 Azure AD 테넌트의 도메인(예: contoso.onmicrosoft.com)입니다.
    -	`clientId`는 포털에서 복사한 응용 프로그램의 clientId여야 합니다.
    -	`redirectUri`는 포털에 등록한 리디렉션 url입니다.

## *4. ADAL을 사용하여 AAD에서 토큰을 가져오기*
ADAL에서 확인되는 기본 원칙은 액세스 토큰이 필요할 때마다 앱이 completionBlock `+(void) getToken : `을 호출하고 나머지 작업은 ADAL이 수행한다는 것입니다.

-	`QuickStart` 프로젝트에서 `GraphAPICaller.m`를 열고 위쪽의 `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` 주석을 찾습니다. CompletionBlock을 통해 Azure AD와 통신하는 데 필요한 좌표를 ADAL에 전달하고 토큰 캐시 방법을 알려줍니다.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- 이제 이 토큰을 사용하여 그래프에서 사용자에 대해 검색해야 합니다. `// TODO: implement SearchUsersList` commentThis 메서드는 Azure AD Graph API에 해당 UPN이 지정된 검색어로 시작하는 사용자를 쿼리하라는 GET 요청을 만듭니다. 그렇지만 Graph API를 쿼리하려면 ADAL이 연결되는 요청의 `Authorization` 헤더에 access\_token을 포함해야 합니다.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
                 }
                 else
                 {
                     completionBlock(nil, error);
                 }

             }];
         }
     }];

}

```
- 앱이 `getToken(...)`을 호출하여 토큰을 요청하면 ADAL은 사용자에게 자격 증명을 요구하지 않고 토큰을 반환하려고 합니다. ADAL은 사용자가 토큰을 가져오기 위해 로그인해야 한다고 판단할 경우 로그인 대화 상자를 표시하고, 사용자의 자격 증명을 수집하고, 인증 성공 시 토큰을 반환합니다. 어떤 이유로든 ADAL이 토큰을 반환할 수 없는 경우 `AdalException`을 발생합니다.
- `AuthenticationResult` 개체에는 사용자 앱에 필요할 수 있는 정보를 수집하는 데 사용할 수 있는 `tokenCacheStoreItem` 개체가 포함되어 있습니다. 빠른 시작에서 `tokenCacheStoreItem`은 인증이 이미 발생한 경우를 결정하는데 사용됩니다.


## 5단계: 응용 프로그램 빌드 및 실행



축하합니다. 이제 사용자를 인증하고 OAuth 2.0을 사용하여 Web API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있는 iOS 응용 프로그램이 작성되었습니다. 아직 일부 사용자로 테넌트를 채우지 않은 경우 지금 할 수 있습니다. 빠른 시작 앱을 실행하고 해당 사용자 중 하나로 로그인합니다. 해당 UPN에 따라 다른 사용자를 검색합니다. 앱을 닫았다가 다시 실행합니다. 사용자의 세션을 그대로 유지하는 방법을 알아두세요.

ADAL은 응용 프로그램에 이러한 모든 일반적인 ID 기능을 쉽게 통합할 수 있습니다. 또한 캐시 관리, OAuth 프로토콜 지원, 사용자에게 로그인 UI 제공, 만료된 토큰 새로 고침 등의 모든 귀찮은 작업을 관리해줍니다. 실제로 알아두어야 할 모든 항목은 단일 API 호출, `getToken`입니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)에 제공됩니다.

## 추가 시나리오
이제 추가 시나리오로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

- [Azure AD를 사용하여 Node.js Web API 보안 유지](active-directory-devquickstarts-webapi-nodejs.md)
- [ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정하는 방법](active-directory-sso-ios.md) 알아보기

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->