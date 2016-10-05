<properties
    pageTitle="C++에서 Blob 저장소(개체 저장소)를 사용하는 방법 | Microsoft Azure"
	description="Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="dineshm;tamram"/>

# C++에서 Blob 저장소를 사용하는 방법  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## 개요

Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C++로 작성되었으며 [Azure Storage Client Library for C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md)를 사용합니다. Blob **업로드**, **나열**, **다운로드** 및 **삭제** 시나리오를 다룹니다.

>[AZURE.NOTE] 이 가이드는 Azure Storage Client Library for C++ 버전 1.0.0 이상을 대상으로 합니다. 권장되는 버전은 Storage Client Library 2.2.0이며, [NuGet](http://www.nuget.org/packages/wastorage) 또는 [GitHub](https://github.com/Azure/azure-storage-cpp)를 통해 사용 가능합니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## C++ 응용 프로그램 만들기
이 가이드에서는 C++ 응용 프로그램 내에서 실행할 수 있는 저장소 기능을 사용합니다.

이 기능을 사용하려면, Azure Storage Client Library for C++를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들어야 합니다.

Azure Storage Client Library for C++를 설치하려면 다음 메서드를 사용할 수 있습니다.

-	**Linux:** [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) 페이지의 지침을 따릅니다.
-	**Windows:** Visual Studio에서 **도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 클릭합니다. [NuGet 패키지 관리자 콘솔](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)에 다음 명령을 입력하고 **ENTER**를 누릅니다.

		Install-Package wastorage

## Blob 저장소에 액세스하도록 응용 프로그램 구성  
Azure 저장소 API를 사용하여 Blob에 액세스하려는 C++ 파일의 맨 위에 다음 include 문을 추가합니다.

	#include "was/storage_account.h"
	#include "was/blob.h"

## Azure 저장소 연결 문자열 설정
Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램에서 실행할 경우, 저장소 계정의 이름 및 [Azure 포털](https://portal.azure.com)에 나열된 저장소 계정의 저장소 액세스 키를 *AccountName*과 *AccountKey* 값에 사용하여 다음 형식의 저장소 연결 문자열을 제공해야 합니다. 저장소 계정 및 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 정보](storage-create-storage-account.md)를 참조하세요. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

로컬 Windows 컴퓨터에서 응용 프로그램을 테스트 하려면 [Azure SDK](https://azure.microsoft.com/downloads/)와 함께 설치된 Microsoft Azure [저장소 에뮬레이터](storage-use-emulator.md)를 사용할 수 있습니다. 저장소 에뮬레이터는 로컬 개발 컴퓨터의Azure에서 사용할 수 있는 Blob, 큐 및 테이블 서비스를 시뮬레이션하는 유틸리티입니다. 다음 예제에서는 로컬 저장소 에뮬레이터에 연결 문자열을 포함할 수 있도록 정적 필드를 선언하는 방법을 보여줍니다.

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Azure 저장소 에뮬레이터를 시작하려면 **시작** 단추를 선택하거나 **Windows** 키를 누릅니다. **Azure 저장소 에뮬레이터** 입력을 시작하고 응용 프로그램 목록에서 **Microsoft Azure 저장소 에뮬레이터**를 선택합니다.

다음 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 메서드 중 하나를 사용한 것으로 가정합니다.

## 연결 문자열 검색
**cloud\_storage\_account** 클래스를 사용하여 저장소 계정 정보를 나타낼 수 있습니다. 저장소 연결 문자열에서 저장소 계정 정보를 검색하려면 **구문 분석** 메서드를 사용할 수 있습니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

다음으로, Blob 저장소 서비스에 저장된 Blob과 컨테이너를 나타내는 개체를 검색할 수 있도록 **cloud\_blob\_client** 클래스에 대한 참조를 가져옵니다. 다음 코드는 위에서 검색한 저장소 계정 개체를 사용하여 **cloud\_blob\_client** 개체를 만듭니다.

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## 방법: 컨테이너 만들기

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

이 예제에서는 컨테이너가 없는 경우 만드는 방법을 보여 줍니다.

	try
	{
		// Retrieve storage account from connection string.
		azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

		// Create the blob client.
		azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

		// Retrieve a reference to a container.
		azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

		// Create the container if it doesn't already exist.
		container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
		std::wcout << U("Error: ") << e.what() << std::endl;
	}  

기본적으로 새 컨테이너는 전용이며, 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일(Blob)을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 해당 액세스 키가 있는 경우에만 수정하거나 삭제할 수 있습니다.

## 방법: 컨테이너에 Blob 업로드
Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **upload\_from\_stream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

또는, **upload\_from\_file** 메서드를 사용하여 블록 Blob에 파일을 업로드할 수 있습니다.

## 방법: 컨테이너에 Blob 나열
컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **list\_blob\_item**의 메서드 및 다양한 속성에 액세스 하기 위해서는 , **cloud\_blob** 개체를 불러오는 **list\_blob\_item.as\_blob**메서드를 호출하거나, cloud\_blob\_directory 개체를 불러오는**list\_blob.as\_directory**메서드를 호출하세요. 다음 코드는 **my-sample-container** 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Output URI of each item.
	azure::storage::list_blob_item_iterator end_of_results;
	for (auto it = container.list_blobs(); it != end_of_results; ++it)
	{
		if (it->is_blob())
		{
			std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
		}
		else
		{
			std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
		}
	}

작업 나열에 대한 자세한 내용은 [C++에서 Azure 저장소 리소스 나열](storage-c-plus-plus-enumeration.md)을 참조하세요.

## 방법: Blob 다운로드
Blob을 다운로드하려면 먼저 Blob 참조를 검색한 다음 **download\_to\_stream** 메서드를 호출합니다. 다음 예제에서는 **download\_to\_stream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();

	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

또는, **download\_to\_file** 메서드를 사용하여 Blob의 콘텐츠를 파일에 다운로드 합니다. 또한, **download\_text** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## 방법: Blob 삭제
Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 **delete\_blob** 메서드를 호출합니다.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## 다음 단계
이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 Azure 저장소 작업에 대해 알아보세요.

-	[C++에서 큐 저장소를 사용하는 방법](storage-c-plus-plus-how-to-use-queues.md)
-	[C++에서 테이블 저장소를 사용하는 방법](storage-c-plus-plus-how-to-use-tables.md)
-	[C++에서 Azure 저장소 리소스 나열](storage-c-plus-plus-enumeration.md)
-	[C++용 Storage Client Library 참조(영문)](http://azure.github.io/azure-storage-cpp)
-	[Azure 저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)
- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)

<!---HONumber=AcomDC_0921_2016-->