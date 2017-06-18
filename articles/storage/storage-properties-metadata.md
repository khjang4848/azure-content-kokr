<properties
	pageTitle="Azure 저장소에서 개체에 대한 속성 및 메타데이터를 설정 및 검색 | Microsoft Azure"
	description="Azure 저장소의 개체에 사용자 지정 메타데이터를 저장하고 시스템 속성을 설정 및 검색합니다."
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

# 속성 및 메타 데이터 설정 및 검색 #

## 개요

Azure 저장소의 개체는 시스템 속성 및 사용자 정의 메타데이터와 포함된 데이터를 지원합니다.

*   **시스템 속성.** 각 저장소 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. Azure 저장소 클라이언트 라이브러리에서 유지 관리합니다.

*   **사용자 정의 메타 데이터.** 사용자 정의 메타 데이터는 이름-값 쌍의 형태로 제공된 리소스에서 지정 하는 메타 데이터입니다. 메타데이터를 사용하면 저장소 리소스와 함께 추가 값을 저장할 수 있습니다. 이 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

저장소 리소스에 대한 속성 및 메타데이터 값 검색은 두 단계로 이루어집니다. 이러한 값을 읽으려면 먼저 **FetchAttributes** 메서드를 호출하여 명시적으로 가져와야 합니다.

> [AZURE.IMPORTANT] **FetchAttributes** 메서드 중 하나를 호출하지 않으면 저장소 리소스에 대한 속성 및 메타데이터 값이 채워지지 않습니다.

## 속성 설정 및 검색

속성 값을 검색하려면 Blob 또는 컨테이너에서 **FetchAttributes** 메서드를 호출하여 속성을 채운 다음 값을 읽습니다.

개체에서 속성을 설정하려면 속성 값을 지정한 다음 **SetProperties** 메서드를 호출합니다.

다음 코드 예제는 컨테이너를 만들고 콘솔 창에 속성 값 일부를 씁니다.

    //Parse the connection string for the storage account.
	const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## 메타 데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정하려면 이름-값 쌍을 리소스의 **메타 데이터** 컬렉션에 추가한 다음, **SetMetadata** 메서드를 호출하여 값을 서비스에 저장합니다.

> [AZURE.NOTE] 메타데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다.
 
다음 코드 예제에서는 컨테이너에서 메타데이터를 설정합니다. 하나의 값은 컬렉션의 **추가** 메서드를 사용하여 설정됩니다. 다른 값은 암시적 키/값 구문을 사용하여 설정됩니다. 둘 다 모두 유효합니다.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

메타데이터를 검색하려면 아래 예제에 나타난 대로 Blob 또는 컨테이너에서 **FetchAttributes** 메서드를 호출하여 **메타데이터** 컬렉션을 채운 다음 값을 읽습니다.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## 참고 항목  

- [.NET용 Azure 저장소 클라이언트 라이브러리 참조](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [.NET용 Azure 저장소 클라이언트 라이브러리 패키지](https://www.nuget.org/packages/WindowsAzure.Storage/)

<!---HONumber=AcomDC_0914_2016-->