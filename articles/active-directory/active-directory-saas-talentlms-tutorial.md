<properties 
    pageTitle="자습서: TalentLMS와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 TalentLMS를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: TalentLMS와 Azure Active Directory 통합
  
이 자습서는 Azure와 TalentLMS의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   TalentLMS 테넌트
  
이 자습서를 완료한 후 TalentLMS에 할당한 Azure AD 사용자가 TalentLMS 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  TalentLMS에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-talentlms-tutorial/IC777289.png "시나리오")

##TalentLMS에 응용 프로그램 통합 사용
  
이 섹션은 TalentLMS에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###TalentLMS에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-talentlms-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-talentlms-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-talentlms-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **TalentLMS**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-talentlms-tutorial/IC777290.png "응용 프로그램 갤러리")

7.  결과 창에서 **TalentLMS**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 TalentLMS에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. TalentLMS에 대한 Single Sign-On을 구성하려면 인증서의 지문(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **TalentLMS** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Single Sign-On 구성")

2.  **TalentLMS에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **TalentLMS 로그인 URL** 텍스트 상자에 다음 패턴 "*https://\<tenant-name>.TalentLMS.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![URL에 로그인](./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL에 로그인")

4.  **TalentLMS에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 **c:\\TalentLMS.cer**에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 TalentLMS 회사 사이트에 관리자로 로그인합니다.

6.  **계정 및 설정** 섹션에서 **사용자** 탭을 클릭합니다.

    ![계정 및 설정](./media/active-directory-saas-talentlms-tutorial/IC777296.png "계정 및 설정")

7.  **SSO(Single Sign-On)**를 클릭합니다.

8.  Single Sign-On 섹션에서 다음 단계를 수행 합니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-talentlms-tutorial/IC777297.png "SSO(Single sign-on)")

    1.  **SSO 통합 형식** 목록에서 **SAML 2.0**을 선택합니다.
    2.  Azure 클래식 포털의 **TalentLMS에서 Single Sign-On 구성** 대화 상자 페이지에서 **ID 공급자 ID** 값을 복사한 다음 **ID 공급자(IdP)** 텍스트 상자에 붙여 넣습니다.
    3.  내보낸 인증서에서 **지문** 값을 복사한 다음 **인증서 지문** 텍스트 상자에 붙여 넣습니다.

        >[AZURE.TIP] 자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

    4.  Azure 클래식 포털의 **TalentLMS에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **원격 로그인 URL** 텍스트 상자에 붙여 넣습니다.
    5.  Azure 클래식 포털의 **TalentLMS에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **원격 로그아웃 URL** 텍스트 상자에 붙여 넣습니다.
    6.  **TargetedID** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**를 입력합니다.
    7.  **이름** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**를 입력합니다.
    8.  **성** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**를 입력합니다.
    9.  **이메일** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**를 입력합니다.
    10. **Save**를 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 TalentLMS에 로그인할 수 있도록 하려면 TalentLMS로 프로비전되어야 합니다. TalentLMS의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  자신의 **TalentLMS** 테넌트에 로그인합니다.

2.  **사용자**를 클릭한 후 **사용자 추가**를 클릭합니다.

3.  **사용자 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-talentlms-tutorial/IC777299.png "사용자 추가")

    1.  Azure AD 사용자 계정의 관련 특성 값을 다음 텍스트 상자에 입력합니다. **이름**, **성**, **이메일 주소**를 입력합니다.
    2.  **사용자 추가**를 클릭합니다.

>[AZURE.NOTE] 다른 TalentLMS 사용자 계정 생성 도구 또는 TalentLMS가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###TalentLMS에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **TalentLMS** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-talentlms-tutorial/IC777300.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-talentlms-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->