<properties
	pageTitle="Node.js에서 Blob 저장소를 사용하는 방법 | Microsoft Azure"
	description="Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
    ms.date="08/11/2016"
	ms.author="micurd"/>



# Node.js에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## 개요

이 문서에서는 Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. 여기서 다루는 시나리오는 Blob을 업로드, 나열, 다운로드 및 삭제하는 방법을 포함합니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Node.js 응용 프로그램 만들기

Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Azure 앱 서비스에서 Node.js 웹앱 만들기], [Azure 클라우드 서비스에 Node.js 응용 프로그램 빌드 및 배포](Windows PowerShell 사용) 또는 [Web Matrix를 사용하여 Azure에 Node.js 웹앱 빌드 및 배포]를 참조하세요.

## 저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js용 Azure 저장소 SDK가 필요합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure-storage**를 입력합니다. 명령 출력은 다음 코드 예제와 비슷합니다.

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 해당 폴더 내에서 저장소에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure-storage** 패키지를 찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의 **server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure-storage');

## Azure 저장소 연결 설정

Azure 모듈은 및 또는 환경 변수를 읽고 `AZURE_STORAGE_ACCOUNT``AZURE_STORAGE_ACCESS_KEY`Azure 저장소 계정에 연결하는 데 `AZURE_STORAGE_CONNECTION_STRING`필요한 정보를 확인합니다. 이러한 환경 변수가 설정되어 있지 않은 경우 **createBlobService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 웹앱의 [Azure 포털](https://portal.azure.com)에서 환경 변수를 설정하는 방법에 대한 예제는 [Azure 테이블 서비스를 사용하여 Node.js 웹앱]을 참조하세요.

## 컨테이너 만들기

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 다음 코드는 **BlobService** 개체를 만듭니다. **server.js**의 위쪽에 다음을 추가합니다.

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] **createBlobServiceAnonymous**를 사용하고 호스트 주소를 제공하여 익명으로 Blob에 액세스할 수 있습니다. 예를 들면 `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`를 사용합니다.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

새 컨테이너를 만들려면 **createContainerIfNotExists**를 사용합니다. 다음 코드 예제에서는 'mycontainer'라는 이름의 새 컨테이너를 만듭니다.

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
	    if(!error){
	      // Container exists and is private
	    }
	});

컨테이너를 새로 작성하는 경우 `result.created`는 true입니다. 컨테이너가 이미 있는 경우 `result.created`는 false입니다. `response`에는 컨테이너의 ETag 정보와 같은 작업 관련 정보가 포함됩니다.

### 컨테이너 보안

기본적으로 새 컨테이너는 개인 컨테이너이며 익명으로 액세스할 수 없습니다. 컨테이너를 공용으로 만들어 익명으로 액세스할 수 있게 하려면 컨테이너의 액세스 수준을 **Blob** 또는 **컨테이너**로 설정할 수 있습니다.

* **blob** - 이 컨테이너 내의 Blob 콘텐츠와 메타데이터에는 익명 읽기 액세스가 허용되지만, 컨테이너 내의 모든 Blob 나열과 같은 컨테이너 메타데이터에는 익명 읽기 권한이 허용되지 않습니다.

* **컨테이너** - Blob 콘텐츠 및 메타데이터와 컨테이너 메타데이터에 대한 익명 읽기 권한을 허용합니다.

다음 코드 예제에서는 액세스 수준을 **Blob**으로 설정하는 방법을 보여 줍니다.

	blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
	    if(!error){
	      // Container exists and allows
	      // anonymous read access to blob
	      // content and metadata within this container
	    }
	});

또는 **setContainerAcl**로 액세스 수준을 지정하여 컨테이너의 액세스 수준을 수정할 수 있습니다. 다음 코드 예제에서는 액세스 수준을 container로 변경합니다.

	blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
	    // Container access level set to 'container'
	  }
	});

result 값에는 컨테이너의 **ETag** 정보와 같은 작업 관련 정보가 포함됩니다.

### 필터

**BlobService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동 재시도 등을 포함할 수 있습니다. 필터는 시그니쳐가 있는 메서드를 구현하는 개체입니다.

	function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

	function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음은 **ExponentialRetryPolicyFilter**를 사용하는 **BlobService** 개체를 만듭니다.

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## 컨테이너에 Blob 업로드

