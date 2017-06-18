<properties
	pageTitle="Blob 저장소와 함께 SAS 만들기 및 사용 | Microsoft Azure"
	description="이 자습서에서는 Blob 저장소와 함께 사용할 공유 액세스 서명을 만드는 방법과 클라이언트 응용 프로그램에서 이를 사용하는 방법을 보여 줍니다."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="tamram"/>


# 공유 액세스 서명, 2부: Blob 저장소를 사용하여 SAS 만들기 및 사용

## 개요

이 자습서의 [1부](storage-dotnet-shared-access-signature-part-1.md)에서는 SAS(공유 액세스 서명)에 대해 설명하고 SAS 사용을 위한 모범 사례를 살펴보았습니다. 2부에서는 Blob 저장소를 사용하여 공유 액세스 서명을 생성한 다음 사용하는 방법에 대해 살펴봅니다. 예제는 C#으로 작성되었으며 Azure Storage Client Library for .NET을 사용합니다. 시나리오에서는 공유 액세스 서명 작업의 다음과 같은 측면을 다룹니다.

- 컨테이너에서 공유 액세스 서명 생성
- Blob에서 공유 액세스 서명 생성
- 컨테이너의 리소스에서 서명을 관리하는 저장된 액세스 정책 만들기
- 클라이언트 응용 프로그램에서 공유 액세스 서명 테스트

## 이 자습서 정보

이 자습서에서는 두 콘솔 응용 프로그램을 만들어 컨테이너 및 Blob에 대한 공유 액세스 서명을 만드는 과정을 중점적으로 다룹니다. 첫 번째 콘솔 응용 프로그램에서는 컨테이너 및 Blob에 대한 공유 액세스 서명을 생성합니다. 이 응용 프로그램에서는 저장소 계정 키를 알고 있습니다. 클라이언트 응용 프로그램 역할을 하는 두 번째 콘솔 응용 프로그램에서는 첫 번째 응용 프로그램에서 만든 공유 액세스 서명을 사용하여 컨테이너 및 Blob 리소스에 액세스합니다. 이 응용 프로그램에서는 컨테이너 및 Blob 리소스에 대한 액세스를 인증하는 데에만 공유 액세스 서명을 사용하고, 계정 키에 대해 알지 못합니다.

## 1부: 공유 액세스 서명을 생성하는 콘솔 응용프로그램 만들기

