<properties
   pageTitle="Distcp를 사용하여 WASB로/에서 데이터 레이크 저장소에 데이터 복사| Microsoft Azure"
   description="Distcp 도구를 사용하여 Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사"
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
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# Distcp를 사용하여 Azure 저장소 Blob과 데이터 레이크 저장소 간에 데이터 복사

> [AZURE.SELECTOR]
- [DistCp 사용](data-lake-store-copy-data-wasb-distcp.md)
- [AdlCopy 사용](data-lake-store-copy-data-azure-storage-blob.md)


Data Lake Store 계정에 액세스할 수 있는 HDInsight 클러스터를 만들었다면 Distcp와 같은 Hadoop 에코시스템 도구를 사용하여 HDInsight 클러스터 저장소(WASB) **간의** 데이터를 Data Lake Store 계정에 복사할 수 있습니다. 이 문서에서는 이 작업을 수행하는 방법에 대한 지침을 제공합니다.

##필수 조건

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- Data Lake Store 계정에 액세스할 수 있는 **Azure HDInsight 클러스터**입니다. [Data Lake Store가 있는 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.

## 비디오로 빠르게 배우시겠습니까?

DistCp를 사용하여 Azure Storage Blob과 Data Lake Store 간에 데이터 복사하는 방법에 대한 [비디오를 보세요](https://mix.office.com/watch/1liuojvdx6sie).

## 원격 데스크톱(Windows 클러스터) 또는 SSH(Linux 클러스터)에서 Distcp 사용

HDInsight 클러스터는 서로 다른 원본에서 HDInsight 클러스터로 데이터를 복사하는 데 사용할 수 있는 Distcp 유틸리티와 함께 제공됩니다. 데이터 레이크 저장소를 추가 저장소로 사용하도록 HDInsight 클러스터를 구성한 경우 Distcp 유틸리티는 기본적으로 데이터 레이크 저장소 계정으로/에서 데이터를 복사할 수 있습니다. 이 섹션에서는 Distcp 유틸리티를 사용하는 방법에 대해 살펴봅니다.

1. Windows 클러스터가 있으면 데이터 레이크 저장소 계정에 액세스할 수 있는 HDInsight 클러스터에 원격으로 연결합니다. 지침은 [RDP를 사용하여 클러스터에 연결](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)을 참조하세요. 클러스터 바탕 화면에서 Hadoop 명령줄을 엽니다.

	Linux 클러스터가 있는 경우 SSH를 사용하여 클러스터에 연결합니다. [Linux 기반 HDInsight 클러스터에 연결](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)을 참조하세요. SSH 프롬프트에서 명령을 실행합니다.

3. Azure 저장소 Blob(WASB)에 액세스할 수 있는지 여부를 확인합니다. 다음 명령을 실행합니다.

		hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

	저장소 Blob의 콘텐츠 목록이 제공되어야 합니다.

4. 마찬가지로 클러스터에서 데이터 레이크 저장소 계정에 액세스할 수 있는지 확인합니다. 다음 명령을 실행합니다.

		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

	데이터 레이크 저장소 계정의 파일/폴더 목록이 제공되어야 합니다.

5. Distcp를 사용하여 WASB에서 데이터 레이크 저장소 계정에 데이터를 복사합니다.

		hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

	WASB에 있는 **/example/data/gutenberg/** 폴더의 콘텐츠가 Data Lake Store 계정의 **/myfolder**에 복사됩니다.

6. 마찬가지로 Distcp를 사용하여 데이터 레이크 저장소 계정에서 WASB에 데이터를 복사합니다.

		hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

	Data Lake Store 계정에 있는 **/myfolder**의 콘텐츠가 WASB의 **/example/data/gutenberg/** 폴더에 복사됩니다.

## 참고 항목

- [Azure 저장소 Blob에서 데이터 레이크 저장소로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Azure HDInsight에 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->