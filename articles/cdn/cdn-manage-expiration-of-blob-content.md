<properties
 pageTitle="Azure CDN에서 Azure Storage Blob 콘텐츠의 만료 관리 | Microsoft Azure"
 description="Azure CDN 캐싱의 Blob에 대한 TTL(Time-To-Live)을 제어하기 위한 옵션에 대해 알아봅니다."
 services="cdn"
 documentationCenter=""
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="multiple"
 ms.topic="article"
 ms.date="09/15/2016"
 ms.author="casoper"/>


# Azure CDN에서 Azure Storage Blob 콘텐츠의 만료 관리

> [AZURE.SELECTOR]
- [Azure Web Apps/Cloud Services, ASP.NET 또는 IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Azure Storage Blob service](cdn-manage-expiration-of-blob-content.md)

[Azure Storage](../storage/storage-introduction.md)에서 [Blob 서비스](../storage/storage-introduction.md#blob-storage)는 Azure CDN과 통합된 여러 Azure 기반 원본 중 하나입니다. TTL(time-to-live)이 경과할 때까지 공개적으로 액세스 가능한 모든 Blob 콘텐츠는 Azure CDN에 캐시될 수 있습니다. TTL은 Azure Storage의 HTTP 응답에 있는 [*캐시 제어* 헤더](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)에 의해 결정됩니다.

>[AZURE.TIP] Blob에 TTL을 설정하지 않을 수 있습니다. 이 경우에 Azure CDN은 기본 TTL인 7일을 자동으로 적용합니다.
>
>Blob 및 다른 파일에 대한 액세스 속도를 가속하기 위해 Azure CDN이 작동하는 방법에 대한 자세한 내용은 [Azure CDN 개요](./cdn-overview.md)를 참조하세요.
>
>Azure Storage Blob 서비스에 대한 자세한 내용은 [Blob 서비스 개념](https://msdn.microsoft.com/library/dd179376.aspx)을 참조하세요.

이 자습서에서는 Azure Storage에서 Blob에 TTL을 설정할 수 있는 여러 가지 방법을 보여 줍니다.

## Azure PowerShell

[Azure PowerShell](../powershell-install-configure.md)은 Azure 서비스를 관리하는 가장 강력하고 빠른 방법 중 하나입니다. `Get-AzureStorageBlob` cmdlet을 사용하여 Blob에 대한 참조를 가져온 다음 `.ICloudBlob.Properties.CacheControl` 속성을 설정합니다.

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "public, max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

>[AZURE.TIP] PowerShell을 사용하여 [CDN 프로필 및 끝점을 관리](./cdn-manage-powershell.md)할 수도 있습니다.

## .NET용 Azure 저장소 클라이언트 라이브러리

.NET을 사용하여 Blob의 TTL을 설정하려면 [.NET용 Azure Storage 클라이언트 라이브러리](../storage/storage-dotnet-how-to-use-blobs.md)를 사용하여 [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 속성을 설정합니다.

```csharp
class Program
{
	const string connectionString = "<storage connection string>";
	static void Main()
	{
		// Retrieve storage account information from connection string
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
		
		// Create a blob client for interacting with the blob service.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		
		// Create a reference to the container
		CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

		// Create a reference to the blob
		CloudBlob blob = container.GetBlobReference("<blob name>");

		// Set the CacheControl property to expire in 1 hour (3600 seconds)
		blob.Properties.CacheControl = "public, max-age=3600";

		// Update the blob's properties in the cloud
		blob.SetProperties();
	}
}
```

>[AZURE.TIP] [.NET용 Azure Blob 저장소 샘플](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)에서 사용할 수 있는 추가 .NET 코드 샘플이 있습니다.

## 다른 방법

- [Azure 명령줄 인터페이스](../xplat-cli-install.md)

	Blob을 업로드하는 경우 `-p` 전환을 사용하여 *cacheControl* 속성을 설정합니다. 이 예제에서는 TTL을 1시간(3600초)으로 설정합니다.

	```text
	azure storage blob upload -c <connectionstring> -p cacheControl="public, max-age=3600" .\test.txt myContainer test.txt
	```

- [Azure 저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

	[Blob 배치](https://msdn.microsoft.com/ko-KR/library/azure/dd179451.aspx), [블록 목록](https://msdn.microsoft.com/ko-KR/library/azure/dd179467.aspx), 또는 [Blob 속성 설정](https://msdn.microsoft.com/library/azure/ee691966.aspx) 요청에 *x-ms-blob-cache-control* 속성을 명시적으로 설정합니다.

- 타사 저장소 관리 도구

	일부 타사 Azure Storage 관리 도구를 사용하면 Blob에 *CacheControl* 속성을 설정할 수 있습니다.

## *캐시 제어* 헤더 테스트

Blob의 TTL을 쉽게 확인할 수 있습니다. 브라우저 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)를 사용하여 Blob에 *캐시 제어* 응답 헤더가 포함되어 있는지 테스트합니다. **wget**, [Postman](https://www.getpostman.com/) 또는 [Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 응답 헤더를 검사할 수도 있습니다.

## 다음 단계

- [*캐시 제어* 헤더에 대해 참고하기](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
- [Azure CDN에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법을 알아봅니다.](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0921_2016-->