<properties
	pageTitle="Azure에 PHP 웹앱 만들기, 구성 및 배포"
	description="Azure 앱 서비스에서 PHP(Laravel) 웹앱을 실행하는 방법을 보여 주는 자습서입니다. Azure 앱 서비스를 구성하여 선택한 PHP 프레임워크의 요구 사항을 충족하는 방법을 알아봅니다."
	services="app-service\web"
	documentationCenter="php"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/03/2016" 
	ms.author="cephalin"/>

# Azure에 PHP 웹앱 만들기, 구성 및 배포

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

이 자습서에서는 Azure용 PHP 웹앱을 생성, 구성 및 배포하는 방법 및 Azure 앱 서비스를 구성하여 PHP 웹앱의 요구 사항을 충족하는 방법을 보여 줍니다. 자습서를 완료하면 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에 라이브로 실행 중인 [Laravel](https://www.laravel.com/) 웹앱을 만들게 됩니다.

PHP 개발자로서 Azure에 즐겨 찾는 PHP 프레임워크를 가져올 수 있습니다. 이 자습서에서는 구체적인 앱 예제로 Laravel을 단순하게 사용합니다. 다음 내용을 배웁니다.

- Git를 사용하여 배포
- PHP 버전 설정
- 루트 응용 프로그램 디렉터리에 있지 않은 시작 파일 사용
- 특정 환경 변수에 액세스
- Azure에서 앱 업데이트

다음에 배운 점을 Azure에 배포하는 다른 PHP 웹앱에 적용할 수 있습니다.

## 필수 조건

- [PHP 5.6.x](http://php.net/downloads.php) 설치(PHP 7 지원은 베타입니다)
- [작성기](https://getcomposer.org/download/) 설치
- [Azure CLI](../xplat-cli-install.md) 설치
- [Git 설치](http://www.git-scm.com/downloads)
- Microsoft Azure 계정을 가져옵니다. 계정이 없는 경우 [무료 평가판을 등록](/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.

>[AZURE.NOTE] 작업에서 웹앱을 확인합니다. [앱 서비스 체험](http://go.microsoft.com/fwlink/?LinkId=523751)에서는 신용 카드와 약정 없이 수명이 짧은 스타터 앱을 즉시 만들 수 있습니다.

## 개발 컴퓨터에 PHP (Laravel) 앱 만들기

1. 새 Windows 명령 프롬프트, PowerShell 창, Linux 셸 또는 OS X 터미널을 엽니다. 다음 명령을 실행하여 필요한 도구가 컴퓨터에 제대로 설치되어 있는지를 확인합니다.

        php --version
        composer --version
        azure --version
        git --version

    ![Azure에 PHP(Laravel) 앱을 만들기 전에 테스트 도구 설치](./media/app-service-web-php-get-started/test-tools.png)

    도구를 설치하지 않은 경우 다운로드 링크는 [필수 구성 요소](#Prerequisites)를 참조하세요.
    
2. 다음과 같이 Laravel을 설치합니다.

        composer global require "laravel/installer

3. 작업 디렉터리에 `CD`하고 새 Laravel 응용 프로그램을 다음과 같이 만듭니다.

        cd <working_directory>
        laravel new <app_name>

4. 새로 만든 `<app_name>` 디렉터리에 `CD`하고 앱을 다음과 같이 테스트합니다.

        cd <app_name>
        php artisan serve
        
    이제 브라우저의 http://localhost:8000로 이동하고 Laravel 시작 화면을 참조할 수 있어야 합니다.
    
    ![Azure에 배포하기 전에 로컬로 PHP(Laravel) 앱 테스트](./media/app-service-web-php-get-started/laravel-splash-screen.png)
    
지금까지 일반 Laravel 워크플로와 동일하며 <a href="https://laravel.com/docs/5.2" rel="nofollow">Laravel을 알아보려</a>는 과정이 아닙니다. 이동하겠습니다.

## Azure 웹앱 만들기 및 Git 배포 설정

>[AZURE.NOTE] "잠깐만요! FTP로 배포하려는 경우 어떻게 하나요?" 요구에 맞게 [FTP 자습서](web-sites-php-mysql-deploy-use-ftp.md)가 있습니다.

Azure CLI를 사용하여 Azure 앱 서비스에서 웹앱을 만들고 명령의 한 줄을 사용하여 Git 배포에 대해 등록할 수 있습니다. 수행해보겠습니다.

1. ASM 모드로 변경하고 Azure에 로그인합니다.

        azure config mode asm
        azure login
    
    로그인 프로세스를 계속하려면 도움말 메시지를 따릅니다.
    
    ![Azure에 로그인하여 Azure에 PHP(Laravel) 앱 배포](./media/app-service-web-php-get-started/log-in-to-azure-cli.png)

4. Git 배포를 사용하여 Azure 웹앱을 만들려면 명령을 실행합니다. 대화 상자가 나타나면 원하는 지역의 수를 지정합니다.

        azure site create --git <app_name>
    
    ![Azure에서 PHP (Laravel) 앱에 Azure 리소스 만들기](./media/app-service-web-php-get-started/create-site-cli.png)
    
    >[AZURE.NOTE] Azure 구독에 대한 배포 자격 증명을 전혀 설정하지 않은 경우 배포 자격 증명을 만들라는 메시지가 나타납니다. 이러한 자격 증명(Azure 계정 자격 증명 아님)은 앱 서비스에서 Git 배포 및 FTP 로그인에만 사용합니다.
    
    이 명령은 (`git init`을 사용하여) 현재 디렉터리에 새 Git 리포지토리를 만들고 (`git remote add`를 사용하여) Git 원격인 Azure의 리포지토리에 연결합니다.

<a name="configure"/>
## Azure 웹앱 구성

Azure에서 작업할 Laravel 앱의 경우 몇 가지 사항에 주의해야 합니다. 선택한 PHP 프레임워크와 비슷한 이 연습을 수행합니다.

- PHP 5.5.9 이상을 구성합니다. 서버 요구 사항의 전체 목록은 [최신 Laravel 5.2 서버 요구 사항](https://laravel.com/docs/5.2#server-requirements)을 참조하세요. 목록의 나머지는 Azure에서 PHP를 설치할 경우 이미 사용하는 확장입니다.
- 앱에 필요한 환경 변수를 설정합니다. Laravel는 환경 변수를 쉽게 설정하기 위해 `.env` 파일을 사용합니다. 그러나 원본 제어로 커밋할 수 없기 때문에([Laravel 환경 구성](https://laravel.com/docs/5.2/configuration#environment-configuration) 참조) 대신 Azure 웹앱의 앱 설정을 설정합니다.
- Laravel 앱의 항목 지점인 `public/index.php`이 먼저 로드되는지 확인합니다. [Laravel 수명 주기 개요](https://laravel.com/docs/5.2/lifecycle#lifecycle-overview)를 참조하세요. 즉, 웹앱의 루트 URL을 설정하여 `public` 디렉터리를 가리켜야 합니다.
- composer.json이 있으므로 Azure에서 작성기 확장을 사용합니다. 이런 방식으로는 `git push`을 배포하는 경우에 필요한 패키지를 가져오는 방법에 대해 작성자가 우려할 수 있습니다. 편의와 관련됩니다. 작성기 자동화를 사용하지 않는 경우 Git가 코드를 커밋 및 배포할 때 `vendor` 디렉터리에 모든 항목을 포함하도록("무시하지 않도록") `.gitignore` 파일에서 `/vendor`를 제거해야 합니다.

이러한 작업을 순서대로 구성하겠습니다.

4. Laravel 앱에 필요한 PHP 버전을 설정합니다.

        azure site set --php-version 5.6

    PHP 버전 설정을 완료했습니다!
    
4. Azure 웹앱에 새 `APP_KEY`을 생성하고 Azure 웹앱에 대한 앱 설정으로 설정합니다.

        php artisan key:generate --show
        azure site appsetting add APP_KEY="<output_of_php_artisan_key:generate_--show>"

4. 또한 알 수 없는 `Whoops, looks like something went wrong.` 페이지를 사전에 방지하기 위해 Laravel 디버깅을 사용합니다.

        azure site appsetting add APP_DEBUG=true

    환경 변수 설정을 완료했습니다!
    
    >[AZURE.NOTE] 다음에서는 약간 천천히 Laravel 수행 작업 및 Azure 용도에 대해 설명하겠습니다. Laravel은 루트 디렉터리에서 `.env` 파일을 사용하여 앱에 대한 환경 변수를 제공합니다. 여기에 `APP_DEBUG=true`(및 `APP_KEY=...`) 줄이 있습니다. 이 변수는 `'debug' => env('APP_DEBUG', false),` 코드에 의해 `config/app.php`에 액세스됩니다. [env()](https://laravel.com/docs/5.2/helpers#method-env)는 PHP [getenv()](http://php.net/manual/en/function.getenv.php)를 사용하는 Laravel 도우미 메서드입니다.
    >
    >그러나 `.env`은 루트 디렉터리에서 `.gitignore` 파일에 의해 호출되기 때문에 Git에서는 무시됩니다. 간단히 말해 로컬 Git 리포지토리의 `.env`은 파일의 나머지 부분을 사용하여 Azure에 푸시되지 않습니다. 물론, `.gitignore`에서 해당 줄을 제거할 수 있지만 원본 제어에 이 파일을 커밋하는 것을 권장하지 않도록 이미 설정했습니다. 그럼에도 불구하고 Azure에서 이러한 환경 변수를 지정하는 방법이 여전히 필요합니다.
    >
    >Azure 앱 서비스의 앱 설정이 PHP에서 [getenv()](http://php.net/manual/en/function.getenv.php)를 지원합니다. 따라서 FTP 또는 다른 방법을 사용하여 Azure에 `.env` 파일을 수동으로 업로드할 수 있는 반면 원하는 대로 Azure에서 `.env` 없이 원하는 변수를 Azure 앱 설정으로 지정할 수 있습니다. 또한 변수가 `.env` 파일 및 Azure 앱 설정에 있는 경우 Azure 앱 설정이 우선합니다.

4. 마지막 두 작업(가상 디렉터리를 설정하고 작성기를 사용함)에는 [Azure 포털](https://portal.azure.com)이 필요하므로 Azure 계정이 있는 [포털](https://portal.azure.com)에 로그인합니다.

4. 왼쪽 메뉴에서 시작하여 **앱 서비스** > **&lt;app\_name>** > **도구**를 클릭합니다.

    ![Azure의 PHP(Laravel) 앱에 작성기 사용](./media/app-service-web-php-get-started/configure-composer-tools.png)
    
    >[AZURE.TIP] **도구** 대신 **설정**을 클릭하면 **응용 프로그램 설정** 블레이드에 액세스할 수 있으며 이를 통해 수행한 것처럼 PHP 버전, 앱 설정 및 가상 디렉터리를 설정할 수 있습니다.
    
4. **확장** > **추가**를 클릭하여 확장을 추가합니다.

4. **확장 선택** [블레이드](../azure-portal-overview.md)에서 **작성기**를 선택합니다(*블레이드*: 가로로 열리는 포털 페이지).

4. **약관에 동의** 블레이드에서 **확인**을 클릭합니다.

5. **확장 추가** 블레이드에서 **확인**을 클릭합니다.

    Azure가 확장을 추가하는 작업을 완료하면 **확장** 블레이드에 나열된 **작성기** 뿐만 아니라 모퉁이에 친숙한 팝업 메시지가 표시되어야 합니다.

    ![Azure의 PHP(Laravel) 앱에 작성기를 사용한 다음 확장 블레이드](./media/app-service-web-php-get-started/configure-composer-end.png)

    작성기 활성화가 완료되었습니다!
    
4. 웹앱의 블레이드로 돌아가서 **설정** > **응용 프로그램 설정**을 클릭합니다.

    ![설정 블레이드에 액세스하여 Azure의 PHP(Laravel) 앱에 가상 디렉터리 설정](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)

    **응용 프로그램 설정** 블레이드에서 앞에서 설정한 PHP 버전은 다음과 같습니다.

    ![Azure의 PHP(Laravel) 앱에 대한 설정 블레이드의 PHP 버전](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a.png)

    또한 추가한 앱 설정은 다음과 같습니다.
    
    ![Azure의 PHP(Laravel) 앱에 대한 설정 블레이드의 앱 설정](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)

4. 블레이드 맨 아래로 스크롤하고 루트 가상 디렉터리를 변경하여 **site\\wwwroot** 대신 **site\\wwwroot\\public**을 가리킵니다.

    ![Azure의 PHP(Laravel) 앱에 대한 가상 디렉터리 설정](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)

4. 블레이드 위쪽에서 **저장**을 클릭합니다.

    가상 디렉터리 설정이 완료되었습니다!

## Git(및 환경 변수 설정)를 사용하여 웹앱 배포

코드를 지금 배포할 준비가 되었습니다. 명령 프롬프트 또는 터미널에서 다시 수행합니다.

4. 모든 변경 내용을 커밋하고 Git 리포지토리에서처럼 Azure 웹앱에 코드를 배포합니다.

        git add .
        git commit -m "Hurray! My first commit for my Azure app!"
        git push azure master 

    `git push`을 실행하는 경우 Git 배포 암호를 제공하라는 메시지가 표시됩니다. `azure site create` 이전에 배포 자격 증명을 만들라는 메시지가 나타나면 사용한 암호를 입력합니다.
    
5. 이 명령을 실행하여 브라우저에서 실행되는지 확인해 보겠습니다.

        azure site browse

    브라우저는 Laravel 시작 화면을 표시해야 합니다.
    
    ![Azure에 웹앱을 배포한 다음 Laravel 시작 화면](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
    
    축하합니다. 이제 Azure에서 Laravel 웹앱을 실행합니다.
             
## 일반적인 오류 문제 해결

다음은 이 자습서를 따를 경우 발생할 수 있는 일부 오류입니다.

- [Azure CLI에서 "'사이트'가 Azure 명령이 아님"이 표시됩니다](#clierror)
- [웹앱에서 HTTP 403 오류가 표시됩니다](#http403)
- [웹앱에서 "잘못된 것 같습니다."가 표시됩니다](#whoops)
- [웹앱에서 "지원되는 암호기를 찾을 수 없습니다."가 표시됩니다.](#encryptor)

<a name="clierror"></a>
### Azure CLI에서 "'사이트'가 Azure 명령이 아님"이 표시됩니다

명령줄 터미널에 `azure site *`을 실행하는 경우 오류 `error:   'site' is not an azure command. See 'azure help'.`가 표시됩니다.

일반적으로 "ARM"(Azure Resource Manager) 모드로 전환한 결과입니다. 이를 해결하려면 `azure config mode asm`을 실행하여 "ASM"(Azure 서비스 관리) 모드로 다시 전환합니다.

<a name="http403"></a>
### 웹앱에서 HTTP 403 오류가 표시됩니다

Azure에 웹앱을 성공적으로 배포했지만 Azure 웹앱으로 이동한 경우 `HTTP 403` 또는 `You do not have permission to view this directory or page.`가 표시됩니다.

웹앱이 Laravel 앱에 대한 진입점을 찾을 수 없기 때문일 수 있습니다. 루트 가상 디렉터리가 Laravel의 `index.php`인 `site\wwwroot\public`을 가리키도록 변경해야 합니다([Azure 웹앱 구성](#configure)을 참조).

<a name="whoops"></a>
### 웹앱에서 "잘못된 것 같습니다."가 표시됩니다

Azure에 웹앱을 성공적으로 배포했지만 Azure 웹앱으로 이동한 경우 알 수 없는 메시지 `Whoops, looks like something went wrong.`이 표시됩니다.

보다 자세한 오류를 가져오려면 `APP_DEBUG` 환경 변수를 `true`로 설정하여 Laravel 디버깅을 활성화합니다([Azure 웹앱 구성](#configure)을 참조).

<a name="encryptor"></a>
### 웹앱에서 "지원되는 암호기를 찾을 수 없습니다."가 표시됩니다.

Azure에 웹앱을 성공적으로 배포했지만 Azure 웹앱으로 이동한 경우 다음 오류 메시지가 표시됩니다.

![Azure의 PHP(Laravel) 앱에 누락된 APP\_KEY](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)
    
까다로운 오류이지만 적어도 Laravel 디버깅을 설정했으므로 알 수 없는 상태는 아닙니다. Laravel 포럼에서 오류 문자열의 대략적인 검색을 통해 `.env`에서 APP\_KEY를 설정하지 않았기 때문이거나 사용자의 경우 Azure에 `.env`가 없기 때문임을 보여 줍니다. 설정 `APP_KEY`을 Azure 앱 설정으로 추가하여 이를 해결할 수 있습니다([Azure 웹앱 구성](#configure)을 참조).
    
## 다음 단계

[Azure에서 MySQL 데이터베이스를 만들어서](../store-php-create-mysql-database.md) 앱에 데이터를 추가하는 방법을 알아봅니다. 또한 Azure에서 PHP에 대한 더 유용한 다음 링크를 확인합니다.

- [PHP 개발자 센터](/develop/php/)
- [Azure 마켓플레이스에서 웹앱 만들기](app-service-web-create-web-app-from-marketplace.md)
- [Azure 앱 서비스 웹앱에서 PHP 구성](web-sites-php-configure.md)
- [Azure 앱 서비스에서 멀티사이트로 WordPress 변환](web-sites-php-convert-wordpress-multisite.md)
- [Azure 앱 서비스의 엔터프라이즈급 WordPress](web-sites-php-enterprise-wordpress.md)

<!---HONumber=AcomDC_0914_2016-->