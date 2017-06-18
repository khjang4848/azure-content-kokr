<properties 
    pageTitle="자습서: Thirdlight와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Thirdlight를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#자습서: Thirdlight과 Azure Active Directory 통합
  
이 자습서는 Azure와 Thirdlight의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Thirdlight Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Thirdlight에 할당한 Azure AD 사용자가 Thirdlight 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Thirdlight에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "시나리오")

##Thirdlight에 응용 프로그램 통합 사용
  
이 섹션은 Thirdlight에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Thirdlight에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Thirdlight**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "응용 프로그램 갤러리")

7.  결과 창에서 **Thirdlight**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Thirdlight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "Thirdlight")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Thirdlight에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Thirdlight에 대한 Single Sign-On을 구성하려면 인증서의 지문(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Thirdlight** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Single Sign-On 구성")

2.  **Thirdlight에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Thirdlight Sign-on URL** 텍스트 상자에서 사용자가 사용한 URL을 입력하여 Thirdlight 응용 프로그램에 로그인하고(예: "*http://azuresso2.thirdlight.com/*") **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "앱 URL 구성")

4.  **Thirdlight에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Thirdlight 회사 사이트에 관리자로 로그인합니다.

6.  **구성 > 시스템 관리**로 이동하고 **SAML2**를 클릭합니다.

    ![시스템 관리](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "시스템 관리")

7.  SAML2 구성 섹션에서 다음 단계를 수행합니다.

    ![SAML Single Sign-On](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  **SAML2 Single Sign-On 사용**을 선택합니다.
    2.  **IdP 메타데이터에 대한 원본**으로 **XML에서 IdP 메타데이터 로드**를 선택합니다.
    3.  다운로드한 메타데이터 파일을 열고 내용을 복사한 다음 **IdP 메타데이터 XML** 텍스트 상자에 붙여 넣습니다.
    4.  **SAML2 설정 저장**을 클릭합니다.

8.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 Thirdlight에 로그인할 수 있도록 하려면 Thirdlight로 프로비전되어야 합니다. Thirdlight의 경우 프로비전은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면

1.  **Thirdlight** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **사용자** 탭으로 이동합니다.

3.  **사용자 및 그룹**을 선택합니다.

4.  **새 사용자 추가** 버튼을 클릭합니다.

5.  입력**사용자 이름, 이름 또는 설명, 이메일, 기본 설정 또는 새 멤버의 그룹 선택**프로 비전 하려는 유효한 AAD 계정입니다.

6.  **만들기**를 클릭합니다.

>[AZURE.NOTE] 다른 Thirdlight 사용자 계정 생성 도구 또는 Thirdlight가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Thirdlight에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Thirdlight** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->