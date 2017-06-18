<properties
    pageTitle="테이블 저장소 사용 방법(C++) | Microsoft Azure"
	description="Azure 테이블 저장소, NoSQL 데이터 저장소를 사용하여 클라우드에 구조화된 데이터를 저장합니다."
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

# C++에서 테이블 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## 개요  
이 가이드에서는 Azure 테이블 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C++로 작성되었으며 [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)를 사용합니다. 여기서 다루는 시나리오에는 **테이블 만들기 및 삭제**와 **테이블 엔터티 작업**이 포함됩니다.

>[AZURE.NOTE] 이 가이드는 Azure Storage Client Library for C++ 버전 1.0.0 이상을 대상으로 합니다. 권장되는 버전은 Storage Client Library 2.2.0이며, [NuGet](http://www.nuget.org/packages/wastorage) 또는 [GitHub](https://github.com/Azure/azure-storage-cpp/)를 통해 사용 가능합니다.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## C++ 응용 프로그램 만들기  
이 가이드에서는 C++ 응용 프로그램 내에서 실행할 수 있는 저장소 기능을 사용합니다. 이 기능을 사용하려면, Azure Storage Client Library for C++를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들어야 합니다.

Azure Storage Client Library for C++를 설치하려면 다음 메서드를 사용할 수 있습니다.

-	**Linux:** [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) 페이지의 지침을 따릅니다.
-	**Windows:** Visual Studio에서 **도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 클릭합니다. [NuGet 패키지 관리자 콘솔](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)에 다음 명령을 입력하고 Enter 키를 누릅니다.

		Install-Package wastorage

## 테이블 저장소에 액세스하도록 응용 프로그램 구성  
테이블에 액세스하기 위해 Azure 저장소 API를 사용하려는 C++ 파일의 맨 위에 다음 include 문을 추가합니다.

	#include "was/storage_account.h"
	#include "was/table.h"

## Azure 저장소 연결 문자열 설정  
Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램을 실행할 때 다음과 같은 형식의 저장소 연결 문자열을 제공해야 합니다. *AccountName* 및 *AccountKey* 값에 대해 [Azure 포털](https://portal.azure.com)에 나열된 저장소 계정으로 저장소 계정 이름 및 저장소 액세스 키를 사용합니다. 저장소 계정 및 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 정보](storage-create-storage-account.md)를 참조하세요. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

	// Define the connection string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

로컬 Windows 기반 컴퓨터에서 응용 프로그램을 테스트하려면 [Azure SDK](https://azure.microsoft.com/downloads/)와 함께 설치된 Azure [저장소 에뮬레이터](storage-use-emulator.md)를 사용할 수 있습니다. 저장소 에뮬레이터는 로컬 개발 컴퓨터에서 사용할 수 있는 Azure Blob, 큐 및 테이블 서비스를 시뮬레이션하는 유틸리티입니다. 다음 예제에서는 로컬 저장소 에뮬레이터에 연결 문자열을 포함할 수 있도록 정적 필드를 선언하는 방법을 보여줍니다.

	// Define the connection string with Azure storage emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Azure 저장소 에뮬레이터를 시작하려면 **시작** 단추를 클릭하거나 Windows 키를 누릅니다. **Azure 저장소 에뮬레이터** 입력을 시작한 다음 응용 프로그램 목록에서 **Microsoft Azure 저장소 에뮬레이터**를 선택합니다.

다음 샘플에서는 저장소 연결 문자열을 가져오기 위해 위의 두 메서드 중 하나를 사용한 것으로 가정합니다.

## 연결 문자열 검색  
**cloud\_storage\_account** 클래스를 사용하여 저장소 계정 정보를 나타낼 수 있습니다. 저장소 연결 문자열에서 저장소 계정 정보를 검색하려면 구문 분석 메서드를 사용할 수 있습니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

다음으로, 테이블 및 테이블 저장소 서비스 내에 저장된 엔터티에 대한 참조 개체를 가져올 수 있도록 **cloud\_table\_client** 클래스에 대한 참조를 가져옵니다. 다음 코드는 위에서 검색한 저장소 계정 개체를 사용하여 **cloud\_table\_client** 개체를 만듭니다.

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## 테이블 만들기
**cloud\_table\_client** 개체를 사용하면 테이블 및 엔터티에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **cloud\_table\_client** 개체를 만든 다음 이 개체를 사용하여 새 테이블을 만듭니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## 테이블에 엔터티 추가
테이블에 엔터티를 추가하려면 새 **table\_entity** 개체를 만들어 **table\_operation::insert\_entity**에 전달합니다. 다음 코드는 고객의 이름을 행키로 사용하고 고객의 성을 파티션 키로 사용합니다. 엔터티의 파티션과 행 키가 결합되어 테이블에서 엔터티를 고유하게 식별합니다. 동일한 파티션 키를 가진 엔터티는 다른 파티션 키를 가진 엔터티보다 더 빨리 쿼리할 수 있지만 다양한 파티션 키를 사용하면 병렬 작업 확장성이 커집니다. 자세한 내용은 [Microsoft Azure 저장소 성능 및 확장성 검사 목록](storage-performance-checklist.md)을 참조하세요.

다음 코드에서는 **table\_entity**의 새 인스턴스를 저장될 일부 고객 데이터와 함께 만듭니다. 그런 다음**table\_operation::insert\_entity**를 호출하여 **table\_operation** 개체를 만들고 엔티티를 테이블에 삽입하고, 새 테이블 엔티티와 연결합니다. 마지막으로, **cloud\_table** 개체에서 execute 메서드를 호출합니다. 새로운 **table\_operation**은 "people" 테이블에 새 고객 엔터티를 삽입하는 저장소 서비스에 요청을 보냅니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(U("Harp"), U("Walter"));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

	properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## 엔터티 일괄 삽입
하나의 쓰기 작업으로 테이블 서비스에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 **table\_batch\_operation** 개체를 만든 다음, 이 개체에 3개의 삽입 작업을 추가합니다. 각 삽입 작업을 추가하기 위해 새 엔터티 개체를 만들고 값을 설정한 후 insert 메서드를 **table\_batch\_operation** 개체에 대해 호출하여 해당 엔터티를 새로운 삽입 작업과 연결합니다. 그런 다음 **cloud\_table.execute**가 호출되어 작업을 실행합니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
	properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(U("Smith"), U("Ben"));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
	properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(U("Smith"), U("Denise"));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
	properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

일괄 작업에 대해 유의할 사항은 다음과 같습니다.

-	최대 100개의 삽입, 삭제, 병합, 바꾸기, 삽입 또는 병합 및 삽입 또는 바꾸기 작업을 임의로 조합하여 단일 일괄 작업으로 수행할 수 있습니다.
-	검색 작업이 일괄 작업의 유일한 작업이면 일괄 작업에 검색 작업이 포함될 수 있습니다.
-	단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
-	일괄 작업은 4MB 데이터 페이로드로 제한됩니다.

## 파티션의 모든 엔터티 검색
테이블에서 파티션의 모든 엔터티를 쿼리하려면 **table\_query** 개체를 사용합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Print the fields for each customer.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

이 예의 쿼리는 필터 조건과 일치하는 모든 엔터티를 제공합니다. 큰 테이블 및 테이블 엔터티를 자주 다운로드할 필요가 있다면 데이터를 Azure 저장소 Blob에 대신 저장하는 것이 좋습니다.

## 파티션의 엔터티 범위 검색
파티션의 모든 엔터티를 쿼리하지 않으려면 파티션 키 필터를 행 키 필터와 결합하여 범위를 지정할 수 있습니다. 다음 코드 예제에서는 두 개의 필터를 사용하여 행 키(이름)가 알파벳에서 'E'보다 앞에 오는 문자로 시작하는 'Smith' 파티션의 모든 엔터티를 가져온 다음 쿼리 결과를 출력합니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

## 단일 엔터티 검색
단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **table\_operation::retrieve\_entity**를 사용하여 고객 'Jeff Smith'를 지정합니다. 이 메서드는 컬렉션 대신 하나의 엔터티만 반환하며, 반환된 값은 **table\_result**입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 테이블 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
		<< U(", Property1: ") << properties.at(U("Email")).string_value()
		<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## 엔터티 바꾸기
엔터티를 바꾸려면 테이블 서비스에서 검색하고 엔터티 개체를 수정한 다음 변경 내용을 다시 테이블 서비스에 다시 저장합니다. 다음 코드에서는 기존 고객의 전화 번호와 메일 주소를 변경합니다. **table\_operation:: insert\_entity**를 호출하는 대신, 이 코드에서는 **table\_operation:: replace\_entity**를 사용합니다. 이렇게 하면 서버의 엔터티가 검색된 후 변경되어 작업이 실패하는 경우를 제외하고 서버에서 엔터티가 완전히 바뀝니다. 이러한 실패는 응용 프로그램이 검색 및 업데이트 사이에 다른 응용 프로그램 구성 요소에 의해 변경된 내용을 실수로 덮어쓰는 것을 방지합니다. 이 실패를 올바르게 처리하려면 엔터티를 다시 검색하고 변경한 다음(유효한 경우) 다른 **table\_operation::replace\_entity** 작업을 수행합니다. 다음 섹션에서는 이 동작을 재정의하는 방법을 보여 줍니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## 엔터티 삽입 또는 바꾸기
엔터티가 서버에서 검색된 이후 변경된 경우 **table\_operation::replace\_entity** 작업이 실패합니다. 또한 **table\_operation:: replace\_entity**를 성공적으로 수행하려면 먼저 서버에서 엔티티를 검색해야 합니다. 그러나 서버에 엔터티가 있는지 알지 못하며 저장된 현재 값이 부적절하여 업데이트로 모두 덮어써야 하는 경우도 있습니다. 이 작업을 수행하려면 **table\_operation:: insert\_or\_replace\_entity** 작업을 사용합니다. 이 작업은 마지막 업데이트가 수행된 시기에 관계없이 엔터티가 없는 경우 삽입하고, 엔터티가 있는 경우 바꿉니다. 다음 코드 예제에서는 Jeff Smith에 대한 고객 엔터티가 여전히 검색되지만 **table\_operation::insert\_or\_replace\_entity**를 사용하여 서버에 다시 저장됩니다. 검색 및 업데이트 작업 사이의 모든 엔터티 업데이트를 덮어씁니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert-or-replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the Table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);

## 엔터티 속성 하위 집합 쿼리  
테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 다음 코드의 쿼리는 **table\_query::set\_select\_columns** 메서드를 사용하여 테이블에 있는 엔터티의 메일 주소만 반환합니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and select only the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Display the results.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

		const azure::storage::table_entity::properties_type& properties = it->properties();
		for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
		{
			std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
		}

		std::wcout << std::endl;
	}

>[AZURE.NOTE] 모든 속성을 검색하는 것보다 엔터티의 몇 가지 속성을 쿼리하는 것이 더 효율적인 작업입니다.

## 엔터티 삭제
엔터티를 검색한 다음 쉽게 삭제할 수 있습니다. 엔터티가 검색되면 **table\_operation:: delete\_entity**를 삭제할 엔터티와 함께 호출합니다. 그런 다음 **cloud\_table.execute** 메서드를 호출합니다. 다음 코드를 검색하고 "Smith"의 파티션 키와 "Jeff"의 행 키가 있는 엔터티를 삭제합니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  

## 테이블 삭제
마지막으로, 다음 코드 예제에서는 저장소 계정에서 테이블을 삭제합니다. 삭제된 테이블은 삭제 후 일정 기간 동안 다시 만들 수 없습니다.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the Table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## 다음 단계
이제 테이블 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 Azure 저장소에 대해 알아보세요.

-	[C++에서 Blob 저장소를 사용하는 방법](storage-c-plus-plus-how-to-use-blobs.md)
-	[C++에서 큐 저장소를 사용하는 방법](storage-c-plus-plus-how-to-use-queues.md)
-	[C++에서 Azure 저장소 리소스 나열](storage-c-plus-plus-enumeration.md)
-	[C++용 Storage Client Library 참조(영문)](http://azure.github.io/azure-storage-cpp)
-	[Azure 저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0921_2016-->