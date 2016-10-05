<properties 
	pageTitle="Azure Multi-Factor Authentication 다음 작업"
	description="MFA로 다음에 수행할 작업을 설명하는 Azure Multi-Factor Authentication 페이지입니다. 보고서, 사기 행위 경고, 일회성 바이패스, 사용자 지정 음성 메시지, 캐시, 신뢰할 수 있는 ip 및 앱 암호를 포함합니다."
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
	ms.topic="article"
	ms.date="08/30/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication 구성

다음 문서는 준비하고 실행 중인 Multi-Factor Authentication을 관리하는데 도움이 됩니다. Azure Multi-Factor Authentication을 최대한 활용할 수 있게 하는 다양한 항목을 설명합니다. 모든 버전의 Multi-Factor Authentication에서 이러한 모든 기능을 사용할 수 없다는 점에 주의하십시오.

아래의 기능 중 일부에 대한 구성은 Azure Multi-Factor Authentication 관리 포털에서 찾을 수 있습니다. 이 포털에 액세스할 수 있는 두 가지 방법은 Azure 포털을 통해 완료됩니다. 첫 번째는 사용량 기반 MFA를 사용하는 경우 Multi-Factor Auth 공급자를 관리하여 수행합니다. 두 번째는 MFA 서비스 설정을 통해 수행합니다. 두 번째 옵션을 사용하려면 다단계 인증 공급자나 Azure MFA, Azure AD Premium 또는 Enterprise Mobility Suite 라이선스가 필요합니다.

Azure Multi-Factor Auth 공급자를 통해 MFA 관리 포털에 액세스하려면 관리자 권한으로 Azure 포털에 로그인하고 Active Directory 옵션을 선택합니다. **Multi-Factor Auth 공급자** 탭을 클릭한 다음 디렉터리를 선택하고 아래쪽에서 **관리** 단추를 클릭합니다.

MFA 서비스 설정 페이지를 통해 MFA 관리 포털에 액세스하려면 관리자 권한으로 Azure 포털에 로그인하고 Active Directory 옵션을 선택합니다. 디렉터리를 클릭한 다음 **구성** 탭을 클릭합니다. Multi-Factor Authentication 섹션에서 **서비스 설정 관리**를 선택합니다. MFA 서비스 설정 페이지의 맨 아래에서 **포털로 이동** 링크를 클릭합니다.


