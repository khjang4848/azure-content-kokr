<properties 
    pageTitle="자습서: Spring CM과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Spring CM을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Spring CM과 Azure Active Directory 통합
  
이 자습서는 Azure Active Directory와 Spring CM 사이에 Single Sign-On을 설정하는 방법을 보여주기 위한 것입니다.
  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   SpringCM Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 SpringCM에 할당한 Azure Active Directory 사용자가 AAD 액세스 패널에서 Single Sign-On을 수행할 수 있습니다.

1.  SpringCM에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "시나리오")

##SpringCM에 응용 프로그램 통합 사용
  
이 섹션은 SpringCM에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###SpringCM에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **SpringCM**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "응용 프로그램 갤러리")

7.  결과 창에서 **SpringCM**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##Single Sign-On 구성
  
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure Active Directory의 계정으로 SpringCM에 인증할 수 있게 하는 방법을 간략하게 설명합니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **SpringCM** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Single Sign-On 구성")

2.  **SpringCM에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **SpringCM Sign On URL** 텍스트 상자에 SpringCM 응용 프로그램에 로그온하기 위해 사용자가 사용한 URL을 입력한 후 **다음**을 클릭합니다.

    앱 URL은 SpringCM 테넌트 URL입니다(예: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*:).

    ![앱 URL 구성](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "앱 URL 구성")

4.  **SpringCM에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single SignOn 구성](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Single SignOn 구성")

5.  다른 웹 브라우저 창에서 **SpringCM** 회사 사이트에 관리자로 로그인합니다.

6.  위쪽에 있는 메뉴에서 **이동**을 클릭하고 **기본 설정**을 클릭한 다음 **계정 기본 설정** 섹션에서 **SAML SSO**를 클릭합니다.

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  ID 공급자 구성 섹션에서 다음 단계를 수행합니다.

    ![Id 공급자 구성](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Id 공급자 구성")

    1.  다운로드한 Azure Active Directory 인증서를 업로드하려면 **발급자 인증서 선택** 또는 **발급자 인증서 변경**을 클릭합니다.
    2.  Azure 클래식 포털의 **SpringCM에서 Single Sign-On 설정** 대화 상자 페이지에서**발급자 URL** 값을 복사하여 **발급자** 텍스트 상자에 붙여 넣습니다.
    3.  Azure 클래식 포털의 **SpringCM에 대한 Single Sign-On 구성** 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **서비스 공급자(SP)가 시작하는 끝점** 텍스트 상자에 붙여 넣습니다.
    4.  **SAML 사용**으로 설정되었을 때 **사용**을 선택합니다.
    5.  **Save**를 클릭합니다.

8.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single SignOn 구성](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Single SignOn 구성")

##사용자 프로비전 구성
  
Azure Active Directory 사용자가 SpringCM에 로그인할 수 있도록 하려면 SpringCM으로 프로비전되어야 합니다. SpringCM의 경우 프로비전은 수동 작업입니다.

>[AZURE.NOTE] 자세한 내용은 [SpringCM 사용자 만들기 및 편집](http://knowledge.springcm.com/create-and-edit-a-springcm-user)을 참조하십시오.

###사용자 계정을 SpringCM에 프로비전하려면 다음 단계를 수행합니다.

1.  **SpringCM** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **GOTO**를 클릭하고 **주소록**을 클릭합니다.

    ![사용자 만들기](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "사용자 만들기")

3.  **사용자 만들기**를 클릭합니다.

4.  **사용자 역할**을 선택합니다.

5.  **활성화 메일 보내기**를 선택합니다.

6.  관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 사용자 계정의 이름, 성 및 메일 주소를 입력합니다.

7.  **보안 그룹**에 사용자를 추가합니다.

8.  **Save**를 클릭합니다.

>[AZURE.NOTE] 다른 SpringCM 사용자 계정 생성 도구 또는 SpringCM이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###SpringCM에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **SpringCM** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0921_2016-->