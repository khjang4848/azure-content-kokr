<properties 
    pageTitle="자습서: Slack과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Slack을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#자습서: Slack과 Azure Active Directory 통합
  
이 자습서는 Azure 및 Slack의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Slack Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Slack에 할당한 Azure AD 사용자가 Slack 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Slack에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-slack-tutorial/IC794980.png "시나리오")

##Slack에 응용 프로그램 통합 사용
  
이 섹션은 Slack에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Slack에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-slack-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-slack-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-slack-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Slack**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-slack-tutorial/IC794981.png "응용 프로그램 갤러리")

7.  결과 창에서 **Slack**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![시나리오](./media/active-directory-saas-slack-tutorial/IC796925.png "시나리오")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Slack에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Slack** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-slack-tutorial/IC794982.png "Single Sign-On 구성")

2.  **에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-slack-tutorial/IC794983.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에 있는 **Slack 로그인 URL** 텍스트 상자에 Slack 테넌트(예: "*https://azuread.slack.com*")를 입력하고 *다음*을 클릭하십시오**.

    ![앱 URL 구성](./media/active-directory-saas-slack-tutorial/IC794984.png "앱 URL 구성")

4.  **Slack에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-slack-tutorial/IC794985.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Slack 회사 사이트에 관리자로 로그인합니다.

6.  **Microsoft Azure AD > 팀 설정**으로 이동합니다.

    ![팀 설정](./media/active-directory-saas-slack-tutorial/IC794986.png "팀 설정")

7.  **팀 설정** 섹션에 있는 **인증** 탭을 클릭한 다음 **설정 변경**을 클릭합니다.

    ![팀 설정](./media/active-directory-saas-slack-tutorial/IC794987.png "팀 설정")

8.  **SAML 인증 설정** 대화 상자에서 다음 단계를 수행합니다.

    ![SAML 설정](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML 설정")

    1.  Azure 클래식 포털의 **Slack에서 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **SAML 2.0 끝점(HTTP)** 텍스트 상자에 붙여 넣습니다.
    2.  Azure 클래식 포털의 **Slack에서 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **ID 공급자 발급자** 텍스트 상자에 붙여 넣습니다.
    3.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.
    
        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    4.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **공용 인증서** 텍스트 상자에 붙여 넣습니다.
    5.  **사용자가 이메일 주소를 변경할 수 있도록 허용**을 선택 취소하십시오.
    6.  **사용자가 자신의 사용자 이름을 선택할 수 있도록 허용**을 선택하십시오.
    7.  **팀이 사용해야 할 인증으로** **선택 사항**을 선택하십시오.
    8.  **구성 저장**을 클릭하십시오.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-slack-tutorial/IC794989.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 Slack에 로그인할 수 있도록 하려면 Slack으로 프로비전되어야 합니다.
  
Slack을 프로비전하는 사용자를 구성할 작업 항목이 없습니다. 할당된 사용자가 Slack에 로그인하려고 하면 필요한 경우 Slack 계정이 자동으로 생성됩니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Slack에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Slack** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-slack-tutorial/IC794990.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-slack-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0921_2016-->