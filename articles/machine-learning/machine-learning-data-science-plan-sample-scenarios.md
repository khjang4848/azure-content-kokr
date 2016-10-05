<properties
	pageTitle="Azure 기계 학습의 고급 분석 프로세스 및 기술 시나리오 | Microsoft Azure"
	description="팀 데이터 과학 프로세스에서 고급 예측 분석을 수행하기 위한 적절한 시나리오를 선택합니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016" 
	ms.author="bradsev" />


# Azure 기계 학습의 고급 분석 시나리오

이 문서에서는 [TDSP(팀 데이터 과학 프로세스)](data-science-process-overview.md)로 처리할 수 있는 다양한 샘플 데이터 원본 및 대상 시나리오를 안내합니다. TDSP는 지능형 응용 프로그램 개발을 위해 팀원들이 공동으로 작업하기 위한 체계적인 방법을 제공합니다. 여기에 제시된 시나리오는 Azure에서 데이터 특성, 원본 위치 및 대상 저장소를 기반으로 하는 데이터 처리 워크플로에서 사용 가능한 옵션을 보여 줍니다.

마지막 섹션에서는 데이터 및 목표에 적합한 샘플 시나리오를 선택하는 **의사 결정 트리**를 소개합니다.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


다음 섹션에서는 각각 샘플 시나리오를 제공합니다. 각 시나리오에 대해 가능한 데이터 과학 또는 고급 분석 흐름 및 지원되는 Azure 리소스가 나열되어 있습니다.

