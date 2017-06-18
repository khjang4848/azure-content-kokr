<properties
	pageTitle="서버 간, 구독 간, Azure 내부 및 외부로 데이터베이스를 이동합니다."
	description="Azure SQL 데이터베이스에서 데이터 및 데이터베이스를 복사, 이동 및 마이그레이션하는 빠른 단계입니다."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="v-shysun"/>

# 서버 간, 구독 간 및 Azure에서/로 데이터베이스 이동

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##동일한 구독의 다른 서버에 데이터베이스를 이동하려면
- [Azure 포털](https://portal.azure.com)에서 **SQL 데이터베이스**를 클릭하고 목록에서 데이터베이스를 선택한 후 **복사**를 클릭합니다. 자세한 내용은 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요.

## 구독 간에 데이터베이스를 이동하려면
- [Azure 포털](https://portal.azure.com)에서 **SQL Server**를 클릭하고, 목록에서 데이터베이스를 호스팅하는 서버를 선택합니다. **이동**을 클릭한 후 이동할 리소스와 이동 대상인 구독을 선택합니다.

## SQL 데이터베이스를 Azure로 마이그레이션하려면
- 데이터베이스 호환성을 확인한 후 필요에 따라 적합한 마이그레이션 방법을 선택합니다. [SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md)에 대한 지침 및 옵션을 따릅니다.

## Azure 외부에서 사용할 데이터베이스 복사본을 만들려면
- [BACPAC 파일을 내보냅니다.](sql-database-export.md)

<!---HONumber=AcomDC_0914_2016-->