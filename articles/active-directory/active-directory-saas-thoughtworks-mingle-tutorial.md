<properties 
    pageTitle="자습서: Thoughtworks Mingle과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Thoughtworks Mingle을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Thoughtworks Mingle과 Azure Active Directory 통합
  
이 자습서는 Azure 및 Thoughtworks Mingle의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Thoughtworks Mingle 테넌트
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Thoughtworks Mingle에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "시나리오")

##Thoughtworks Mingle에 응용 프로그램 통합 사용
  
이 섹션은 Thoughtworks Mingle에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Thoughtworks Mingle에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **thoughtworks mingle**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "응용 프로그램 갤러리")

7.  결과 창에서 **Thoughtworks Mingle**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Huddle에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 Thoughtworks Mingle로 인코딩된 인증서 파일을 만들어야 합니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Thoughtworks Mingle** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Single Sign-On 구성")

2.  **Thoughtworks Mingle에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Thoughtworks Mingle 테넌트 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력한 다음 "*http://company.mingle.thoughtworks.com*"을(를) 사용하여 URL을 입력하고 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "앱 URL 구성")

4.  **Thoughtworks Mingle의 Single Sign-On 구성** 페이지에서 메타데이터 다운로드를 클릭한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Single Sign-On 구성")

5.  **Thoughtworks Mingle** 회사 사이트에 관리자 권한으로 로그인합니다.

6.  **관리자** 탭을 클릭하고 **SSO 구성**을 클릭합니다.

    ![SSO 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO 구성")

7.  **SSO 구성** 섹션에서 다음 단계를 수행합니다.

    ![SSO 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO 구성")

    1.  메타데이터 파일을 업로드하려면 **파일 선택**을 클릭합니다.
    2.  **변경 내용 저장**을 클릭합니다.

8.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
AAD 사용자가 로그인 할 수 있도록 Azure Active Directory 사용자 이름을 사용하여 Thoughtworks Mingle 응용 프로그램에 프로비전되어야 합니다. Thoughtworks Mingle의 경우 프로비전은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면

1.  Thoughtworks Mingle 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **프로필**을 클릭합니다.

    ![첫 번째 프로젝트](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "첫 번째 프로젝트")

3.  **관리자** 탭을 클릭하고 **사용자**를 클릭합니다.

    ![사용자](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "사용자")

4.  **새 사용자**를 클릭합니다.

    ![새 사용자](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "새 사용자")

5.  **새 사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "새 사용자")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **로그인 이름**, **표시 이름**, **암호 선택**, **암호 확인**을 입력합니다.
    2.  **사용자 유형에서는** **전체 사용자**를 선택합니다.
    3.  **이 프로필 만들기**를 클릭합니다.

>[AZURE.NOTE] 다른 Thoughtworks Mingle 사용자 계정 생성 도구 또는 Thoughtworks Mingle이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수도 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Thoughtworks Mingle에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Thoughtworks Mingle** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->