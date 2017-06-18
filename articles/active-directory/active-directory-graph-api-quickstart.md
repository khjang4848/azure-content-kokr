<properties
   pageTitle="Azure AD Graph API 빠른 시작 | Microsoft Aure"
   description="Azure Active Directory Graph API는 OData REST API 끝점을 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있게 합니다. 응용 프로그램은 Graph API를 사용하여 디렉터리 데이터 및 개체에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다."
   services="active-directory"
   documentationCenter="n/a"
   authors="JimacoMS"
   manager="msmbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="v-jibran@microsoft.com"/>

# Azure AD Graph API 빠른 시작

Azure AD(Active Directory) Graph API는 OData REST API 끝점을 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있게 합니다. 응용 프로그램은 Graph API를 사용하여 디렉터리 데이터 및 개체에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다. 예를 들어 Graph API를 사용하여 새 사용자를 만들고, 사용자 속성을 보거나 업데이트하고, 사용자 암호를 변경하고, 역할 기반 액세스를 위한 그룹 구성원 자격을 확인하고, 사용자를 사용하지 않도록 설정 또는 삭제할 수 있습니다. Graph API 기능 및 응용 프로그램 시나리오에 대한 자세한 내용은 [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 및 [Azure AD Graph API 필수 조건](https://msdn.microsoft.com/library/hh974476.aspx)을 참조하세요.

> [AZURE.IMPORTANT] Azure AD Graph API 기능은 [Microsoft Graph](https://graph.microsoft.io/)를 통해서도 사용 가능하며, Outlook, OneDrive, OneNote, Planner 및 Office Graph와 같은 다른 Microsoft 서비스의 API를 포함하는 통합 API로, 단일 끝점과 단일 액세스 토큰을 통해 액세스 가능합니다.

## Graph API URL을 생성하는 방법

Graph API에서 디렉터리 데이터 및 CRUD 작업을 수행하려는 개체(즉, 리소스 또는 엔터티)에 액세스하려면 OData(개방형 데이터) 프로토콜을 기반으로 하는 URL을 사용할 수 있습니다. Graph API에서 사용되는 URL은 서비스 루트, 테넌트 식별자, 리소스 경로 및 쿼리 문자열 옵션의 네 가지 주요 부분으로 구성됩니다. `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. 다음 URL을 예로 들어보겠습니다. `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **서비스 루트**: Azure AD Graph API에서 서비스 루트는 항상 https://graph.windows.net입니다.
- **테넌트 식별자**: 확인된(등록된) 도메인 이름일 수 있습니다(위 예제에서는 contoso.com). 테넌트 개체 ID나 "myorganiztion" 또는 "me" 별칭일 수도 있습니다. 자세한 내용은 [Graph API의 엔터티 및 작업 주소 지정](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)을 참조하세요.
- **리소스 경로**: URL의 이 섹션에서는 조작할 리소스(사용자, 그룹, 특정 사용자 또는 특정 그룹 등)를 식별합니다. 위의 예제에서는 해당 리소스 집합의 주소를 지정하는 최상위 "그룹"입니다. 특정 엔터티 주소를 지정할 수도 있습니다(예: "users/{objectId}" 또는 "users/userPrincipalName").
- **쿼리 매개 변수**: ?는 리소스 경로 섹션과 쿼리 매개 변수 섹션을 구분합니다. Graph API의 모든 요청에는 "api-version" 쿼리 매개 변수가 필요합니다. 또한 Graph API는 다음과 같은 OData 쿼리 옵션을 지원합니다. **$filter**, **$orderby**, **$expand**, **$top** 및 **$format**. 다음 쿼리 옵션은 현재 지원되지 않습니다. **$count**, **$inlinecount** 및 **$skip**. 자세한 내용은 [Azure AD Graph API에서 지원되는 쿼리, 필터 및 페이징 옵션](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)을 참조하세요.

## Graph API 버전

"api-version" 쿼리 매개 변수에 Graph API 요청에 대한 버전을 지정합니다. 버전 1.5 이상의 경우 숫자 버전 값 api-version=1.6을 사용합니다. 이전 버전의 경우 YYYY-MM-DD 형식을 준수하는 날짜 문자열을 사용합니다(예: api-version=2013-11-08). 미리 보기 기능의 경우 문자열 "beta"를 사용합니다(예: api-version=beta). Graph API 버전 간의 차이점에 대한 자세한 내용은 [Azure AD Graph API 버전 관리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)를 참조하세요.

## Graph API 메타데이터

Graph API 메타데이터 파일을 반환하려면 URL에서 테넌트 식별자 뒤에 "$metadata" 세그먼트를 추가합니다. 예를 들어 다음 URL은 Graph Explorer에서 사용되는 데모 회사에 대한 메타데이터를 반환합니다. `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. 웹 브라우저의 주소 표시줄에 이 URL을 입력하면 메타데이터를 볼 수 있습니다. 반환된 CSDL 메타데이터 문서에서는 엔터티 및 복합 형식, 해당 속성 및 요청한 Graph API 버전에서 노출되는 함수 및 작업에 대해 설명합니다. api-version 매개 변수를 생략하면 가장 최신 버전에 대한 메타데이터가 반환됩니다.

## 일반 쿼리

[Azure AD Graph API 일반 쿼리](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries)는 디렉터리의 최상위 리소스에 액세스하는 데 사용할 수 있는 쿼리 및 디렉터리에서 작업을 수행하는 쿼리를 포함하여 Azure AD Graph에서 사용할 수 있는 일반 쿼리를 나열합니다.

예를 들어 `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6`는 contoso.com 디렉터리에 대한 회사 정보를 반환합니다.

또는 `https://graph.windows.net/contoso.com/users?api-version=1.6`는 contoso.com 디렉터리에 있는 모든 사용자 개체를 나열합니다.

## Graph Explorer 사용

Azure AD Graph API용 Graph Explorer를 사용하여 응용 프로그램을 빌드할 때 디렉터리 데이터를 쿼리할 수 있습니다.

> [AZURE.IMPORTANT] Graph Explorer는 디렉터리에서 데이터 삭제 또는 쓰기를 지원하지 않습니다. Graph Explorer에서는 Azure AD 디렉터리에 대한 읽기 작업만 수행할 수 있습니다.

다음은 Graph Explorer로 이동하고 Use Demo Company를 선택한 다음 `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`를 입력하여 데모 디렉터리에 있는 모든 사용자를 표시하는 경우 나타나는 출력입니다.

![Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Graph Explorer 로드**: 도구를 로드하려면 [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/)으로 이동합니다. **Use Demo Company**를 클릭하여 샘플 테넌트의 데이터에 대해 Graph Explorer를 실행합니다. 데모 회사는 자격 증명 없이 사용할 수 있습니다. 또는 **Sign in**을 클릭하고 Azure AD 계정 자격 증명으로 로그인하여 테넌트에 대해 Graph Explorer를 실행할 수 있습니다. 고유한 테넌트에 대해 Graph Explorer를 실행하는 경우 사용자 또는 관리자가 로그인 중에 동의해야 합니다. Office 365 구독이 있는 경우 Azure AD 테넌트를 자동으로 보유합니다. Office 365에 로그인하는 데 사용하는 자격 증명은 실제로 Azure AD 계정이며, Graph Explorer에서 이러한 자격 증명을 사용할 수 있습니다.

**쿼리 실행**: 쿼리를 실행하려면 요청 텍스트 상자에 쿼리를 입력하고 **GET**을 클릭하거나 **Enter** 키를 클릭합니다. 결과가 응답 상자에 표시됩니다. 예를 들어 `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6`는 데모 디렉터리에 있는 모든 그룹 개체를 나열합니다.

Graph Explorer의 다음 기능 및 제한 사항을 확인합니다.
- 리소스 집합에 대한 자동 완성 기능. 이 기능을 확인하려면 **Use Demo Company**를 클릭한 다음 회사 URL이 표시되는 요청 텍스트 상자를 클릭합니다. 드롭다운 목록에서 리소스 집합을 선택할 수 있습니다.

- "me" 및 "myorganization" 주소 지정 별칭을 지원합니다. 예를 들어 `https://graph.windows.net/me?api-version=1.6`를 사용하여 로그인한 사용자의 사용자 개체를 반환하거나 `https://graph.windows.net/myorganization/users?api-version=1.6`를 사용하여 현재 디렉터리에 있는 모든 사용자를 반환할 수 있습니다. "me" 별칭을 사용하면 요청을 수행하는 로그인한 사용자가 없기 때문에 데모 회사에 대한 오류가 반환됩니다.

- 응답 헤더 섹션입니다. 쿼리를 실행할 때 발생하는 문제를 해결하는 데 활용할 수 있습니다.

- 확장 및 축소 기능이 있는 응답에 대한 JSON 뷰어입니다.

- 축소판 사진 표시를 지원하지 않습니다.

## Fiddler를 사용하여 디렉터리에 쓰기

이 빠른 시작 가이드에서는 Azure AD 디렉터리에 '쓰기' 작업을 연습하기 위해 Fiddler Web Debugger를 사용할 수 있습니다. 자세한 내용을 보고 Fiddler를 설치하려면 [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler)를 참조하세요.

아래 예제에서는 Fiddler Web Debugger를 사용하여 Azure AD 디렉터리에 새 보안 그룹 'MyTestGroup'을 만듭니다.

**액세스 토큰 얻기**: Azure AD Graph에 액세스하려면 클라이언트가 먼저 Azure AD에 인증해야 합니다. 자세한 내용은 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.

**쿼리 작성 및 실행**: 다음 단계를 완료합니다.

1. Fiddler Web Debugger를 열고 **Composer** 탭으로 전환합니다.
2. 새 보안 그룹을 생성하려고 하므로 풀다운 메뉴에서 **Post**를 HTTP 메서드로 선택합니다. 그룹 개체의 작업 및 사용 권한에 대한 자세한 내용은 [Azure AD Graph REST API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 내에서 [그룹](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity)을 참조하세요.
3. **Post** 옆에 있는 필드에 다음을 요청 URL로 입력합니다. `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] mytenantdomain을 고유한 Azure AD 디렉터리의 도메인 이름으로 대체해야 합니다.

4. Post 풀다운 바로 아래에 있는 필드에 다음을 입력합니다.

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] &lt;액세스 토큰gt;을 Azure AD 디렉터리에 대한 액세스 토큰으로 대체합니다.

5. **요청 본문** 필드에 다음을 입력합니다.

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    그룹을 만드는 방법에 대한 자세한 내용은 [그룹 만들기](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)를 참조하세요.

Graph에 의해 노출되는 Azure AD 엔터티 및 형식에 대한 자세한 내용 및 Graph를 사용하여 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD Graph REST API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)를 참조하세요.

## 다음 단계

- [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)에 대해 자세히 알아보세요.
- [Azure AD Graph API 사용 권한 범위](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->