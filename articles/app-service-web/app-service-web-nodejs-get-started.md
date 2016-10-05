<properties
	pageTitle="Azure 앱 서비스에서 Node.js 웹앱 시작"
	description="Azure 앱 서비스에서 웹앱에 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="07/01/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에서 Node.js 웹앱 시작

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 자습서에서는 간단한 [Node.js] 응용 프로그램을 만들고 cmd.exe 또는 bash와 같은 명령줄 환경에서 [Azure App Service]에 배포하는 방법을 보여줍니다. 이 자습서의 지침은 Node.js를 실행할 수 있는 모든 운영 체제를 따를 수 있습니다.

<a name="prereq"></a>
## 필수 조건

- [Node.JS]
- [Bower]
- [Yeoman]
- [Git]
- [Azure CLI]
- Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록]하거나 [Visual Studio 구독자 혜택을 활성화]할 수 있습니다.

## 간단한 Node.js 웹앱 만들기 및 배포

1. 원하는 명령줄 터미널을 열고 [Yeoman Express 생성기]를 설치합니다.

        npm install -g generator-express

2. 작업 디렉터리에 대해 `CD`하고 다음 구문을 사용하여 Express 앱을 생성합니다.

        yo express
        
    메시지가 표시되면 다음 옵션을 선택합니다.

    `? Would you like to create a new directory for your project?` **예** `? Enter directory name` **{appname}** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **없음** `? Select a database to use:` **없음** `? Select a build tool to use:` **Grunt**

3. 새 앱의 루트 디렉터리에 대해 `CD`하고 이를 시작하여 개발 환경에서 실행되는지 확인합니다.

        npm start

    브라우저에서 <http://localhost:3000>으로 이동하여 Express 홈 페이지를 볼 수 있는지 확인합니다. 앱이 제대로 실행되는지 확인했으면 `Ctrl-C`를 사용하여 중지합니다.
    