>[AZURE.NOTE] **이 모든 시나리오에서 다음을 수행해야 합니다.** <br/>
>* [저장소 계정 만들기](../storage/storage-create-storage-account.md) <br/>
>* [Azure 기계 학습 작업 영역 만들기](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>시나리오 #1: 로컬 파일에서 중간 테이블 형식 데이터 집합보다 작음

![보통 로컬 파일보다 작음][1]

#### 추가 Azure 리소스: 없음

1.  [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

2.  데이터 집합을 업로드합니다.

3.  데이터 집합을 업로드한 Azure 컴퓨터 학습 실험 흐름을 작성합니다.

## <a name="smalllocalprocess"></a>시나리오 #2: 처리를 요구하는 로컬 파일의 보통 데이터 집합보다 작음

![처리 중인 중간 로컬 파일보다 작음][2]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (IPython Notebook 서버)

1.  IPython Notebook을 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  Azure 저장소 컨테이너에 데이터를 업로드 합니다.

3.  IPython Notebook에서 데이터를 사전 처리하고 정리하며, Azure 저장소 컨테이너에서 데이터에 액세스합니다.

4.  데이터를 정리된 테이블 형식으로 변환합니다.

5.  Azure Blob에 변환된 데이터를 저장합니다.

6.  [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

7.  [데이터 가져오기][import-data] 모듈을 사용하여 Azure Blob에서 데이터를 읽습니다.

8. 수집된 데이터 집합으로 시작하여 Azure 컴퓨터 학습 실험 흐름을 작성합니다.

## <a name="largelocal"></a>시나리오 #3: 로컬 파일에서 큰 데이터 집합, Azure Blob을 대상으로 함

![큰 로컬 파일][3]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (IPython Notebook 서버)

1.  IPython Notebook을 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  Azure 저장소 컨테이너에 데이터를 업로드 합니다.

3.  IPython Notebook에서 데이터를 사전 처리하고 정리하며, Azure Blob에서 데이터에 액세스합니다.

4.  필요한 경우, 데이터를 정리된 테이블 형식으로 변환합니다.

5.  데이터를 탐색하고 필요에 따라 기능을 만듭니다.

6.  중소 데이터 샘플을 추출합니다.

7.  Azure Blob에 샘플링된 데이터를 저장합니다.

8. [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

9. [데이터 가져오기][import-data] 모듈을 사용하여 Azure Blob에서 데이터를 읽습니다.

10. 수집된 데이터 집합으로 시작하여 Azure 컴퓨터 학습 실험 흐름을 작성합니다.


## <a name="smalllocaltodb"></a>시나리오 #4: 로컬 파일의 보통 데이터 집합보다 작음, Azure 가상 컴퓨터의 SQL Server를 대상으로 함

![Azure에서 SQL DB보다 중간인 로컬 파일보다 작음][4]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (SQL Server / IPython Notebook 서버)

1.  SQL Server + IPython Notebook을 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  Azure 저장소 컨테이너에 데이터를 업로드 합니다.

3.  IPython Notebook을 사용하여 Azure 저장소 컨테이너에서 데이터를 사전 처리하고 정리합니다.

4.  필요한 경우, 데이터를 정리된 테이블 형식으로 변환합니다.

5.  VM 로컬 파일에 데이터 저장합니다(IPython Notebook이 VM에서 실행되고, 로컬 드라이브가 VM 드라이브를 참조).

6.  Azure VM에서 실행되는 SQL Server 데이터베이스에 데이터를 로드 합니다.

    옵션 #1: SQL Server Management Studio 사용.

    - SQL Server VM에 로그인
    - SQL Server Management Studio를 실행합니다.
    - 데이터베이스 및 대상 테이블을 만듭니다.
    - 대량 가져오기 방법 중 하나를 사용하여 VM-로컬 파일의 데이터를 로드합니다.

    옵션 #2: IPython Notebook 사용 – 중간 및 대규모 데이터 집합을 권장하지 않음
    <!-- -->    
    - ODBC 연결 스트링을 사용하여 VM의 SQL 서버에 액세스합니다.
    - 데이터베이스 및 대상 테이블을 만듭니다.
    - 대량 가져오기 방법 중 하나를 사용하여 VM-로컬 파일의 데이터를 로드합니다.

7.  데이터를 탐색하고 필요에 따라 기능을 만듭니다. 기능을 데이터베이스 테이블에 구체화하지 않아도 됩니다. 필요한 쿼리를 기록하여 만들기만 합니다.

8. 필요하거나 원하는 경우 데이터 샘플 크기를 결정합니다.

9. [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

10. [데이터 가져오기][import-data] 모듈을 사용하여 SQL Server에서 직접 데이터를 읽습니다. [데이터 가져오기][import-data] 쿼리에서 직접 필요한 경우, 필드를 추출하는 데 필요한 쿼리를 붙여넣고, 기능을 만들고 데이터를 샘플링합니다.

11. 수집된 데이터 집합으로 시작하여 Azure 컴퓨터 학습 실험 흐름을 작성합니다.

## <a name="largelocaltodb"></a>시나리오 #5: 로컬 파일의 큰 데이터 집합, Azure VM의 SQL Server를 대상으로 함

![Azure의 SQL DB보다 큰 로컬 파일][5]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (SQL Server / IPython Notebook 서버)

1.  SQL Server 및 IPython Notebook 서버를 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  Azure 저장소 컨테이너에 데이터를 업로드 합니다.

3.  (선택 사항) 데이터를 사전 처리하고 정리합니다.

    a. IPython Notebook에서 데이터를 사전 처리하고 정리하며, Azure Blob에서 데이터에 액세스합니다.

    b. 필요한 경우, 데이터를 정리된 테이블 형식으로 변환합니다.

    c. VM 로컬 파일에 데이터 저장합니다(IPython Notebook이 VM에서 실행되고, 로컬 드라이브가 VM 드라이브를 참조).

4.  Azure VM에서 실행되는 SQL Server 데이터베이스에 데이터를 로드 합니다.

    a. SQL Server VM에 로그인합니다.

    b. 데이터를 저장하지 경우, Azure 저장소 컨테이너에서 로컬 VM 폴더에 데이터 파일을 다운로드합니다.

    c. SQL Server Management Studio를 실행합니다.

    d. 데이터베이스 및 대상 테이블을 만듭니다.

	e. 대량 가져오기 메서드 중 하나를 사용하여 데이터를 로드합니다.

    f. 테이블 조인이 필요한 경우, 인덱스를 만들어 조인을 신속하게 처리합니다.

     > [AZURE.NOTE] 큰 데이터를 좀 더 빨리 로드하기 위해, 분할된 테이블을 만들고 병렬로 대량 데이터를 가져오는 것이 좋습니다. 자세한 내용은 [SQL 분할된 테이블로 병렬로 데이터 가져오기](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)를 참조하세요.

5.  데이터를 탐색하고 필요에 따라 기능을 만듭니다. 기능을 데이터베이스 테이블에 구체화하지 않아도 됩니다. 필요한 쿼리를 기록하여 만들기만 합니다.

6.  필요하거나 원하는 경우 데이터 샘플 크기를 결정합니다.

7.  [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

8. [데이터 가져오기][import-data] 모듈을 사용하여 SQL Server에서 직접 데이터를 읽습니다. [데이터 가져오기][import-data] 쿼리에서 직접 필요한 경우, 필드를 추출하는 데 필요한 쿼리를 붙여넣고, 기능을 만들고 데이터를 샘플링합니다.

9. 업로드 데이터 집합으로 단순 Azure 기계 학습 실험 흐름 시작

## <a name="largedbtodb"></a>시나리오 #6: 온-프레미스의 SQL 서버 데이터베이스의 큰 데이터 집합, Azure 가상 컴퓨터의 SQL Server를 대상으로 함

![Azure의 SQL DB보다 큰 SQL DB 온-프레미스][6]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (SQL Server / IPython Notebook 서버)

1.  SQL Server 및 IPython Notebook 서버를 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  데이터 내보내기 메서드 중 하나를 사용하여 SQL Server에서 덤프 파일로 데이터를 내보냅니다.

    > [AZURE.NOTE] 온-프레미스 데이터베이스에서 모든 데이터를 이동하기로 결정한 경우, (빠른)메서드로 대체하여 전체 데이터베이스를 Azure의 SQL Server 인스턴스로 이동합니다. 데이터를 내보내고, 데이터베이스를 만들고 대상 데이터베이스로 데이터를 로드/가져오기하는 단계를 건너뛰고 대체 메서드를 따릅니다.

3.  Azure 저장소 컨테이너로 덤프 파일을 업로드합니다.

4.  Azure 가상 컴퓨터에서 실행 중인 SQL Server 데이터베이스에 데이터를 로드합니다.

    a. SQL Server VM에 로그인합니다.

    b. Azure 저장소 컨테이너에서 로컬 VM 폴더로 데이터 파일을 다운로드합니다.

    c. SQL Server Management Studio를 실행합니다.

    d. 데이터베이스 및 대상 테이블을 만듭니다.

	e. 대량 가져오기 메서드 중 하나를 사용하여 데이터를 로드합니다.

	f. 테이블 조인이 필요한 경우, 인덱스를 만들어 조인을 신속하게 처리합니다.

    > [AZURE.NOTE] 큰 데이터를 좀 더 빨리 로드하기 위해, 분할된 테이블을 만들고 병렬로 대량 데이터를 가져옵니다. 자세한 내용은 [SQL 분할된 테이블로 병렬로 데이터 가져오기](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)를 참조하세요.

5.  데이터를 탐색하고 필요에 따라 기능을 만듭니다. 기능을 데이터베이스 테이블에 구체화하지 않아도 됩니다. 필요한 쿼리를 기록하여 만들기만 합니다.

6.  필요하거나 원하는 경우 데이터 샘플 크기를 결정합니다.

7.  [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

8. [데이터 가져오기][import-data] 모듈을 사용하여 SQL Server에서 직접 데이터를 읽습니다. [데이터 가져오기][import-data] 쿼리에서 직접 필요한 경우, 필드를 추출하는 데 필요한 쿼리를 붙여넣고, 기능을 만들고 데이터를 샘플링합니다.

9. 업로드 데이터 집합으로 단순 Azure 기계 학습 실험 흐름 시작

### 온-프레미스에서 Azure SQL 데이터베이스로 전체 데이터베이스를 복사하는 대체 메서드

![로컬 DB를 분리하고 Azure의 SQL DB에 첨부][7]

#### 추가 Azure 리소스: Azure 가상 컴퓨터 (SQL Server / IPython Notebook 서버)

SQL Server VM에서 전체 SQL Server 데이터베이스를 복제하려면, 한 위치/서버에서 다른 위치/서버로 데이터베이스를 복사해야 하며, 데이터베이스가 일시적으로 오프라인 상태가 될 수 있다고 가정합니다. SQL Server Management Studio 개체 탐색기 또는 해당하는 TRANSACT-SQL 명령을 사용하여 이 작업을 수행합니다.

1. 원본 위치에서 데이터베이스를 분리합니다. 자세한 내용은 [데이터베이스 분리](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx))를 참조하세요.
2. Windows 탐색기나 Windows 명령 프롬프트 창에서, 분리된 데이터베이스 파일을 복사하여 파일을 Azure의 SQL Server VM의 대상 위치에 로그합니다.
3. 복사된 파일을 대상 SQL Server 인스턴스에 첨부합니다. 자세한 내용은 [데이터베이스 연결](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx))을 참조하세요.

[분리 및 연결을 사용하여 데이터베이스 이동(Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>시나리오 #7: 로컬 파일의 빅 데이터, Azure HDInsight Hadoop 클러스터의 Hive 데이터베이스를 대상으로 함

![로컬 대상 Hive의 빅 데이터][9]

#### 추가 Azure 리소스: Azure HDInsight Hadoop 클러스터 및 Azure Virtual Machine(IPython Notebook 서버)

1.  IPython Notebook 서버를 실행하는 Azure 가상 컴퓨터를 만듭니다.

2.  Azure HDInsight Hadoop 클러스터를 만듭니다.

3.  (선택 사항) 데이터를 사전 처리하고 정리합니다.

    a. IPython Notebook에서 데이터를 사전 처리하고 정리하며, Azure Blob에서 데이터에 액세스합니다.

    b. 필요한 경우, 데이터를 정리된 테이블 형식으로 변환합니다.

    c. VM 로컬 파일에 데이터 저장합니다(IPython Notebook이 VM에서 실행되고, 로컬 드라이브가 VM 드라이브를 참조).

4.  2단계에서 선택한 Hadoop 클러스터의 기본 컨테이너에 데이터를 업로드합니다.

5.  Azure HDInsight Hadoop 클러스터의 Hive 데이터베이스에 데이터를 로드합니다.

    a. Hadoop 클러스터의 헤드 노드에 로그인

    b. Hadoop 명령줄을 엽니다.

    c. Hadoop 명령줄의 명령 `cd %hive_home%\bin`로 Hive 루트 디렉터리를 입력합니다.

    d. Hive 쿼리를 실행하여 데이터베이스 및 테이블을 만들고 Blob 저장소에서 Hive 테이블로 데이터를 로드합니다.

 	> [AZURE.NOTE] 데이터가 큰 경우 사용자는 파티션이 있는 Hive 테이블을 만들 수 있습니다. 그런 다음, 사용자는 헤드 노드의 Hadoop 명령줄에서 `for` 루프를 사용하여 파티션에서 분할된 Hive 테이블로 데이터를 로드합니다.

6.  Hadoop 명령줄에서 데이터를 탐색하고 필요에 따라 기능을 만듭니다. 기능을 데이터베이스 테이블에 구체화하지 않아도 됩니다. 필요한 쿼리를 기록하여 만들기만 합니다.

	a. Hadoop 클러스터의 헤드 노드에 로그인

    b. Hadoop 명령줄을 엽니다.

    c. Hadoop 명령줄의 명령 `cd %hive_home%\bin`로 Hive 루트 디렉터리를 입력합니다.

	d. Hadoop 클러스터의 헤드 노드에 있는 Hadoop 명령줄에서 Hive 쿼리를 실행하여 필요에 따라 데이터를 탐색하고 기능을 만듭니다.

7.  필요하거나 원하는 경우, Azure 컴퓨터 학습 Studio에 맞게 데이터를 샘플링합니다.

8.  [Azure 컴퓨터 학습 Studio](https://studio.azureml.net/)에 로그인합니다.

9. [데이터 가져오기][import-data] 모듈을 사용하여 `Hive Queries`에서 데이터를 직접 읽습니다. [데이터 가져오기][import-data] 쿼리에서 직접 필요한 경우, 필드를 추출하는 데 필요한 쿼리를 붙여넣고, 기능을 만들고 데이터를 샘플링합니다.

10. 업로드 데이터 집합으로 단순 Azure 기계 학습 실험 흐름 시작

## <a name="decisiontree"></a>시나리오 선택 의사 결정 트리
------------------------

다음 다이어그램에는 위에서 설명한 시나리오 및 각 항목별 시나리오를 안내하도록 구성된 고급 분석 프로세스 및 기술이 요약되어 있습니다. 데이터 처리, 탐색, 기능 엔지니어링 및 샘플링은 소스, 중간 및/또는 대상 환경에서 하나 이상의 메서드/환경에서 발생할 수 있으며, 필요에 따라 반복적으로 진행할 수 있습니다. 이 다이어그램은 가능한 모든 흐름을 열거한 것이 아니라 일부만을 보여 줄 뿐입니다.

![샘플 DS 프로세스 연습 시나리오][8]

### 고급 분석 작동 예제

공용 데이터 집합에서 고급 분석 프로세스 및 기술을 사용하는 종단 간 Azure 기계 학습 연습에 대한 자세한 내용은 다음을 참조하세요.


* [실행 중인 팀 데이터 과학 프로세스: SQL Server 사용](machine-learning-data-science-process-sql-walkthrough.md)
* [실행 중인 팀 데이터 과학 프로세스: HDInsight Hadoop 클러스터 사용](machine-learning-data-science-process-hive-walkthrough.md)


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->