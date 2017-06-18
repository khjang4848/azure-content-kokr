<properties 
    pageTitle="자습서: Workday와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Workday를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#자습서: Workday와 Azure Active Directory 통합
  
이 자습서는 Azure와 Workday의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Workday 내의 테넌트
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Workday에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 프로비전 구성

![시나리오](./media/active-directory-saas-workday-tutorial/IC782919.png "시나리오")

##Workday에 응용 프로그램 통합 사용
  
이 섹션에서는 Workday에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###Workday에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-workday-tutorial/IC700994.png "응용 프로그램")

4.  **응용 프로그램 갤러리**를 열려면 **앱 추가**를 클릭한 다음 **조직에서 사용할 응용 프로그램 추가**를 클릭합니다.

    ![원하는 작업을 선택하세요.](./media/active-directory-saas-workday-tutorial/IC700995.png "원하는 작업을 선택하세요.")

5.  **검색 상자**에 **Workday**를 입력합니다.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6.  결과 창에서 **Workday**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Workday에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서를 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  **Workday** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/IC782920.png "Single Sign-On 구성")

2.  **Workday에 대한 사용자 로그온 방법을 선택하세요** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/IC782921.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행하고 **다음**을 선택합니다.

    ![앱 URL 구성](./media/active-directory-saas-workday-tutorial/IC782957.png "앱 URL 구성")

	a. **로그인 URL** 텍스트 상자에서 다음 패턴을 사용하여 Workday에 로그인할 때 사용자가 사용한 URL을 입력합니다. `https://impl.workday.com/<tenant>/login-saml2.htmld`

	b. **Workday 회신 URL** 텍스트 상자에서 다음 패턴을 사용하여 Workday 회신 URL을 입력합니다. `https://impl.workday.com/<tenant>/login-saml.htmld`

	>[AZURE.NOTE] 회신 URL에 하위 도메인이 있어야 합니다(예: www, wd2, wd3, wd3-impl, wd5, wd5-impl). "*http://www.myworkday.com*" 등을 사용하면 작동하지만 "*http://myworkday.com*"을 사용하면 작동하지 않습니다.
 
4.  **Workday에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/IC782922.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Workday 회사 사이트에 관리자 권한으로 로그인합니다.

6.  **메뉴 > 워크벤치**로 이동합니다.

    ![워크벤치](./media/active-directory-saas-workday-tutorial/IC782923.png "워크벤치")

7.  **계정 관리**로 이동합니다.

    ![계정 관리](./media/active-directory-saas-workday-tutorial/IC782924.png "계정 관리")

8.  **테넌트 설정 편집 - 보안**으로 이동합니다.

    ![테넌트 보안 편집](./media/active-directory-saas-workday-tutorial/IC782925.png "테넌트 보안 편집")

