<properties 
    pageTitle="자습서: SugarCRM과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Sugar CRM을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: SugarCRM과 Azure Active Directory 통합
  
이 자습서는 Azure 및 SugarCRM의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Sugar CRM Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Sugar CRM에 할당한 Azure AD 사용자가 Sugar CRM 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Sugar CRM에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "시나리오")

##Sugar CRM에 응용 프로그램 통합 사용
  
이 섹션은 Sugar CRM에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Sugar CRM에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Sugar CRM**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "응용 프로그램 갤러리")

7.  결과 창에서 **Sugar CRM**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Sugar CRM에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 Sugar CRM 테넌트에 업로드해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Sugar CRM** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Single Sign-On 구성")

2.  **Sugar CRM에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Sugar CRM Sign On URL** 텍스트 상자에 Sugar CRM 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: "*http://company.sugarondemand.com*")을 입력한 후 *다음*을 클릭합니다**.

    ![앱 URL 구성](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "앱 URL 구성")

4.  **Sugar CRM에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Sugar CRM 회사 사이트에 관리자로 로그인합니다.

6.  **관리자**로 이동합니다.

    ![관리자](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "관리자")

7.  **관리** 섹션에서 **암호 관리**를 클릭합니다.

    ![관리](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "관리")

8.  **SAML 인증 사용**을 선택합니다.

    ![관리](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "관리")

9.  **SAML Authentication** 섹션에서 다음 단계를 수행합니다.

    ![SAML 인증](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML 인증")

    1.  Azure 클래식 포털의 **Sugar CRM에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여 넣습니다.
    2.  Azure 클래식 포털의 **Sugar CRM에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **SLO URL** 텍스트 상자에 붙여 넣습니다.
    3.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    4.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **X.509 인증서** 텍스트 상자에 붙여 넣습니다.
    5.  **Save**를 클릭합니다.

10. Azure 클래식 포털의 **Sugar CRM에서 Single Sign-On 구성** 대화 상자 페이지에서 Single Sign-On 구성 확인을 선택한 다음 **완료**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 Sugar CRM에 로그인할 수 있도록 하려면 Sugar CRM으로 프로비전되어야 합니다. Sugar CRM의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  **Sugar CRM** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **관리자**로 이동합니다.

    ![관리자](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "관리자")

3.  **관리** 섹션에서 **사용자 관리**를 클릭합니다.

    ![관리](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "관리")

4.  **사용자 > 새 사용자 만들기**로 이동합니다.

    ![새 사용자 만들기](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "새 사용자 만들기")

5.  **사용자 프로필** 탭에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "새 사용자")

    1.  관련된 텍스트 상자에 유효한 Azure Active Directory 사용자 이름, 성 및 메일 주소를 입력합니다.

6.  **상태**는 **활성**을 선택합니다.

7.  암호 탭에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "새 사용자")

    1.  관련된 텍스트 상자에 암호를 입력합니다.
    2.  **Save**를 클릭합니다.

>[AZURE.NOTE] 다른 Sugar CRM 사용자 계정 생성 도구 또는 Sugar CRM이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Sugar CRM에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Sugar CRM** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->