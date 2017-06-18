<properties
	pageTitle="Azure 앱 서비스에 Sails.js 웹앱을 배포합니다."
	description="Azure 앱 서비스에서 Node.js 응용 프로그램을 배포하는 방법을 알아봅니다. 이 자습서에서는 Sails.js 웹앱을 배포하는 방법을 보여 줍니다."
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
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cephalin"/>

# Azure 앱 서비스에 Sails.js 웹앱을 배포합니다.

이 자습서에서는 Azure 앱 서비스에 Sails.js 앱을 배포하는 방법을 보여 줍니다. 프로세스를 통해 앱 서비스에서 실행할 Node.js 앱 구성 방법에 대한 일반적인 지식을 얻을 수 있습니다.

## 필수 조건

- [Node.js](https://nodejs.org/).
- [Sails.js](http://sailsjs.org/get-started).
- Sails.js 작업 지식. 이 자습서는 일반적으로 Sail.js의 실행과 관련된 문제를 해결하는 데 적합하지 않습니다.
- [Git](http://www.git-scm.com/downloads)
- [Azure CLI](../xplat-cli-install.md).
- Microsoft Azure 계정. 계정이 없는 경우 [무료 평가판을 등록](/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 동작 중인 Azure 앱 서비스를 확인하려면 [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751)으로 이동하세요. 여기서 신용 카드와 약정 없이 앱 서비스에서 수명이 짧은 스타터 앱을 즉시 만들 수 있습니다.

## 1단계: 개발 환경에서 Sails.js 앱 만들기

먼저 다음 단계를 수행하여 기본 Sails.js 앱을 신속하게 만듭니다.

1. 선택한 명령줄 터미널을 열고 작업 디렉터리로 `CD`합니다.

2. 새 Sails.js 앱을 만들고 실행합니다.

        sails new <appname>
        cd <appname>
        sails lift

    기본 홈 페이지(http://localhost:1377)로 이동할 수 있는지 확인합니다.

## 2단계: Azure에서 앱 서비스 앱 리소스 만들기

그런 다음 앱 서비스 앱 리소스를 만듭니다. 나중에 Sails.js 앱을 이 리소스에 배포합니다.

1. 다음과 같이 Azure에 로그인합니다.
1. 동일한 터미널에서 ASM 모드로 변경하고 Azure에 로그인합니다.

        azure config mode asm
        azure login

    프롬프트를 따라 Azure 구독을 보유하고 있는 Microsoft 계정을 사용하여 브라우저에 로그인을 계속합니다.

2. 여전히 Sails.js 프로젝트의 루트 디렉터리에 있는지 확인합니다. 다음 명령을 사용하여 Azure에 고유한 앱 이름을 가진 앱 서비스 앱 리소스를 만듭니다. 웹앱의 URL은 http://&lt;appname>.azurewebsites.net입니다.

        azure site create --git <appname>

    프롬프트에 따라 배포할 Azure 지역을 선택합니다. Azure 구독에 대한 Git/FTP 배포 자격 증명을 설정하지 않은 경우 배포 자격 증명을 만들라는 메시지가 표시됩니다.

    앱 서비스 앱 리소스를 만든 후:

    - Sails.js 앱이 Git-initialized입니다.
    - 로컬 Git-initialized 리포지토리가 Git remote로 "azure"라는 새 앱 서비스 앱에 연결되고
    - 루트 디렉터리에 iisnode.yml 파일이 만들어집니다. 이 파일을 사용하여 App Service가 Node.js 앱을 실행하는 데 사용할 [iisnode](https://github.com/tjanczuk/iisnode)를 구성할 수 있습니다.

## 3단계: Sails.js 앱 구성 및 배포

 앱 서비스에서의 Sails.js 앱 작업은 세 가지 주요 단계로 구성됩니다.

 - 앱 서비스에서 실행되도록 앱 구성
 - 앱 서비스에 배포
 - 배포 문제를 해결하기 위해 stderr 및 stdout 로그 읽기

다음 단계를 수행하세요.

1. 루트 디렉터리에서 새 iisnode.yml 파일을 열고 다음 두 줄을 추가합니다.

        loggingEnabled: true
        logDirectory: iisnode

    이제 iisnode에 대해 로깅을 사용할 수 있습니다. 이 작업 방법에 대한 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-get-started.md#iisnodelog)를 참조하세요.

2. 프로덕션 환경을 구성하기 위해 config/env/production.js를 열고 `port` 및 `hookTimeout`을 설정합니다.

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    이러한 구성 설정에 대한 설명은 [Sails.js 설명서](http://sailsjs.org/documentation/reference/configuration/sails-config)에서 확인할 수 있습니다.

    그런 다음 [Grunt](https://www.npmjs.com/package/grunt)가 Azure의 네트워크 드라이브와 호환되는지 확인해야 합니다. 버전 1.0.0 미만의 Grunt 버전은 네트워크 드라이브를 지원하지 않는 오래된 [glob](https://www.npmjs.com/package/glob) 패키지(5.0.14 이전)를 사용합니다.

3. package.json을 열고 `grunt` 버전을 `1.0.0`으로 변경한 후 모든 `grunt-*` 패키지를 제거합니다. `dependencies` 속성은 다음과 같습니다.

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

6. 변경 내용을 저장하고 변경 내용을 테스트하여 로컬에서 계속 앱이 실행되는지 확인합니다. 이 작업을 수행하려면 `node_modules` 폴더를 삭제하고 다음을 실행합니다.

        npm install
        sails lift

4. 이제 git를 사용하여 앱을 Azure에 배포합니다.

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. 마지막으로 브라우저에서 라이브 Azure 앱을 실행하면 됩니다.

        azure site browse

    이제 동일한 Sails.js 홈페이지가 나타납니다.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## 배포 문제 해결

Sails.js 응용 프로그램이 앱 서비스에서 어떤 이유로 실패하면 문제 해결을 위해 stderr 로그를 찾습니다. 자세한 내용은 [iisnode에서 stdout 및 stderr 로그 가져오기](app-service-web-nodejs-sails.md#iisnodelog)를 참조하세요. 성공적으로 시작된 경우 stdout 로그에 익숙한 메시지가 표시됩니다.

                .-..-.

    Sails              <|    .-..-.
    v0.12.3             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\a76e8111-663e-449d-956e-5c5deff2d304
    To shut down Sails, press <CTRL> + C at any time.

[config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) 파일에서 stdout 로그의 세분화 수준을 제어할 수 있습니다.

## Azure의 데이터베이스에 연결

Azure 데이터베이스에 연결하려면 Azure에 Azure SQL Database, MySQL, MongoDB, Azure (Redis) Cache 등 원하는 데이터베이스를 만들고 해당하는 [데이터 저장소 어댑터](https://github.com/balderdashy/sails#compatibility)를 사용하여 이 데이터베이스에 연결합니다. 이 섹션의 단계는 Azure SQL 데이터베이스에 연결하는 방법을 보여 줍니다.

1. 새 SQL Server에 빈 Azure SQL Database를 만들려면 [여기](../sql-database/sql-database-get-started.md)의 자습서를 따릅니다. 기본 방화벽 설정으로 Azure 서비스(예: 앱 서비스)가 이 데이터베이스에 연결할 수 있습니다.

2. 명령줄 터미널에서 SQL Server 어댑터를 설치합니다.

        npm install sails-sqlserver --save

3. config/connections.js를 열고 목록에 다음 연결 개체를 추가합니다.

        sqlserver: {
            adapter: 'sails-sqlserver',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.sqlserver, 
            database: process.env.dbname,
            options: {
                encrypt: true   // use this for Azure databases
            }
        },

4. 각 환경 변수(`process.env.*`)의 경우 App Service에서 설정해야 합니다. 이렇게 하려면 터미널에서 다음 명령을 실행합니다.

        azure site appsetting add dbuser="<database server administrator>"
        azure site appsetting add dbpassword="<database server password>"
        azure site appsetting add sqlserver="<database server name>.database.windows.net"
        azure site appsetting add dbname="<database name>"
        
    Azure 앱 설정에 설정 내용을 적용하면 중요한 데이터의 소스를 제어할 수 없게 됩니다(Git). 다음으로, 동일한 연결 정보를 사용하도록 개발 환경을 구성합니다.

4. config/local.js를 열고 다음 연결 개체를 추가합니다.

        connections: {
            sqlserver: {
                user: "<database server administrator>",
                password: "<database server password>",
                host: "<database server name>.database.windows.net", 
                database: "<database name>",
            },
        },
    
    이 구성은 config/connections.js 파일에서 로컬 환경에 대한 설정을 재정의합니다. 이 파일은 프로젝트에서 기본 .gitignore에 의해 제외되므로 Git에 저장되지 않습니다. 이제 Azure 웹앱 및 로컬 개발 환경 둘 다에서 Azure SQL 데이터베이스에 연결할 수 있습니다.

4. config/env/production.js를 열어 프로덕션 환경을 구성하고 다음 `models` 개체를 추가합니다.

        models: {
            connection: 'sqlserver',
            migrate: 'safe'
        },

4. config/env/development.js를 열어 개발 환경을 구성하고 다음 `models` 개체를 추가합니다.

        models: {
            connection: 'sqlserver',
            migrate: 'alter'
        },

    `migrate: 'alter'`를 사용하면 데이터베이스 마이그레이션 기능을 사용하여 Azure SQL Database에서 데이터베이스 테이블을 쉽게 만들고 업데이트할 수 있습니다. 그러나 Sails.js에서는 프로덕션 환경에서 `migrate: 'alter'` 사용을 허용하지 않으므로 Azure(프로덕션) 환경에 `migrate: 'safe'`가 사용됩니다([Sails.js 설명서](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings) 참조).

4. 터미널에서 평소처럼 Sails.js [청사진 API](http://sailsjs.org/documentation/concepts/blueprints)를 [생성](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate)한 다음 `sails lift`를 실행하여 Sails.js 데이터베이스 마이그레이션을 통해 데이터베이스를 만듭니다. 예:

         sails generate api mywidget
         sails lift

    이 명령에 의해 생성된 `mywidget` 모델은 비어 있으나 이 모델을 사용하여 데이터베이스에 연결되어 있다는 사실을 나타낼 수 있습니다. `sails lift`를 실행하면 앱이 사용하는 모델에 대해 누락된 테이블이 만들어집니다.

6. 브라우저에서 방금 만든 청사진 API에 액세스합니다. 예:

        http://localhost:1337/mywidget/create
    
    API가 만든 항목을 브라우저 창에 다시 반환합니다. 이것은 데이터베이스가 정상적으로 만들어졌다는 의미입니다.

        {"id":1,"createdAt":"2016-03-28T23:08:01.000Z","updatedAt":"2016-03-28T23:08:01.000Z"}

5. 이제 변경 내용을 Azure에 푸시하고, 앱으로 이동하여 계속 작동하는지 확인합니다.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Azure 웹앱의 청사진 API에 액세스합니다. 예:

        http://<appname>.azurewebsites.net/mywidget/create

    API가 다른 새 항목을 반환하는 경우 Azure 웹앱은 Azure SQL 데이터베이스에 알립니다.

## 추가 리소스

- [Azure 앱 서비스에서 Node.js 웹앱 시작](app-service-web-nodejs-get-started.md)
- [Azure 응용 프로그램에 Node.js 모듈 사용](../nodejs-use-node-modules-azure-apps.md)

<!---HONumber=AcomDC_0914_2016-->