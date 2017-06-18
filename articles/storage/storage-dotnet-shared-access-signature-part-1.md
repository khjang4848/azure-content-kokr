<properties
	pageTitle="SAS(공유 액세스 서명) 사용 | Microsoft Azure"
	description="SAS(공유 액세스 서명)를 사용하여 Blob, 큐, 테이블 및 파일을 비롯한 Azure 저장소 리소스에 대한 액세스 권한을 위임하는 방법을 알아봅니다."
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



# SAS(공유 액세스 서명) 사용

## 개요

SAS(공유 액세스 서명)를 사용하면 계정 키를 노출하지 않고 다른 클라이언트에게 저장소 계정의 개체에 대한 제한된 액세스 권한을 확실히 부여할 수 있습니다. 공유 액세스 서명에 관한 이 자습서의 1부에서는 SAS 모델의 개요를 제공하고 SAS 모범 사례를 검토합니다. 자습서의 [2부](storage-dotnet-shared-access-signature-part-2.md)에서는 Blob 서비스를 통해 공유 액세스 서명을 만드는 프로세스를 안내합니다.

## 공유 액세스 서명이란?

공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 즉, 계정 액세스 키를 공유할 필요 없이 지정된 권한 집합을 사용하여 지정된 기간 동안 클라이언트에게 저장소 계정의 개체에 대한 제한된 권한을 부여할 수 있습니다. SAS는 저장소 리소스에 인증된 액세스를 수행하는 데 필요한 모든 정보가 쿼리 매개 변수에 있는 URI입니다. SAS를 사용하여 저장소 리소스에 액세스하려는 클라이언트는 SAS를 적절한 생성자 또는 메서드에 전달하면 됩니다.

## 공유 액세스 서명은 언제 사용하나요?

계정 키로 신뢰할 수 없는 클라이언트에게 저장소 계정의 리소스에 대한 액세스 권한을 제공하려는 경우에 SAS를 사용할 수 있습니다. 저장소 계정 키는 기본 키와 보조 키를 모두 포함하여, 계정과 계정에 있는 모든 리소스에 대한 관리 권한을 부여합니다. 이러한 계정 키가 노출되면 계정이 악의적이거나 부주의하게 사용될 수 있습니다. 공유 액세스 서명은 다른 클라이언트가 계정 키를 사용하지 않고 부여된 권한에 따라 저장소 계정에서 데이터를 읽고, 쓰고, 삭제하도록 허용하는 안전한 대체 방법입니다.

SAS가 유용한 일반적인 시나리오로는 다른 사용자가 저장소 계정에서 데이터를 읽고 쓰는 서비스가 있습니다. 저장소 계정에 사용자 데이터를 저장하는 시나리오에는 다음과 같은 두 가지 일반적인 디자인 패턴이 있습니다.


1\. 클라이언트는 인증을 수행하는 프런트 엔드 프록시 서비스를 통해 데이터를 업로드하고 다운로드합니다. 프런트 엔드 프록시 서비스는 비즈니스 규칙의 유효성을 검사할 수 있다는 장점이 있지만, 데이터의 양이 많거나 트랜잭션 볼륨이 높은 경우 수요에 맞게 확장 가능한 서비스를 구축하려면 많은 비용이 필요하거나 어려움이 따를 수 있습니다.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\. 간단한 서비스에서 필요에 따라 클라이언트를 인증한 다음 SAS를 생성합니다. 클라이언트는 SAS를 수신한 후 SAS에 정의된 권한을 사용하여 SAS에 허용된 간격으로 저장소 계정 리소스에 직접 액세스할 수 있습니다. SAS를 사용하면 프런트 엔드 프록시 서비스를 통해 모든 데이터를 라우팅할 필요성이 감소됩니다.

![sas-storage-provider-service][sas-storage-provider-service]

대부분의 실제 서비스에서는 포함된 시나리오에 따라 두 가지 방법을 혼합하여 사용할 수 있습니다. 즉, 일부 데이터는 프런트 엔드 프록시를 통해 처리 및 확인하고 일부 데이터는 SAS를 사용하여 직접 저장하거나 읽습니다.

