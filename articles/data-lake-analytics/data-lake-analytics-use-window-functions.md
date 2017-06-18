<properties 
   pageTitle="Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용 | Azure" 
   description="U-SQL 창 함수를 사용하는 방법에 대해 알아봅니다. " 
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


# Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용  

창 함수는 2003년에 ISO/ANSI SQL 표준에 도입되었습니다. U-SQL은 ANSI SQL 표준에 의해 정의된 창 함수의 하위 집합을 채택합니다.

창 함수는 *windows*라고 하는 행 집합 내에서 계산을 수행하는데 사용합니다. Windows는 OVER 절에 의해 정의됩니다. 창 함수는 매우 효율적으로 주요 시나리오의 일부를 해결합니다.

이 학습 가이드는 두 가지 샘플 데이터 집합을 사용하여 창 함수를 적용할 수 있는 샘플 시나리오를 안내합니다. 자세한 내용은 [U-SQL 참조](http://go.microsoft.com/fwlink/p/?LinkId=691348)를 참조하세요.

창 함수는 다음과 같이 분류됩니다.

- [보고 집계 함수](#reporting-aggregation-functions)(예: SUM 또는 AVG)
- [순위 함수](#ranking-functions)(예: DENSE\_RANK, ROW\_NUMBER, NTILE, RANK)
- [분석 함수](#analytic-functions)(예: 누적 분포, 백분위수 또는 셀프 조인을 사용하지 않고 동일한 결과 집합에 포함된 이전 행의 데이터에 액세스)

**필수 조건:**

- 다음 두 가지 자습서를 살펴봅니다.

    - [Visual Studio용 Azure 데이터 레이크 도구 사용 시작](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure 데이터 레이크 분석 작업에 U-SQL 사용 시작](data-lake-analytics-u-sql-get-started.md)
- [Visual Studio용 Azure 데이터 레이크 도구 사용 시작](data-lake-analytics-data-lake-tools-get-started.md)의 지침에 따라 데이터 레이크 분석 계정을 만듭니다.
- [Azure 데이터 레이크 분석 작업에 U-SQL 사용 시작](data-lake-analytics-u-sql-get-started.md)의 지침에 따라 Visual Studio U-SQL 프로젝트를 만듭니다.

## 샘플 데이터 집합

이 자습서에는 데이터 집합이 두 개 사용됩니다.

- QueryLog

    QueryLog는 검색 엔진에서 사람들이 검색한 목록을 나타냅니다. 각 쿼리 로그에 포함되는 내용은 다음과 같습니다.
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    QueryLog 행 집합을 구성하기 위해 다음 스크립트를 복사하여 사용자의 U-SQL 프로젝트에 붙여넣습니다.
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    실제로 데이터는 데이터 파일에 저장되는 경우가 많습니다. 다음 코드를 사용하여 탭으로 구분된 파일의 내부에 있는 데이터에 액세스합니다.
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Employees

    Employee 데이터 집합에는 다음 필드가 포함됩니다.
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Employees 행 집합을 구성하기 위해 다음 스크립트를 복사하여 사용자의 U-SQL 프로젝트에 붙여넣습니다.

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    다음 문은 데이터 파일로부터 행 집합을 추출하여 행 집합을 만드는 것을 보여줍니다.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

자습서에서 샘플을 테스트하는 경우 행 집합 정의를 포함해야 합니다. U-SQL에서는 사용되는 행 집합만 정의해야 합니다. 일부 샘플에는 행 집합이 하나만 필요합니다.

결과 행 집합을 데이터 파일에 출력하려면 다음 문을 추가해야 합니다.

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 대부분의 샘플은 결과에 대해 **@result** 변수를 사용합니다.

## 창 함수와 그룹화 비교

기간 이동과 그룹화는 개념적으로 관련되어 있지만 다릅니다. 이러한 관계를 이해하는 것이 유용합니다.

### 집계 및 그룹화 사용

다음 쿼리는 모든 직원의 총 급여를 계산하기 위해 집계를 사용합니다.

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] 테스트 및 출력물 검사에 대한 지침은 [Azure 데이터 레이크 분석 작업에 U-SQL 사용 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.

결과는 단일 열과 단일 행입니다. 전체 테이블에서 가져온 급여의 합계는 $165000입니다.

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] 창 함수를 처음 사용한다면 출력물에 포함된 숫자를 기억하는 것이 유용합니다.

다음 문은 각 부서의 총 급여를 계산하기 위해 GROUP BY 절을 사용합니다.

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

결과는 다음과 같습니다.

|DeptName|SalaryByDept
|--------|------------
|공학|60000
|HR|30000
|경영자|50000
|Marketing|25000

SalaryByDept 열의 합계는 $165000이고 이것은 마지막 스크립트의 금액과 일치합니다.
 
이 두 가지 경우 모두 출력 행의 수가 입력 행보다 적습니다.
 
- GROUP BY를 사용하지 않으면 집계는 모든 행을 하나의 행으로 축소합니다.
- GROUP BY를 사용하면, N이 데이터에 표시되는 명시적인 값의 수를 나타낸다고 할 때 출력 행이 N개이며, 이 경우 출력되는 행은 4개입니다.

###  창 함수 사용

다음 샘플에서 OVER 절은 비어 있습니다. 이것은 "window"에 모든 행이 포함되도록 정의합니다. 이 예에서 SUM은 뒤에 나오는 OVER 절에 적용됩니다.

이 쿼리는 “모든 행의 특정 창에 대한 급여의 합계”로 읽을 수 있습니다.

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

GROUP BY와 달리 입력 행의 수만큼 출력 행이 있습니다.

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


165000(모든 급여의 총계)이라는 값은 각 출력 행에 배치됩니다. 해당 총계는 모든 행의 “창”에서 가져온 것이므로 모든 급여를 포함합니다.

다음 예는 모든 직원, 부서, 부서의 총 급여를 나열하도록 "창"을 구체화하는 방법을 보여줍니다. PARTITION BY는 OVER 절에 추가됩니다.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

결과는 다음과 같습니다.

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|공학|60000
|Sophia|공학|60000
|Liam|공학|60000
|Mason|경영자|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|Marketing|25000
|Ethan|Marketing|25000

마찬가지로, 입력 행의 수와 출력 행의 수가 같습니다. 하지만, 각 행에는 해당 부서에 대한 총 급여가 있습니다.




## 집계 함수 보고

창 함수는 다음 집계 함수도 지원합니다.

- 개수
- 합계
- 최소
- 최대
- 평균
- STDEV
- VAR

구문은 다음과 같습니다:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

참고:

- 기본적으로 COUNT를 제외한 집계 함수는 null 값을 무시합니다.
- 집계 함수를 OVER 절과 함께 지정하면, OVER 절 내에ORDER BY 절이 허용되지 않습니다.

### SUM 사용

다음 예는 부서 별 총 급여를 각 입력 행에 추가합니다.
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

출력은 다음과 같습니다.

|EmpID|EmpName|DeptName|DeptID|Salary|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|공학|100|10000|60000
|2|Sophia|공학|100|20000|60000
|3|Liam|공학|100|30000|60000
|7|Mason|경영자|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|Marketing|400|15000|25000
|9|Ethan|Marketing|400|10000|25000

### COUNT 사용

다음 예는 각 행에 각 부서의 총 직원 수를 표시하는 필드를 추가합니다.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

결과:

|EmpID|EmpName|DeptName|DeptID|Salary|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|공학|100|10000|3
|2|Sophia|공학|100|20000|3
|3|Liam|공학|100|30000|3
|7|Mason|경영자|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|Marketing|400|15000|2
|9|Ethan|Marketing|400|10000|2


### MIN 및 MAX 사용

다음 예는 각 행에 각 부서의 최저 임금을 표시하는 필드를 추가합니다.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

결과:

|EmpID|EmpName|DeptName|DeptID|Salary|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|공학|100|10000|10000
|2|Sophia|공학|100|20000|10000
|3|Liam|공학|100|30000|10000
|7|Mason|경영자|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|Marketing|400|15000|10000
|9|Ethan|Marketing|400|10000|10000

MIN을 MAX와 바꾸고 시도해 봅니다.


## 순위 함수

순위 함수는 PARTITION BY 및 OVER 절로 정의된 각 파티션에 각 행에 대한 순위 값(long)을 반환합니다. 순위의 순서는 OVER 절의 ORDER BY로 제어됩니다.

다음과 같은 순위 함수가 지원됩니다.

- RANK
- DENSE\_RANK
- NTILE
- ROW\_NUMBER

**구문:**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- ORDER BY 절은 순위 함수에서 선택 사항입니다. ORDER BY를 지정하면 순위의 순서를 결정합니다. ORDER BY를 지정하지 않으면 U-SQL이 레코드 읽는 순서를 기반으로 값을 할당합니다. 따라서 행 번호의 값이 비결정적인 상태가 되며, 이 경우 rank 또는 dense rank에 order by 절이 지정되지 않습니다.
- NTILE은 양의 정수를 구하는 식을 필요로 합니다. 이 숫자는 각 파티션을 나누는 그룹의 수를 지정합니다. 이 식별자는 NTILE 순위 함수에만 사용됩니다.

OVER 절에 대한 자세한 내용은 [U-SQL 참조]()를 참조하세요.

ROW\_NUMBER, RANK, DENSE\_RANK 모두 창에 행 번호를 할당합니다. 이 함수들을 따로 다루기 보다는 동일한 입력에 대해 함수들이 어떻게 대응하는지 보는 것이 직관적으로 인식하기 좋습니다.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
OVER 절은 동일합니다. 결과:

|쿼리|Latency:int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banana|300|이미지|1|1|1
|Cherry|300|이미지|2|1|1
|Durian|500|이미지|3|3|2
|Apple|100|웹|1|1|1
|Fig|200|웹|2|2|2
|Papaya|200|웹|3|2|2
|Fig|300|웹|4|4|3
|Cherry|400|웹|5|5|4
|Durian|500|웹|6|6|5

### ROW\_NUMBER

각 창(Vertical, Image 또는 Web)에서 행 번호가 Latency를 기준으로 1씩 증가합니다.

![U-SQL 창 함수 ROW\_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

ROW\_NUMBER()와 달리, RANK()는 창의 ORDER BY 절에 지정된 Latency 값을 고려합니다.

RANK는 Latency에 대한 앞쪽의 두 개 값이 동일하기 때문에 (1,1,3)으로 시작됩니다. Latency 값이 500으로 이동했기 때문에 다음 값은 3입니다. 여기에 대한 요점은 중복된 값이 동일한 순위에 주어졌지만 RANK 번호는 다음 ROW\_NUMBER 값으로 “건너뛴다”는 것입니다. 웹 Vertical의 시퀀스(2,2,4)에서 이러한 패턴이 반복되는 것을 볼 수 있습니다.

![U-SQL 창 함수 RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK는 다음 ROW\_NUMBER로 “건너뛰지” 않는다는 것만 제외하면 RANK와 동일합니다. 대신 시퀀스의 다음 번호로 이동합니다. 샘플에서 (1,1,2) 및 (2,2,3) 시퀀스를 참고합니다.

![U-SQL 창 함수 DENSE\_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### 설명

- ORDER BY가 지정되지 않으면 순위 함수에 순서가 지정되지 않은 채로 행 집합에 적용됩니다. 그러면 순위 함수가 비결정적인 동작으로 적용됩니다.
- 다음 조건이 참이 아니면 ROW\_NUMBER를 사용하여 쿼리에 의해 반환되는 행이 각 실행과 정확히 같은 순서로 정렬된다는 보장이 없습니다.

	- 분할된 열의 값은 고유합니다.
	- ORDER BY 열의 값은 고유합니다.
	- 분할 열과 ORDER BY 열 값의 조합은 고유합니다.

### NTILE

NTILE은 지정된 수의 그룹으로 정렬된 파티션에 행을 배포합니다. 그룹에는 1부터 번호가 지정됩니다.


다음 예는 각 파티션(Vertical)의 행 집합을 쿼리 대기 시간 순으로 4개 그룹으로 분할하고 각 행에 대한 그룹 번호를 반환합니다.

이미지 Vertical에는 행이 3개 있고 따라서 그룹이 3개 있습니다.

웹 Vertical에는 행이 6개 있고, 2개의 추가 행이 앞쪽 2개 그룹에 배포되었습니다. 때문에 그룹 1과 그룹 2에 행이 2개 있고, 그룹 3과 그룹 4에는 1개만 있습니다.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
결과:

|쿼리|대기 시간|Vertical|Quartile
|-----|-----------|--------|-------------
|Banana|300|이미지|1
|Cherry|300|이미지|2
|Durian|500|이미지|3
|Apple|100|웹|1
|Fig|200|웹|1
|Papaya|200|웹|2
|Fig|300|웹|2
|Cherry|400|웹|3
|Durian|500|웹|4

NTILE은 매개 변수 ("numgroups")를 사용합니다. Numgroups는 각 파티션을 나누는 그룹의 수를 지정하는 양의 정수 또는 긴 상수 식입니다.

- 파티션에 포함된 행의 수를 numgroups으로 균등하게 나눌 수 있으면, 각 그룹은 동일한 크기를 갖게 됩니다.
- 파티션에 포함된 행의 수를 numgroups으로 균등하게 나눌 수 없으면, 두 그룹의 크기는 한 멤버 차이로 달라집니다. OVER 절로 지정되는 순서에서 큰 그룹이 작은 그룹 앞에 옵니다.

예:

- 100개의 행은 4개 그룹[ 25, 25, 25, 25 ]으로 나뉩니다.
- 102개의 행은 4개 그룹[ 26, 26, 25, 25 ]으로 나뉩니다.


### RANK, DENSE\_RANK 또는 ROW\_NUMBER를 통한 파티션 당 상위 N개 레코드

많은 사용자가 그룹 당 최대 행 n개를 선택하기를 바랍니다. 기존의 GROUP BY로는 이것이 불가능합니다.

순위 함수 섹션의 시작 부분에 다음 예가 있습니다. 각 파티션에 대해 상위 N개 레코드를 표시하지 않습니다.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

결과:

|쿼리|대기 시간|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banana|300|이미지|1|1|1
|Cherry|300|이미지|1|1|2
|Durian|500|이미지|3|2|3
|Apple|100|웹|1|1|1
|Fig|200|웹|2|2|2
|Papaya|200|웹|2|2|3
|Fig|300|웹|4|3|4
|Cherry|400|웹|5|4|5
|Durian|500|웹|6|5|6

### DENSE RANK를 통한 상위 N개

다음 예는 각 창 파티션에서 행의 연속적인 순위 번호에 간격을 두지 않고 각 그룹에서 상위 3개 레코드를 반환합니다.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

결과:

|쿼리|대기 시간|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banana|300|이미지|1
|Cherry|300|이미지|1
|Durian|500|이미지|2
|Apple|100|웹|1
|Fig|200|웹|2
|Papaya|200|웹|2
|Fig|300|웹|3

### RANK를 통한 상위 N개

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

결과:

|쿼리|대기 시간|Vertical|Rank
|-----|-----------|--------|---------
|Banana|300|이미지|1
|Cherry|300|이미지|1
|Durian|500|이미지|3
|Apple|100|웹|1
|Fig|200|웹|2
|Papaya|200|웹|2


### ROW\_NUMBER를 통한 상위 N개

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

결과:
    
|쿼리|대기 시간|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banana|300|이미지|1
|Cherry|300|이미지|2
|Durian|500|이미지|3
|Apple|100|웹|1
|Fig|200|웹|2
|Papaya|200|웹|3

### 전역적으로 고유한 행 번호 할당

각 행에 전역적으로 고유한 번호를 할당하는 것이 유용합니다. 이 작업은 순위 함수를 사용하면 쉽습니다(리듀서를 사용하는 것보다 효율적입니다).

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## 분석 함수

분석 함수는 창의 값 배포를 이해하는데 사용됩니다. 분석 함수를 사용하는 가장 일반적인 시나리오는 백분위수 계산입니다.

**지원되는 분석 창 함수**

- CUME\_DIST
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST는 값 그룹에 지정된 값의 상대적인 위치를 계산합니다. 동일한 Vertical에 포함된 현재 쿼리와 대기 시간이 같거나 작은 쿼리의 백분율을 계산합니다. R 행에 대해 오름차순 정렬을 가정하고, R의 CUME\_DIST는 R의 값보다 작거나 같은 값을 포함하는 행의 수를 쿼리 결과 집합 또는 파티션에서 계산된 행의 수로 나눈 값입니다. CUME\_DIST는 0 < x <= 1 범위에 속하는 숫자를 반환합니다.

**구문**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

다음 예는 Vertical 내 각 쿼리에 대한 대기 시간 백분위수 계산에 CUME\_DIST 함수를 사용합니다.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

결과:
    
|쿼리|대기 시간|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durian|500|이미지|1
|Banana|300|이미지|0\.666666666666667
|Cherry|300|이미지|0\.666666666666667
|Durian|500|웹|1
|Cherry|400|웹|0\.833333333333333
|Fig|300|웹|0\.666666666666667
|Fig|200|웹|0\.5
|Papaya|200|웹|0\.5
|Apple|100|웹|0\.166666666666667

파티션에 행이 6개 있고 파티션 키는 “Web”(4번째 행 이하)입니다.

- 값이 500보다 작거나 같은 행이 6개 있고, 따라서 CUME\_DIST는 6/6=1과 같습니다.
- 값이 400보다 작거나 같은 행이 5개 있고, 따라서 CUME\_DIST는 5/6=0.83과 같습니다.
- 값이 300보다 작거나 같은 행이 4개 있고, 따라서 CUME\_DIST는 4/6=0.66과 같습니다.
- 값이 200보다 작거나 같은 행이 3개 있고, 따라서 CUME\_DIST는 3/6=0.5와 같습니다. 대기 시간 값이 동일한 행이 둘 있습니다.
- 값이 100보다 작거나 같은 행이 1개 있고, 따라서 CUME\_DIST는 1/6=0.16과 같습니다.


**사용 메모:**

- 동등한 값은 항상 같은 누적 분포 값으로 평가됩니다.
- NULL 값은 최저 가능한 값으로 처리됩니다.
- CUME\_DIST를 계산하려면 ORDER BY 절을 지정해야 합니다.
- CUME\_DIST는 PERCENT\_RANK 함수와 유사합니다.

참고: ORDER BY 절은 SELECT 문 다음에 OUTPUT이 오지 않으면 허용되지 않습니다. OUTPUT 문에 포함된 ORDER BY 절은 결과로 생기는 행 집합의 표시 순서를 결정합니다.


### PERCENT\_RANK

PERCENT\_RANK는 행 그룹 내에서 행의 상대적인 순위를 계산합니다. PERCENT\_RANK는 행 집합 또는 파티션에 포함된 값의 상대적인 위치를 계산하는데 사용됩니다. PERCENT\_RANK에 의해 반환되는 값의 범위는 0보다 크고 1보다 작거나 같습니다. CUME\_DIST와 달리 PERCENT\_RANK의 첫 번째 행은 항상 0입니다.
	
**구문**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**참고 사항**

- 모든 집합의 첫 번째 행에는 PERCENT\_RANK가 0입니다.
- NULL 값은 최저 가능한 값으로 처리됩니다.
- PERCENT\_RANK를 계산하려면 ORDER BY 절을 지정해야 합니다.
- CUME\_DIST는 PERCENT\_RANK 함수와 유사합니다.


다음 예는 Vertical 내 각 쿼리에 대한 대기 시간 백분위수 계산에 PERCENT\_RANK 함수를 사용합니다.

PARTITION BY 절은 결과 집합의 행을 Vertical로 분할하도록 지정됩니다. OVER 절에 포함된 RDER BY 절은 각 파티션에 포함된 행의 순서를 지정합니다.

PERCENT\_RANK 함수에 의해 반환되는 값은 Vertical에 포함된 쿼리의 대기 시간의 순위를 백분율로 나타냅니다.


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

결과:

|쿼리|Latency:int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banana|300|이미지|0
|Cherry|300|이미지|0
|Durian|500|이미지|1
|Apple|100|웹|0
|Fig|200|웹|0\.2
|Papaya|200|웹|0\.2
|Fig|300|웹|0\.6
|Cherry|400|웹|0\.8
|Durian|500|웹|1

### PERCENTILE\_CONT 및 PERCENTILE\_DISC

두 함수는 열 값의 연속적인 또는 비연속적인 분포를 기반으로 백분위수를 계산합니다.

**구문**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal** - 계산할 백분위수입니다. 이 값은 0.0과 1.0 사이여야 합니다.

WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ]) - 백분위수를 정렬하고 계산하는 숫자 값 목록을 나타냅니다. 열 식별자 하나만 허용됩니다. 이 식은 숫자 형식만 계산해야 합니다. 다른 데이터 형식은 허용되지 않습니다. 기본 정렬 순서는 오름차순입니다.

OVER ([ PARTITION BY <identifier>…[n] ] ) – 입력 행 집합을 백분율 함수가 적용되는 파티션 키 마다 나뉘도록 파티션으로 나눕니다. 자세한 내용은 이 문서의 순위 섹션을 참고하십시오. 참고: 데이터 집합의 모든 null 값은 무시됩니다.

**PERCENTILE\_CONT** 열 값의 연속적인 분포를 기반으로 백분위수를 계산합니다. 결과는 보정되며 열에 포함된 어떤 값과도 같지 않을 수 있습니다.

**PERCENTILE\_DISC** 열 값의 비연속적인 분포를 기반으로 백분위수를 계산합니다. 결과는 열의 특정 값과 동일합니다. 다시 말해, PERCENTILE\_DISC는 PERCENTILE\_CONT와 대조적으로, 항상 실제(원래 입력) 값을 반환합니다.

아래 예는 각 Vertical에 포함된 대기 시간에 대한 중간값(백분위수=0.50)을 찾으려고 하는 내용이며, 두 함수의 작동 방식을 볼 수 있습니다.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

결과:

|쿼리|Latency:int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banana|300|이미지|300|300
|Cherry|300|이미지|300|300
|Durian|500|이미지|300|300
|Apple|100|웹|250|200
|Fig|200|웹|250|200
|Papaya|200|웹|250|200
|Fig|300|웹|250|200
|Cherry|400|웹|250|200
|Durian|500|웹|250|200


PERCENTILE\_CONT의 경우, 웹 Vertical에 포함된 쿼리 중에 대기 시간이 250인 쿼리는 없지만, 값을 보정할 수 있기 때문에 웹에 대한 중간값은 250입니다.

PERCENTILE\_DISC는 값을 보정하지 않으며 따라서 웹에 대한 중간값은 200입니다. 이것은 입력 행에서 찾은 실제 값입니다.











## 참고 항목

- [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure 포털을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell을 사용하여 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
- [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure 데이터 레이크 분석 대화형 자습서 사용](data-lake-analytics-use-interactive-tutorials.md)
- [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)
- [Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)
- [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-powershell.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0914_2016-->