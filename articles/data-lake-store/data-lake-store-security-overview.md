<properties
   pageTitle="Data Lake Store의 보안에 대한 개요 | Microsoft Azure"
   description="Azure Data Lake Store가 보다 안전한 보안 빅 데이터 저장소인지 이해"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Azure Data Lake Store의 보호

대부분의 기업에서는 현명한 의사 결정을 내리는 데 도움을 주는 비즈니스 insights에 대한 빅 데이터 분석을 활용합니다. 조직은 다양한 사용자 수가 증가하는 복잡하고 규제된 환경을 포함할 수 있습니다. 중요한 비즈니스 데이터가 개별 사용자에게 부여된 올바른 액세스 수준으로 더욱 안전하게 저장되도록 하는 것이 기업에게 필수적입니다. Azure Data Lake Store는 이러한 보안 요구 사항에 부응하도록 설계되었습니다. 이 문서에서는 다음을 포함하는 Data Lake Store의 보안 기능에 대해 알아봅니다.

* 인증
* 권한 부여
* 네트워크 격리
* 데이터 보호
* 감사

## 인증 및 ID 관리

인증은 사용자가 Data Lake Store 또는 Data Lake Store에 연결하는 서비스와 상호 작용할 때 사용자의 ID를 확인하는 프로세스입니다. ID 관리 및 인증을 위해 Data Lake Store는 [Azure Active Directory](../active-directory/active-directory-whatis.md)와 사용자 및 그룹의 관리를 간소화하는 포괄적인 ID 및 액세스 관리 클라우드 솔루션을 사용합니다.

각 Azure 구독은 Azure Active Directory의 인스턴스와 연결될 수 있습니다. 사용자 및 Azure Active Directory 서비스에서 정의된 서비스 ID는 Azure 포털, 명령줄 도구를 사용하거나 Azure Data Lake Store SDK를 사용하여 조직이 빌드하는 클라이언트 응용 프로그램을 통해 Data Lake Store 계정에 액세스할 수 있습니다. 중앙 집중식 액세스 제어 메커니즘으로 Azure Active Directory를 사용하는 주요 이점은 다음과 같습니다.

* 단순화된 ID 수명 주기 관리. 디렉터리에서 계정을 삭제하거나 비활성화하여 사용자 또는 서비스(서비스 주체 ID)를 신속하게 만들고 해지할 수 있습니다.

* 다단계 인증. [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)은 사용자 로그인 및 트랜잭션에 대해 추가 보안 계층을 제공합니다.

* OAuth 또는 OpenID와 같은 표준 개방형 프로토콜을 통해 모든 클라이언트에서 인증.

* 엔터프라이즈 디렉터리 서비스 및 클라우드 ID 공급자와 페더레이션.

## 권한 부여 및 액세스 제어

사용자가 Azure Data Lake Store에 액세스할 수 있도록 Azure Active Directory가 사용자를 인증한 후 권한 부여로 Data Lake Store에 대한 액세스 권한을 제어합니다. Data Lake Store는 다음과 같은 방식으로 계정 관련 작업과 데이터 관련 작업에 대한 권한 부여를 구분합니다.

* 계정을 관리하기 위해 Azure에서 제공하는 RBAC([역할 기반 액세스 제어](../active-directory/role-based-access-control-what-is.md))
* 저장소에서 데이터에 액세스하기 위한 POSIX ACL


### 계정 관리를 위한 RBAC

기본적으로 네 가지 기본 역할이 Data Lake Store에 대해 정의됩니다. 역할은 Azure 포털, PowerShell cmdlet 및 REST API를 통해 Data Lake Store 계정에서 다양한 작업을 허용합니다. 소유자 및 참여자 역할은 해당 계정에 다양한 관리 기능을 수행할 수 있습니다. 데이터와 상호 작용하는 사용자에 리더 역할을 할당할 수 있습니다.

![RBAC 역할](./media/data-lake-store-security-overview/rbac-roles.png "RBAC 역할")

계정 관리에 대한 역할이 할당되어 있지만 일부 역할은 데이터 액세스에 영향을 줍니다. 파일 시스템에 대해 사용자가 수행할 수 있는 작업에 대한 액세스를 제어하기 위해 ACL을 사용해야 합니다. 다음 표는 관리 권한 및 기본 역할에 대한 데이터 액세스 권한의 요약을 보여 줍니다.

| 역할 | 관리 권한 | 데이터 액세스 권한 | 설명 |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| 할당된 역할 없음 | 없음 | ACL에 의해 제어 | 사용자는 Azure 포털 또는 Azure PowerShell Cmdlet을 사용하여 Data Lake Store를 찾아볼 수 없습니다. 사용자는 명령줄 도구만 사용할 수 있습니다.
| 소유자 | 모두 | 모두 | 소유자 역할은 superuser입니다. 이 역할은 모든 것을 관리할 수 있으며 데이터에 대한 완전한 액세스를 가집니다.
| 리더 | 읽기 전용 | ACL에 의해 제어 | 리더 역할은 계정 관리와 관련된 모든 항목(예: 어떤 사용자가 어떤 역할에 할당되는지)을 볼 수 있습니다. 리더 역할은 항목을 변경할 수 없습니다. |
| 참여자 | 역할 추가 및 제거를 제외한 모든 항목 | ACL에 의해 제어 | 참여자 역할은 배포 및 경고 만들기 및 관리와 같은 계정의 일부 측면을 관리할 수 있습니다. 참여자 역할은 역할을 추가 또는 제거할 수 없습니다.
| 사용자 액세스 관리자 | 역할 추가 및 제거 | ACL에 의해 제어 | 사용자 액세스 관리자 역할은 계정에 대한 사용자 액세스를 관리할 수 있습니다. |

