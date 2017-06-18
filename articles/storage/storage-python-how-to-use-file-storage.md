<properties
	pageTitle="Python에서 Azure 파일 저장소를 사용하는 방법 | Microsoft Azure"
	description="Python에서 Azure 파일 저장소를 사용하여 파일을 업로드, 나열, 다운로드 및 삭제하는 방법을 알아봅니다."
	services="storage"
	documentationCenter="python"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="minet;robinsh"/>

# Python에서 Azure 파일 저장소를 사용하는 방법

[AZURE.INCLUDE [저장소-선택기-파일-포함](../../includes/storage-selector-file-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## 개요

이 문서에서는 파일 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python으로 작성되었으며 [Microsoft Azure Storage SDK for Python]을 사용합니다. 여기서 다루는 시나리오에는 파일 업로드, 나열, 다운로드 및 삭제가 포함됩니다.

[AZURE.INCLUDE [저장소-파일-개념-포함](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 공유 만들기

**FileService** 개체를 사용하면 공유, 디렉터리 및 파일 작업을 수행할 수 있습니다. 다음 코드는 **FileService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 위쪽에 다음을 추가합니다.

	from azure.storage.file import FileService

다음 코드는 저장소 계정 이름 및 계정 키를 사용하여 **FileService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 사용자의 계정 이름 및 키로 바꾸세요.

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

다음 코드 예제에서는 **FileService** 개체를 사용하여 공유가 없는 경우 새로 만들 수 있습니다.

	file_service.create_share('myshare')

## 파일을 공유로 업로드

Azure 파일 저장소 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함되어 있습니다. 이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 만들고 데이터를 업로드하려면 **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** 또는 **create\_file\_from\_text** 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

**create\_file\_from\_path**는 지정된 경로에서 파일의 내용을 업로드하고, **create\_file\_from\_stream**은 이미 열린 파일/스트림에서 내용을 업로드합니다. **create\_file\_from\_bytes**는 바이트 배열을 업로드하고, **create\_file\_from\_text**는 지정된 인코딩을 사용하여 지정된 텍스트 값을 업로드합니다(기본값은 UTF-8).

다음 예제에서는 **sunset.png** 파일의 내용을 **myfile** 파일에 업로드합니다.

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## 방법: 디렉터리 만들기

또한 루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure 파일 저장소 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드는 루트 디렉터리 아래에 **sampledir**라는 이름의 하위 디렉터리를 만듭니다.

	file_service.create_directory('myshare', 'sampledir')

## 방법: 공유에 파일 및 디렉터리 나열

공유의 파일 및 디렉터리를 나열하려면 **list\_directories\_and\_files** 메서드를 사용합니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 공유에 있는 각 파일 및 디렉터리의 **이름**을 콘솔에 출력합니다.

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## 파일 다운로드

파일에서 데이터를 다운로드하려면 **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** 또는 **get\_file\_to\_text**를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 **get\_file\_to\_path**를 사용하여 **myfile** 파일의 내용을 다운로드 후 **out-sunset.png** 파일에 저장하는 방법을 보여 줍니다.

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## 파일 삭제

마지막으로 파일을 삭제하려면 **delete\_file**을 호출합니다.

	file_service.delete_file('myshare', None, 'myfile')

## 다음 단계

이제 파일 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

- [Python 개발자 센터](/develop/python/)
- [Azure 저장소 서비스 REST API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure 저장소 팀 블로그]
- [Microsoft Azure Storage SDK for Python]

[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->