<properties 
	pageTitle="Azure Multi-Factor Authentication 서버의 사용자 포털 배포"
	description="Azure MFA 및 사용자 포털 시작 방법을 설명하는 Azure Multi-Factor Authentication 페이지입니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/15/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication 서버의 사용자 포털 배포

사용자 포털을 사용하여 관리자는 Azure Multi-Factor Authentication 사용자 포털을 설치하고 구성할 수 있습니다. 사용자 포털은 사용자가 Azure Multi-Factor Authentication을 등록하고 계정을 관리할 수 있는 IIS 웹 사이트입니다. 사용자는 다음 로그온 시에 전화 번호를 변경하거나, PIN을 변경하거나, Azure Multi-Factor Authentication을 바이패스할 수 있습니다.

사용자는 일반 사용자 이름과 암호를 사용하여 사용자 포털에 로그인하고 Azure Multi-Factor Authentication 통화를 완료하거나 보안 질문에 답변하여 인증을 완료합니다. 사용자 등록이 허용되면 사용자 포털에 처음 로그인할 때 전화 번호와 PIN을 구성합니다.

사용자 포털 관리자는 새 사용자를 추가하고 기존 사용자를 업데이트하기 위한 권한이 설정되고 부여될 수 있습니다.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Azure Multi-Factor Authentication 서버와 동일한 서버의 사용자 포털 배포

Azure Multi-Factor Authentication 서버와 동일한 서버에 사용자 포털을 설치하려면 다음 필수 요건이 충족되어야 합니다.

- asp.net 및 IIS 6 메타베이스 호환성(IIS 7 이상)을 포함하는 IIS를 설치해야 합니다.
- 해당되는 경우 로그인한 사용자는 컴퓨터 및 도메인에 대해 관리자 권한이 있어야 합니다. 계정에 Active Directory 보안 그룹을 만들 수 있는 권한이 필요하기 때문입니다.

### Azure Multi-Factor Authentication 서버의 사용자 포털을 배포하려면

