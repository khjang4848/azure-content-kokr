<properties 
    pageTitle="자습서: TOPdesk - Public과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 TOPdesk - Public을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: TOPdesk - Public과 Azure Directory 통합

이 자습서는 Azure 및 TOPdesk - Public의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   TOPdesk - Public Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 TOPdesk – Public에 할당한 Azure AD 사용자가 TOPdesk – Public 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  TOPdesk - Public에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "시나리오")

##TOPdesk - Public에 응용 프로그램 통합 사용
  
이 섹션은 TOPdesk - Public에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###TOPdesk - Public에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **TOPdesk - Public**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "응용 프로그램 갤러리")

7.  결과 창에서 **TOPdesk - Public**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![TOPdesk Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 TOPdesk - Public에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
TOPdesk - Public에 대한 Single Sign-On을 구성하려면 로고 아이콘 파일을 업로드해야 합니다. 아이콘 파일을 가져오려면 TOPdesk 지원팀에 문의합니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2.  **TOPdesk** 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "설정")

3.  **로그인 설정**을 클릭합니다.

    ![로그인 설정](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "로그인 설정")

4.  **로그인 설정** 메뉴를 확장한 다음 **일반**을 클릭합니다.

    ![일반](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "일반")

5.  **SAML 로그인** 구성 섹션의 **공용**섹션에서 다음 단계를 수행합니다.

    ![기술 설정](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "기술 설정")

    1.  **다운로드**를 클릭하여 공용 메타데이터 파일을 다운로드한 다음 컴퓨터에 로컬 저장합니다.
    2.  메타데이터 파일을 열고 **AssertionConsumerService** 노드를 찾습니다.
    ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
    3.  **AssertionConsumerService** 값을 복사합니다.

        >[AZURE.NOTE] 해당 값은 자습서 뒷부분의 **앱 URL 구성** 섹션에서 필요합니다.

6.  다른 웹 브라우저 창에서 **Azure 클래식 포털**에 관리자 권한으로 로그인합니다.

7.  **TOPdesk - Public** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Single Sign-On 구성")

8.  **TOPdesk - Public에 대한 사용자 로그온 방법을 선택하십시오** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Single Sign-On 구성")

9.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "앱 URL 구성")

    1.  **TOPdesk - Public Sign On URL** 텍스트 상자에서 TOPdesk - 사용자가 Public 응용 프로그램 로그인에 사용한 URL을 입력합니다(예: "*https://qssolutions.topdesk.net*").
    2.  **TOPdesk – Public Reply URL** 텍스트 상자에서 **TOPdesk - Public AssertionConsumerService URL**을 붙여 넣기합니다 (예: "*https://qssolutions.topdesk.net/tas/public/login/saml*")
    3.  **Next**를 클릭합니다.

10. **TOPdesk - Public에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Single Sign-On 구성")

11. 인증서 파일을 만들려면 다음 단계를 수행하십시오.

    ![인증서](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "인증서")

    1.  다운로드한 메타데이터 파일을 엽니다.
    2.  **fed:ApplicationServiceType**의 **xsi:type**을 가진 **RoleDescriptor** 노드를 확장합니다.
    3.  **X509Certificate** 노드의 값을 복사합니다.
    4.  복사한 **X509Certificate** 값을 컴퓨터에 파일로 로컬 저장합니다.

12. TOPdesk - Public 회사 사이트의 **TOPdesk** 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "설정")

13. **로그인 설정**을 클릭합니다.

    ![로그인 설정](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "로그인 설정")

14. **로그인 설정** 메뉴를 확장한 다음 **일반**을 클릭합니다.

    ![일반](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "일반")

15. **공용** 섹션에서 **추가**를 클릭합니다.

    ![SAML 로그인](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML 로그인")

16. **SAML 구성 도우미** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![SAML 구성 도우미](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML 구성 도우미")

    1.  다운로드한 메타데이터 파일을 업로드하려면 **페더레이션 메타데이터**에서 **찾아보기**를 클릭합니다.
    2.  인증서 파일을 업로드하려면 **인증서 (RSA)**에서 **찾아보기**를 클릭합니다.
    3.  TOPdesk 지원팀에서 받은 로고 파일을 업로드하려면 **로고 아이콘**에서 **찾아보기**를 클릭합니다.
    4.  **사용자 이름 특성** 텍스트 상자에서 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**을 입력합니다.
    5.  **이름 표시** 텍스트 상자에 구성할 이름을 입력합니다.
    6.  **Save**를 클릭합니다.

17. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 TOPdesk - Public에 로그인할 수 있도록 하려면 사용자가 TOPdesk - Public으로 프로비전되어야 합니다.
TOPdesk - Public의 경우 프로비전은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면

1.  **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2.  위쪽 메뉴에서 **TOPdesk > 새로 만들기 > 지원 파일 > 사람** 순으로 클릭합니다.

    ![사람](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "사람")

3.  새로운 사용자 대화 상자에서 다음 단계를 수행합니다.

    ![새로운 사람](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "새로운 사람")

    1.  일반 탭을 클릭합니다.
    2.  성 텍스트 상자에서 프로비전하려는 유효한 Azure Active Directory 계정의 성을 입력합니다.
    3.  계정에 대한 **사이트**를 선택합니다.
    4.  **Save**를 클릭합니다.

>[AZURE.NOTE] 다른 TOPdesk - Public 사용자 계정 생성 도구 또는 TOPdesk - Public이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###TOPdesk - Public에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **TOPdesk - Public** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0914_2016-->