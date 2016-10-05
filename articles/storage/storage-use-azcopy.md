<properties
	pageTitle="AzCopy 사용하여 저장소로 데이터 이동 또는 복사 | Microsoft Azure"
	description="AzCopy 유틸리티를 사용하여 blob, 테이블 및 파일 콘텐츠에서 데이터를 이동하거나 복사합니다. 로컬 파일에서 Azure 저장소로 데이터를 복사하거나, 저장소 계정 내에서 데이터를 복사하거나, 저장소 계정 간에 데이터를 복사합니다. 데이터를 Azure 저장소로 손쉽게 마이그레이션할 수 있습니다."
	services="storage"
	documentationCenter=""
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2016"
	ms.author="micurd;tamram"/>

# AzCopy 명령줄 유틸리티로 데이터 전송

## 개요

AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Microsoft Azure Blob, 파일 및 테이블 저장소에(로부터) 복사하도록 디자인된 Windows 명령줄 유틸리티입니다. 저장소 계정 내에서나 저장소 계정 사이에서 개체 간에 데이터 복사할 수 있습니다.

> [AZURE.NOTE] 이 가이드는 [Azure 저장소](https://azure.microsoft.com/services/storage/)에 대해 잘 알고 있다는 것을 가정합니다. 그렇지 않은 경우, [Azure 저장소 소개](storage-introduction.md) 설명서가 유용할 것입니다. 가장 중요한 것은 [저장소 계정을 만들어](storage-create-storage-account.md#create-a-storage-account) AzCopy를 사용해야 한다는 것입니다.

## AzCopy 다운로드 및 설치

### Windows

[최신 버전의 AzCopy](http://aka.ms/downloadazcopy)를 다운로드합니다.

### Mac/Linux

AzCopy는 Mac/Linux Os에 사용할 수 없습니다. 그러나 Azure CLI는 Azure 저장소에(로부터) 데이터를 복사하기 위한 적절한 대안입니다. 자세한 내용은 [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md)을 읽어보세요.

## 첫 번째 AzCopy 명령 작성

AzCopy 명령의 기본 구문은 다음과 같습니다.

	AzCopy /Source:<source> /Dest:<destination> [Options]

명령 창을 열고 컴퓨터의 AzCopy 설치 디렉터리로 이동합니다. 이 디렉터리에 `AzCopy.exe` 실행 파일이 있습니다. 원할 경우 시스템 경로에 AzCopy 설치 위치를 추가할 수 있습니다. 기본적으로 AzCopy는 `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` 또는 `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`에 설치됩니다.

다음 예제는 Microsoft Azure Blob, 파일 및 테이블에(로부터) 데이터를 복사하는 여러 시나리오를 보여줍니다. 각 샘플에 사용된 매개 변수에 대 한 자세한 정보는 [AzCopy 매개 변수](#azcopy-parameters) 섹션을 참고하세요.

## Blob: 다운로드

### 단일 blob 다운로드

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"

`C:\myfolder` 폴더가 없으면 AzCopy는 파일 시스템에서 이 폴더를 만든 후 새 폴더에 `abc.txt `를 다운로드합니다.

### 보조 지역에서 단일 Blob 다운로드

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

지역 중복 저장소가 사용된 읽기 액세스가 있어야 합니다.

### 모든 Blob 다운로드

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다.

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

다운로드 작업 후에 `C:\myfolder` 디렉터리에는 다음 파일이 포함됩니다.

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

`/S` 옵션을 지정하지 않으면 Blob이 다운로드됩니다.

### 지정된 접두사가 있는 Blob 다운로드

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

다음 Blob이 지정된 컨테이너에 있는 경우를 생각해 보겠습니다. 접두사 `a`로 시작하는 모든 Blob이 다운로드됩니다.

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

다운로드 작업 후에 `C:\myfolder` 폴더에는 다음 파일이 포함됩니다.

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

접두사는 Blob 이름의 처음 부분을 구성하는 가상 디렉터리에 적용됩니다. 위에 표시된 예에서는 가상 디렉터리가 지정된 접두사와 일치하지 않으므로 다운로드가 진행되지 않습니다. 또한 옵션 `\S`이(가) 지정되지 않으면 AzCopy는 어떤 Blob도 다운로드하지 않습니다.

### 내보낸 파일의 마지막으로 수정한 시간을 소스 Blob과 동일하게 설정

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

또한 마지막으로 수정한 시간에 따라 다운로드 작업에서 Blob을 제외할 수도 있습니다. 예를 들어 마지막으로 수정된 시간이 대상 파일과 동일하거나 더 최신인 Blob을 제외하려는 경우 `/XN` 옵션을 추가합니다.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

또는 마지막으로 수정된 시간이 대상 파일과 동일하거나 더 이전인 Blob을 제외하려는 경우 `/XO` 옵션을 추가합니다.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

## Blob: 업로드

### 단일 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"

지정된 대상 컨테이너가 존재하지 않을 경우 AzCopy는 컨테이너를 만든 후 여기에 파일을 업로드합니다.

### 가상 디렉터리에 단일 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

지정한 가상 디렉터리가 없으면 AzCopy는 해당 이름에 가상 디렉터리를 포함하여 파일을 업로드합니다(*예*: 위 예의 `vd/abc.txt`).

### 모든 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

`/S` 옵션을 지정하면 지정된 디렉터리의 내용이 Blob 저장소에 재귀적으로 업로드됩니다. 즉, 모든 하위 폴더 및 해당 파일도 업로드됩니다. 예를 들어 다음 파일이 `C:\myfolder` 폴더에 있는 경우를 생각해 보겠습니다.

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

업로드 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

  	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

`/S` 옵션을 지정하지 않으면, AzCopy는 재귀적으로 업로드되지 않습니다. 업로드 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

	abc.txt
	abc1.txt
	abc2.txt

### 지정된 패턴과 일치하는 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

다음 파일이 `C:\myfolder` 폴더에 있는 경우를 생각해 보겠습니다.

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

업로드 작업 후에 컨테이너에는 다음 파일이 포함됩니다.

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt

`/S` 옵션을 지정하지 않으면, AzCopy만 가상 디렉터리에 존재하지 않는 Blob만 업로드합니다.

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

### 대상 Blob의 MIME 콘텐츠 형식을 지정합니다.

기본적으로 AzCopy에서는 대상 Blob의 콘텐츠 형식을 `application/octet-stream`으로 설정합니다. 버전 3.1.0부터는 `/SetContentType:[content-type]` 옵션을 통해 콘텐츠 형식을 명시적으로 지정할 수 있습니다. 이 구문은 업로드 작업에서 모든 Blob의 콘텐츠 형식을 설정합니다.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

값 없이 `/SetContentType`을 지정하면 AzCopy에서 각 Blob 또는 파일의 콘텐츠 형식을 파일 확장명에 따라 설정합니다.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

## Blob: 복사

### 저장소 계정 내 단일 Blob 복사

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt

저장소 계정 내에 Blob을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### 저장소 계정 간 단일 Blob 복사

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

저장소 계정 간 Blob을 복사할 때는 [서버 쪽 복사](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) 작업이 수행됩니다.

### 보조 지역에서 주 지역으로 단일 Blob 복사

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

지역 중복 저장소가 사용된 읽기 액세스가 있어야 합니다.

### 저장소 계정 간 단일 Blob 및 스냅숏 복사

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

복사 작업 후에 대상 컨테이너에는 Blob 및 해당 스냅숏이 포함됩니다. 위 예에서 Blob에 두 개의 스냅숏이 있다면 컨테이너에는 다음 Blob과 스냅숏이 포함될 것입니다.

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt

### 저장소 계정 간 Blob 비동기 복사

기본적으로 AzCopy는 두 저장소 끝점 간에 데이터를 비동기적으로 복사합니다. 따라서 복사 작업은 Blob이 복사되는 속도와 관련하여 SLA가 없는 여분의 대역폭 용량을 사용하여 백그라운드로 실행되며, AzCopy는 복사가 완료되거나 실패할 때까지 복사 상태를 정기적으로 확인합니다.

`/SyncCopy` 옵션을 사용하면 복사 작업이 일관된 속도를 유지할 수 있습니다. AzCopy는 지정된 소스에서 로컬 메모리로 복사할 Blob을 다운로드한 후 대상 Blob 저장소에 업로드하여 동기 복사를 수행합니다.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

`/SyncCopy`은(는) 비동기 복사와 비교하여 추가적인 송신 비용이 발생할 수 있으므로 송신 비용을 방지하려면 원본 저장소 계정과 동일한 지역에 있는 Azure VM에서 이 옵션을 사용하는 것이 좋습니다.

## 파일: 다운로드

### 단일 파일 다운로드

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

지정된 소스가 Azure 파일 공유이면 단일 파일을 다운로드할 정확한 파일 이름(*예:* `abc.txt`)을 지정하거나 `/S` 옵션을 지정하여 공유의 모든 파일을 재귀 방식으로 다운로드해야 합니다. 파일 패턴과 `/S` 옵션을 함께 지정하려고 하면 오류가 발생합니다.

### 모든 파일 다운로드

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

빈 폴더는 다운로드되지 않습니다.

## 파일: 업로드

### 단일 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt

### 모든 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

빈 폴더는 업로드되지 않습니다.

### 지정된 패턴과 일치하는 파일 업로드

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## 파일: 복사

### 파일 공유 간 복사

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

### 파일 공유에서 Blob으로 복사

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S

파일 저장소-페이지 Blob로의 비동기 복사는 지원되지 않습니다.

### Blob에서 파일 공유로 복사

	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

### 동기적으로 파일 복사

`/SyncCopy` 옵션을 지정하여 파일 저장소 간에, 파일 저장소에서 Blob 저장소로, Blob 저장소에서 파일 저장소로 동기적으로 데이터를 복사할 수 있습니다. AzCopy는 로컬 메모리에 원본 데이터를 다운로드한 후 대상에 다시 업로드하여 이 작업을 수행합니다.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy

파일 저장소에서 Blob 저장소로 복사할 경우 기본 Blob 형식은 블록 Blob입니다. `/BlobType:page` 옵션을 지정하면 사용자가 대상 Blob 유형을 변경할 수 있습니다.

`/SyncCopy`는 비동기 복사와 비교하여 추가적인 송신 비용이 발생할 수 있으므로 송신 비용을 방지하려면 원본 저장소 계정과 동일한 지역에 있는 Azure VM에서 이 옵션을 사용하는 것이 좋습니다.

## 테이블: 내보내기

### 테이블 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

AzCopy는 지정된 대상 폴더에 매니페스트 파일을 씁니다. 매니페스트 파일은 필요한 데이터 파일을 찾고 데이터 유효성 검사를 수행하는 가져오기 프로세스에서 사용됩니다. 매니페스트 파일에는 기본적으로 다음의 명명 규칙이 사용됩니다.

	<account name>_<table name>_<timestamp>.manifest

매니페스트 파일 이름을 설정하는 `/Manifest:<manifest file name>` 옵션을 지정할 수도 있습니다.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest

### 여러 파일로 분할 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy는 분할 데이터 파일 이름에서 *볼륨 인덱스*를 사용해 여러 파일을 구분합니다. 볼륨 인덱스는 *파티션 키 범위 인덱스*와 *분할 파일 인덱스*의 두 부분으로 구성됩니다. 두 인덱스는 모두 0부터 시작됩니다.

사용자가 `/PKRS` 옵션을 지정하지 않으면 파티션 키 범위 인덱스는 0이 됩니다.

예를 들어 사용자가 `/SplitSize` 옵션을 지정한 후 AzCopy에서 데이터 파일 두 개를 생성한다고 가정해 보겠습니다. 이 경우 결과 데이터 파일 이름은 다음과 같습니다.

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

`/SplitSize` 옵션에 사용할 수 있는 최소값은 32MB입니다. 지정된 대상이 Blob 저장소인 경우 AzCopy는 데이터 파일이 Blob 크기 제한(200GB)에 도달하면 사용자가 `/SplitSize` 옵션을 지정했는지 여부에 관계없이 데이터 파일을 분할합니다.

### JSON 또는 CSV 데이터 파일 형식으로 테이블 내보내기

기본적으로 AzCopy는 JSON 데이터 파일로 테이블을 내보냅니다. `/PayloadFormat:JSON|CSV` 옵션을 지정하여 JSON 또는 CSV 형태로 테이블을 내보낼 수 있습니다.

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV

CSV 페이로드 형식을 지정하는 경우 AzCopy는 각 데이터 파일에 대해 파일 확장명 `.schema.csv`을(를) 가진 스키마 파일도 생성합니다.

### 동시에 테이블 엔터티 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

사용자가 `/PKRS` 옵션을 지정하면 AzCopy는 엔터티를 내보내는 동시 작업을 시작합니다. 각 작업에서는 파티션 키 범위 하나를 내보냅니다.

`/NC` 옵션도 동시 작업 수를 제어합니다. AzCopy는 `/NC`가 지정되지 않은 경우에도 테이블 엔터티를 복사할 때 `/NC`의 기본값으로 코어 프로세서의 수를 사용합니다. 사용자가 `/PKRS` 옵션을 지정하는 경우 AzCopy는 두 값, 즉 파티션 키 범위와 암시적 또는 명시적으로 지정된 동시 작업의 수 중 더 작은 쪽을 사용하여 시작할 동시 작업의 수를 결정합니다. 자세한 내용을 확인하려면 명령줄에 `AzCopy /?:NC`를 입력하세요.

### Blob에 테이블 내보내기

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy는 다음 명명 규칙을 사용하여 Blob 컨테이너에 JSON 데이터 파일을 생성합니다.

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

생성된 JSON 데이터 파일은 최소 메타데이터의 페이로드 형식을 따릅니다. 이 페이로드 형식에 대한 자세한 내용은 [테이블 서비스 작업을 위한 페이로드 형식](http://msdn.microsoft.com/library/azure/dn535600.aspx)을 참조하세요.

테이블을 Blob로 내보낼 때 AzCopy는 테이블 엔터티를 로컬 임시 데이터 파일로 먼저 다운로드한 다음 Blob에 업로드합니다. 이러한 임시 데이터 파일은 기본 경로인 “<code>%LocalAppData%\\Microsoft\\Azure\\AzCopy</code>”로 저널 파일 폴더에 저장됩니다. 또한 /Z:[journal-file-folder] 옵션을 지정하여 저널 파일 폴더 위치와 임시 데이터 파일 위치를 변경할 수 있습니다. 임시 데이터 파일의 크기는 테이블 엔터티의 크기와 /SplitSize 옵션으로 지정한 크기에 의해 결정되며, 로컬 디스크의 임시 데이터 파일은 Blob에 업로드된 후 즉시 삭제됩니다. 임시 데이터 파일이 삭제되기 전에 저장하려면 로컬 디스크 공간이 충분한지 확인하세요.

## 테이블: 가져오기

### 테이블 가져오기

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace

`/EntityOperation` 옵션은 테이블에 엔터티를 삽입하는 방법을 나타냅니다. 가능한 값은 다음과 같습니다.

- `InsertOrSkip`: 기존 엔터티를 건너뛰거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
- `InsertOrMerge`: 기존 엔터티를 병합하거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.
- `InsertOrReplace`: 기존 엔터티를 바꾸거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.

가져오기 시나리오에서는 `/PKRS` 옵션을 지정할 수 없습니다. 동시 작업을 시작하려면 `/PKRS` 옵션을 지정해야 하는 내보내기 시나리오와는 달리 테이블을 가져올 때는 AzCopy가 기본적으로 동시 작업을 시작합니다. 시작되는 동시 작업의 기본 수는 코어 프로세서의 수와 같습니다. 그러나 `/NC` 옵션을 사용하여 다른 동시 작업 수를 지정할 수 있습니다. 자세한 내용을 확인하려면 명령줄에 `AzCopy /?:NC`를 입력하세요.

AzCopy는 CSV가 아닌 JSON 가져오기만 지원합니다. AzCopy는 사용자가 만든 JSON 및 매니페스트 파일에서 테이블 가져오기를 지원하지 않습니다. 이러한 파일 모두는 AzCopy 테이블 내보내기에서 가져와야 합니다. 오류를 방지하려면 내보낸 JSON 또는 매니페스트 파일을 수정하지 마십시오.

### Blob을 사용하여 테이블에 엔터티 가져오기

Blob 컨테이너에는 Azure 테이블과 해당 매니페스트 파일을 나타내는 JSON 파일을 포함하는 것으로 가정합니다.

	myaccount_mytable_20140103T112020.manifest
	myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

해당 Blob 컨테이너에 매니페스트 파일을 사용하여 엔터티를 테이블로 가져오려면 다음 명령을 실행할 수 있습니다.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"

## 기타 AzCopy 기능

### 대상에 없는 데이터만 복사

`/XO` 및 `/XN` 매개 변수를 통해 이전 또는 최신 소스 리소스를 각각 복사 작업에서 제외할 수 있습니다. 대상에 없는 소스 리소스만 복사하려는 경우 AzCopy 명령에 두 매개 변수를 지정할 수 있습니다.

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

	/Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

참고: 원본 또는 대상이 테이블인 경우 지원되지 않습니다.

### 지시 파일을 사용하여 명령줄 매개 변수 지정

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

지시 파일에 AzCopy 명령줄 매개 변수를 포함할 수 있습니다. AzCopy는 파일의 매개 변수가 마치 명령줄에 지정된 것처럼 처리하고 파일 내용을 직접적으로 대체합니다.

다음 줄을 포함하는 `copyoperation.txt`라는 지시 파일이 있고 한 줄 또는 여러 줄에 각 AzCopy 매개 변수를

	/Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

지정할 수 있습니다.

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S
	/Y

`/sourcekey` 매개 변수에 대해 여기에 표시된 것처럼 매개 변수를 두 줄로 분할하면 AzCopy는 실패합니다.

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	<sourcekey>
	/S
	/Y

### 여러 지시 파일을 사용하여 명령줄 매개 변수 지정

소스 컨테이너를 지정하는 `source.txt`라는 지시 파일이 있고,

	/Source:http://myaccount.blob.core.windows.net/mycontainer

파일 시스템의 대상 폴더를 지정하는 `dest.txt`라는 지시 파일이 있고,

	/Dest:C:\myfolder

AzCopy에 대한 옵션을 지정하는 `options.txt`라는 지시 파일이 있는 경우

	/S /Y

모두 `C:\responsefiles` 디렉터리에 있는 이러한 지시 파일을 사용하여 AzCopy를 호출하려면 다음 명령을 사용합니다.

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy는 명령줄에 개별 매개 변수를 모두 포함할 때처럼 이 명령을 처리합니다.

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

### SAS(공유 액세스 서명) 지정

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

또한 컨테이너 URI에서 SAS를 지정할 수도 있습니다.

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

### 저널 파일 폴더

AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.

저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다.

저널 파일에 대해 기본 위치를 사용하는 경우

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

`/Z` 옵션을 생략하거나 위에 표시된 것처럼 폴더 경로 없이 `/Z`를 지정하면 AzCopy는 기본 위치인 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`에 저널 파일을 만듭니다. 저널 파일이 이미 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

저널 파일의 사용자 지정 위치를 지정하려는 경우

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

이 예에서는 저널 파일이 없는 경우 파일을 만듭니다. 저널 파일이 있으면 AzCopy는 저널 파일을 기반으로 작업을 다시 시작합니다.

AzCopy 작업을 다시 시작하려는 경우

	AzCopy /Z:C:\journalfolder\

이 예에서는 완료하지 못했을 수 있는 마지막 작업을 다시 시작합니다.

### 로그 파일 생성

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

세부 정보 표시 로그에 대한 파일 경로를 제공하지 않고 `/V` 옵션을 지정하면 AzCopy는 기본 위치인 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`에 로그 파일을 만듭니다.

그렇지 않으면 사용자 지정 위치에 로그 파일을 만들 수 있습니다.

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

`/V:test/azcopy1.log`와 같이 `/V` 옵션 다음에 상대 경로를 지정하면 하위 폴더 `test` 내의 현재 작업 디렉터리에 세부 정보 표시 로그가 만들어집니다.

### 시작할 동시 작업 수 지정

`/NC` 옵션은 동시 복사 작업의 수를 지정합니다. AzCopy는 데이터 전송 처리량을 높이기 위해 기본적으로 특정 수의 동시 작업을 시작합니다. 테이블 작업의 경우 동시 작업 수는 가지고 있는 프로세서의 수와 같습니다. Blob 및 파일 작업의 경우 동시 작업 수는 가지고 있는 프로세서 수의 8배입니다. 저대역폭 네트워크에서 AzCopy를 실행하는 경우에는 리소스 경쟁으로 인한 실패를 방지하기 위해 /NC를 더 낮게 지정할 수 있습니다.

### Azure 저장소 에뮬레이터에 대해 AzCopy 실행

Blob 및 테이블용 [Azure 저장소 에뮬레이터](storage-use-emulator.md)에 대해 AzCopy를 실행할 수

	AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

있습니다.

	AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table

## AzCopy 매개 변수

AzCopy의 매개 변수는 아래에 설명되어 있습니다. 명령줄에서 다음 명령 중 하나를 입력하여 AzCopy 사용을 위한 도움말을 확인할 수도 있습니다.

- AzCopy에 대한 자세한 명령줄 도움말: `AzCopy /?`
- AzCopy 매개 변수와 관련된 자세한 도움말: `AzCopy /?:SourceKey`
- 명령줄 예제: `AzCopy /?:Samples`

### /Source:"source"

복사할 소스 데이터를 지정합니다. 소스는 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리, 저장소 파일 공유, 저장소 파일 디렉터리 또는 Azure 테이블일 수 있습니다.

**적용 대상:** Blob, 파일, 테이블

### /Dest:"destination"

복사할 대상을 지정합니다. 대상은 파일 시스템 디렉터리, Blob 컨테이너, Blob 가상 디렉터리, 저장소 파일 공유, 저장소 파일 디렉터리 또는 Azure 테이블일 수 있습니다.

**적용 대상:** Blob, 파일, 테이블

### /Pattern:"file-pattern"

복사할 파일을 나타내는 파일 패턴을 지정합니다. 선택적 /Pattern 매개 변수의 동작은 소스 데이터의 위치 및 재귀 모드 옵션 유무에 따라 결정됩니다. 재귀 모드는 /S 옵션을 통해 지정됩니다.

지정된 소스가 파일 시스템의 디렉터리이면 표준 와일드카드를 사용할 수 있으며 제공되는 파일 패턴과 일치하는 파일을 디렉터리 내에서 검색합니다. /S 옵션을 지정하면 AzCopy는 디렉터리 아래의 모든 하위 폴더에서 지정된 패턴과 일치하는 모든 파일을 찾습니다.

지정된 소스가 Blob 컨테이너 또는 가상 디렉터리인 경우 와일드카드가 지정되지 않습니다. /S 옵션을 지정하면 AzCopy는 지정된 파일 패턴을 Blob 접두사로 해석합니다. /S 옵션을 지정하지 않으면 AzCopy는 파일 패턴과 정확히 일치하는 Blob 이름을 찾습니다.

지정된 소스가 Azure 파일 공유이면 단일 파일을 복사할 정확한 파일 이름(예: abc.txt)을 지정하거나 /S 옵션을 지정하여 공유의 모든 파일을 재귀 방식으로 복사해야 합니다. 파일 패턴과 /S 옵션을 모두 지정하려고 하면 오류가 발생합니다.

AzCopy는 /Source가 blob 컨테이너 또는 blob 가상 디렉터리일 때 대/소문자를 구분해서 검색하고 다른 모든 경우에서 대/소문자를 구분하지 않습니다.

파일 패턴을 지정하지 않을 때 사용되는 기본 파일 패턴은 파일 시스템 위치의 경우 *.*이고 Azure 저장소 위치의 경우에는 빈 접두사입니다. 여러 파일 패턴을 지정할 수는 없습니다.

**적용 대상:** Blob, 파일

### /DestKey:"storage-key"

대상 리소스에 대한 저장소 계정 키를 지정합니다.

**적용 대상:** Blob, 파일, 테이블

### /DestSAS:"sas-token"

대상에 대해 읽기 및 쓰기 권한이 있는 SAS(공유 액세스 서명)를 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다.

대상 리소스가 Blob 컨테이너, 파일 공유 또는 테이블이면 이 옵션을 지정하고 SAS 토큰을 지정하거나 이 옵션 없이 대상 Blob 컨테이너, 파일 공유 또는 테이블 URI의 일부로 SAS를 지정할 수 있습니다.

소스와 대상이 모두 Blob이면 대상 Blob은 소스 Blob과 같은 저장소 계정 내에 있어야 합니다.

**적용 대상:** Blob, 파일, 테이블

### /SourceKey:"storage-key"

소스 리소스에 대한 저장소 계정 키를 지정합니다.

**적용 대상:** Blob, 파일, 테이블

### /SourceSAS:"sas-token"

소스에 대해 읽기 및 목록 권한이 있는 공유 액세스 서명을 지정합니다(해당되는 경우). SAS가 특수한 명령줄 문자를 포함할 수 있으므로 큰따옴표로 묶습니다.

소스 리소스가 Blob 컨테이너인 경우 키와 SAS를 모두 지정하지 않으면 익명 액세스를 통해 Blob 컨테이너를 읽습니다.

소스가 파일 공유 또는 테이블이면 키나 SAS를 지정해야 합니다.

**적용 대상:** Blob, 파일, 테이블

### /S

복사 작업의 재귀 모드를 지정합니다. 재귀 모드에서 AzCopy는 하위 폴더의 Blob 또는 파일을 비롯하여 지정된 파일 패턴과 일치하는 모든 Blob 또는 파일을 복사합니다.

**적용 대상:** Blob, 파일

### /BlobType:"block" | "page" | "append"

대상 Blob이 블록 Blob인지, 페이지 Blob인지 아니면 추가 Blob인지를 지정합니다. 이 옵션은 Blob을 업로드하는 경우에만 적용됩니다. 그렇지 않은 경우 오류가 생성합니다. 대상이 Blob인데 이 옵션을 지정하지 않으면 기본적으로 AzCopy는 블록 Blob를 만듭니다.

**적용 대상:** Blob

### /CheckMD5

다운로드한 데이터의 MD5 해시를 계산하고 Blob 또는 파일의 Content-MD5 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 기본적으로 이 MD5 검사는 해제되어 있으므로 데이터를 다운로드할 때 MD5 검사를 수행하도록 이 옵션을 지정해야 합니다.

Azure 저장소는 Blob 또는 파일에 대해 저장된 MD5 해시가 최신 버전임을 보장하지 않습니다. Blob 또는 파일이 수정될 때마다 MD5를 업데이트하는 것은 클라이언트의 책임입니다.

AzCopy는 Azure Blob 또는 파일을 서비스로 업로드하기 전에 항상 해당 Content-MD5 속성을 설정합니다.

**적용 대상:** Blob, 파일

### /Snapshot

스냅숏을 전송할지 여부를 나타냅니다. 이 옵션은 소스가 Blob일 때만 유효합니다.

전송된 Blob 스냅숏의 이름을 blob-name (snapshot-time).extension 형식으로 바꿉니다.

기본적으로 스냅숏은 복사되지 않습니다.

**적용 대상:** Blob

### /V:[verbose-log-file]

세부 정보 표시 상태 메시지를 로그 파일로 출력합니다.

기본적으로 세부 정보 로그 파일은 `%LocalAppData%\Microsoft\Azure\AzCopy`에서 AzCopyVerbose.log라는 이름이 지정됩니다. 이 옵션에 기존 파일 위치를 지정하면 세부 정보 표시 로그가 해당 파일에 추가됩니다.

**적용 대상:** Blob, 파일, 테이블

### /Z:[journal-file-folder]

작업을 다시 시작하기 위한 저널 파일 폴더를 지정합니다.

AzCopy는 작업이 중단된 경우 항상 다시 시작을 지원합니다.

이 옵션을 지정하지 않거나 폴더 경로 없이 지정하면 AzCopy는 기본 위치인 %LocalAppData%\\Microsoft\\Azure\\AzCopy에 저널 파일을 만듭니다.

AzCopy로 명령을 실행할 때마다 AzCopy는 기본 폴더에 저널 파일이 있는지 또는 이 옵션을 통해 지정한 폴더에 있는지 확인합니다. 저널 파일이 이 두 위치에 없으면 AzCopy는 이 작업을 새 작업으로 취급하고 새 저널 파일을 생성합니다.

저널 파일이 존재하면 AzCopy는 입력한 명령줄이 저널 파일의 명령줄과 일치하는지 확인합니다. 두 명령줄이 일치하면 AzCopy는 불완전한 작업을 다시 시작합니다. 일치하지 않으면 저런 파일을 덮어써서 새 작업을 시작할지 또는 현재 작업을 취소할지 묻는 메시지가 표시됩니다.

작업이 성공적으로 완료되면 저널 파일이 삭제됩니다.

이전 버전의 AzCopy에서 만들어진 저널 파일에서 작업을 다시 시작하는 것은 지원되지 않습니다.

**적용 대상:** Blob, 파일, 테이블

### /@:"parameter-file"

매개 변수를 포함하는 파일을 지정합니다. AzCopy는 파일의 매개 변수가 명령줄에 지정된 것처럼 처리합니다.

지시 파일에서 단일 줄에 여러 매개 변수를 지정하거나 한 줄에 매개 변수를 하나씩 지정할 수 있습니다. 하나의 매개 변수가 여러 줄에 걸쳐 있을 수 없습니다.

지시 파일은 # 기호로 시작하는 명령줄을 포함할 수 있습니다.

여러 지시 파일을 지정할 수 있습니다. 그렇지만 AzCopy는 중첩된지시 파일을 지원하지 않습니다.

**적용 대상:** Blob, 파일, 테이블

### /Y

모든 AzCopy 확인 프롬프트를 표시하지 않습니다.

**적용 대상:** Blob, 파일, 테이블

### /L

열거 작업만 지정하고 데이터는 복사되지 않습니다.

AzCopy는 이 옵션의 사용을 이 옵션 /L 없이 명령줄을 실행하기 위한 시뮬레이션으로 해석하고 복사할 개체의 수를 계산하므로, 사용자는 /V 옵션을 동시에 지정하여 자세한 로그에 복사할 개체를 확인할 수 있습니다.

이 옵션의 동작은 소스 데이터의 위치와 재귀 모드 옵션 /S 및 파일 패턴 옵션 /Pattern의 유무에 따라 결정됩니다.

AzCopy는 이 옵션을 사용하는 경우 이 원본 위치에 대한 목록 및 읽기 권한이 필요합니다.

**적용 대상:** Blob, 파일

### /MT

다운로드한 파일의 마지막으로 수정한 시간을 소스 Blob 또는 파일과 동일하게 설정합니다.

**적용 대상:** Blob, 파일

### /XN

최신 소스 리소스를 제외합니다. 원본이 마지막으로 수정된 시간이 대상과 동일하거나 더 최신인 경우 리소스가 복사되지 않습니다.

**적용 대상:** Blob, 파일

### /XO

오래된 소스 리소스를 제외합니다. 원본이 마지막으로 수정된 시간이 대상과 동일하거나 더 오래된 경우 리소스가 복사되지 않습니다.

**적용 대상:** Blob, 파일

### /A

Archive 특성 집합이 있는 파일만 업로드합니다.

**적용 대상:** Blob, 파일

### /IA:[RASHCNETOI]

지정된 특성 집합 중 하나라도 있는 파일만 업로드합니다.

사용 가능한 특성에는 다음이 포함됩니다.

- R = 읽기 전용 파일
- A = 보관 준비가 된 파일
- S = 시스템 파일
- H = 숨김 파일
- C = 압축 파일
- N = 일반 파일
- E = 암호화된 파일
- T = 임시 파일
- O = 오프라인 파일
- I = 인덱싱되지 않은 파일

**적용 대상:** Blob, 파일

### /XA:[RASHCNETOI]

지정된 특성 집합 중 하나라도 있는 파일을 제외합니다.

사용 가능한 특성에는 다음이 포함됩니다.

- R = 읽기 전용 파일
- A = 보관 준비가 된 파일
- S = 시스템 파일
- H = 숨김 파일
- C = 압축 파일
- N = 일반 파일
- E = 암호화된 파일
- T = 임시 파일
- O = 오프라인 파일
- I = 인덱싱되지 않은 파일

**적용 대상:** Blob, 파일

### /Delimiter:"delimiter"

Blob 이름에서 가상 디렉터리를 구분하는 데 사용되는 구분 기호를 나타냅니다.

기본적으로 AzCopy는 구분 문자로 /를 사용합니다. 그렇지만 AzCopy는 아무 일반 문자(예: @, # 또는 %)를 구분 문자로 사용할 수 있게 지원합니다. 명령줄에 이러한 특수 문자 중 하나를 포함해야 하는 경우 파일 이름을 큰따옴표로 묶으세요.

이 옵션은 Blob을 다운로드하는 데만 적용됩니다.

**적용 대상:** Blob

### /NC:"number-of-concurrent-operations"

동시 작업의 수를 지정합니다.

AzCopy는 데이터 전송 처리량을 높이기 위해 기본적으로 특정 수의 동시 작업을 시작합니다. 저대역폭 환경에서는 많은 수의 동시 작업으로 네트워크 연결에 과부하가 걸려 작업이 완전히 실행되지 못할 수 있습니다. 사용 가능한 실제 네트워크 대역폭에 따라 동시 작업을 조절하세요.

동시 작업의 상한은 512개입니다.

**적용 대상:** Blob, 파일, 테이블

### /SourceType:"Blob" | "Table"

`source` 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob로 지정합니다.

**적용 대상:** Blob, 테이블

### /DestType:"Blob" | "Table"

`destination` 리소스를 저장소 에뮬레이터에서 실행되는 로컬 개발 환경에서 사용할 수 있는 Blob로 지정합니다.

**적용 대상:** Blob, 테이블

### /PKRS:"key1#key2#key3#..."

테이블 데이터를 병렬로 내보낼 수 있도록 파티션 키 범위를 분할합니다. 그러면 내보내기 작업의 속도가 빨라집니다.

이 옵션을 지정하지 않으면 AzCopy는 단일 스레드를 사용하여 테이블 엔터티를 내보냅니다. 예를 들어 사용자가 /PKRS:"aa#bb"를 지정하면 AzCopy는 3개 동시 작업을 시작합니다.

각 작업에서는 아래에 나와 있는 것처럼 3개 파티션 키 범위 중 하나를 내보냅니다.

  [첫 번째 파티션 키, aa)

  [aa, bb)

  [bb, 마지막 파티션 키]

**적용 대상:** 테이블

### /SplitSize:"file-size"

내보내는 파일의 분할 크기를 지정합니다(MB 단위). 허용되는 최소 값은 32입니다.

이 옵션을 지정하지 않으면 AzCopy는 단일 파일로 테이블 데이터를 내보냅니다.

테이블 데이터를 Blob로 내보내는데 내보낸 파일 크기가 Blob 크기에 대한 제한인 200GB에 도달하면 AzCopy는 이 옵션을 지정하지 않은 경우에도 내보낸 파일을 분할합니다.

**적용 대상:** 테이블

### /EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

테이블 데이터 가져오기 동작을 지정합니다.

- InsertOrSkip - 기존 엔터티를 건너뛰거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.

- InsertOrMerge - 기존 엔터티를 병합하거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.

- InsertOrReplace - 기존 엔터티를 바꾸거나 테이블에 엔터티가 없으면 새 엔터티를 삽입합니다.

**적용 대상:** 테이블

### /Manifest:"manifest-file"

테이블 내보내기 및 가져오기 작업을 위한 매니페스트 파일을 지정합니다.

내보내기 작업 중에는 이 옵션이 선택 사항이므로 이 옵션을 지정하지 않으면 AzCopy는 미리 정의된 이름의 매니페스트 파일을 생성합니다.

이 옵션은 데이터 파일 찾기를 위한 가져오기 작업 중에 필요합니다.

**적용 대상:** 테이블

### /SyncCopy

두 Azure 저장소 끝점 간에 Blob 또는 파일을 동기적으로 복사할지 여부를 나타냅니다.

기본적으로 AzCopy에서는 서버 쪽 비동기 복사를 사용합니다. Blob 또는 파일을 로컬 메모리에 다운로드한 다음 Azure 저장소에 업로드하는 동기 복사를 수행하려면 이 옵션을 지정합니다.

Blob 저장소 내에서, 파일 저장소 내에서 또는 Blob 저장소에서 파일 저장소로 혹은 그 반대로 파일을 복사할 때 이 옵션을 사용할 수 있습니다.

**적용 대상:** Blob, 파일

### /SetContentType:"content-type"

대상 Blob 또는 파일의 MIME 콘텐츠 형식을 지정합니다.

기본적으로 AzCopy에서는 Blob 또는 파일의 콘텐츠 형식을 application/octet-stream으로 설정합니다. 이 옵션에 대해 값을 명시적으로 지정하면 모든 Blob 또는 파일의 콘텐츠 형식을 설정할 수 있습니다.

값 없이 이 옵션을 지정하면 AzCopy에서 각 Blob 또는 파일의 콘텐츠 형식을 파일 확장명에 따라 설정합니다.

**적용 대상:** Blob, 파일

### /PayloadFormat:"JSON" | "CSV"

테이블의 내보낸 데이터 파일의 형식을 지정합니다.

이 옵션을 지정하지 않으면 기본적으로 AzCopy는 JSON 형식으로 테이블 데이터 파일을 내보냅니다.

**적용 대상:** 테이블

## 알려진 문제 및 모범 사례

### 데이터를 복사하는 동안 동시 쓰기 제한

AzCopy를 사용하여 Blob 또는 파일을 복사할 때는 복사하는 동안 다른 응용 프로그램이 데이터를 수정할 수 있다는 사실을 유의해야 합니다. 가능한 경우 복사 중인 데이터가 복사 작업 중에 수정되지 않도록 합니다. 예를 들어 Azure 가상 컴퓨터와 연결된 VHD를 복사할 때는 다른 응용 프로그램이 현재 VHD에 쓰고 있지 않은지 확인합니다. 이렇게 하려면 복사할 리소스를 임대하는 것이 좋습니다. 또는 먼저 VHD의 스냅샷을 만든 후 스냅샷을 복사할 수 있습니다.

다른 응용 프로그램이 복사 중인 Blob 또는 파일에 쓰지 못하게 할 수 없으면 작업이 완료될 때까지 복사된 리소스가 소스 리소스와 더 이상 완전히 동일하지 않을 수 있습니다.

### 한 컴퓨터에서 AzCopy 인스턴스 하나를 실행합니다.
AzCopy는 데이터 전송 속도를 높이기 위해 컴퓨터 리소스를 최대한 활용할 수 있도록 설계되었습니다. 따라서 컴퓨터 한 대에서 AzCopy 인스턴스를 하나만 실행하는 것이 좋으며 더 많은 동시 작업을 수행해야 하는 경우에는 `/NC` 옵션을 지정해야 합니다. 자세한 내용을 확인하려면 명령줄에 `AzCopy /?:NC`를 입력하세요.

### "암호화, 해시 및 서명에 FIPS 호환 알고리즘을 사용"할 경우 AzCopy에 대해 FIPS 규격 MD5 알고리즘을 사용하도록 설정합니다.
기본적으로 AzCopy는 개체를 복사할 때 .NET MD5 구현을 사용하여 MD5를 계산하지만 보안 요구 때문에 FIPS 규격 MD5 설정을 사용하도록 설정하는 데 AzCopy가 필요합니다.

`AzureStorageUseV1MD5` 속성을 사용하여 app.config 파일(`AzCopy.exe.config`)을 만들고 AzCopy.exe를 통해 일단 사용을 보류할 수 있습니다.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <appSettings>
	    <add key="AzureStorageUseV1MD5" value="false"/>
	  </appSettings>
	</configuration>

속성 "AzureStorageUseV1MD5" • True - 기본값 AzCopy는 .NET MD5 구현을 사용합니다. • False - AzCopy는 FIPS 규격 MD5 알고리즘을 사용합니다.

FIPS 규격 알고리즘은 Windows 컴퓨터에는 기본적으로 사용되지 않도록 설정되어 있으며 실행 창에 secpol.msc를 입력하고 보안 설정-> 로컬 정책-> 보안 옵션->시스템 암호화: 암호화, 해시, 서명에 FIPS 규격 알고리즘 사용에서 이 스위치를 선택할 수 있습니다.

## 다음 단계

Azure 저장소 및 AzCopy에 대한 자세한 내용은 다음 리소스를 참조하세요.

### Azure 저장소 설명서

- [Azure 저장소 소개](storage-introduction.md)
- [.NET에서 Blob 저장소를 사용하는 방법](storage-dotnet-how-to-use-blobs.md)
- [.NET에서 파일 저장소를 사용하는 방법](storage-dotnet-how-to-use-files.md)
- [.NET에서 테이블 저장소를 사용하는 방법](storage-dotnet-how-to-use-tables.md)
- [저장소 계정을 만들거나, 관리하거나, 삭제하는 방법](storage-create-storage-account.md)

### Azure 저장소 블로그 게시물:
- [Azure 저장소 데이터 이동 라이브러리 미리 보기 소개](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
- [AzCopy: 동기 복사본 및 사용자 지정 콘텐츠 형식 소개(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: AzCopy 3.0의 일반 공급 및 테이블 및 파일을 지원하는 AzCopy 4.0의 미리 보기 릴리스 발표(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: 대량 복사 시나리오에 맞게 최적화(영문)](http://go.microsoft.com/fwlink/?LinkId=507682)
- [AzCopy: 읽기 액세스 지역 중복 저장소 지원(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: 다시 시작 가능 모드 및 SAS 토큰으로 데이터 전송(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: 크로스 계정 Blob 복사 사용(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Azure Blob 파일 업로드/다운로드(영문)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

<!---HONumber=AcomDC_0921_2016-->