<properties
   pageTitle="SQL 데이터베이스 인증 및 권한 부여: 액세스 부여 | Microsoft Azure"
   description="SQL 데이터베이스 보안 관리, 특히 서버 수준 보안 주체 계정을 통해 데이터베이스 액세스 및 로그인 보안을 관리하는 방법에 대해 알아봅니다."
   keywords="sql 데이터베이스 보안,데이터베이스 보안 관리,로그인 보안,데이터베이스 보안,데이터베이스 액세스"
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
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# SQL 데이터베이스 인증 및 권한 부여: 액세스 부여 


> [AZURE.SELECTOR]
- [시작 자습서](sql-database-get-started-security.md)
- [액세스 권한 부여](sql-database-manage-logins.md)


관리자, 비관리자 및 역할에 대한 SQL 데이터베이스 액세스 개념의 개요에 대한 설명은 여기에서 시작합니다.

## 무제한 관리 계정

가상 master 데이터베이스 및 모든 사용자 데이터베이스에 대한 무제한 액세스 권한이 있는 두 개의 가능한 관리 계정이 있습니다. 이러한 계정을 서버 수준 주체 계정이라고 합니다.

### Azure SQL 데이터베이스 구독자 계정 

논리적 SQL 인스턴스가 만들어질 때, SQL 데이터베이스 구독자 계정이라는 단일 로그인 계정이 만들어집니다. 이 계정은 SQL Server 인증(사용자 이름 및 암호)을 사용하여 연결됩니다. 이 계정은 논리적 서버 인스턴스 및 해당 인스턴스에 연결된 모든 사용자 데이터베이스에서 관리자입니다. 구독자 계정의 사용 권한은 제한할 수 없습니다. 이러한 계정 중 하나만 존재할 수 있습니다.

### Azure Active Directory 관리자
하나의 Azure Active Directory 계정만 관리자로 구성할 수 있습니다. 이 계정은 개별 Azure AD 사용자이거나 여러 Azure AD 사용자를 포함하는 Azure AD 그룹일 수 있습니다. Azure AD 관리자를 구성하는 것은 선택 사항이지만 Azure AD 계정에서 SQL 데이터베이스에 연결하는 데 Windows 인증을 사용하려면 Azure AD 관리자를 반드시 구성해야 합니다. Azure Active Directory 액세스 구성에 대한 자세한 내용은 [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결](sql-database-aad-authentication.md) 및 [SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원](sql-database-ssms-mfa-authentication.md)을 참조하세요.

### 방화벽 구성
서버 수준 방화벽이 구성된 경우 Azure SQL 데이터베이스 구독자 계정 및 Azure Active Directory 계정을 master 데이터베이스 및 모든 사용자 데이터베이스에 연결할 수 있습니다. 포털을 통해 서버 수준 방화벽을 구성할 수 있습니다. 연결이 이루어지면 [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) Transact-SQL 문을 사용하여 추가 서버 수준 방화벽 규칙도 구성할 수 있습니다. 방화벽 구성에 대한 자세한 내용은 [방법: Azure 포털을 사용하여 Azure SQL 데이터베이스 방화벽 구성](sql-database-configure-firewall-settings.md)을 참조하세요.

### 관리자 액세스 경로