먼저 Azure Storage Client Library for .NET을 설치했는지 확인합니다. 클라이언트 라이브러리에 대한 최신 어셈블리가 들어 있는 [NuGet 패키지](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet 패키지")를 설치할 수 있습니다. 이는 최신 수정이 설치되어 있는지 확인하는 권장 방법입니다. 클라이언트 라이브러리를 최신 버전 [Azure SDK for .NET](https://azure.microsoft.com/downloads/)의 일부로 다운로드할 수도 있습니다.

Visual Studio에서 새 Windows 콘솔 응용 프로그램을 만들고 이름을 **GenerateSharedAccessSignatures**로 지정합니다. 다음 중 한 가지 방법을 사용하여 **Microsoft.WindowsAzure.Configuration.dll** 및 **Microsoft.WindowsAzure.Storage.dll**에 대한 참조를 추가합니다.

- 	NuGet 패키지를 설치하려는 경우 먼저 [NuGet 클라이언트](https://docs.nuget.org/consume/installing-nuget)를 설치합니다. Visual Studio에서 **프로젝트 | NuGet 패키지 관리**를 선택하고 온라인에서 **Azure 저장소**를 검색한 다음 지침에 따라 설치하십시오.
- 	또는 Azure SDK 설치에서 어셈블리를 찾은 다음 참조를 추가합니다.

Program.cs 파일의 맨 위에 다음 **using** 문을 추가합니다.

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

저장소 계정을 가리키는 연결 문자열과 함께 구성 설정을 포함하도록 app.config 파일을 편집합니다. app.config 파일은 다음과 비슷합니다.

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### 컨테이너에 대한 공유 액세스 서명 URI 생성

먼저 새 컨테이너에서 공유 액세스 서명을 생성하는 메서드를 추가합니다. 이 경우 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에서 만료 시간과 허용된 권한을 나타내는 정보를 전달합니다.

먼저 **Main()** 메서드에 저장소 계정에 대한 액세스를 인증하고 새 컨테이너를 만드는 코드를 추가합니다.

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();

	    //Insert calls to the methods created below here...

	    //Require user input before closing the console window.
	    Console.ReadLine();
    }

그런 다음 컨테이너에 대한 공유 액세스 서명을 생성하고 서명 URI를 반환하는 새 메서드를 추가합니다.

    static string GetContainerSasUri(CloudBlobContainer container)
    {
	    //Set the expiry time and permissions for the container.
	    //In this case no start time is specified, so the shared access signature becomes valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

	    //Generate the shared access signature on the container, setting the constraints directly on the signature.
	    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetContainerSasUri()**를 호출하고 콘솔 창에 서명 URI를 쓰는 다음 줄을 추가합니다.

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

새 컨테이너에 대한 공유 액세스 서명 URI를 출력하도록 컴파일 및 실행합니다. URI는 다음과 비슷합니다.

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

코드를 실행하면 컨테이너에서 만든 공유 액세스 서명은 다음 24시간동안 유효합니다. 서명은 클라이언트에게 컨테이너에 있는 Blob을 나열하고 컨테이너에 새 Blob을 쓸 수 있는 권한을 부여합니다.

### Blob에 대한 공유 액세스 서명 URI 생성

이제 컨테이너 내에서 새 Blob을 만들고 해당 Blob에 대한 공유 액세스 서명을 생성하는 비슷한 코드를 작성합니다. 이 공유 액세스 서명은 저장된 액세스 정책과 연결되지 않으므로, URI에 대한 시작 시간, 만료 시간 및 권한 정보를 포함합니다.

새 Blob을 만들고 이 Blob에 일부 텍스트를 쓴 다음 공유 액세스 서명을 생성하고 서명 URI를 반환하는 새 메서드를 추가합니다.

    static string GetBlobSasUri(CloudBlobContainer container)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

	    //Upload text to the blob. If the blob does not yet exist, it will be created.
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }

	    //Set the expiry time and permissions for the blob.
	    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
	    //The shared access signature will be valid immediately.
	    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
	    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
	    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
	    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

	    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
	    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetBlobSasUri()**를 호출하고 콘솔 창에 공유 액세스 서명 URI를 쓰는 다음 줄을 추가합니다.

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


새 Blob에 대한 공유 액세스 서명 URI를 출력하도록 컴파일 및 실행합니다. URI는 다음과 비슷합니다.

	https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### 컨테이너에 대한 저장된 액세스 정책 만들기

이제 연결된 공유 액세스 서명에 대한 제약 조건을 정의하는 컨테이너에 대한 저장된 액세스 정책을 만들어 보겠습니다.

이전 예제에서는 공유 액세스 서명 URI 자체에 대한 시작 시간(암시적 또는 명시적), 만료 시간 및 사용 권한을 지정했습니다. 다음 예제에서는 공유 액세스 서명이 아니라 저장된 액세스 정책에 대해 이러한 설정을 지정합니다. 그러면 공유 액세스 서명을 다시 실행하지 않고 제약 조건을 변경할 수 있습니다.

공유 액세스 서명에 대한 제약 조건 및 저장된 액세스 정책에 대한 나머지를 하나 이상 둘 수 있습니다. 하지만 시작 시간, 만료 시간 및 사용 권한을 한 곳에서만 지정할 수 있습니다. 예를 들어 사용 권한을 공유 액세스 서명에 대해 지정하고 저장된 액세스 정책에 대해서도 지정할 수는 없습니다.

액세스 정책을 컨테이너에 추가하면 컨테이너의 기존 사용 권한을 가지고 새 액세스 정책을 추가한 다음 컨테이너의 사용 권한을 설정해야 합니다.

컨테이너에 저장된 액세스 정책을 새로 만드는 새 메서드를 추가하고 정책의 이름을 반환합니다.

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
		string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 다음 행을 추가하여 먼저 기존 액세스 정책을 지운 다음 **CreateSharedAccessPolicy()** 메서드를 호출합니다.

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

컨테이너에 대한 액세스 정책을 지우면 먼저 컨테이너의 기존 사용 권한을 가지고 사용 권한을 지운 다음 사용 권한을 다시 설정합니다.

### 액세스 정책을 사용하는 컨테이너에 대한 공유 액세스 서명 URI 생성

이제 이전에 만든 컨테이너에 대한 다른 공유 액세스 서명을 만듭니다. 이번에는 이전 예제에서 만든 액세스 정책에 서명을 연결합니다.

컨테이너에 대한 다른 공유 액세스 서명을 생성하는 새 메서드를 추가합니다.

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Generate the shared access signature on the container. In this case, all of the constraints for the
	    //shared access signature are specified on the stored access policy.
	    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

	    //Return the URI string for the container, including the SAS token.
	    return container.Uri + sasContainerToken;
    }

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetContainerSasUriWithPolicy** 메서드를 호출하는 다음 줄을 추가합니다.

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### 액세스 정책을 사용하는 Blob에 대한 공유 액세스 서명 URI 생성

마지막으로 다른 Blob을 만들고 액세스 정책에 연결되는 공유 액세스 서명을 생성하는 비슷한 메서드를 추가합니다.

Blob을 만들고 공유 액세스 서명을 생성하는 새 메서드를 추가합니다.

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
	    //Get a reference to a blob within the container.
	    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

	    //Upload text to the blob. If the blob does not yet exist, it will be created.
	    //If the blob does exist, its existing content will be overwritten.
	    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
	    "A stored access policy defines the constraints for the signature.";
	    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
	    ms.Position = 0;
	    using (ms)
	    {
		    blob.UploadFromStream(ms);
	    }

	    //Generate the shared access signature on the blob.
	    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

	    //Return the URI string for the container, including the SAS token.
	    return blob.Uri + sasBlobToken;
    }