1. Azure Multi-Factor Authentication 서버 내에서 왼쪽 메뉴에 있는 사용자 포털 아이콘을 클릭하고 Install User Portal(사용자 포털 설치) 단추를 클릭합니다.
1. 다음을 클릭합니다.
1. 다음을 클릭합니다.
1. 컴퓨터가 도메인에 연결되어 있고 사용자 포털과 Azure Multi-Factor Authentication 서비스 간의 통신 보안 유지를 위한 Active Directory 구성이 완료되지 않은 경우 Active Directory 단계가 표시됩니다. 이 구성을 자동으로 완료하려면 다음 단추를 클릭합니다.
1. 다음을 클릭합니다.
1. 다음을 클릭합니다.
1. 닫기를 클릭합니다.
1. 아무 컴퓨터에서나 웹 브라우저를 열고 사용자 포털이 설치된 URL(예: https://www.publicwebsite.com/MultiFactorAuth)로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## 별도 서버에 Azure Multi-Factor Authentication 서버 사용자 포털 배포

Azure Multi-Factor Authentication 앱을 사용하려면 앱이 사용자 포털과 성공적으로 통신할 수 있도록 하기 위해 다음 사항이 필요합니다.

하드웨어 및 소프트웨어 요구 사항에 대해서는 하드웨어 및 소프트웨어 요구 사항을 참조하세요.

- v6.0 이상의 Azure Multi-Factor Authentication 서버를 사용해야 합니다.
- Microsoft® IIS(인터넷 정보 서비스) 6.x, IIS 7.x 이상이 실행되는 인터넷 연결 웹 서버에 사용자 포털을 설치해야 합니다.
- IIS 6.x를 사용하는 경우 ASP.NET v2.0.50727이 설치되고, 등록되고, 허용으로 설정되어야 합니다.
- IIS 7.x 이상을 사용할 때 필요한 역할 서비스에는 ASP.NET 및 IIS 6 메타베이스 호환성이 포함됩니다.
- 사용자 포털은 SSL 인증서로 보호되어야 합니다.
- Azure Multi-Factor Authentication 서버가 설치된 서버의 IIS 6.x, IIS 7.x 이상에 Azure Multi-Factor Authentication 웹 서비스 SDK가 설치되어야 합니다.
- Azure Multi-Factor Authentication 웹 서비스 SDK는 SSL 인증서로 보호되어야 합니다.
- 사용자 포털은 SSL을 통해 Azure Multi-Factor Authentication 웹 서비스 SDK에 연결될 수 있어야 합니다.
- 사용자 포털은 "PhoneFactor Admins"라는 보안 그룹의 구성원인 서비스 계정의 자격 증명을 사용하여 Azure Multi-Factor Authentication 웹 서비스 SDK에서 인증을 받을 수 있어야 합니다. 이 서비스 계정 및 그룹은 Azure Multi-Factor Authentication 서버가 도메인 연결된 서버에서 실행 중인 경우 Active Directory에 존재합니다. 도메인에 연결되지 않은 경우에는 이 서비스 계정 및 그룹이 Azure Multi-Factor Authentication 서버에 로컬로 존재합니다.

Azure Multi-Factor Authentication 서버 이외의 서버에 사용자 포털을 설치하려면 다음 세 단계가 필요합니다.

1. 웹 서비스 SDK 설치
2. 사용자 포털 설치
3. Azure Multi-Factor Authentication 서버에서 사용자 포털 설정 구성


### 웹 서비스 SDK 설치

Azure Multi-Factor Authentication 웹 서비스 SDK가 Azure Multi-Factor Authentication 서버에 아직 설치되어 있지 않으면 해당 서버로 이동하고 Azure Multi-Factor Authentication 서버를 엽니다. 웹 서비스 SDK 아이콘을 클릭하고 웹 서비스 SDK 설치... 단추를 클릭한 후 제공되는 지침을 따릅니다. 웹 서비스 SDK는 SSL 인증서로 보호되어야 합니다. 이 작업을 위해 자체 서명된 인증서를 사용해도 되지만 SSL 연결을 시작할 때 해당 인증서를 신뢰할 수 있도록 사용자 포털 웹 서버에 있는 로컬 컴퓨터 계정의 "신뢰할 수 있는 루트 인증 기관" 저장소로 가져와야 합니다.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### 사용자 포털 설치

별도의 서버에 사용자 포털을 설치하기 전에 다음을 고려해야 합니다.

- 인터넷 연결 웹 서버에서 웹 브라우저를 열고 web.config 파일에 입력된 웹 서비스 SDK의 URL로 이동하는 것이 좋습니다. 브라우저가 웹 서비스로 성공적으로 이동될 수 있으면 자격 증명을 묻는 메시지가 표시됩니다. web.config 파일에 입력된 사용자 이름 및 암호를 파일에 표시된 그대로 입력합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.
- 역방향 프록시 또는 방화벽이 사용자 포털 웹 서버를 차단하고 SSL 오프로드를 수행하고 있는 경우 사용자 포털 web.config 파일을 편집하고 <appSettings> 섹션에 다음 키를 추가하여 사용자 포털이 https 대신 http를 사용하도록 할 수 있습니다. <add key="SSL\_REQUIRED" value="false"/>

#### 사용자 포털을 설치하려면

1. Azure Multi-Factor Authentication 서버에서 Windows 탐색기를 열고 Azure Multi-Factor Authentication 서버가 설치된 폴더로 이동합니다(예: C:\\Program Files\\Multi-Factor Authentication Server). 사용자 포털이 설치될 서버에 적절하게 32비트 또는 64비트 버전의 MultiFactorAuthenticationUserPortalSetup 설치 파일을 선택합니다. 인터넷 연결 서버에 설치 파일을 복사합니다.
2. 인터넷 연결 웹 서버에서 관리자 권한으로 설치 파일을 실행해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 관리자 권한으로 명령 프롬프트를 열고 설치 파일이 복사된 위치로 이동하는 것입니다.
3. MultiFactorAuthenticationUserPortalSetup64 설치 파일을 실행하고 필요한 경우 사이트 및 가상 디렉터리 이름을 변경합니다.
4. 사용자 포털의 설치가 끝나면 C:\\inetpub\\wwwroot\\MultiFactorAuth(또는 가상 디렉터리 이름에 따른 적절한 디렉터리)로 이동한 후 web.config 파일을 편집합니다.
5. USE\_WEB\_SERVICE\_SDK 키를 찾아 값을 false에서 true로 변경합니다. WEB\_SERVICE\_SDK\_AUTHENTICATION\_USERNAME 및 WEB\_SERVICE\_SDK\_AUTHENTICATION\_PASSWORD 키를 찾아 해당 값을 PhoneFactor Admins 보안 그룹의 구성원인 서비스 계정의 사용자 이름 및 암호로 설정합니다(위의 요구 사항 섹션 참조). 줄 끝에 인용 부호를 제외한 사용자 이름 및 암호를 입력합니다(value=””/>). 정규화된 사용자 이름(예: 도메인\\사용자 이름 또는 컴퓨터\\사용자 이름)을 사용하는 것이 좋습니다.
6. pfup\_pfwssdk\_PfWsSdk 설정을 찾아 해당 값을 "http://localhost:4898/PfWsSdk.asmx"에서 Azure Multi-Factor Authentication 서버에서 실행되는 웹 서비스 SDK의 URL(예:https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx)로 변경합니다. 이 연결에 SSL이 사용되고 서버 이름에 대해 SSL 인증서가 발급되며 사용되는 URL은 인증서의 이름과 일치해야 하므로 IP 주소가 아니라 서버 이름으로 웹 서비스 SDK를 참조해야 합니다. 서버 이름이 인터넷 연결 서버의 IP 주소로 확인되지 않으면 Azure Multi-Factor Authentication 서버의 이름을 해당 IP 주소로 매핑하기 위해 해당 서버의 hosts 파일에 항목을 추가합니다. 변경된 web.config 파일을 저장합니다.
7. 사용자 포털이 설치된 웹 사이트(예: 기본 웹 사이트)가 공개적으로 서명된 인증서에 아직 바인딩되지 않았으면 서버에 인증서를 설치하고(아직 설치되지 않은 경우) IIS 관리자를 연 다음 웹 사이트에 인증서를 바인딩합니다.
8. 아무 컴퓨터에서나 웹 브라우저를 열고 사용자 포털이 설치된 URL(예: https://www.publicwebsite.com/MultiFactorAuth)로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.



## Azure Multi-Factor Authentication 서버에서 사용자 포털 설정 구성
포털이 설치되었으므로 포털을 사용하도록 Azure Multi-Factor Authentication 서버를 구성해야 합니다.

Azure Multi-Factor Authentication 서버에서는 사용자 포털에 대한 몇 가지 옵션을 제공합니다. 다음 표에서는 이러한 옵션 및 각 옵션의 용도를 설명하는 목록을 제공합니다.

사용자 포털 설정|설명|
:------------- | :------------- |
User Portal URL(사용자 포털 URL)| 포털이 호스트되는 URL을 입력할 수 있습니다.
Primary authentication(기본 인증)| 포털에 로그인할 때 사용할 인증 형식을 지정할 수 있습니다. Windows, Radius 또는 LDAP 인증 중 하나입니다.
Allow users to log in(로그인 허용)|사용자 포털의 로그인 페이지에서 사용자 이름 및 암호를 입력할 수 있습니다. 선택하지 않으면 상자는 회색으로 표시됩니다.
Allow user enrollment(등록 허용)|설치 화면에서 전화 번호와 같은 추가 정보를 입력하여 다단계 인증에 등록할 수 있습니다. 백업 휴대폰 프롬프트를 통해 보조 전화 번호를 지정할 수 있습니다. 타사 OATH 토큰 프롬프트를 통해 타사 OATH 토큰을 지정할 수 있습니다.
Allow users to initiate One-Time Bypass(일회성 바이패스 시작 허용)| 일회성 바이패스를 시작할 수 있습니다. 이를 설정하면 해당 사용자가 다음번에 로그인할 때 적용됩니다. 바이패스(초) 프롬프트의 상자에서 기본값 300초를 변경할 수 있습니다. 변경하지 않으면 일회성 바이패스는 300초가 지나면 만료됩니다.
Allow users to select method(방법 선택 허용)| 자신의 기본 연락 방법을 지정할 수 있습니다. 전화 통화, 문자 메시지, 모바일 앱 또는 OATH 토큰 옵션 중 선택할 수 있습니다.
Allow users to select language(언어 선택 허용)| 전화 통화, 문자 메시지, 모바일 앱 또는 OATH 토큰에 사용되는 언어를 변경할 수 있습니다.
Allow users to activate mobile app(모바일 앱 활성화 허용)| 서버에서 사용되는 모바일 앱 활성화 프로세스를 완료하기 위한 활성화 코드를 생성할 수 있습니다. 이를 활성화할 수 있는 장치 수를 설정할 수도 있습니다. 1개에서 10개를 설정할 수 있습니다.
Use security questions for fallback(대체 방법으로 보안 질문 사용)|다단계 인증에 실패하는 경우 보안 질문을 사용할 수 있습니다. 답변이 맞아야 하는 보안 질문의 수를 지정할 수 있습니다.
Allow users to associate third-party OATH token(타사 OATH 토큰 연결 허용)| 타사 OATH 토큰을 지정할 수 있습니다.
Use OATH token for fallback(대체 방법으로 OATH 토큰 사용)|다단계 인증에 실패하는 경우 OATH 토큰을 사용할 수 있습니다. 세션 제한 시간(분)을 지정할 수도 있습니다.
로깅 사용|사용자 포털에서 로깅을 사용합니다. 로그 파일은 C:\\Program Files\\Multi-Factor Authentication Server\\Logs에 있습니다.

이 설정의 대부분은 사용하도록 설정되고 사용자가 사용자 포털에 로그인하면 표시됩니다.

![사용자 포털 설정](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### Azure Multi-Factor Authentication 서버에서 사용자 포털 설정을 구성하려면




1. Azure Multi-Factor Authentication 서버에서 사용자 포털 아이콘을 클릭합니다. 설정 탭에서 사용자 포털 URL 텍스트 상자에 사용자 포털의 URL을 입력합니다. 이 URL은 메일 기능이 사용되도록 설정된 경우 Azure Multi-Factor Authentication 서버로 가져올 때 사용자에게 전송되는 메일에 삽입됩니다.
2. 사용자 포털에서 사용하려는 설정을 선택합니다. 예를 들어 사용자가 자신의 인증 방법을 제어하도록 허용되면, 사용자가 선택할 수 있는 방법과 함께 사용자가 방법을 선택할 수 있도록 허용 옵션이 선택되어 있는지 확인합니다.
3. 표시되는 설정을 이해하기 위한 정보를 보려면 오른쪽 위 구석에 있는 도움말 링크를 클릭합니다.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## Administrators(관리자) 탭
이 탭에서 관리자 권한을 부여할 사용자를 추가할 수 있습니다. 관리자를 추가할 때 관리자에게 부여되는 권한을 세부적으로 조정할 수 있습니다. 이러한 방식으로 관리자에게 필요한 권한만을 부여할 수 있습니다. Add(추가) 단추를 클릭하고 사용자와 해당 권한을 선택한 다음 Add(추가)를 클릭하면 됩니다.

![사용자 포털 관리자](./media/multi-factor-authentication-get-started-portal/admin.png)


## Security Questions(보안 질문)
이 탭에서 대체 방법으로 보안 질문 사용 옵션을 선택한 경우 답변을 제공해야 하는 보안 질문을 지정할 수 있습니다. Azure Multi-Factor Authentication 서버에는 사용할 수 있는 기본 질문이 함께 제공됩니다. 순서를 변경하거나 사용자가 직접 질문을 추가할 수도 있습니다. 질문을 추가할 때 질문에 사용되는 언어를 지정할 수도 있습니다.

![사용자 포털 보안 질문](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## Passed Sessions(전달된 세션)

## SAML
SAML을 사용하는 ID 공급자의 클레임을 받을 수 있는 사용자 포털을 설치할 수 있습니다. 시간 제한 세션, 확인 인증서 및 로그아웃 리디렉션 URL을 지정할 수 있습니다.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## 신뢰할 수 있는 IP
이 탭에서 추가할 수 있는 IP 주소 범위 또는 단일 IP 주소를 지정할 수 있어 사용자가 이러한 IP 주소 중 하나에서 로그인하는 경우 다단계 인증을 바이패스할 수 있습니다.

![사용자 포털 신뢰할 수 있는 IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## Self-Service User Enrollment(셀프 서비스 사용자 등록)
사용자가 로그인하고 등록할 수 있게 하려면 로그인 허용과 등록 허용 옵션을 선택해야 합니다. 선택한 설정은 사용자 로그인 환경에 영향을 줍니다.

예를 들어 사용자 포털에 로그인하고 Log In(로그인) 단추를 클릭하면 Azure Multi-Factor Authentication User Setup(Azure Multi-Factor Authentication 사용자 설정) 페이지로 이동합니다. Azure Multi-Factor Authentication 구성 방식에 따라 사용자는 인증 방법을 선택할 수 있습니다.

음성 통화 인증 방법을 선택하거나 해당 방법을 사용하도록 미리 구성했으면 사용자의 기본 전화 번호와 내선 번호(해당하는 경우)를 입력하라는 메시지가 표시됩니다. 백업 전화 번호도 입력할 수도 있습니다.

![사용자 포털 신뢰할 수 있는 IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

사용자가 PIN을 사용해서 인증해야 하는 경우 PIN을 입력하라는 메시지가 표시됩니다. 전화 번호와 PIN(해당되는 경우)을 입력한 후 Call Me Now(전화로 인증)라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 기본 전화 번호로 전화 통화 인증을 수행합니다. 사용자는 전화 통화에 응답하고 PIN(해당되는 경우)을 입력하고 # 키를 눌러야 자체 등록 프로세스의 다음 단계로 이동할 수 있습니다.

사용자가 SMS 문자 인증 방법을 선택하거나 해당 방법을 사용하도록 미리 구성했으면 휴대폰 번호를 입력하라는 메시지가 표시됩니다. 사용자가 PIN을 사용해서 인증해야 하는 경우 PIN을 입력하라는 메시지가 표시됩니다. 전화 번호와 PIN(해당되는 경우)을 입력한 후 Text Me Now(문자로 인증)라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 휴대폰으로 SMS 인증을 수행합니다. 사용자는 OTP(일회성 암호)가 포함된 SMS를 받고 OTP(일회성 암호)와 PIN(해당되는 경우)을 사용하여 메시지에 회신해야만 자체 등록 프로세스의 다음 단계로 이동할 수 있습니다.

![사용자 포털 SMS](./media/multi-factor-authentication-get-started-portal/text.png)

사용자가 모바일 앱 인증 방법을 선택하거나 해당 방법을 사용하도록 미리 구성했으면 해당 장치에 Azure Multi-Factor Authentication 앱을 설치하고 활성화 코드를 생성하라는 메시지가 페이지에 표시됩니다. Azure Multi-Factor Authentication 앱을 설치한 후 Generate Activation Code(활성화 코드 생성) 단추를 클릭합니다.

>[AZURE.NOTE]Azure Multi-Factor Authentication 앱을 사용하려면 해당 장치에 푸시 알림을 사용하도록 설정해야 합니다.

그러면 페이지에 활성화 코드와 URL이 바코드 그림과 함께 표시됩니다. 사용자가 PIN을 사용해서 인증해야 하는 경우 PIN을 입력하라는 메시지가 표시됩니다. Azure Multi-Factor Authentication 앱에 활성화 코드와 URL을 입력하거나 바코드 스캐너를 사용하여 바코드 그림을 스캔하고 Activate(활성화) 단추를 클릭합니다.

활성화를 완료한 후 Authenticate Me Now(지금 인증)라는 단추를 클릭합니다. Azure Multi-Factor Authentication은 사용자의 모바일 앱에서 인증을 수행합니다. 사용자는 PIN(해당되는 경우)을 입력하고 모바일 앱에서 Authenticate(인증) 단추를 눌러야 자체 등록 프로세스의 다음 단계로 이동할 수 있습니다.


관리자가 보안 질문 및 답변을 수집하도록 Azure Multi-Factor Authentication 서버를 구성했으면 보안 질문 페이지로 이동됩니다. 사용자는 4개의 보안 질문을 선택하고 해당 질문에 대한 답변을 입력해야 합니다.

![사용자 포털 보안 질문](./media/multi-factor-authentication-get-started-portal/secq.png)

이제 사용자 자체 등록이 완료되어 사용자가 사용자 포털에 로그인됩니다. 사용자는 관리자가 허용하는 경우 이후에 언제든지 사용자 포털에 다시 로그인하여 전화 번호, PIN, 인증 방법 및 보안 질문을 변경할 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->