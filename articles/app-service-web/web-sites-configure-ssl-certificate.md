<properties
	pageTitle="HTTPS를 사용하여 앱의 사용자 지정 도메인 보안 유지 | Microsoft Azure"
	description="SSL 인증서 바인딩을 구성하여 Azure 앱 서비스에서 앱에 대한 사용자 지정 도메인 이름의 보안을 유지하는 방법을 알아봅니다. 또한 여러 도구에서 SSL 인증서를 가져오는 방법을 알아봅니다."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cephalin"/>

# HTTPS를 사용하여 앱의 사용자 지정 도메인 보안 유지


> [AZURE.SELECTOR]
- [Azure에서 SSL 인증서 구입](web-sites-purchase-ssl-web-site.md)
- [다른 곳에서 SSL 인증서 사용](web-sites-configure-ssl-certificate.md)


이 문서에서는 사용자 지정 도메인 이름을 사용하는 웹앱, 모바일 앱 백 엔드 또는 [Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)의 API 앱에 HTTPS를 사용하도록 설정하는 방법을 보여 줍니다. 여기서는 서버 전용 인증을 다룹니다. 상호 인증(클라이언트 인증 포함)이 필요한 경우 [앱 서비스에 대한 TLS 상호 인증을 구성하는 방법](app-service-web-configure-tls-mutual-auth.md)을 참조하세요.

HTTPS를 사용하여 사용자 지정 도메인 이름이 있는 앱의 보안을 유지하려면 해당 도메인 이름에 대한 인증서를 추가합니다. 기본적으로 Azure는 단일 SSL 인증서를 사용하여 ***.azurewebsites.net** 와일드카드 도메인의 보안을 유지하므로 클라이언트는 이미 **https://*&lt;appname>*.azurewebsites.net**에서 앱에 액세스할 수 있습니다. 그러나 **contoso.com**, **www.contoso.com** 및 ***.contoso.com**과 같은 사용자 지정 도메인을 사용하려는 경우에는 기본 인증서로 보안을 유지할 수 없습니다. 또한 모든 [와일드카드 인증](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)와 마찬가지로 기본 인증서는 사용자 지정 도메인과 해당 도메인용 인증서를 사용하는 것보다 안전하지 않습니다.

