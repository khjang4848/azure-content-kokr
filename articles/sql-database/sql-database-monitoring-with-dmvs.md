<properties
   pageTitle="동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링 | Microsoft Azure"
   description="동적 관리 뷰를 사용하여 Microsoft Azure SQL 데이터베이스를 모니터링하여 일반적인 성능 문제를 감지 및 진단하는 방법에 대해 알아봅니다."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# 동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링

Microsoft Azure SQL 데이터베이스를 사용하여 차단되거나 오래 실행된 쿼리, 리소스 병목 현상, 잘못된 쿼리 계획 등에 의해 야기될 수 있는 동적 관리 뷰의 하위 집합에서 성능 문제를 진단할 수 있습니다. 이 항목에서는 동적 관리 뷰를 사용하여 일반적인 성능 문제를 감지하는 방법을 설명합니다.

SQL 데이터베이스는 세 가지 범주의 동적 관리 뷰를 부분적으로 지원합니다.

- 데이터베이스 관련 동적 관리 뷰.
- 실행 관련 동적 관리 뷰.
- 트랜잭션 관련 동적 관리 뷰

동적 관리 뷰에 대한 자세한 내용은 SQL Server 온라인 설명서의 [동적 관리 뷰 및 함수(Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx)를 참조하세요.

## 권한

SQL 데이터베이스에서 동적 관리 뷰를 쿼리하려면 **VIEW DATABASE STATE** 권한이 있어야 합니다. **VIEW DATABASE STATE** 권한은 현재 데이터베이스 내의 모든 개체에 대한 정보를 반환합니다. 특정 데이터베이스 사용자에게 **VIEW DATABASE STATE** 권한을 부여하려면 다음 쿼리를 실행합니다.

```GRANT VIEW DATABASE STATE TO database_user; ```

온-프레미스 SQL 서버의 인스턴스에서 동적 관리 뷰는 서버 상태 정보를 반환합니다. SQL 데이터베이스에서는 현재의 논리 데이터베이스에 관한 정보만 반환합니다.

## 데이터베이스 크기 계산

다음 쿼리는 데이터베이스 크기(MB)를 반환합니다.

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

다음 쿼리는 데이터베이스의 개별 개체 크기(MB)를 반환합니다.

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## 연결 모니터링

[sys.dm\_exec\_connections](https://msdn.microsoft.com/library/ms181509.aspx) 뷰를 사용하여 특정 Azure SQL 데이터베이스 서버에 설정된 연결에 관한 정보 및 각 연결의 세부 정보를 검색할 수 있습니다. 또한 [sys.dm\_exec\_sessions](https://msdn.microsoft.com/library/ms176013.aspx) 뷰는 모든 활성 사용자 연결 및 내부 작업에 대한 정보를 검색할 때 유용합니다. 다음 쿼리는 현재 연결에 대한 정보를 검색합니다.

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] **sys.dm\_exec\_requests** 및 **sys.dm\_exec\_sessions views**를 실행하는 경우, 데이터베이스에 대한 **VIEW DATABASE STATE** 권한을 가지고 있으면 데이터베이스에 대한 모든 실행 세션이 표시되며, 그렇지 않으면 현재 세션만 표시됩니다.

## 쿼리 성능 모니터링

느린 속도로 또는 장시간 실행하는 쿼리는 많은 양의 시스템 리소스를 사용할 수 있습니다. 이 섹션에서는 동적 관리 뷰를 사용하여 몇 가지 일반적인 쿼리 성능 문제를 감지하는 방법을 보여 줍니다. 오래 되었지만 여전히 유익한 문제 해결 참고 자료는 Microsoft Technet의 [SQL Server 2008의 성능 문제해결](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) 문서입니다.

### 최상위 N개 쿼리 찾기

다음 예제는 평균 CPU 시간별로 상위 5개의 쿼리에 대한 정보를 반환합니다. 이 예제는 쿼리 해시에 따라 쿼리를 집계하므로 논리적으로 동일한 쿼리는 해당 누적 리소스 소비량에 따라 그룹화됩니다.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### 차단된 쿼리 모니터링

느린 속도로 또는 장시간 실행하는 쿼리는 과도한 리소스 서비에 기여하고 차단된 쿼리에 따른 결과일 수 있습니다. 차단의 원인으로 부실한 응용 프로그램 디자인, 잘못된 쿼리 계획, 유용한 인덱스 부족 등이 있습니다. sys.dm\_tran\_locks 뷰를 사용하여 Azure SQL 데이터베이스의 현재 잠금 작업에 관한 정보를 가져올 수 있습니다. 예제 코드는 SQL Server 온라인 설명서의 [sys.dm\_tran\_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)를 참조하세요.

### 쿼리 계획 모니터링

또한 비효율적인 쿼리 계획 때문에 CPU 사용량이 증가할 수 있습니다. 다음 예제에서는 [sys.dm\_exec\_query\_stats](https://msdn.microsoft.com/library/ms189741.aspx) 뷰를 사용하여 가장 많은 누적 CPU를 사용하는 쿼리를 확인합니다.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## 참고 항목

[SQL 데이터베이스 소개](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0921_2016-->