<properties 
   pageTitle="Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 문제 해결 | Azure" 
   description="Azure 포털을 사용하여 데이터 레이크 분석 작업의 문제를 해결하는 방법에 대해 알아봅니다. " 
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

# Azure 포털을 사용하여 Azure 데이터 레이크 분석 작업 문제 해결

Azure 포털을 사용하여 데이터 레이크 분석 작업의 문제를 해결하는 방법에 대해 알아봅니다.

이 자습서에서는 누락된 원본 파일 문제를 설치하고 Azure 포털을 사용하여 문제를 해결합니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **데이터 레이크 분석 작업 프로세스에 대한 기본 지식**. [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
- **데이터 레이크 분석 계정**. [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md#create-adl-analytics-account)을 참조하세요.
- **기본 데이터 레이크 저장소 계정에 샘플 데이터 복사**. [원본 데이터 준비](data-lake-analytics-get-started-portal.md#prepare-source-data)를 참조하세요.

##데이터 레이크 분석 작업 제출

이제 잘못된 원본 파일 이름을 사용하여 U-SQL 작업 이름을 만듭니다.

**작업을 제출하려면**

1. Azure 포털의 왼쪽 위 모서리에서 **Microsoft Azure**를 클릭합니다.
2. 데이터 레이크 분석 계정 이름을 가진 타일을 클릭합니다. 계정을 만들었을 때 여기에 고정되었습니다. 계정이 여기에 고정되어 있지 않은 경우 [포털에서 분석 계정 열기](data-lake-analytics-manage-use-portal.md#access-adla-account)를 참조하세요.
3. 위쪽 메뉴에서 **새 작업**을 클릭합니다.
4. 작업 이름을 입력하고 다음 U-SQL 스크립트를 입력합니다.

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    스크립트에 정의된 원본 파일은 **/Samples/Data/SearchLog.tsv1**이고 여기서는 **/Samples/Data/SearchLog.tsv**입니다.
     
5. 위쪽에서 **작업 제출**을 클릭합니다. 새 작업 세부 정보 창이 열립니다. 제목 표시줄에 작업 상태가 표시됩니다. 완료하는 데 몇 분이 걸립니다. 최신 상태를 가져오려면 **새로 고침**을 클릭하면 됩니다.
6. 작업 상태가 **실패**로 바뀔 때까지 기다립니다. 작업이 **성공**인 경우 이는 /Samples 폴더를 제거하지 않았기 때문입니다. 이 자습서의 앞부분에서 **필수 조건** 섹션을 참조하세요.

작업이 작은데 오래 걸리는 이유가 궁금할 수 있습니다. 데이터 레이크 분석은 빅 데이터를 처리하기 위해 설계되었다는 점을 기억하세요. 이 기능은 해당 분산 시스템을 사용하여 많은 양의 데이터를 처리할 때 그 빛을 발합니다.

작업을 제출했다고 가정하고 포털을 닫습니다. 다음 섹션에서는 작업의 문제를 해결하는 방법에 대해 알아봅니다.


## 작업 문제 해결

마지막 섹션에서 작업을 제출했고 작업이 실패했습니다.

**모든 작업을 보려면**

1. Azure 포털의 왼쪽 위 모서리에서 **Microsoft Azure**를 클릭합니다.
2. 데이터 레이크 분석 계정 이름을 가진 타일을 클릭합니다. **작업 관리** 타일에 작업 요약이 표시됩니다.

    ![Azure 데이터 레이크 분석 작업 관리](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    작업 관리에서 작업 상태를 한 눈에 볼 수 있습니다. 실패한 작업이 있는지 확인합니다.
   
3. 해당 작업을 보려면 **작업 관리** 타일을 클릭합니다. 작업은 **실행 중**, **대기**, **종료됨**으로 범주화되어 있습니다. **종료됨** 섹션에 실패한 작업이 표시됩니다. 목록에서 먼저 나옵니다. 작업이 많은 경우 **필터**를 클릭하여 작업을 찾을 수 있습니다.

    ![Azure 데이터 레이크 분석 필터 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. 새 블레이드에서 작업 세부 정보를 열려면 목록에서 실패한 작업을 클릭합니다.

    ![Azure 데이터 레이크 분석 실패 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    **다시 제출** 단추를 확인합니다. 문제를 해결한 후 해당 작업을 다시 제출할 수 있습니다.

5. 이전 스크린샷에서 강조 표시된 부분을 클릭하고 오류 세부 정보를 엽니다. 다음과 같이 표시됩니다.

    ![Azure 데이터 레이크 분석 실패 작업 세부 정보](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    원본 폴더를 찾을 수 없다고 나옵니다.
    
6. **Duplicate Script**(스크립트 복제)를 클릭합니다.
7. **FROM** 경로를 다음으로 업데이트합니다.

    "/Samples/Data/SearchLog.tsv"

8. **작업 제출**을 클릭합니다.


##참고 항목

- [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
- [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
- [Visual Studio를 사용하여 Azure 데이터 레이크 분석 및 U-SQL 시작](data-lake-analytics-u-sql-get-started.md)
- [Azure 포털을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->