>[AZURE.NOTE] 언제든지 [Azure 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가의 도움을 받을 수 있습니다. 보다 개별화된 지원을 원하는 경우 [Azure 지원](https://azure.microsoft.com/support/options/)으로 이동하여 **지원 받기**를 클릭합니다.

<a name="bkmk_domainname"></a>
## 필요한 항목
HTTPS를 사용하여 사용자 지정 도메인 이름의 보안을 유지하려면 Azure에서 사용자 지정 SSL 인증서를 해당 사용자 지정 도메인에 바인딩합니다. 사용자 지정 인증서를 바인딩하기 전에 먼저 다음을 수행해야 합니다.

- **사용자 지정 도메인 구성** - 앱 서비스에서는 앱에 이미 구성된 도메인 이름에 대한 인증서만 추가하도록 허용합니다. 지침은 [Azure 앱에 사용자 지정 도메인 이름 매핑](web-sites-custom-domain-name.md)을 참조하세요.
- **기본 계층 이상으로 강화** - 낮은 가격 책정 계층의 앱 서비스 계획은 사용자 지정 SSL 인증서를 지원하지 않습니다. 지침은 [Azure에서 앱 강화](web-sites-scale.md)를 참조하세요.
- **SSL 인증서 가져오기** - 아직 없는 경우 신뢰할 수 있는 CA([인증 기관](http://en.wikipedia.org/wiki/Certificate_authority))에서 받아야 합니다. 인증서는 다음 요구 사항을 모두 충족해야 합니다.

	- 개인 CA 서버가 아니라 신뢰할 수 있는 CA가 서명한 것이어야 합니다.
	- 개인 키가 포함되어 있어야 합니다.
	- 키 교환용으로 만들어졌으며 .PFX 파일로 내보낼 수 있어야 합니다.
	- 최소 2048비트 암호화를 사용해야 합니다.
	- 해당 주체 이름이 보안을 유지해야 하는 사용자 지정 도메인과 일치해야 합니다. 여러 도메인을 하나의 인증서로 보안을 유지하려면 와일드 카드 이름(예: ***. contoso.com**)을 사용하거나 subjectAltName 값을 지정하면 됩니다.
	- 이는 CA에서 사용하는 모든 **[중간 인증서](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**와 병합됩니다. 그렇지 않으면 일부 클라이언트에서 재생 불가능한 상호 운용성 문제가 발생할 수 있습니다.

		>[AZURE.NOTE] 모든 요구 사항을 충족하는 SSL 인증서를 얻는 가장 쉬운 방법은 [Azure 포털에서 직접 구입](web-sites-purchase-ssl-web-site.md)하는 것입니다. 이 문서에서는 수동으로 이 작업을 수행한 다음 앱 서비스에서 사용자 지정 도메인에 바인딩하는 방법을 보여 줍니다.
		>	
		> **ECC(타원 곡선 암호화) 인증서**는 앱 서비스에서 사용할 수 있지만 이 문서의 범위를 벗어납니다. ECC 인증서를 만드는 정확한 단계에서 CA를 사용하세요.

<a name="bkmk_getcert"></a>
## 1단계. SSL 인증서 가져오기

CA는 다양한 가격의 여러 SSL 인증서 유형을 제공하므로 먼저 구입할 SSL 인증서 유형을 결정해야 합니다. 단일 도메인 이름(**www.contoso.com**)의 보안을 유지하려면 기본 인증서만 있으면 됩니다. 여러 도메인 이름(**contoso.com** *and* **www.contoso.com** *및* **mail.contoso.com**)의 보안을 유지하려면 [와일드카드 인증서](http://en.wikipedia.org/wiki/Wildcard_certificate) 또는 [주체 대체 이름](http://en.wikipedia.org/wiki/SubjectAltName)(`subjectAltName`)이 포함된 인증서가 필요합니다.

구입할 SSL 인증서를 알았으면 CA에 CSR(인증서 서명 요청)을 제출합니다. CA에서 요청한 인증서를 다시 가져온 경우 인증서에서 .pfx 파일을 생성합니다. 선택한 도구를 사용하여 이러한 단계를 수행할 수 있습니다. 다음은 일반적인 도구에 대한 지침입니다.

- [Certreq.exe 단계](#bkmk_certreq) - 인증서 요청을 만들기 위한 Windows 유틸리티입니다. Windows XP/Windows Server 2000 이후 Windows의 일부가 되었습니다.
- [IIS 관리자 단계](#bkmk_iismgr) - 이미 익숙한 경우에 선택하는 도구입니다.
- [OpenSSL 단계](#bkmk_openssl) - [오픈 소스, 크로스 플랫폼 도구](https://www.openssl.org)입니다. 이 도구를 사용하면 모든 플랫폼에서 SSL 인증서를 가져올 수 있습니다.
- [OpenSSL을 사용하는 subjectAltName 단계](#bkmk_subjectaltname) -`subjectAltName` 인증서를 가져오는 단계입니다.

인증서를 구입하기 전에 앱 서비스에서 설정을 테스트하려는 경우 [자체 서명된 인증서](https://en.wikipedia.org/wiki/Self-signed_certificate)를 생성할 수 있습니다. 이 자습서에서는 인증서를 생성하는 두 가지 방법을 제공합니다.

- [자체 서명된 인증서, Certreq.exe 단계](#bkmk_sscertreq)
- [자체 서명된 인증서, OpenSSL 단계](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### Certreq.exe를 사용하여 인증서 받기

1. 파일(예: **myrequest.txt**)을 만들고 다음 텍스트를 복사한 다음 작업 디렉터리에 저장합니다. `<your-domain>` 자리 표시자를 앱의 사용자 지정 도메인 이름으로 바꿉니다.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; Required minimum is 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	CSR의 옵션 및 사용 가능한 기타 옵션에 대한 자세한 내용은 [Certreq 참조 설명서](https://technet.microsoft.com/library/dn296456.aspx)를 참조하세요.

4. 명령 프롬프트에서 작업 디렉터리로 `CD`한 후 다음 명령을 실행하여 CSR을 만듭니다.

		certreq -new myrequest.txt myrequest.csr

	이제 **myrequest.csr**이 현재 작업 디렉터리에 생성되었습니다.

5. **myrequest.csr**을 CA에 제출하여 SSL 인증서를 가져옵니다. 웹 양식으로 파일을 업로드하거나 텍스트 편집기에서 해당 콘텐츠를 복사합니다.

	Microsoft에서 신뢰할 수 있는 CA 목록은 [Microsoft에서 신뢰할 수 있는 루트 인증서 프로그램: 참가자][cas]를 참조하세요.

6. CA가 인증서(.CER) 파일로 응답한 경우 작업 디렉터리에 저장합니다. 다음 명령을 실행하여 보류 중인 CSR을 완료합니다.

		certreq -accept -user <certificate-name>.cer

	이 명령은 완성된 인증서를 Windows 인증서 저장소에 저장합니다.

6. CA에서 중간 인증서를 사용하는 경우 먼저 이를 설치한 다음 계속 진행합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용 버전을 선택합니다.

	인증서를 다운로드한 후 Windows 탐색기에서 각 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

7. 인증서 저장소에서 SSL 인증서를 내보내려면 `Win`+`R`를 누르고 **certmgr.msc**를 실행하여 인증서 관리자를 시작합니다. **개인** > **인증서**를 선택합니다. **발급 대상** 열에 사용자 지정 도메인 이름이 있는 항목이 표시되고, **발급자** 열에 인증서를 생성하는 데 사용한 CA가 표시됩니다.

	![여기에 인증서 관리자 이미지 삽입][certmgr]

9. 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업** > **내보내기**를 선택합니다. **인증서 내보내기 마법사**에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 다시 클릭합니다.

	![개인 키 내보내기][certwiz1]

10. **개인 정보 교환 - PKCS #12**, **가능한 경우 인증 경로에 있는 인증서 모두 포함** 및 **확장된 속성 모두 내보내기**를 선택합니다. 그런 후 **다음**을 클릭합니다.

	![모든 인증서 및 확장 속성 포함][certwiz2]

11. **암호**를 선택한 후 암호를 입력하고 확인합니다. **다음**을 클릭합니다.

	![암호 지정][certwiz3]

12. 내보낸 인증서의 경로 및 파일 이름을 확장명 **.pfx**와 함께 제공합니다. **다음**을 클릭하여 완료합니다.

	![파일 경로 제공][certwiz4]

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_iismgr"></a>
### IIS 관리자를 사용하여 인증서 받기

1. IIS 관리자를 사용하여 CA에 보낼 CSR을 생성합니다. CSR 생성에 대한 자세한 내용은 [인터넷 서버 인증서 요청(IIS 7)][iiscsr]을 참조하세요.

3. SSL 인증서를 가져오려면 CA에 CSR을 제출합니다. Microsoft에서 신뢰할 수 있는 CA 목록은 [Microsoft에서 신뢰할 수 있는 루트 인증서 프로그램: 참가자][cas]를 참조하세요.


3. CA에서 반환하는 인증서로 CSR을 완료합니다. CSR 완료에 대한 자세한 내용은 [인터넷 서버 인증서 설치(IIS 7)][installcertiis]를 참조하세요.

4. CA에서 중간 인증서를 사용하는 경우 먼저 이를 설치한 다음 계속 진행합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. Microsoft IIS용 버전을 선택합니다.

	인증서를 다운로드한 후 Windows 탐색기에서 각 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치**를 선택합니다. **인증서 가져오기 마법사**에서 기본값을 사용하고 가져오기가 완료될 때까지 **다음**을 계속 선택합니다.

4. IIS 관리자에서 SSL 인증서를 내보냅니다. 인증서 내보내기에 대한 자세한 내용은 [서버 인증서 내보내기(IIS 7)][exportcertiis]를 참조하세요.

	>[AZURE.IMPORTANT] **인증서 내보내기 마법사**에서 **예, 개인 키를 내보냅니다.**가 선택되어 있는지 확인합니다.
	>
	>![개인 키 내보내기][certwiz1]
	>
	> 또한 **개인 정보 교환 - PKCS #12**, **가능한 경우 인증 경로에 있는 인증서 모두 포함** 및 **확장된 속성 모두 내보내기**를 선택합니다.
	>
	>![모든 인증서 및 확장 속성 포함][certwiz2]

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_openssl"></a>
### OpenSSL을 사용하여 인증서 받기

1. 명령줄 터미널에서 작업 디렉터리로 `CD`한 후 다음 명령을 실행하여 개인 키와 CSR을 생성합니다.

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. 메시지가 표시되면 해당 정보를 입력합니다. 예:

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	완료되면 작업 디렉터리에 두 개의 파일 **myserver.key** 및 **server.csr**이 생성됩니다. **server.csr**에 CSR이 포함되어 있으며 **myserver.key**는 나중에 필요합니다.

3. SSL 인증서를 가져오려면 CA에 CSR을 제출합니다. Microsoft에서 신뢰할 수 있는 CA 목록은 [Microsoft에서 신뢰할 수 있는 루트 인증서 프로그램: 참가자][cas]를 참조하세요.


4. CA에서 요청한 인증서를 보내면 작업 디렉터리의 **myserver.crt**라는 파일에 저장합니다. CA에서 텍스트 형식으로 인증서를 제공하는 경우에는 텍스트 편집기에서 **myserver.crt**로 콘텐츠를 복사하여 저장하면 됩니다. 파일은 다음과 같아야 합니다.

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. 명령줄 터미널에서 다음 명령을 실행하여 **myserver.key** 및 **myserver.crt**에서 **myserver.pfx**를 내보냅니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

	> [AZURE.NOTE] CA에서 중간 인증서를 사용하는 경우 `-certfile` 매개 변수에 이를 포함해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. 확장명이 `.pem`인 버전을 선택합니다.
	>
	> `openssl -export` 명령은 다음 예와 같이 **intermediate-cets.pem** 파일에서 중간 인증서가 포함된 .pfx 파일을 만듭니다.
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_subjectaltname"></a>
### OpenSSL을 사용하여 SubjectAltName 인증서 받기

1. **sancert.cnf**라는 파일을 만들고 다음 텍스트를 복사한 다음 작업 디렉터리에 저장합니다.

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	`subjectAltName`으로 시작하는 줄에서 보안을 유지하려는 모든 도메인 이름으로 값을 바꿉니다(`commonName`과 함께). 예:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	`commonName`을 포함하여 다른 필드를 변경할 필요는 없습니다. 다음 몇 단계에서 지정하라는 메시지가 표시됩니다.

1. 명령줄 터미널에서 작업 디렉터리로 `CD`한 후 다음 명령을 실행합니다.

		openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. 메시지가 표시되면 해당 정보를 입력합니다. 예:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

	완료되면 작업 디렉터리에 두 개의 파일 **myserver.key** 및 **server.csr**이 생성됩니다. **server.csr**에 CSR이 포함되어 있으며 **myserver.key**는 나중에 필요합니다.

3. SSL 인증서를 가져오려면 CA에 CSR을 제출합니다. Microsoft에서 신뢰할 수 있는 CA 목록은 [Microsoft에서 신뢰할 수 있는 루트 인증서 프로그램: 참가자][cas]를 참조하세요.


4. CA에서 요청한 인증서를 보내면 **myserver.crt**라는 파일에 저장합니다. CA에서 텍스트 형식으로 인증서를 제공하는 경우에는 텍스트 편집기에서 **myserver.crt**로 콘텐츠를 복사하여 저장하면 됩니다. 파일은 다음과 같아야 합니다.

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

5. 명령줄 터미널에서 다음 명령을 실행하여 **myserver.key** 및 **myserver.crt**에서 **myserver.pfx**를 내보냅니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

	> [AZURE.NOTE] CA에서 중간 인증서를 사용하는 경우 `-certfile` 매개 변수에 이를 포함해야 합니다. 일반적으로 이 인증서는 CA에서 개별 다운로드로 제공되며, 각 웹 서버 유형에 따라 여러 형식으로 제공됩니다. 확장명이 `.pem`인 버전을 선택합니다.
	>
	> `openssl -export` 명령은 다음 예와 같이 **intermediate-cets.pem** 파일에서 중간 인증서가 포함된 .pfx 파일을 만듭니다.
	>  
	> `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_sscertreq"></a>
### Certreq.exe를 사용하여 자체 서명된 인증서 생성 ###

>[AZURE.IMPORTANT] 자체 서명된 인증서는 테스트 전용입니다. 대부분의 브라우저는 자체 서명된 인증서로 보안이 유지되는 웹 사이트를 방문할 경우 오류를 반환합니다. 일부 브라우저는 사이트 이동을 거절할 수도 있습니다.

1. 텍스트 파일(예: **mycert.txt**)을 만들고 다음 텍스트를 복사한 다음 작업 디렉터리에 저장합니다. `<your-domain>` 자리 표시자를 앱의 사용자 지정 도메인 이름으로 바꿉니다.

		[NewRequest]
		Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
		Exportable = TRUE
		KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		HashAlgorithm = SHA256
		RequestType = Cert            ; Self-signed certificate
		ValidityPeriod = Years
		ValidityPeriodUnits = 1

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

	중요한 매개 변수는 자체 서명된 인증서를 지정하는 `RequestType = Cert`입니다. CSR의 옵션 및 사용 가능한 기타 옵션에 대한 자세한 내용은 [Certreq 참조 설명서](https://technet.microsoft.com/library/dn296456.aspx)를 참조하세요.

4. 명령 프롬프트에서 작업 디렉터리로 `CD`한 후 다음 명령을 실행합니다.

		certreq -new mycert.txt mycert.crt
	
	이제 새 자체 서명된 인증서가 인증서 저장소에 설치됩니다.

7. 인증서 저장소에서 인증서를 내보내려면 `Win`+`R`를 누르고 **certmgr.msc**를 실행하여 인증서 관리자를 시작합니다. **개인** > **인증서**를 선택합니다. **발급 대상** 열에 사용자 지정 도메인 이름이 있는 항목이 표시되고, **발급자** 열에 인증서를 생성하는 데 사용한 CA가 표시됩니다.

	![여기에 인증서 관리자 이미지 삽입][certmgr]

9. 인증서를 마우스 오른쪽 단추로 클릭하고 **모든 작업** > **내보내기**를 선택합니다. **인증서 내보내기 마법사**에서 **다음**을 클릭하고 **예, 개인 키를 내보냅니다.**를 선택한 후 **다음**을 다시 클릭합니다.

	![개인 키 내보내기][certwiz1]

10. **개인 정보 교환 - PKCS #12**, **가능한 경우 인증 경로에 있는 인증서 모두 포함** 및 **확장된 속성 모두 내보내기**를 선택합니다. 그런 후 **다음**을 클릭합니다.

	![모든 인증서 및 확장 속성 포함][certwiz2]

11. **암호**를 선택한 후 암호를 입력하고 확인합니다. **다음**을 클릭합니다.

	![암호 지정][certwiz3]

12. 내보낸 인증서의 경로 및 파일 이름을 확장명 **.pfx**와 함께 제공합니다. **다음**을 클릭하여 완료합니다.

	![파일 경로 제공][certwiz4]

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_ssopenssl"></a>
###OpenSSL을 사용하여 자체 서명된 인증서 생성 ###

>[AZURE.IMPORTANT] 자체 서명된 인증서는 테스트 전용입니다. 대부분의 브라우저는 자체 서명된 인증서로 보안이 유지되는 웹 사이트를 방문할 경우 오류를 반환합니다. 일부 브라우저는 사이트 이동을 거절할 수도 있습니다.

1. **serverauth.cnf**라는 텍스트 파일을 만들고 다음 콘텐츠를 복사한 다음 작업 디렉터리에 저장합니다.

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. 명령줄 터미널에서 작업 디렉터리로 `CD`한 후 다음 명령을 실행합니다.

		openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	이 명령은 **serverauth.cnf**의 설정에 따라 두 개의 파일 **myserver.crt**(자체 서명된 인증서) 및 **myserver.key**(개인 키)를 만듭니다.

3. 다음 명령을 실행하여 인증서를 .pfx 파일로 내보냅니다.

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	메시지가 표시되면 암호를 입력하여 .pfx 파일의 보안을 설정합니다.

이제 내보낸 PFX 파일을 앱 서비스에 업로드할 준비가 되었습니다. [2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩](#bkmk_configuressl)을 참조하세요.

<a name="bkmk_configuressl"></a>
## 2단계. 사용자 지정 SSL 인증서 업로드 및 바인딩

계속하기 전에 [필요한 항목](#bkmk_domainname) 섹션을 검토하고 다음을 확인합니다.

- Azure 앱에 매핑되는 사용자 지정 도메인이 있습니다.
- 앱이 **기본** 계층 이상에서 실행 중입니다.
- CA로부터 사용자 지정 도메인에 대한 SSL 인증서를 받았습니다.


1. 브라우저에서 **[Azure 포털](https://portal.azure.com/)**을 엽니다.
2.	페이지의 왼쪽에서 **앱 서비스** 옵션을 클릭합니다.
3.	이 인증서를 할당하려는 앱의 이름을 클릭합니다.
4.	**설정**에서 **SSL 인증서**를 클릭합니다.
5.	**인증서 업로드**를 클릭합니다.
6.	[1단계](#bkmk_getcert)에서 내보낸 .pfx 파일을 선택하고 이전에 만든 암호를 지정합니다. 그런 후 **업로드**를 클릭하여 인증서를 업로드합니다. 이제 업로드된 인증서가 **SSL 인증서** 블레이드에 다시 표시됩니다.
7. **ssl 바인딩** 섹션에서 **바인딩 추가**를 클릭합니다.
8. **ssl bindings** 블레이드에서 드롭다운을 사용하여 SSL로 보안을 설정할 도메인 이름과 사용할 인증서를 선택합니다. **[SNI(서버 이름 표시)](http://en.wikipedia.org/wiki/Server_Name_Indication)**를 사용할지 또는 IP 기반 SSL을 사용할지 선택할 수도 있습니다.

    ![SSL 바인딩 이미지 삽입](./media/web-sites-configure-ssl-certificate/sslbindings.png)

       • IP 기반 SSL은 서버의 전용 공용 IP 주소를 도메인 이름에 매핑하여 인증서를 도메인 이름과 연결합니다. 이렇게 하려면 서비스와 연결된 각 도메인 이름(contoso.com, fabricam.com 등)에 전용 IP 주소가 있어야 합니다. 이 방법은 SSL 인증서를 웹 서버와 연결하는 일반적인 방법입니다. • SNI 기반 SSL은 SSL 및 TLS(**[전송 계층 보안](http://en.wikipedia.org/wiki/Transport_Layer_Security)**)에 대한 확장으로, 각 도메인에 별도의 보안 인증서를 사용하여 여러 도메인이 동일한 IP 주소를 공유할 수 있게 해 줍니다. 대부분의 최신 브라우저(Internet Explorer, Chrome, Firefox 및 Opera 포함)는 SNI를 지원하지만 이전 브라우저는 SNI를 지원하지 않을 수도 있습니다. SNI에 대한 자세한 내용은 Wikipedia의 **[서버 이름 표시](http://en.wikipedia.org/wiki/Server_Name_Indication)** 문서를 참조하세요.
     
9. **바인딩 추가**를 클릭하여 변경 내용을 저장하고 SSL을 사용하도록 설정합니다.

## 3단계. 도메인 이름 매핑 변경(IP 기반 SSL에만 해당)

**SNI SSL** 바인딩만 사용하는 경우 이 섹션을 건너뛰세요. 여러 **SNI SSL** 바인딩을 앱에 할당된 기존 공유 IP 주소에서 함께 사용할 수 있습니다. 그러나 **IP 기반 SSL** 바인딩을 만든 경우 앱 서비스에서는 바인딩 전용 IP 주소(**IP 기반 SSL**에 필요하기 때문)를 만듭니다. 하나의 전용 IP 주소만을 만들 수 있으므로 하나의 **IP 기반 SSL** 바인딩만 추가할 수 있습니다.

이 전용 IP 주소로 인해 다음에 해당하는 경우 앱을 추가로 구성해야 합니다.

- [A 레코드를 사용하여 사용자 지정 도메인을 Azure 앱에 매핑](web-sites-custom-domain-name.md#a)하고 **IP 기반 SSL** 바인딩을 추가했습니다. 이 시나리오에서는 다음 단계에 따라 전용 IP 주소를 가리키도록 기존 A 레코드를 다시 매핑해야 합니다.

	1. IP 기반 SSL 바인딩을 구성하면 앱에 전용 IP 주소가 할당됩니다. **호스트 이름** 섹션.바로 위에 있는 앱 설정 아래 **사용자 지정 도메인** 페이지에서 이 IP 주소를 확인할 수 있습니다. **외부 IP 주소**로 나열됩니다.
    
	    ![가상 IP 주소](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)

	2. [사용자 지정 도메인 이름에 대한 A 레코드를 이 새 IP 주소에 다시 매핑합니다](web-sites-custom-domain-name.md#a).

- 앱에 이미 하나 이상의 **SNI SSL** 바인딩이 있으므로 **IP 기반 SSL** 바인딩을 추가하기만 했습니다. 바인딩이 완료되면 *&lt;appname>*.azurewebsites.net 도메인 이름이 새 IP 주소를 가리킵니다. 따라서 **SNI SSL** 바인딩을 포함하여 *&lt;appname>*.azurewebsites.net과의 모든 기존 [사용자 지정 도메인 CNAME 매핑](web-sites-custom-domain-name.md#cname)도 **IP 기반 SSL** 전용으로 생성된 새 주소에서 트래픽을 받습니다. 이 시나리오에서는 다음 단계에 따라 원래 공유 IP 주소로 **SNI SSL** 트래픽을 다시 보내야 합니다.

	1. **SNI SSL** 바인딩이 있는 앱에 대한 모든 [사용자 지정 도메인 CNAME 매핑](web-sites-custom-domain-name.md#cname)을 확인합니다.

	2. 각 CNAME 레코드를 &lt;appname>.azurewebsites.net 대신 **sni.**&lt;appname>.azurewebsites.net에 다시 매핑합니다.

## 4단계. 사용자 지정 도메인에 대한 HTTPS 테스트

이제 HTTPS가 사용자 지정 도메인에 작동하는지 확인하는 작업만 남았습니다. 다양한 브라우저에서 `https://<your.custom.domain>`으로 이동하여 앱을 처리하는지 확인합니다.

- 앱에서 인증서 유효성 검사 오류가 발생한 경우 자체 서명된 인증서를 사용하고 있을 수도 있습니다.

- 그렇지 않으면 .pfx 인증서를 내보낼 때 중간 인증서를 생략했을 수도 있습니다. [필요한 항목](#bkmk_domainname)으로 돌아가 CSR이 앱 서비스의 모든 요구 사항을 충족하는지 확인합니다.

<a name="bkmk_enforce"></a>
## 앱에 HTTPS 적용

앱에 대한 HTTP 액세스를 계속 허용하려면 이 단계를 건너뜁니다. 앱 서비스에서 HTTPS를 적용하지 *않으므로* 방문자는 HTTP를 사용하여 앱에 계속 액세스할 수 있습니다. 앱에 HTTPS를 적용하려면 앱의 `web.config`에서 다시 쓰기 규칙을 정의하면 됩니다. 앱의 언어 프레임워크에 관계없이 모든 앱 서비스 앱에 이 파일이 있습니다.

> [AZURE.NOTE] 언어별 요청 리디렉션이 있습니다. ASP.NET MVC는 `web.config`의 다시 쓰기 규칙 대신 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 필터를 사용할 수 있습니다([웹앱에 보안 ASP.NET MVC 5 앱 배포](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) 참조).

다음 단계를 수행하세요.

1. 앱에 대한 Kudu 디버그 콘솔로 이동합니다. 해당 주소는 `https://<appname>.scm.azurewebsites.net/DebugConsole`입니다.

2. 디버그 콘솔에서 `D:\home\site\wwwroot`로 CD합니다.

3. 연필 단추를 클릭하여 `web.config`를 엽니다.

	![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

	Visual Studio 또는 Git를 사용하여 앱을 배포하는 경우 앱 서비스는 응용 프로그램 루트에 .NET, PHP, Node.js 또는 Python 앱에 대한 적절한 `web.config`를 자동으로 생성합니다. `web.config`가 없으면 웹 기반 명령 프롬프트에서 `touch web.config`를 실행하여 만듭니다. 또는 로컬 프로젝트에서 만들어 코드를 다시 배포할 수 있습니다.

4. `web.config`를 만들어야 하는 경우 다음 코드를 복사한 후 저장합니다. 기존 web.config를 연 경우 전체 `<rule>` 태그를 `web.config`의 `configuration/system.webServer/rewrite/rules` 요소에 복사하기만 하면 됩니다.

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <rewrite>
		      <rules>
			    <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
		        <rule name="Force HTTPS" enabled="true">
		          <match url="(.*)" ignoreCase="false" />
		          <conditions>
		            <add input="{HTTPS}" pattern="off" />
		          </conditions>
		          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
		        </rule>
				<!-- END rule TAG FOR HTTPS REDIRECT -->
		      </rules>
		    </rewrite>
          </system.webServer>
		</configuration>

	이 규칙에서는 사용자가 HTTP를 사용하여 페이지를 요청할 때마다 HTTPS 프로토콜에 HTTP 301(영구 리디렉션)을 반환합니다. http://contoso.com에서 https://contoso.com으로 리디렉션합니다.

	>[AZURE.IMPORTANT] `web.config`에 다른 `<rule>` 태그가 이미 있는 경우 복사한 `<rule>` 태그를 다른 `<rule>` 태그 앞에 배치합니다.

4. Kudu 디버그 콘솔에서 파일을 저장합니다. 모든 요청이 HTTPS로 즉시 리디렉션됩니다.

IIS URL 다시 쓰기 모듈에 대한 자세한 내용은 [URL 다시 쓰기](http://www.iis.net/downloads/microsoft/url-rewrite)(영문) 설명서를 참조하세요.

## 추가 리소스 ##
- [Microsoft Azure 보안 센터](/support/trust-center/security/)
- [Azure 웹 사이트에서 잠금 해제된 구성 옵션](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [진단 로깅 사용](web-sites-enable-diagnostic-log.md)
- [Azure 앱 서비스에서 웹앱 구성](web-sites-configure.md)
- [Azure 관리 포털](https://manage.windowsazure.com)

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png

<!---HONumber=AcomDC_0914_2016-->