블록 Blob, 페이지 Blob 및 추가 Blob의 세 가지 Blob 유형이 있습니다. 블록 Blob을 사용하면 대용량 데이터를 보다 효율적으로 업로드할 수 있습니다. 추가 Blob은 추가 작업에 최적화되어 있습니다. 페이지 Blob은 읽기/쓰기 작업에 최적화되어 있습니다. 자세한 내용은 [블록 Blob, 추가 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

### 블록 Blob

데이터를 블록 Blob으로 업로드하려면 다음을 사용합니다.

* **createBlockBlobFromLocalFile** - 새 블록 Blob을 만들고 파일 내용을 업로드합니다.

* **createBlockBlobFromStream** - 새 블록 Blob을 만들고 스트림의 내용을 업로드합니다.

* **createBlockBlobFromText** - 새 블록 Blob을 만들고 문자열의 내용을 업로드합니다.

* **createWriteStreamToBlockBlob** - 블록 Blob에 쓰기 스트림을 제공합니다.

다음 코드 예제에서는 **test.txt** 파일의 내용을 **myblob**에 업로드합니다.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

이 메서드에 의해 `result`반환되면 Blob의 **ETag**와 같은 작업 관련 정보가 포함됩니다.

### 추가 Blob

데이터를 추가 Blob으로 업로드하려면 다음을 사용합니다.

* **createAppendBlobFromLocalFile** - 새 추가 Blob을 만들고 파일 내용을 업로드합니다.

* **createAppendBlobFromStream** - 새 추가 Blob을 만들고 스트림 내용을 업로드합니다.

* **createAppendBlobFromText** - 새 추가 Blob을 만들고 문자열 내용을 업로드합니다.

* **createWriteStreamToNewAppendBlob** - 새 추가 Blob을 만든 다음 쓸 스트림을 제공합니다.

다음 코드 예제에서는 **test.txt** 파일의 내용을 **myappendblob**에 업로드합니다.

	blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

기존 추가 Blob에 블록을 추가하려면 다음을 사용합니다.

* **appendFromLocalFile** -기존 추가 Blob에 파일 내용을 추가합니다.

* **appendFromStream** -기존 추가 Blob에 스트림 내용을 추가합니다.

* **appendFromText** -기존 추가 Blob에 문자열 내용을 추가합니다.

* **appendBlockFromStream** -기존 추가 Blob에 스트림 내용을 추가합니다.

* **appendBlockFromText** -기존 추가 Blob에 문자열 내용을 추가합니다.

> [AZURE.NOTE] appendFromXXX API는 불필요한 서버 호출을 방지하기 위해 빠른 오류에 대한 일부 클라이언트 쪽 유효성 검사를 수행합니다. appendBlockFromXXX는 이를 수행하지 않습니다.

다음 코드 예제에서는 **test.txt** 파일의 내용을 **myappendblob**에 업로드합니다.

	blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
	  if(!error){
	    // text appended
	  }
	});


### 페이지 Blob

데이터를 페이지 Blob으로 업로드하려면 다음을 사용합니다.

* **createPageBlob** - 특정 길이의 새 페이지 Blob을 만듭니다.

* **createPageBlobFromLocalFile** - 새 페이지 Blob을 만들고 파일 내용을 업로드합니다.

* **createPageBlobFromStream** - 새 페이지 Blob을 만들고 스트림의 내용을 업로드합니다.

* **createWriteStreamToExistingPageBlob** - 기존 페이지 Blob에 쓰기 스트림을 제공합니다.

* **createWriteStreamToNewPageBlob** - 새 페이지 Blob을 만든 다음 쓸 스트림을 제공합니다.

다음 코드 예제에서는 **test.txt** 파일의 내용을 **mypageblob**에 업로드합니다.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] 페이지 Bobl은 512바이트의 '페이지'로 구성됩니다. 크기가 512의 배수가 아닌 경우에는 데이터를 업로드할 때 오류가 표시됩니다.

## 컨테이너의 Blob 나열

