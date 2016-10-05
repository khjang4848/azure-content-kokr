<properties
	pageTitle="Microsoft Azure 저장소용 Java를 사용하는 클라이언트 쪽 암호화 | Microsoft Azure"
	description="Java용 Azure 저장소 클라이언트 라이브러리는 Azure 저장소 응용 프로그램의 보안을 최대화하기 위해 클라이언트 쪽 암호화 및 Azure 키 자격 증명 모음과의 통합을 지원합니다."
	services="storage"
	documentationCenter="java"
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
	ms.author="dineshm;robinsh"/>


# Microsoft Azure 저장소용 Java를 이용한 클라이언트쪽 암호화   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## 개요  

[Java용 Azure 저장소 클라이언트 라이브러리](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)는 Azure 저장소에 업로드하기 전에 클라이언트 응용 프로그램 내부에서 데이터를 암호화하고 클라이언트로 다운로드하는 동안 데이터 암호를 해독하는 기능을 지원합니다. 라이브러리 또한 저장소 계정 키 관리를 위해 Azure [키 자격 증명 모음](https://azure.microsoft.com/services/key-vault/)과의 통합을 지원합니다.

## 봉투(Envelope) 기술을 통해 암호화 및 암호 해독    
암호화 및 암호 해독 프로세스는봉투(Envelope) 기법을 따릅니다.

### 봉투(Envelope) 기술을 통해 암호화  
암호화는 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1.	Azure 저장소 클라이언트 라이브러리는 1회용 대칭 키인 콘텐츠 암호화 키(CEK)를 생성합니다.

2.	사용자 데이터는 이 CEK를 사용하여 암호화됩니다.

3.	그런 다음 키 암호화 KEK를 사용하여 CEK를 래핑(암호화)합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 Azure 키 자격 증명 모음에 저장됩니다. 저장소 클라이언트 라이브러리 자체는 KEK에 액세스할 수 없습니다. 라이브러리는 자격 증명 모음에서 제공되는 키 래핑 알고리즘을 호출합니다. 사용자는 원하는 경우 키 래핑/래핑 해제를 위해 사용자 지정 공급자를 사용하도록 선택할 수 있습니다.

4.	그런 다음 암호화된 데이터를 Azure 저장소 서비스에 업로드합니다. 일부 추가 암호화 메타데이터와 함께 래핑된 키에 메타 데이터로(Blob) 저장 되거나 암호화 된 데이터 (메시지 큐 및 테이블 엔터티)와 보관 합니다.

### 봉투(Envelope) 기술을 통해 암호해독  
암호해독은 봉투(Envelope) 기술을 통해 다음과 같은 방식으로 작동합니다.

1.	클라이언트 라이브러리는 사용자가 키 암호화 키를 로컬로 또는 Azure 키 자격증명모음으로 관리한다고 가정합니다. 사용자는 암호화에 사용된 특정 키를 알 필요가 없습니다. 대신 키를 서로 다른 키 식별자를 확인 하는 키 확인자 수를 설정하고 사용 합니다.

2.	클라이언트 라이브러리는 서비스에 저장된 모든 암호화 자료와 함께 암호화된 데이터를 다운로드 합니다.

3.	래핑된 콘텐츠 암호화 키 (CEK)는 키 암호화 키를(KEK) 사용하여 래핑해제(암호 해독)합니다. 여기서 다시, 클라이언트 라이브러리는 KEK에 대한 액세스권한이 없습니다. 단순히 사용자 지정 또는 래핑 해제 알고리즘 키 자격 증명 모음 공급자를 호출합니다.

4.	그리고 콘텐츠 암호화 키 (CEK)는 암호화 된 사용자 데이터의 암호를 해독 하는데 사용 됩니다.

## 암호화 메커니즘  
저장소 클라이언트 라이브러리는 사용자 데이터를 암호화하기 위해 [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다. 특히, AES를 이용한 [CBC(암호화 블록 체인)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) 모드입니다. 각 서비스는 하는 일이 각각 다르므로 여기서 이것들을 살펴볼 것입니다.

### Blob  
클라이언트 라이브러리는 현재 전체 blob 암호화만 지원합니다. 특히 사용자가 **upload*** 메서드 또는 **openOutputStream** 메서드를 사용할 때 암호화가 지원됩니다. 다운로드는 전체 및 범위 다운로드가 모두 지원됩니다.

암호화 하는 동안 클라이언트 라이브러리는 임의 IV (Initialization Vector) 32 바이트의 임의의 콘텐츠 암호화 키 (CEK)와 함께 16 바이트를 생성 하고 이 정보를 사용 여 blob 데이터의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터 서비스에서 암호화 된 blob과 함께 메타 데이터를 blob으로 저장합니다.

>[AZURE.WARNING] blob에 대해 고유 메타데이터를 편집하거나 업로드 할 경우, 메타데이타가 유지되는지 확인하세요. 이 메타 데이터 없이 새 메타 데이터를 업로드 하는 경우에는 래핑된 CEK, IV 및 기타 메타 데이터가 손실 되고 blob 콘텐츠를 절대로 다시 검색할 수 없습니다.

암호화 BLOB 다운로드는 **download*/openInputStream** 편리한 메서드를 사용한 전체 BLOB의 콘텐츠 검색을 포함합니다. 래핑된 CEK는 IV (blob 메타 데이터로 저장된 경우)와 함께 암호해독되고 사용되어 지며 해독된 데이터가 사용자에게 돌아갑니다.

암호화된 BLOB 내에서 임의의 범위를 다운로드할 경우(**DownloadRange*** 메서드) 요청된 범위를 성공적으로 암호를 해독하는 데 사용되는 소량의 추가 데이터를 얻기 위해 사용자가 제공하는 범위가 조정됩니다.

이 스키마를 사용하여 모든 blob 유형(블록 blob, 페이지 blob 및 추가 blob)을 암호화/암호 해독할 수 있습니다.

### 큐  
큐 메시지의 모든 형식이 될 수, 있으므로 클라이언트 라이브러리는 IV (Initialization Vector) 및 암호화 된 콘텐츠 암호화 키 (CEK) 메시지 텍스트에 포함 된 사용자 지정 형식을 정의 합니다.

암호화 하는 동안 클라이언트 라이브러리는 32 바이트의 임의 CEK 함께 16 바이트의 임의 IV를 생성하고 이 정보를 사용하여 큐 메시지 텍스트의 봉투 (envelope) 암호화를 수행 합니다. 래핑된 CEK 및 일부 추가 암호화 메타 데이터를 암호화 된 큐 메시지에 추가합니다. (아래 참조)이 수정 된 메시지는 서비스에 저장 됩니다.

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

암호를 해독 하는 동안, 래핑된 키는 큐 메시지에서 추출되고 래핑이 해제됩니다. IV 또한 큐메시지에서 추출되고 큐 메시지 데이터를 암호해독하기 위해 래핑해제된 키와 함께 사용 됩니다. 참고로 암호화 메타데이터는 작아야하므로(500바이트 이하),큐 메시지는 64KB의 제한이 있어야만 영향을 관리 할 수 있습니다.

### 테이블  
클라이언트 라이브러리는 작업 삽입 및 삭제의 엔터티 속성 암호화를 지원합니다.

>[AZURE.NOTE] 병합은 현재 지원 되지 않습니다. 속성의 하위 집합은 이전에 다른 키를 사용하여 암호화됐을 가능성이 있기 때문에 단순히 새로운 속성을 병합하는 것과 메타데이터를 업데이트 하는 것은 데이터 손실을 불러 올 수 있습니다. 서비스에서 기존 엔터티를 읽을 수 있는 추가 서비스 호출을 수행 하거나 속성 당 새 키를 사용하는 것 모두에 성능상의 이유로 적합하지 않습니다.

테이블 데이터 암호화는 다음과 같이 작동합니다.

1.	사용자는 암호화할 속성을 지정합니다.

2.	클라이언트 라이브러리는 모든 엔터티에 대해 16바이트의 임의 IV(Initialization Vector)와 함께 32바이트의 임의 CEK(콘텐츠 암호화 키)를 생성하고 속성당 새 IV를 파생하여 암호화해야 하는 개별적인 속성에 대해 봉투(envelope) 암호화를 수행합니다. 암호화된 속성은 이진 데이터로 저장됩니다.

3.	래핑된 CEK 및 일부 추가 암호화 메타 데이터는 다음 추가 예약 된 두 가지 속성으로 저장 됩니다. 첫 번째 예약 된 속성 (\_ClientEncryptionMetadata1)은 IV, 버전, 래핑된 키의 정보를 담고 있는 문자열 속성입니다. 또 다른 예약된 속성(\_ClientEncryptionMetadata2)은 암호화된 속성에 대한 정보를 담고 있는 이진 속성입니다. 이 두 번째 속성(\_ClientEncryptionMetadata2)의 정보는 자체적으로 암호화됩니다.

4.	이 추가적인 예약 속성이 암호화에 필요하기 때문에 사용자들은 252가지 사용자 지정 속성 대신 250가지를 갖게 됩니다. 엔터티의 총 크기는 1MB 미만 이어야 합니다.

	문자열 속성만 암호화 할 수 있다는 것을 참고하세요. 다른 유형의 속성이 암호화 된 경우, 문자열로 변환합니다. 암호화된 문자열은 서비스에 이진 속성으로 저장되고 암호 해독 후에는 다시 문자열로 변환됩니다.

	테이블의 경우, 암호화 정책 외에도 사용자가 암호화할 속성을 지정해야 합니다. 이것은 특성(TableEntity에서 파생 되는 POCO 엔터티)을 지정[Encrypt]하거나 암호화 해결 프로그램 요청 옵션에서 수행할 수 있습니다. 암호화 해결 프로그램은 파티션 키, 행 키 및 속성 이름을 취하고 해당 속성을 암호화해야 하는지 여부를 나타내는 부울 값을 반환하는 대리자입니다. 암호화 하는 동안 클라이언트 라이브러리는 네트워크에 쓰는 동안 속성을 암호화 해야 하는지 여부를 결정하는데 이 정보를 사용합니다. 대리자 속성은 암호화 하는 방법 논리의 가능성도 제공 합니다. (예를 들어 X의 경우, A 속성을 암호화하고 그렇지 않은 경우 A와 B 속성을 암호화) 읽기 또는 엔터티를 쿼리 하는 동안은 이정보가 필요없다는 것을 참고하세요.

### 배치 작업  
일괄 처리 작업에서, 같은 kek가 배치 작업 안의 모든 행간에 사용되는데, 클라이언트 라이브러리는 배치 작업당 오직 하나의 옵션개체(하나의 정책/kek 때문에 )만 허용하기 때문입니다. 그러나 클라이언트 라이브러리는 배치 안에 새로운 임의 IV와 행 당 임의 CEK를 내부적으로 만듭니다. 사용자가 암호화 해결 프로그램에 이동작을 정의하여 배치의 모든 작업에 대해 암호화 할 다른 속성들을 선택할 수 있습니다.

### 쿼리  
쿼리 작업을 수행 하려면 결과 집합에 있는 모든 키를 확인할 수 있는 키 확인자를 지정 해야 합니다. 공급자에는 쿼리 결과에 포함 된 엔터티를 확인할 수 없으면, 클라이언트 라이브러리는 오류를 throw 합니다. 서버 쪽 프로젝션을 수행하는 모든 쿼리에 대해 클라이언트 라이브러리는 선택한 열에 기본적으로 특별한 암호 메타데이터 속성(\_ClientEncryptionMetadata1 및 \_ClientEncryptionMetadata2)을 추가합니다.

## Azure 키 자격 증명 모음  
Azure 키 자격 증명 모음은 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure 키 자격 증명 모음을 사용하여, 사용자는 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키, PFX 파일 및 암호)을 암호화하여 하드웨어 보안 모듈(HSM)로 보호된 키를 사용합니다. 자세한 내용은 [Azure 주요 자격 증명 모음이란?](../key-vault/key-vault-whatis.md)을 참조하세요.

저장소 클라이언트 라이브러리는 Azure 내에서 키를 관리 하기 위한 공통 프레임 워크를 제공 하기 위해 키 자격 증명 모음 핵심 라이브러리를 사용 합니다. 사용자는 또한 키 자격 증명 모음 확장 라이브러리를 사용하여 추가적인 이점을 제공을 받습니다. 이 확장 라이브러리는 간단하고 원활한 대칭/RSA 로컬 및 집계와 캐싱같은 클라우드 키 공급자 관련 유용한 기능을 제공합니다. .

### 인터페이스 및 종속성  
세 가지 키 자격증명 모음 패키지가 있습니다.

- azure-keyvault-core는 IKey 및 IKeyResolver 포함합니다. 어떤 부속품도 없는 작은 패키지입니다. Java용 저장소 클라이언트 라이브러리는 이를 종속성으로 정의합니다.
- azure-keyvault는 키 자격 증명 모음 REST 클라이언트를 포함하고 있습니다.
- azure-keyvault-extensions는 암호화 알고리즘 및 RSAKey와 SymmetricKey의 구현이 포함된 확장 코드를 포함하고 있습니다. 코어 및 KeyVault 네임 스페이스에 의존하고 (여러 키 공급자를 사용하여 사용자가 원하는) 경우 집계 해결 프로그램 및 캐싱 키 해결 프로그램을 정의 하는 기능을 제공 합니다. 비록 저장소 클라이언트 라이브러리가 이 패키지에 직접적으로 의존하지 않지만, 사용자가 그들의 키를 저장하거나 로컬과 클라우드 암호화 공급자를 소비하는 키 자격증명 모음 확장을 사용에 Azure 키 자격증명 모음을 사용하고 싶을 때는 이 패키지가 필요합니다.

  키 자격증명모음은 고급 가치 마스터키로 고안되었으며 키 자격증명 모음당 스로틀 한계는 이것을 염두에 두고 만들어졌습니다. 키 자격 증명 모음을 사용하여 클라이언트측 암호화를 수행할 때 모델을 선호 로컬로 대칭 마스터 키 암호 키 자격 증명 모음에로 저장 하 고 캐시를 사용 하는 것입니다. 다음 작업을 수행합니다.

1.	암호를 오프라인으로 만들고 키 자격 증명 모음에 업로드 합니다.

2.	비밀의 기본 식별자를 현재 버전의 암호화에 대한 암호를 풀기 위해 매개변수로 사용하고 이 정보를 로컬로 캐시합니다. CachingKeyResolver를 사용합니다. 사용자는 자체 캐싱 논리가 구현되지 않는 것을 예상합니다.

3.	암호화 정책을 생성하는 동안 캐싱 확인자를 입력으로 사용합니다. 주요 자격 증명 모음 사용법에 대한 자세한 내용은 암호화 코드 샘플에서 찾을 있습니다. <fix URL>

## 모범 사례  
암호화 지원은 Java용 저장소 클라이언트 라이브러리에만 사용할 수 있습니다.

>[AZURE.IMPORTANT] 클라이언트 쪽 암호화를 사용할 때는 이러한 중요점을 유의하세요.
>  
>- 암호화된 blob에서 읽거나 여기에 쓸 때는 전체 blob 업로드 명령 및 범위/전체 blob 다운로드 명령을 사용하세요. 블록 배치, 블록 목록 배치, 페이지 쓰기, 페이지 지우기 또는 블록 추가와 같은 프로토콜 작업을 사용하여 암호화된 blob에 쓰지 않도록 합니다. 그렇지 않으면 암호화된 blob이 손상되어 읽지 못하게 될 수 있습니다.
>
>- 테이블의 경우에는 유사한 제약 조건이 있습니다. 암호화 메타데이터를 업데이트하지 않고 암호화된 속성을 업데이트하지 않도록 주의해야 합니다.
>
>- 암호화된 blob에서 메타데이터를 설정하는 경우 메타데이터의 설정은 가산적이 아니므로 암호 해독에 필요한 암호화 관련 메타데이터를 덮어쓸 수도 있습니다. 이것은 스냅숏에 대해서 마찬가지입니다. 암호화된 blob의 스냅숏을 생성하는 동안 메타데이터를 지정하지 않도록 하세요. 메타데이터가 설정되어야 하는 경우 먼저 **downloadAttributes** 메서드를 호출하여 현재 암호화 메타데이터를 가져오고, 메타데이터가 설정되는 동안에는 동시 쓰기를 피합니다.
>
>- 암호화된 데이터에만 작동해야 하는 사용자의 기본 요청 옵션에는 **requireEncryption** 플래그를 사용하도록 설정합니다. 자세한 내용은 다음을 참조하세요.

## 클라이언트 API / 인터페이스  
EncryptionPolicy 개체를 만드는 동안 사용자만 키를 공급 (IKey 구현), 확인자만 키를 공급 (IKeyResolver 구현) 또는 둘 모두 키를 공급. IKey 래핑/래핑 해제에 대한 논리를 제공하고 키 식별자를 사용하여 식별 되는 기본 키 유형입니다. IKeyResolver 키는 암호 해독 프로세스에서 키를 해독하기 위해 사용됩니다. 키 식별자가 제공하는 IKey를 반환하는 ResolveKey 메서드를 정의 합니다. 이것은 사용자에게 여러 위치에서 관리되는 여러 키 중 하나를 선택할 수 있게 합니다.

- 암호화는 키가 항상 사용되고, 키가 없으면 오류가 발생합니다.
- 암호를 해독하려면
	- 키 확인자는 키를 가져오기 위해 지정된 경우 호출됩니다. 확인자를 지정 하 고 키 식별자에 대한 매핑이 없는 경우, 오류가 전달됩니다.
	- 확인자는 지정하지 않고 키는 지정한 경우 해당 식별자가 필요한 키 식별자와 일치하는 경우 키가 사용됩니다. 식별자가 일치하지 않으면 오류가 throw됩니다.

	  [암호화 샘플](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)<fix URL>은 주요 자격 증명 모음 통합과 함께 BLOB, 큐 및 테이블에 대한 보다 자세한 종단 간 시나리오를 보여 줍니다.

### RequireEncryption 모드  
사용자는 모든 업로드 및 다운로드를 암호화해야 할 경우 작업 모드를 선택적으로 사용하도록 설정할 수 있습니다. 이 모드에서는 클라이언트에서 암호화 정책 없이 데이터를 업로드하거나 서비스에서 암호화되지 않은 데이터를 다운로드하려고 하면 실패합니다. 요청 옵션 개체의 **requireEncryption** 플래그가 이 동작을 제어합니다. 응용 프로그램이 Azure 저장소에 저장된 모든 개체를 암호화하는 경우 서비스 클라이언트 개체에 대한 기본 요청 옵션에서 **requireEncryption** 속성을 설정할 수 있습니다.

예를 들어 모든 BLOB 작업에 대한 암호화가 해당 클라이언트 개체를 통해 수행되도록 하려면 **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)**을 사용합니다.

