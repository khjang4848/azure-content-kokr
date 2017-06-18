<properties 
    pageTitle="자습서: Azure Active Directory와 Zendesk 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Zendesk을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#자습서:Azure Active Directory와 Zendesk 통합
  
이 자습서는 Azure와 Zendesk 통합을 보여줍니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Zendesk 테넌트
  
이 자습서를 완료하면 Zendesk에 할당한 Azure AD 사용자가 Zendesk 회사 사이트 (로그온을 시작한 서비스 공급자)에서, 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Zendesk에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-zendesk-tutorial/IC773083.png "시나리오")

##Zendesk에 응용 프로그램 통합 사용
  
이 섹션에서는 Zendesk에 응용 프로그램 통합을 사용하는 방법에 대해 간략하게 설명합니다.

###Zendesk에 응용 프로그램 통합을 사용하려면, 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-zendesk-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-zendesk-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-zendesk-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에서 **Zendesk**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-zendesk-tutorial/IC773084.png "응용 프로그램 갤러리")

7.  결과 창에서 **Zendesk**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##Single Sign-On 구성
  
이 섹션의 목적은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD에서 자신의 계정으로 Zendesk에 인증할 수 있게 하는 방법을 간략하게 설명하는 것입니다. Zendesk에 singl sign-on을 구성하려면 인증서에서 지문 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Zendesk** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Configure Single Sign On** 대화 상자를 엽니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-zendesk-tutorial/IC773086.png "SSO(Single sign-on)")

2.  **Zendesk에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택한 후, **다음**을 클릭하세요..

    ![Single Sign-On 구성](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-zendesk-tutorial/IC773088.png "앱 URL 구성")
  
	a. **Zendesk 로그인 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<tenant-name>.zendesk.com`

	b. **Next**를 클릭합니다.



4.  **Zendesk에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 관리자 권한으로 Zendesk 회사 사이트에 로그인 합니다.

6.  **Admin**을 클릭합니다.

7.  **설정**을 클릭하고 왼쪽 탐색 창에서 **보안**을 클릭합니다.

    ![보안](./media/active-directory-saas-zendesk-tutorial/IC773089.png "보안")

8.  **보안** 페이지에서 **관리자 및 에이전트** 탭을 클릭합니다.

9.  **SSO(Single Sign-On) 및 SAML**을 선택하고**SAML**을 선택합니다.

10. Azure 포털의 **Zendesk에서 Single Sign-On 구성** 대화 상자 페이지에서, **SAML SSO URL** 값을 복사한 다음, **SAML SSO URL** 텍스트 상자에 붙여 넣습니다.

11. Azure AD 포털의 **Zendesk에서 Single Sign-On 구성**페이지에서 **원격 로그 아웃 URL**값을 복사하여 **원격 로그 아웃 URL** 텍스트 상자에 붙여 넣기 합니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-zendesk-tutorial/IC773090.png "SSO(Single sign-on)")

12. 내보낸 인증서에서 **지문** 값을 복사한 다음 **인증서 지문** 텍스트 상자에 붙여 넣습니다.

	>[AZURE.TIP] 자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

13. **Save**를 클릭합니다.

14. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 **Zendesk**에 로그인할 수 있도록 하려면 사용자 계정이 **Zendesk**에 프로비전되어야 합니다. **Zendesk**의 경우, 수동으로 프로비전합니다.

###사용자 계정을 Zendesk로 프로비전 하려면 다음 단계를 수행합니다.

1.  **Zendesk** 테넌트에 로그인합니다.

2.  **고객 목록** 탭을 선택합니다.

3.  **사용자** 탭을 선택하고 **추가**를 클릭합니다.

    ![사용자 추가](./media/active-directory-saas-zendesk-tutorial/IC773632.png "사용자 추가")

4.  프로비전 하려는 기존 Azure AD 계정의 이메일 주소를 입력하고, **저장**을 클릭합니다.

    ![새 사용자](./media/active-directory-saas-zendesk-tutorial/IC773633.png "새 사용자")

>[AZURE.NOTE] 다른 Zendesk 사용자 계정 생성 도구 또는 Zendesk에서 제공하는 APIs를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Zendesk에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Zendesk** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-zendesk-tutorial/IC773094.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-zendesk-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->