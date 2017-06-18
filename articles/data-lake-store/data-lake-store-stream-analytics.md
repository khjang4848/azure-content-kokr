<properties
   pageTitle="스트림 분석에서 Data Lake 저장소에 데이터 스트리밍 | Azure"
   description="Azure 스트림 분석을 사용하여 Azure Data Lake 저장소에 데이터 스트리밍"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# Azure 스트림 분석을 사용하여 Azure 저장소 Blob에서 Data Lake 저장소에 데이터 스트리밍

이 문서는 Azure 스트림 분석 작업에 대한 출력으로 Azure Data Lake 저장소를 사용하는 방법을 알아봅니다. 이 문서에서는 Azure 저장소 Blob(입력)에서 데이터를 읽고 Data Lake 저장소(출력)에 데이터를 기록하는 간단한 시나리오를 보여줍니다.

>[AZURE.NOTE] 이 때 [Azure 클래식 포털](https://manage.windowsazure.com)에서만 스트림 분석에 대한 Data Lake Store 출력을 만들고 구성할 수 있습니다. 따라서 이 자습서의 일부는 Azure 클래식 포털을 사용합니다.

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.

- **Azure 저장소 계정** 이 계정에서 Blob 컨테이너를 사용하여 스트림 분석 작업에 대한 데이터를 입력합니다. 이 자습서의 경우 **datalakestoreasa**라는 저장소 계정 및 **datalakestoreasacontainer**라는 계정 내의 컨테이너를 만든다고 가정합니다. 컨테이너를 만든 후에 샘플 데이터 파일을 거기에 업로드합니다. [Azure Data Lake Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)에서 예제 데이터 파일을 가져올 수 있습니다. [Azure 저장소 탐색기](http://storageexplorer.com/)와 같은 다양한 클라이언트를 사용하여 Blob 컨테이너에 데이터를 업로드할 수 있습니다.

	>[AZURE.NOTE] Azure 포털에서 계정을 만들면 **클래식** 배포 모델로 만들어야 합니다. 이렇게 하면 스트림 분석 작업을 만드는 데 사용하는 것이기 때문에 Azure 클래식 포털에서 저장소 계정에 액세스할 수 있습니다. 클래식 배포를 사용하여 Azure 포털에서 저장소 계정을 만드는 방법에 대한 자세한 내용은 [Azure 저장소 계정 만들기](../storage/storage-create-storage-account/#create-a-storage-account)를 참조하세요.
	>
	> 또는 Azure 클래식 포털에서 저장소 계정을 만들 수 있습니다.

- **Azure Data Lake 저장소 계정** [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다.


## 스트림 분석 작업 만들기

입력 원본 및 출력 대상을 포함하는 스트림 분석 작업을 만들어 시작합니다. 이 자습서의 경우 원본은 Azure blob 컨테이너이고 대상은 Data Lake 저장소입니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 화면의 왼쪽 아래에서 **새로 만들기**, **데이터 서비스**, **스트림 분석**, **빠른 생성**을 차례로 클릭합니다. 다음과 같이 값을 지정하고 **스트림 분석 작업 만들기**를 클릭합니다.

	![스트림 분석 작업 만들기](./media/data-lake-store-stream-analytics/create.job.png "스트림 분석 작업 만들기")

## 작업에 대한 Blob 입력 만들기

1. 스트림 분석 작업에 대한 페이지를 열고 **입력** 탭을 클릭한 다음 **입력 추가**를 클릭하여 마법사를 시작합니다.

2. **작업에 대한 입력 추가** 페이지에서 **데이터 스트림**을 선택한 다음 정방향 화살표를 클릭합니다.

	![작업에 입력 추가](./media/data-lake-store-stream-analytics/create.input.1.png "작업에 입력 추가")

3. **작업에 대한 데이터 스트림 추가** 페이지에서 **Blob 저장소**를 선택한 다음 정방향 화살표를 클릭합니다.

	![작업에 데이터 스트림 추가](./media/data-lake-store-stream-analytics/create.input.2.png "작업에 데이터 스트림 추가")

4. **Blob 저장소 설정** 페이지에서 입력 데이터 원본으로 사용하는 Blob 저장소에 대한 세부 정보를 제공합니다.

	![Blob 저장소 설정 제공](./media/data-lake-store-stream-analytics/create.input.3.png "Blob 저장소 설정 제공")

	* **입력 별칭을 입력합니다**. 작업 입력에 제공하는 고유한 이름입니다.
	* **저장소 계정을 선택합니다**. 저장소 계정을 스트림 분석 작업과 동일한 지역에 배치하지 않으면 지역 간에 데이터를 이동하는 데 추가 비용이 발생합니다.
	* **저장소 컨테이너를 제공합니다**. 새 컨테이너를 만들거나 기존 컨테이너를 선택하도록 선택할 수 있습니다.

	앞으로 화살표를 클릭합니다.

5. **직렬화 설정** 페이지에서 직렬화 서식을 **CSV**로, 구분 기호를 **탭**으로, 인코딩을 **UTF8**로 설정한 다음 확인 표시를 클릭합니다.

	![직렬화 설정 제공](./media/data-lake-store-stream-analytics/create.input.4.png "직렬화 설정 제공")

6. 마법사를 실행하면 Blob 입력은 **입력** 탭에 추가되고 **진단** 열은 **확인**을 표시해야 합니다. 아래쪽의 **연결 테스트** 단추를 사용하여 입력에 대한 연결을 명시적으로 테스트할 수 있습니다.

## 작업에 대한 Data Lake 저장소 출력 만들기

1. 스트림 분석 작업에 대한 페이지를 열고 **출력** 탭을 클릭한 다음 **출력 추가**를 클릭하여 마법사를 시작합니다.

2. **작업에 대한 출력 추가** 페이지에서 **Data Lake 저장소**를 선택한 다음 정방향 화살표를 클릭합니다.

	![작업에 출력 추가](./media/data-lake-store-stream-analytics/create.output.1.png "작업에 출력 추가")

3. **연결 권한 부여** 페이지에서 Data Lake 저장소 계정을 이미 만든 경우 **지금 권한 부여**를 클릭합니다. 그렇지 않으면 **지금 등록**을 클릭하여 새 계정을 만듭니다. 이 자습서에서는 생성된 Data Lake 저장소 계정이 이미 있다고 가정합니다(필수 구성 요소에서 설명한 대로). Azure 클래식 포털에 로그인하는 자격 증명을 사용하여 자동으로 권한이 부여됩니다.

	![Data Lake 저장소 권한 부여](./media/data-lake-store-stream-analytics/create.output.2.png "Data Lake 저장소 권한 부여")

4. **Data Lake 저장소 설정** 페이지에서 아래 화면 캡처에 표시된 대로 정보를 입력합니다.

	![Data Lake 저장소 설정 지정](./media/data-lake-store-stream-analytics/create.output.3.png "Data Lake 저장소 설정 지정")

	* **출력 별칭을 입력합니다**. 작업 출력에 제공하는 고유한 이름입니다.
	* **Data Lake 저장소 계정을 지정합니다**. 이 필수 구성 요소에서 설명한 대로 만들어야 합니다.
	* **경로 접두사 패턴을 지정합니다**. 스트림 분석 작업에 의해 Data Lake 저장소에 기록되는 출력 파일을 식별해야 합니다. 작업에 의해 작성된 출력의 제목이 GUID 서식이기 때문에 접두사를 포함하면 작성된 출력을 식별하는 데 도움이 됩니다. 날짜 및 시간 스탬프를 접두사의 일부로 포함하려면 접두사 패턴에 `{date}/{time}`를 포함하도록 합니다. 이를 포함하는 경우 **날짜 형식** 및 **시간 형식** 필드를 사용할 수 있으며 원하는 서식을 선택할 수 있습니다.

	앞으로 화살표를 클릭합니다.

5. **직렬화 설정** 페이지에서 직렬화 서식을 **CSV**로, 구분 기호를 **탭**으로, 인코딩을 **UTF8**로 설정한 다음 확인 표시를 클릭합니다.

	![출력 서식 지정](./media/data-lake-store-stream-analytics/create.output.4.png "출력 서식 지정")

6. 마법사를 실행하면 Data Lake Store 출력은 **출력** 탭에 추가되고 **진단** 열은 **확인**을 표시해야 합니다. 아래쪽의 **연결 테스트** 단추를 사용하여 출력에 대한 연결을 명시적으로 테스트할 수 있습니다.

## 스트림 분석 작업 실행

스트림 분석 작업을 실행하려면 쿼리 탭에서 쿼리를 실행해야 합니다. 이 자습서에서는 아래의 화면 캡처와 같이 작업 입력 및 출력 별칭으로 자리 표시자를 대체하여 샘플 쿼리를 실행할 수 있습니다.

![쿼리 실행](./media/data-lake-store-stream-analytics/run.query.png "쿼리 실행")

화면 맨 아래에서 **저장**을 클릭한 다음 **시작**을 클릭합니다. 대화 상자에서 **사용자 지정 시간**을 선택한 다음 **1/1/2016**과 같은 과거의 날짜를 선택합니다. 확인 표시를 클릭하여 작업을 시작합니다. 작업을 시작하는 데 최대 몇 분이 걸릴 수 있습니다.

![작업 시간 설정](./media/data-lake-store-stream-analytics/run.query.2.png "작업 시간 설정")

작업이 시작된 후에 **모니터링** 탭을 클릭하여 데이터를 처리하는 방법을 확인합니다.

![작업 모니터링](./media/data-lake-store-stream-analytics/run.query.3.png "작업 모니터링")

마지막으로 [Azure 포털](https://portal.azure.com)을 사용하여 Data Lake Store 계정을 열고 계정에 데이터가 성공적으로 기록되었는지 여부를 확인할 수 있습니다.

![출력 확인](./media/data-lake-store-stream-analytics/run.query.4.png "출력 확인")

데이터 탐색기 창에서 Data Lake Store 출력 설정에 지정된 대로 출력(`streamanalytics/job/output/{date}/{time}`)이 폴더에 기록됩니다.

## 참고 항목

* [HDInsight 클러스터를 만들어 Data Lake 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->