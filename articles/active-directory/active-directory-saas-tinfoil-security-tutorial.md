<properties 
    pageTitle="자습서: Tinfoil Security와 Azure Active Directory 통합 | Microsoft Azure"
    description="Azure Active Directory에서 Tinfoil Security를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Tinfoil Security와 Azure Active Directory 통합
  
이 자습서는 Azure와 Tinfoil Security의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Tinfoil Security Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Tinfoil Security에 할당한 Azure AD 사용자가 Tinfoil Security 회사 사이트 (ID 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Tinfoil Security에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![Single Sign-On 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Single Sign-On 구성")

##Tinfoil Security에 응용 프로그램 통합 사용
  
이 섹션은 Tinfoil Security에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Tinfoil Security에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Tinfoil Security**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "응용 프로그램 갤러리")

7.  결과 창에서 **Tinfoil Security**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Tinfoil Security에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Tinfoil Security에 대한 Single Sign-On을 구성하려면 인증서의 지문(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Tinfoil Security** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Single Sign-On 구성")

2.  **Tinfoil Security에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에 있는 **Tinfoil Security 회신 URL** 텍스트 상자에 자신의 Tinfoil Security 어설션 소비자 서비스(ACS) URL(예: "*https://www.tinfoilsecurity.com/saml/consume*")을 입력하고 **다음**을 클릭합니다.

    >[AZURE.NOTE] Tinfoil Security 메타데이터(https://www.tinfoilsecurity.com/saml/metadata)에서 ACS URL을 가져올 수 있어야 합니다.

    ![앱 URL 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "앱 URL 구성")

4.  **Tinfoil Security에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 **Tinfoil Security**에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Tinfoil Security 회사 사이트에 관리자로 로그인합니다.

6.  위쪽에 도구 모음에서 **내 계정**을 클릭합니다.

    ![대시보드](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "대시보드")

7.  **보안**을 클릭합니다.

    ![보안](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "보안")

8.  **Single Sign-On** 구성 페이지에서 다음 단계를 수행합니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "SSO(Single sign-on)")

    1.  **SAML 사용**을 선택합니다.
    2.  **수동 구성**을 클릭합니다.
    3.  Azure 클래식 포털의 **Tinfoil Security에 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **SAML 게시 URL** 텍스트 상자에 붙여 넣습니다.
    4.  내보낸 인증서에서 **지문** 값을 복사한 다음 **SAML 인증서 지문** 텍스트 상자에 붙여 넣습니다.

        >[AZURE.TIP] 자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

    5.  **계정 ID**를 복사합니다.
    6.  **Save**를 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Single Sign-On 구성")

10. 위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "특성")

11. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "특성")

    1.  **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에 **accountid**를 입력합니다.
    3.  **특성 값** 텍스트 상자에 이전 섹션에서 복사한 계정 ID 값을 붙여 넣습니다.
    4.  **완료**를 클릭합니다.

12. **변경 내용 적용**을 클릭합니다.

##사용자 프로비전 구성
  
Azure AD 사용자가 Tinfoil Security에 로그인할 수 있게 하려면 Tinfoil Security로 프로비전되어야 합니다. Tinfoil Security의 경우 프로비전은 수동 작업입니다.

###사용자 프로비전을 구성하려면 다음 단계를 따르십시오.

1.  사용자가 엔터프라이즈 계정의 일부라면 Tinfoil Security 지원 팀에 연락하여 사용자 계정을 생성해야 합니다.

2.  사용자가 일반적인 Tinfoil Security SaaS 사용자라면 사용자는 사용자 사이트에 협력자를 추가할 수 있습니다. 그러면 지정된 이메일로 초대장이 발송되는 프로세스가 호출되어 새로운 Tinfoil Security 사용자 계정을 생성합니다.

>[AZURE.NOTE] 다른 Tinfoil Security 사용자 계정 생성 도구 또는 Tinfoil Security가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Tinfoil Security에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Tinfoil Security** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->