컨테이너 내의 Blob을 나열하려면 **listBlobsSegmented** 메서드를 사용합니다. 특정 접두사가 있는 Blob을 반환하려면 **listBlobsSegmentedWithPrefix**를 사용합니다.

	blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
	  if(!error){
	      // result.entries contains the entries
	      // If not all blobs were returned, result.continuationToken has the continuation token.
	  }
	});

`result`에는 각 Blob을 설명하는 개체의 배열인 `entries` 컬렉션이 포함됩니다. 또한 Blob을 모두 반환할 수 없는 경우에는 `result`에서 추가 항목 검색에 두 번째 매개 변수로 사용할 수 있는 `continuationToken`을 제공합니다.

## Blob 다운로드

Blob에서 데이터를 다운로드하려면 다음을 사용합니다.

* **getBlobToLocalFile** - Blob의 내용을 파일에 씁니다.

* **getBlobToStream** - Blob의 내용을 스트림에 씁니다.

* **getBlobToText** - Blob의 내용을 문자열에 씁니다.

* **createReadStream** - Blob에서 읽는 스트림을 제공합니다.

다음 코드 예제에서는 **getBlobToStream**을 사용하여 **myblob** Blob의 콘텐츠를 다운로드한 다음 스트림을 사용하여 **output.txt** 파일에 저장하는 방법을 보여 줍니다.

	var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result`에는 **ETag** 정보를 비롯한 Blob 관련 정보가 포함됩니다.

## Blob 삭제

마지막으로 Blob을 삭제하려면 **deleteBlob**을 호출합니다. 다음 코드 예제에서는 **myblob**이라는 Blob을 삭제합니다.

	blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## 동시 액세스

여러 클라이언트 또는 여러 프로세스 인스턴스에서 Blob에 대한 동시 액세스를 지원하려면 **ETags** 또는 **임대**를 사용합니다.

* **Etag** - Blob이나 컨테이너를 다른 프로세스에서 수정했는지 감지할 수 있습니다.

* **임대** - Blob에 대해 일정 기간 동안 단독, 갱신 가능, 쓰기 또는 삭제 권한을 얻을 수 있습니다.

### ETag

ETag는 여러 클라이언트 또는 인스턴스에서 블록 Blob에 또는 페이지 Blob에 동시에 쓰기를 허용해야 하는 경우에 사용합니다. ETag를 사용하면 컨테이너나 Blob을 처음 읽거나 만든 후에 수정되었는지 확인하여 다른 클라이언트나 프로세스에서 커밋된 변경 사항을 덮어쓰는 것을 방지할 수 있습니다.

ETag 조건은 선택적인 `options.accessConditions` 매개 변수를 사용하여 설정할 수 있습니다. 아래 코드 예제에서는 Blob이 이미 `etagToMatch`에 포함된 ETag 값이 있는 경우에만 **test.txt** 파일을 업로드합니다.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
	    if(!error){
	    // file uploaded
	  }
	});

ETag를 사용하는 경우 일반적인 패턴:

1. create, list 또는 get 작업의 결과로 ETag를 얻습니다.

2. ETag 값이 수정되지 않은 것을 확인하며 작업을 수행합니다.

값이 수정되지 않았다면 ETag 값을 얻은 후로 다른 클라이언트 또는 인스턴스에서 Blob이나 컨테이너를 수정한 것을 의미합니다.

### 임대

**acquireLease** 메서드를 사용하여 임대를 받을 Blob 또는 컨테이너를 지정하면 새 임대를 받을 수 있습니다. 예를 들어 다음 코드에서는 **myblob**에 임대를 얻습니다.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

**myblob**의 후속 작업에서는 `options.leaseId` 매개 변수를 제공해야 합니다. 임대 ID는 **acquireLease**에서 `result.id`로 반환됩니다.

> [AZURE.NOTE] 기본적으로 임대 기간은 무한입니다. 15초에서 60초까지의 유한한 기간을 지정하려면 `options.leaseDuration` 매개 변수를 제공합니다.

임대를 제거하려면 **releaseLease**를 사용합니다. 임대를 중단하면서 원래 기간이 만료될 때까지 다른 사람이 새 임대를 얻지 못하게 하려면 **breakLease**를 사용합니다.

## 공유 액세스 서명 작업

SAS(공유 액세스 서명)는 저장소 계정 이름이나 키를 제공하지 않으면서 Blob 및 컨테이너에 세분화된 액세스 권한을 안전하게 제공하는 방법입니다. 공유 액세스 서명은 모바일 앱에서 Blob에 액세스하는 경우와 같이 데이터에 대해 제한된 액세스를 제공하는 경우에 자주 사용합니다.

> [AZURE.NOTE] Blob에 대해 익명 액세스를 허용할 수도 있지만 공유 액세스 서명에서 더 잘 제어된 액세스를 제공할 수 있으므로 SAS를 생성해야 합니다.

클라우드 기반 서비스와 같이 신뢰할 수 있는 응용 프로그램에서는 **BlobService**의 **generateSharedAccessSignature**를 사용하여 공유 액세스 서명을 생성하고 모바일 앱과 같은 신뢰할 수 없거나 신뢰가 약한 응용 프로그램에 제공합니다. 공유 액세스 서명은 공유 액세스 서명이 유효한 시작 및 종료 날짜와 공유 액세스 서명 소유자에게 부여되는 액세스 수준을 설명하는 정책을 사용하여 생성됩니다.

다음 코드 예제에서는 공유 액세스 서명 소유자가 **myblob** Blob에서 읽기 작업을 수행할 수 있도록 허용하며 만든 후 100분이 지나면 만료되는 새 공유 액세스 정책을 생성합니다.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

공유 액세스 서명 소유자가 컨테이너에 액세스할 때 필요하므로 호스트 정보도 제공해야 합니다.

그러고 나면 클라이언트 응용 프로그램에서 **BlobServiceWithSAS**에 공유 액세스 서명을 사용하여 Blob에 대한 작업을 수행합니다. 다음에서는 **myblob**에 대한 정보를 가져옵니다.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Blob를 수정하려고 하는 경우 공유 액세스 서명이 읽기 전용 액세스로 생성되었으므로 오류가 반환됩니다.

### 액세스 제어 목록

ACL(액세스 제어 목록)을 사용하여 SAS에 액세스 정책을 설정할 수도 있습니다. 이 방법은 여러 클라이언트에서 컨테이너에 액세스하게 하면서 각 클라이언트에 서로 다른 액세스 정책을 제공하려는 경우에 유용합니다.

ACL은 각 정책에 ID가 연결된 액세스 정책 배열을 사용하여 구현됩니다. 다음 코드 예제에서는 'user1'와 'user2'에 대해 하나씩, 두 개의 정책을 정의합니다.

	var sharedAccessPolicy = {
	  user1: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	  user2: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	    Start: startDate,
	    Expiry: expiryDate
	  }
	};

다음 코드 예제에서는 **mycontainer**에 대한 현재 ACL을 가져온 다음 **setBlobAcl**을 사용하여 새 정책을 추가합니다. 이 접근 방식을 통해 다음을 수행할 수 있습니다.

	var extend = require('extend');
	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
	  if(!error){
	    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
	    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
	      if(!error){
	        // ACL set
	      }
	    });
	  }
	});

ACL이 설정되고 나면 정책의 ID를 기반으로 공유 액세스 서명을 만들 수 있습니다. 다음 코드 예제에서는 ‘user2'에 대한 새 공유 액세스 서명을 만듭니다.

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## 다음 단계

자세한 내용은 다음 리소스를 참조하세요.

-   [Node용 Azure 저장소 SDK API 참조][]
-   [Azure 저장소 팀 블로그][]
-   GitHub의 [Azure Storage SDK for Node][] 리포지토리
-   [Node.js 개발자 센터](/develop/nodejs/)
-   [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[Azure 앱 서비스에서 Node.js 웹앱 만들기]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Azure 테이블 서비스를 사용하여 Node.js 웹앱]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Web Matrix를 사용하여 Azure에 Node.js 웹앱 빌드 및 배포]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Azure 클라우드 서비스에 Node.js 응용 프로그램 빌드 및 배포]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Node용 Azure 저장소 SDK API 참조]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!---HONumber=AcomDC_0921_2016-->