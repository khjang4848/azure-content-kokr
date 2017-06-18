<properties 
    pageTitle="자습서: Veracode와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Veracode를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Veracode와 Azure Active Directory 통합
  
이 자습서는 Azure 및 Veracode의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Veracode Single Sign-On이 설정된 구독
  
이 자습서를 완료하면 Veracode에 할당한 Azure AD 사용자가 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On으로 로그인할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Veracode에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-veracode-tutorial/IC802903.png "시나리오")

##Veracode에 응용 프로그램 통합 사용
  
이 섹션은 Veracode에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Veracode에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-veracode-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-veracode-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-veracode-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Veracode**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-veracode-tutorial/IC802904.png "응용 프로그램 갤러리")

7.  결과 창에서 **Veracode**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Veracode에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Veracode 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 할당 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![특성](./media/active-directory-saas-veracode-tutorial/IC802906.png "특성")

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Veracode** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-veracode-tutorial/IC802907.png "Single Sign-On 구성")

2.  **Veracode에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-veracode-tutorial/IC802908.png "Single Sign-On 구성")

3.  **응용 프로그램 설정 구성** 페이지에서 **다음**을 클릭합니다.

    ![앱 설정 구성](./media/active-directory-saas-veracode-tutorial/IC802909.png "앱 설정 구성")

4.  **Veracode에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-veracode-tutorial/IC802910.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Veracode 회사 사이트에 관리자 권한으로 로그인합니다.

6.  위쪽 메뉴에서 **설정**을 클릭한 다음 **관리자**를 클릭합니다.

    ![관리](./media/active-directory-saas-veracode-tutorial/IC802911.png "관리")

7.  **SAML** 탭을 클릭합니다.

8.  **조직 SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![관리](./media/active-directory-saas-veracode-tutorial/IC802912.png "관리")

    1.  Azure 클래식 포털의 **Veracode에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **발급자** 텍스트 상자에 붙여 넣습니다.
    2.  다운로드한 인증서를 업로드하려면 **파일 선택**을 클릭합니다.
    3.  **자체 등록 사용**을 선택합니다.

9.  **자체 등록 설정** 섹션에서 다음 단계를 수행하고 **저장**을 클릭합니다.

    ![관리](./media/active-directory-saas-veracode-tutorial/IC802913.png "관리")

    1.  **새 사용자 활성화**에 대해 **활성화 필요 없음**을 선택합니다.
    2.  **사용자 데이터 업데이트**에 대해 **기본 설정 Veracode 사용자 데이터**를 선택합니다.
    3.  **SAML 특성 세부 정보**에 대해 다음을 선택합니다.
        -   **사용자 역할**
        -   **정책 관리자**
        -   **검토자**
        -   **보안 팀장**
        -   **경영자**
        -   **제출자**
        -   **작성자**
        -   **모든 검색 형식**
        -   **팀 멤버 자격**
        -   **기본 팀**

10. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-veracode-tutorial/IC802914.png "Single Sign-On 구성")

11. 위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-veracode-tutorial/IC795920.png "특성")

12. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-veracode-tutorial/IC802906.png "특성")

	| 특성 이름 | 특성 값 |
	|:---------------|:----------------|
	| firstname | User.givenname |
	| lastname | User.surname |
	| email | User.mail |

    1.  위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    
	2.  **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    3.  **특성 값** 텍스트 상자에서 해당 행에 표시된 특성 값을 선택합니다.

    4.  **완료**를 클릭합니다.

13. **변경 내용 적용**을 클릭합니다.

##사용자 프로비전 구성
  
Azure AD 사용자가 Veracode에 로그인할 수 있도록 하려면 Veracode로 프로비전되어야 합니다. Veracode의 경우 프로비전은 자동 작업입니다. 작업 항목이 없습니다.
  
필요한 경우 첫 번째 Single Sign-On 시도에서 사용자가 자동으로 생성될 수 있습니다.

>[AZURE.NOTE] 다른 Veracode 사용자 계정 생성 도구 또는 Veracode가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Veracode에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Veracode** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-veracode-tutorial/IC802915.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-veracode-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->