<properties
   pageTitle="포털에서 서비스 보안 주체 만들기 | Microsoft Azure"
   description="Azure 리소스 관리자에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Active Directory 응용 프로그램 및 서비스 주체를 만드는 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# 포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 및 서비스 주체 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)


리소스를 액세스하거나 수정해야 하는 응용 프로그램이 있는 경우 AD(Active Directory) 응용 프로그램을 설정하고 필수 사용 권한을 할당해야 합니다. 이 토픽에서는 포털을 통해 이러한 단계를 수행하는 방법을 보여 줍니다. 현재 클래식 포털을 사용하여 새 Active Directory 응용 프로그램을 만든 후 Azure 포털로 전환하여 응용 프로그램에 역할을 할당해야 합니다.

> [AZURE.NOTE] 인증서를 사용하여 인증하려는 경우에 특히 [PowerShell](resource-group-authenticate-service-principal.md) 또는 [Azure CLI](resource-group-authenticate-service-principal-cli.md)를 통해 AD 응용 프로그램 및 서비스 주체를 더욱 쉽게 설정할 수 있습니다. 이 토픽에는 인증서 사용 방법은 나오지 않습니다.

Active Directory 개념에 대한 설명을 보려면 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

## Active Directory 응용 프로그램 만들기

1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.

