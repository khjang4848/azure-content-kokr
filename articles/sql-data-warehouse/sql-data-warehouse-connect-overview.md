<properties
   pageTitle="Azure SQL 데이터 웨어하우스에 연결 | Microsoft Azure"
   description="Azure SQL Data Warehouse의 서버 이름 및 연결 문자열을 찾는 방법"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Azure SQL 데이터 웨어하우스에 연결

이 문서를 사용하면 처음으로 SQL Data Warehouse에 연결할 수 있습니다.

## 서버 이름 찾기

SQL Data Warehouse에 연결하는 첫 번째 단계로 서버 이름을 찾는 방법을 알아야 합니다. 예를 들어, 다음 예제에서 서버 이름은 sample.database.windows.net입니다. 정규화된 서버 이름을 찾으려면:

1. [Azure 포털][]로 이동합니다.
2. **SQL Database**를 클릭합니다.
3. 연결하려는 데이터베이스를 클릭합니다.
4. 전체 서버 이름을 찾습니다.

    ![전체 서버 이름][1]

## 지원되는 드라이버 및 연결 문자열

Azure SQL Data Warehouse는 [ADO.NET][], [ODBC][], [PHP][] 및 [JDBC][]를 지원합니다. 최신 버전 및 설명서를 찾으려면 이전 드라이버 중 하나를 클릭합니다. Azure Portal에서 사용 중인 드라이버에 대한 연결 문자열을 자동으로 생성하려면 앞의 예제에서 **데이터베이스 연결 문자열 표시**를 클릭할 수 있습니다. 또한 각 드라이버에 대한 연결 문자열의 모양에 대한 몇 가지 예는 다음과 같습니다.

> [AZURE.NOTE] 사용 불가능한 짧은 시간 동안 연결이 효력을 유지하려면 연결 제한 시간을 300초로 설정하는 것이 좋습니다.

### ADO.NET 연결 문자열 예제

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### ODBC 연결 문자열 예제

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### PHP 연결 문자열 예제

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### JDBC 연결 문자열 예제

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## 연결 설정

SQL Data Warehouse는 연결 및 개체 생성 중에 몇 가지 설정을 표준화합니다. 이러한 설정은 재정의되거나 다음을 포함할 수 없습니다.

| 데이터베이스 설정 | 값 |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | 켜기 |
| [QUOTED\_IDENTIFIERS][] | 켜기 |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## 다음 단계

Visual Studio를 사용하여 연결하고 쿼리하려면 [Visual Studio를 사용하여 쿼리][]를 참조하세요. 인증 옵션에 대한 자세한 내용은 [Azure SQL Data Warehouse에 인증][]을 참조하세요.

<!--Articles-->
[Visual Studio를 사용하여 쿼리]: ./sql-data-warehouse-query-visual-studio.md
[Azure SQL Data Warehouse에 인증]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure 포털]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->