기능| 설명| 포함된 내용
:------------- | :------------- | :------------- |
[사기 행위 경고](#fraud-alert)|사기 행위 경고를 구성하고 설정하여 사용자가 해당 리소스에 액세스하려는 사기성 시도를 사기를 보고할 수 있습니다.|사기 행위 설치, 구성 및 보고 방법
[일회성 바이패스](#one-time-bypass) |일회성 바이패스로 Multi-Factor Authentication "바이패스"하여 사용자가 단일 시간을 인증할 수 있습니다.|일회성 바이패스 설치 및 구성 방법
[사용자 지정 음성 메시지](#custom-voice-messages) |사용자 지정 음성 메시지를 사용하면 Multi-Factor Authentication으로 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. |사용자 지정 인사말 및 메시지 설정 및 구성 방법
[캐싱](#caching-in-azure-multi-factor-authentication)|캐싱을 사용하면 특정 시간 기간을 설정하여 후속 인증 시도가 자동으로 성공하도록 할 수 있습니다. |인증 캐싱을 설정 및 구성하는 방법입니다.
[신뢰할 수 있는 IP](#trusted-ips)|신뢰할 수 있는 IP는 관리 또는 페더레이션된 테넌트의 관리자가 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 Multi-Factor Authentication을 바이패스할 수 있도록 하는 Multi-Factor Authentication의 기능입니다.|Multi-Factor Authentication에서 제외된 IP 주소 구성 및 설정
[앱 암호](#app-passwords)|앱 암호를 사용하면 mfa를 인식하지 않는 응용 프로그램은 Multi-Factor Authentication를 바이패스하고 계속 작업할 수 있습니다.|앱 암호에 대한 정보입니다.
[기억된 장치 및 브라우저용 Multi-Factor Authentication 기억](#remember-multi-factor-authentication-for-devices-users-trust)|사용자가 MFA를 사용하여 성공적으로 로그인한 후 정해진 일수 동안 장치를 기억할 수 있습니다.|이 기능을 활성화하고 일 수를 설정하는 방법에 대한 정보입니다.
[선택 가능한 확인 방법](#selectable-verification-methods)|사용자가 사용할 수 있는 인증 방법을 선택할 수 있습니다.|전화 또는 문자 메시지 같은 특정 인증 방법을 활성화 또는 비활성화하는 방법에 대한 정보입니다.



## 사기 행위 경고
사기 행위 경고를 구성하고 설정하여 사용자가 해당 리소스에 액세스하려는 사기성 시도를 사기를 보고할 수 있습니다. 사용자가 모바일 앱 또는 자신의 전화를 통해 사기 행위를 보고할 수 있습니다.

### 사기 행위 경고 설정 및 구성

1.	http://azure.microsoft.com에 로그온합니다.
2.	이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
3.	Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 설정을 클릭합니다.
4.	설정 페이지의 사기 경고 섹션에서 허용 사용자를 선택하여 사기 행위 경고 확인란을 제출합니다.
5.	사기 보고 시 사용자를 차단하려는 경우 사기 보고 시 사용자 차단에 확인란을 선택합니다.
6.	**초기 인사말 중 사기 행위 보고 코드** 텍스트 상자에 호출 확인 중 사용할 수 있는 숫자 코드를 입력합니다. 사용자가 # 기호 대신 코드와 #를 입력하면 사기 행위 경고가 보고됩니다.
7.	아래쪽에서 저장을 클릭합니다.

>[AZURE.NOTE]
Microsoft의 기본 음성 인사말은 사용자가 사기 행위 경고 제출하기 위해 0# 키를 누르도록 지시합니다. 0이 아닌 코드를 사용하는 경우 적절한 지침과 함께 고유의 사용자 지정 음성 인사말을 기록하고 업로드해야 합니다.


![클라우드](./media/multi-factor-authentication-whats-next/fraud.png)

### 사기 행위 경고 보고
두 가지 방법으로 사기 행위 경고를 보고할 수 있습니다. 모바일 앱 또는 전화를 통해 보고합니다.

### 모바일 앱으로 사기 행위 경고 보고



1. 전화에 전송된 확인 메시지를 클릭하면 Microsoft Authenticator 앱이 시작됩니다.
2. 사기를 보고하려면 취소 및 사기 보고를 클릭합니다. 그러면 조직의 IT 지원 담당자에 통지됩니다라는 상자가 표시됩니다.
3. 사기 행위 보고를 클릭합니다.
4. 앱에서 닫기를 클릭합니다.

![클라우드](./media/multi-factor-authentication-whats-next/report1.png)


![클라우드](./media/multi-factor-authentication-whats-next/fraud2.png)

### 모바일 앱으로 사기 행위 경고를 보고하려면

1. 전화로 확인 호출이 들어오면 응답합니다.</li>
2. 사기 행위를 보고하려면 휴대폰을 통해 사기 행위를 보고하도록 구성된 코드와 # 기호를 입력합니다. 그러면 사기 행위 경고가 전송되었다는 알림이 표시됩니다.
3. 통화를 종료합니다.

### 사기 행위 보고서를 보려면

1. 여기로 로그온합니다. [http://azure.microsoft.com](https://azure.microsoft.com/)
2. 왼쪽에서 Active Directory를 선택합니다.
3. 위쪽에서 Multi-Factor Auth 공급자를 선택합니다. 그러면 Multi-Factor Auth 공급자의 목록이 표시됩니다.
4. 둘 이상의 Multi-Factor Auth 공급자를 사용하도록 설정한 경우 사기 행위 경고 보고서를 보려는 하나를 선택하고 페이지의 아래에서 관리를 클릭합니다. 하나만 있는 경우 관리를 클릭합니다. 그러면 Multi-Factor Authentication 관리 포털이 열립니다.
5. Azure Multi-factor Authentication 관리 포털의 왼쪽, 보고서 보기 아래에서 사기 행위 경고를 클릭합니다.
6. 보고서에서 보려는 날짜 범위를 지정합니다. 또한 특정 사용자 이름, 전화번호 및 사용자 상태를 지정할 수 있습니다.
7. 실행을 클릭합니다. 그러면 아래와 비슷한 보고서를 표시합니다. 또한 보고서를 내보내려는 경우 CSV로 내보내기를 클릭할 수 있습니다.

## 일회성 바이패스

일회성 바이패스로 Multi-Factor Authentication "바이패스"하여 사용자가 단일 시간을 인증할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다. 그러므로 모바일 앱 또는 전화가 알림 또는 전화 통화를 받지 못하는 경우에 일회성 바이패스를 설정하여 원하는 리소스에 액세스할 수 있습니다.

### 일회성 바이패스를 만들려면

1.	http://azure.microsoft.com에 로그온합니다.
2.	이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
3.	Azure Multi-Factor Authentication 관리 포털의 왼쪽에서 옆에 +가 있는 테넌트 또는 Azure MFA 공급자의 이름이 나타날 경우 +를 클릭하여 다른 MFA 서버 복제 그룹 및 Azure 기본 그룹을 참조하세요. 적절한 그룹을 클릭합니다.
4.	사용자 관리에서 **일회성 바이패스**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/create1.png)
5.	일회성 바이패스 페이지에서 **새 일회성 바이패스**를 클릭합니다.
6.	사용자 이름, 바이패스가 유지될 시간(초), 바이패스가 필요한 이유를 입력하고 **바이패스**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/create2.png)
7.	이 시점에서 일회성 바이패스가 만료되기 전에 사용자가 로그인해야 합니다.



### 일회성 바이패스 보고서를 보려면

1. 여기로 로그온합니다. [http://azure.microsoft.com](https://azure.microsoft.com/)
2. 왼쪽에서 Active Directory를 선택합니다.
3. 위쪽에서 Multi-Factor Auth 공급자를 선택합니다. 그러면 Multi-Factor Auth 공급자의 목록이 표시됩니다.
4. 둘 이상의 Multi-Factor Auth 공급자를 사용하도록 설정한 경우 사기 행위 경고 보고서를 보려는 하나를 선택하고 페이지의 아래에서 관리를 클릭합니다. 하나만 있는 경우 관리를 클릭합니다. 그러면 Multi-Factor Authentication 관리 포털이 열립니다.
5. Azure Multi-factor Authentication 관리 포털의 왼쪽, 보고서 보기 아래에서 일회성 바이패스를 클릭합니다.
6. 보고서에서 보려는 날짜 범위를 지정합니다. 또한 특정 사용자 이름, 전화번호 및 사용자 상태를 지정할 수 있습니다.
7. 실행을 클릭합니다. 그러면 아래와 비슷한 보고서를 표시합니다. 또한 보고서를 내보내려는 경우 CSV로 내보내기를 클릭할 수 있습니다.

<center>![클라우드](./media/multi-factor-authentication-whats-next/report.png)</center>

## 사용자 지정 음성 메시지

사용자 지정 음성 메시지를 사용하면 Multi-Factor Authentication으로 사용자 고유의 녹음/녹화 또는 인사말을 사용할 수 있습니다. Microsoft 기록을 추가하거나 바꾸는 데 사용할 수 있습니다.

이 작업을 수행하려면 다음을 고려하십시오.

- 현재 지원되는 파일 형식은 .wav 및 .mp3입니다.
- 파일 크기는 5MB로 제한됩니다.
- 인증 메시지는 20초 보다 길지 않은 것이 좋습니다. 이를 초과하면 메시지가 완료되기 전에 사용자가 응답하지 않고 확인 시간이 초과되기 때문에 확인이 실패를 일으킵니다.



### Multi-Factor Authentication에 사용자 지정 음성 메시지를 설정하려면
1.	지원되는 파일 형식 중 하나를 사용하여 사용자 지정 음성 메시지 만들기
2.	http://azure.microsoft.com에 로그온합니다.
3.	이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
4.	Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 음성 메시지를 클릭합니다.
5.	음성 메시지 섹션에서 **새 음성 메시지**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/custom1.png)
6.	구성: 새 음성 메시지 페이지에서 **사운드 파일 관리**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/custom2.png)
7.	구성: 사운드 파일 페이지에서 **사운드 파일 업로드**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/custom3.png)
8.	구성: 사운드 파일 업로드에서 **찾아보기**를 클릭하고 음성 메시지로 이동하여 **열기**를 클릭합니다. ![클라우드](./media/multi-factor-authentication-whats-next/custom4.png)
9.	설명을 추가하고 업로드를 클릭합니다.
10.	이 작업이 완료되면 파일을 성공적으로 업로드했다는 메시지가 표시됩니다.
11.	왼쪽에서 음성 메시지를 클릭합니다.
12.	음성 메시지 섹션에서 새 음성 메시지를 클릭합니다.
13.	언어 드롭다운 메뉴에서 언어를 선택합니다.
14.	이 메시지가 특정 응용 프로그램용인 경우 응용 프로그램 상자에서 지정하십시오.
15.	메시지 유형에서 새 사용자 지정 메시지와 함께 재정의될 수 있는 메시지 유형을 선택합니다.
16.	사운드 파일 드롭다운에서 사운드 파일을 선택합니다.
17.	**만들기**를 클릭합니다. 성공적으로 음성 메시지를 만들었다는 메시지가 표시됩니다. ![클라우드](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## Azure Multi-Factor Authentication에서 캐싱

캐싱을 사용하면 특정 시간 기간을 설정하여 후속 인증 시도가 자동으로 성공하도록 할 수 있습니다. 첫 번째 요청이 진행 중인 동안 VPN과 같은 온-프레미스 시스템이 여러 확인 요청을 전송하는 경우 주로 사용됩니다. 이렇게 하면 사용자가 진행 중인 확인에 성공한 후에 자동으로 후속 요청이 성공할 수 있습니다. 캐싱은 Azure AD 로그인에 사용하기 위한 것이 아닙니다.


### Azure Multi-Factor Authentication에서 캐싱 설정

1.	http://azure.microsoft.com에 로그온합니다.
2.	이 페이지 맨 위에 있는 지침에 따라 MFA 관리 포털로 이동합니다.
3.	Azure Multi-Factor Authentication 관리 포털의 구성 섹션에서 캐싱을 클릭합니다.
4.	캐싱 페이지 구성에서 새 캐시를 클릭합니다.
5.	캐시 유형 및 캐시 시간(초)을 선택합니다. 만들기를 클릭합니다.

<center>![클라우드](./media/multi-factor-authentication-whats-next/cache.png)</center>

## 신뢰할 수 있는 IP

신뢰할 수 있는 IP는 관리 또는 페더레이션된 테넌트의 관리자가 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 Multi-Factor Authentication을 바이패스할 수 있도록 하는 Multi-Factor Authentication의 기능입니다. 기능은 Azure AD Premium, Enterprise Mobility Suite 또는 Azure Multi-Factor Authentication 라이선스가 있는 Azure AD 테넌트에서 사용할 수 있습니다.


Azure AD 테넌트의 유형| 사용 가능한 신뢰할 수 있는 IP 옵션
:------------- | :------------- |
관리|특정 IP 주소 범위 – 관리자는 사용자 회사의 인트라넷에서 로그인하는 사용자에 대해 Multi-factor Authentication을 바이패스할 수 있는 IP 주소를 지정할 수 있습니다.
페더레이션|<li>모든 페더레이션된 사용자 - 조직 내에서 로그인한 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하는 Multi-Factor Authentication을 바이패스합니다. </li><li>특정 IP 주소 범위 – 관리자는 회사의 인트라넷에서 로그인하는 사용자에 대해 Multi-Factor Authentication을 바이패스할 수 있는 범위의 IP 주소를 지정할 수 있습니다.

이 바이패스는 회사의 인트라넷 내부에서만 작동합니다. 예를 들어 모든 페더레이션된 사용자를 선택하고 사용자가 회사의 인트라넷 외부에서 로그인한 경우 해당 사용자가 AD FS 클레임을 제시하더라도 Multi-factor Authentication을 사용하여 인증해야 합니다. 다음 테이블은 신뢰할 수 있는 IP를 설정하였을 때 내부 회사 네트워크 및 회사 네트워크 외부에서 Multi-factor Authentication 및 앱 암호가 필요한 경우를 설명합니다.


|활성화된 신뢰할 수 있는 IP| 비활성화된 신뢰할 수 있는 IP
:------------- | :------------- | :------------- |
회사 네트워크 내부|브라우저 흐름을 위해 Multi-factor Authentication는 필요하지 않습니다.|브라우저 흐름을 위해 Multi-factor Authentication가 필요합니다.
|리치 클라이언트 앱을 위해 사용자가 앱 암호를 생성하지 않은 경우 일반 암호가 작동합니다. 앱 암호를 만들면 앱 암호가 필요합니다.|리치 클라이언트 앱을 위해 앱 암호가 필요합니다
회사 네트워크 외부|브라우저 흐름을 위해 Multi-factor Authentication가 필요합니다|브라우저 흐름을 위해 Multi-factor Authentication가 필요합니다
|리치 클라이언트 앱을 위해 앱 암호가 필요합니다.|리치 클라이언트 앱을 위해 앱 암호가 필요합니다.

### 신뢰할 수 있는 IP를 활성화하려면

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. 디렉터리 아래에서 사용자가 Trusted IPsing을 설정하려는 디렉터리를 클릭합니다.
4. 선택한 디렉터리에서 구성을 클릭합니다.
5. Multi-factor Authentication 섹션에서 서비스 설정 관리를 클릭합니다.
6. 서비스 설정 페이지의 신뢰할 수 있는 IP에서 다음 중 하나를 선택합니다.

	- 내 인트라넷에서 발생한 페더레이션된 사용자의 요청에 대해 회사 네트워크에서 로그인하는 모든 페더레이션된 사용자는 AD FS에서 발급한 클레임을 사용하는 Multi-Factor Authentication을 바이패스합니다.
	- 공용 IP 중 특정 범위의 요청에 대해 CIDR 표기법을 사용하여 제공된 상자에 IP 주소를 입력합니다. 예를 들어 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 범위의 IP 주소에 xxx.xxx.xxx.0/24, 또는 단일 IP 주소에 xxx.xxx.xxx.xxx/32입니다. 최대 50개의 IP 주소 범위를 입력할 수 있습니다.

7. 저장을 클릭합니다.
8. 업데이트를 적용하면 닫기를 클릭합니다.



![신뢰할 수 있는 IP](./media/multi-factor-authentication-whats-next/trustedips3.png)




## 앱 암호

Office 2010 또는 이전 및 Apple 메일과 같은 일부 앱에서 Multi-factor Authentication를 사용할 수 없습니다. 이러한 앱을 사용하려면 기존의 암호 대신 "앱 암호"를 사용해야 합니다. 앱 암호를 사용하면 응용 프로그램이 Multi-factor Authentication을 바이패스하고 작업을 계속할 수 있습니다.

>[AZURE.NOTE] Office 2013 클라이언트에 대한 최신 인증
>
> Office 2013 클라이언트(Outlook 포함)는 이제 새 인증 프로토콜을 지원하고 Multi-Factor Authentication을 지원하도록 설정할 수 있습니다. 즉, 일단 이렇게 설정되면 Office 2013 클라이언트에서 앱 암호를 사용할 필요가 없습니다. 자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하십시오.



### 앱 암호에 대해 알아야 할 중요 사항

다음은 앱 암호에 대해 알고 있어야 하는 중요한 사항의 목록입니다.

인증 환경|브라우저 기반 앱용|브라우저 이외 기반 앱용
:------------- | :------------- | :-------------
|<ul><li>인증의 첫번째 요소는 수행된 온-프레미스입니다</li><li>두번째 요소는 클라우드 ID에서 수행되는 휴대폰 기반 방법입니다.</li>|<ul><li>관리자 및 사용자는 앱 암호를 사용하여 로그인할 수 있습니다.

- 사용자가 도난 노출 영역이 늘어나는 여러 앱 암호를 가질 수 있습니다. 앱 암호는 기억하기가 어려우므로 사용자가 적어 두는 것이 좋을 수도 있습니다. 앱 암호로 로그인하는 데 단 하나의 요소만 필요하므로 이는 권장되지 않으며 하지 않는 것이 좋습니다.
- 암호를 캐시하고 온-프레미스 시나리오에서 사용하는 앱은 앱 암호가 조직 ID 외부에서 알려질 수 없으므로 실패할 수도 있습니다. 예제는 온-프레미스에 있는 Exchange 전자 메일이지만 보관된 메일은 클라우드에 있습니다. 동일한 암호는 작동하지 않습니다.
- 실제 암호는 자동으로 생성되고 사용자가 제공하지 않습니다. 자동으로 생성된 암호를 공격자가 추측하기 어렵고 더 안전하기 때문입니다.
- 현재 사용자 당 40개의 암호로 제한되어 있습니다. 새로 만들려면 기존 앱 암호 중 하나를 삭제하라는 메시지가 표시됩니다.
- 사용자 계정에 Multi-Factor Authentication을 사용하도록 설정되었으면 대부분의 브라우저가 아닌 클라이언트(예: Outlook 및 Lync)에 앱 암호를 사용할 수 있지만, Windows PowerShell과 같은 브라우저가 아닌 응용 프로그램을 통해서는 사용자에게 관리 계정이 있어도 앱 암호를 사용하여 관리 작업을 수행할 수 없습니다. 강력한 암호로 PowerShell 스크립트를 실행할 서비스 계정을 만들고 해당 계정에 Multi-Factor Authentication을 사용하지 않도록 설정해야 합니다.

>[AZURE.WARNING]  앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 끝점과 통신하는 하이브리드 환경에서는 작동하지 않습니다.
>
>앱 암호는 클라이언트가 온-프레미스 및 클라우드 자동 검색 끝점과 통신하는 하이브리드 환경에서는 작동하지 않음을 유의하세요. 온-프레미스를 인증하는 데는 도메인 암호가 필요하고 클라우드를 인증하는 데는 앱 암호가 필요하기 때문입니다.


### 앱 암호에 대한 명명 지침
앱 암호 이름은 해당 이름이 사용될 장치를 반영하는 것이 좋습니다. 예를 들어 Outlook, Word 및 Excel 등의 브라우저 이외 앱이 있는 노트북을 사용하도록 설정한 경우 노트북을 지칭하는 하나의 앱 암호를 만들고 해당 앱 암호를 이러한 모든 응용 프로그램에서 사용합니다. 이러한 모든 응용 프로그램에 대해 별도의 암호를 만들 수 있지만 권장되지 않습니다. 장치당 하나씩 앱 암호를 사용하는 것이 권장 방식입니다.


<center>![클라우드](./media/multi-factor-authentication-whats-next/naming.png)</center>


### 페더레이션된(SSO) 앱 암호
Azure AD는 온-프레미스 Windows Server Active Directory 도메인 서비스(AD DS)로 페더레이션을 지원합니다. 조직이 Azure AD를 사용하여 페더레이션되고(SSO) Azure Multi-Factor Authentication을 사용하려는 경우 앱 암호를 사용할 때 인식해야 하는 중요한 정보가 다음에 있습니다. 이는 페더레이션된 고객(SSO)에게만 적용됩니다.

- Azure AD로 앱 암호를 확인하므로 페더레이션을 바이패스합니다. 앱 암호를 설정할 때 페더레이션이 능동적으로 사용됩니다.
- 페더레이션된 사용자의 경우 수동 흐름과 달리 ID 공급자(IdP)로 이동하지 않습니다. 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 실시간으로 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함/삭제 설정은 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
- 앱 암호를 사용할 경우 온-프레미스 클라이언트 액세스 제어 설정은 적용되지 않습니다.
- 온-프레미스 인증 로깅 / 감사 기능은 앱 암호에 사용할 수 없습니다
- 추가 최종 사용자 교육이 Microsoft Lync 2013 클라이언트에 필요합니다. 필요한 단계에 대해 전자 메일의 암호를 앱 암호로 변경하는 방법을 참조하십시오.
- 특정 고급 아키텍처 디자인은 클라이언트와 Multi-Factor Authentication을 사용하는 경우 인증 위치에 따라 조직의 사용자 이름과 암호 및 앱 암호를 조합하여 사용할 필요가 있습니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.

예를 들어 다음을 구성하는 아키텍처가 있다고 가정합니다.

- Azure AD를 사용하여 Active Directory의 온-프레미스 인스턴스를 페더레이션하고 있습니다
- 온라인 Exchange를 사용하고 있습니다
- 특별히 온-프레미스인 Lync를 사용하고 있습니다
- Azure Multi-Factor Authentication을 사용하고 있습니다


![검사](./media/multi-factor-authentication-whats-next/federated.png)

 이러한 경우 다음을 수행해야 합니다.

- Lync에 로그인하는 경우 조직의 사용자 이름 및 암호를 사용합니다.
- 온라인으로 Exchange에 연결하는 Outlook 클라이언트를 통해 주소록에 액세스하려는 경우 앱 암호를 사용합니다.

### 앱 암호 만들기 허용
기본적으로 사용자가 앱 암호를 만들 수 없습니다. 이 기능을 사용하도록 설정해야 합니다. 사용자가 앱 암호를 만들 수 있으려면 다음 절차를 수행합니다.

#### 사용자가 앱 암호를 만들 수 있으려면



1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. 디렉터리 아래에서 사용하도록 설정하려는 사용자에 대한 디렉터리 클릭합니다.
4. 위쪽에서 사용자를 클릭합니다.
5. 페이지의 아래쪽에서 Multi-Factor Auth 관리를 클릭합니다. Multi-Factor Auth 페이지를 엽니다.
6. Multi-factor Authentication 페이지의 위쪽에서 서비스 설정을 클릭합니다.
7. 사용자가 비 브라우저 응용 프로그램에 로그인하도록 앱 암호를 만들 수 있습니다 옆의 라디오 단추가 선택되어 있는지 확인합니다.


![앱 암호 만들기](./media/multi-factor-authentication-whats-next/trustedips3.png)

### 앱 암호 만들기
사용자가 초기 등록을 하는 동안 앱 암호를 만들 수 있습니다. 등록 프로세스를 스스로 만들 수 있도록 하는 등록 프로세스의 끝에 옵션이 제공됩니다.

또한 Azure 포털, Office 365 포털에서 설정을 변경하거나 하여 사용자가 나중에 앱 암호를 만들 수 있습니다

### Office 365 포털에서 앱 암호를 만들려면
--------------------------------------------------------------------------------


1. Office 365 포털에 로그인
2. 오른쪽 위 구석에서 설정 위젯을 선택합니다.
3. 왼쪽에서 추가 보안 확인을 선택합니다.
4. 오른쪽의 **Update my phone numbers used for account security(계정 보안에 사용되는 전화 번호 업데이트)**를 선택합니다.
5. 검사 페이지 위쪽에서 앱 암호를 선택합니다.
6. **만들기**를 클릭합니다.
7. 앱 암호의 이름을 입력하고 **다음**을 클릭합니다.
8. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.

<center>![클라우드](./media/multi-factor-authentication-whats-next/security.png)</center>


### Azure 포털에서 앱 암호를 만들려면
--------------------------------------------------------------------------------
1. Azure 클래식 포털에 로그인합니다.
3. 위쪽에서 사용자 이름을 마우스 오른쪽 단추로 클릭하고 추가 보안 확인을 선택합니다.
5. 검사 페이지 위쪽에서 앱 암호를 선택합니다.
6. **만들기**를 클릭합니다.
7. 앱 암호의 이름을 입력하고 **다음**을 클릭합니다.
8. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.


![앱 암호](./media/multi-factor-authentication-whats-next/app2.png)

### Office 365 또는 Azure 구독이 없는 경우 앱 암호를 만들려면
--------------------------------------------------------------------------------
1. [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.
2. 위쪽에서 프로필을 선택합니다.
3. 사용자 이름을 클릭하고 추가 보안 확인을 선택합니다.
5. 검사 페이지 위쪽에서 앱 암호를 선택합니다.
6. **만들기**를 클릭합니다.
7. 앱 암호의 이름을 입력하고 **다음**을 클릭합니다.
8. 앱 암호를 클립보드에 복사하고 앱에 붙여 넣습니다.

![앱 암호](./media/multi-factor-authentication-whats-next/myapp.png)

## 사용자가 신뢰하는 장치에 대한 Multi-Factor Authentication 기억

사용자가 신뢰하는 장치 및 브라우저에 대한 Multi-Factor Authentication 기억은 모든 MFA 사용자에 대해 무료로 사용할 수 있는 기능입니다. 이를 통해 사용자에게 MFA를 사용하여 성공적으로 로그인한 후 설정된 일수 동안 MFA를 바이패스하는 옵션을 제공할 수 있습니다. 이 기능은 사용자의 가용성을 강화할 수 있습니다.

그러나 신뢰할 수 있는 장치에 대한 MFA를 기억하도록 하기 때문에 이 기능은 계정 보안을 저하시킬 수 있습니다. 계정의 보안이 유지되도록 장치에서 다음의 시나리오 중 하나에 대해 Multi-Factor Authentication을 복원해야 합니다.

- 자신의 회사 계정이 손상될 경우
- 기억된 장치를 분실하거나 도난당한 경우

> [AZURE.NOTE] 이 기능은 브라우저 쿠키 캐시로 구현됩니다. 브라우저 쿠키를 사용할 수 없는 경우 작동하지 않습니다.

### 다단계 인증 기억을 사용하거나 사용하지 않는 방법

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. Active Directory 아래에서 장치에 대한 Multi-Factor Authentication 기억을 설정하려는 디렉터리를 클릭합니다.
4. 선택한 디렉터리에서 구성을 클릭합니다.
5. Multi-factor Authentication 섹션에서 서비스 설정 관리를 클릭합니다.
6. 서비스 설정 페이지의 사용자 장치 설정 관리 아래에서 **사용자가 신뢰하는 장치에 다단계 인증을 기억하도록 허용**을 선택/선택 취소합니다. ![장치 기억](./media/multi-factor-authentication-whats-next/remember.png)
8. 일시 중단을 허용하려는 일 수를 설정합니다. 기본값은 14일입니다.
9. 저장을 클릭합니다.
10. 닫기를 클릭합니다.


## 선택 가능한 확인 방법
이제 Azure Multi-factor Authentication을 사용할 때 사용자에게 제공되는 인증 방법을 선택할 수 있게 되었습니다. 이 기능은 이전에 온-프레미스 서버 버전에만 제공되었습니다. 아래 테이블은 사용자에게 활성화 또는 비활성화할 수 있는 다양한 인증 방법을 개략적으로 보여 줍니다.

메서드|설명
:------------- | :------------- |
[휴대폰에 전화 걸기](multi-factor-authentication-end-user-first-time-mobile-phone.md)| 인증 휴대폰으로 자동 음성 전화를 겁니다. 사용자가 전화를 받고 휴대폰 키패드에서 #을 눌러 인증합니다. 이 전화 번호는 온-프레미스 Active Directory와 동기화되지 않습니다.
[휴대폰에 문자 메시지 전송](multi-factor-authentication-end-user-first-time-mobile-phone.md)|확인 코드를 포함하는 문자 메시지를 사용자에게 보냅니다. 사용자는 확인 코드로 문자 메시지에 응답하거나 또는 로그인 인터페이스에 확인 코드를 입력하도록 요구됩니다.
[모바일 앱을 통한 알림](multi-factor-authentication-end-user-first-time-mobile-app.md)|이 모드에서는 Microsoft Authenticator 앱이 계정에 대한 무단 액세스를 방지하고 사기성 트랜잭션을 중지합니다. 이 작업은 휴대폰이나 등록된 장치로 푸시 알림을 보내는 방식으로 수행됩니다. 알림을 확인한 후 올바르면 확인을 누릅니다. 그렇지 않은 경우 거부를 선택하거나 사기성 알림을 거부한 후 보고하도록 선택할 수 있습니다. 사기성 알림을 보고하는 방법에 대한 자세한 내용은 Multi-Factor Authentication에 사기 행위 거부 및 보고 기능을 사용하는 방법을 참조하세요.</br></br>Microsoft Authenticator 앱은 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)에 제공됩니다.|
[모바일 앱의 확인 코드](multi-factor-authentication-end-user-first-time-mobile-app.md)|이 모드에서는 Microsoft Authenticator 앱을 소프트웨어 토큰으로 사용하여 OATH 확인 코드를 생성할 수 있습니다. 이 확인 코드를 사용자 이름 및 암호와 함께 입력하여 두 번째 형식의 인증을 제공할 수 있습니다.</li><br><p> [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

### 인증 방법을 활성화/비활성화하는 방법

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽에서 Active Directory를 클릭합니다.
3. Active Directory에서 인증 방법을 활성화 또는 비활성화하려는 디렉터리를 클릭합니다.
4. 선택한 디렉터리에서 구성을 클릭합니다.
5. Multi-factor Authentication 섹션에서 서비스 설정 관리를 클릭합니다.
6. 서비스 설정 페이지의 확인 옵션에서 사용할 옵션을 선택/선택 취소합니다.</br></br> ![확인 옵션](./media/multi-factor-authentication-whats-next/authmethods.png)
9. 저장을 클릭합니다.
10. 닫기를 클릭합니다.

<!---HONumber=AcomDC_0921_2016-->