또한 특정 시나리오에서는 SAS를 사용하여 복사 작업의 원본 개체를 인증해야 합니다.

- 다른 저장소 계정에 있는 다른 Blob에 Blob을 복사하는 경우 SAS를 사용하여 원본 Blob을 인증해야 합니다. 2015-04-05 버전에서는 필요에 따라 SAS를 사용하여 대상 Blob도 인증할 수 있습니다.
- 다른 저장소 계정에 있는 다른 파일에 파일을 복사하는 경우 SAS를 사용하여 원본 파일을 인증해야 합니다. 2015-04-05 버전에서는 필요에 따라 SAS를 사용하여 대상 파일도 인증할 수 있습니다.
- Blob을 파일에 복사하거나 파일을 Blob에 복사하는 경우 원본 및 대상 개체가 동일한 저장소 계정 내에 있더라도 SAS를 사용하여 원본 개체를 인증해야 합니다.

## 공유 액세스 서명의 유형

2015-04-05 버전의 Azure 저장소부터 계정 SAS라는 새로운 유형의 공유 액세스 서명이 도입되었습니다. 이제 두 가지 유형의 공유 액세스 서명을 만들 수 있습니다.

- **계정 SAS** 계정 SAS는 하나 이상의 저장소 서비스에서 리소스에 대한 액세스 권한을 위임합니다. 서비스 SAS를 통해 사용 가능한 모든 작업은 계정 SAS를 통해서도 사용할 수 있습니다. 또한 계정 SAS를 사용하면 **서비스 속성 가져오기/설정**, **서비스 통계 설정**과 같은 특정 서비스에 적용되는 작업에 대한 액세스 권한을 위임할 수 있습니다. 또한 서비스 SAS로 허용되지 않는 Blob 컨테이너, 테이블, 큐, 파일 공유에서 읽기, 쓰기, 삭제 작업에 대한 액세스 권한을 위임할 수 있습니다. 계정 SAS 토큰을 구성하는 방법에 대한 자세한 내용은 [계정 SAS 구성](https://msdn.microsoft.com/library/mt584140.aspx)을 참조하세요.

- **서비스 SAS** 서비스 SAS는 저장소 서비스(Blob, 큐, 테이블, 파일 서비스) 중 하나의 리소스에만 액세스 권한을 위임합니다. 서비스 SAS 토큰을 구성하는 방법에 대한 자세한 내용은 [서비스 SAS 구성](https://msdn.microsoft.com/library/dn140255.aspx) 및 [서비스 SAS 예제](https://msdn.microsoft.com/library/dn140256.aspx)를 참조하세요.

## 공유 액세스 서명 사용 방법

공유 액세스 서명은 하나 이상의 저장소 리소스를 가리키는 URI이며 쿼리 매개 변수의 특별 집합이 포함된 토큰이 들어 있습니다. 토큰은 클라이언트가 리소스를 액세스하는 방식을 나타냅니다. 이러한 쿼리 매개 변수 중 하나인 서명은 SAS 매개 변수에서 구성되고 계정 키로 서명됩니다. 이 서명은 Azure 저장소에서 SAS를 인증하는 데 사용됩니다.

계정 SAS 및 서비스 SAS 토큰에는 일반적인 매개 변수 몇 개가 포함되며 다른 몇 가지 매개 변수도 사용합니다.

### 계정 SAS 및 서비스 SAS 토큰에 일반적인 매개 변수

- **Api 버전** 요청을 실행하기 위해 사용할 저장소 서비스 버전을 지정하는 선택적 매개 변수입니다.
- **서비스 버전** 요청을 인증하기 위해 사용할 저장소 서비스 버전을 지정하는 선택적 매개 변수입니다.
- **시작 시간.** SAS가 유효해지는 시간입니다. 공유 액세스 서명의 시작 시간은 선택 사항이며, 생략할 경우 SAS가 즉시 유효해집니다. 특정 UTC 지정자("Z")를 이용하여 UTC(협정 세계시)로 표시해야 합니다(즉, 1994-11-05T13:15:30Z).
- **만료 시간.** SAS가 더 이상 유효하지 않게 되는 시간입니다. 모범 사례에 따라 SAS의 만료 시간을 지정하거나 만료 시간을 저장된 액세스 정책과 연결하는 것이 좋습니다. 특정 UTC 지정자("Z")를 이용하여 UTC(협정 세계시)로 표시해야 합니다(즉, 1994-11-05T13:15:30Z)(아래 내용 참조).
- **사용 권한** SAS에 지정된 사용 권한은 클라이언트가 SAS를 사용하여 저장소 리소스에 대해 수행할 수 있는 작업을 나타냅니다. 사용 가능한 권한은 계정 SAS와 서비스 SAS가 다릅니다.
- **IP** 요청을 수락할 Azure 외부(Express 경로에 대한 [라우팅 세션 구성 상태](../expressroute/expressroute-workflows.md#routing-session-configuration-state) 섹션 참조)의 IP 주소 또는 IP 주소 범위를 지정하는 선택적 매개 변수입니다.
- **프로토콜** 요청에 허용되는 프로토콜을 지정하는 선택적 매개 변수입니다. 기본값인 HTTPS 및 HTTP(https,http) 또는 HTTPS만(https) 허용됩니다. HTTP만은 허용되는 값이 아닙니다.
- **서명** 서명은 토큰의 일부로 지정된 다음 암호화된 다른 매개 변수에서 구성됩니다. 서명은 SAS 인증에 사용됩니다.

### 계정 SAS 토큰의 매개 변수

- **서비스** 계정 SAS는 하나 이상의 저장소 서비스에 대한 액세스 권한을 위임할 수 있습니다. 예를 들어 Blob 및 파일 서비스에 대한 액세스 권한을 위임하는 계정 SAS를 만들 수 있습니다. 또는 4개 서비스(Blob, 큐, 테이블, 파일) 전체에 대한 액세스 권한을 위임하는 SAS를 만들 수 있습니다.
- **저장소 리소스 유형** 계정 SAS는 특정 리소스가 아닌 하나 이상의 클래스의 저장소 리소스에 적용됩니다. 계정 SAS를 만들어 다음에 대한 액세스 권한을 위임할 수 있습니다.
	- 저장소 계정 리소스에 대해 호출되는 서비스 수준 API. 예를 들면 **서비스 속성 가져오기/설정**, **서비스 통계 가져오기**, **목록 컨테이너/큐/테이블/공유**가 포함됩니다.
	- 각 서비스(Blob 컨테이너, 큐, 테이블, 파일 공유)의 컨테이너 개체에 대해 호출되는 컨테이너 수준 API입니다. 예를 들어 **컨테이너 만들기/삭제**, **큐 만들기/삭제**, **테이블 만들기/삭제**, **공유 만들기/삭제**, **목록 Blob/파일 및 디렉터리**가 있습니다.
	- Blob, 큐 메시지, 테이블 엔터티, 파일에 대해 호출되는 개체 수준 API. 예를 들어 **Blob 배치**, **쿼리 엔터티**, **메시지 가져오기**, **파일 만들기**가 있습니다.

### 서비스 SAS 토큰의 매개 변수

- **저장소 리소스** 서비스 SAS를 사용하여 액세스 권한을 위임할 수 있는 저장소 리소스는 다음과 같습니다.
	- 컨테이너 및 Blob
	- 파일 공유 및 파일
	- 큐
	- 테이블 및 테이블 엔터티 범위

## SAS URI의 예

다음은 Blob에 대한 읽기 및 쓰기 권한을 제공하는 서비스 SAS URI의 예입니다. 테이블에서는 URI의 각 부분이 SAS에서 어떤 역할을 하는지를 이해할 수 있도록 세분화했습니다.

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

이름|SAS 부분|설명
---|---|---
Blob URI|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Blob의 주소입니다. HTTPS를 사용하는 것이 좋습니다.
저장소 서비스 버전|sv=2015-04-05|2012-02-12 이후의 저장소 서비스 버전의 경우 이 매개 변수는 사용할 버전을 나타냅니다.
시작 시간|st=2015-04-29T22%3A18%3A26Z|UTC 시간으로 지정됩니다. SAS를 즉시 유효화하려면 시작 시간을 생략하십시오.
만료 시간|se=2015-04-30T02%3A23%3A26Z|UTC 시간으로 지정됩니다.
리소스|sr=b|Blob의 리소스입니다.
권한|sp=rw|SAS에서 부여하는 권한에는 읽기 및 쓰기가 포함됩니다.
IP 범위|sip=168.1.5.60-168.1.5.70|요청을 수락할 IP 주소 범위입니다.
프로토콜|spr=https|HTTPS를 사용하는 요청만 허용됩니다.
서명|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|Blob에 대한 액세스 권한을 인증하는 데 사용합니다. 이 서명은 SHA256 알고리즘을 사용하여 서명할 문자열과 키를 통해 계산되고 Base64 인코딩을 사용하여 인코드되는 HMAC입니다.

다음은 토큰에 동일한 일반적 매개 변수를 사용하는 계정 SAS의 예입니다. 이러한 매개 변수는 위에서 설명했으므로 여기에서는 설명을 생략합니다. 계정 SAS에 해당하는 매개 변수만 아래 표에 설명되어 있습니다.

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

이름|SAS 부분|설명
---|---|---
리소스 URI|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The 서비스 속성을 가져오거나(GET으로 호출할 경우) 서비스 속성을 설정하기 위한(SET으로 호출하는 경우) 매개 변수를 사용하는 Blob 서비스 끝점입니다.
서비스|ss=bf|SAS는 Blob 및 파일 서비스에 적용됩니다.
리소스 유형|srt=s|SAS는 서비스 수준 작업에 적용됩니다.
권한|sp=rw|사용 권한으로 읽기 및 쓰기 작업에 대한 액세스 권한을 부여합니다.  

사용 권한이 서비스 수준으로 제한된 경우 이 SAS로 액세스 가능한 작업은 **Blob 서비스 속성 가져오기**(읽기) 및 **Blob 서비스 속성 설정**(쓰기)입니다. 하지만 다른 리소스 URI를 사용하면 동일한 SAS 토큰을 사용하여 **Blob 서비스 통계 가져오기**(읽기)에 대한 액세스 권한을 위임할 수도 있습니다.

## 저장된 액세스 정책을 사용하여 SAS 관리 ##

공유 액세스 서명은 다음 두 가지 형식 중 하나를 사용할 수 있습니다.

- **임시 SAS:** 임시 SAS를 만들 때 SAS의 시작 시간, 만료 시간 및 사용 권한이 SAS URI에 모두 지정되거나 시작 시간이 생략되는 경우에는 묵시적으로 지정됩니다. 이 SAS 유형은 계정 SAS 또는 서비스 SAS로 만들 수 있습니다.

- **저장된 액세스 정책 사용 SAS:** 저장된 액세스 정책은 리소스 컨테이너(Blob 컨테이너, 테이블, 큐 또는 파일 공유)에서 정의되며, 하나 이상의 공유 액세스 서명에 대한 제약 조건을 관리하는 데 사용할 수 있습니다. SAS를 공유 액세스 정책과 연결할 경우 SAS는 저장된 액세스 정책에 대해 정의된 제약 조건(시작 시간, 만료 시간 및 사용 권한)을 상속합니다.

>[AZURE.NOTE] 현재, 계정 SAS는 애드혹 SAS여야 합니다. 저장된 액세스 정책은 아직 계정 SAS에 지원되지 않습니다.

두 형식의 차이점은 주요 시나리오인 해지에 중요합니다. SAS는 URL이므로 SAS를 시작하도록 요청한 사용자에 상관없이 SAS를 획득한 모든 사용자가 SAS를 사용할 수 있습니다. SAS가 공개적으로 게시된 경우 전 세계의 모든 사용자가 SAS를 사용할 수 있습니다. 분산된 SAS는 다음 네 가지 중 하나에 해당할 때까지 유효합니다.

1.	SAS에 지정된 만료 시간에 도달한 경우
2.	SAS에서 참조되는 저장된 액세스 정책에 지정된 만료 시간에 도달한 경우(저장된 액세스 정책을 참조하고 이 정책에 만료 시간이 지정된 경우). 시간 간격이 경과하거나 저장된 액세스 정책에서 만료 시간을 과거의 시간으로 수정한 경우에 해당하며, 이는 SAS를 해지하는 한 가지 방법입니다.
3.	SAS에서 참조되는 저장된 액세스 정책을 삭제한 경우(SAS를 해지하는 다른 방법). 동일한 이름을 사용하여 저장된 액세스 정책을 다시 만들면 저장된 액세스 정책에 연결된 사용 권한에 따라 모든 기존 SAS 토큰이 다시 유효해집니다(SAS의 만료 시간이 경과하지 않은 것으로 가정). SAS를 해지하기 위해 만료 시간을 미래의 시간으로 지정하여 액세스 정책을 다시 만들 경우 다른 이름을 사용해야 합니다.
4.	SAS를 만드는 데 사용된 계정 키를 다시 생성한 경우. 이렇게 하면 해당 계정 키를 사용하는 모든 응용 프로그램 구성 요소는 다른 유효한 계정 키 또는 새로 생성된 계정 키를 사용하도록 업데이트될 때까지 인증되지 않습니다.

>[AZURE.IMPORTANT] 공유 액세스 서명 URI는 서명을 만드는 데 사용된 계정 키 및 저장된 관련 액세스 정책(있는 경우)에 연결됩니다. 저장된 액세스 정책을 지정하지 않는 경우 공유 액세스 서명을 해지하는 방법은 계정 키를 변경하는 것뿐입니다.

## 예제: 공유 액세스 서명 만들기 및 사용

다음은 공유 액세스 서명의 두 유형(계정 SAS, 서비스 SAS)에 대한 몇 가지 예입니다.

이 예제를 실행하려면 다음 패키지를 다운로드하여 참조해야 합니다.

- [Azure Storage Client Library for .NET](http://www.nuget.org/packages/WindowsAzure.Storage), 버전 6.x 이상(계정 SAS 사용을 위해).
- [Azure 구성 관리자](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

### 예제: 계정 SAS

다음 코드 예제는 Blob 및 파일 공유에 유효한 계정 SAS를 만들며 클라이언트가 읽기, 쓰기, 목록 권한을 사용하여 서비스 수준 API에 액세스할 수 있는 권한을 부여합니다. 계정 SAS는 프로토콜을 HTTPS로 제한하므로 반드시 HTTPS로 요청해야 합니다.

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials. Modify for your account.
	    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

계정 SAS를 사용하여 Blob 서비스에 대한 서비스 수준 API에 액세스하려면 SAS를 사용하는 Blob 클라이언트 개체와 저장소 계정의 Blob 저장소 끝점을 구성합니다.

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
	    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### 예제: 저장된 액세스 정책을 사용하는 서비스 SAS

다음 코드 예제에서는 컨테이너에 저장된 액세스 정책을 만든 다음 컨테이너에 대한 서비스 SAS를 생성합니다. 그런 다음 클라이언트에 이 SAS를 제공하여 컨테이너의 읽기-쓰기 권한을 제공할 수 있습니다. 자신의 계정 이름을 사용하려면 코드를 변경합니다.

    // Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();

    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();

    // Clear the container's shared access policies to avoid naming conflicts.
    blobPermissions.SharedAccessPolicies.Clear();

    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Add
    });

    // The public access setting explicitly specifies that
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;

    // Set the new stored access policy on the container.
    container.SetPermissions(blobPermissions);

    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

서비스 SAS를 소유한 클라이언트는 코드에 이 SAS를 사용하여 컨테이너의 Blob 읽기 또는 쓰기에 대한 요청을 인증할 수 있습니다. 예를 들어 다음 코드는 SAS 토큰을 사용하여 컨테이너에 새 블록 Blob을 만듭니다. 자신의 계정 이름을 사용하려면 코드를 변경합니다.

    Uri blobUri = new Uri("https://<myaccount>.blob.core.windows.net/mycontainer/myblob.txt");

    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);

    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);

    // Upload the blob.
    // If the blob does not yet exist, it will be created.
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Temp\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## 공유 액세스 서명을 사용하는 모범 사례

응용 프로그램에서 공유 액세스 서명을 사용할 경우 다음과 같은 두 가지 잠재적 위험에 대해 잘 알고 있어야 합니다.

- SAS가 누설될 경우 SAS를 획득한 모든 사용자가 SAS를 사용하여 저장소 계정을 손상시킬 수 있습니다.
- 클라이언트 응용 프로그램에 제공된 SAS가 만료되어 응용 프로그램이 서비스에서 새 SAS를 검색할 수 없는 경우 응용 프로그램의 기능이 저하될 수 있습니다.

다음은 공유 액세스 서명을 사용하여 이러한 위험을 조율하는 데 도움이 되는 권장 사항입니다.

1. **항상 HTTPS를 사용하여** SAS를 만들거나 SAS를 분산합니다. HTTP를 통해 SAS를 전달할 경우 SAS가 노출되면 메시지 가로채기(man-in-the-middle) 공격을 수행하는 공격자가 SAS를 읽은 다음 의도된 사용자처럼 SAS를 사용할 수 있으므로, 악의적인 사용자가 중요한 데이터를 손상시킬 수 있습니다.
2. **저장된 액세스 정책을 최대한 참조합니다.** 저장된 액세스 정책을 사용하면 저장소 계정 키를 다시 생성할 필요 없이 사용 권한을 해지할 수 있습니다. 만료 시간을 매우 길게(또는 무제한) 설정하고 정기적으로 업데이트하여 만료 시간을 미래의 시간으로 이동해야 합니다.
3. **임시 SAS의 경우 짧은 만료 시간을 사용합니다.** 그러면 SAS가 자신도 모르게 손상되더라도 SAS를 단기적으로만 실행할 수 있습니다. 이 방법은 저장된 액세스 정책을 참조할 수 없는 경우에 특히 중요합니다. 또한 업로드할 수 있는 시간을 제한하여 Blob에 쓸 수 있는 데이터의 양을 제한할 수 있습니다.
4. **필요한 경우 클라이언트가 SAS를 자동으로 갱신하도록 허용합니다.** 클라이언트는 예상 만료일 이전에 SAS를 갱신하여 SAS를 제공하는 서비스를 사용할 수 없을 때 다시 시도할 수 있는 시간적 여유를 확보해야 합니다. 지정된 만료 시간 내에 완료할 수 있는 즉각적인 소규모 단기 작업에 SAS를 사용할 경우에는 SAS를 갱신할 필요가 없습니다. 하지만 클라이언트가 SAS를 통해 일상 요청을 수행하는 경우에는 중간에 만료될 수 있습니다. 따라서 위에서 설명한 SAS 단기 실행 요구 사항과 클라이언트가 조기에 갱신을 요청하여 갱신 이전에 SAS가 만료되어 가동 중단이 발생하는 것을 방지해야 하는 요구 사항을 적절하게 조율하는 것이 중요합니다.
5. **SAS 시작 시간에 유의하세요.** SAS 시작 시간을 **지금**으로 설정한 경우 클럭 오차(컴퓨터의 차이에 따른 현재 시간의 차이)로 인해 처음 몇 분 동안 간헐적으로 오류가 발생할 수 있습니다. 일반적으로 시작 시간을 최소 15분 이전으로 설정하거나 설정하지 않습니다. 그러면 시작 시간이 즉시 유효해집니다. 이는 만료 시간에도 일반적으로 적용됩니다. 요청 방향에서 최대 15분의 클럭 오차가 발생할 수 있습니다. 2012-02-12 이전 REST 버전을 사용하는 클라이언트의 경우 저장된 액세스 정책을 참조하지 않는 SAS의 최대 기간은 1시간이고, 이 시간보다 더 긴 기간을 지정하는 정책은 실패합니다.
6.	**액세스할 리소스를 구체적으로 지정하세요.** 일반적으로 보안을 위한 최적의 방법은 사용자에게 필요한 최소 권한을 제공하는 것입니다. 사용자가 단일 엔터티에 대한 읽기 권한만 필요한 경우 해당 엔터티에 대한 읽기 권한만 부여하고 모든 엔터티에 대한 읽기/쓰기/삭제 권한은 부여하지 않습니다. 그러면 SAS 손상 위협을 완화하여 SAS가 공격자의 수중에 넘어갈 가능성이 낮아집니다.
7.	**SAS 사용 작업을 포함한 모든 사용량에 대한 요금이 계정에 청구됩니다.** Blob에 쓰기 권한을 제공한 경우 사용자가 200GB Blob을 업로드하도록 선택할 수 있습니다. 사용자에게 읽기 권한도 제공한 경우 사용자가 이 Blob을 10번 다운로드하도록 선택하여 2TB의 발신 비용이 청구될 수 있습니다. 또한 제한된 권한을 제공하여 악의적인 사용자의 공격 가능성을 낮추십시오. 단기 실행 SAS를 사용하여 이 위협을 줄이세요. 이때 종료 시간의 클럭 오차에 유의하세요.
8.	**SAS를 사용하여 작성된 데이터의 유효성을 검사하세요.** 클라이언트 응용 프로그램이 저장소 계정에 데이터를 쓸 경우 해당 데이터에 문제가 있을 수 있습니다. 데이터를 사용할 준비가 되기 이전에 응용 프로그램에서 데이터의 유효성을 검사하거나 권한을 부여해야 하는 경우 데이터를 작성한 이후 응용 프로그램에서 데이터를 사용하기 이전에 유효성 검사를 수행해야 합니다. 그러면 SAS를 적절한 방법으로 습득한 사용자나 누설된 SAS를 악용하는 사용자로 인해 계정이 손상되거나 데이터에 악의적인 데이터가 기록되는 것을 방지할 수 있습니다.
9. **경우에 따라 SAS를 사용하지 마세요..** 저장소 계정에 대한 특정 작업 관련 위험이 SAS의 이점을 능가하는 경우도 있습니다. 그런 작업에 대해서는 비즈니스 규칙 유효성 검사, 인증 및 감사를 수행한 이후에 저장소 계정에 쓰는 중간 계층 서비스를 만듭니다. 또한 다른 방법으로 액세스를 관리하는 것이 더 간단한 경우도 있습니다. 예를 들어 컨테이너의 모든 Blob을 공개적으로 읽기 가능하도록 설정하려면 모든 클라이언트가 액세스하도록 SAS를 제공하는 대신에 컨테이너를 공용으로 설정할 수 있습니다.
10.	**저장소 분석을 사용하여 응용 프로그램을 모니터링합니다.** 로깅 및 메트릭을 사용하여 SAS 공급자 서비스의 가동 중단이나 저장된 액세스 정책의 잘못된 제거로 인한 인증 오류의 급격한 증가를 관찰할 수 있습니다. 자세한 내용은 [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx)(영문)를 참조하십시오.

## 결론 ##

공유 액세스 서명은 계정 키가 필요하지 않은 클라이언트에게 저장소 계정에 대한 제한된 권한을 제공하는 데 유용합니다. 일반적으로 공유 액세스 서명은 Azure 저장소를 사용하는 응용 프로그램에 대한 보안 모델의 필수적인 부분입니다. 여기에 나열된 모범 사례를 따를 경우 SAS를 사용하여 응용 프로그램의 보안을 훼손하지 않으면서 저장소 계정에 있는 리소스에 유연하게 액세스할 수 있습니다.

## 다음 단계 ##

- [공유 액세스 서명, 2부: Blob 저장소를 사용하여 SAS 만들기 및 사용](storage-dotnet-shared-access-signature-part-2.md)
- [Windows에서 Azure 파일 저장소 시작](storage-dotnet-how-to-use-files.md)
- [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)
- [공유 액세스 서명을 사용하여 액세스 위임](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [테이블 및 큐 SAS 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png

<!---HONumber=AcomDC_0914_2016-->