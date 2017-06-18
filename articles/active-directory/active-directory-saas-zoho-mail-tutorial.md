<properties 
    pageTitle="자습서: Azure Active Directory와 Zoho Mail 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Zoho Mail을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.author="markvi" />

#자습서: Azure Active Directory와 Zoho Mail 통합
  
이 자습서에서는 Azure와 Zoho Mail의 통합을 보여줍니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Zoho 메일 테넌트
  
이 자습서를 완료하면 Zoho mail에 할당한 Azure AD 사용자가 Zoho mail 회사 사이트 (로그온을 시작한 서비스 공급자)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Zoho Mail에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "시나리오")

##Zoho Mail에 응용 프로그램 통합 사용
  
이 섹션에서는 Zoho Mail에 응용 프로그램 통합 사용하는 방법을 설명합니다.

###Zoho Mail에 응용 프로그램 통합을 사용하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에서 **Zoho Mail**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "응용 프로그램 갤러리")

7.  결과 창에서 **Zoho Mail**을 선택하고 **완료**를 눌러 응용 프로그램을 추가합니다.

    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

##Single Sign-On 구성
  
이 섹션의 목적은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD에서 자신의 계정으로 Zoho Mail에 인증할 수 있게 하는 방법을 간략하게 설명하는 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Zoho Mail** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Single Sign-On 구성")

2.  **Zoho mail에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택한 후, **다음**을 클릭하세요.

    ![Single Sign-On 구성](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "앱 URL 구성")

	a. **Zoho Mail 로그인 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `http://<company name>.ZohoMail.com`

	b. **Next**를 클릭합니다.


4.  **Zoho mail에서 Single Sign-On 구성** 페이지에서, **인증서 다운로드**를 클릭하여 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 관리자 권한으로 Zoho Mail 회사 사이트에 로그인 합니다.

6.  **제어판**으로 이동합니다.

    ![제어판](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "제어판")

7.  **SAML 인증** 탭을 클릭합니다.

    ![SAML 인증](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML 인증")

8.  **SAML 인증 세부 정보** 섹션에서 다음 단계를 수행합니다.

    ![SAML 인증 세부 정보](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML 인증 세부 정보")

    1.  Azure 클래식 포털의 **Zoho Mail에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여 넣습니다.
    2.  Azure 클래식 포털의 **Zoho Mail에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여 넣습니다.
    3.  Azure 클래식 포털의 **Zoho Mail에서 Single Sign-On 구성** 대화 상자 페이지에서 **암호 변경 URL** 값을 복사한 다음 **암호 변경 URL** 텍스트 상자에 붙여 넣습니다.
    4.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    5.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PublicKey** 텍스트 상자에 붙여 넣습니다.
    6.  **알고리즘**으로 **RSA**를 선택합니다.
    7.  **확인**을 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 Zoho mail에 로그인할 수 있도록 하려면 사용자 계정이 Zoho mail로 프로비전되어야 합니다. Zoho mail의 경우, 수동으로 프로비전합니다.

###사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  **Zoho Mail** 회사 사이트에 관리자로 로그인합니다.

2.  **제어판 > 메일 & 문서**로 이동합니다.

3.  **사용자 세부 정보 > 사용자 추가**로 이동합니다.

    ![사용자 추가](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "사용자 추가")

4.  **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "사용자 추가")

    1.  프로비전하려는 유효한 Azure Active Directory 계정의 **성**, **이름**, **이메일 ID**, **암호**를 관련된 텍스트 상자에 입력합니다.
    2.  **확인**을 클릭합니다.

        >[AZURE.NOTE] Azure Active Directory 계정 소유자는 계정 활성화 전에 계정을 확인하는 링크가 포함된 이메일을 받습니다.

>[AZURE.NOTE] 다른 Zoho Mail 사용자 계정 생성 도구 또는 Zoho Mail에서 제공하는 APIs를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Zoho 메일에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Zoho 메일** 응용 프로그램 통합 페이지에서 **사용자 할당** 을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->