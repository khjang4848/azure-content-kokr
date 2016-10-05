<properties
   pageTitle="Azure 데이터 카탈로그의 필수 구성 요소 | Microsoft Azure"
   description="Azure 데이터 카탈로그 필수 구성 요소 - Azure 데이터 카탈로그 시작에 필요합니다."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# Azure 데이터 카탈로그의 필수 구성 요소

## Azure 데이터 카탈로그를 시작하려면 어떻게 하나요?

**Azure 데이터 카탈로그**를 설정하기 전에 몇 가지 주의 사항이 있습니다. 걱정하지 마세요. 오래 걸리지 않습니다!

## Azure 구독
Azure Data Catalog를 설정하려면 Azure 구독의 소유자 또는 공동 소유자여야 합니다.

Azure 구독에서는 Azure 데이터 카탈로그와 같은 클라우드 서비스 리소스에 대한 액세스를 구성할 수 있습니다. 리소스 사용을 보고하고, 요금을 청구하고, 지불하는 방식을 제어할 수도 있습니다. 각 구독은 청구 및 지불 설정이 다를 수 있으므로 부서, 프로젝트, 지사 등에 따라 구독 및 계획이 다를 수 있습니다. 모든 클라우드 서비스는 구독에 속하고 Azure 데이터 카탈로그를 설정하기 전에 구독을 보유해야 합니다. 자세한 내용은 [계정, 구독 및 관리 역할 관리](../active-directory/active-directory-assign-admin-roles.md)를 참조하세요.

## Azure Active Directory
Azure Data Catalog를 설정하려면 Azure Active Directory 사용자 계정을 사용하여 로그인해야 합니다.

Azure Active Directory(Azure AD)는 클라우드 및 온-프레미스 모두에서비즈니스가 ID와 액세스를 쉽게 관리하는 방법을 제공합니다. 사용자는 클라우드 및 온-프레미스 웹 응용 프로그램에 SSO(Single Sign-on)를 위해 단일 회사 또는 학교 계정을 사용할 수 있습니다. Azure 데이터 카탈로그는 로그온 인증에 Azure AD를 사용합니다. 자세히 알아보려면 [Azure Active Directory란](../active-directory/active-directory-whatis.md)을 참조하세요.

> [AZURE.NOTE] [Azure 포털](http://portal.azure.com/)에서 사용자는 개인 Microsoft 계정 또는 Azure Active Directory 작업 또는 학교 계정을 사용하여 로그인할 수 있습니다. Azure 포털을 사용하거나 [데이터 카탈로그 포털](http://www.azuredatacatalog.com)을 사용하여 Azure Data Catalog를 설정하려면 개인 계정이 아닌, Azure Active Directory 계정을 사용하여 로그인해야 합니다.

## Active Directory 정책 구성

Azure 데이터 카탈로그 포털에 로그온할 수 있는 상황이 발생하는 경우도 있지만, 데이터 원본 등록 도구에 로그온을 시도할 때 로그온하지 않도록 하는 오류 메시지가 표시됩니다. 이 문제 동작은 사용자가 회사 네트워크에 있는 경우 또는 회사 네트워크 외부에서 연결하는 경우에만 발생할 수 있습니다.

데이터 원본 등록 도구가 폼 인증을 사용하여 Active Directory에 대한 사용자 로그온의 유효성 검사를 수행합니다. 로그온이 성공하려면 Active Directory 관리자가 전역 인증 정책에서 폼 인증을 사용하도록 설정해야 합니다.

전역 인증 정책을 사용하면 아래와 같이 인트라넷 및 엑스트라넷 연결에 대해 개별적으로 인증 방법을 사용하도록 설정할 수 있습니다. 폼 인증이 사용자가 연결되는 네트워크에 사용하도록 설정되지 않은 경우 로그온 오류가 발생할 수 있습니다.

 ![Active Directory 전역 인증 정책](./media/data-catalog-prerequisites/global-auth-policy.png)

자세한 내용은 [인증 정책 구성](https://technet.microsoft.com/library/dn486781.aspx)을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->