1. ASM 모드로 변경하여 Azure에 로그인합니다([Azure CLI](#prereq)가 필요함).

        azure config mode asm
        azure login

    프롬프트를 따라 Azure 구독을 보유하고 있는 Microsoft 계정을 사용하여 브라우저에 로그인을 계속합니다.

2. 앱의 루트 디렉터리에서 작업하고 있는지 확인합니다. 다음 명령을 사용하여 고유한 앱 이름을 가진 Azure의 App Service 앱 리소스를 만듭니다. 예: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    프롬프트에 따라 배포할 Azure 지역을 선택합니다. Azure 구독에 대한 Git/FTP 배포 자격 증명을 설정하지 않은 경우 배포 자격 증명을 만들라는 메시지가 표시됩니다.

3. 응용 프로그램의 루트에서 ./config/config.js 파일을 열고 프로덕션 포트를 `process.env.port`로 변경합니다. `config` 개체의 `production` 속성은 다음 예제와 같아야 합니다.

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    이를 통해 Node.js 앱이 iisnode가 수신 대기하는 기본 포트에서 웹 요청에 응답합니다.
    
4. ./package.json을 열고 `engines` 속성을 추가하여 [원하는 Node.js 버전을 지정](#version)합니다.

        "engines": {
            "node": "6.6.0"
        }, 

4. 변경 내용을 저장한 후 git를 사용하여 앱을 Azure에 배포합니다.

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Express 생성기가 .gitignore 파일을 이미 제공하므로 `git push`가 node\_modules/ 디렉터리로 업로드를 시도하는 대역폭을 사용하지 않습니다.

5. 마지막으로 브라우저에서 라이브 Azure 앱을 시작합니다.

        azure site browse

    이제 Azure 앱 서비스에서 라이브로 실행되는 Node.js 웹앱이 표시됩니다.
    
    ![배포된 응용 프로그램에 대한 검색의 예제][deployed-express-app]

## Node.js 웹앱 업데이트

앱 서비스에서 실행되는 Node.js 웹앱을 업데이트하려면 웹앱을 처음 배포할 때처럼 `git add`, `git commit` 및 `git push`을 실행합니다.
     
## 앱 서비스에서 Node.js 앱을 배포하는 방법

Azure 앱 서비스는 [iisnode]를 사용하여 Node.js 앱을 실행합니다. Azure CLI 및 Kudu 엔진(Git 배포)은 함께 작동하여 명령줄에서 Node.js 앱을 개발 및 배포할 때 능률적인 환경을 제공합니다.

- `azure site create --git`은 server.js 또는 app.js의 일반적인 Node.js 패턴을 인식하고 루트 디렉터리에 iisnode.yml을 생성합니다. 이 파일을 사용하여 iisnode를 사용자 지정할 수 있습니다.
- `git push azure master`에서 Kudu는 다음 배포 작업을 자동화합니다.

    - package.json이 리포지토리 루트에 있는 경우 `npm install --production`을 실행합니다.
    - package.json의 시작 스크립트를 가리키는 iisnode에 대한 Web.config를 생성합니다(예: server.js 또는 app.js).
    - Node-inspector로 디버깅을 위해 앱을 준비하는 데 Web.config를 사용자 지정합니다.
    
## Node.js 프레임워크 사용

앱을 개발하기 위해 [Sails.js][SAILSJS] 또는 [MEAN.js][MEANJS]와 같이 널리 사용되는 Node.js 프레임워크를 사용하는 경우 앱 서비스에 배포할 수 있습니다. 널리 사용되는 Node.js 프레임워크에는 특정 쿼크가 있고 해당 패키지 종속성은 계속 업데이트됩니다. 그러나 App Service에서는 stdout 및 stderr 로그가 제공되므로 앱으로 수행하는 작업을 정확히 알고 적절히 변경할 수 있습니다. 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](#iisnodelog)를 참조하세요.

다음 자습서에서는 앱 서비스에서 특정 프레임워크를 사용하는 방법을 보여 줍니다.

- [Azure 앱 서비스에 Sails.js 웹앱 배포]
- [Azure 앱 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 만들기]
- [Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법]

<a name="version"></a>
## 특정 Node.js 엔진 사용

일반적인 워크플로에서는 package.json에서 일반적으로 하는 것처럼 특정 Node.js 엔진을 사용하도록 App Service에 지시합니다. 예:

    "engines": {
        "node": "6.6.0"
    }, 

Kudu 배포 엔진은 다음 순서로 사용할 Node.js 엔진을 결정합니다.

- 먼저 iisnode.yml에서 `nodeProcessCommandLine`이 지정되었는지 확인합니다. 지정된 경우 사용합니다.
- 다음으로 package.json에서 `engines` 개체에 `"node": "..."`가 지정되었는지 확인합니다. 지정된 경우 사용합니다.
- 기본적으로 기본 Node.js 버전을 선택합니다.

>[AZURE.NOTE] 원하는 Node.js 엔진을 명시적으로 정의하는 것이 좋습니다. 기본 Node.js 버전을 변경하면 기본 Node.js 버전이 사용자의 앱에 적절하지 않기 때문에 Azure 웹앱에서 오류가 발생할 수 있습니다.

<a name="iisnodelog"></a>
## iisnode에서 stdout 및 stderr 로그 가져오기

iisnode 로그를 읽으려면 다음 단계를 수행하세요.

> [AZURE.NOTE] 다음 단계를 완료한 후에 오류가 발생할 때까지 로그 파일이 존재하지 않을 수 있습니다.

1. Azure CLI에서 제공하는 iisnode.yml 파일을 엽니다.

2. 이때 다음 두 매개 변수를 지정합니다.

        loggingEnabled: true
        logDirectory: iisnode
    
    함께 stdout 및 stderror 출력을 D:\\home\\site\\wwwroot**iisnode** 디렉터리에서 배치하도록 앱 서비스의 iisnode에 지시합니다.

3. 변경 내용을 저장한 후 다음 Git 명령을 사용하여 Azure에 푸시합니다.

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    이제 iisnode가 구성됩니다. 다음 단계에서는 이러한 로그에 액세스하는 방법을 보여 줍니다.
     
4. 브라우저에서 다음과 같이 앱에 대한 Kudu 디버그 콘솔에 액세스합니다.

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    "*.scm.*"이 DNS 이름에 추가되어 이 URL은 웹앱 URL과 다릅니다. URL에 해당 추가를 생락하면 404 오류가 발생하게 됩니다.

5. D:\\home\\site\\wwwroot\\iisnode로 이동합니다.

    ![iisnode 로그 파일의 위치 탐색][iislog-kudu-console-find]

6. 읽을 로그에 대한 **편집** 아이콘을 클릭합니다. 원하는 경우 **다운로드** 또는 **삭제**를 클릭할 수도 있습니다.

    ![iisnode 로그 파일 열기][iislog-kudu-console-open]

    이제 앱 서비스 배포를 디버깅할 수 있도록 로그를 볼 수 있습니다.
    
    ![iisnode 로그 파일 검사][iislog-kudu-console-read]

## Node-Inspector로 앱 디버그

Node-Inspector를 사용하여 Node.js 앱을 디버깅하는 경우 라이브 앱 서비스 앱에 이를 사용할 수 있습니다. Node-Inspector는 앱 서비스에 대한 iisnode 설치에 미리 설치되어 있습니다. Git을 통해 배포하는 경우 Kudu에서 자동 생성된 Web.config에는 Node-Inspector를 사용하는 데 필요한 모든 구성이 이미 들어 있습니다.

Node-Inspector를 사용하려면 다음 단계를 수행하세요.

1. 리포지토리 루트에서 iisnode.yml을 열고 다음 매개 변수를 지정합니다.

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. 변경 내용을 저장한 후 다음 Git 명령을 사용하여 Azure에 푸시합니다.

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. 이제 package.json의 시작 스크립트에 지정된 대로 URL에 추가된 /debug와 함께 앱의 시작 파일로 이동합니다. 예를 들면 다음과 같습니다.

        http://{appname}.azurewebsites.net/server.js/debug
    
    또는
    
        http://{appname}.azurewebsites.net/app.js/debug

## 추가 리소스

- [Azure 응용 프로그램에서 Node.js 버전 지정](../nodejs-specify-node-version-azure-apps.md)
- [Azure에서 Node.js 응용 프로그램에 대한 모범 사례 및 문제 해결 가이드](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Azure 앱 서비스에서 Node.js 웹 앱을 디버그하는 방법](web-sites-nodejs-debug.md)
- [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)
- [Azure 앱 서비스 웹앱: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js 개발자 센터](/develop/nodejs/)
- [Azure 앱 서비스에서 웹 앱 시작](app-service-web-get-started.md)
- [Super 암호 Kudu 디버그 콘솔 탐색]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Visual Studio 구독자 혜택을 활성화]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Azure 앱 서비스에서 Socket.IO를 사용하여 Node.js 채팅 응용 프로그램 만들기]: ./web-sites-nodejs-chat-app-socketio.md
[Azure 앱 서비스에 Sails.js 웹앱 배포]: ./app-service-web-nodejs-sails.md
[Super 암호 Kudu 디버그 콘솔 탐색]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Yeoman Express 생성기]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Azure 앱 서비스 웹앱에서 io.js를 사용하는 방법]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[무료 평가판을 등록]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png

<!---HONumber=AcomDC_0928_2016-->