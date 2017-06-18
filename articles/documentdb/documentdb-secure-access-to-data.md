<properties 
	pageTitle="DocumentDB에서 데이터에 대한 액세스를 보호하는 방법 | Microsoft Azure" 
	description="마스터 키, 읽기 전용 키, 사용자 및 권한을 포함해서 DocumentDB의 액세스 제어 개념에 대해 알아봅니다." 
	services="documentdb" 
	authors="kiratp" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/19/2016" 
	ms.author="kipandya"/>

# DocumentDB 데이터에 대한 액세스 보호

이 문서에서는 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)에 저장된 데이터에 대한 액세스를 보호하는 방법을 개괄적으로 설명합니다.

이 개요를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

-	DocumentDB 마스터 키는 무엇인가요?
-	DocumentDB 읽기 전용 키는 무엇인가요?
-	DocumentDB 리소스 토큰은 무엇인가요?
-	DocumentDB 사용자 및 권한을 사용해서 DocumentDB 데이터에 대한 액세스를 보호하려면 어떻게 하나요?

## DocumentDB 액세스 제어 개념

DocumentDB는 DocumentDB 리소스에 대한 액세스를 제어하기 위해 최초의 클래스 개념을 제공합니다. 이 항목의 목적에 따라 DocumentDB 리소스는 두 가지 범주로 그룹화되어 있습니다.

- 관리 리소스
	- 계정
	- 데이터베이스
	- 사용자
	- 사용 권한
- 응용 프로그램 리소스
	- 컬렉션
	- 제안
	- 문서
	- 첨부 파일
	- 저장 프로시저
	- 트리거
	- 사용자 정의 함수

이러한 두 범주의 컨텍스트에서 DocumentDB는 계정 관리자, 읽기 전용 관리자 및 데이터베이스 사용자의 세 가지 액세스 제어 사용자 유형을 지원합니다. 각 액세스 제어 사용자의 권한은 다음과 같습니다.
 
- 계정 관리자: 제공된 DocumentDB 계정 내에서 모든 리소스(관리 및 응용 프로그램)에 대한 모든 액세스 권한을 갖습니다.
- 읽기 전용 관리자: 제공된 DocumentDB 계정 내에서 모든 리소스(관리 및 응용 프로그램)에 대한 읽기 전용 액세스 권한을 갖습니다.
- 데이터베이스 사용자: 특정 DocumentDB 데이터베이스 리소스(예: 컬렉션, 문서, 스크립트)의 특정 집합과 연관된 DocumentDB 사용자 리소스입니다. 제공된 데이터베이스와 연관된 사용자 리소스는 1개 이상일 수 있으며, 각 사용자 리소스는 연관된 권한을 하나 이상 포함할 수 있습니다.

앞에서 언급한 범주 및 리소스에 따라 DocumentDB 액세스 제어 모델은 세 가지 유형의 액세스 생성을 정의합니다.

- 마스터 키: DocumentDB 계정을 만들면 2개의 마스터 키(기본 및 보조)가 생성됩니다. 이러한 키는 DocumentDB 계정 내의 모든 리소스에 대한 모든 관리 액세스 권한을 사용할 수 있게 해줍니다.

![DocumentDB 마스터 키 그림](./media/documentdb-secure-access-to-data/masterkeys.png)

- 읽기 전용 키: DocumentDB 계정을 만들면 2개의 읽기 전용 키(기본 및 보조)가 생성됩니다. 이러한 키는 DocumentDB 계정 내의 모든 리소스에 대한 읽기 전용 액세스 권한을 사용할 수 있게 해줍니다.

![DocumentDB 읽기 전용 키 그림](./media/documentdb-secure-access-to-data/readonlykeys.png)

- 리소스 토큰: 리소스 토큰은 DocumentDB 권한 리소스와 연관되며, 데이터베이스 사용자와 해당 사용자가 특정 DocumentDB 응용 프로그램 리소스(예: 컬렉션, 문서)에 대해 갖고 있는 권한 및 데이터베이스 사용자 사이의 관계를 캡처합니다.

![DocumentDB 리소스 토큰 그림](./media/documentdb-secure-access-to-data/resourcekeys.png)

## DocumentDB 마스터 및 읽기 전용 키 작업

앞에서 설명한 것처럼, DocumentDB 마스터 키는 DocumentDB 계정 내에서 모든 리소스에 대해 모든 관리 액세스 권한을 제공하며, 읽기 전용 키는 해당 계정 내의 모든 리소스에 대한 읽기 액세스를 가능하게 해줍니다. 다음 코드 조각에서는 DocumentDB 계정 끝점 및 마스터 키를 사용해서 DocumentClient를 인스턴스화하고 새 데이터베이스를 만듭니다.

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
    private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
    // Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


## DocumentDB 리소스 토큰 개요

DocumentDB 계정에 있는 리소스에 대한 액세스 권한을 마스터 키로 신뢰할 수 없는 클라이언트에 제공하려는 경우 DocumentDB 사용자 및 권한을 만들어서 리소스 토큰을 사용할 수 있습니다. DocumentDB 마스터 키에는 기본 및 보조 키가 포함되며, 이러한 각 키는 계정 및 해당 계정의 모든 리소스에 대한 관리 액세스 권한을 부여합니다. 마스터 키를 노출하면 계정도 악의적이거나 잘못된 용도로 사용될 수 있습니다.

