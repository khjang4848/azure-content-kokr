<properties
	pageTitle="예를 들어 관리자 암호 다시 설정, 관리 작업을 수행 하는 방법 | Microsoft Azure"
	description="SQL 데이터베이스의 일반 관리 작업을 수행하는 방법을 설명합니다. 예: 관리자 암호 재설정, 액세스 부여 및 제거."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""
	keywords="관리자 암호 재설정"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# Azure SQL 데이터베이스에서 관리자 암호 재설정과 같은 일반 관리 작업을 수행하는 방법
이 항목을 통해 Azure SQL 데이터베이스에 대한 액세스 권한을 신속하게 부여 및 제거하는 방법을 알아보세요. 자세한 내용은 다음 항목을 참조하세요.

- [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)
- [SQL 데이터베이스 보안 설정](sql-database-security.md)
- [SQL Server 데이터베이스 엔진 및 Azure SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 논리 서버에 대한 관리자 암호를 재설정하려면

- [Azure 포털](https://portal.azure.com)에서 **SQL Server**를 클릭하고, 목록에서 서버를 선택한 다음 **암호 재설정**을 클릭합니다.

## 권한이 부여된 IP 주소만 서버에 액세스할 수 있도록 허용하려면
- [방법: SQL 데이터베이스에서 방화벽 설정 구성](sql-database-configure-firewall-settings.md)을 참조하세요.

## 사용자 데이터베이스에 포함된 데이터베이스 사용자를 만들려면
- [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) 문을 사용하세요. 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

## Azure Active Directory를 사용하여 포함된 데이터베이스 사용자를 인증하려면
- [Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결](sql-database-aad-authentication.md)을 참조하세요.

## 가상 마스터 데이터베이스에서 권한이 높은 사용자에 대한 추가 로그인을 만들려면
- [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) 문을 사용하세요. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)의 로그인 관리 섹션을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->