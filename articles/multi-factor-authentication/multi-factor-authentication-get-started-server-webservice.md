<properties 
	pageTitle="MFA 서버 모바일 앱 웹 서비스 시작"
	description="Azure Multi-Factor Authentication 앱은 추가적인 대역외 인증 옵션을 제공합니다. 이 옵션을 사용하면 MFA 서버는 사용자에게 푸시 알림을 사용할 수 있습니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# MFA 서버 모바일 앱 웹 서비스 시작

Azure Multi-Factor Authentication 앱은 추가적인 대역외 인증 옵션을 제공합니다. 로그인하는 동안 사용자에게 자동으로 전화가 걸리거나 SMS가 전송되는 대신, Azure Multi-Factor Authentication에서 사용자의 스마트폰이나 태블릿에 있는 Azure Multi-Factor Authentication 앱에 푸시 알림을 보냅니다. 사용자는 앱에서 "인증"을 탭하거나 PIN을 입력하고 "인증"을 탭하여 로그인하면 됩니다.

Azure Multi-Factor Authentication 앱을 사용하려면 앱이 모바일 앱 웹 서비스와 성공적으로 통신할 수 있도록 하기 위해 다음 사항이 필요합니다.

- 하드웨어 및 소프트웨어 요구 사항에 대해서는 하드웨어 및 소프트웨어 요구 사항을 참조하세요.
- v6.0 이상의 Azure Multi-Factor Authentication 서버를 사용해야 합니다.
- Microsoft® IIS(인터넷 정보 서비스) 7.x 이상이 실행되는 인터넷 연결 웹 서버에 모바일 앱 웹 서비스를 설치해야 합니다. IIS에 대한 자세한 내용은 [IIS.NET](http://www.iis.net/)을 참조하세요.
- ASP.NET v4.0.30319가 설치, 등록되고 허용됨으로 설정되어 있는지 확인합니다.
- 필요한 역할 서비스에는 ASP.NET 및 IIS 6 메타베이스 호환성이 포함됩니다.
- 모바일 앱 웹 서비스는 공용 URL을 통해 액세스할 수 있어야 합니다.
- 모바일 앱 웹 서비스는 SSL 인증서로 보호되어야 합니다.
- Azure Multi-Factor Authentication 웹 서비스 SDK는 Azure Multi-Factor Authentication 서버가 설치된 서버의 IIS 7.x 이상에 설치해야 합니다.
- Azure Multi-Factor Authentication 웹 서비스 SDK는 SSL 인증서로 보호되어야 합니다.
- 모바일 웹 앱 서비스는 SSL을 통해 Azure Multi-Factor Authentication 웹 서비스 SDK에 연결될 수 있어야 합니다.
- 모바일 앱 웹 서비스는 "PhoneFactor Admins"라는 보안 그룹의 구성원인 서비스 계정의 자격 증명을 사용하여 Azure Multi-Factor Authentication 웹 서비스 SDK에서 인증을 받을 수 있어야 합니다. 이 서비스 계정 및 그룹은 Azure Multi-Factor Authentication 서버가 도메인 연결된 서버에서 실행 중인 경우 Active Directory에 존재합니다. 도메인에 연결되지 않은 경우에는 이 서비스 계정 및 그룹이 Azure Multi-Factor Authentication 서버에 로컬로 존재합니다.


Azure Multi-Factor Authentication 서버 이외의 서버에 사용자 포털을 설치하려면 다음 세 단계가 필요합니다.

1. 웹 서비스 SDK 설치
2. 모바일 앱 웹 서비스 설치
3. Azure Multi-Factor Authentication 서버에서 모바일 앱 설정 구성
4. 최종 사용자를 위한 Azure Multi-Factor Authentication 앱 활성화

## 웹 서비스 SDK 설치

Azure Multi-Factor Authentication 웹 서비스 SDK가 Azure Multi-Factor Authentication 서버에 아직 설치되어 있지 않으면 해당 서버로 이동하고 Azure Multi-Factor Authentication 서버를 엽니다. 웹 서비스 SDK 아이콘을 클릭하고 웹 서비스 SDK 설치... 단추를 클릭한 후 제공되는 지침을 따릅니다. 웹 서비스 SDK는 SSL 인증서로 보호되어야 합니다. 이 작업을 위해 자체 서명된 인증서를 사용해도 되지만 SSL 연결을 시작할 때 해당 인증서를 신뢰할 수 있도록 사용자 포털 웹 서버에 있는 로컬 컴퓨터 계정의 "신뢰할 수 있는 루트 인증 기관" 저장소로 가져와야 합니다.

<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## 모바일 앱 웹 서비스 설치
모바일 앱 웹 서비스를 설치하기 전에 다음을 고려해야 합니다.

- Azure Multi-Factor Authentication 사용자 포털이 인터넷 연결 서버에 이미 설치되어 있는 경우 사용자 이름, 암호 및 웹 서비스 SDK에 대한 URL을 사용자 포털의 web.config 파일에서 복사할 수 있습니다.
- 인터넷 연결 웹 서버에서 웹 브라우저를 열고 web.config 파일에 입력된 웹 서비스 SDK의 URL로 이동하는 것이 좋습니다. 브라우저가 웹 서비스로 성공적으로 이동될 수 있으면 자격 증명을 묻는 메시지가 표시됩니다. web.config 파일에 입력된 사용자 이름 및 암호를 파일에 표시된 그대로 입력합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.
- 역방향 프록시 또는 방화벽이 모바일 앱 웹 서비스 웹 서버를 차단하고 있으며 SSL 오프로드를 수행하는 경우 모바일 앱 웹 서비스 web.config 파일을 편집하고 <appSettings> 섹션에 다음 키를 추가하여 모바일 앱 웹 서비스가 https 대신 http를 사용하도록 할 수 있습니다. 그러나 모바일 앱에서 방화벽/역방향 프록시 사이에 SSL은 여전히 필요합니다. <add key="SSL\_REQUIRED" value="false"/>

### 모바일 앱 웹 서비스를 설치하려면

<ol>
<li>Azure Multi-Factor Authentication 서버에서 Windows 탐색기를 열고 Azure Multi-Factor Authentication 서버가 설치된 폴더로 이동합니다(예: C:\Program Files\Azure Multi-Factor Authentication). 모바일 앱 웹 서비스가 설치될 서버에 적합하게 32비트 또는 64비트 버전의 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup 설치 파일을 선택합니다. 인터넷 연결 서버에 설치 파일을 복사합니다.</li>

<li>인터넷 연결 웹 서버에서 관리자 권한으로 설치 파일을 실행해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 관리자 권한으로 명령 프롬프트를 열고 설치 파일이 복사된 위치로 이동하는 것입니다.</li>  

<li>Multi-Factor AuthenticationMobileAppWebServiceSetup 설치 파일을 실행하고, 필요한 경우 사이트를 변경한 후 가상 디렉터리를 "PA" 등의 짧은 이름으로 변경합니다. 사용자가 활성화 동안 모바일 장치에 모바일 앱 웹 서비스 URL을 입력해야 하므로 짧은 가상 디렉터리 이름이 권장됩니다.</li>

<li>Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 설치가 끝나면 C:\inetpub\wwwroot\PA(또는 가상 디렉터리 이름에 따른 적절한 디렉터리)로 이동한 후 web.config 파일을 편집합니다.</li>  

<li>WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 및 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 키를 찾아 해당 값을 PhoneFactor Admins 보안 그룹의 구성원인 서비스 계정의 사용자 이름 및 암호로 설정합니다(위의 요구 사항 섹션 참조). 이 값은 이전에 설치한 Azure Multi-Factor Authentication 사용자 포털의 ID 값으로 사용되는 것과 같은 계정일 수 있습니다. 줄 끝에 인용 부호를 제외한 사용자 이름 및 암호를 입력합니다(value=””/>). 정규화된 사용자 이름(예: 도메인\사용자 이름 또는 컴퓨터\사용자 이름)을 사용하는 것이 좋습니다.</li>  

<li>pfMobile App Web Service_pfwssdk_PfWsSdk 설정을 찾아 해당 값을 "http://localhost:4898/PfWsSdk.asmx"에서 Azure Multi-Factor Authentication 서버에서 실행되는 웹 서비스 SDK의 URL(예: https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)로 변경합니다. 이 연결에 SSL이 사용되고 서버 이름에 대해 SSL 인증서가 발급되며 사용되는 URL은 인증서의 이름과 일치해야 하므로 IP 주소가 아니라 서버 이름으로 웹 서비스 SDK를 참조해야 합니다. 서버 이름이 인터넷 연결 서버의 IP 주소로 확인되지 않으면 Azure Multi-Factor Authentication 서버의 이름을 해당 IP 주소로 매핑하기 위해 해당 서버의 hosts 파일에 항목을 추가합니다. 변경된 web.config 파일을 저장합니다.</li>  

<li>모바일 앱 웹 서비스가 설치된 웹 사이트(예: 기본 웹 사이트)가 공개적으로 서명된 인증서에 아직 바인딩되지 않았으면 서버에 인증서를 설치하고(아직 설치되지 않은 경우) IIS 관리자를 연 다음 웹 사이트에 인증서를 바인딩합니다.</li>  

<li>모든 컴퓨터에서 웹 브라우저를 열고 모바일 앱 웹 서비스가 설치된 URL(예: https://www.publicwebsite.com/PA)로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.</li>

### Azure Multi-Factor Authentication 서버에서 모바일 앱 설정 구성
모바일 앱 웹 서비스가 설치되었으므로 포털을 사용하도록 Azure Multi-Factor Authentication 서버를 구성해야 합니다.

#### Azure Multi-Factor Authentication 서버에서 모바일 앱 설정을 구성하려면

1. Azure Multi-Factor Authentication 서버에서 사용자 포털 아이콘을 클릭합니다. 사용자가 자신의 인증 방법을 제어할 수 있도록 허용되면 설정 탭의 Allow users to select method(사용자의 방법 선택 허용)에서 모바일 앱을 선택합니다. 이 기능을 사용하도록 설정하면 최종 사용자는 지원 센터에 문의하여 모바일 앱에 대한 정품 인증을 요청해야 합니다.
2. 사용자가 모바일 앱을 활성화할 수 있도록 허용 확인란을 선택합니다.
3. 사용자 등록 허용 확인란을 선택합니다.
4. 모바일 앱 아이콘을 클릭합니다.
5. Azure Multi-Factor AuthenticationMobileAppWebServiceSetup을 설치할 때 만든 가상 디렉터리에서 사용할 URL을 입력합니다. 제공된 공간에 계정 이름을 입력할 수 있습니다. 이 회사 이름은 모바일 응용 프로그램에 표시됩니다. 이 필드를 비워두면 Azure 관리 포털에서 만들어진 Multi-Factor Auth 공급자의 이름이 표시됩니다.



<center>![Setup](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

<!---HONumber=AcomDC_0921_2016-->