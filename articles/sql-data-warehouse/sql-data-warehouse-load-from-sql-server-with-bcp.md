<properties
   pageTitle="SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(bcp) | Microsoft Azure"
   description="데이터의 크기가 작으면, bcp를 사용하여 SQL Server의 데이터를 플랫 파일로 내보내고 Azure SQL 데이터 웨어하우스로 데이터를 직접 가져옵니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# SQL Server에서 Azure SQL 데이터 웨어하우스로 데이터 로드(플랫 파일)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

소규모 데이터 집합의 경우, bcp 명령줄 유틸리티를 사용하여 SQL Server에서 데이터를 내보낸 다음 Azure SQL 데이터 웨어하우스로 데이터를 직접 로드할 수 있습니다.

이 자습서에서는 bcp를 사용하여:

- bcp out 명령을 사용하여 SQL Server의 테이블을 내보냅니다.(또는 간단한 샘플 파일을 만듭니다.)
- 플랫 파일의 테이블을 SQL 데이터 웨어하우스로 가져옵니다.
- 로드한 데이터에 대한 통계를 만듭니다.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## 시작하기 전에

### 필수 조건

이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

- SQL 데이터 웨어하우스 데이터베이스
- 설치된 bcp 명령줄 유틸리티
- 설치된 sqlcmd 명령줄 유틸리티

[Microsoft 다운로드 센터][]에서 bcp 및 sqlcmd 유틸리티를 다운로드할 수 있습니다.

### ASCII 또는 UTF-16 형식 데이터

사용자의 데이터로 이 자습서를 수행하는 경우에는, bcp가 UTF-8을 지원하지 않으므로, 데이터에 ASCII 또는 UTF-16 인코딩을 사용해야 합니다.

PolyBase는 UTF-8을 지원하지만 아직 UTF-16은 지원하지 않습니다. bcp를 PolyBase와 결합하려면 데이터를 SQL Server에서 내보낸 후 UTF-8로 변환해야 합니다.


## 1\. 대상 테이블 만들기

SQL 데이터 웨어하우스에 로드에 대한 대상 테이블이 될 테이블을 정의합니다. 테이블의 열은 데이터 파일의 각 행에 있는 데이터와 일치해야 합니다.

테이블을 만들려면, 명령 프롬프트를 열고 sqlcmd.exe를 사용하여 다음 명령을 실행합니다.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```


## 2\. 원본 데이터 파일 만들기

메모장을 열고 다음 데이터 줄을 새 텍스트 파일에 복사한 다음 이 파일을 로컬 임시 디렉터리 C:\\Temp\\DimDate2.txt에 저장합니다. 이 데이터는 ASCII 형식입니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(선택 사항) SQL Server 데이터베이스에서 사용자의 데이터를 내보내려면, 명령 프롬프트를 열고 다음 명령을 실행합니다. TableName, ServerName, DatabaseName, Username, 및 Password를 사용자의 정보로 바꿉니다.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## 3\. 데이터 로드
데이터를 로드하려면, 명령 프롬프트를 열고 Server Name, Database name, Username, 및 Password 값을 사용자의 정보로 바꿔서 다음 명령을 실행합니다.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

이 명령을 사용하여 데이터가 제대로 로드되었는지 확인합니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

결과는 다음과 같습니다.

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

## 4\. 통계 만들기

SQL 데이터 웨어하우스는 자동 만들기 또는 통계 자동 업데이트를 아직 지원하지 않습니다. 최고의 쿼리 성능을 얻으려면, 데이터를 처음 로드하거나 데이터 내에 상당한 변화가 생긴 후에, 모든 테이블의 모든 열에서 통계를 만드는 것이 중요합니다. 통계에 대한 자세한 설명은 [통계][]를 참조하세요.

다음 명령을 실행하여 새로 로드한 테이블에 대한 통계를 만듭니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5\. SQL 데이터 웨어하우스에서 데이터 내보내기
재미를 위해 방금 SQL 데이터 웨어하우스에서 로드한 데이터를 내보낼 수 있습니다. 내보내는 명령은 SQL Server에서 내보내는 것과 똑같습니다.

하지만, 결과에는 차이가 있습니다. 데이터가 SQL 데이터 웨어하우스 내의 분산된 위치에 저장되기 때문에, 데이터를 내보내면 각 계산 노드는 데이터를 출력 파일에 기록합니다. 출력 파일에 포함된 데이터 순서는 입력 파일의 데이터 순서와 다를 수 있습니다.

### 테이블을 내보내고 내보낸 결과 비교

내보낸 데이터를 보려면, 명령 프롬프트를 열고 사용자의 매개 변수를 사용하여 이 명령을 실행합니다. ServerName은 Azure 논리적 SQL Server의 이름입니다.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
새 파일을 열어 데이터를 올바르게 내보냈는지 확인할 수 있습니다. 파일에 포함된 데이터는 아래 텍스트와 일치해야 하지만, 다른 순서로 정렬되어 있을 수 있습니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### 쿼리 결과 내보내기

bcp의 **queryout** 함수를 사용하면 전체 테이블을 내보내는 대신 쿼리 결과를 내보낼 수 있습니다.

## 다음 단계
로드 개요는 [SQL 데이터 웨어하우스로 데이터 로드][]를 참조하세요. 더 많은 개발 팁은 [SQL 데이터 웨어하우스 개발 개요][]를 참조하세요. SQL 데이터 웨어하우스에 테이블을 만드는 방법에 대한 내용은 [테이블 개요][] 또는 [CREATE TABLE 구문][]을 참조하세요.

<!--Image references-->

<!--Article references-->

[SQL 데이터 웨어하우스로 데이터 로드]: ./sql-data-warehouse-overview-load.md
[SQL 데이터 웨어하우스 개발 개요]: ./sql-data-warehouse-overview-develop.md
[테이블 개요]: ./sql-data-warehouse-tables-overview.md
[통계]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE 구문]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft 다운로드 센터]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0706_2016-->