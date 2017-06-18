<properties
	pageTitle="자습서: Optimizely와 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory와 Optimizely 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
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
	ms.date="09/11/2016"
	ms.author="jeedes"/>


# 자습서: Optimizely와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Optimizely를 통합하는 방법에 대해 알아봅니다.

Optimizely를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Optimizely 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Optimizely에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건

Optimizely와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- **Optimizely** Single Sign-On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## 시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Optimizely 추가
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 Optimizely 추가
Optimizely의 Azure AD 통합을 구성하려면 갤러리의 Optimizely를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Optimizely를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **Optimizely**를 입력합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. 결과 창에서 **Optimizely**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Optimizely에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Optimizely 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Optimizely의 관련 사용자 간에 연결이 형성되어야 합니다. 이 연결 관계는 Azure AD의 **사용자 이름** 값을 Optimizely의 **Username** 값으로 할당하여 설정합니다.

Optimizely에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Optimizely 테스트 사용자 만들기](#creating-an-optimizely-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Optimizely에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Optimizely 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

Optimizely 응용 프로그램은 SAML 어셜선이 "email"이라는 특성을 포함할 것으로 예상합니다. "email"의 값은 Azure AD에서 인증을 받을 수 있는 Optimizely 인식 전자 메일이어야 합니다. 이 응용 프로그램에 대한 "email" 클레임을 구성하세요. 응용 프로그램의 **"Atrributes"** 탭에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.


![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png)


**Optimizely에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Optimizely** 응용 프로그램 통합 페이지에 있는 상단 메뉴에서 **특성**을 클릭합니다.
     
    ![Single Sign-On 구성][5]

2. SAML 토큰 특성 대화 상자에서 "email" 특성을 추가합니다.

	a. **사용자 특성 추가**를 클릭하여 **사용자 특성 추가** 대화 상자를 엽니다.
	
	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

	b. **특성 이름** 텍스트 상자에서 특성 이름 "email"을 입력합니다.

	c. **특성 값** 목록에서 특성 값 "userprincipalname" 또는 Azure AD 및 Optimizely에서 인식하는 전자 메일을 포함하는 임의 값을 선택합니다.

	d. **완료**를 클릭합니다.
3. 위쪽 메뉴에서 **빠른 시작**을 클릭합니다.

	![Single Sign-On 구성][6]
4. 클래식 포털의 **Optimizely** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

	![Single Sign-On 구성][7]

5. **Optimizely에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
 	
	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    a. **로그온 URL** 텍스트 상자에 `https://app.optimizely.net/contoso`를 입력합니다.

	b. **식별자** 텍스트 상자에 `urn:auth0:optimizely:contoso`를 입력합니다.

	c. **Next**를 클릭합니다.


	> [AZURE.NOTE] **로그온 URL** 및 **식별자** 값은 실제 값에 대한 자리 표시자에 불과합니다. 이 자습서의 뒷부분에는 Optimizely에서 실제 값을 획득하기 위한 지침을 찾을 수 있습니다.

7. **Optimizely에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    a. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.

    b. **Single Sign-On 서비스 URL**을 복사합니다.

8. 응용 프로그램에 대해 구성된 SSO를 얻으려면 Optimizely 계정 관리자에 문의하고 다음 정보를 제공하세요.

    - 다운로드한 인증서
    - Single Sign-On 서비스 URL
 
    전자 메일에 대한 응답으로 Optimizely는 로그온 URL(SP에서 시작한 SSO) 및 식별자(서비스 공급자 엔터티 ID) 값을 제공합니다.

9. **앱 설정 구성** 대화 상자 페이지로 돌아간 후 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

	a. **로그온 URL** 텍스트 상자에 Optimizely에서 제공한 **SP 시작 SSO URL**을 입력합니다.

	b. **식별자** 텍스트 상자에 Optimizely에서 제공한 **서비스 공급자 엔터티 ID**를 입력합니다.

    c. **다음**을 클릭합니다.

10. **Optimizely에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
	
	![Azure AD Single Sign-On][10]

    a. Single Sign-On 구성 확인을 선택합니다.

    b. **Next**를 클릭합니다.

11. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
  	
	![Azure AD Single Sign-On][11]

12. 다른 브라우저 창에서 Optimizely 응용 프로그램에 로그온합니다.
13. 오른쪽 위 구석에 있는 계정 이름을 클릭하고 **계정 설정**을 클릭합니다.

	![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. 계정 탭에서 **개요** 섹션의 Single Sign-On 아래에 있는 **SSO 사용** 확인란을 선택합니다.

	![Azure AD Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다. 사용자 목록에서 **Britta Simon**을 선택합니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
	
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
	
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png)

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png)

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **Next**를 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png)

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다.

    e. **Next**를 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png)

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png)

    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.



### Optimizely 테스트 사용자 만들기

이 섹션에서는 Optimizely에서 Britta Simon이라는 사용자를 만듭니다.

1. 홈페이지에서 **Collaborators** 탭을 선택합니다.
2. **New Collaborator**를 클릭하여 프로젝트에 새 공동 작업자를 추가합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  전자 메일 주소를 입력하고 역할을 할당합니다. **초대**를 클릭합니다.


	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. 전자 메일 초대를 받게 됩니다. 전자 메일 주소를 사용하여 Optimizely에 로그인해야 합니다.


### Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Optimizely에 대한 액세스 권한을 부여합니다.

![사용자 할당][200]

**Britta Simon을 Optimizely에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![사용자 할당][201]

2. 응용 프로그램 목록에서 **Optimizely**를 선택합니다.

	![Single Sign-On 구성](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png)

1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

1. 모든 사용자 목록에서 **Britta Simon**을 선택합니다.

2. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

	![사용자 할당][205]


### Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 Optimizely 타일을 클릭하면 Optimizely 응용 프로그램에 자동으로 로그온됩니다.

## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0914_2016-->