### Blob 서비스 암호화  
**BlobEncryptionPolicy** 개체를 만들고 요청 옵션에서 설정합니다(**DefaultRequestOptions**를 사용하여 API 기준으로 또는 클라이언트 수준에서). 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

	// Create the IKey used for encryption.
	RsaKey key = new RsaKey("private:key1" /* key identifier */);

	// Create the encryption policy to be used for upload and download.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

	// Set the encryption policy on the request options.
	BlobRequestOptions options = new BlobRequestOptions();
	options.setEncryptionPolicy(policy);

	// Upload the encrypted contents to the blob.
	blob.upload(stream, size, null, options, null);

	// Download and decrypt the encrypted contents from the blob.
	ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
	blob.download(outputStream, null, options, null);

### 큐 서비스 암호화  
**QueueEncryptionPolicy** 개체를 만들고 요청 옵션에서 설정합니다(**DefaultRequestOptions**를 사용하여 API 기준으로 또는 클라이언트 수준에서). 다른 모든 요소에서 처리 되는 클라이언트 라이브러리는 내부적으로 처리됩니다.

	// Create the IKey used for encryption.
	RsaKey key = new RsaKey("private:key1" /* key identifier */);

	// Create the encryption policy to be used for upload and download.
	QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

	// Add message
	QueueRequestOptions options = new QueueRequestOptions();
	options.setEncryptionPolicy(policy);

	queue.addMessage(message, 0, 0, options, null);

	// Retrieve message
	CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### 테이블 서비스 암호화  
