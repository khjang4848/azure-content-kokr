<properties
	pageTitle="자습서: SAP Business ByDesign과 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory 및 SAP Business ByDesign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="jeedes"/>


# 자습서: SAP Business ByDesign과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Business ByDesign을 통합하는 방법에 대해 알아봅니다.

SAP Business ByDesign을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SAP Business ByDesign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP Business ByDesign에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건

SAP Business ByDesign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SAP Business ByDesign Single Sign-On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## 시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP Business ByDesign 추가
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 SAP Business ByDesign 추가
SAP Business ByDesign의 Azure AD 통합을 구성하려면 갤러리의 SAP Business ByDesign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP Business ByDesign을 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.


3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **SAP Business ByDesign**을 입력합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_01.png)

7. 결과 창에서 **SAP Business ByDesign**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![Active Directory](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_02.png)


##  Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP Business ByDesign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP Business ByDesign 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP Business ByDesign의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 SAP Business ByDesign의 **Username** 값으로 할당하여 설정합니다.

SAP Business ByDesign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SAP Business ByDesign 테스트 사용자 만들기](#creating-an-sap-business-bydesign-test-user)** - Azure AD를 대표하여 SAP Business ByDesign에서 Britta Simon에 해당하는 사용자가 있어야 합니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션에서는 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP Business ByDesign 응용 프로그램에서 Single Sign-On을 구성합니다.

SAP Business ByDesign 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대한 다음 클레임을 구성하세요. 응용 프로그램의 **"특성"** 탭에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.


**SAP Business ByDesign에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**


1. Azure 클래식 포털의 **SAP Business ByDesign** 응용 프로그램 통합 페이지에 있는 상단 메뉴에서 **특성**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_80.png)


2. 특성의 SAML 토큰 특성 목록에서 nameidentifier 특성을 선택한 다음 **편집**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_84.png)

3. 사용자 특성 편집 대화 상자에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_85.png)

	a. 특성 값 목록에서 **ExtractMailPrefix()** 함수를 선택합니다.

	b. 메일 목록에서 구현에 사용할 사용자 특성을 선택합니다. 예를 들어, EmployeeID를 고유한 사용자 식별자로 사용하고자 하고 ExtensionAttribute2에 특성 값을 저장했다면 **user.extensionattribute2**를 선택합니다.

	c. **완료**를 클릭합니다.
	

4. 클래식 포털의 **SAP Business ByDesign** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
	 
	![Single Sign-On 구성][6]

5. **SAP Business ByDesign에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_03.png)

6. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_04.png)

    a. **로그인 URL** 텍스트 상자에 다음 패턴을 사용하여 사용자가 SAP Business ByDesign 응용 프로그램에 로그인하는 데 사용할 URL을 입력합니다. `https://<servername>.sapbydesign.com`
	
	b. **다음**을 클릭합니다.
 
7. **SAP Business ByDesign에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_05.png)

    a. **메타데이터 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.

    b. **다음**을 클릭합니다.


8. 응용 프로그램에 SSO를 구성하려면 다음 단계를 수행합니다.

	a. 관리자 권한으로 SAP Business ByDesign 포털에 로그인합니다.

	b. **응용 프로그램 및 사용자 관리 일반 작업**으로 이동하여 **ID 공급자** 탭을 클릭합니다.

	c. **새 ID 공급자**를 클릭하고 Azure 클래식 포털에서 다운로드한 메타데이터 XML 파일을 선택합니다. 시스템은 메타데이터를 가져와서 필수 서명 인증서 및 암호화 인증서를 자동으로 업로드합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

	d. **어설션 소비자 서비스 URL**을 SAML 요청에 포함하려면 **어설션 소비자 서비스 URL 포함**을 선택합니다.

	e. **Single Sign-on 활성화**를 클릭합니다.

	f. 변경 내용을 저장합니다.

	g. **내 시스템** 탭을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

	h. **SSO URL**을 복사하고 **Azure AD 로그온 URL** 텍스트 상자에 붙여 넣습니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

	i 직원이 **수동 ID 공급자 선택**을 선택하여 사용자 ID 및 암호 또는 SSO를 사용하는 로그온 중 하나를 수동으로 선택할 수 있는지 여부를 지정합니다.

	j. **SSO URL** 섹션에서 시스템에 로그온하려는 직원이 사용해야 하는 URL을 지정합니다. 직원에게 전송된 URL 드롭다운 목록에서 다음 옵션 중 하나를 선택할 수 있습니다.
	
	**SSO가 아닌 URL**
 
	시스템은 직원에게 정상적인 시스템 URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 없고 대신 암호 또는 인증서를 사용해야 합니다.

	**SSO URL**

	시스템은 직원에게 SSO URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 있습니다. IdP를 통해 인증 요청이 리디렉션됩니다.

	**자동 선택**
 
	SSO가 활성 상태가 아닌 경우 시스템은 직원에게 정상적인 시스템 URL을 보냅니다. SSO가 활성 상태인 경우 시스템은 직원이 암호를 가지는지 여부를 확인합니다. 암호를 사용할 수 있는 경우 SSO URL와 SSO가 아닌 URL은 직원에게 전송됩니다. 그러나 직원에게 암호가 없는 경우 SSO URL만이 직원에게 전송됩니다.

	k. 변경 내용을 저장합니다.

9. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
	
	![Azure AD Single Sign-On][10]

10. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
 
	![Azure AD Single Sign-On][11]


### Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.


![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_09.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
	
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_05.png)

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **Next**를 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
	
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_06.png)

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다.

    e. **Next**를 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_07.png)

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapbusinessbydesign-tutorial/create_aaduser_08.png)

    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.



### SAP Business ByDesign 테스트 사용자 만들기

이 섹션에서는 SAP Business ByDesign에서 Britta Simon이라는 사용자를 만듭니다. SAP Business ByDesign 지원 팀과 함께 SAP Business ByDesign 플랫폼에 사용자를 추가하세요.

> [AZURE.NOTE] NameID 값이 SAP Business ByDesign 플랫폼에서 사용자 이름 필드와 일치하는지 확인하세요.

### Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Business ByDesign에 대한 액세스 권한을 부여합니다.

![사용자 할당][200]

**Britta Simon을 SAP Business ByDesign에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![사용자 할당][201]

2. 응용 프로그램 목록에서 **SAP Business ByDesign**을 선택합니다.

	![Single Sign-On 구성](./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_50.png)

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

	![사용자 할당][205]


### Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Business ByDesign 타일을 클릭하면 SAP Business ByDesign 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sapbusinessbydesign-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->