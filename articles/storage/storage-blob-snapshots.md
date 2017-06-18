<properties
	pageTitle="Blob의 읽기 전용 스냅숏 만들기 | Microsoft Azure"
	description="지정된 시점에서 Blob 데이터를 백업하는 Blob의 스냅숏을 만드는 방법을 알아봅니다. 용량 요금을 최소화하기 위해 스냅숏의 청구 방법 및 사용 방법을 파악합니다."
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

# Blob 스냅숏 만들기

## 개요

스냅숏은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다. 스냅숏은 blob를 백업하는데 유용 합니다. 스냅숏을 만든 후에 읽기, 복사 또는 삭제할 수 있지만 수정할 수 없습니다.

Blob URI에 스냅숏이 만들어진 시점의 시간을 나타내는 Blob URI에 추가된 **DateTime** 값이 있다는 점을 제외하고 Blob의 스냅숏은 해당 Blob와 동일합니다. 예를 들어 페이지 Blob URI가 `http://storagesample.core.blob.windows.net/mydrives/myvhd`이면 스냅숏 URI는 `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`과 유사합니다.

> [AZURE.NOTE] 모든 스냅숏은 기본 Blob의 URI를 공유합니다. 기본 Blob와 스냅숏의 유일한 차이는 추가된 **DateTime** 값입니다.

Blob 하나에 여러 스냅숏이 있을 수 있습니다. 스냅숏을 명시적으로 삭제하기 전까지 유지됩니다. 스냅숏은 해당 기본 Blob보다 수명이 길 수 없습니다. 기본 Blob와 연결된 스냅숏을 열거하여 현재 스냅숏을 추적할 수 있습니다.

Blob의 스냅숏을 만들면 blob의 시스템 속성이 같은 값으로 스냅숏에 복사됩니다. 만들 때 스냅숏에 대한 별도의 메타데이터를 지정하지 않으면 기본 Blob의 메타데이터가 스냅숏에 복사됩니다.

기본 blob와 연결된 모든 임대는 스냅숏에 영향을 주지 않습니다. 스냅숏에 대해 임대를 가져올 수 없습니다.

## 스냅숏 만들기

다음 코드 예제에서는 .NET에서 스냅숏을 만드는 방법을 보여 줍니다. 이 예제에서는 만들 때 스냅숏에 대한 별도의 메타데이터를 지정합니다.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## 스냅숏 복사

Blob 및 스냅숏 관련 복사 작업에는 다음 규칙이 적용됩니다.

- 기본 Blob에 대해 스냅숏을 복사할 수 있습니다. 스냅숏의 수준을 기본 Blob 위치로 올리면 이전 Blob 버전을 복원할 수 있습니다. 스냅숏은 그대로 유지되지만 기본 blob은 스냅숏의 쓰기 가능한 복사본으로 덮어씁니다.

- 스냅숏을 다른 이름으로 대상 Blob에 복사할 수 있습니다. 생성되는 대상 Blob은 스냅숏이 아닌 쓰기 가능한 Blob입니다.

- 원본 Blob을 복사해도 해당 원본 Blob의 스냅숏은 대상에 복사되지 않습니다. 대상 Blob을 복사본으로 덮어쓸 때 원래 대상 Blob와 연결된 스냅숏은 그대로 유지됩니다.

- 블록 Blob의 스냅숏을 만들면 블록의 커밋된 블록 목록도 스냅숏에 복사됩니다. 커밋되지 않은 블록은 복사되지 않습니다.

## 액세스 조건 지정

조건이 충족되어야 스냅숏이 작성되도록 액세스 조건을 지정할 수 있습니다. 액세스 조건을 지정하려면 **AccessCondition** 속성을 사용합니다. 지정한 조건이 충족되지 않으면 스냅숏은 작성되지 않으며 Blob 서비스는 상태 코드 HTTPStatusCode.PreconditionFailed를 반환합니다.

## 스냅숏 삭제

스냅숏을 삭제하지 않으면 스냅숏이 포함된 Blob을 삭제할 수 없습니다. 스냅숏을 개별적으로 삭제하거나 원본 Blob를 삭제할 때 모든 스냅숏을 삭제되도록 지정할 수 있습니다. 스냅숏이 있는 Blob을 삭제하려고 하면 오류가 발생합니다.

다음 코드 예제에서는 .NET에서 Blob 및 해당 스냅숏을 삭제하는 방법을 보여 줍니다. 여기서 `blockBlob`은 **CloudBlockBlob** 유형의 변수입니다.

	await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## Azure 프리미엄 저장소를 사용한 스냅숏

프리미엄 저장소에서 스냅숏을 사용할 때는 다음 규칙이 적용됩니다.

- 프리미엄 저장소 계정의 페이지 Blob당 최대 스냅숏 수는 100개입니다. 해당 제한을 초과하면 스냅숏 Blob 작업에서 오류 코드 409(**SnapshotCountExceeded**)가 반환됩니다.

- 프리미엄 저장소 계정의 페이지 Blob 스냅숏은 10분마다 한 번 생성할 수 있습니다. 해당 속도를 초과하면 스냅숏 Blob 작업에서 오류 코드 409(**SnaphotOperationRateExceeded**)가 반환됩니다.

