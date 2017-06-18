<properties
   pageTitle="Azure CLI에서 서비스 주체 만들기 | Microsoft Azure"
   description="Azure CLI를 사용하여 Active Directory 응용 프로그램 및 서비스 주체를 만들고 역할 기반 액세스 제어를 통해 리소스에 대한 액세스를 부여하는 방법을 설명합니다. 암호 또는 인증서를 사용하여 응용 프로그램을 인증하는 방법을 보여 줍니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# Azure CLI를 사용하여 리소스에 액세스하는 서비스 주체 만들기

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure CLI](resource-group-authenticate-service-principal-cli.md)
- [포털](resource-group-create-service-principal-portal.md)


리소스에 액세스해야 하는 응용 프로그램이나 스크립트가 있는 경우 이 프로세스를 자체 자격 증명에 따라 실행하지 않으려고 할 수 있습니다. 응용 프로그램에 대해 원하는 다양한 권한을 보유할 수 있으며 책임이 변경된 경우 응용 프로그램에서 자격 증명을 더 이상 사용하지 않고 싶을 수 있습니다. 대신 인증 자격 증명 및 역할 할당을 포함하는 응용 프로그램에 대한 ID를 만듭니다. 앱을 실행할 때마다 이러한 자격 증명을 사용하여 자체적으로 인증합니다. 이 항목에서는 [Mac, Linux 및 Windows용 Azure CLI](xplat-cli-install.md)를 사용하여 응용 프로그램을 자체 자격 증명 및 ID로 실행하도록 설정하는 방법을 보여 줍니다.

Azure CLI를 사용하는 경우 AD 응용 프로그램을 인증하기 위한 다음 2가지 옵션이 있습니다.

 - password
 - 인증서

