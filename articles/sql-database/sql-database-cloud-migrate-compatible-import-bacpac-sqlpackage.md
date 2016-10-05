<properties
   pageTitle="SqlPackage를 사용하여 BACPAC 파일에서 SQL 데이터베이스로 가져오기"
   description="Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, BACPAC 파일 가져오기, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# SqlPackage를 사용하여 BACPAC 파일에서 SQL 데이터베이스로 가져오기

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure 포털](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

이 문서에서는 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 파일에서 SQL Database로 데이터를 가져오는 방법을 설명합니다. 이 유틸리티는 최신 버전의 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 [Visual Studio용 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)가 함께 제공되며, Microsoft 다운로드 센터에서 직접 최신 버전의 [SqlPackage](https://www.microsoft.com/ko-KR/download/details.aspx?id=53876)를 다운로드할 수 있습니다.


> [AZURE.NOTE] 아래 단계에서는 사용자가 이미 SQL Database 서버를 프로비전했고, 연결 정보를 알고 있으며, 원본 데이터베이스가 호환됨을 확인했다고 가정합니다.

## SqlPackage를 사용하여 BACPAC 파일을 Azure SQL 데이터베이스로 가져오기

아래 단계를 따라 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 명령줄 유틸리티를 사용하여 호환되는 SQL Server 데이터베이스 또는 Azure SQL Database를 BACPAC 파일에서 가져옵니다.

> [AZURE.NOTE] 아래 단계에서는 사용자가 이미 Azure SQL Database 서버를 프로비전했으며 연결 정보를 알고 있다고 가정합니다.

1. 명령 프롬프트를 열고 sqlpackage.exe 명령줄 유틸리티가 들어 있는 디렉터리를 변경합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다.
2. 사용자 환경에 대해 다음 인수를 사용하여 다음 sqlpackage.exe 명령을 실행하세요.

	`sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

	| 인수 | 설명 |
	|---|---|
	| < server\_name > | 대상 서버 이름 |
	| < database\_name > | 대상 데이터베이스 이름 |
	| < user\_name > | 대상 서버의 사용자 이름 |
	| < password > | 사용자 암호 |
	| < source\_file > | 가져올 BACPAC 파일의 파일 이름 및 위치 |

	![작업 메뉴에서 데이터 계층 응용 프로그램 내보내기](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## 다음 단계

- [SSDT 최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server Management Studio 최신 버전](https://msdn.microsoft.com/library/mt238290.aspx)

## 추가 리소스

- [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)
- [Transact-SQL의 부분적으로 지원되거나 지원되지 않는 기능](sql-database-transact-sql-information.md)
- [SQL Server Migration Assistant를 사용하여 SQL Server 이외의 데이터베이스 마이그레이션](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->