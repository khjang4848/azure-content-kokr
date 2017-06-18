<properties
	pageTitle="Azure AD v2.0 NodeJS 웹앱 | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다로 사용자를 로그인하는 Node JS 웹앱을 빌드하는 방법입니다."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# NodeJS 웹앱에 로그인 추가


> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


여기서는 Passport를 통해 다음 작업을 수행합니다.

- Azure AD 및 v2.0 끝점을 사용하여 사용자를 앱에 로그인합니다.
- 사용자에 대한 일부 정보를 표시합니다.
- 앱에서 사용자를 로그아웃합니다.

**Passport**는 Node.js에 대한 인증 미들웨어입니다. 매우 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Microsoft Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Microsoft Azure Active Directory `passport-azure-ad` 플러그 인을 추가하겠습니다.

## 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

전체 응용 프로그램은 이 자습서 마지막 부분에서도 제공됩니다.

## 1. 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.
- 앱에 대한 **웹** 플랫폼을 추가합니다.
- 올바른 **리디렉션 URI**를 입력합니다. 리디렉션 URI는 인증 응답을 보내야 하는 Azure AD를 나타냅니다. 이 자습서에 대한 기본값은 `http://localhost:3000/auth/openid/return`입니다.

## 2. pre requisities를 디렉터리에 추가

명령줄에서 루트 폴더가 없는 경우 디렉터리를 루트 폴더로 변경하고 다음 명령을 실행합니다.

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- 또한 빠른 시작의 뼈대에 `passport-azure-ad`를 사용했습니다.

- `npm install passport-azure-ad`


이는 passport-azure-ad가 의존하는 라이브러리를 설치합니다.

## 3. passport-node-js 전략을 사용하도록 앱을 설정합니다.
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 Express 미들웨어를 구성합니다. passport는 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

-	먼저 프로젝트의 루트에서 `config.js` 파일을 열고 `exports.creds` 섹션에 앱의 구성 값을 입력합니다.
    -	`clientID:`는 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.
    -	`returnURL`은 포털에서 입력한 **리디렉션 URI**입니다.
    - `clientSecret`는 포털에서 생성한 암호입니다.

- 그 다음 프로젝트의 루트에 있는 `app.js` 파일을 열고 `passport-azure-ad`와 함께 제공되는 `OIDCStrategy` 전략을 불러오기 위해 다음 호출을 추가합니다.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- 그런 후에 방금 참조한 전략을 사용하여 로그인 요청을 처리합니다.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport는 모든 전략 작성자가 준수하는 유사한 패턴을 모든 전략(Twitter, Facebook 등)에 대해 사용합니다. 전략을 보면 토큰 및 완료가 매개 변수로 포함된 function()이 전달되는 것을 확인할 수 있습니다. 전략은 모든 작업을 수행한 후 돌아옵니다. 전략이 돌아오면 사용자를 저장하고 토큰을 다시 요청할 필요가 없도록 보관하려 합니다.

> [AZURE.IMPORTANT]
위 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 프로덕션 서버에서는 결정한 등록 프로세스를 먼저 통과해야만 사용자 액세스를 허용하려 합니다. 일반적으로 이는 Facebook으로 등록할 수 있도록 하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 나타나는 패턴입니다. 샘플 응용 프로그램이 아니라면 반환된 토큰 개체에서 메일을 추출하고 추가 정보를 입력하도록 요구할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 추가합니다.

- 다음에는 Passport에서 요구하는 대로 로그인한 사용자를 추적할 수 있는 메서드를 추가하겠습니다. 여기에는 사용자 정보의 직렬화 및 역직렬화가 포함됩니다.

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- 다음에는 Express 엔진을 로드하는 코드를 추가하겠습니다. 여기서 Express가 제공하는 기본 /views 및 /routes 패턴이 사용되는 것을 확인할 수 있습니다.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 마지막으로, 실제 로그인 요청을 `passport-azure-ad` 엔진에 전달하는 POST 경로를 추가하겠습니다.

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## 4\. Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행

이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 v2.0 끝점과 통신하도록 올바르게 구성되었습니다. `passport-azure-ad`가 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 까다로운 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인 및 로그아웃하는 방법을 알려주고 로그인한 사용자에 대한 추가 정보를 수집하기만 하면 됩니다.

- 먼저 `app.js` 파일에 기본, 로그인, 계정 및 로그아웃 메서드를 추가합니다.

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	자세히 살펴보겠습니다.
    -	`/` 경로는 요청에 사용자를 전달하여(있는 경우) index.ejs 뷰로 리디렉션됩니다.
    - `/account` 경로는 먼저 ***인증되었는지 확인***하고(아래에서 구현) 사용자에 대한 추가 정보를 얻을 수 있도록 요청에 사용자를 전달합니다.
    - `/login` 경로는 `passport-azuread`에서 azuread-openidconnect 인증자를 호출하고, 실패할 경우 사용자를 다시 /login으로 리디렉션합니다.
    - `/logout`은(는) 쿠키를 지우는 logout.ejs(및 경로)를 호출하고 사용자를 다시 index.ejs로 돌려보냅니다.


- `app.js`의 마지막 부분을 위해 위의 `/account`에서 사용된 EnsureAuthenticated 메서드를 추가하겠습니다.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- 마지막으로, `app.js`에서 실제로 서버 자체를 만들겠습니다.

```JavaScript

app.listen(3000);

```


## 5\. 웹 사이트에서 사용자에게 표시할 뷰와 경로를 빠르게 만들기

`app.js`가 완료되었습니다. 이제 얻은 정보를 사용자에게 표시할 경로와 뷰를 추가하고 만든 `/logout` 및 `/login` 경로를 처리하기만 하면 됩니다.

- 루트 디렉터리 아래에 `/routes/index.js` 경로를 만듭니다.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- 루트 디렉터리 아래에 `/routes/user.js` 경로 만들기

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

이러한 간단한 경로는 단순히 사용자(있는 경우)를 포함하여 요청을 뷰에 전달합니다.

- 루트 디렉터리 아래에 `/views/index.ejs` 뷰를 만듭니다. 이는 login 및 logout 메서드를 호출하고 계정 정보를 얻을 수 있게 해주는 간단한 페이지입니다. 요청에 전달되는 사용자가 로그인한 사용자가 있음을 입증하므로 조건부 `if (!user)`을(를) 사용할 수 있습니다.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- `passport-azuread`가 사용자 요청에 포함한 추가 정보를 볼 수 있도록 루트 디렉터리 아래에 `/views/account.ejs` 뷰를 만듭니다.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- 마지막으로, 레이아웃을 추가하여 모양을 개선합니다. 루트 디렉터리 아래에 '/views/layout.ejs' 뷰 만들기

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> |
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> |
			<a href="/account">Account</a> |
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>
```

마지막으로 앱을 빌드하고 실행합니다.

`node app.js`를 실행하고 `http://localhost:3000`으로 이동합니다.


개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인하고 /account 목록에 사용자 ID가 반영되는 방식을 확인합니다. 이제 개인 및 회사/학교 계정으로 사용자를 인증할 수 있는 업계 표준 프로토콜을 사용하여 웹앱이 보안되었습니다.

##다음 단계

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[V 2.0 끝점을 사용하여 node.js 웹앱의 보안 유지 >>](active-directory-v2-devquickstarts-node-api.md)

추가 리소스는 다음을 확인해보세요.
- [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->