이 항목에서는 Azure CLI에서 두 가지 옵션을 사용하는 방법을 보여 줍니다. 프로그래밍 프레임워크(예: Python, Ruby 또는 Node.js)에서 Azure에 로그인하려는 경우 암호 인증이 가장 좋은 방법일 수 있습니다. 암호 또는 인증서 중 어떤 방법을 사용할지 결정하기 전에 [샘플 응용 프로그램](#sample-applications) 섹션에서 여러 다른 프레임워크에서 인증하는 방법의 예를 참조하세요.

## Active Directory 개념

이 문서에서는 AD(Active Directory) 응용 프로그램 및 서비스 주체라는 두 개체를 만듭니다. AD 응용 프로그램은 응용 프로그램에 대한 글로벌 표현입니다. 여기에는 자격 증명(응용 프로그램 ID 및 암호 또는 인증서)이 포함되어 있습니다. 서비스 주체는 Active Directory의 응용 프로그램에 대한 로컬 표현입니다. 여기에는 역할 할당이 포함됩니다. 이 토픽에서는 응용 프로그램을 하나의 조직 내에서만 실행하게 되는 단일 테넌트 응용 프로그램을 중점적으로 다룹니다. 일반적으로 단일 조직 내에서 실행되는 LOB(기간 업무) 응용 프로그램에 대해 단일 테넌트 응용 프로그램을 사용하게 됩니다. 단일 테넌트 응용 프로그램에는 하나의 AD 앱과 하나의 서비스 주체가 있습니다.

두 개체가 모두 필요한 이유는 무엇일까요? 이 접근 방식은 다중 테넌트 응용 프로그램을 고려할 때 유용하기 때문입니다. 일반적으로 응용 프로그램이 여러 구독에서 실행되는 SaaS(Software-as-a-Service) 응용 프로그램에 대해 다중 테넌트 응용 프로그램을 사용합니다. 다중 테넌트 응용 프로그램의 경우 하나의 AD 앱과 여러 서비스 주체(앱에 액세스하는 Active Directory에 대해 하나씩)가 있습니다. 다중 테넌트 응용 프로그램을 설정하려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

## 필요한 사용 권한

이 항목을 완료하려면 Azure Active Directory와 Azure 구독에 대한 충분한 권한이 있어야 합니다. 특히, Active Directory에서 앱을 만들고 역할에 서비스 주체를 할당할 수 있어야 합니다.

Active Directory에서 관리자 계정이어야 합니다(예: **전역 관리자** 또는 **사용자 관리자**). 계정이 **사용자** 역할에 할당된 경우 관리자에게 사용 권한의 상승을 요청해야 합니다.

구독에서 해당 계정에는 [소유자](./active-directory/role-based-access-built-in-roles.md#owner) 역할 또는 [사용자 액세스 관리자](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) 역할을 통해 부여된 `Microsoft.Authorization/*/Write` 액세스 권한이 있어야 합니다. 계정이 **참가자** 역할에 할당된 경우 서비스 주체를 역할에 할당하려고 하면 오류가 발생합니다. 다시 말하지만, 구독 관리자가 충분한 액세스 권한을 부여해야 합니다.

이제 [암호](#create-service-principal-with-password) 또는 [인증서](#create-service-principal-with-certificate) 인증에 대한 섹션을 계속 진행합니다.

## 암호를 사용하여 서비스 주체 만들기

이 섹션에서 수행하는 단계는 다음과 같습니다.

- 암호를 사용하는 AD 응용 프로그램과 서비스 주체 만들기
- 읽기 권한자 역할을 서비스 주체에 할당

이러한 단계를 신속하게 수행하려면 다음 명령을 사용합니다.

    azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>
    azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

프로세스를 이해하기 위해 이러한 단계를 더 신중하게 살펴보겠습니다.

1. 계정에 로그인합니다.

        azure config mode arm
        azure login

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 링크, 응용 프로그램을 식별하는 URI, 응용 프로그램 ID에 대한 암호를 제공합니다. 이 명령은 AD 응용 프로그램 및 서비스 주체를 모두 만듭니다.

        azure ad sp create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p {your-password}
        
     단일 테넌트 응용 프로그램에서는 URI의 유효성이 검사되지 않습니다.
     
     Active Directory에 대한 [필수 권한](#required-permissions)이 계정에 없는 경우 "Authentication\_Unauthorized" 또는 "No subscription found in the context"(컨텍스트에서 구독을 찾을 수 없습니다.)라는 오류 메시지가 나타납니다.
    
     새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다. 서비스 주체 이름은 로그인할 때 필요합니다.
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 **읽기 권한자** 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md)을 참조하세요. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ObjectId**를 제공합니다.

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     계정에 역할을 할당할 권한이 없는 경우 오류 메시지가 나타납니다. 이 메시지는 계정에 **'/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없다**는 내용입니다.

이것으로 끝입니다. AD 응용 프로그램 및 서비스 주체가 설정되었습니다. 다음 섹션에서는 Azure CLI를 통해 자격 증명을 사용하여 로그인하는 방법을 보여 줍니다. 코드 응용 프로그램에서 자격 증명을 사용하려는 경우 이 항목을 진행할 필요가 없습니다. [샘플 응용 프로그램](#sample-applications)으로 이동하여 응용 프로그램 ID 및 암호를 사용하여 로그인하는 예제를 참조할 수 있습니다.

### Azure CLI를 통해 자격 증명 제공

이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. 서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하려면 다음을 사용합니다.

        azure account show

     반환하는 내용은 다음과 같습니다.

        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     다른 구독의 테넌트 ID를 가져와야 하는 경우 다음 명령을 사용하세요.

        azure account show -s {subscription-id}

3. 서비스 주체로 로그인합니다.

        azure login -u https://www.contoso.org/example --service-principal --tenant {tenant-id}

    암호를 입력하라는 메시지가 나타납니다. AD 응용 프로그램을 만들 때 지정한 암호를 제공합니다.

        info:    Executing command login
        Password: ********

이제 사용자는 작성한 서비스 주체에 대한 서비스 주체로 인증됩니다.

## 인증서를 사용하여 서비스 주체 만들기

이 섹션에서 수행하는 단계는 다음과 같습니다.

- 자체 서명된 인증서 만들기
- 인증서를 사용하는 AD 응용 프로그램과 서비스 주체 만들기
- 읽기 권한자 역할을 서비스 주체에 할당

다음 단계를 완료하려면 [OpenSSL](http://www.openssl.org/)을 설치해야 합니다.

1. 자체 서명된 인증서를 만듭니다.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. 공개 및 개인 키를 결합합니다.

        cat privkey.pem cert.pem > examplecert.pem

3. **examplecert.pem** 파일을 열고 **---BEGIN CERTIFICATE---**와 **---END CERTIFICATE---** 사이의 긴 시퀀스 문자를 찾습니다. 인증서 데이터를 복사합니다. 서비스 주체를 만들 때 이 데이터를 매개 변수로 전달합니다.

1. 계정에 로그인합니다.

        azure config mode arm
        azure login

1. 응용 프로그램에 대한 서비스 주체를 만듭니다. 표시 이름, 응용 프로그램을 설명하는 페이지에 대한 URI, 응용 프로그램을 식별하는 URI 및 복사한 인증서 데이터를 제공합니다. 이 명령은 AD 응용 프로그램 및 서비스 주체를 모두 만듭니다.

        azure ad sp create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>
        
     단일 테넌트 응용 프로그램에서는 URI의 유효성이 검사되지 않습니다.
     
     Active Directory에 대한 [필수 권한](#required-permissions)이 계정에 없는 경우 "Authentication\_Unauthorized" 또는 "No subscription found in the context"(컨텍스트에서 구독을 찾을 수 없습니다.)라는 오류 메시지가 나타납니다.
    
     새 서비스 주체가 반환됩니다. 사용 권한을 부여할 때 개체 ID가 필요합니다.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. 서비스 사용자에게 구독에 대한 권한을 부여합니다. 이 예제에서는 구독에서 모든 리소스를 읽을 수 있는 권한이 부여된 **읽기 권한자** 역할에 서비스 주체를 추가합니다. 다른 역할에 대해서는 [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md)을 참조하세요. **ServicePrincipalName** 매개 변수의 경우 응용 프로그램을 만들 때 사용한 **ObjectId**를 제공합니다.

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     계정에 역할을 할당할 권한이 없는 경우 오류 메시지가 나타납니다. 이 메시지는 계정에 **'/subscriptions/{guid}' 범위에 대해 'Microsoft.Authorization/roleAssignments/write' 작업을 수행할 권한이 없다**는 내용입니다.

### 자동화된 Azure CLI 스크립트를 통해 인증서 제공

이제 응용 프로그램으로 로그인하여 작업을 수행해야 합니다.

1. 서비스 주체로 로그인할 때마다 AD 앱에 디렉터리의 테넌트 ID를 제공해야 합니다. 테넌트는 Active Directory의 인스턴스입니다. 현재 인증된 구독에 대한 테넌트 ID를 검색하려면 다음을 사용합니다.

        azure account show

     반환하는 내용은 다음과 같습니다.

        info:    Executing command account show
        data:    Name                        : Microsoft Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     다른 구독의 테넌트 ID를 가져와야 하는 경우 다음 명령을 사용하세요.

        azure account show -s {subscription-id}

1. 인증서 지문을 검색하고 불필요한 문자를 제거하려면 다음을 사용합니다.

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     다음과 유사한 지문 값을 반환합니다.

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

1. 서비스 주체로 로그인합니다.

        azure login --service-principal --tenant {tenant-id} -u https://www.contoso.org/example --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

이제 사용자는 작성한 Active Directory 응용 프로그램에 대한 서비스 주체로 인증됩니다.

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
  
- 리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.
- 인증서 및 Azure CLI 사용에 대한 자세한 내용은 [Linux 명령줄에서 Azure 서비스 보안 주체로 인증서 기반 인증](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->