서버 수준 방화벽이 제대로 구성되면 SQL 데이터베이스 구독자 계정 및 Azure Active Directory SQL Server 관리자가 SQL Server Management Studio 또는 SQL Server Data Tools와 같은 클라이언트 도구를 사용하여 연결할 수 있습니다. 최신 도구만 모든 특징 및 기능을 제공합니다. 다음 다이어그램에서는 두 명의 관리자 계정에 대한 일반적인 구성을 보여 줍니다. ![관리자 액세스 경로](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

서버 수준 방화벽에서 열려 있는 포트를 사용하면 관리자가 모든 SQL 데이터베이스에 연결할 수 있습니다.

### SQL Server Management Studio를 사용하여 데이터베이스에 연결
SQL Server Management Studio를 사용하여 연결하는 연습은 [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결 및 샘플 T-SQL 쿼리 실행](sql-database-connect-query-ssms.md)을 참조하세요.


> [AZURE.IMPORTANT] Microsoft Azure 및 SQL 데이터베이스에 대한 업데이트와 동기화 상태를 유지하려면 항상 최신 버전의 Management Studio를 사용하는 것이 좋습니다. [SQL Server Management Studio를 업데이트합니다](https://msdn.microsoft.com/library/mt238290.aspx).


## 추가 특별 계정
SQL 데이터베이스는 사용자 계정을 추가할 수 있는 가상 master 데이터베이스에 두 가지 제한된 관리 역할을 제공합니다.

### 데이터베이스 작성자
관리자 계정에서 새 데이터베이스를 만들 수 있습니다. 데이터베이스를 만들 수 있는 추가 계정을 만들려면 master에서 사용자를 만들고 사용자를 특별 **dbmanager** 데이터베이스 역할에 추가해야 합니다. 사용자는 포함된 데이터베이스 사용자이거나 가상 master 데이터베이스에서 SQL Server 로그인을 기반으로 한 사용자일 수 있습니다.

1.	관리자 계정을 사용하여 가상 master 데이터베이스에 연결합니다.
2.	선택적 단계: [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) 문을 사용하여 SQL Server 인증 로그인을 만듭니다. 샘플 문:

     ```
     CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
     ```

     > [AZURE.NOTE] 로그인 또는 포함된 데이터베이스 사용자를 만들 때는 강력한 암호를 사용합니다. 자세한 내용은 [강력한 암호](https://msdn.microsoft.com/library/ms161962.aspx)를 참조하십시오.

    성능 향상을 위해 로그인(서버 수준 보안 주체)이 데이터베이스 수준에서 일시적으로 캐시됩니다. 인증 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)를 참조하세요.

3.	가상 master 데이터베이스에서 [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) 문을 사용하여 사용자를 만듭니다. 사용자는 Azure Active Directory 인증 포함된 데이터베이스 사용자(Azure AD 인증에 대한 환경을 구성한 경우)이거나, SQL Server 인증 포함된 데이터베이스 사용자 또는 SQL Server 인증 로그인 기반 SQL Server 인증 사용자(이전 단계에서 만든)일 수 있습니다. 샘플 문:

     ```
     CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
     CREATE USER Tran WITH PASSWORD = '<strong_password>';
     CREATE USER Mary FROM LOGIN Mary; 
     ```

4.	[ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) 문을 사용하여 새 사용자를 **dbmanager** 데이터베이스 역할에 추가합니다. 샘플 문:

     ```
     ALTER ROLE dbmanager ADD MEMBER Mary; 
     ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
     ```

     > [AZURE.NOTE] dbmanager는 가상 master 데이터베이스의 데이터베이스 역할이므로 사용자를 dbmanager 역할에만 추가할 수 있습니다. 서버 수준 로그인을 데이터베이스 수준 역할에 추가할 수 없습니다.

5.	필요한 경우 새 사용자의 연결을 허용하도록 서버 수준 방화벽을 구성합니다.

이제 사용자는 가상 master 데이터베이스에 연결할 수 있으며 새 데이터베이스를 만들 수 있습니다. 데이터베이스를 만드는 계정이 데이터베이스의 소유자가 됩니다.

### 로그인 관리자

원한다면 동일한 단계(로그인 및 사용자 만들기, 사용자를 **loginmanager** 역할에 추가)를 완료하여 사용자가 가상 master에서 새 로그인을 만들 수 있도록 합니다. 일반적으로 Microsoft는 로그인 기반 사용자를 사용하는 대신 데이터베이스 수준에서 인증하는 포함된 데이터베이스 사용자를 사용할 것을 권장하므로 필수는 아닙니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

## 비관리자 사용자

일반적으로 비관리자 계정은 가상 master 데이터베이스에 액세스할 필요가 없습니다. [CREATE USER(Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) 문을 사용하여 데이터베이스 수준에서 포함된 데이터베이스 사용자를 만듭니다. 사용자는 Azure Active Directory 인증 포함된 데이터베이스 사용자(Azure AD 인증에 대한 환경을 구성한 경우)이거나, SQL Server 인증 포함된 데이터베이스 사용자 또는 SQL Server 인증 로그인 기반 SQL Server 인증 사용자(이전 단계에서 만든)일 수 있습니다. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

사용자를 만들고 데이터베이스에 연결하려면 다음 예제와 유사한 문을 실행합니다.

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

처음에는 관리자 또는 데이터베이스 소유자 중 하나만 사용자를 만들 수 있습니다. 새 사용자를 만드는 추가 사용자의 권한을 부여하려면 다음과 같은 문을 사용하여 선택한 사용자에게 `ALTER ANY USER` 권한을 부여합니다.

```
GRANT ALTER ANY USER TO Mary;
```

추가 사용자에게 데이터베이스의 모든 권한을 부여하려면 `ALTER ROLE` 문을 사용하여 **db\_owner** 고정 데이터베이스 역할의 멤버로 만듭니다.

> [AZURE.NOTE] 로그인을 기반으로 데이터베이스 사용자를 만드는 주 원인은 여러 데이터베이스에 액세스해야 하는 SQL Server 인증 사용자가 있는 경우입니다. 로그인 기반 사용자가 로그인을 시도하면 해당 로그인에 대해 하나의 암호만 유지 관리됩니다. 개별 데이터베이스에 포함된 데이터베이스 사용자는 각 개별 엔터티이며 각각 고유한 암호를 유지 관리합니다. 사용자가 동일하게 암호를 유지 관리하지 않으면 포함된 데이터베이스 사용자를 혼동할 수 있습니다.

### 데이터베이스 수준 방화벽 구성

가장 좋은 방법은 비관리자 사용자가 방화벽을 통해서만 사용하는 데이터베이스에 액세스하는 것입니다. 서버 수준 방화벽을 통해 IP 주소를 권한 부여하고 모든 데이터베이스에 대한 액세스를 부여하는 대신 [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) 문을 사용하여 데이터베이스 수준 방화벽을 구성합니다. 포털을 사용해서는 데이터베이스 수준 방화벽을 구성할 수 없습니다.

### 비관리자 액세스 경로

데이터베이스 수준 방화벽이 제대로 구성되었으면 데이터베이스 사용자는 SQL Server Management Studio 또는 SQL Server Data Tools와 같은 클라이언트 도구를 사용하여 연결할 수 있습니다. 최신 도구만 모든 특징 및 기능을 제공합니다. 다음 다이어그램에서는 일반적인 비관리자 액세스 경로를 보여 줍니다. ![비관리자 액세스 경로](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)
 
## 그룹 및 역할
효율적인 액세스 관리에서는 개별 사용자 대신 그룹 및 역할에 할당된 권한을 사용합니다. 예를 들어 Azure Active Directory 인증을 사용하는 경우:

- Azure Active Directory 사용자를 Azure Active Directory 그룹에 넣습니다. 해당 그룹에 대해 포함된 데이터베이스 사용자를 만듭니다. 하나 이상의 데이터베이스 사용자를 데이터베이스 역할에 배치합니다. 그런 다음 사용 권한을 데이터베이스 역할에 할당합니다.

SQL Server 인증을 사용하는 경우:

- 데이터베이스에 포함된 데이터베이스 사용자를 만듭니다. 하나 이상의 데이터베이스 사용자를 데이터베이스 역할에 배치합니다. 그런 다음 사용 권한을 데이터베이스 역할에 할당합니다.

데이터베이스 역할은 **db\_owner**, **db\_ddladmin**, **db\_datawriter**, **db\_datareader**, **db\_denydatawriter**, **db\_denydatareader**와 같은 기본 제공된 역할일 수 있습니다. **db\_owner**는 일반적으로 일부 사용자에게만 전체 권한을 부여하는 데 사용됩니다. 기타 고정된 데이터베이스 역할은 개발에서 단순한 데이터베이스를 신속하게 가져오는 데 유용하지만 대부분의 프로덕션 데이터베이스에는 권장되지 않습니다. 예를 들어 **db\_datareader** 고정된 데이터베이스 역할은 데이터베이스에 있는 모든 테이블에 대한 읽기 액세스 권한을 부여하며 일반적으로 그 이상이 필요합니다. [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) 문을 사용하여 고유의 사용자 정의된 데이터베이스 역할을 만들고 각 역할에 비즈니스 요구에 필요한 최소한의 권한을 신중하게 부여하는 것이 좋습니다. 사용자가 여러 역할의 멤버인 경우 모두에 대한 권한을 집계합니다.

## 권한

SQL 데이터베이스에는 개별적으로 부여하거나 거부할 수 있는 100개가 넘는 사용 권한이 있습니다. 이러한 사용 권한은 대부분 중첩됩니다. 예를 들어 스키마에 대한 `UPDATE` 권한에는 해당 스키마 내에 있는 각 테이블에 대한 `UPDATE` 권한이 포함됩니다. 대부분의 사용 권한 시스템에서와 같이 사용 권한 거부는 권한 부여를 재정의합니다. 중첩된 특성과 사용 권한 수로 인해 데이터베이스를 제대로 보호할 적절한 사용 권한 시스템을 설계하는 데 신중을 기할 수 있습니다. [사용 권한(데이터베이스 엔진)](https://msdn.microsoft.com/library/ms191291.aspx)에서 사용 권한 목록부터 시작하여 사용 권한의 [포스터 크기 그래픽](http://go.microsoft.com/fwlink/?LinkId=229142)을 검토하세요.


## 다음 단계

[SQL 데이터베이스 보안 설정](sql-database-security.md)

[테이블 만들기(자습서)](https://msdn.microsoft.com/library/ms365315.aspx)

[테이블에 데이터 삽입 및 업로드(자습서)](https://msdn.microsoft.com/library/ms365309.aspx)

[테이블에서 데이터 읽기(자습서)](https://msdn.microsoft.com/library/ms365310.aspx)

[뷰 및 저장 프로시저 만들기](https://msdn.microsoft.com/library/ms365311.aspx)

[데이터베이스 개체에 대한 액세스 부여](https://msdn.microsoft.com/library/ms365327.aspx)


## 추가 리소스

[SQL 데이터베이스 보안 설정](sql-database-security.md)

[SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589.aspx)

<!---HONumber=AcomDC_0914_2016-->