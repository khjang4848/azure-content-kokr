<properties
	pageTitle=".NET을 사용하여 Azure Blob 저장소(개체 저장소) 시작 | Microsoft Azure"
	description="Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>


# .NET을 사용하여 Azure Blob 저장소 시작

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## 개요

Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

### 이 자습서 정보

이 자습서에서는 Azure Blob 저장소를 사용하여 몇 가지 일반적인 시나리오에 대한 .NET 코드를 작성하는 방법을 보여 줍니다. Blob 업로드, 나열, 다운로드 및 삭제 시나리오를 다룹니다.

**예상 완료 시간:** 45분

**필수 구성 요소**

- [Microsoft Visual Studio](https://www.visualstudio.com/ko-KR/visual-studio-homepage-vs.aspx)
- [.NET용 Azure 저장소 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [.NET용 Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- [Azure 저장소 계정](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### 추가 샘플

Blob 저장소를 사용하는 추가 예제는 [.NET에서 Azure Blob 저장소 시작](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)을 참조하세요. GitHub에서 샘플 응용 프로그램을 다운로드하고 실행하거나 코드를 탐색할 수 있습니다.


[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### 네임스페이스 선언 추가

`program.cs` 파일 맨 위에 다음 `using` 문을 추가합니다.

	using Microsoft.Azure; // Namespace for CloudConfigurationManager
	using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### 연결 문자열 구문 분석

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### Blob 서비스 클라이언트 만들기

**CloudBlobClient** 클래스를 통해 Blob 저장소에 저장된 컨테이너 및 Blob을 검색할 수 있습니다. 서비스 클라이언트를 만드는 한 가지 방법은 다음과 같습니다.

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

이제 데이터를 읽어 오고 Blob 저장소에 데이터를 기록하는 코드를 작성할 준비가 되었습니다.

## 컨테이너 만들기

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

이 예제에서는 컨테이너가 없는 경우 만드는 방법을 보여 줍니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

기본적으로 새 컨테이너는 전용입니다. 즉, 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있게 하려는 경우 다음 코드를 사용하여 컨테이너를 공용으로 설정할 수 있습니다.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

인터넷상의 누구든지 공용 컨테이너의 Blob을 볼 수 있지만 적절한 계정 선택키 또는 공유 액세스 서명이 있는 경우에만 수정하거나 삭제할 수 있습니다.

## 컨테이너에 Blob 업로드

Azure Blob 저장소는 블록 Blob 및 페이지 Blob을 지원합니다. 대부분의 경우 블록 Blob을 사용하는 것이 좋습니다.

블록 Blob에 파일을 업로드하려면 컨테이너 참조를 가져온 다음 이 참조를 사용하여 블록 Blob 참조를 가져옵니다. Blob 참조가 있는 경우 **UploadFromStream** 메서드를 호출하여 데이터 스트림을 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다.

다음 예제에서는 컨테이너에 Blob을 업로드하는 방법을 보여 주며, 컨테이너가 이미 만들어져 있다고 가정합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 먼저 컨테이너 참조를 가져옵니다. 컨테이너의 **ListBlobs** 메서드를 사용하여 컨테이너 내의 Blob 및/또는 디렉터리를 검색할 수 있습니다. 반환된 **IListBlobItem**에 대한 풍부한 속성 및 메서드 집합에 액세스하려면 **CloudBlockBlob**, **CloudPageBlob** 또는 **CloudBlobDirectory** 개체로 캐스트해야 합니다. 형식을 알 수 없는 경우 형식 검사를 사용하여 캐스트할 형식을 확인할 수 있습니다. 다음 코드는 `photos` 컨테이너에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;

			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

위와 같이 이름에 대한 경로 정보를 사용하여 Blob 이름을 지정할 수 있습니다. 이렇게 하면 기존 파일 시스템과 같이 구성 및 트래버스할 수 있는 가상 디렉터리 구조를 만듭니다. 디렉터리 구조는 가상만 해당됩니다. Blob 저장소에서 사용할 수 있는 리소스만 컨테이너 및 Blob입니다. 하지만 저장소 클라이언트 라이브러리는 참조하도록 가상 디렉터리에 **CloudBlobDirectory** 개체를 제공하며 이러한 방식으로 구성되는 Blob을 사용한 작업 과정을 단순화합니다.

예를 들어 `photos` 컨테이너에 있는 다음 블록 Blob 집합을 고려합니다.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

위 샘플과 같이 'photos' 컨테이너에서 **ListBlobs**를 호출하면 계층적 목록이 반환됩니다. 각각 컨테이너에서 디렉터리 및 Blob을 나타내는 **CloudBlobDirectory** 및 **CloudBlockBlob** 개체를 모두 포함합니다. 결과 출력은 다음과 같습니다.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


선택적으로, **ListBlobs** 메서드의 **UseFlatBlobListing** 매개 변수를 **true**로 설정할 수 있습니다. 이 경우에 컨테이너의 모든 Blob은 **CloudBlockBlob** 개체로 반환됩니다. 플랫 목록을 반환하는 **ListBlobs**에 대한 호출은 다음과 같습니다.

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

결과는 다음과 같습니다.

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Blob 다운로드

Blob을 다운로드하려면 먼저 Blob 참조를 검색한 다음 **DownloadToStream** 메서드를 호출합니다. 다음 예제에서는 **DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 스트림 개체로 전송한 다음 이 개체를 로컬 파일에 저장할 수 있습니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

**DownloadToStream** 메서드를 사용하여 Blob 콘텐츠를 텍스트 문자열로 다운로드할 수도 있습니다.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Blob 삭제

Blob을 삭제하려면 먼저 Blob 참조를 가져온 다음 **Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## 여러 페이지에서 비동기식으로 Blob 나열

많은 수의 Blob을 나열하거나 한 번의 나열 작업에서 반환되는 결과 수를 제어하려는 경우에는 여러 결과 페이지에 Blob을 나열할 수 있습니다. 이 예제에서는 여러 페이지에서 비동기식으로 결과를 반환하는 방법을 보여 주므로 큰 결과 집합이 반환되도록 기다리는 동안 실행이 차단되지 않습니다.

이 예제에서는 플랫 Blob 나열을 보여 주지만 **ListBlobsSegmentedAsync** 메서드의 `useFlatBlobListing` 매개 변수를 `false`(으)로 설정하여 계층적 나열을 수행할 수도 있습니다.

샘플 메서드는 비동기 메서드를 호출하므로 앞에 `async` 키워드를 추가해야 하며 **Task** 개체를 반환해야 합니다. **ListBlobsSegmentedAsync** 메서드에 대해 지정된 await 키워드는 나열 작업이 완료될 때까지 샘플 메서드의 실행을 일시 중단합니다.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 추가 Blob에 쓰기

추가 Blob은 .NET용 Azure 저장소 클라이언트 라이브러리의 버전 5.x에서 도입된 새로운 유형의 Blob입니다. 추가 Blob은 로깅 등의 추가 작업에 최적화되어 있습니다. 블록 Blob과 마찬가지로 추가 Blob은 블록으로 구성되지만 추가 Blob에 새 블록을 추가할 때 항상 Blob 끝에 추가됩니다. 추가 Blob의 기존 블록을 업데이트하거나 삭제할 수는 없습니다. 블록 Blob과 달리 추가 Blob의 블록 ID는 노출되지 않습니다.

추가 Blob의 각 블록은 최대 4MB까지 다양한 크기일 수 있으며, 추가 Blob 하나에 최대 50,000개의 블록이 포함될 수 있습니다. 따라서 추가 Blob의 최대 크기는 195GB(4MB X 50,000개 블록)보다 약간 더 큽니다.

아래 예제에서는 새 추가 Blob을 만들고 간단한 로깅 작업을 시뮬레이트하여 일부 데이터를 추가합니다.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

세 가지 Blob 유형의 차이점에 대한 자세한 내용은 [블록 Blob, 페이지 Blob 및 추가 Blob 이해](https://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

## Blob 보안 관리

기본적으로 Azure 저장소는 데이터 액세스를 계정 액세스 키를 보유한 계정 소유자로 제한하여 데이터를 보호합니다. 저장소 계정의 Blob 데이터를 공유해야 할 경우 계정 액세스 키의 보안을 손상시키지 않고 공유하는 것이 중요합니다. 또한 Blob 데이터를 암호화하여 유선 및 Azure 저장소에서 데이터가 안전하게 이동하게 할 수 있습니다.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### Blob 데이터에 대한 액세스 제어

기본적으로 저장소 계정의 Blob 데이터는 저장소 계정 소유자만 액세스할 수 있습니다. 기본적으로 Blob 저장소에 대한 요청을 인증할 때는 계정 액세스 키가 필요합니다. 그러나 다른 사용자에게 특정 Blob 데이터를 사용 가능하게 제공하고자 할 수 있습니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- **익명 액세스:** 컨테이너나 Blob를 공개 제공하여 익명 액세스를 구현할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.
- **공유 액세스 서명:** 공유 액세스 서명(SAS)을 클라이언트에 제공할 수 있습니다. 여기서는 저장소 계정의 리소스에 대해 사용자가 지정한 권한과 사용자가 지정한 간격에 따라 제한적인 액세스를 제공합니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

### Blob 데이터 암호화

Azure 저장소는 클라이언트와 서버 모두에서 Blob 데이터를 암호화합니다.

- **클라이언트 쪽 암호화:** NET용 Azure 저장소 클라이언트 라이브러리는 Azure 저장소에 업로드하기 전에 클라이언트 응용 프로그램 내부에서 데이터를 암호화하고 클라이언트로 다운로드하는 동안 데이터 암호를 해독하는 기능을 지원합니다. 라이브러리 또한 저장소 계정 키 관리를 위해 Azure 키 자격 증명 모음과의 통합을 지원합니다. 자세한 내용은 [Microsoft Azure 저장소용 .NET을 사용하는 클라이언트 쪽 암호화](storage-client-side-encryption.md)를 참조하세요. 또한 [자습서: Microsoft Azure 저장소에서 Azure 주요 자격 증명 모음을 사용하여 Blob 암호화 및 해독](storage-encrypt-decrypt-blobs-key-vault.md)을 참조하세요.
- **서버 쪽 암호화**: 이제 Azure 저장소에서는 서버 쪽 암호화를 지원합니다. [미사용 데이터에 대한 Azure 저장소 서비스 암호화(미리 보기)](storage-service-encryption.md)를 참조하세요.

## 다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

### Microsoft Azure 저장소 탐색기
- [Microsoft Azure 저장소 탐색기(MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OS X 및 Linux에서 Azure 저장소 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.

### Blob 저장소 샘플

- [.NET에서 Azure Blob 저장소 시작](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### Blob 저장소 참조

- [Storage Client Library for .NET 참조](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST API 참조](http://msdn.microsoft.com/library/azure/dd179355)

### 개념적 지침

- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
- [.NET용 파일 저장소 시작](storage-dotnet-how-to-use-files.md)
- [WebJob SDK를 사용하여 Azure Blob 저장소로 작업하는 방법](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355

<!----HONumber=AcomDC_0921_2016-->