9.  **리디렉션 URL** 섹션에서 다음 단계를 수행합니다.

    ![리디렉션 URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "리디렉션 URL")

	a. **행 추가**를 클릭합니다.

	b. **로그인 리디렉션 URL** 텍스트 상자 및 **모바일 리디렉션 URL** 텍스트 상자에서 Azure 클래식 포털의 **앱 URL 구성** 페이지에 입력한 **Workday 테넌트 URL**을 입력합니다.
    
	c. Azure 클래식 포털의 **Workday에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-Out 서비스 URL**을 복사한 다음 **로그아웃 리디렉션 URL** 텍스트 상자에 붙여 넣습니다.

	d. **환경** 텍스트 상자에서 환경 이름을 입력합니다.


	>[AZURE.NOTE] 테넌트 URL의 값에 연결된 환경 특성의 값은:
	>
    >-   Workday 테넌트 URL의 도메인 이름이 impl (예:*https://impl.workday.com/\<tenant>/login-saml2.htmld*)로 시작하면, **환경** 특성이 구현에 설정되어있어야 합니다.
    >-   다른 것으로 시작하는 도메인 이름의 경우 Workday에 문의해 일치하는 **환경** 값을 얻어야 합니다.

10. **SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![SAML 설정](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML 설정")

	a. **SAML 인증 사용**을 선택합니다.

	b. **행 추가**를 클릭합니다.

11. SAML ID 공급자 섹션에서 다음 단계를 수행합니다.

    ![SAML ID 공급자](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML ID 공급자")

	a. ID 공급자 이름 텍스트 상자에 공급자 이름(예:*SPInitiatedSSO*)을 입력합니다.

    b. Azure 클래식 포털의 **Workday에서 Single Sign-On 구성** 대화 상자 페이지에서 **ID 공급자 ID** 값을 복사한 다음 **발급자** 텍스트 상자에 붙여 넣습니다.

    c. **Workday가 시작한 로그아웃 사용**을 선택합니다.

    d. Azure 클래식 포털의 **Workday에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **로그아웃 요청 URL** 텍스트 상자에 붙여 넣습니다.


    e. **ID 공급자 공개 키 인증서**를 클릭한 다음 **만들기**를 클릭합니다.

	![생성](./media/active-directory-saas-workday-tutorial/IC782928.png "생성")

    f. **x509 공개 키 만들기**를 클릭합니다.
        
	![생성](./media/active-directory-saas-workday-tutorial/IC782929.png "생성")


1. **x509 공개 키 보기** 섹션에서 다음 단계를 수행합니다.

	![x509 공개 키 보기](./media/active-directory-saas-workday-tutorial/IC782930.png "x509 공개 키 보기")

	a. **이름** 텍스트 상자에 인증서 이름(예: *PPE\_SP*)을 입력합니다.
    	
	b. **유효 시작** 텍스트 상자에 인증서의 유효 시작 특성 값을 입력합니다.
    
	c. **유효 만료** 텍스트 상자에 인증서의 유효 만료 특성 값을 입력합니다.
		
    >[AZURE.NOTE] 유효 시작일과 유효 만료일은 다운로드한 인증서를 두 번 클릭하여 확인할 수 있습니다. 날짜는 **세부 정보** 탭에 나열됩니다.

	d. 다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

	>[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

	e. 메모장에서 Base-64로 인코딩된 인증서를 열고 콘텐츠를 복사합니다.
    
	f. **인증서** 텍스트 상자에 클립보드의 내용을 붙여 넣습니다.
    
	g. **확인**을 클릭합니다.

12.  다음 단계를 수행합니다.

	![SSO 구성](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO 구성")

	a. **x509 개인 키 쌍**을 사용하도록 설정합니다.

	b. **서비스 공급자 ID** 텍스트 상자에 **http://www.workday.com**을 입력합니다.

	c. **SP가 시작한 SAML 인증 사용**을 선택합니다.

	d. Azure 클래식 포털의 **Workday에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **IdP SSO 서비스 URL** 텍스트 상자에 붙여 넣습니다.
     
	e. **SP에서 시작한 인증 요청을 Deflate하지 않음**을 선택합니다.

    f. **인증 요청 서명 메서드**로 **SHA256**를 선택합니다.
        
	![인증 요청 서명 메서드](./media/active-directory-saas-workday-tutorial/IC782932.png "인증 요청 서명 메서드")
 
	g. **확인**을 클릭합니다.
        
	![확인](./media/active-directory-saas-workday-tutorial/IC782933.png "확인")

12. Azure 클래식 포털의 **Workday에서 Single Sign-On 구성** 페이지에서 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/IC782934.png "Single Sign-On 구성")

13. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/IC782935111.png "Single Sign-On 구성")



##사용자 프로비전 구성
  
Workday에 테스트 사용자를 프로비전하려면 Workday 지원팀에 문의해야 합니다. Workday 지원팀이 사용자를 만듭니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Workday에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Workday** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-workday-tutorial/IC782935.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-workday-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->