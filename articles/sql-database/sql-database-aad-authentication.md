<properties
   pageTitle="Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결하는 방법을 확인합니다."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결

Azure Active Directory 인증은 Azure AD(Azure Active Directory)의 ID를 사용하여 Microsoft Azure SQL 데이터베이스 및 [SQL 데이터 웨어하우스](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 연결하는 메커니즘입니다. Azure Active Directory 인증을 사용하면 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 하나의 중앙 위치에서 관리할 수 있습니다. 중앙 ID 관리는 데이터베이스 사용자 관리를 위한 단일 위치를 제공하며 권한 관리를 간소화합니다. 이점은 다음과 같습니다.

- SQL Server 인증에 대한 대안을 제공합니다.
- 데이터베이스 서버 전체에서 사용자 ID의 확산을 중지합니다.
- 한 위치에서의 암호 회전이 가능합니다.
- 고객이 외부(AAD) 그룹을 사용하여 데이터베이스 사용 권한을 관리할 수 있습니다.
- Windows 통합 인증 또는 Azure Active Directory에서 지원하는 기타 인증을 사용하여 암호 저장을 제거할 수 있습니다.
- Azure Active Directory 인증에서는 포함된 데이터베이스 사용자를 통해 데이터베이스 수준에서 ID를 인증합니다.
- Azure Active Directory는 SQL 데이터베이스에 연결되는 응용 프로그램에 대한 토큰 기반 인증을 지원합니다.
- Azure Active Directory 인증은 도메인 동기화 없이 로컬 Azure Active Directory에 대해 ADFS(도메인 페더레이션) 또는 기본 사용자/암호 인증을 지원합니다.
- Azure Active Directory는 MFA(Multi-Factor Authentication)를 포함하는 Active Directory 유니버설 인증을 사용하는 SQL Server Management Studio를 통해 연결하도록 지원합니다. MFA는 전화 통화, 문자 메시지, 모바일 앱 알림 등의 여러 가지 간편한 검증 옵션을 제공하는 강력한 인증을 포함합니다. 자세한 내용은 [SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원](sql-database-ssms-mfa-authentication.md)을 참조하세요.

구성 단계에는 Azure Active Directory 인증의 구성 및 사용을 위한 다음 절차가 포함됩니다.

1. Azure Active Directory를 만들고 채웁니다.
2. Azure SQL 데이터베이스 V12에 데이터베이스가 있는지 확인합니다. (SQL 데이터 웨어하우스의 경우 필요하지 않음)
3. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.
4. Azure SQL Server 또는 [Azure SQL 데이터 웨어하우스](https://azure.microsoft.com/services/sql-data-warehouse/)에 대한 Azure Active Directory 관리자를 만듭니다.
5. 클라이언트 컴퓨터를 구성합니다.
6. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자를 만듭니다.
7. Azure AD ID를 사용하여 데이터베이스에 연결합니다.


## 트러스트 아키텍처

다음의 간략한 다이어그램에서는 Azure SQL 데이터베이스에서 Azure AD 인증을 사용하는 솔루션 아키텍처를 요약 설명합니다. SQL 데이터 웨어하우스에 동일한 개념이 적용됩니다. Azure Active Directory 기본 사용자 암호를 지원하기 위해 클라우드 부분 및 Azure AD/Azure SQL 데이터베이스만 고려합니다. 페더레이션 인증(또는 Windows 자격 증명에 대한 사용자/암호)을 지원하려면 ADFS 블록과의 통신이 필요합니다. 화살표는 통신 경로 나타냅니다.

![aad auth 다이어그램][1]

다음 다이어그램은 토큰을 제출하여 클라이언트가 데이터베이스에 연결할 수 있는 페더레이션, 신뢰 및 호스팅 관계를 나타냅니다. 이 토큰은 Azure AD를 통해 인증되고 데이터베이스에서 신뢰됩니다. 고객 1은 기본 사용자가 있는 Azure Active Directory 또는 페더레이션된 사용자가 있는 Azure Active Directory를 나타낼 수 있습니다. 고객 2는 가져온 사용자를 포함하는 가능한 해결 방법을 나타냅니다. 이 예에서는 Azure Active Directory와 동기화되는 ADFS로 페더레이션된 Azure Active Directory에서 가져옵니다. Azure AD 인증을 사용하는 데이터베이스에 액세스하려면 호스팅 구독이 Azure Active Directory에 연결되어 있어야 합니다. Azure SQL 데이터베이스 또는 SQL 데이터 웨어하우스를 호스트하는 SQL Server를 만들려면 동일한 구독을 사용해야 합니다.

![구독 관계][2]

## 관리자 구조

Azure AD 인증을 사용할 때는 SQL 데이터베이스 서버에 대해 SQL Server 관리자와 Azure AD 관리자 등, 두 관리자 계정이 있습니다. SQL 데이터 웨어하우스에 동일한 개념이 적용됩니다. Azure AD 계정을 기반으로 하는 관리자만 사용자 데이터베이스에서 최초 Azure AD 포함 데이터베이스 사용자를 만들 수 있습니다. Azure AD 관리자 로그인은 Azure AD 사용자나 Azure AD 그룹이 될 수 있습니다. 관리자가 그룹 계정일 경우 모든 그룹 구성원이 사용할 수 있으므로 해당 SQL Server 인스턴스에 대해 여러 Azure AD 관리자가 가능합니다. 그룹 계정을 관리자로 사용하면 SQL 데이터베이스에서 사용자나 권한을 변경하지 않고도 Azure AD의 그룹 구성원을 중앙에서 추가 및 제거할 수 있으므로 관리 효율성이 향상됩니다. 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)를 구성할 수 있습니다.

![관리자 구조][3]

## 권한

새 사용자를 만들려면 데이터베이스에서 `ALTER ANY USER` 권한이 있어야 합니다. `ALTER ANY USER` 권한은 아무 데이터베이스 사용자에게나 부여할 수 있습니다. 서버 관리자 계정과, 해당 데이터베이스에 대한 `CONTROL ON DATABASE` 또는 `ALTER ON DATABASE` 권한이 있는 데이터베이스 사용자와, `db_owner` 데이터베이스 역할 그룹의 구성원도 `ALTER ANY USER` 권한을 보유할 수 있습니다.

Azure SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 포함된 데이터베이스 사용자를 만들려면 Azure AD ID를 사용하여 데이터베이스에 연결해야 합니다. 최초 포함 데이터베이스 사용자를 만들려면 Azure Active Directory 관리자(데이터베이스 소유자)를 사용하여 데이터베이스에 연결해야 합니다. 이에 대해서는 아래 4, 5단계에서 설명합니다. 모든 Azure Active Directory 인증은 Azure Active Directory 관리자가 Azure SQL 데이터베이스 또는 SQL 데이터 웨어하우스 서버에 대해 생성된 경우에만 가능합니다. Azure Active Directory 관리자가 서버에서 제거된 경우 SQL Server 내에 이전에 만든 기존 Azure Active Directory 사용자는 Azure Active Directory 자격 증명을 사용하여 데이터베이스에 더 이상 연결할 수 없습니다.

## Azure AD 기능 및 제한 사항

Azure Active Directory의 다음 멤버를 Azure SQL Server 또는 SQL 데이터 웨어하우스에서 프로비저닝할 수 있습니다.

- 기본 멤버: 고객 도메인 또는 관리되는 도메인 내 Azure AD에서 만든 멤버입니다. 자세한 내용은 [Azure AD에 고유한 도메인 이름을 추가](../active-directory/active-directory-add-domain.md)를 참조하세요.

- 도메인 구성원 멤버: 페더레이션 도메인으로 Azure AD에 만들어진 멤버입니다. 자세한 내용은 [Microsoft Azure는 이제 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)을 참조하세요.

- 네이티브 또는 페더레이션 도메인 멤버인 다른 Azure Active Directory에서 가져온 멤버입니다.

- 보안 그룹으로 만들어진 Active Directory 그룹.

Microsoft 계정(예: outlook.com, hotmail.com, live.com) 또는 다른 게스트 계정(예: gmail.com, yahoo.com)은 지원되지 않습니다. 계정 및 암호를 사용하여 [https://login.live.com](https://login.live.com)에 로그인할 수 있는 경우 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에 대한 Azure AD 인증에 지원되지 않는 Microsoft 계정을 사용하는 것입니다.

### 추가 고려 사항

- 관리 효율성을 높일 수 있게 관리자 권한으로 전용 Azure Active Directory 그룹을 프로비전하는 것이 좋습니다.
- Azure SQL Server 또는 Azure SQL 데이터 웨어하우스에 대해 한 번에 하나의 Azure AD 관리자(그룹 또는 사용자)만 구성할 수 있습니다.
- SQL Server에 대한 Azure Active Directory 관리자만 Azure Active Directory 계정을 사용하여 Azure SQL Server 또는 Azure SQL 데이터 웨어하우스에 처음 연결할 수 있습니다. Active Directory 관리자가 이후의 Azure Active Directory 사용자를 구성할 수 있습니다.
- 연결 제한 시간은 30초로 설정하는 것이 좋습니다.
- SQL Server 2016 Management Studio 및 Visual Studio 2015용 SQL Server Data Tools(버전 14.0.60311.1 2016년 4월 이상)는 Azure Active Directory 인증을 지원합니다. Azure Active Directory 인증은 **.NET Framework Data Provider for SqlServer**(.NET Framework 4.6 버전 이상)에서 지원됩니다. 따라서 이러한 도구 및 데이터 계층 응용 프로그램(DAC 및 .bacpac)의 최신 버전에서는 Azure Active Directory 인증을 사용할 수 있습니다.
- [ODBC 버전 13.1](https://www.microsoft.com/download/details.aspx?id=53339)은 Azure Active Directory 인증을 지원하지만 `bcp.exe`는 구형 ODBC 공급자를 사용하기 때문에 Azure Active Directory 인증을 사용하여 연결할 수 없습니다.
- `sqlcmd`는 [다운로드 센터](http://go.microsoft.com/fwlink/?LinkID=825643)에서 사용할 수 있는 버전 13.1부터 Azure Active Directory 인증을 지원합니다.
- Visual Studio 2015용 SQL Server Data Tools는 Data Tools의 2016년 4월 버전 이상이 필요합니다(버전 14.0.60311.1). 현재 Azure Active Directory 사용자는 SSDT 개체 탐색기에 표시되지 않습니다. 해결 방법으로 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx)에서 사용자를 봅니다.
- [SQL Server용 Microsoft JDBC 드라이버 6.0](https://www.microsoft.com/ko-KR/download/details.aspx?id=11774)은 Azure Active Directory 인증을 지원합니다. 또한 [연결 속성 설정](https://msdn.microsoft.com/library/ms378988.aspx)을 참조하세요.
- PolyBase는 Azure Active Directory 인증을 사용하여 인증할 수 없습니다.
- BI 및 Excel 등, 일부 도구는 지원되지 않습니다.
- Azure Active Directory 인증은 Azure 포털 **데이터베이스 가져오기** 및 **데이터베이스 내보내기** 블레이드로 SQL 데이터베이스에서 지원됩니다. Azure Active Directory 인증을 사용한 가져오기 및 내보내기도 PowerShell 명령에서 지원됩니다.


## 1\. Azure AD 만들기 및 채우기

Azure Active Directory를 만들고 사용자 및 그룹으로 채웁니다. Azure Active Directory는 초기 도메인 Azure AD의 관리되는 도메인일 수 있습니다. Azure Active Directory는 Azure Active Directory와 페더레이션된 온-프레미스 Active Directory 도메인 서비스일 수도 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/active-directory-aadconnect.md), [Azure AD에 고유한 도메인 이름 추가](../active-directory/active-directory-add-domain.md), [이제 Microsoft Azure에서 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD 디렉터리 관리](https://msdn.microsoft.com/library/azure/hh967611.aspx) 및 [Windows PowerShell을 사용한 Azure AD 관리](https://msdn.microsoft.com/library/azure/jj151815.aspx)를 참조하세요.

## 2\. SQL 데이터베이스가 버전 12인지 확인

Azure Active Directory 인증은 최신 SQL 데이터베이스 V12에서 지원됩니다. SQL 데이터베이스 V12에 대한 자세한 내용과 해당 지역에서의 사용 가능 여부는 [최신 SQL 데이터베이스 업데이트 V12의 새로운 기능](sql-database-v12-whats-new.md)을 참조하세요. SQL 데이터 웨어하우스는 V12에서만 사용할 수 있으므로 Azure SQL 데이터 웨어하우스에는 이 단계가 필요하지 않습니다.

기존 데이터베이스를 사용할 경우 데이터베이스(예: SQL Server Management Studio)에 연결하고 `SELECT @@VERSION;`을 실행하여 해당 데이터베이스가 SQL Database V12에서 호스트되는지 확인합니다. SQL 데이터베이스 V12의 데이터베이스에서 예상되는 출력은 **Microsoft SQL Azure (RTM) - 12.0** 이상입니다. 데이터베이스가 SQL 데이터베이스 V12에서 호스팅되지 않을 경우 [ SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md)를 참조하고, Azure 클래식 포털을 방문하여 데이터베이스를 SQL 데이터베이스 V12로 마이그레이션합니다.

또는 [첫 번째 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)에 나열된 단계에 따라 SQL 데이터베이스 V12에 새 데이터베이스를 만들 수 있습니다. **팁**: 새 데이터베이스에 대한 구독을 선택하기 전에 다음 단계를 확인합니다.

## 3\. 옵션: 현재 Azure 구독과 연결된 Active Directory를 연결하거나 변경합니다.

조직에서 Azure AD 디렉터리에 데이터베이스를 연결하려면 해당 디렉터리가 데이터베이스를 호스팅하는 Azure 구독에서 신뢰할 수 있는 디렉터리여야 합니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](https://msdn.microsoft.com/library/azure/dn629581.aspx)를 참조하세요.

**추가 정보:** 모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 장치를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. [https://manage.windowsazure.com/](https://manage.windowsazure.com/)의 **설정** 탭에서 구독이 신뢰하는 디렉터리를 확인할 수 있습니다. 구독이 디렉터리와 갖는 이 트러스트 관계는 구독이 Azure의 다른 모든 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다르며 구독의 하위 리소스와 더 유사합니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하여 디렉터리 사용자를 계속 관리할 수 있습니다. 리소스에 대한 자세한 내용은 [Azure의 리소스 액세스 이해](https://msdn.microsoft.com/library/azure/dn584083.aspx)를 참조하세요.

다음 절차는 특정 구독에 대해 연결된 디렉터리를 변경하는 방법에 대한 단계별 지침을 제공합니다.

1. Azure 구독 관리자를 통해 [Azure 클래식 포털](https://manage.windowsazure.com/)에 연결합니다.
2. 왼쪽 배너에서 **설정**을 선택합니다.
3. 구독이 설정 화면에 표시됩니다. 원하는 구독이 나타나지 않으면 위쪽의 **구독**을 클릭하고 **디렉터리로 필터링** 상자를 드롭다운하여 구독이 포함된 디렉터리를 선택한 다음 **적용**을 클릭합니다.

	![구독 선택][4]
4. **설정** 영역에서 구독을 클릭하고 페이지 아래쪽의 **디렉터리 편집**을 클릭합니다.

	![ad-settings-portal][5]
5. **디렉터리 편집** 상자에서 SQL Server 또는 SQL 데이터 웨어하우스와 연결된 Azure Active Directory를 선택하고 다음 화살표를 클릭합니다.

	![edit-directory-select][6]
6. **확인** 디렉터리 매핑 대화 상자에서 "**모든 공동 관리자가 제거됩니다.**"를 확인합니다.

	![edit-directory-confirm][7]
7. 확인을 클릭하여 포털을 다시 로드합니다.

> [AZURE.NOTE] 디렉터리를 변경하면 모든 공동 관리자, Azure AD 사용자 및 그룹, 디렉터리 기반 리소스 사용자에 대한 액세스가 제거되며 더 이상 이 구독 또는 관련 리소스에 액세스할 수 없습니다. 서비스 관리자 본인만 새 디렉터리를 기반으로 하는 주체에 대한 액세스를 구성할 수 있습니다. 이 변경이 모든 리소스에 전파되는 데는 상당한 시간이 소요될 수 있습니다. 디렉터리를 변경하면 SQL 데이터베이스 및 SQL 데이터 웨어하우스에 대한 Azure AD 관리자도 변경되며 기존 Azure AD 사용자의 데이터베이스 액세스가 허용되지 않습니다. Azure AD 관리자를 재설정하고(아래 설명 참조) 새 Azure AD 사용자를 만들어야 합니다.

## 4\. Azure SQL Server에 대한 Azure AD 관리자 만들기

각각의 Azure SQL Server(SQL 데이터베이스 또는 SQL 데이터 웨어하우스를 호스트하는)는 전체 Azure SQL Server의 관리자인 단일 서버 관리자 계정으로 시작됩니다. Azure AD 계정인 두 번째 SQL Server 관리자를 만들어야 합니다. 이 주체는 마스터 데이터베이스에 포함된 데이터베이스 사용자로 생성됩니다. 관리자인 서버 관리자 계정은 모든 사용자 데이터베이스에서 **db\_owner** 역할의 멤버이며 각 사용자 데이터베이스에 **dbo** 사용자로 들어갑니다. 서버 관리자 계정에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)와, [Azure SQL 데이터베이스 보안 지침 및 제한 사항](sql-database-security-guidelines.md)의 **로그인 및 사용자** 섹션을 참조하세요.

Azure Active Directory와 함깨 Geo-Replication을 사용할 때 Azure Active Directory 관리자는 주 서버와 보조 서버 모두에 대해 구성되어야 합니다. 서버에 Azure Active Directory 관리자가 없는 경우 다음 Azure Active Directory 로그인 및 사용자는 서버에 "연결할 수 없음" 오류를 수신합니다.

> [AZURE.NOTE] Azure AD 계정을 기반으로 하지 않는 사용자(Azure SQL Server 관리자 계정 포함)는 Azure AD에서 제안된 데이터베이스 사용자에 대한 유효성 검사 권한이 없으므로 Azure AD 기반 사용자를 만들 수 없습니다.

### Azure 포털을 사용하여 Azure SQL Server에 대한 Azure Active Directory 관리자 프로비전

1. [Azure 포털](https://portal.azure.com/)의 상단 오른쪽 끝에서 해당 연결을 클릭하여 가능한 Active Directory 목록을 드롭다운합니다. 정확한 Active Directory를 기본 Azure AD로 선택합니다. 이 단계는 구독 연결을 Azure SQL Server의 Active Directory와 연결하여 동일한 구독이 두 Azure AD 및 SQL Server에 사용되게 합니다. (Azure SQL Server는 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에서 호스트할 수 있습니다.)

	![choose-ad][8]
2. 왼쪽 배너에서 **SQL Server**와 해당 **SQL Server**를 선택한 다음 **SQL Server** 블레이드의 위쪽에서 **설정**을 선택합니다.

	![ad 설정][9]
3. **설정** 블레이드에서 **Active Directory 관리를 클릭합니다.
4. **Active Directory 관리자** 블레이드에서 **Active Directory 관리**를 클릭한 다음 위쪽의 **관리자 설정**을 클릭합니다.
5. **관리자 추가** 블레이드에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음 **선택**을 클릭합니다. Active Directory 관리 블레이드에 해당 Active Directory에 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. 위의 **Azure AD 기능 및 제한 사항**에서 지원되는 관리자 목록을 참조하세요. 역할 기반 액세스 제어(RBAC)는 포털에만 적용되며 SQL Server에 전파되지 않습니다.
6. **Active directory 관리자** 블레이드 위쪽에서 **저장**을 클릭합니다. ![관리자 선택][10]

	관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 **Active Directory 관리자** 상자에 표시됩니다.

> [AZURE.NOTE] Azure AD 관리자를 설정하는 경우, 새 관리자 이름(사용자 또는 그룹)이 SQL Server 인증 사용자로 가상 master 데이터베이스에 있으면 안 됩니다. 새 관리자 이름이 있으면 Azure AD 관리자 설정은 실패하며, 생성 작업을 롤백하고 해당 관리자(이름)이 이미 존재한다는 것을 나타냅니다. SQL Server 인증 사용자는 Azure AD에 속하지 않기 때문에 Azure AD 인증을 사용하여 서버에 연결하려는 작업은 모두 실패합니다.

나중에 관리자를 제거하려면, **Active Directory 관리자** 블레이드 위쪽에서 **관리자 제거**를 클릭하고 **저장**을 클릭합니다.

### PowerShell을 사용하여 Azure SQL Server에 대한 Azure AD 관리자 프로비전

PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

- Add-AzureRmAccount
- Select-AzureRmSubscription


Azure AD 관리자 프로비전 및 관리에 사용되는 Cmdlet

| Cmdlet 이름 | 설명 |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) | Azure SQL Server 또는 Azure SQL 데이터 웨어하우스에 대한 Azure Active Directory 관리자 프로비전 (현재 구독 설정에서 수행되어야 함). |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Azure SQL Server 또는 Azure SQL 데이터 웨어하우스에 대한 Azure Active Directory 관리자 제거 |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) | 현재 Azure SQL Server 또는 Azure SQL 데이터 웨어하우스에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |

각 명령에 대한 자세한 내용을 확인하려면 PowerShell 명령 get-help를 사용합니다(예: ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``).

다음 스크립트는 리소스 그룹 **Group-23**에서 **demo\_server** 서버에 대해 이름이 **DBA\_Group**(개체 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`)인 Azure AD 관리자 그룹을 프로비전합니다.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 입력 매개 변수에는 Azure AD 표시 이름이나 사용자 계정 이름이 허용됩니다. 예를 들어 ``DisplayName="John Smith"`` 또는 ``DisplayName="johns@contoso.com"``입니다. Azure AD 그룹에는 Azure AD 표시 이름만 지원됩니다.

> [AZURE.NOTE] Azure PowerShell 명령 ```Set-AzureRmSqlServerActiveDirectoryAdministrator```는 지원되지 않는 사용자에 대한 Azure AD 관리자 프로비전을 차단하지 않습니다. 지원되지 않는 사용자를 프로비전할 수는 있지만 데이터베이스에 연결할 수는 없습니다. 위의 **Azure AD 기능 및 제한 사항**에서 지원되는 관리자 목록을 참조하세요.

다음 예제에서는 **ObjectID** 옵션을 사용합니다.

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] **DisplayName**이 고유하지 않을 경우 Azure AD **ObjectID**가 필수입니다. **ObjectID** 및 **DisplayName** 값을 검색하려면 Azure 클래식 포털의 Active Directory 섹션을 사용하고 사용자 또는 그룹의 속성을 확인합니다.

다음 예제에서는 Azure SQL Server에 대해 현재 Azure AD 관리자 정보를 반환합니다.

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

다음 예제에서는 Azure AD 관리자를 제거합니다.
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

REST API를 사용하여 Azure Active Directory 관리자를 프로비전할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조 및 Azure SQL 데이터베이스에 대한 작업](https://msdn.microsoft.com/library/azure/dn505719.aspx)을 참조하세요.

## 5\. 클라이언트 컴퓨터 구성

모든 클라이언트 컴퓨터에서 Azure AD를 사용하여 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에 연결하는 응용 프로그램 또는 사용자를 통해 다음 소프트웨어를 설치해야 합니다.

- .NET Framework 4.6 이상, [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)
- SQL Server용 Azure Active Directory 인증 라이브러리(**ADALSQL.DLL**)는 다운로드 센터([Microsoft SQL Server용 Microsoft Active Directory 인증 라이브러리)](http://www.microsoft.com/download/details.aspx?id=48742)에서 여러 언어로 제공됩니다(x86 및 amd64 모두 해당).

### 도구

- [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 또는 [Visual Studio 2015용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)를 설치하면 .NET Framework 4.6 요구 사항에 부합합니다.
- SSMS이 x86 버전의 **ADALSQL.DLL**을 설치합니다.
- SSDT가 amd64 버전의 **ADALSQL.DLL**을 설치합니다.
- [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs)의 최신 Visual Studio는 .NET Framework 4.6 요구 사항을 만족하지만, 필요한 amd64 버전의 **ADALSQL.DLL**을 설치하지 않습니다.

## 6\. Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기

### 포함된 데이터베이스 사용자 정보

Azure Active Directory 인증에는 포함된 데이터베이스 사용자로 만들 데이터베이스 사용자가 필요합니다. Azure AD ID를 기반으로 하는 포함된 데이터베이스 사용자는 마스터 데이터베이스에 로그인이 없는 데이터베이스 사용자이며, 데이터베이스와 연결된 Azure AD 디렉터리의 ID에 매핑됩니다. Azure AD ID는 개별 사용자 계정 또는 그룹일 수 있습니다. 포함된 데이터베이스 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

> [AZURE.NOTE] 데이터베이스 사용자(관리자 예외)는 포털을 사용하여 만들 수 없습니다. RBAC 역할은 SQL Server, SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 전파되지 않습니다. Azure RBAC 역할은 Azure 리소스 관리에 사용되며 데이터베이스 사용 권한에는 적용되지 않습니다. 예를 들어 **SQL Server 참여자** 역할은 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결 권한을 부여하지 않습니다. TRANSACT-SQL 문을 사용하여 데이터베이스에 직접 액세스 권한을 부여해야 합니다.

### SQL Server Management Studio 또는 SQL Server Data Tools를 사용하여 사용자 데이터베이스 또는 데이터 웨어하우스에 연결

Azure AD 관리자가 제대로 설정되었는지 확인하려면 Azure AD 관리자 계정을 사용하여 **master** 데이터베이스에 연결합니다. Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 프로비전하려면 해당 데이터베이스에 대한 액세스가 있는 Azure AD ID로 데이터베이스에 연결합니다.

> [AZURE.IMPORTANT] Azure Active Directory 인증에 대한 지원은 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 Visual Studio 2015의 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)에서 사용 가능합니다. SSMS의 2016년 8월 릴리스에는 관리자가 전화 통화, 문자 메시지, PIN이 있는 스마트 카드 또는 모바일 앱 알림을 사용하여 Multi-Factor Authentication을 요구할 수 있도록 하는 Active Directory 유니버설 인증도 포함되어 있습니다.

#### Active Directory 통합 인증을 사용하여 연결

페더레이션된 도메인의 Azure Active Directory 자격 증명을 사용하여 Windows에 로그인한 경우 이 방법을 사용합니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진 연결**) 대화 상자의 **인증** 상자에서 **Active Directory 통합 인증**을 선택합니다. 연결에 대한 기존 자격 증명이 있으므로 암호 입력이 필요하지 않습니다. ![AD 통합 인증 선택][11]

2. **옵션** 단추를 클릭하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. ![데이터베이스 이름 선택][13]


#### Active Directory 암호 인증을 사용하여 연결

Azure AD 관리 도메인을 사용하여 Azure AD 사용자 이름과 연결할 때 이 방법을 사용합니다. 원격 작업 등, 도메인 액세스 없이 페더레이션된 계정에도 이 방법을 사용할 수 있습니다.

Azure와 페더레이션되지 않은 도메인으로부터 자격 증명을 사용하여 Windows에 로그인하거나, 최초 또는 클라이언트 도메인 기반의 Azure AD를 사용하는 Azure AD 인증을 사용할 경우 이 방법을 선택합니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진에 연결**) 대화 상자의 **인증** 상자에서 **Active Directory 암호 인증**을 선택합니다.
2. **사용자 이름** 상자에 **username@domain.com** 형식으로 Azure Active Directory 사용자 이름을 입력합니다. Azure Active Directory의 계정이거나, Azure Active Directory와 페더레이션된 도메인의 계정이어야 합니다.
3. **암호** 상자에 Azure Active Directory 계정이나 페더레이션된 도메인 계정의 사용자 암호를 입력합니다. ![AD 암호 인증 선택][12]

4. **옵션** 단추를 클릭하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. (이전 옵션의 그래픽을 참조하세요.)


### 사용자 데이터베이스에서 Azure AD 포함 데이터베이스 사용자 만들기

Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 만들려면 **ALTER ANY USER** 이상의 권한이 있는 사용자인 Azure AD ID를 통해 데이터베이스에 연결합니다. 그런 다음 아래 TRANSACT-SQL 구문을 사용합니다.

	CREATE USER <Azure_AD_principal_name>
	FROM EXTERNAL PROVIDER;


 *Azure\_AD\_principal\_name* 은 Azure AD 사용자의 사용자 계정 이름이거나 Azure AD 그룹의 표시 이름일 수 있습니다.

**예:** Azure AD 페더레이션 또는 관리 도메인 사용자를 나타내는 포함된 데이터베이스 사용자를 만드는 방법

	CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
	CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Azure AD 또는 페더레이션 도메인 그룹을 나타내는 포함된 데이터베이스 사용자를 만들려면 보안 그룹의 표시 이름을 제공하십시오.

	CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Azure AD 토큰을 사용하여 연결할 응용 프로그램을 나타내는 포함된 데이터베이스 사용자를 만들려면 다음을 실행합니다.

	CREATE USER [appName] FROM EXTERNAL PROVIDER;

Azure Active Directory 기반의 포함된 데이터베이스 사용자 만들기와 관련한 자세한 내용은 [CREATE USER(Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.


> [AZURE.NOTE] Azure SQL Server에 대한 Azure Active Directory 관리자를 제거하면 Azure AD 인증 사용자가 서버에 연결되지 않도록 할 수 있습니다. 필요한 경우 사용할 수 없는 Azure AD 사용자는 SQL 데이터베이스 관리자가 수동으로 삭제할 수 있습니다.

데이터베이스 사용자를 만들 때 해당 사용자는 **연결** 권한을 부여 받으며 **공용** 역할의 멤버로서 해당 데이터베이스에 연결할 수 있습니다. 처음에 이 사용자에게 제공되는 권한은 **공용** 역할에 부여된 권한이거나 사용자가 속해 있는 Windows 그룹에 부여된 권한 뿐입니다. Azure AD 기반의 포함된 데이터베이스 사용자를 프로비전한 후에는 다른 사용자 유형에 권한을 부여하는 것과 같은 방식으로 이 사용자에게 추가 권한을 부여할 수 있습니다. 일반적으로 데이터베이스 역할에 권한을 부여하고 역할에 사용자를 추가합니다. 자세한 내용은 [데이터베이스 엔진의 권한 기초](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)를 참조하세요. 특수 SQL 데이터베이스 역할에 대한 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요. 관리 도메인에 가져온 페더레이션된 도메인은 관리되는 도메인 ID를 사용해야 합니다.

> [AZURE.NOTE] Azure AD 사용자는 데이터베이스 메타데이터에서 E 형식(EXTERNAL\_USER) 및 그룹의 경우 X 형식(EXTERNAL\_GROUPS)으로 표시됩니다. 자세한 내용은 [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx)를 참조하세요.


## 7\. Azure AD ID를 사용하여 연결

Azure Active Directory 인증에서는 Azure AD ID를 사용하여 데이터베이스에 연결하는 다음 방법을 지원합니다.

- 통합 Windows 인증 사용
- Azure AD 사용자 이름 및 암호 사용
- 응용 프로그램 토큰 인증 사용

### 7\.1. 통합(Windows) 인증을 사용하는 연결

Windows 통합 인증을 사용하려면 도메인의 Active Directory를 Azure Active Directory와 페더레이션해야 합니다. 데이터베이스에 연결되는 클라이언트 응용 프로그램(또는 서비스)은 사용자의 도메인 자격 증명으로 도메인에 가입된 컴퓨터에서 실행되어야 합니다.

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 데이터베이스 연결 문자열의 인증 키워드가 Active Directory 통합으로 설정되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

	string ConnectionString =
	@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

연결 문자열 키워드 ``Integrated Security=True``는 Azure SQL 데이터베이스 연결에 지원되지 않습니다. ODBC 연결을 설정할 때는 공백을 제거하고 인증을 'ActiveDirectoryIntegrated'로 설정해야 합니다.

### 7\.2. Azure AD 사용자 이름 및 암호로 연결
통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 인증 키워드가 Active Directory 암호로 설정되어 있어야 합니다. 연결 문자열에는 사용자 ID/UID 및 암호/PWD 키워드와 값이 포함되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

	string ConnectionString =
	  @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
	SqlConnection conn = new SqlConnection(ConnectionString);
	conn.Open();

[Azure AD 인증 GitHub 데모](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)에서 사용할 수 있는 데모 코드 샘플을 사용한 Azure AD 인증 방법에 대해 자세히 알아보세요.


### 7\.3 Azure AD 토큰에 연결
이 인증 방법을 사용하면 AAD(Azure Active Directory)에서 토큰을 가져와 Azure SQL 데이터베이스 또는 Azure SQL 데이터 웨어하우스에 연결할 수 있습니다. 이는 인증서 기반 인증을 비롯한 정교한 시나리오를 지원합니다. Azure AD 토큰 인증을 사용하려면 다음 네 가지 기본 단계를 완료해야 합니다.

1. Azure Active Directory에 응용 프로그램을 등록하고 코드에 대한 클라이언트 ID를 가져옵니다.
2. 응용 프로그램을 나타내는 데이터베이스 사용자를 만듭니다(이전 6단계에서 완료).
3. 응용 프로그램을 실행하는 클라이언트 컴퓨터에서 인증서를 만듭니다.
4. 인증서를 응용 프로그램의 키로 추가합니다.

샘플 연결 문자열:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

자세한 내용은 [SQL Server 보안 블로그](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)를 참조하세요.

### sqlcmd를 사용하여 연결  
다음 문은 [다운로드 센터](http://go.microsoft.com/fwlink/?LinkID=825643)에서 사용할 수 있는 sqlcmd 버전 13.1을 사용하여 연결합니다.

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## 참고 항목

[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)

[포함된 데이터베이스 사용자 관리](https://msdn.microsoft.com/library/ff929071.aspx)

[CREATE USER(Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

<!---HONumber=AcomDC_0921_2016-->