- Blob 가져오기를 호출하여 프리미엄 저장소 계정에서 페이지 Blob의 스냅숏을 읽을 수는 없습니다. 프리미엄 저장소 계정에서 스냅숏에 대해 Blob 가져오기를 호출하면 오류 코드 400(**InvalidOperation**)이 반환됩니다. 그러나 프리미엄 저장소 계정에서 스냅숏에 대해 Blob 속성 가져오기 및 Blob 메타데이터 가져오기를 호출할 수 있습니다.

- 스냅숏을 읽으려는 경우 Blob 복사 작업을 사용하여 계정의 다른 페이지 Blob에 스냅숏을 복사할 수 있습니다. 이때 복사 작업의 대상 Blob에는 기존 스냅숏이 없어야 합니다. 대상 Blob에 스냅숏이 있으면 Blob 복사 작업에서 오류 코드 409(**SnapshotsPresent**)가 반환됩니다.

## 스냅숏에 대한 절대 URI 반환

이 C# 코드 예제에서는 스냅숏을 만들고 기본 위치에 대한 절대 URI를 작성합니다.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## 스냅숏 요금 청구 방법 이해

Blob의 읽기 전용 복사본인 스냅숏을 만들면 계정에 데이터 저장소 요금이 추가로 부과될 수 있습니다. 응용 프로그램을 디자인할 때는 불필요한 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

### 청구 관련 중요 고려 사항

아래 목록에는 스냅숏을 만들 때 고려할 주요 사항이 나와 있습니다.

- 저장소 계정에서는 고유 블록이나 페이지가 Blob에 있는지 혹은 스냅숏에 있는지에 관계없이 요금이 발생합니다. 스냅숏의 기준이 되는 Blob을 업데이트할 때까지는 해당 Blob와 연결된 스냅숏에 대해 계정에 추가 요금이 부과되지 않습니다. 기본 Blob를 업데이트한 후에 해당 스냅숏과 달라집니다. 이 경우에 Blob 또는 스냅숏 각각에서 고유한 블록이나 페이지에 대한 요금이 청구됩니다.

- 블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅숏에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅숏의 해당 항목과 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

- **UploadFile**, **UploadText**, **UploadStream** 또는 **UploadByteArray** 메서드를 호출하여 블록 Blob을 바꾸면 해당 Blob의 모든 블록이 바뀝니다. 해당 Blob에 스냅숏이 연결되어 있으면 스냅숏과 기본 Blob의 모든 블록이 달라지므로 두 Blob의 모든 블록에 대해 요금이 부과됩니다. 이는 기본 Blob와 스냅숏의 데이터가 동일하게 유지되더라도 마찬가지입니다.

- Azure Blob 서비스에서는 두 블록이 같은 데이터를 포함하는지를 확인할 수 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해서는 비용이 부과되므로 스냅숏이 포함된 Blob을 업데이트하면 고유한 블록이 추가로 생성되어 추가 비용이 발생함을 고려해야 합니다.

> [AZURE.NOTE] 모범 사례에 따르면 추가 비용을 방지하기 위해 스냅숏을 철저하게 관리해야 합니다. 즉, 다음과 같은 방식으로 스냅숏을 관리하는 것이 좋습니다.

> - 응용 프로그램 디자인상 스냅숏을 유지해야 하는 경우가 아니면, Blob을 업데이트할 때마다 같은 데이터로 업데이트하더라도 해당 Blob에 연결된 스냅숏을 삭제한 후에 다시 만듭니다. Blob의 스냅숏을 삭제한 후에 다시 만들면 Blob와 스냅숏이 달라지지 않습니다.

> - Blob의 스냅숏을 유지하는 경우에는 **UploadFile**, **UploadText**, **UploadStream** 또는 **UploadByteArray**를 호출하여 Blob을 업데이트하지 않습니다. 이러한 메서드는 Blob의 모든 블록을 바꾸어 기본 Blob 및 스냅숏이 심각하게 달라집니다. 대신 **PutBlock** 및 **PutBlockList** 메서드를 사용하여 가능한 최소 블록 수만 업데이트합니다.


### 스냅숏 청구 시나리오


다음 시나리오에서는 블록 Blob와 해당 스냅숏에 대해 비용이 청구되는 방법을 보여 줍니다.

시나리오 1에서는 스냅숏을 생성한 후 기본 Blob을 업데이트하지 않았으므로 고유 블록 1, 2, 3에 대해서만 비용이 청구됩니다.

![Azure 저장소 리소스](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

시나리오 2에서는 기본 Blob은 업데이트되었지만 스냅숏은 업데이트되지 않았습니다. 블록 3이 업데이트되고 동일한 데이터와 동일한 ID를 가지고 있지만 스냅숏의 블록 3과 같지는 않습니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![Azure 저장소 리소스](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

시나리오 3에서는 기본 Blob은 업데이트되었지만 스냅숏은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 스냅숏은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![Azure 저장소 리소스](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록을 하나도 포함하지 않습니다. 따라서 8개 고유 블록 모두에 대한 요금이 계정에 청구됩니다. **UploadFile**, **UploadText**, **UploadFromStream**, **UploadByteArray** 등의 업데이트 메서드를 사용하는 경우가 이러한 시나리오에 해당합니다. 이러한 메서드는 Blob의 모든 콘텐츠를 바꾸기 때문입니다.

![Azure 저장소 리소스](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## 다음 단계

Blob 저장소를 사용하는 추가 예제는 [Azure 코드 샘플](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)을 참조하세요. GitHub에서 샘플 응용 프로그램을 다운로드하고 실행하거나 코드를 탐색할 수 있습니다.

<!---HONumber=AcomDC_0914_2016-->