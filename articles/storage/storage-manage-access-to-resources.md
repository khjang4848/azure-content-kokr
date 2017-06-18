<properties 
	pageTitle="컨테이너 및 Blob에 대한 익명의 읽기 액세스 권한 관리 | Microsoft Azure" 
	description="컨테이너와 Blob에서 익명 액세스를 사용하도록 설정하는 방법 및 프로그래밍 방식으로 액세스하는 방법을 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016"
	ms.author="tamram"/>

# 컨테이너 및 Blob에 대한 익명 읽기 권한 관리

## 개요

기본적으로는 저장소 계정 소유자만 해당 계정 내의 저장소 리소스에 액세스할 수 있습니다. Blob 저장소에만 컨테이너의 권한을 설정하여 익명 읽기 권한을 컨테이너 및 해당 Blob에 허용할 수 있으므로 계정 키를 공유하지 않고도 해당 리소스에 액세스를 허용할 수 있습니다.

익명 액세스는 특정 Blob을 익명 읽기 권한에 사용하기를 원하는 경우에 가장 적합합니다. 더 세밀하게 제어하려면 공유 액세스 서명을 만들어 다른 권한 및 지정된 시간 간격을 통해 제한된 액세스를 위임할 수 있습니다. 공유 액세스 서명 만들기에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

## 컨테이너 및 Blob에 익명의 사용자 권한 부여

기본적으로는 저장소 계정 소유자만 컨테이너 및 해당 컨테이너 내의 모든 Blob에 액세스할 수 있습니다. 익명 사용자에게 컨테이너와 해당 Blob에 대한 읽기 권한을 제공하려면 공용 액세스를 허용하도록 컨테이너 권한을 설정할 수 있습니다. 그러면 익명 사용자가 요청을 인증하지 않고도 공개적으로 액세스 가능한 컨테이너 내의 Blob을 읽을 수 있습니다.

컨테이너는 컨테이너 액세스를 관리하기 위한 다음 옵션을 제공합니다.

- **전체 공개 읽기 액세스:** 익명 요청을 통해 컨테이너와 Blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

- **Blob에 대해서만 공개 읽기 액세스:** 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.

- **공개 읽기 액세스 권한 없음:** 계정 소유자만 컨테이너 및 Blob 데이터를 읽을 수 있습니다.

다음과 같은 방법으로 컨테이너 권한을 설정할 수 있습니다.

