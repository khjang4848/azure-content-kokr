<properties
   pageTitle="Azure Data Catalog에 Data Lake 저장소의 데이터 등록 | Azure"
   description="Azure Data Catalog에 Data Lake 저장소의 데이터 등록"
   services="data-lake-store,data-catalog" 
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
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Azure Data Catalog에 Data Lake 저장소의 데이터 등록

이 문서에서는 데이터 카탈로그와 데이터를 통합하여 조직 내에서 데이터를 검색할 수 있도록 만들기 위해 Azure Data Lake 저장소와 Azure Data Catalog를 통합하는 방법을 알아봅니다. 데이터 카탈로그를 만드는 방법에 대한 자세한 내용은 [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md)를 참조하세요. 데이터 카탈로그를 사용할 수 있는 시나리오를 이해하려면 [Azure Data Catalog 일반적인 시나리오](../data-catalog/data-catalog-common-scenarios.md)를 참조하세요.

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.

- **Azure Data Lake 저장소 계정** [Azure 포털을 사용하여 Azure 데이터 레이크 저장소 시작](data-lake-store-get-started-portal.md)의 지침을 따릅니다. 이 자습서에서는 **datacatalogstore**라는 Data Lake 저장소 계정을 만들겠습니다.

	계정을 만든 후에 그 계정에 샘플 데이터 집합을 업로드합니다. 이 자습서에서는, [Azure Data Lake Git 리포지토리](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/)의 **AmbulanceData** 폴더에 있는 모든 .csv 파일을 업로드하겠습니다. [Azure 저장소 탐색기](http://storageexplorer.com/)와 같은 다양한 클라이언트를 사용하여 Blob 컨테이너에 데이터를 업로드할 수 있습니다.

- **Azure Data Catalog**. 조직용 Azure Data Catalog가 이미 생성되어 있어야 합니다. 각 조직에는 카탈로그가 하나만 허용됩니다.

## Data Lake 저장소를 데이터 카탈로그에 대한 원본으로 등록

>[AZURE.VIDEO adcwithadl] 

1. `https://azure.microsoft.com/services/data-catalog`로 이동하여 **시작**을 클릭합니다.

2. Azure Data Catalog 포털에 로그인하고 **데이터 게시**를 클릭합니다.

	![데이터 원본 등록](./media/data-lake-store-with-data-catalog/register-data-source.png "데이터 원본 등록")

3. 다음 페이지에서 **응용 프로그램 시작**을 클릭합니다. 컴퓨터에 응용 프로그램 매니페스트 파일이 다운로드됩니다. 매니페스트 파일을 두 번 클릭하여 응용 프로그램을 시작합니다.

4. 시작 페이지에서 **로그인**을 클릭하고 자격 증명을 입력합니다.

	![시작 화면](./media/data-lake-store-with-data-catalog/welcome.screen.png "시작 화면")

5. 데이터 원본 선택 페이지에서 **Azure Data Lake**를 선택하고 **다음**을 클릭합니다.

	![데이터 원본 선택](./media/data-lake-store-with-data-catalog/select-source.png "데이터 원본 선택")

6. 다음 페이지에서, 데이터 카탈로그에 등록할 Data Lake 저장소 계정 이름을 제공합니다. 다른 옵션은 기본값으로 두고 **연결**을 클릭합니다.

	![데이터 원본에 연결](./media/data-lake-store-with-data-catalog/connect-to-source.png "데이터 원본에 연결")

7. 다음 페이지는 다음과 같은 세그먼트로 나눌 수 있습니다.

	a. **서버 계층 구조** 상자는 Data Lake 저장소 계정 폴더 구조를 나타냅니다. **$Root**는 Data Lake 저장소 계정 루트를, **AmbulanceData**는 Data Lake 저장소 계정의 루트에 생성된 폴더를 나타냅니다.

	b. **사용 가능한 개체** 상자에 **AmbulanceData** 폴더의 하위 파일과 폴더가 나열됩니다.

	c. **등록할 개체** 상자에는 Azure Data Catalog에 등록할 파일과 폴더가 나열됩니다.

	![데이터 구조 보기](./media/data-lake-store-with-data-catalog/view-data-structure.png "데이터 구조 보기")

8. 이 자습서에서는, 디렉터리의 모든 파일을 등록합니다. 이를 위해, (![개체 이동](./media/data-lake-store-with-data-catalog/move-objects.png "개체 이동")) 단추를 클릭하여 모든 파일을 **등록할 개체** 상자로 이동합니다.

	데이터를 조직 전체의 데이터 카탈로그에 등록하는 것이므로, 나중에 데이터를 신속하게 찾는 데 사용할 수 있는 메타데이터를 추가하는 것이 좋습니다. 예를 들어, 데이터 소유자(예: 데이터를 업로드한 사람)의 전자 메일 주소를 추가하거나 데이터 식별을 위한 태그를 추가할 수 있습니다. 아래 스크린 캡처에서 데이터에 추가하는 태그를 볼 수 있습니다.

	![데이터 구조 보기](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "데이터 구조 보기")

	**Register**를 클릭합니다.

8. 다음 화면 캡처는 데이터가 데이터 카탈로그에 성공적으로 등록된 것을 나타냅니다.

	![등록 완료](./media/data-lake-store-with-data-catalog/registration-complete.png "데이터 구조 보기")

9. **포털 보기**를 클릭하고 데이터 카탈로그로 돌아가서, 이제 포털에서 등록된 데이터를 액세스할 수 있는지 확인합니다. 데이터 검색을 위해, 데이터를 등록할 때 사용한 태그를 사용할 수 있습니다.

	![카탈로그에서 데이터 검색](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "카탈로그에서 데이터 검색")

10. 이제 데이터에 주석 및 설명서를 추가하는 등의 작업을 수행할 수 있습니다. 자세한 내용은 다음 링크를 참조하세요.
	* [데이터 카탈로그에서 데이터 원본에 주석 추가](../data-catalog/data-catalog-how-to-annotate.md)
	* [데이터 카탈로그에서 데이터 원본 문서화](../data-catalog/data-catalog-how-to-documentation.md)

## 참고 항목

* [데이터 카탈로그에서 데이터 원본에 주석 추가](../data-catalog/data-catalog-how-to-annotate.md)
* [데이터 카탈로그에서 데이터 원본 문서화](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake 저장소를 다른 Azure 서비스와 통합](data-lake-store-integrate-with-other-services.md)

<!---HONumber=AcomDC_0914_2016-->