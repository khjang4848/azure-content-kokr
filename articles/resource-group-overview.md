<properties
   pageTitle="Azure 리소스 관리자 개요 | Microsoft Azure"
   description="Azure에서 리소스 배포, 관리 및 액세스 제어용 Azure 리소스 관리자 사용 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="tomfitz"/>

# Azure 리소스 관리자 개요

응용 프로그램에 대한 인프라는 일반적으로 가상 컴퓨터, 저장소 계정 및 가상 네트워크 또는 웹앱, 데이터베이스, 데이터베이스 서버 및 타사 서비스 등의 많은 구성 요소를 만듭니다. 이러한 구성 요소를 별도 엔터티로 표시하지 않으면, 대신 관련된 단일 엔터티의 상호 종속적으로 부분으로 표시됩니다. 그룹으로 배포, 관리 및 모니터링하려고 합니다. Azure 리소스 관리자를 사용하면 솔루션에서 리소스를 그룹으로 사용할 수 있습니다. 조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 리소스 관리자는 보안, 감사 및 태그 기능을 제공하여 배포 후에 리소스를 관리할 수 있습니다.

## 용어

Azure Resource Manager가 처음이라면 익숙하지 않은 용어가 있을 수 있습니다.

- **리소스** - Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 몇 가지 일반적인 리소스는 가상 컴퓨터, 저장소 계정, 웹앱, 데이터베이스 및 가상 네트워크이지만 더 많은 종류가 있습니다.
- **리소스 그룹** - Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. [리소스 그룹](#resource-groups)을 참조하세요.
- **리소스 공급자** - Resource Manager를 통해 배포하고 관리할 수 있는 리소스를 제공하는 서비스입니다. 각 리소스 공급자는 배포된 리소스로 작업하기 위한 작업을 제공합니다. 몇 가지 일반 리소스 공급자는 가상 컴퓨터 리소스를 제공하는 Microsoft.Compute, 저장소 계정 리소스를 제공하는 Microsoft.Storage 및 웹앱에 관련된 리소스를 제공하는 Microsoft.Web입니다. [리소스 공급자](#resource-providers)를 참조하세요.
- **Resource Manager 템플릿** - 리소스 그룹에 배포한 하나 이상의 리소스를 정의하는 JSON(JavaScript Object Notation) 파일입니다. 또한 배포된 리소스 간의 종속성을 정의합니다. 템플릿은 리소스를 일관되고 반복적으로 배포하는 데 사용할 수 있습니다. [템플릿 배포](#template-deployment)를 참조하세요.
- **선언적 구문** - 항목을 만드는 프로그래밍 명령의 시퀀스를 작성하지 않고도 "만들려는 대상은 다음과 같습니다"라고 선언하는 구문입니다. Resource Manager 템플릿은 선언적 구문의 예입니다. 파일에서 Azure에 배포하는 인프라에 대한 속성을 정의합니다.

## 리소스 관리자를 사용할 경우의 이점

리소스 관리자는 다음과 같은 여러 이점이 있습니다.

- 이 리소스를 개별적으로 처리하는 것이 아니라 솔루션에 대한 모든 리소스를 그룹으로 배포, 관리 및 모니터링할 수 있습니다.
- 개발 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.
- 스크립트가 아닌 선언적 템플릿을 통해 인프라를 관리할 수 있습니다.
- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.
- 역할 기반 액세스 제어(RBAC)가 관리 플랫폼으로 통합되기 때문에 리소스 그룹의 모든 서비스에 대해 액세스 제어를 적용할 수 있습니다.
- 리소스에 태그를 적용하여 구독에서 모든 리소스를 논리적으로 구성할 수 있습니다.
- 같은 태그를 공유하는 리소스 그룹에 대한 비용을 확인하여 조직의 청구를 명확히 할 수 있습니다.

리소스 관리자는 솔루션을 배포 및 관리하는 새로운 방식을 제공합니다. 이전의 배포 모델을 사용한 경우 변경 사항을 알아보려면 [리소스 관리자 배포 및 클래식 배포 이해](resource-manager-deployment-model.md)를 참조하세요.

## 지침

다음 제안으로 솔루션으로 작업할 때 Resource Manager를 완벽하게 활용할 수 있습니다.

1. 명령적 명령을 사용하는 대신 리소스 관리자 템플릿의 선언적 구문을 통해 인프라를 정의하고 배포합니다.
2. 템플릿에서 모든 배포 및 구성 단계를 정의합니다. 솔루션을 설정하기 위해 수동 단계가 없어야 합니다.
3. 명령적 명령을 실행하여 앱 또는 컴퓨터를 시작하거나 중지하는 등 리소스를 관리합니다.
4. 리소스 그룹에서 동일한 수명 주기로 리소스를 정렬합니다. 리소스의 모든 다른 구성에 태그를 사용합니다.

자세한 권장 사항은 [Azure Resource Manager 템플릿 생성 모범 사례](resource-manager-template-best-practices.md)를 참조하세요.

## 리소스 그룹

리소스 그룹을 정의할 때 고려해야 할 몇 가지 중요한 요인이 있습니다.

1. 그룹에서 모든 리소스는 동일한 수명 주기를 공유해야 합니다. 리소스를 함께 배포, 업데이트, 삭제합니다. 데이터베이스 서버와 같은 하나의 리소스에 다양한 배포 주기가 존재하는 경우 다른 리소스 그룹에 있어야 합니다.
2. 각 리소스는 하나의 리소스 그룹에만 있을 수 있습니다.
3. 언제든지 리소스 그룹에 리소스를 추가하거나 제거할 수 있습니다.
4. 특정 리소스 그룹에서 다른 그룹에 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.
4. 리소스 그룹을 다른 지역에 상주하는 리소스를 포함할 수 있습니다.
5. 관리 작업에 대한 액세스 제어 범위를 지정하는 데 리소스 그룹을 사용할 수 있습니다.
6. 리소스는 다른 리소스 그룹의 리소스와 상호 작용할 수 있습니다. 이 상호 작용은 두 개의 리소스가 관련되어 있지만 동일한 수명 주기를 공유하지 않는 경우에 일반적입니다(예: 데이터베이스에 연결된 웹앱).

리소스 그룹을 만들 때 해당 리소스 그룹의 위치를 제공해야 합니다. 리소스 그룹에 위치가 필요한 이유는 무엇인지 궁금할 수 있습니다. 리소스의 위치가 리소스 그룹과 다른 경우 리소스 그룹 위치가 중요한 이유는 무엇인가요? 리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

## 리소스 공급자

각 리소스 공급자는 기술 영역으로 작업하는 일련의 리소스 및 작업을 제공합니다. 예를 들어 키와 암호를 저장하려는 경우 **Microsoft.KeyVault** 리소스 공급자로 작업합니다. 이 리소스 공급자는 주요 자격 증명 모음을 만드는 데 **자격 증명 모음**이라는 리소스 형식을 제공하고 주요 자격 증명 모음에 암호를 만드는 데 **자격 증명 모음/암호**라는 리소스 형식을 제공합니다. 또한 [주요 자격 증명 모음 REST API 작업](https://msdn.microsoft.com/library/azure/dn903609.aspx)을 통한 작업을 제공합니다. REST API를 직접 호출할 수 있거나 [주요 자격 증명 모음 PowerShell cmdlet](https://msdn.microsoft.com/library/dn868052.aspx) 및 [주요 자격 증명 모음 Azure CLI](./key-vault/key-vault-manage-with-cli.md)를 제공하여 주요 자격 증명 모음을 관리할 수 있습니다. 또한 대부분의 리소스에 맞게 다양한 프로그래밍 언어를 사용할 수 있습니다. 자세한 내용은 [SDK 및 샘플](#sdks-and-samples)을 참조하세요.

인프라를 배포하고 관리하려면 리소스 공급자에 대한 세부 정보를 알아야 합니다. 리소스를 만드는 데 사용할 해당 리소스 유형, REST API 작업의 버전 번호, 지원되는 작업 및 스키마를 알아야 합니다. 지원되는 리소스 공급자에 대해 알아보려면 [Resource Manager 공급자, 영역, API 버전 및 스키마](resource-manager-supported-services.md)를 참조하세요.

## 템플릿 배포

Resource Manager로 응용 프로그램의 배포 및 구성을 정의하는 템플릿을(JSON 형식으로) 만들 수 있습니다. 서식 파일을 사용하여 수명 주기 내내 응용 프로그램을 반복적으로 배포하며 안심하고 일관된 상태로 리소스가 배포할 수 있습니다. 리소스가 올바른 순서로 생성되도록 Azure Resource Manager가 종속성을 분석합니다. 자세한 정보는 [Azure 리소스 관리자 템플릿에서 종속성 정의](resource-group-define-dependencies.md)를 참조하세요.

포털에서 솔루션을 만들 때 자동으로 솔루션에 배포 템플릿을 포함합니다. 솔루션용 템플릿으로 시작하고 특정 요구 사항에 맞게 사용자 지정할 수 있기 때문에 서식 파일을 처음부터 새로 만들 필요가 없습니다. 리소스 그룹의 현재 상태를 내보내거나 특정 배포에 사용된 템플릿을 검토하여 기존 리소스 그룹에 대한 템플릿을 검색할 수 있습니다. 내보낸 템플릿을 살펴보면 템플릿 구문에 대해 알아보는 데 도움이 됩니다. 내보낸 템플릿으로 작업하는 방법에 대한 자세한 내용은 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.

단일 템플릿에서 전체 인프라를 정의할 필요가 없습니다. 대부분 배포 요구 사항을 대상, 목적에 특정 템플릿 집합으로 나누는 것이 좋습니다. 서로 다른 솔루션에 이러한 템플릿을 쉽게 다시 사용할 수 있습니다. 특정 솔루션을 배포하려면 모든 필수 템플릿에 연결하는 마스터 템플릿을 만듭니다. 자세한 내용은 [Azure 리소스 관리자에서 연결된 템플릿 사용](resource-group-linked-templates.md)을 참조하세요.

또한 인프라의 업데이트에 대한 템플릿을 사용할 수 있습니다. 예를 들어 솔루션에 리소스를 추가할 수 있으며 이미 배포된 리소스에 대한 구성 규칙을 추가할 수 있습니다. 템플릿에서 리소스 만들기를 지정하지만 해당 리소스가 이미 존재하는 경우 Azure Resource Manager는 새 자산을 만드는 대신 업데이트를 수행합니다. Azure 리소스 관리자는 새 것과 동일한 상태로 기존 자산을 업데이트합니다. 또는 리소스 관리자가 템플릿에 지정되지 않은 모든 리소스를 삭제하도록 지정할 수 있습니다. 여러 배포 옵션의 차이점을 이해하려면 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

사용자 지정 및 배포 유연성을 허용하도록 템플릿에서 매개 변수를 지정할 수 있습니다. 예를 들어 테스트 환경에 배포를 조정하는 매개 변수 값을 전달할 수 있습니다. 매개 변수를 지정하여 여러 환경에 솔루션을 배포하는 데 동일한 템플릿을 사용할 수 있습니다.

리소스 관리자는 설치에 포함되지 않은 특정 소프트웨어를 설치하는 등의 추가 작업을 할 때 시나리오에 대한 확장을 제공합니다. DSC, Chef 또는 Puppet와 같은 구성 관리 서비스를 이미 사용 중인 경우 확장을 사용하여 해당 서비스로 작업을 계속할 수 있습니다.

마지막으로 템플릿은 앱에 대한 소스 코드의 일부가 됩니다. 소스 코드 리포지토리를 확인하고 앱이 발전하면 업데이트할 수 있습니다. Visual Studio를 통해 템플릿을 편집할 수 있습니다.

템플릿을 정의하는 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](resource-group-authoring-templates.md)를 참조하십시오.

템플릿 작성에 대한 단계별 지침은 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요.

다른 환경에 솔루션 배포에 관한 지침은 [Microsoft Azure의 개발 및 테스트 환경](solution-dev-test-environments.md)을 참조하세요.

## 태그

리소스 관리자는 관리 또는 청구에 대한 요구 사항에 따라 리소스를 분류할 수 있는 태그 지정 기능을 제공합니다. 리소스 그룹 및 리소스의 복잡한 컬렉션이 있고 해당 자산을 사용자에게 가장 적합한 방식으로 시각화할 필요가 있을 때 태그를 사용합니다. 예를 들어 조직에서 비슷한 역할을 제공하거나 동일한 부서에 속한 리소스를 태그로 지정할 수 있습니다. 조직의 사용자는 태그 없이 나중에 식별하고 관리하기 어려울 수 있는 여러 리소스를 만들 수 있습니다. 예를 들어 특정 프로젝트에 대한 모든 리소스를 삭제하려고 할 수 있습니다. 해당 리소스가 프로젝트에 대해 태그가 지정되지 않은 경우 수동으로 찾아야 합니다. 태그를 지정하는 작업은 구독에서 불필요한 비용을 줄일 수 있는 좋은 방법입니다.

리소스는 태그를 공유하는 동일한 리소스 그룹에 있을 필요가 없습니다. 조직의 모든 사용자가 실수로 약간 다른 태그 (예: "dept" 대신 "department")를 적용하지 않고 일반 태그를 사용하는지 확인하려면 사용자 고유의 태그 분류를 만들 수 있습니다.

태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하십시오. 배포하는 동안 리소스에 태그를 추가해야 하는 [사용자 지정된 정책](#manage-resources-with-customized-policies)을 만들 수 있습니다.

## 액세스 제어

리소스 관리자를 사용하면 조직에 대한 특정 작업에 액세스하는 사람을 제어할 수 있습니다. 고유하게 관리 플랫폼으로 OAuth 및 역할 기반 액세스 제어(RBAC)를 통합하고 해당 액세스 제어를 리소스 그룹에서 모든 서비스에 적용합니다. 사용자가 미리 정의된 플랫폼 및 리소스 특정 역할을 추가하고 구독, 리소스 그룹 또는 리소스에 해당 역할을 적용하여 액세스를 제한할 수 있습니다. 예를 들어 데이터베이스 서버 또는 보안 정책이 아닌 데이터베이스를 관리하는 사용자를 허용하는 SQL DB 참여자를 호출하는 미리 정의된 역할의 기능을 활용할 수 있습니다. SQL DB 참가자 역할에 이 유형의 액세스를 필요로 하는 조직에서 사용자를 추가하고 구독, 리소스 그룹 또는 리소스에 역할을 적용합니다.

리소스 관리자는 자동으로 감사에 대한 사용자 작업을 기록합니다. 감사 로그를 사용하는 데 대한 내용은 [Resource Manager를 사용하는 감사 작업](resource-group-audit.md)을 참조하세요.

역할 기반 액세스 제어에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요. [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md) 항목은 기본 제공 역할 및 허용된 작업의 목록을 포함합니다. 기본 제공 역할은 소유자, 판독기 및 참여자와 같은 일반 역할 및 가상 컴퓨터 참여자, 가상 네트워크 참여자 및 SQL 보안 관리자와 같은 서비스 관련 역할을 포함합니다.(사용 가능한 역할의 일부에 이름을 지정함)

또한 사용자가 삭제 및 수정하는 것을 방지하기 위해 명시적으로 중요한 리소스를 잠글 수 있습니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

모범 사례는 [Azure 리소스 관리자에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.

## 사용자 지정된 정책으로 리소스 관리

리소스 관리자를 사용하면 리소스를 관리하기 위해 사용자 지정된 정책을 만들 수 있습니다. 만든 정책의 유형에는 다양한 시나리오가 포함될 수 있습니다. 리소스에 대한 명명 규칙을 적용하거나 배포할 수 있는 리소스의 형식 및 인스턴스를 제한하거나 리소스 종류를 호스트할 수 있는 지역을 제한할 수 있습니다. 부서별로 청구를 구성하기 위해 리소스에 대한 태그 값이 필요할 수 있습니다. 구독에서 비용을 절감하고 일관성을 유지하는 데 도움이 되는 정책을 만들 수 있습니다. 자세한 내용은 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](resource-manager-policy.md)를 참조하세요.

## 일관적인 관리 계층

Resource Manager는 Mac, Linux 및 Windows, Azure Portal 또는 REST API용 Azure PowerShell, Azure CLI를 통해 호환되는 작업을 제공합니다. 가장 잘 작동하는 인터페이스를 사용하여 혼동 없이 인터페이스 간을 빠르게 이동할 수 있습니다.

Azure PowerShell에 대한 정보는 [Azure 리소스 관리자와 함께 Azure PowerShell 사용](powershell-azure-resource-manager.md) 및 nd [Azure 리소스 관리자 Cmdlets](https://msdn.microsoft.com/library/azure/dn757692.aspx)을 참조하십시오.

Azure CLI에 대한 정보는 [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](xplat-cli-azure-resource-manager.md)을 참조하십시오.

REST API에 대한 정보는 [Azure 리소스 관리자 REST API 참조](https://msdn.microsoft.com/library/azure/dn790568.aspx)를 참조하십시오.. 배포된 리소스에 대한 REST 작업을 보려면 [Azure 리소스 탐색기를 사용하여 리소스 보기 및 수정](resource-manager-resource-explorer.md)을 참조하세요.

포털을 사용하는 방법에 대한 정보는 [Resource Manager 템플릿 및 Azure 포털을 사용하여 리소스 배포](resource-group-template-deploy-portal.md)를 참조하세요.

Azure 리소스 관리자는 크로스-원본 자원 공유 (CORS)를 지원합니다. CORS를 통해 다른 도메인에 상주하는 웹 응용 프로그램에서 리소스 관리자 REST API 또는 Azure 서비스 REST API를 호출할 수 있습니다. CORS 지원하지 않는 웹 브라우저에서는 한 도메인의 응용 프로그램이 다른 도메인의 리소스에 액세스할 수 없습니다. 리소스 관리자는 유효한 인증 자격 증명을 사용하여 모든 요청에 대해 CORS할 수 있습니다.

## SDK

Azure SDK는 여러 언어 및 플랫폼에 사용할 수 있습니다. 이러한 언어 구현은 각각 해당 에코 시스템 패키지 관리자 및 GitHub를 통해 사용할 수 있습니다.

이러한 SDK의 각 코드는 Azure RESTful API 사양에서 생성됩니다. 이러한 사양은 오픈 소스이며 Swagger 2.0 사양을 기반으로 합니다. SDK 코드는 AutoRest라는 오픈 소스 프로젝트를 통해 생성됩니다. AutoRest는 이러한 RESTful API 사양을 여러 언어의 클라이언트 라이브러리로 변환합니다. SDK에서 생성된 코드의 어떤 측면을 개선하려면, SDK를 만드는 전체 도구는 오픈되어 자유롭게 사용 가능하고 광범위하게 채택된 API 사양 형식에 기반으로 해야 합니다.

다음은 오픈 소스 SDK 리포지토리입니다. 피드백, 문제 및 끌어오기 요청을 환영합니다.

[.NET](https://github.com/Azure/azure-sdk-for-net) | [Java](https://github.com/Azure/azure-sdk-for-java) | [Node.js](https://github.com/Azure/azure-sdk-for-node) | [PHP](https://github.com/Azure/azure-sdk-for-php) | [Python](https://github.com/Azure/azure-sdk-for-python) | [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE] SDK가 필요한 기능을 제공하지 않는 경우 [Azure REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)에 직접 요청할 수 있습니다.

## 샘플

### .NET

- [Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)
- [템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)

### Java

- [Azure 리소스 관리](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource/)
- [Azure 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)

### Node.js

- [Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)

### Python

- [Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)

### Ruby

- [Azure 리소스 및 리소스 그룹 관리](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)
- [템플릿을 사용하여 SSH 사용 VM 배포](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


이러한 샘플 외에도 갤러리 샘플을 통해 검색할 수 있습니다.

[.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) | [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) | [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs) | [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python) | [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

## 다음 단계

- 템플릿으로 작업하는 방법에 대한 간단한 소개는 [기존 리소스에서 Azure Resource Manager 템플릿 내보내기](resource-manager-export-template.md)를 참조하세요.
- 템플릿 작성에 대한 안내는 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요.
- 템플릿에서 사용할 수 있는 함수를 이해하려면 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.
- Resource Manager로 Visual Studio를 사용하는 방법에 대한 정보는 [Visual Studio를 통해 Azure 리소스 그룹 생성 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.
- Resource Manager와 함께 VS 코드를 사용하는 방법에 대한 정보는 [Visual Studio 코드에서 Azure Resource Manager 템플릿으로 작업](resource-manager-vs-code.md)을 참조하세요.

이 개요에 대한 비디오 데모는 다음과 같습니다.

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0921_2016-->