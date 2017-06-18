<properties
	pageTitle="Azure AD v2.0 NodeJS 웹 API | Microsoft Azure"
	description="개인 Microsoft 계정과 회사 또는 학교 계정 둘 다의 토큰을 허용하는 NodeJS Web API를 빌드하는 방법입니다."
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

# node.js를 사용하여 Web API 보안 유지

> [AZURE.NOTE]
	일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.

Azure Active Directory v2.0 끝점을 사용하면 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 액세스 토큰을 사용하여 Web API를 보호함으로써 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다를 가진 사용자가 Web API에 안전하게 액세스할 수 있도록 합니다.

**Passport**는 Node.js에 대한 인증 미들웨어입니다. 매우 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Microsoft Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Microsoft Azure Active Directory `passport-azure-ad` 플러그 인을 추가하겠습니다.

## 다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

전체 응용 프로그램은 이 자습서 마지막 부분에서도 제공됩니다.


## 1. 앱 등록
[apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새 앱을 만들거나 다음 [자세한 단계](active-directory-v2-app-registration.md)를 따르십시오. 다음을 수행해야 합니다.

- 곧 필요하게 되므로 앱에 할당된 **응용 프로그램 ID**를 적어둡니다.
- 앱용 **Mobile** 플랫폼을 추가합니다.
- 포털에서 **Redirect URI**를 복사합니다. `urn:ietf:wg:oauth:2.0:oob`의 기본값을 사용해야 합니다.


## 2: 사용자 플랫폼을 위한 node.js 다운로드
이 샘플을 사용하려면 작동하는 Node.js 설치가 있어야 합니다.

[http://nodejs.org](http://nodejs.org)에서 Node.js를 설치합니다.

## 3: 사용 중인 플랫폼에 MongoDB 설치

이 샘플을 사용하려면 작동하는 MongoDB 설치가 있어야 합니다. MongoDB를 사용하여 REST API가 서버 인스턴스 간에 지속되도록 할 것입니다.

[http://mongodb.org](http://www.mongodb.org)에서 MongoDB를 설치합니다.

> [AZURE.NOTE] 이 연습에서는 연습 과정을 작성할 때의 MongoDB에 대한 기본 설치 및 서버 끝점인 mongodb://localhost을(를) 사용한다고 가정합니다.

## 4: Web API에 Restify 모듈 설치

Resitfy를 사용하여 REST API를 작성할 것입니다. Restify는 Connect 위에 REST API를 구축하기 위한 강력한 기능 집합이 있는 Express에서 파생된 유연한 최소 Node.js 응용 프로그램 프레임워크입니다.

### Restify 설치

명령줄에서 디렉터리를 azuread 디렉터리로 변경합니다. **azuread** 디렉터리가 없으면 만듭니다.

`cd azuread` - 또는- `mkdir azuread;`

다음 명령을 입력합니다.

`npm install restify`

이 명령은 Restify를 설치합니다.

#### 오류가 발생했나요?

일부 운영 체제에서 rpm을 사용할 때 Error: EPERM, chmod '/usr/local/bin/..' 오류 메시지와 관리자 계정으로 실행하라는 요청이 표시될 수 있습니다. 이런 경우 sudo 명령을 사용하여 더 높은 권한 수준으로 npm을 실행하세요.

#### DTrace 관련 오류가 발생했나요?

Restify를 설치할 때 다음과 같은 내용이 표시될 수 있습니다.

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify는 DTrace를 사용하여 REST 호출을 추적하는 강력한 메커니즘을 제공합니다. 그러나 대부분의 운영 체제에서는 DTrace를 사용할 수 없습니다. 이러한 오류는 무시해도 됩니다.


이 명령의 출력은 다음과 유사합니다.


	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0(mv@0.0.5)


## 5: Web API에 Passport.js 설치

[Passport](http://passportjs.org/)는 Node.js에 대한 인증 미들웨어입니다. 매우 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Azure Active Directory 전략 플러그 인을 추가해 보겠습니다.

명령줄에서 디렉터리를 azuread 디렉터리로 변경합니다.

다음 명령을 입력하여 passport.js를 설치합니다.

`npm install passport`

이 명령의 출력은 다음과 유사하게 표시됩니다.

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 6: Web API에 Passport-Azure-AD 추가

Passport와 Azure Active Directory를 연결하는 전략 모음인 passport-azuread를 사용하여 OAuth 전략을 추가하겠습니다. 이 Rest API 샘플에서는 이 전략을 전달자 토큰으로 사용합니다.

> [AZURE.NOTE] OAuth2는 알려진 모든 토큰 유형이 발급될 수 있는 프레임워크를 제공하지만 특정 토큰 유형만 일반적으로 사용할 수 있습니다. 끝점 보호를 위해 이러한 토큰 유형은 전달자 토큰으로 확인되었습니다. 전달자 토큰은 OAuth2에서 가장 널리 발급되는 토큰 유형이며 많은 구현에서는 발급되는 유일한 토큰 유형이 전달자 토큰이라고 가정합니다.

명령줄에서 디렉터리를 azuread 디렉터리로 변경합니다.

다음 명령을 입력하여 Passport.js passport-azure-ad 모듈을 설치합니다.

`npm install passport-azure-ad`

이 명령의 출력은 다음과 유사하게 표시됩니다.

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## 7: Web API에 MongoDB 모듈 추가

MongoDB를 데이터 저장소로 사용할 예정입니다. 따라서 널리 사용되는 두 플러그 인을 설치해야 합니다. 하나는 모델 및 스키마를 관리하기 위한 Mongoose 플러그 인이고 다른 하나는 MongoDB용 데이터베이스 드라이버인 MongoDB 플러그 인입니다.


* `npm install mongoose`
* `npm install mongodb`

## 8: 추가 모듈 설치

다음에는 나머지 필수 모듈을 설치합니다.


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`


다음 명령을 입력하여 node\_modules 디렉터리에 다음 모듈을 설치합니다.

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 9: 종속성을 적용하여 server.js 만들기

server.js 파일이 Web API 서버의 기능 대부분을 제공하게 됩니다. 이 파일에 대부분의 코드를 추가할 것입니다. 프로덕션 목적으로, 기능을 별도 경로 및 컨트롤러와 같은 좀 더 작은 파일로 리팩터링합니다. 이 데모의 목적에 맞게 이 기능을 위해 server.js를 사용합니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 만들고 다음 정보를 추가합니다.

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

파일을 저장합니다. 잠시 후에 이 파일로 다시 돌아갈 것입니다.

## 10: Azure AD 설정을 저장하기 위한 구성 파일 만들기

이 코드 파일은 Azure Active Directory 포털의 구성 매개 변수를 Passport.js에 전달합니다. 이 연습의 첫 번째 부분에서 포털에 Web API를 추가했을 때 이러한 구성 값을 만들었습니다. 코드를 복사한 후에 이러한 매개 변수 값에 추가할 항목에 대해 설명할 것입니다.


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `config.js` 파일을 만들고 다음 정보를 추가합니다.

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### 필요한 값

*IdentityMetadata*: passport-azure-ad는 여기서 IdP에 대한 구성 데이터와 JWT 토큰의 유효성을 검사할 키를 찾습니다. Azure Active Directory를 사용하는 경우 이 값을 변경하지 않는 것이 좋습니다.

*audience*: 포털에서의 리디렉션 URI입니다.

> [AZURE.NOTE]
키는 자주 롤링됩니다. 항상 "openid\_keys" URL에서 끌어오고 있으며 앱에서 인터넷에 액세스할 수 있는지 확인하세요.


## 11: server.js 파일에 구성 추가

응용 프로그램에서 방금 만든 구성 파일로부터 이러한 값을 읽어야 합니다. 이를 위해 응용 프로그램에 필수 리소스로 .config 파일을 추가하고 전역 변수를 config.js 문서의 변수로 설정합니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 다음 정보를 추가합니다.

```Javascript
var config = require('./config');
```
그 다음에 다음 코드를 사용하여 `server.js`에 새 섹션을 추가합니다.

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## 12단계: Moongoose를 사용하여 MongoDB 모델 및 스키마 정보 추가

이제 이러한 세 개의 파일이 하나의 REST API 서비스에 통합되었으므로 이러한 모든 준비 과정을 마무리할 때가 되었습니다.

이 연습에서는 ***4단계***에 설명된 것처럼 MongoDB를 사용하여 작업을 저장할 것입니다.

11단계에서 만든 config.js 파일에서 기억할 수 있는 것처럼 mogoose\_auth\_local 연결 URL의 끝에 추가했던 데이터베이스 *tasklist*를 호출했습니다. 서버 응용 프로그램을 처음 실행할 때 생성되므로(존재하지 않을 경우) MongoDB에서 이 데이터베이스를 미리 만들 필요는 없습니다.

사용하려는 MongoDB 데이터베이스를 서버에 알렸으므로 일부 추가 코드를 작성하여 서버 작업에 대한 모델 및 스키마를 만들어야 합니다.

#### 모델에 대한 논의

스키마 모델은 매우 간단하며 필요에 따라 확장할 수 있습니다.

NAME - 작업에 할당된 사람의 이름입니다. ***String***

TASK - 작업 자체입니다. ***String***

DATE - 작업이 만료되는 날짜입니다. ***DATETIME***

COMPLETED - 작업이 완료되었는지 여부입니다. ***BOOLEAN***

#### 코드에 스키마 만들기


아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 구성 항목 아래에 다음 정보를 추가합니다.

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
이렇게 하면 MongoDB 서버에 연결되고 스키마 개체가 다시 전달됩니다.

#### 스키마를 사용하여 코드에서 모델 만들기

위에서 작성한 코드 아래에 다음 코드를 추가합니다.

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
코드에서 알 수 있듯이 스키마를 만든 다음, ***Routes***를 정의할 때 코드 전체에서 데이터를 저장하는 데 사용할 모델 개체를 만듭니다.

## 13단계: Task REST API 서버에 대한 경로 추가

작업할 데이터베이스 모델이 준비되었으므로 REST API 서버에 사용할 경로를 추가하겠습니다.

### Restify의 경로 정보

경로는 Express 스택을 사용할 때와 정확히 동일한 방식으로 Restify에서 작동합니다. 클라이언트 응용 프로그램이 호출할 것으로 예상하는 URI를 사용하여 경로를 정의합니다. 일반적으로 경로는 별도 파일에 정의합니다. 여기서는 경로를 server.js 파일에 저장할 것입니다. 프로덕션 사용을 위해서는 자체 파일에서 이러한 경로를 팩터링하는 것이 좋습니다.

Restify 경로의 일반적인 패턴:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


가장 기본적인 수준의 패턴입니다. Resitfy(및 Express)는 응용 프로그램 유형 정의 및 여러 다른 끝점에서 복잡한 라우팅 수행 등의 훨씬 더 수준 높은 기능을 제공합니다. 여기서는 이러한 경로를 매우 간단하게 유지할 것입니다.

#### 서버에 기본 경로 추가

이제 만들기, 검색, 업데이트 및 삭제의 기본 CRUD 경로를 추가할 것입니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

즐겨 사용하는 편집기에서 `server.js` 파일을 열고 위에서 작성한 데이터베이스 항목 아래에 다음 정보를 추가합니다.

```Javascript
/**
*
* APIs for our REST Task server
*/
// Create a task
function createTask(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

### 경로에 대한 일부 오류 처리 추가

발생한 문제를 클라이언트가 이해할 수 있는 방식으로 다시 전달할 수 있도록 일부 오류 처리를 추가할 수 있습니다.

위에서 작성한 코드 아래에 다음 코드를 추가합니다.

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## 14단계: 서버 만들기

데이터베이스를 정의했으며 경로가 올바르게 지정되었으므로 마지막으로 호출을 관리하는 서버 인스턴스를 추가할 것입니다.

Restify(및 Express)는 REST API 서버에 대해 사용자가 수행할 수 있는 고급 사용자 지정 기능을 많이 제공하지만 여기서는 가장 기본적인 설정만 사용할 것입니다.

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
version: "2.0.1"
});
// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## 15: 경로 추가(지금은 인증 없이)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## 16: OAuth 지원을 추가하기 전에 서버를 실행하겠습니다.

인증을 추가하기 전에 서버 테스트

이를 위한 가장 쉬운 방법은 명령줄에서 durl을 사용하는 것입니다. 이 작업을 수행하기 전에 JSON처럼 출력을 구문 분석할 수 있도록 하는 간단한 유틸리티가 필요합니다. 이를 위해 json 도구를 설치합니다. 이 도구는 아래의 모든 예제에서 사용됩니다.

`$npm install -g jsontool`

이렇게 하면 JSON 도구가 전역적으로 설치됩니다. 이 작업은 완료했으므로 서버 작업을 진행하겠습니다.

먼저 monogoDB 인스턴스가 실행되고 있는지 확인합니다.

`$sudo mongod`

그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

그런 다음 다음과 같은 방식으로 작업을 추가할 수 있습니다.

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

응답은 다음과 같습니다.

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
또한 다음 방식으로 Brandon에 대한 작업을 나열할 수 있습니다.

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

이러한 모든 작업이 수행되면 REST API 서버에 OAuth를 추가할 준비가 완료된 것입니다.

**MongoDB를 사용하는 REST API 서버가 완료되었습니다.**

## 17: REST API 서버에 인증 추가

작동하는 REST API를 만들었으므로 이제 Azure AD에서 유용하게 사용할 수 있게 만들어 보겠습니다.

아직 디렉터리를 변경하지 않았으면 명령줄에서 디렉터리를 **azuread** 폴더로 변경합니다.

`cd azuread`

### 1: passport-azure-ad에 포함된 oidcbearerstrategy 사용

지금까지 권한 부여 없이 일반적인 REST TODO 서버를 빌드했습니다. 이 지점에서 통합 작업을 시작합니다.

먼저, Passport를 사용하도록 지정해야 합니다. 다른 서버 구성 바로 뒤에 이 코드를 추가합니다.

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
API를 작성하는 경우 항상 사용자가 스푸핑할 수 없는 토큰의 고유한 항목에 데이터를 연결해야 합니다. 이 서버는 TODO 항목을 저장할 때 "owner" 필드에 넣은 토큰의 사용자 구독 ID(token.sub라고 함)를 기준으로 항목을 저장합니다. 이렇게 하면 해당 사용자만 자신의 TODO에 액세스할 수 있고 다른 사용자는 입력된 TODO에 액세스할 수 없습니다. API에 "owner"가 노출되지 않으므로 외부 사용자가 인증된 경우에도 다른 사용자의 TODO를 요청할 수 있습니다.

다음에는 passport-azure-ad에 포함된 Open ID Connect 전달자 전략을 사용하겠습니다. 지금은 코드만 살펴보고 곧 설명하겠습니다. 위에서 붙여 넣은 코드 뒤에 이 코드를 넣습니다.

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retreived');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport는 모든 전략 작성자가 준수하는 유사한 패턴을 모든 전략(Twitter, Facebook 등)에 대해 사용합니다. 전략을 보면 토큰 및 완료가 매개 변수로 포함된 function()이 전달되는 것을 확인할 수 있습니다. 전략은 모든 작업을 수행한 후 돌아옵니다. 전략이 돌아오면 사용자를 저장하고 토큰을 다시 요청할 필요가 없도록 보관하려 합니다.

> [AZURE.IMPORTANT]
위 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 프로덕션 서버에서는 결정한 등록 프로세스를 먼저 통과해야만 사용자 액세스를 허용하려 합니다. 일반적으로 이는 Facebook으로 등록할 수 있도록 하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 나타나는 패턴입니다. 명령줄 프로그램이 아니라면 반환된 토큰 개체에서 메일을 추출하고 추가 정보를 입력하도록 요구할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 추가합니다.

### 2. 마지막으로 일부 끝점 보호

사용하려는 프로토콜을 통해 passport.authenticate() 호출을 지정하여 끝점을 보호합니다.

서버 코드에서 경로를 편집하여 좀더 흥미로운 작업을 수행해 보겠습니다.

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 18: 서버 응용 프로그램을 다시 실행하고 사용자를 거부하는지 확인

`curl`을 다시 사용하여 끝점에 대해 OAuth2 보호가 적용되는지 확인해 보겠습니다. 이 끝점에 대해 클라이언트 SDK 중 하나를 실행하기 전에 이 작업을 수행할 것입니다. 반환되는 헤더에는 올바른 경로를 따르고 있음을 알려줄 수 있는 충분한 정보가 포함되어야 합니다.

먼저 monogoDB 인스턴스가 실행되고 있는지 확인합니다.

	$sudo mongod

그런 다음 해당 디렉터리로 변경하고 curl을 시작합니다.

	$ cd azuread
	$ node server.js

기본 POST를 시도합니다.

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

여기서 찾아야 하는 응답은 원하던 것처럼 Passport 계층이 끝점을 인증하기 위해 리디렉션하려고 함을 나타내는 401입니다.


## 축하합니다. OAuth2를 사용하는 REST API Service가 완료되었습니다.

OAuth2 호환 클라이언트를 사용하지 않고 이 서버로 수행할 수 있는 작업은 모두 완료했습니다. 이제 추가 연습 과정을 진행해야 합니다.

Restify 및 OAuth2를 사용하여 REST API를 구현하는 방법에 대한 정보만 원한다면 지금까지 제공된 코드만으로도 서비스를 계속 개발하고 이 예제의 빌드 방법을 계속 배울 수 있습니다.

## 다음 단계

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[V 2.0 끝점을 사용하여 Node.js 웹앱의 보안 유지 >>](active-directory-v2-devquickstarts-node-web.md)

추가 리소스는 다음을 확인해보세요.
- [개발자 가이드 v2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active-directory" 태그 >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## 당사 제품에 대한 보안 업데이트 가져오기

[이 페이지](https://technet.microsoft.com/security/dd252948)를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->