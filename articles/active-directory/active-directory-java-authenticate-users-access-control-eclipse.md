<properties
    pageTitle="액세스 제어 사용 방법(Java) | Microsoft Azure"
    description="Azure에서 Java를 사용하여 액세스 제어를 개발하고 사용하는 방법에 대해 알아봅니다."
	services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# Eclipse를 사용하여 Azure 액세스 제어 서비스를 통해 웹 사용자를 인증하는 방법

이 가이드에서는 Eclipse용 Azure 도구 키트 내에서 Azure ACS(액세스 제어 서비스)를 사용하는 방법을 보여 줍니다. ACS에 대한 자세한 내용은 [다음 단계](#next_steps) 섹션을 참조하세요.

> [AZURE.NOTE]
Azure Access Control Services Filter는 CTP(Community Technology Preview)입니다. 이 필터는 시험판 소프트웨어로서 Microsoft에서 공식적으로 지원되지 않습니다.

## ACS 정의

대부분의 개발자는 ID 전문가가 아니며 일반적으로 해당 응용 프로그램과 서비스에 대한 인증 및 권한 부여 메커니즘을 직접 개발하려고 하지 않습니다. ACS는 복잡한 인증 논리를 코드에 고려해 넣지 않고도 웹 응용 프로그램과 서비스에 액세스해야 하는 사용자를 쉽게 인증할 수 있는 방법을 제공하는 Azure 서비스입니다.

ACS에서 사용할 수 있는 기능은 다음과 같습니다.

-   WIF(Windows Identity Foundation)와 통합
-   Windows Live ID, Google, Yahoo! 및 Facebook을 비롯한 인기 있는 웹 IP(ID 공급자) 지원
-   AD FS(Active Directory Federation Services) 2.0 지원
-   ACS 설정에 대한 프로그래밍 방식의 액세스를 제공하는 OData(Open Data Protocol) 기반 관리 서비스
-   ACS 설정에 대한 관리자 액세스 권한을 허용하는 관리 포털

ACS에 대한 자세한 내용은 [액세스 제어 서비스 2.0][]을 참조하세요.

## 개념

Azure ACS는 온-프레미스 또는 클라우드에서 실행되는 응용 프로그램에 대한 인증 메커니즘을 만들기 위한 일관된 접근 방식인 클레임 기반 ID 사용자를 기반으로 합니다. 클레임 기반 ID는 응용 프로그램과 서비스가 조직 내부, 다른 조직 및 인터넷의 사용자에 대해 필요한 ID 정보를 얻는 일반적인 방법을 제공합니다.

이 가이드의 작업을 완료하려면 다음 개념을 이해해야 합니다.

**클라이언트** - 이 방법 가이드의 컨텍스트에서 클라이언트는 웹 응용 프로그램에 대한 액세스 권한을 얻으려고 하는 브라우저입니다.

**RP(신뢰 당사자) 응용 프로그램** - RP 응용 프로그램은 외부 기관에 인증을 아웃소싱하는 웹 사이트 또는 서비스입니다. ID 표현으로는 RP가 해당 기관을 신뢰한다고 합니다. 이 가이드에서는 ACS를 신뢰하도록 응용 프로그램을 구성하는 방법을 설명합니다.

**토큰** - 토큰은 일반적으로 사용자의 인증 성공 시 발급되는 보안 데이터 컬렉션입니다. 토큰에는 인증된 사용자의 특성인 *클레임* 집합이 포함되어 있습니다. 클레임은 사용자 이름, 사용자가 속한 역할의 식별자, 사용자 연령 등을 나타낼 수 있습니다. 토큰은 일반적으로 디지털 서명되며, 이는 항상 토큰이 발급자에게 다시 제공될 수 있고 해당 내용이 변조될 수 없음을 의미합니다. 사용자는 RP 응용 프로그램이 신뢰하는 기관에서 발급된 유효한 토큰을 제공하여 RP 응용 프로그램에 대한 액세스 권한을 얻습니다.

**IP(ID 공급자)** - IP는 사용자 ID를 인증하고 보안 토큰을 발급하는 기관입니다. 토큰 발급의 실제 작업은 STS(보안 토큰 서비스)라는 특수 서비스를 통해 구현됩니다. IP의 일반적인 예로는 Windows Live ID, Facebook, 비즈니스 사용자 리포지토리(예: Active Directory) 등이 있습니다. ACS가 IP를 신뢰하도록 구성된 경우 시스템은 해당 IP에서 발급된 토큰을 수락하고 유효성을 검사합니다. ACS는 한 번에 여러 IP를 신뢰할 수 있으며, 이는 응용 프로그램이 ACS를 신뢰하는 경우 사용자를 대신하여 ACS가 신뢰하는 모든 IP의 인증된 모든 사용자에게 응용 프로그램을 즉시 제공할 수 있음을 의미합니다.

**FP(페더레이션 공급자)** - IP는 사용자 정보를 직접 보유하고 해당 자격 증명을 통해 사용자를 인증하며 사용자에 대해 알고 있는 내용에 대한 클레임을 발급합니다. FP(페더레이션 공급자)는 다른 종류의 기관입니다. FP는 사용자를 직접 인증하는 대신 한 RP와 하나 이상의 IP 사이에서 중간자 역할을 하며 이 둘 사이의 인증을 조정합니다. IP와 FP 둘 다 STS(보안 토큰 서비스)를 사용하므로 둘 다 보안 토큰을 발급합니다. ACS는 하나의 FP입니다.

**ACS 규칙 엔진** - 신뢰할 수 있는 IP에서 들어오는 토큰을 RP에서 사용할 토큰으로 변환하는 데 사용되는 논리로서 단순한 클레임 변환 규칙 형식으로 코딩됩니다. ACS는 RP에 대해 지정한 변환 논리에 상관없이 해당 변환 논리 적용을 제어하는 규칙 엔진을 특징으로 합니다.

**ACS 네임스페이스** - 설정을 구성하는 데 사용하는 ACS의 최상위 파티션인 네임스페이스입니다. 네임스페이스에는 신뢰하는 IP, 서비스하려는 RP 응용 프로그램, 규칙 엔진이 들어오는 토큰을 처리하는 데 사용할 것으로 예상되는 규칙의 목록 등이 포함됩니다. 네임스페이스는 응용 프로그램과 개발자가 ACS가 해당 기능을 수행하도록 하는 데 사용할 다양한 끝점을 표시합니다.

다음 그림은 웹 응용 프로그램에서 ACS 인증이 작동하는 방식을 보여 줍니다.

![ACS 흐름 다이어그램][acs_flow]

1.  클라이언트(이 경우 브라우저)가 RP의 페이지를 요청합니다.
2.  요청이 아직 인증되지 않았으므로 RP가 사용자를 신뢰하는 기관, 즉 ACS로 리디렉션합니다. ACS는 이 RP에 대해 지정된 IP 선택 항목을 사용자에게 제공합니다. 사용자가 적절한 IP를 선택합니다.
3.  클라이언트가 IP의 인증 페이지로 이동하고 사용자에게 로그온하라는 메시지를 표시합니다.
4.  클라이언트가 인증된 후(예: ID 자격 증명이 입력됨) IP가 보안 토큰을 발급합니다.
5.  보안 토큰을 발급한 후 IP가 클라이언트를 ACS로 리디렉션하고 클라이언트는 IP가 발급한 보안 토큰을 ACS로 보냅니다.
6.  ACS가 IP에 의해 발급된 보안 토큰의 유효성을 검사하고 이 토큰에 포함된 ID 클레임을 ACS 규칙 엔진에 입력한 다음 출력 ID 클레임을 계산하고 이러한 출력 클레임이 포함된 새 보안 토큰을 발급합니다.
7.  ACS가 클라이언트를 RP로 리디렉션합니다. 클라이언트는 ACS가 발급한 새 보안 토큰을 RP로 보냅니다. RP가 ACS가 발급한 보안 토큰에 있는 서명의 유효성을 검사하고 이 토큰에 있는 클레임의 유효성을 검사한 후 원래 요청된 페이지를 반환합니다.

## 필수 조건

이 가이드의 작업을 완료하려면 다음이 필요합니다.

- JDK(Java 개발자 키트), v 1.6 이상
- Eclipse IDE for Java EE Developers, Indigo 이상. <http://www.eclipse.org/downloads/>에서 다운로드할 수 있습니다.
- Java 기반 웹 서버 또는 응용 프로그램 서버의 배포(예: Apache Tomcat, GlassFish, JBoss Application Server 또는 Jetty)
- Azure 구독. <http://www.microsoft.com/windowsazure/offers/>에서 구입할 수 있습니다.
- Eclipse용 Azure 도구 키트, 2014년 4월 릴리스 이상. 자세한 내용은 [Eclipse용 Azure 도구 키트 설치](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx)를 참조하세요.
- 응용 프로그램에 사용할 X.509 인증서. 이 인증서는 공용 인증서(.cer)와 개인 정보 교환(.PFX) 형식 둘 다로 필요합니다. (이 인증서를 만들기 위한 옵션은 이 자습서의 뒷부분에 설명되어 있음)
- Azure 계산 에뮬레이터 및 배포 기술의 기본적인 지식은 [Eclipse에서 Azure용 Hello World 응용 프로그램 만들기](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)(영문)에 설명되어 있습니다.

## ACS 네임스페이스 만들기

Azure에서 ACS(액세스 제어 서비스) 사용을 시작하려면 ACS 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 ACS 리소스의 주소를 지정하기 위한 고유 범위를 제공합니다.

1. [Azure 관리 포털][]에 로그인합니다.
2. **Active Directory**를 클릭합니다.
3. 새 액세스 제어 네임스페이스를 만들려면 **새로 만들기**, **앱 서비스**, **액세스 제어**, **빠른 생성**을 차례로 클릭합니다.
4. 네임스페이스의 이름을 입력합니다. Azure에서 이름이 고유한지 검증합니다.
5. 네임스페이스가 사용되는 지역을 선택합니다. 최상의 성능을 얻으려면 응용 프로그램을 배포할 지역을 사용합니다.
6. 둘 이상의 구독이 있는 경우 ACS 네임스페이스에 사용할 구독을 선택합니다.
7. **만들기**를 클릭합니다.

Azure에서 네임스페이스를 만들고 활성화합니다. 계속하기 전에 새 네임스페이스의 상태가 **활성**이 될 때까지 기다리세요.

## ID 공급자 추가

이 작업에서는 인증을 위해 RP 응용 프로그램에 사용할 IP를 추가합니다. 데모 목적으로 이 작업에서는 IP로 Windows Live를 추가하는 방법을 보여 주지만 ACS 관리 포털에 나열되는 있는 모든 IP를 사용할 수 있습니다.


1.  [Azure 관리 포털][]에서 **Active Directory**를 클릭하고 액세스 제어 네임스페이스를 선택한 후 **관리**를 클릭합니다. ACS 관리 포털이 열립니다.
2.  ACS 관리 포털의 왼쪽 탐색 창에서 **ID 공급자**를 클릭합니다.
3.  Windows Live ID가 기본적으로 사용하도록 설정되어 있으며 삭제할 수 없습니다. 이 자습서에서는 Windows Live ID만 사용됩니다. 그러나 이 화면에서 **추가** 단추를 클릭하여 다른 IP를 추가할 수 있습니다.

현재 Windows Live ID가 ACS 네임스페이스에 대한 IP로 사용하도록 설정되어 있습니다. 이제 Java 웹 응용 프로그램(나중에 만들 예정)을 RP로 지정합니다.

## 신뢰 당사자 응용 프로그램 추가

이 작업에서는 Java 웹 응용 프로그램을 유효한 RP 응용 프로그램으로 인식하도록 ACS를 구성합니다.

1.  ACS 관리 포털에서 **신뢰 당사자 응용 프로그램**을 클릭합니다.
2.  **신뢰 당사자 응용 프로그램** 페이지에서 **추가**를 클릭합니다.
3.  **신뢰 당사자 응용 프로그램 추가** 페이지에서 다음을 수행합니다.
    1.  **이름**에 RP의 이름을 입력합니다. 이 자습서에서는 **Azure Web App**을 입력합니다.
    2.  **모드**에서 **Enter settings manually**를 선택합니다.
    3.  **영역**에 ACS가 발급한 보안 토큰이 적용되는 URI를 입력합니다. 이 작업의 경우 **http://localhost:8080/**을 입력합니다. ![계산 에뮬레이터에 사용할 신뢰 당사자 영역][relying_party_realm_emulator]
    4.  **반환 URL**에 ACS가 보안 토큰을 반환하는 URL을 입력합니다. 이 작업의 경우 **http://localhost:8080/MyACSHelloWorld/index.jsp**을 입력합니다. ![계산 에뮬레이터에 사용할 신뢰 당사자 반환 URL][relying_party_return_url_emulator]
    5.  나머지 필드는 기본값을 그대로 사용합니다.

4.  **Save**를 클릭합니다.

이제 Java 웹 응용 프로그램이 Azure 계산 에뮬레이터(http://localhost:8080/)에서 실행될 때 ACS 네임스페이스에서 RP가 되도록 Java 웹 응용 프로그램을 구성했습니다. 다음 단계에서는 ACS가 RP에 대한 클레임을 처리하는 데 사용하는 규칙을 만듭니다.

## 규칙 만들기

이 작업에서는 클레임이 IP에서 RP로 전달되는 방법을 구동하는 규칙을 정의합니다. 이 가이드에서는 입력 클레임 유형 및 값을 필터링하거나 수정하지 않고 직접 출력 토큰에 복사하도록 ACS를 간단히 구성합니다.

1.  ACS 관리 포털 기본 페이지에서 **규칙 그룹**을 클릭합니다.
2.  **규칙 그룹** 페이지에서 **Azure Web App의 기본 규칙 그룹**을 클릭합니다.
3.  **규칙 그룹 편집** 페이지에서 **생성**을 클릭합니다.
4.  **규칙 생성: Azure Web App의 기본 규칙 그룹** 페이지에서 Windows Live ID가 선택되어 있는지 확인한 후 **생성**을 클릭합니다.
5.  **규칙 그룹 편집** 페이지에서 **저장**을 클릭합니다.

## ACS 네임스페이스에 인증서 업로드

이 작업에서는 ACS 네임스페이스에 의해 만들어진 토큰 요청에 서명하는 데 사용할 .PFX 인증서를 업로드합니다.

1.  ACS 관리 포털 기본 페이지에서 **인증서 및 키**를 클릭합니다.
2.  **인증서 및 키** 페이지에서 **토큰 서명** 위에 있는 **추가**를 클릭합니다.
3.  **토큰 서명 인증서 또는 키 추가** 페이지에서 다음을 수행합니다.
    1. **Used for** 섹션에서 **신뢰 당사자 응용 프로그램**을 클릭하고 **Azure Web App**(앞에서 신뢰 당사자 응용 프로그램의 이름으로 설정한 이름)을 선택합니다.
    2. **Type** 섹션에서 **X.509 인증서**를 선택합니다.
    3. **인증서** 섹션에서 찾아보기 단추를 클릭하고 사용할 X.509 인증서 파일로 이동합니다. 이 파일은 .PFX 파일입니다. 파일을 선택하고 **열기**를 클릭한 후 **암호** 텍스트 상자에 인증서 암호를 입력합니다. 테스트 목적으로 자체 서명된 인증서를 사용할 수 있습니다. 자체 서명된 인증서를 만들려면 **ACS Filter Library** 대화 상자(뒷부분의 설명 참조)에서 **New** 단추를 사용하거나 Azure Starter Kit for Java의 **프로젝트 웹 사이트**에서 [encutil.exe][] 유틸리티를 사용하세요.
    4. **Make Primary**가 선택되어 있는지 확인합니다. **토큰 서명 인증서 또는 키 추가** 페이지는 다음과 유사합니다. ![토큰 서명 인증서 추가][add_token_signing_cert]
    5. **저장**을 클릭하여 설정을 저장하고 **토큰 서명 인증서 또는 키 추가** 페이지를 닫습니다.

이제 응용 프로그램 통합 페이지의 정보를 검토하고 ACS를 사용하도록 Java 웹 응용 프로그램을 구성하는 데 필요한 URI를 복사합니다.

## 응용 프로그램 통합 페이지 검토

ACS 관리 포털의 응용 프로그램 통합 페이지에서 ACS를 사용하도록 Java 웹 응용 프로그램(RP 응용 프로그램)을 구성하는 데 필요한 모든 정보 및 코드를 찾을 수 있습니다. 페더레이션 인증에 대해 Java 웹 응용 프로그램을 구성할 때 이 정보가 필요합니다.

1.  ACS 관리 포털에서 **응용 프로그램 통합**을 클릭합니다.
2.  **응용 프로그램 통합** 페이지에서 **로그인 페이지**를 클릭합니다.
3.  **로그인 페이지 통합** 페이지에서 **Azure Web App**을 클릭합니다.

**로그인 페이지 통합: Azure Web App** 페이지에서 **옵션 1: Link to an ACS-hosted login page**에 나열된 URL이 Java 웹 응용 프로그램에 사용됩니다. Azure Access Control Services Filter 라이브러리를 Java 응용 프로그램에 추가할 때 이 값이 필요합니다.

## Java 웹 응용 프로그램 만들기
1. Eclipse 내의 메뉴에서 **File**, **New**, **Dynamic Web Project**를 차례로 클릭합니다. (**File**, **New**를 차례로 클릭한 후 **Dynamic Web Project**가 사용 가능한 프로젝트로 표시되지 않는 경우 **File**, **New**, **Project**를 차례로 클릭한 후 **Web**을 확장하고 **Dynamic Web Project**를 클릭한 후 **Next**를 클릭합니다.) 이 자습서에서는 프로젝트의 이름을 **MyACSHelloWorld**로 지정합니다. (이 이름을 사용했는지 확인하세요. 이 자습서의 이후 단계에서는 WAR 파일의 이름이 MyACSHelloWorld라고 가정합니다.) 화면이 다음과 유사하게 나타납니다.

    ![ACS용 Hello World 프로젝트 만들기 예제][create_acs_hello_world]

    **마침**을 클릭합니다.
2. Eclipse의 Project Explorer 뷰 내에서 **MyACSHelloWorld**를 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.
3. **New JSP File** 대화 상자에서 파일의 이름을 **index.jsp**로 지정합니다. 다음에 표시된 것처럼 상위 폴더를 MyACSHelloWorld/WebContent로 유지합니다.

    ![ACS용 JSP 파일 추가 예제][add_jsp_file_acs]

    **Next**를 클릭합니다.

4. **Select JSP Template** 대화 상자에서 **New JSP File (html)**을 선택하고 **Finish**를 클릭합니다.
5. index.jsp 파일이 Eclipse에서 열리면 텍스트에 추가하여 기존 `<body>` 요소 내에 **Hello ACS World!**를 표시합니다. 업데이트된 `<body>` 내용이 다음과 같이 나타납니다.

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    index.jsp를 저장합니다.
  
## 응용 프로그램에 ACS Filter 라이브러리 추가

1. Eclipse의 Project Explorer에서 **MyACSHelloWorld**를 마우스 오른쪽 단추로 클릭하고 **Build Path**를 클릭한 후 **Configure Build Path**를 클릭합니다.
2. **Java Build Path** 대화 상자에서 **Libraries** 탭을 클릭합니다.
3. **Add Library**를 클릭합니다.
4. **Azure Access Control Services Filter (by MS Open Tech)**를 클릭한 후 **Next**를 클릭합니다. **Azure Access Control Services Filter** 대화 상자가 표시됩니다. (Eclipse를 설치한 위치에 따라 **Location** 필드의 경로가 다를 수 있으며, 소프트웨어 업데이트에 따라 버전 번호가 다를 수 있습니다.)

    ![ACS Filter 라이브러리 추가][add_acs_filter_lib]

5. 관리 포털의 **로그인 페이지 통합** 페이지가 열려 있는 브라우저를 사용하여 **옵션 1: Link to an ACS-hosted login page** 필드에 나열된 URL을 복사하여 Eclipse 대화 상자의 **ACS Authentication Endpoint** 필드에 붙여넣습니다.
6. 관리 포털의 **신뢰 당사자 응용 프로그램 편집** 페이지가 열려 있는 브라우저를 사용하여 **영역** 필드에 나열된 URL을 복사하여 Eclipse 대화 상자의 **Relying Party Realm** 필드에 붙여넣습니다.
7. Eclipse 대화 상자의 **Security** 섹션 내에서 기존 인증서를 사용하려는 경우 **Browse**를 클릭하여 사용할 인증서로 이동하여 선택한 후 **Open**을 클릭합니다. 또는 새 인증서를 만들려는 경우 **New**를 클릭하여 **New Certificate** 대화 상자를 표시한 후 새 인증서의 암호, .cer 파일 이름 및 .pfx 파일 이름을 지정합니다.
8. **Embed the certificate in the WAR file**을 선택합니다. 이 방법으로 인증서를 포함하면 인증서를 구성 요소로 수동으로 추가하지 않고도 배포에 인증서가 포함됩니다. (대신 WAR 파일에서 인증서를 외부적으로 저장해야 하는 경우 인증서를 역할 구성 요소로 추가하고 **Embed the certificate in the WAR file**을 선택 취소하면 됩니다.)
9. [옵션] **Require HTTPS connections**를 선택된 상태로 둡니다. 이 옵션을 설정하면 HTTPS 프로토콜을 사용하여 응용 프로그램에 액세스해야 합니다. HTTPS 연결이 필요하지 않은 경우 이 옵션을 선택 취소합니다.
10. 계산 에뮬레이터에 대한 배포의 경우 **Azure ACS Filter** 설정은 다음과 유사합니다.

    ![계산 에뮬레이터에 대한 배포의 경우 Azure ACS Filter 설정][add_acs_filter_lib_emulator]

11. **마침**을 클릭합니다.
12. web.xml 파일이 만들어진다는 내용이 표시된 대화 상자가 나타나면 **Yes**를 클릭합니다.
13. **OK**를 클릭하여 **Java Build Path** 대화 상자를 닫습니다.

## 계산 에뮬레이터에 배포

1. Eclipse의 Project Explorer에서 **MyACSHelloWorld**를 마우스 오른쪽 단추로 클릭하고 **Azure**를 클릭한 후 **Package for Azure**를 클릭합니다.
2. **Project name**에 **MyAzureACSProject**를 입력하고 **Next**를 클릭합니다.
3. JDK 및 응용 프로그램 서버를 선택합니다. (이러한 단계는 [Eclipse에서 Azure용 Hello World 응용 프로그램 만들기](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)(영문) 자습서에 자세히 설명되어 있습니다.)
4. **Finish**를 클릭합니다.
5. **Run in Azure Emulator** 단추를 클릭합니다.
6. Java 웹 응용 프로그램이 계산 에뮬레이터에서 시작되고 나면 현재 브라우저 세션이 ACS 로그인 테스트를 방해하지 않도록 모든 브라우저 인스턴스를 닫습니다.
7. 브라우저에서 <http://localhost:8080/MyACSHelloWorld/>(또는 **Require HTTPS connections**를 선택한 경우 <https://localhost:8080/MyACSHelloWorld/>)를 열어 응용 프로그램을 실행합니다. Windows Live ID 로그인 메시지가 표시되어야 하며, 그런 다음 신뢰 당사자 응용 프로그램에 대해 지정된 반환 URL로 이동되어야 합니다.
99.  응용 프로그램 확인을 마쳤으면 **Reset Azure Emulator** 단추를 클릭합니다.

## Azure에 배포

Azure에 배포하려면 ACS 네임스페이스의 신뢰 당사자 영역 및 반환 URL을 변경해야 합니다.

1. Azure 관리 포털 내의 **신뢰 당사자 응용 프로그램 편집** 페이지에서 **영역**을 배포된 사이트의 URL로 수정합니다. **example**을 배포에 대해 지정한 DNS 이름으로 바꿉니다.

    ![프로덕션에 사용할 신뢰 당사자 영역][relying_party_realm_production]

2. **반환 URL**을 응용 프로그램 URL로 수정합니다. **example**을 배포에 대해 지정한 DNS 이름으로 바꿉니다.

    ![프로덕션에 사용할 신뢰 당사자 반환 URL][relying_party_return_url_production]

3. **저장**을 클릭하여 업데이트된 신뢰 당사자 영역 및 반환 URL 변경 내용을 저장합니다.
4. 브라우저에 **로그인 페이지 통합** 페이지를 열어둔 상태로 둡니다. 곧 이 페이지에서 복사해야 하기 때문입니다.
5. Eclipse의 Project Explorer에서 **MyACSHelloWorld**를 마우스 오른쪽 단추로 클릭하고 **Build Path**를 클릭한 후 **Configure Build Path**를 클릭합니다.
6. **Libraries** 탭, **Azure Access Control Services Filter**, **Edit**를 차례로 클릭합니다.
7. 관리 포털의 **로그인 페이지 통합** 페이지가 열려 있는 브라우저를 사용하여 **옵션 1: Link to an ACS-hosted login page** 필드에 나열된 URL을 복사하여 Eclipse 대화 상자의 **ACS Authentication Endpoint** 필드에 붙여넣습니다.
8. 관리 포털의 **신뢰 당사자 응용 프로그램 편집** 페이지가 열려 있는 브라우저를 사용하여 **영역** 필드에 나열된 URL을 복사하여 Eclipse 대화 상자의 **Relying Party Realm** 필드에 붙여넣습니다.
9. Eclipse 대화 상자의 **Security** 섹션 내에서 기존 인증서를 사용하려는 경우 **Browse**를 클릭하여 사용할 인증서로 이동하여 선택한 후 **Open**을 클릭합니다. 또는 새 인증서를 만들려는 경우 **New**를 클릭하여 **New Certificate** 대화 상자를 표시한 후 새 인증서의 암호, .cer 파일 이름 및 .pfx 파일 이름을 지정합니다.
10. 인증서를 WAR 파일에 포함하려 한다고 가정하므로 **Embed the certificate in the WAR file**을 선택된 상태로 둡니다.
11. [옵션] **Require HTTPS connections**를 선택된 상태로 둡니다. 이 옵션을 설정하면 HTTPS 프로토콜을 사용하여 응용 프로그램에 액세스해야 합니다. HTTPS 연결이 필요하지 않은 경우 이 옵션을 선택 취소합니다.
12. Azure에 대한 배포의 경우 Azure ACS Filter 설정은 다음과 유사합니다.

    ![프로덕션 배포의 Azure ACS Filter 설정][add_acs_filter_lib_production]

13. **Finish**를 클릭하여 **Edit Library** 대화 상자를 닫습니다.
14. **OK**를 클릭하여 **Properties for MyACSHelloWorld** 대화 상자를 닫습니다.
15. Eclipse에서 **Publish to Azure Cloud** 단추를 클릭합니다. [Eclipse에서 Azure용 Hello World 응용 프로그램 만들기](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)(영문) 항목의 **To deploy your application to Azure** 섹션에서 수행한 것과 유사하게 프롬프트에 응답합니다.

웹 응용 프로그램이 배포된 후 열려 있는 모든 브라우저 세션을 닫고 웹 응용 프로그램을 실행하고 나면 Windows Live ID 자격 증명을 사용하여 로그인하라는 메시지가 표시되어야 하며, 그런 다음 신뢰 당사자 응용 프로그램의 반환 URL로 이동되어야 합니다.

ACS Hello World 응용 프로그램 사용을 완료했으면 배포를 삭제해야 합니다([Eclipse에서 Azure용 Hello World 응용 프로그램 만들기](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)(영문) 항목에서 배포 삭제 방법을 알아볼 수 있음).


## <a name="next_steps"></a>다음 단계

ACS에 의해 응용 프로그램으로 반환되는 SAML(Security Assertion Markup Language)의 예제는 [Azure 액세스 제어 서비스에 의해 반환되는 SAML을 확인하는 방법][]\(영문)을 참조하세요. ACS 기능을 자세히 살펴보고 보다 정교한 시나리오를 실험하려면 [액세스 제어 서비스 2.0][]\(영문)을 참조하세요.

또한 이 예제에서는 **Embed the certificate in the WAR file** 옵션을 사용했습니다. 이 옵션을 사용하면 인증서를 더 간단하게 배포할 수 있습니다. 대신 서명 인증서를 WAR 파일과 별도로 유지하려면 다음 방법을 사용하면 됩니다.

1. **Azure Access Control Services Filter** 대화 상자의 **Security** 섹션 내에서 **${env.JAVA\_HOME}/mycert.cer**을 입력하고 **Embed the certificate in the WAR file**을 선택 취소합니다. (인증서 파일 이름이 다른 경우 mycert.cer을 조정합니다.) **Finish**를 클릭하여 대화 상자를 닫습니다.
2. 인증서를 배포에 구성 요소로 복사합니다. Eclipse의 Project Explorer에서 **MyAzureACSProject**를 확장하고 **WorkerRole1**을 마우스 오른쪽 단추로 클릭한 후 **Properties**를 클릭하고 **Azure Role**을 확장한 후 **Components**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **Add Component** 대화 상자 내에서 다음을 수행합니다.
    1. **Import** 섹션에서 다음을 수행합니다.
        1. **File** 단추를 사용하여 사용할 인증서로 이동합니다.
        2. **Method**의 경우 **copy**를 선택합니다.
    2. **As Name**의 경우 텍스트 상자를 클릭하고 기본 이름을 그대로 사용합니다.
    3. **Deploy** 섹션에서 다음을 수행합니다.
        1. **Method**의 경우 **copy**를 선택합니다.
        2. **To directory**의 경우 **%JAVA\_HOME%**를 입력합니다.
    4. **Add Component** 대화 상자는 다음과 유사합니다.

        ![인증서 구성 요소 추가][add_cert_component]

    5. **확인**을 클릭합니다.

이때 인증서가 배포에 포함됩니다. 인증서를 WAR 파일에 포함하든 배포에 구성 요소로 추가하든 관계없이 [ACS 네임스페이스에 인증서 업로드][] 섹션에 설명된 대로 인증서를 네임스페이스에 업로드해야 합니다.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[ACS 네임스페이스에 인증서 업로드]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[encutil.exe]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Azure 액세스 제어 서비스에 의해 반환되는 SAML을 확인하는 방법]: /ko-kr/develop/java/how-to-guides/view-saml-returned-by-acs/
[액세스 제어 서비스 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure 관리 포털]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png
 

<!----HONumber=AcomDC_0817_2016-->