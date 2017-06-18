<properties 
    pageTitle="자습서: Wikispaces와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Wikispaces를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Wikispaces와 Azure Active Directory 통합
  
이 자습서는 Azure 및 Wikispaces의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Wikispaces Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Wikispaces에 할당한 Azure AD 사용자가 Wikispaces 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Wikispaces에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "시나리오")

##Wikispaces에 응용 프로그램 통합 사용
  
이 섹션은 Wikispaces에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Wikispaces에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Wikispaces**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "응용 프로그램 갤러리")

7.  결과 창에서 **Wikispaces**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Wikispaces에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Wikispaces** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Single Sign-On 구성")

2.  **Wikispaces에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Wikispaces Sign-on URL** 텍스트 상자에 다음 패턴 "*http://company.wikispaces.net*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "앱 URL 구성")

4.  **Wikispaces에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Single Sign-On 구성")

5.  메타데이터를 Wikispaces 지원팀에 보냅니다.

    >[AZURE.NOTE] Single Sign-On 구성을 Wikispaces 지원팀에서 수행할 수 있습니다. 구성이 완료되는 즉시 알림을 받아볼 수 있습니다.

6.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 Wikispaces에 로그인할 수 있도록 하려면 Wikispaces로 프로비전되어야 합니다. Wikispaces의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  **Wikispaces** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **멤버**로 이동합니다.

    ![멤버](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "멤버")

3.  **피플 초대**를 클릭합니다.

    ![피플 초대](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "피플 초대")

4.  **피플 초대** 페이지에서 다음 단계를 수행합니다.

    ![피플 초대](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "피플 초대")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **사용자 이름 또는 이메일 주소**를 입력합니다.
    2.  **보내기**를 클릭합니다.

        >[AZURE.NOTE] Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 이메일을 받습니다.

>[AZURE.NOTE] 다른 Wikispaces 사용자 계정 생성 도구 또는 Wikispaces가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Wikispaces에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Wikispaces** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->