2. 구독의 기본 Active Directory를 알고 있는지 확인합니다. 구독과 동일한 디렉터리에 있는 응용 프로그램에 대해서만 액세스 권한을 부여할 수 있습니다. **설정**을 선택하고 구독과 연결된 디렉터리 이름을 찾습니다. 자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](./active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.
   
     ![기본 디렉터리 찾기](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. 왼쪽 창에서 **Active Directory**를 선택합니다.

     ![Active Directory 선택](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 응용 프로그램을 만드는 데 사용할 Active Directory를 선택합니다. Active Directory가 두 개 이상인 경우 구독의 기본 디렉터리에 응용 프로그램을 만듭니다.

     ![디렉터리 선택](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. 디렉터리의 응용 프로그램을 보려면 **응용 프로그램**을 선택합니다.

     ![응용 프로그램 보기](./media/resource-group-create-service-principal-portal/view-applications.png)

4. 해당 디렉터리에 응용 프로그램을 만든 적이 없는 경우 다음과 비슷한 이미지가 표시됩니다. **응용 프로그램 추가**를 선택합니다.

     ![응용 프로그램 추가](./media/resource-group-create-service-principal-portal/create-application.png)

     또는 아래쪽 창에서 **추가**를 클릭합니다.

     ![추가](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다.

     ![새 응용 프로그램](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. 응용 프로그램의 이름을 입력하고 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **웹 응용 프로그램 및/또는 웹 API**를 만들기로 선택하고 다음 단추를 클릭합니다. **네이티브 클라이언트 응용 프로그램**을 선택하는 경우 이 문서의 나머지 단계가 작업 환경과 일치하지 않게 됩니다.

     ![응용 프로그램 이름 지정](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. 앱에 대한 속성을 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. **앱 ID URI**의 경우 응용 프로그램을 식별하는 URI를 제공합니다.

     ![응용 프로그램 속성](./media/resource-group-create-service-principal-portal/app-properties.png)

응용 프로그램이 만들어졌습니다.

## 클라이언트 ID 및 인증 키 가져오기

프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID가 필요합니다. 응용 프로그램이 자체 자격 증명에서 실행되는 경우 인증 키도 필요합니다.

1. **구성** 탭을 선택하여 응용 프로그램의 암호를 구성합니다.

     ![응용 프로그램 구성](./media/resource-group-create-service-principal-portal/application-configure.png)

2. **클라이언트 ID**를 복사합니다.
  
     ![클라이언트 ID](./media/resource-group-create-service-principal-portal/client-id.png)

3. 응용 프로그램이 자체 자격 증명에서 실행되는 경우 **키** 섹션까지 아래로 스크롤하여 암호가 유효한 기간을 선택합니다.

     ![키](./media/resource-group-create-service-principal-portal/create-key.png)

4. **저장**을 선택하여 키를 만듭니다.

     ![저장](./media/resource-group-create-service-principal-portal/save-icon.png)

     저장된 키가 표시되고 키를 복사할 수 있습니다. 나중에 키를 검색할 수 없으므로 지금 키를 복사합니다.

     ![공유 키](./media/resource-group-create-service-principal-portal/save-key.png)

## 테넌트 ID 가져오기

프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 웹앱 및 Web API 앱의 경우 아래 이미지와 같이 화면 아래쪽에서 **끝점 보기**를 선택하고 ID를 검색하여 테넌트 ID를 검색할 수 있습니다.

   ![테넌트 ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

PowerShell을 통해 테넌트 ID를 검색할 수도 있습니다.

    Get-AzureRmSubscription

또는 Azure CLI:

    azure account show --json

## 위임된 권한 설정

응용 프로그램이 로그인한 사용자를 대신하여 리소스에 액세스하는 경우에는 응용 프로그램이 다른 응용 프로그램에 액세스하도록 위임된 권한을 부여해야 합니다. **구성** 탭의 **다른 응용 프로그램에 대한 권한** 섹션에서 이 액세스 권한을 부여합니다. 기본적으로 위임된 권한은 Azure Active Directory에서 사용하도록 이미 설정되어 있습니다. 위임된 권한을 변경하지 않고 그대로 둡니다.

1. **응용 프로그램 추가**를 선택합니다.

2. 목록에서 **Microsoft Azure Service Management API**를 선택합니다. 그런 후 완료 아이콘을 선택합니다.

      ![앱 선택](./media/resource-group-create-service-principal-portal/select-app.png)

3. 위임된 권한의 드롭다운 목록에서 **조직으로 Azure 서비스 관리에 액세스**를 선택합니다.

      ![사용 권한 선택](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 변경 내용을 저장합니다.

## 응용 프로그램을 역할에 할당

응용 프로그램이 자체 자격 증명으로 실행되는 경우 응용 프로그램을 역할에 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md)을 참조하세요.

응용 프로그램에 역할을 할당하려면 올바른 사용 권한이 있어야 합니다. 특히, [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다. 참가자 역할은 올바른 액세스 권한이 없습니다.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 응용 프로그램에 역할을 할당하려면 클래식 포털에서 [Azure 포털](https://portal.azure.com)로 전환하세요.

1. 역할에 서비스 주체를 할당하려면 사용 권한을 확인하세요. 계정의 **내 사용 권한**을 선택합니다.

    ![내 사용 권한 선택](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. 계정에 할당된 사용 권한을 확인합니다. 앞에서 설명했듯이, 소유자 또는 사용자 액세스 관리자 역할에 속해 있거나 Microsoft.Authorization에 대한 쓰기 액세스 권한을 부여하는 사용자 지정 역할을 갖고 있어야 합니다. 다음 이미지는 구독의 참여자 역할에 할당된 계정으로, 응용 프로그램을 역할에 할당하기에 적합한 사용 권한이 아닙니다.

    ![내 사용 권한 표시](./media/resource-group-create-service-principal-portal/show-permissions.png)

     응용 프로그램에 대한 액세스 권한을 부여하는 데 필요한 사용 권한이 없는 경우 사용자 액세스 관리자 역할에 추가해 달라고 구독 관리자에게 요청하거나 응용 프로그램에 대한 액세스 권한을 부여해 달라고 관리자에게 요청해야 합니다.

1. 응용 프로그램을 할당하려는 범위 수준으로 이동합니다. 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

     ![구독 선택](./media/resource-group-create-service-principal-portal/select-subscription.png)

     응용 프로그램을 할당할 특정 구독을 선택합니다.

     ![할당을 위한 구독 선택](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     오른쪽 위 모퉁이에서 **액세스** 아이콘을 선택합니다.

     ![액세스 선택](./media/resource-group-create-service-principal-portal/select-access.png)
     
     또는 리소스 그룹 범위에서 역할을 할당하려면 리소스 그룹으로 이동합니다. 리소스 그룹 블레이드에서 **액세스 제어**를 선택합니다.

     ![사용자 선택](./media/resource-group-create-service-principal-portal/select-users.png)

     다음 단계는 모든 범위에서 동일합니다.

2. **추가**를 선택합니다.

     ![추가 선택](./media/resource-group-create-service-principal-portal/select-add.png)

3. **읽기 권한자** 역할(또는 응용 프로그램에 할당하고 싶은 역할)을 선택합니다.

     ![역할 선택](./media/resource-group-create-service-principal-portal/select-role.png)

4. 역할에 추가할 수 있는 사용자 목록이 처음 표시될 때에는 응용 프로그램이 표시되지 않고 그룹 및 사용자만 보입니다.

     ![사용자 표시](./media/resource-group-create-service-principal-portal/show-users.png)

5. 응용 프로그램을 찾으려면 응용 프로그램을 검색해야 합니다. 응용 프로그램 이름을 입력하면 사용 가능한 옵션 목록이 변경됩니다. 목록에 응용 프로그램이 표시되면 해당 응용 프로그램을 선택합니다.

     ![역할에 할당](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. **확인**을 선택하여 역할 할당을 완료합니다. 이제 목록에서 리소스 그룹에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.


포털을 통해 역할에 사용자 및 응용 프로그램을 할당하는 방법에 대한 자세한 내용은 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal)를 참조하세요.

## 샘플 응용 프로그램

다음 예제 응용 프로그램에서는 서비스 주체로 로그인하는 방법을 보여 줍니다.

**.NET**

- [.NET에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [.NET을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [리소스 사용 시작 - Azure Resource Manager 템플릿을 사용하여 배포 - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [리소스 사용 시작 - 리소스 그룹 관리 - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Python에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Python을 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.JS**

- [Node.js에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Node.js를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## 다음 단계

- 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.
- 이러한 단계에 대한 비디오 데모를 보려면 [Azure Active Directory에서 Azure 리소스의 프로그래밍 방식 관리 활성화](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->