암호화 정책을 생성하고 요청 옵션에 설정하는 것 외에도 사용자는 **TableRequestOptions**에서 **EncryptionResolver**를 지정하거나 엔터티의 getter와 setter에 대해 [Encrypt] 특성을 설정해야 합니다.

### 확인자를 사용하여  

	// Create the IKey used for encryption.
	RsaKey key = new RsaKey("private:key1" /* key identifier */);

	// Create the encryption policy to be used for upload and download.
	TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

	TableRequestOptions options = new TableRequestOptions()
	options.setEncryptionPolicy(policy);
	options.setEncryptionResolver(new EncryptionResolver() {
	    public boolean encryptionResolver(String pk, String rk, String key) {
        	if (key == "foo")
        	{
	            return true;
        	}
        	return false;
    	}
	});

	// Insert Entity
	currentTable.execute(TableOperation.insert(ent), options, null);

	// Retrieve Entity
	// No need to specify an encryption resolver for retrieve
	TableRequestOptions retrieveOptions = new TableRequestOptions()
	retrieveOptions.setEncryptionPolicy(policy);

	TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
	TableResult result = currentTable.execute(operation, retrieveOptions, null);

### 특성을 사용하여  
앞서 설명한 것처럼 엔터티가 TableEntity를 구현하는 경우 **EncryptionResolver**를 지정하는 대신 [Encrypt] 특성으로 속성 getter와 setter를 데코레이트할 수 있습니다.

	private string encryptedProperty1;

	@Encrypt
	public String getEncryptedProperty1 () {
	    return this.encryptedProperty1;
	}

	@Encrypt
	public void setEncryptedProperty1(final String encryptedProperty1) {
	    this.encryptedProperty1 = encryptedProperty1;
	}

## 암호화 및 성능  
저장소 데이터를 암호화하면 추가 성능 오버헤드가 발생합니다. 콘텐츠 키 및 IV를 생성해야 하고, 콘텐츠 자체를 암호화해야 하고, 추가 메타데이터의 형식을 지정한 후 업로드해야 합니다. 이 오버헤드는 암호화되는 데이터의 양에 따라 달라집니다. 고객은 항상 개발 중에 응용 프로그램 성능을 테스트하는 것이 좋습니다.

## 다음 단계  

- [Java Maven 패키지에 대한 Azure 저장소 클라이언트 라이브러리](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) 다운로드
- [GitHub에서 Java 소스 코드용 Azure 저장소 클라이언트 라이브러리](https://github.com/Azure/azure-storage-java) 다운로드
- Java Maven 패키지에 대한 Azure 주요 자격 증명 Maven 라이브러리 다운로드
	- [코어](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) 패키지
	- [클라이언트](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) 패키지
- [Azure 주요 자격 증명 모음 설명서](../key-vault/key-vault-whatis.md) 참조

<!---HONumber=AcomDC_0921_2016-->