마찬가지로 DocumentDB 읽기 전용 키는 DocumentDB 계정 내에서 모든 리소스(권한 리소스는 제외)에 대한 읽기 액세스 권한을 제공하며, 특정 DocumentDB 리소스에 대해 보다 세부적인 액세스 권한을 제공하기 위해 사용할 수 없습니다.

DocumentDB 리소스 토큰은 사용자가 부여한 권한에 따라 마스터 또는 읽기 전용 키에 대한 요구 없이 Document 계정에서 클라이언트가 리소스를 읽고, 쓰고, 삭제할 수 있게 해주는 안전한 대안을 제공합니다.

다음은 리소스 토큰을 요청, 생성 및 클라이언트에 제공할 수 있는 일반적인 디자인 패턴이 있습니다.

1. 중간 계층 서비스는 모바일 응용 프로그램이 사용자 사진을 공유하도록 설정됩니다.
2. 중간 계층 서비스는 DocumentDB 계정의 마스터 키를 소유합니다.
3. 사진 앱은 최종 사용자 모바일 장치에 설치됩니다.
4. 로그인하면 사진 앱이 중간 계층 서비스를 사용해서 사용자의 ID를 설정합니다. ID 설정 방식은 순전히 응용 프로그램에 달려 있습니다.
5. ID가 설정된 다음에는 중간 계층 서비스 요청 권한이 ID를 기반으로 합니다.
6. 중간 계층 서비스는 리소스 토큰을 다시 전화 앱으로 전송합니다.
7. 전화 앱은 계속 리소스 토큰을 사용해서 리소스의 토큰으로 정의된 권한을 사용하여 DocumentDB 리소스에 직접 액세스합니다.
8. 리소스 토큰이 만료되면 후속 요청은 401 허가되지 않은 예외가 수시됩니다. 이 지점에서는 전화 앱이 ID 를 다시 설정하고 새로운 리소스 토큰을 요청합니다.

![DocumentDB 리소스 토큰 워크플로](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

## DocumentDB 사용자 및 권한 작업
DocumentDB 사용자 리소스는 DocumentDB 데이터베이스와 연관됩니다. 각 데이터베이스는 0개 이상의 DocumentDB 사용자를 포함할 수 있습니다. 다음 코드 조각은 DocumentDB 사용자 리소스를 만드는 방법을 보여 줍니다.

    //Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);

> [AZURE.NOTE] 각 DocumentDB 사용자에게는 해당 사용자와 연관된 권한 목록을 검색하기 위해 사용할 수 있는 PermissionsLink 속성이 있습니다.

DocumentDB 권한 리소스는 DocumentDB 사용자와 연관됩니다. 각 사용자는 0개 이상의 DocumentDB 권한을 포함할 수 있습니다. 권한 리소스는 사용자가 특정 응용 프로그램 리소스에 액세스하려고 시도할 때 필요한 보안 토큰에 대한 액세스 권한을 제공합니다. 권한 리소스에서 제공될 수 있는 사용 가능한 액세스 수준은 다음 두 가지입니다.

- 전체: 사용자가 리소스에 대한 모든 권한을 갖습니다.
- 읽기: 사용자가 리소스 내용을 읽을 수만 있고 리소스에 대해 쓰기, 업데이트 또는 삭제 작업을 수행할 수 없습니다.


> [AZURE.NOTE] DocumentDB 저장 프로시저를 실행하기 위해서는 사용자에게 저장 프로시저가 실행되는 컬렉션에 대한 모든 권한이 있어야 합니다.


다음 코드 조각은 권한 리소스를 만들고, 권한 리소스의 리소스 토큰을 읽고, 위에서 생성된 사용자와 사용 권한을 연결시키는 방법을 보여 줍니다.

    // Create a permission.
    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
  docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission); Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
  
컬렉션에 파티션 키를 지정한 다음 컬렉션에 대한 권한을 지정한 경우 문서 및 첨부 파일 리소스는 ResourceLink 외에도 ResourcePartitionKey를 포함해야 합니다.

특정 사용자와 연관된 모든 권한 리소스를 쉽게 가져오기 위해, DocumentDB는 각 사용자 개체에 대해 권한 피드를 사용할 수 있도록 설정합니다. 다음 코드 조각은 위에서 생성된 사용자와 연관된 권한을 검색하고, 권한 목록을 생성하고, 사용자를 대신해서 새 DocumentClient를 인스턴스화하는 방법을 보여 줍니다.

    //Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
      UriFactory.CreateUserUri("db", "myUser"));

    List<Permission> permList = new List<Permission>();
      
    foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);

> [AZURE.TIP] 리소스 토큰은 기본 1시간의 유효한 시간을 갖습니다. 하지만 토큰 수명은 최대 5시간까지 명시적으로 지정할 수 있습니다.

## 다음 단계

- DocumentDB에 대해 자세히 알아보려면 [여기](http://azure.com/docdb)를 클릭하세요.
- 마스터 및 읽기 전용 키 관리에 대해 자세히 알아보려면 [여기](documentdb-manage-account.md)를 클릭합니다.
- DocumentDB 권한 부여 토큰을 생성하는 방법에 대해 자세히 알아보려면 [여기](https://msdn.microsoft.com/library/azure/dn783368.aspx)를 클릭합니다.
 

<!---HONumber=AcomDC_0921_2016-->