- [Azure 포털](https://portal.azure.com)의 경우.
- 프로그래밍 방식으로 저장소 클라이언트 라이브러리 또는 REST API 사용
- PowerShell 사용 Azure PowerShell에서 컨테이너 권한을 설정하는 방법에 관해 알아보려면 [Azure 저장소와 Azure PowerShell 사용](storage-powershell-guide-full.md#how-to-manage-azure-blobs)을 참조하세요.

### Azure 포털에서 컨테이너 권한 설정

[Azure 포털](https://portal.azure.com)에서 컨테이너 권한을 설정하려면 다음 단계를 수행하세요.

1. 저장소 계정에 대한 대시보드로 이동합니다.
2. 목록에서 컨테이너 이름을 선택합니다. 이름 열의 오른쪽을 클릭하여 컨테이너 이름을 선택해야 합니다. 이름을 클릭하면 컨테이너로 드릴다운하여 해당 Blob을 표시합니다.
3. 도구 모음에서 **편집**을 선택합니다.
4. **컨테이너 메타데이터 편집** 대화 상자에서 아래 스크린샷에 표시된 것처럼 **액세스** 필드에서 원하는 권한 수준을 선택합니다.

	![컨테이너 메타데이터 편집 대화 상자](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-1.png)

### .NET을 사용하여 프로그래밍 방식으로 컨테이너 권한 설정

.NET 클라이언트 라이브러리를 사용하여 컨테이너에 대한 권한을 설정하려면 먼저 컨테이너의 기존 권한을 **GetPermissions** 메서드를 호출하여 검색합니다. 그런 다음 **GetPermissions** 메서드에 의해 반환된 **BlobContainerPermissions** 개체에 대해 **PublicAccess** 속성을 설정합니다. 마지막으로 업데이트된 권한으로 **SetPermissions** 메서드를 호출합니다.

다음 예제에서는 컨테이너의 권한을 전체 공용 읽기 권한으로 설정합니다. 권한을 Blob에 대해서만 공용 읽기 권한으로 설정하려면 **PublicAccess** 속성을 **BlobContainerPublicAccessType.Blob**으로 설정합니다. 익명 사용자에 대한 모든 권한을 제거하려면 속성을 **BlobContainerPublicAccessType.Off**로 설정합니다.

    public static void SetPublicContainerPermissions(CloudBlobContainer container)
    {
        BlobContainerPermissions permissions = container.GetPermissions();
        permissions.PublicAccess = BlobContainerPublicAccessType.Container;
        container.SetPermissions(permissions);
    }

## 컨테이너 및 Blob에 익명으로 액세스

컨테이너 및 Blob에 익명으로 액세스하는 클라이언트는 자격 증명을 필요로 하지 않는 생성자를 사용할 수 있습니다. 다음 예제에서는 Blob 서비스 리소스를 익명으로 참조하는 약간 다른 방법을 보여 줍니다.

### 익명 클라이언트 개체 만들기

계정에 Blob 서비스 끝점을 제공하여 익명 액세스에 대한 새 서비스 클라이언트 개체를 만들 수 있습니다. 그러나 익명 액세스에 사용할 수 있는 해당 계정의 컨테이너 이름도 알아야 합니다.

    public static void CreateAnonymousBlobClient()
    {
        // Create the client object using the Blob service endpoint.
        CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

        // Read the container's properties. Note this is only possible when the container supports full public read access.
        container.FetchAttributes();
        Console.WriteLine(container.Properties.LastModified);
        Console.WriteLine(container.Properties.ETag);
    }

### 컨테이너를 익명으로 참조

익명으로 사용할 수 있는 컨테이너에 대한 URL이 있는 경우 해당 URL을 사용하여 컨테이너를 직접 참조할 수 있습니다.

    public static void ListBlobsAnonymously()
    {
        // Get a reference to a container that's available for anonymous access.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

        // List blobs in the container.
        foreach (IListBlobItem blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        }
    }


### Blob을 익명으로 참조

익명 액세스에 사용할 수 있는 Blob에 대한 URL이 있는 경우 해당 URL을 사용하여 Blob을 직접 참조할 수 있습니다.

    public static void DownloadBlobAnonymously()
    {
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
        blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
    }

## 익명 사용자에게 제공되는 기능

아래 표에는 컨테이너 ACL이 공용 액세스를 허용하도록 설정되어 있을 때 익명 사용자가 호출할 수 있는 작업이 나와 있습니다.

| REST 작업 | 전체 공용 읽기가 가능한 권한 | Blob 전용 공용 읽기가 가능한 권한 |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| 컨테이너 나열 | 소유자만 | 소유자만 |
| 컨테이너 만들기 | 소유자만 | 소유자만 |
| 컨테이너 속성 가져오기 | 모두 | 소유자만 |
| 컨테이너 메타데이터 가져오기 | 모두 | 소유자만 |
| 컨테이너 메타데이터 설정 | 소유자만 | 소유자만 |
| 컨테이너 ACL 가져오기 | 소유자만 | 소유자만 |
| 컨테이너 ACL 설정 | 소유자만 | 소유자만 |
| 컨테이너 삭제 | 소유자만 | 소유자만 |
| Blob 나열 | 모두 | 소유자만 |
| Blob 배치 | 소유자만 | 소유자만 |
| Blob 가져오기 | 모두 | 모두 |
| Blob 속성 가져오기 | 모두 | 모두 |
| Blob 속성 설정 | 소유자만 | 소유자만 |
| Blob 메타데이터 가져오기 | 모두 | 모두 |
| Blob 메타데이터 설정 | 소유자만 | 소유자만 |
| 블록 배치 | 소유자만 | 소유자만 |
| 블록 목록 가져오기(커밋된 블록만) | 모두 | 모두 |
| 블록 목록 가져오기(커밋되지 않은 블록만 또는 모든 블록) | 소유자만 | 소유자만 |
| 블록 목록 배치 | 소유자만 | 소유자만 |
| Blob 삭제 | 소유자만 | 소유자만 |
| Blob 복사 | 소유자만 | 소유자만 |
| Blob 스냅숏 | 소유자만 | 소유자만 |
| Blob 임대 | 소유자만 | 소유자만 |
| 페이지 가져오기 | 소유자만 | 소유자만 |
| 페이지 범위 가져오기 | 모두 | 모두 |
| Blob 추가 | 소유자만 | 소유자만 |


## 참고 항목

- [Azure 저장소 서비스에 대한 인증](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)
- [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/azure/ee395415.aspx)

<!---HONumber=AcomDC_0914_2016-->