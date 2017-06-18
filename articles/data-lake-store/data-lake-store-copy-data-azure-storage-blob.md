<properties
   pageTitle="Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사 | Microsoft Azure"
   description="AdlCopy 도구를 사용하여 Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사"
   services="data-lake-store"
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
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사

> [AZURE.SELECTOR]
- [DistCp 사용](data-lake-store-copy-data-wasb-distcp.md)
- [AdlCopy 사용](data-lake-store-copy-data-azure-storage-blob.md)

Azure Data Lake Store는 다음 원본에서 데이터를 복사하는 [AdlCopy](http://aka.ms/downloadadlcopy)라는 명령줄 도구를 제공합니다.

* Azure 저장소 Blob에서 Data Lake Store로 데이터 레이크 저장소에서 Azure 저장소 Blob으로 데이터를 복사하는 데 AdlCopy를 사용할 수 없습니다.

* 두 Azure Data Lake Store 계정 간

또한 다음 두 가지 방법으로 AdlCopy 도구를 사용할 수 있습니다.

* **독립 실행형**, 여기서 도구는 데이터 레이크 저장소 리소스를 사용하여 작업을 수행합니다.
* **데이터 레이크 분석 계정 사용**, 여기서 데이터 레이크 분석 계정에 할당된 단위는 복사 작업을 수행하는 데 사용됩니다. 예측 가능한 방식으로 복사 작업을 수행하려는 경우 이 옵션을 사용 수 있습니다.

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- 일부 데이터가 포함된 **Azure 저장소 Blob** 컨테이너
- **Azure 데이터 레이크 분석 계정(선택 사항)** - 데이터 레이크 저장소 계정을 만드는 방법에 대한 지침은 [Azure 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.
- **AdlCopy 도구**. [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy)에서 AdlCopy 도구를 설치합니다.

## AdlCopy 도구 구문

다음 구문에 따라 AdlCopy 도구를 사용합니다.

	AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

이 구문에서 매개 변수는 다음과 같습니다.

| 옵션 | 설명 |
|-----------|------------|
| 원본 | Azure 저장소 Blob에서 원본 데이터의 위치를 지정합니다. 원본은 Blob 컨테이너, Blob 또는 다른 Data Lake Store 계정일 수 있습니다. |
| Dest | 복사할 데이터 레이크 저장소 대상을 지정합니다. |
| SourceKey | Azure 저장소 Blob 원본에 대한 저장소 액세스 키를 지정합니다. 원본이 Blob 컨테이너 또는 Blob인 경우에만 필요합니다. |
| 계정 | **옵션**. 복사 작업을 실행하기 위해 Azure 데이터 레이크 분석 계정을 사용하려는 경우 사용합니다. 구문에서 /Account 옵션을 사용하지만 데이터 레이크 분석 계정을 지정하지 않으면 AdlCopy는 기본 계정을 사용하여 작업을 실행합니다. 또한 이 옵션을 사용하는 경우 원본(Azure 저장소 Blob) 및 대상(Azure 데이터 레이크 저장소)을 데이터 레이크 분석 계정에 대한 데이터 원본으로 추가해야 합니다. |
| Units | 복사 작업에 사용할 데이터 레이크 분석 단위의 수를 지정합니다. 이 옵션은 **/Account** 옵션을 사용하여 데이터 레이크 분석 계정을 지정하는 경우 필수입니다.
| 패턴 | 복사할 Blob 또는 파일을 나타내는 regex 패턴을 지정합니다. AdlCopy는 대/소문자 구분 일치를 사용합니다. 패턴을 지정하지 않았을 때 사용되는 기본 패턴은 모든 항목을 복사하는 것입니다. 여러 파일 패턴을 지정할 수는 없습니다.                                                                                                                                                                                                                                                                                                                                               



## AdlCopy(독립 실행형)를 사용하여 Azure 저장소 Blob에서 데이터 복사

1. 명령 프롬프트를 열고 AdlCopy가 설치된 디렉터리로 이동합니다(일반적으로 `%HOMEPATH%\Documents\adlcopy`).

2. 다음 명령을 실행하여 원본 컨테이너에서 데이터 레이크 저장소로 특정 BLOB를 복사합니다.

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	예:

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	
	>[AZURE.NOTE] 위의 구문은 Data Lake Store 계정에서 폴더에 복사될 파일을 지정합니다. 지정한 폴더 이름이 존재하지 않는 경우 AdlCopy 도구는 폴더를 만듭니다.

	Data Lake Store 계정이 있는 Azure 구독에 대한 자격 증명을 입력하라는 메시지가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

		Initializing Copy.
		Copy Started.
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. 다음 명령을 사용하여 한 컨테이너에서 데이터 레이크 저장소 계정으로 모든 BLOB를 복사할 수 있습니다.

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	예:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## AdlCopy(독립 실행형)를 사용하여 다른 Data Lake Store 계정에서 데이터 복사

AdlCopy를 사용하여 두 Data Lake Store 계정 간에 데이터를 복사할 수도 있습니다.

1. 명령 프롬프트를 열고 AdlCopy가 설치된 디렉터리로 이동합니다(일반적으로 `%HOMEPATH%\Documents\adlcopy`).

2. 하나의 Data Lake Store 계정에서 다른 계정으로 특정 파일을 복사하려면 다음 명령을 실행합니다.

		AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

	예:

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

	>[AZURE.NOTE] 위의 구문은 대상 Data Lake Store 계정에서 폴더에 복사될 파일을 지정합니다. 지정한 폴더 이름이 존재하지 않는 경우 AdlCopy 도구는 폴더를 만듭니다.

	Data Lake Store 계정이 있는 Azure 구독에 대한 자격 증명을 입력하라는 메시지가 표시됩니다. 다음과 유사한 출력이 표시됩니다.

		Initializing Copy.
		Copy Started.|
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. 다음 명령은 원본 Data Lake Store 계정의 특정 폴더에 있는 모든 파일을 대상 Data Lake Store 계정의 폴더로 복사합니다.

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## Data Lake Analytics 계정에 AdlCopy를 사용하여 데이터 복사

또한 데이터 레이크 분석 계정을 사용하여 Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사하는 AdlCopy 작업을 실행할 수 있습니다. 이동할 데이터가 기가바이트 및 테라바이트 범위에 있고 보다 향상되고 예측 가능한 성능 처리량을 원하는 경우 일반적으로 이 옵션을 사용합니다.

AdlCopy와 함께 Data Lake Analytics 계정을 사용하여 Azure 저장소 Blob에서 복사하려면 원본(Azure 저장소 Blob)을 Data Lake Analytics 계정의 데이터 원본으로 추가해야 합니다. 데이터 레이크 분석 계정에 추가 데이터 원본을 추가하는 방법에 대한 지침은 [데이터 레이크 분석 계정 데이터 원본 관리](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources)를 참조하세요.

>[AZURE.NOTE] Data Lake Analytics 계정을 사용하여 Azure Data Lake Store 계정을 원본으로 데이터를 복사하는 경우 Data Lake Store 계정을 Data Lake Analytics 계정에 연결할 필요가 없습니다. 원본 저장소를 Data Lake Analytics 계정에 연결하려는 경우 원본이 Azure 저장소 계정이기만 하면 됩니다.

Data Lake Analytics 계정을 사용하여 Azure 저장소 Blob에서 Data Lake Store 계정으로 복사하려면 다음 명령을 실행합니다.

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

예:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


마찬가지로 Data Lake Analytics 계정을 사용하여 Azure 저장소 Blob에서 Data Lake Store 계정으로 복사하려면 다음 명령을 실행합니다.

	AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## AdlCopy를 사용하여 패턴 일치를 통해 데이터 복사

이 섹션에서는 AdlCopy를 사용하여 패턴 일치를 통해 원본(아래 예제에서는 Azure 저장소 Blob)의 데이터를 대상 Data Lake Store 계정으로 복사하는 방법을 알아봅니다. 예를 들어 아래 단계를 사용하여 원본 Blob에서 확장명이 .csv인 모든 파일을 대상으로 복사할 수 있습니다.

1. 명령 프롬프트를 열고 AdlCopy가 설치된 디렉터리로 이동합니다(일반적으로 `%HOMEPATH%\Documents\adlcopy`).

2. 다음 명령을 실행하여 원본 컨테이너의 특정 Blob 중에서 확장명이 *.csv인 모든 파일을 Data Lake Store로 복사합니다.

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

	예:

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

	
## 결제

* AdlCopy 도구를 독립 실행형으로 사용하는 경우 원본 Azure 저장소 계정이 데이터 레이크 저장소와 동일한 지역에 없는 경우 데이터 이동에 따른 송신 비용이 청구됩니다.

* 데이터 레이크 분석 계정과 함께 AdlCopy 도구를 사용하면 표준 [데이터 레이크 분석 청구 금액](https://azure.microsoft.com/pricing/details/data-lake-analytics/)이 적용 됩니다.

## AdlCopy 사용에 대한 고려 사항

* AdlCopy(버전 1.0.5)는 모두 수천 개가 넘는 파일과 폴더가 있는 원본에서 데이터를 복사하는 작업을 지원합니다. 그러나 대량의 데이터 집합을 복사할 때 문제가 발생할 경우, 파일/폴더를 다른 하위 폴더에 배포하고 해당 하위 폴더에 대한 경로를 원본으로 대신 사용할 수 있습니다.

## 다음 단계

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->