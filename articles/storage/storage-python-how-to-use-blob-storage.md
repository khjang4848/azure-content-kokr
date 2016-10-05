<properties
	pageTitle="Python에서 Azure Blob 저장소(개체 저장소)를 사용하는 방법 | Microsoft Azure"
	description="Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
    ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>

# Python에서 Azure Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## 개요

Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

이 문서에서는 Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python으로 작성되었으며 [Microsoft Azure Storage SDK for Python]을 사용합니다. 여기서 다루는 시나리오에는 Blob 업로드, 나열, 다운로드 및 삭제가 포함됩니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 컨테이너 만들기

사용하려는 Blob의 형식을 기반으로 **BlockBlobService**, **AppendBlobService** 또는 **PageBlobService** 개체를 만듭니다. 다음 코드에서는 **BlockBlobService** 개체를 사용합니다. 프로그래밍 방식으로 Azure 블록 Blob 저장소에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

	from azure.storage.blob import BlockBlobService

다음 코드에서는 저장소 계정 이름 및 계정 키를 사용하는 **BlockBlobService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 사용자의 계정 이름 및 키로 바꾸세요.

	block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

다음 코드 예제에서는 **BlockBlobService** 개체를 사용하여 컨테이너가 없는 경우 새로 만들 수 있습니다.

	block_blob_service.create_container('mycontainer')

기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 앞에서 한 것처럼 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 blob을 모든 사용자가 사용할 수 있도록 설정하려는 경우 다음 코드를 사용하여 컨테이너를 만들고 공용 액세스 수준을 전달할 수 있습니다.

	from azure.storage.blob import PublicAccess
	block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

또는 다음 코드를 사용하여 컨테이너를 만든 후 수정할 수 있습니다.

	block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

이렇게 변경한 후에는 인터넷의 모든 사용자가 공용 컨테이너의 Blob을 볼 수 있지만 수정하거나 삭제는 할 수 없습니다.

## 컨테이너에 Blob 업로드

블록 Blob을 만들고 데이터를 업로드하려면 **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** 또는 **create\_blob\_from\_text** 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

**create\_blob\_from\_path**는 지정된 경로에서 파일의 내용을 업로드하고, **create\_blob\_from\_stream**은 이미 열린 파일/스트림에서 내용을 업로드합니다. **create\_blob\_from\_bytes**는 바이트 배열을 업로드하고, **create\_blob\_from\_text**는 지정된 인코딩을 사용하여 지정된 텍스트 값을 업로드합니다(기본값은 UTF-8).

다음 예제에서는 **sunset.png** 파일의 내용을 **myblob** Blob에 업로드합니다.

	from azure.storage.blob import ContentSettings
	block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
				)

## 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **list\\_blobs** 메서드를 사용합니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 컨테이너에 있는 각 Blob의 **이름**을 콘솔에 출력합니다.

	generator = block_blob_service.list_blobs('mycontainer')
	for blob in generator:
		print(blob.name)

## Blob 다운로드

Blob에서 데이터를 다운로드하려면 **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** 또는 **get\_blob\_to\_text**를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 **get\_blob\_to\_path**를 사용하여 **myblob** Blob의 내용을 다운로드 후 **out-sunset.png** 파일에 저장하는 방법을 보여 줍니다.

	block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## Blob 삭제

마지막으로 Blob을 삭제하려면 **delete\_blob**을 호출합니다.

	block_blob_service.delete_blob('mycontainer', 'myblockblob')

## 추가 Blob에 쓰기

추가 Blob은 로깅 등의 추가 작업에 최적화되어 있습니다. 블록 Blob과 마찬가지로 추가 Blob은 블록으로 구성되지만 추가 Blob에 새 블록을 추가할 때 항상 Blob 끝에 추가됩니다. 추가 Blob의 기존 블록을 업데이트하거나 삭제할 수는 없습니다. 블록 Blob과 달리 추가 Blob의 블록 ID는 노출되지 않습니다.

추가 Blob의 각 블록은 최대 4MB까지 다양한 크기일 수 있으며, 추가 Blob 하나에 최대 50,000개의 블록이 포함될 수 있습니다. 따라서 추가 Blob의 최대 크기는 195GB(4MB X 50,000개 블록)보다 약간 더 큽니다.

아래 예제에서는 새 추가 Blob을 만들고 간단한 로깅 작업을 시뮬레이트하여 일부 데이터를 추가합니다.

	from azure.storage.blob import AppendBlobService
	append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

	# The same containers can hold all types of blobs
	append_blob_service.create_container('mycontainer')

	# Append blobs must be created before they are appended to
	append_blob_service.create_blob('mycontainer', 'myappendblob')
	append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

	append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## 다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

- [Python 개발자 센터](/develop/python/)
- [Azure 저장소 서비스 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 저장소 팀 블로그]
- [Microsoft Azure Storage SDK for Python]

[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->