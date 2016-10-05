<properties
	pageTitle="자습서: SilkRoad Life Suite와 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory와 SilkRoad Life Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# 자습서: SilkRoad Life Suite와 Azure Active Directory 통합

이 자습서에서는 SilkRoad Life Suite와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다. SilkRoad Life Suite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 사용자의 SilkRoad Life Suite에 대한 액세스 권한을 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 SilkRoad Life Suite에 자동으로 로그온(Single Sign-on)할 수 있도록 설정할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건 

SilkRoad Life Suite와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SilkRoad Life Suite Single-Sign-On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

 
## 시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SilkRoad Life Suite 추가
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 SilkRoad Life Suite 추가
SilkRoad Life Suite가 Azure AD에 통합되도록 구성하려면 갤러리의 SilkRoad Life Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SilkRoad Life Suite를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **SilkRoad Life Suite**를 입력합니다.

	![응용 프로그램][5]

7. 결과 창에서 **SilkRoad Life Suite**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![응용 프로그램][50]


##  Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SilkRoad Life Suite에서 Azure AD Single Sign-on을 구성하고 테스트하는 방법을 설명합니다.

Single Sign-On이 작동하려면 Azure AD는 SilkRoad Life Suite를 사용하려는 사용자가 Azure AD의 어떤 사용자인지 알아야 합니다. 즉 Azure AD 사용자와 SilkRoad Life Suite 사용자 간 연결이 필요합니다. 이 연결은 Azure AD의 **사용자 이름** 값을 SilkRoad Life Suite의 **Username** 값으로 할당하면 이루어집니다.
 
SilkRoad Life Suite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[SilkRoad Life Suite 테스트 사용자 만들기](#creating-a-silkroad-life-suite-test-user)** - Britta Simon이라는 Azure AD 사용자와 연결된 SilkRoad Life Suite 사용자를 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션에서는 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 SilkRoad Life Suite에서 Single Sign-On을 구성하는 방법을 설명합니다.

**SilkRoad Life Suite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

5. SilkRoad 회사 사이트에 관리자로 로그인합니다.


    > [AZURE.NOTE] Microsoft Azure AD와 페더레이션을 구성하기 위한 SilkRoad Life 인증 응용 프로그램에 대한 액세스를 얻으려면 SilkRoad 지원 또는 SilkRoad 서비스 담당자에게 문의하세요.


6. **서비스 공급자**로 이동한 다음 **페더레이션 세부 정보**를 클릭합니다.

	![Azure AD Single Sign-On][10]


1. **페더레이션 메타데이터 다운로드**를 클릭한 후, 컴퓨터에 메타데이터 파일을 저장 합니다.

	![Azure AD Single Sign-On][11]

3. Azure 클래식 포털의 **SilkRoad Life Suite** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

	![Single Sign-On 구성][6]

2. **SilkRoad Life Suite에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고**다음**을 클릭합니다.

	![Azure AD Single Sign-On][7]

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD Single Sign-On][8]
 
    a. **로그온 URL** 텍스트 상자에 사용자가 SilkRoad Life Suite 사이트에 로그인하는 데 사용하는 URL을 입력합니다.(예: *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).

    b. 다운로드한 **Silkroad** 메타데이터 파일을 엽니다.

    c. **AssertionConsumerService** 태그를 찾은 후 **위치** 특성을 복사합니다.

	![Azure AD Single Sign-On][21]
   
    d. **Reply URL** 텍스트 상자에 값을 붙여 넣습니다.
 
    e. **Next**를 클릭합니다.
 
4. **SilkRoad Life Suite의 Single Sign-On을 구성** 페이지에서 다음 단계를 수행합니다.

	![Azure AD Single Sign-On][9]

    a. 인증서 다운로드를 클릭하고 파일을 컴퓨터에 저장합니다.

    b. **Next**를 클릭합니다.




1. **SilkRoad** 응용 프로그램에서 **인증 원본**을 클릭합니다.

	![Azure AD Single Sign-On][12]



1. **인증 원본 추가**를 클릭합니다.

	![Azure AD Single Sign-On][13]



1. **인증 원본 추가** 섹션에서 다음 단계를 수행합니다.

	![Azure AD Single Sign-On][14]

    a. **옵션2 - 메타데이터 파일**에서 **찾아보기**를 클릭하여 다운로드한 메타데이터 파일을 업로드합니다.

    b. **파일 데이터를 사용하여 ID 공급자 만들기**를 클릭합니다.



1. **인증 원본** 섹션에서 **편집**을 클릭합니다.

	![Azure AD Single Sign-On][15]


1. **인증 원본 편집** 대화 상자에서 다음 단계를 수행합니다.

	![Azure AD Single Sign-On][16]

    a. **사용**을 **예**로 선택합니다.

    b. **IdP 설명** 텍스트 상자에 구성에 대한 설명을 입력합니다.(예: *Azure AD SSO*)

    c. **IdP 이름** 텍스트 상자에 구성에 적용되는 이름을 입력합니다.(예: *Azure SP*)

    d. **Save**를 클릭합니다.


6. 다른 모든 인증 원본을 사용하지 않도록 설정합니다.

	![Azure AD Single Sign-On][17]

7. Azure 클래식 포털의 **Single Sign-On 확인** 페이지에서 **다음**을 클릭합니다.

	![Azure AD Single Sign-On][18]

1. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.

	![Azure AD Single Sign-On][19]


### Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)
 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **Next**를 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)
 
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다. e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png)
 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)
  
    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.

  
 
### SilkRoad Life Suite 테스트 사용자 만들기

이 섹션에서는 SilkRoad Life Suite에서 Britta Simon이라는 사용자를 만들어 보겠습니다. Britta는 Azure AD에서 Britta의 **emailaddress**와 일치하는 SSO ID(종종 *AuthParam*라고 함)가 있어야 합니다

**SilkRoad Life Suite에서 Britta Simon라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. SilkRoad Life Suite 지원 팀에 요청하여 Azure AD에서 Britta Simon이라는 **emailaddress**가 동일한 값인 **SSO ID** 특성을 가진 사용자를 만듭니다.



### Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon에게 SilkRoad Life Suite에 대한 액세스 권한을 부여하여 Azure Single Sign-On을 사용할 수 있도록 하는 방법을 설명합니다.

![사용자 할당][200]

**Britta Simon을 SilkRoad Life Suite에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![사용자 할당][201]

2. 응용 프로그램 목록에서 **SilkRoad Life Suite**를 선택합니다.

	![사용자 할당][202]

1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

1. 사용자 목록에서 **Britta Simon**을 선택합니다.

2. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

	![사용자 할당][205]



### Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다. 액세스 패널에서 SilkRoad Life Suite 타일을 클릭하면 SilkRoad Life Suite 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->