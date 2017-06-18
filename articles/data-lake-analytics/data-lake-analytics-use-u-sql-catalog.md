<properties
   pageTitle="Azure 데이터 레이크 분석 U-SQL 카탈로그 소개 | Azure"
   description="Azure 데이터 레이크 분석 U-SQL 카탈로그 소개"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# U-SQL 카탈로그 사용

U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다.

각 Azure 데이터 레이크 분석 계정에는 U-SQL 카탈로그가 하나만 연결되어 있습니다. U-SQL 카탈로그는 삭제할 수 없습니다. 현재 데이터 레이크 저장소 계정 간에 U-SQL 카탈로그를 공유할 수 없습니다.

각 U-SQL 카탈로그에는 **Master**라고 하는 데이터베이스가 있습니다. Master 데이터베이스는 삭제할 수 없습니다. 각 U-SQL 카탈로그에는 추가 데이터베이스가 포함될 수 있습니다.

U-SQL 데이터베이스에는 다음이 포함됩니다.

- 어셈블리 – U-SQL 스크립트에서 .NET 코드를 공유합니다.
- 테이블 반환 함수 – U-SQL 스크립트에서 U-SQL 코드를 공유합니다.
- 테이블 – U-SQL 스크립트에서 데이터를 공유합니다.
- 스키마 - U-SQL 스크립트에서 테이블 스키마를 공유합니다.

## 카탈로그 관리
각 Azure 데이터 레이크 분석 계정은 기본 Azure 데이터 레이크 저장소 계정이 연결되어 있습니다. 이 데이터 레이크 저장소 계정을 기본 데이터 레이크 저장소 계정이라고 합니다. U-SQL 카탈로그는 기본 데이터 레이크 저장소 계정의 /catalog 폴더 아래에 저장됩니다. /catalog 폴더에서 어떤 파일도 삭제하지 마세요.

### Azure 포털 사용

[포털을 사용하여 데이터 레이크 분석 관리](data-lake-analytics-use-portal.md#view-u-sql-catalog)를 참조하세요.


### Visual Studio용 데이터 레이크 도구를 사용합니다.

Visual Studio용 데이터 레이크 도구를 사용하여 카탈로그를 관리할 수 있습니다. 도구에 대한 자세한 내용은 [Visual Studio용 데이터 레이크 도구 사용](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.

**카탈로그를 관리하려면**

1. Visual Studio를 열고 Azure에 연결합니다. 지침을 보려면 [Azure에 연결](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure)을 참조하세요.
1. **CTRL+ALT+S**를 눌러 **서버 탐색기**를 엽니다.
2. **서버 탐색기**에서 **Azure**를 확장하고 **데이터 레이크 분석**, 데이터 레이크 분석 계정, **데이터베이스**, **master**를 차례로 확장합니다.



    - 새 데이터베이스를 추가하려면 **데이터베이스**를 마우스 오른쪽 단추로 클릭한 다음 **데이터베이스 만들기**를 클릭합니다.
    - 새 어셈블리를 추가하려면 **어셈블리**를 마우스 오른쪽 단추로 클릭하고 **Register Assembly**(어셈블리 등록)를 클릭합니다.
    - 새 스키마를 추가하려면 **스키마**를 마우스 오른쪽 단추로 클릭한 다음 "스키마** 만들기”를 클릭합니다.
    - 새 테이블을 추가하려면 **테이블**을 마우스 오른쪽 단추로 클릭한 다음 "테이블** 만들기”를 클릭합니다.
    - 새 테이블 반환 함수를 추가하려면 [데이터 레이크 분석 작업을 위한 U-SQL 사용자 정의 연산자 개발](data-lake-analytics-u-sql-develop-user-defined-operators.md)을 참조하세요.


![U-SQL Visual Studio 카탈로그 찾아보기](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## 참고 항목

- 시작
    - [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
    - [Azure PowerShell을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
    - [Azure .NET SDK를 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-net-sdk.md)
    - [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)

- U-SQL 및 개발
    - [Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
    - [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
    - [데이터 레이크 분석 작업을 위한 U-SQL 사용자 정의 연산자 개발](data-lake-Analytics-u-sql-user-defined-operators.md)

- 관리
    - [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-use-portal.md)
    - [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-use-powershell.md)
    - [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- 종단간 자습서
    - [Azure 데이터 레이크 분석 대화형 자습서 사용](data-lake-analytics-use-interactive-tutorials.md)
    - [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=AcomDC_0914_2016-->