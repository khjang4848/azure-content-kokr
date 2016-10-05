<properties
	pageTitle="Python을 사용하여 Azure Blob 저장소의 데이터 이동 | Microsoft Azure"
	description="Python을 사용하여 Azure Blob 저장소의 데이터 이동"
	services="machine-learning,storage"
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
	ms.date="09/14/2016"
	ms.author="bradsev" />

# Python을 사용하여 Azure Blob 저장소의 데이터 이동

이 항목에서는 Python API를 사용하여 blob를 나열, 업로드 및 다운로드하는 방법을 설명합니다. Azure SDK에 제공되는 Python API를 사용하여 다음을 수행할 수 있습니다.

- 컨테이너 만들기
- 컨테이너에 Blob 업로드
- Blob 다운로드
- 컨테이너의 Blob 나열
- Blob 삭제

Python API 사용에 대한 자세한 내용은 [Python에서 Blob 저장소 서비스를 사용하는 방법](../storage/storage-python-how-to-use-blob-storage.md)을 참조하세요.

Azure Blob 저장소로 및/또는 저장소에서 데이터를 이동하는 데 사용되는 기술 지침은 여기에 연결되어 있습니다.

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] [Azure의 데이터 과학 가상 컴퓨터](machine-learning-data-science-virtual-machines.md)에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 AzCopy가 VM에 이미 설치되어 있습니다.

> [AZURE.NOTE] Azure Blob 저장소에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../storage/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.


## 필수 조건

이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

- Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.


## Blob에 데이터 업로드

프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 코드의 맨 위쪽에 다음 코드 조각을 추가합니다.

	from azure.storage.blob import BlobService

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 다음 코드에서는 저장소 계정 이름 및 계정 키를 사용하는 BlobService 개체를 만듭니다. 계정 이름 및 계정 키를 실제 계정 및 키로 바꾸세요.

	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

다음 메서드를 사용하여 blob에 데이터를 업로드합니다.

1. put\_block\_blob\_from\_path(지정된 경로의 파일 콘텐츠 업로드)
2. put\_block\_blob\_from\_file(이미 열려 있는 파일/스트림의 콘텐츠 업로드)
3. put\_block\_blob\_from\_bytes(바이트 배열 업로드)
4. put\_block\_blob\_from\_text(지정된 인코딩을 사용하여 지정된 텍스트 값 업로드)

다음은 로컬 파일을 컨테이너에 업로드하는 샘플 코드입니다.

	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

다음은 로컬 디렉터리의 모든 파일(디렉터리 제외)을 blob 저장소에 업로드하는 샘플 코드입니다.

	from azure.storage.blob import BlobService
	from os import listdir
	from os.path import isfile, join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name


## Blob에서 데이터 다운로드

다음 메서드를 사용하여 blob에서 데이터를 다운로드합니다.
1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 메서드입니다.

다음은 컨테이너에 있는 blob의 콘텐츠를 로컬 파일에 다운로드하는 샘플 코드입니다.

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

다음은 컨테이너의 모든 blob를 다운로드하는 샘플 코드입니다. 이 샘플 코드는 list\_blobs를 사용하여 컨테이너의 사용 가능한 blob 목록을 가져오고 로컬 디렉터리에 다운로드합니다.

	from azure.storage.blob import BlobService
	from os.path import join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!---HONumber=AcomDC_0921_2016-->