자세한 내용은 [사용자 또는 보안 그룹을 Data Lake Store 계정에 할당](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts)을 참조하세요.

### 파일 시스템 작업에 ACL 사용

Data Lake Store는 HDFS(Hadoop 분산 파일 시스템)와 같은 계층적 파일 시스템이며 [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)을 지원합니다. 소유자 역할, 소유자 그룹 및 다른 사용자 및 그룹의 리소스에 대한 읽기(r), 쓰기(w) 및 실행(x) 권한을 제어합니다. Data Lake Store 공개 미리 보기(현재 릴리스)에서 ACL은 루트 폴더, 하위 폴더 및 개별 파일에서도 사용할 수 있습니다. 루트 폴더에 적용하는 ACL은 모든 하위 폴더 및 파일에도 적용됩니다.

[보안 그룹](../active-directory/active-directory-accessmanagement-manage-groups.md)을 사용하여 여러 사용자에 대한 ACL을 정의하는 것이 좋습니다. 사용자를 보안 그룹에 추가한 다음 파일 또는 폴더에 대한 ACL을 해당 보안 그룹에 할당합니다. 사용자 지정 액세스에 대해 최대 9개의 항목을 추가하는 제한 때문에 사용자 지정 액세스를 제공하려는 경우에 유용합니다. Azure Active Directory 보안 그룹을 사용하여 Data Lake Store에 저장된 데이터의 보안을 강화하는 방법에 대한 자세한 내용은 [ACL인 사용자 또는 보안 그룹을 Azure Data Lake Store 파일 시스템에 할당](data-lake-store-secure-data.md#filepermissions)을 참조하세요.

![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-security-overview/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")

## 네트워크 격리

네트워크 수준에서 데이터 저장소에 대한 액세스를 제어하려면 Data Lake Store를 사용합니다. 방화벽을 설정하고 신뢰할 수 있는 클라이언트에 대한 IP 주소 범위를 정의할 수 있습니다. IP 주소 범위와 함께 정의된 범위 내에서 IP 주소를 가지고 있는 클라이언트만 Data Lake Store에 연결할 수 있습니다.

![방화벽 설정 및 IP 액세스](./media/data-lake-store-security-overview/firewall-ip-access.png "방화벽 설정 및 IP 주소")

## 데이터 보호

조직에서는 수명 주기 내내 비즈니스에 중요한 데이터가 보호되는지 확인하려고 합니다. 전송 중인 데이터의 경우 Data Lake Store는 업계 표준 TLS(전송 계층 보안) 프로토콜을 사용하여 클라이언트 및 Data Lake Store 간 이동하는 데이터를 보호합니다.

미사용 데이터에 대한 데이터 보호는 향후 릴리스에서 제공될 예정입니다.

## 감사 및 진단 로그

관리 관련 활동 또는 데이터 관련 활동에 대한 로그를 찾는지에 따라 감사 또는 진단 로그를 사용할 수 있습니다.

*  관리 관련 활동에서는 Azure Resource Manager API를 사용하며 감사 로그를 통해 Azure 포털에 표시됩니다.
*  데이터 관련 활동에서는 WebHDFS REST API를 사용하고 진단 로그를 통해 Azure 포털에 표시됩니다.

### 감사 로그

규정을 준수하기 위해 특정 인시던트를 자세히 살펴보아야 하는 경우 조직에 적절한 감사 내역이 필요할 수 있습니다. Data Lake Store에는 기본 제공 모니터링 및 감사가 있으며 모든 계정 관리 활동을 기록합니다.

계정 관리 감사 내역의 경우 기록하려는 열을 보고 선택합니다. Azure Storage에 감사 로그를 내보낼 수도 있습니다.

![감사 로그](./media/data-lake-store-security-overview/audit-logs.png "감사 로그")

### 진단 로그

Azure 포털에서 데이터 액세스 감사 내역을 설정(진단 설정에서)하고 로그가 저장되는 Azure Blob 저장소 계정을 만들 수 있습니다.

![진단 로그](./media/data-lake-store-security-overview/diagnostic-logs.png "진단 로그")

진단 설정을 구성한 후 **진단 로그** 탭에서 로그를 볼 수 있습니다.

Azure Data Lake Store와 함께 진단 로그를 사용하는 방법에 대한 자세한 내용은 [Data Lake Store에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)를 참조하세요.

## 요약

엔터프라이즈 고객은 안전하고 사용하기 간편한 데이터 분석 클라우드 플랫폼을 요구합니다. Azure Data Lake Store는 Azure Active Directory 통합, ACL 기반 권한 부여, 네트워크 격리, 전송 중 및 미사용 데이터 암호화(향후 제공) 및 감사를 통해 ID 관리 및 인증으로 이러한 요구 사항을 해결하도록 설계되었습니다.

Data Lake Store의 새로운 기능을 참조하려는 경우 [Data Lake Store UserVoice 포럼](https://feedback.azure.com/forums/327234-data-lake)에 사용자 의견을 보내 주세요.

## 참고 항목

- [Azure Data Lake Store 개요](data-lake-store-overview.md)
- [Data Lake Store 시작](data-lake-store-get-started-portal.md)
- [Data Lake Store의 데이터 보호](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0914_2016-->