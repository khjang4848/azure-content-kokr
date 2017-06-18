<properties
	pageTitle="Azure AD v2.0 AngularJS 시작 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정이 있는 사용자로 로그인하는 Angular JS 단일 페이지 앱을 빌드하는 방법입니다."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# AngularJS 단일 페이지 앱에 로그인 추가 - NodeJS

이 문서에서는 Azure Active Directory v2.0 끝점을 사용하여 Microsoft 지원 계정을 사용한 로그인을 AngularJS 앱에 추가합니다. v2.0 끝점을 사용하면 앱 내에서 단일 통합을 수행할 수 있으며 개인 계정과 회사/학교 계정을 모두 사용하여 사용자를 인증할 수 있습니다.

이 샘플은 Azure AD의 OAuth 전달자 토큰을 사용하여 보안이 유지되고 NodeJS로 작성된 백 엔드 REST API에 작업을 저장하는 간단한 To-Do List 단일 페이지 앱입니다. AngularJS 앱은 오픈 소스 JavaScript 인증 라이브러리 [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js)를 사용하여 전반적인 로그인 프로세스를 처리하고 REST API 호출용 토큰을 가져옵니다. 동일한 패턴이 [Microsoft Graph](https://graph.microsoft.com) 또는 Azure 리소스 관리자 API와 같은 다른 REST API에 대한 인증에 적용될 수 있습니다.

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

## 다운로드

시작하려면 [node.js](https://nodejs.org)를 다운로드해서 설치해야 합니다. 그런 다음 기본 앱을 복제하거나 [다운로드](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip)할 수 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

기본 앱은 간단한 AngularJS 앱에 대한 모든 상용구 코드를 포함하지만 ID 관련 부분은 전혀 포함하지 않습니다. 따라서 진행하지 않으려면 전체 샘플을 대신 복제하거나 [다운로드](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip)합니다.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## 앱 등록

우선 [앱 등록 포털](https://apps.dev.microsoft.com)에서 앱을 만들거나 [자세한 단계](active-directory-v2-app-registration.md)에 따라서 진행합니다. 다음을 수행해야 합니다.

- 앱에 대한 **웹** 플랫폼을 추가합니다.
- 올바른 **리디렉션 URI**를 입력합니다. 이 샘플에 대한 기본값은 `http://localhost:8080`입니다.
- **암시적 흐름 허용** 확인란을 선택한 채로 둡니다.

앱에 할당된 **응용 프로그램 ID**를 적어둡니다. 곧 이 정보가 필요합니다.

## adal.js 설치
시작하려면 다운로드한 프로젝트로 이동하여 adal.js를 설치합니다. [bower](http://bower.io/)가 설치되어 있는 경우 이 명령을 실행하기만 하면 됩니다. 종속성 버전 불일치가 있는 경우 높은 버전을 선택합니다.
```
bower install adal-angular#experimental
```

또는 [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js)와 [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js)를 수동으로 다운로드할 수 있습니다. 두 파일을 `app/lib/adal-angular-experimental/dist` 디렉터리에 추가합니다.

이제 원하는 텍스트 편집기에서 프로젝트를 열고 페이지 본문 끝에 adal.js를 로드합니다.

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## REST API 설정

설정을 진행하면서, 백 엔드 REST API가 작동하도록 하겠습니다. 명령 프롬프트에서 다음을 실행하여 필요한 모든 패키지를 설치합니다(프로젝트의 최상위 디렉터리에 있는지 확인).

```
npm install
```

이제 `config.js`를 열고 `audience` 값을 바꿉니다.

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
	 
	 ...
}
```

REST API는 이 값을 사용하여 AJAX 요청에 대해 Angular 앱으로부터 수신하는 토큰의 유효성을 검사합니다. 이 간단한 REST API는 메모리에 데이터를 저장하므로 서버를 중지할 때마다 이전에 만든 모든 작업이 손실됩니다.

이제 REST API의 작동 방식에 대한 설명을 마쳤습니다. 코드를 자유롭게 살펴보고, Azure AD를 사용한 웹 API 보안 설정에 대해 자세히 알아보려면 [이 문서](active-directory-v2-devquickstarts-node-api.md)를 참조하세요.

## 사용자 로그인
이제 ID 코드를 작성해 보겠습니다. 이미 눈치채셨겠지만, adal.js는 AngularJS 공급자를 포함하며, 이것은 Angular 라우팅 메커니즘을 잘 활용합니다. 먼저 adal 모듈을 앱에 추가합니다.

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

이제 응용 프로그램 ID로 `adalProvider`를 초기화할 수 있습니다.

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

adal.js에 앱 보안과 사용자 로그인에 필요한 모든 정보가 준비되었습니다. 앱의 특정 경로에 대해 강제 로그인을 설정하려면 코드 한 줄만 추가하면 됩니다.

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

이제 사용자가 `TodoList` 링크를 클릭하면 adal.js는 필요한 경우 로그인을 위해 Azure AD에 자동으로 리디렉션합니다. 컨트롤러에서 adal.js를 호출하여 명시적으로 로그인 및 로그아웃 요청을 보낼 수 있습니다.

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## 사용자 정보 표시
사용자가 로그인 했으니, 응용 프로그램에서 로그인한 사용자의 인증 데이터에 대한 액세스가 필요할 수 있습니다. Adal.js는 이 정보를 `userInfo` 개체에 노출합니다. 보기에서 이 개체에 액세스하려면 우선 adal.js를 해당 컨트롤러의 루트 범위에 추가합니다.

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

그런 다음 보기에서 `userInfo` 개체를 직접 처리할 수 있습니다.

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

`userInfo` 개체를 사용하여 사용자가 로그인 상태인지 또는 로그아웃 상태인지를 판단할 수 있습니다.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## REST API 호출
마지막으로 작업에 대한 생성, 읽기, 업데이트, 삭제를 위해 토큰을 가져오고 REST API를 호출하겠습니다. 무엇이 필요할까요? *아무것도* 할 필요가 없습니다. Adal.js에서 토큰 가져오기, 캐싱, 새로 고침 작업을 자동으로 처리합니다. REST API에 보내는 AJAX 요청에 토큰을 연결하는 작업도 수행합니다.

작동 원리는 바로 이렇습니다. 모두 [AngularJS 인터셉터](https://docs.angularjs.org/api/ng/service/$http) 덕분으로, adal.js에서 나가고 들어오는 http 메시지를 변환할 수 있게 해줍니다. 또한 adal.js에서는 창과 동일한 도메인에 보내는 모든 요청이 AngularJS 앱과 동일한 응용 프로그램 ID용 토큰을 사용하는 것으로 가정합니다. 이런 이유 때문에 Angular 앱과 NodeJS REST API에서 동일한 응용 프로그램 ID를 사용합니다. 물론, 필요한 경우 이러한 동작을 무시하고 adal.js에서 다른 REST API용 토큰을 가져오도록 작성할 수 있지만 이 샘플 시나리오에서는 기본 사항대로 진행하겠습니다.

아래 코드 조각은 Azure AD의 전달자 토큰을 사용하여 손쉽게 요청을 보낼 수 있는 방법을 보여 줍니다.

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

축하합니다. Azure AD 통합 단일 페이지 앱이 완성되었습니다. 수고 많으셨습니다. 이제 앱에서 사용자를 인증하고, OpenID Connect를 사용하여 백 엔드 REST API를 안전하게 호출하고, 사용자에 대한 기본 정보를 가져올 수 있습니다. 기본적으로, 개인 Microsoft 계정이나 Azure AD의 회사/학교 계정이 있는 모든 사용자를 지원합니다. 다음을 실행하여 앱을 테스트합니다.

```
node server.js
```

브라우저에서 `http://localhost:8080`으로 이동합니다. 개인 Microsoft 계정 또는 회사/학교 계정을 사용하여 로그인합니다. 사용자의 할 일 모음에 작업을 추가하고 로그아웃합니다. 다른 유형의 계정으로 로그인을 시도합니다. Azure AD 테넌트에서 회사/학교 사용자를 만들어야 하는 경우에는 [여기에서 만드는 방법을 알아봅니다(무료)](active-directory-howto-tenant.md).

v2.0 끝점에 대해 계속 알아보려면, [v2.0 개발자 가이드](active-directory-appmodel-v2-overview.md)로 돌아가세요. 추가 리소스는 다음을 확인해보세요.

- [GitHub의 Azure 샘플(영문) >>](https://github.com/Azure-Samples)
- [스택 오버플로의 Azure AD(영문) >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)의 Azure AD 설명서

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->