**Main()** 메서드의 맨 아래에 **Console.ReadLine()**에 대한 호출 앞에 **GetBlobSasUriWithPolicy** 메서드를 호출하는 다음 줄을 추가합니다.

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

**Main()** 메서드는 전체적으로 다음과 비슷합니다. 이 메서드를 실행하여 콘솔 창에 공유 액세스 서명 URI를 쓴 다음 URI를 복사하여 이 자습서의 2부에서 사용할 텍스트 파일에 붙여넣습니다.

    static void Main(string[] args)
    {
	    //Parse the connection string and return a reference to the storage account.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

	    //Create the blob client object.
	    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	    //Get a reference to a container to use for the sample code, and create it if it does not exist.
	    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
	    container.CreateIfNotExists();

	    //Generate a SAS URI for the container, without a stored access policy.
	    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
	    Console.WriteLine();

	    //Generate a SAS URI for a blob within the container, without a stored access policy.
	    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
	    Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
	    string sharedAccessPolicyName = "tutorialpolicy";
	    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

	    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();

	    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
	    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
	    Console.WriteLine();

	    Console.ReadLine();
    }

GenerateSharedAccessSignatures 콘솔 응용 프로그램을 실행하면 콘솔 창에 다음과 비슷한 출력이 표시됩니다. 이는 자습서의 2부에서 사용할 공유 액세스 서명입니다.

![sas-console-output-1][sas-console-output-1]

## 2부: 공유 액세스 서명을 테스트하는 콘솔 응용프로그램 만들기

이전 예제에서 만든 공유 액세스 서명을 테스트하려면 서명을 사용하여 컨테이너 및 Blob에서 작업을 수행하는 두 번째 콘솔 응용 프로그램을 만듭니다.

> [AZURE.NOTE] 자습서의 1부를 완료한 후 24시간이 초과된 경우 생성된 서명은 더 이상 유효하지 않습니다. 이 경우, 첫 번째 콘솔 응용 프로그램에서 코드를 실행하여 자습서의 2부에서 사용할 공유 액세스 서명을 다시 생성해야 합니다.

Visual Studio에서 새 Windows 콘솔 응용 프로그램을 만들고 이름을 **ConsumeSharedAccessSignatures**로 지정합니다. 앞에서 수행한 것과 같은 방법으로 **Microsoft.WindowsAzure.Configuration.dll** 및 **Microsoft.WindowsAzure.Storage.dll**에 참조를 추가합니다.

Program.cs 파일의 맨 위에 다음 **using** 문을 추가합니다.

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

**Main()** 메서드의 본문에 다음 제약 조건을 추가하고 자습서의 1부에서 생성한 공유 액세스 서명으로 값을 업데이트합니다.

    static void Main(string[] args)
    {
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### 공유 액세스 서명을 사용하여 컨테이너 작업을 수행하는 메서드 추가

이제 컨테이너에서 공유 액세스 서명을 사용하여 일부 대표적인 컨테이너 작업을 테스트하는 메서드를 추가합니다. 공유 액세스 서명은 서명만을 기반으로 하여 컨테이너에 대한 액세스를 인증하여 컨테이너에 대한 참조를 반환하는 데 사용됩니다.

Program.cs에 다음 메서드를 추가합니다.

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


컨테이너에서 만든 공유 액세스 서명을 모두 사용하여 **UseContainerSAS()**를 호출하도록 **Main()** 메서드를 업데이트합니다.

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy);

	    Console.ReadLine();
	}


### 공유 액세스 서명을 사용하여 Blob 작업을 수행하는 메서드 추가

마지막으로 Blob에서 공유 액세스 서명을 사용하여 일부 대표적인 Blob 작업을 테스트하는 메서드를 추가합니다. 이 경우 공유 액세스 서명을 전달하여 Blob에 대한 참조를 반환하는 생성자 **CloudBlockBlob(String)**을 사용합니다. 다른 인증은 필요하지 않으며 서명만을 기반으로 합니다.

Program.cs에 다음 메서드를 추가합니다.

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Blob에서 만든 공유 액세스 서명을 모두 사용하여 **UseBlobSAS()**를 호출하도록 **Main()** 메서드를 업데이트합니다.

	static void Main(string[] args)
	{
	    string containerSAS = "<your container SAS>";
	    string blobSAS = "<your blob SAS>";
	    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
	    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

	    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
	    UseContainerSAS(containerSAS);
	    UseContainerSAS(containerSASWithAccessPolicy);

	    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
	    UseBlobSAS(blobSAS);
	    UseBlobSAS(blobSASWithAccessPolicy);

	    Console.ReadLine();
	}

콘솔 응용 프로그램을 실행하고 출력을 관찰하여 서명별로 허용되는 작업을 확인합니다. 콘솔 창의 출력은 다음과 비슷합니다.

![sas-console-output-2][sas-console-output-2]

## 다음 단계

[공유 액세서 서명, 1부: SAS 모델 이해하기](storage-dotnet-shared-access-signature-part-1.md)

[컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)

[공유 액세스 서명을 사용하여 액세스 위임(REST API)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[테이블 및 큐 SAS 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG

<!---HONumber=AcomDC_0914_2016-->