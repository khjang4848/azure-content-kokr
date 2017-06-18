<properties
	pageTitle="AzCopy를 사용하여 Azure Blob 저장소의 데이터 이동 | Microsoft Azure"
	description="AzCopy를 사용하여 Azure Blob 저장소의 데이터 이동"
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev" />

# AzCopy를 사용하여 Azure Blob 저장소의 데이터 이동

AzCopy는 Microsoft Azure Blob, 파일 및 테이블 저장소에 대해 데이터 업로드, 다운로드 및 복사를 수행하도록 디자인된 명령줄 유틸리티입니다.

AzCopy 설치 지침 및 Azure 플랫폼에서 사용하는 방법에 대한 추가 정보는 [AzCopy 명령줄 유틸리티 시작](../storage/storage-use-azcopy.md)을 참조하세요.

Azure Blob 저장소로 및/또는 저장소에서 데이터를 이동하는 데 사용되는 기술 지침은 여기에 연결되어 있습니다.

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] [Azure의 데이터 과학 가상 컴퓨터](machine-learning-data-science-virtual-machines.md)에서 제공하는 스크립트를 통해 설정된 VM을 사용하는 경우 AzCopy가 VM에 이미 설치되어 있습니다.

> [AZURE.NOTE] Azure Blob 저장소에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../storage/storage-dotnet-how-to-use-blobs.md) 및 [Azure Blob 서비스](https://msdn.microsoft.com/library/azure/dd179376.aspx)를 참조하세요.


## 필수 조건

이 문서에서는 사용자에게 Azure 구독, 저장소 계정 및 계정에 해당하는 저장소 키가 있다고 가정합니다. 데이터를 업로드/다운로드하려면 Azure 저장소 계정 이름 및 계정 키를 알아야 합니다.

- Azure 구독을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 저장소 계정을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md)를 참조하세요.


## AzCopy 명령 실행

AzCopy 명령을 실행하려면 명령 창을 열고 컴퓨터의 AzCopy 설치 디렉터리로 이동합니다. 이 디렉터리에 AzCopy.exe 실행 파일이 있습니다.

AzCopy 명령의 기본 구문은 다음과 같습니다.

	AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] 시스템 경로에 AzCopy 설치 위치를 추가하고 임의 디렉터리의 명령을 실행합니다. 기본적으로 AzCopy는 *%ProgramFiles (x86) %\\Microsoft SDKs\\Azure\\AzCopy* 또는 *%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy*에 설치됩니다.

## Azure blob에 파일 업로드

파일을 업로드하려면 다음 명령을 사용합니다.

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## Azure blob에서 파일 다운로드

Azure blob에서 파일을 다운로드하려면 다음 명령을 사용합니다.

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## Azure 컨테이너 간 Blob 전송

Azure 컨테이너 간 Blob을 전송하려면 다음 명령을 사용합니다.

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## AzCopy 사용에 대한 팁

> [AZURE.TIP]   
> 1. 파일을 **업로드**하는 경우 */S*는 파일을 재귀적으로 업로드합니다. 이 매개 변수가 없으면 하위 디렉터리의 파일이 업로드되지 않습니다.
> 2. 파일을 **다운로드**하는 경우 */S*는 지정된 디렉터리 및 하위 디렉터리의 모든 파일 또는 제공된 디렉터리 및 하위 디렉터리에 지정된 패턴과 일치하는 모든 파일이 다운로드될 때까지 컨테이너를 재귀적으로 검색합니다.
> 3.  */Source* 매개 변수를 사용하여 **특정 blob 파일**을 다운로드하도록 지정할 수 없습니다. 특정 파일을 다운로드하려면 */Pattern* 매개 변수를 사용하여 다운로드할 blob 파일 이름을 지정하세요. **/S** 매개 변수를 사용하면 AzCopy에서 파일 이름 패턴을 재귀적으로 검색할 수 있습니다. 패턴 매개 변수가 없으면 AzCopy는 해당 디렉터리의 모든 파일을 다운로드합니다.

<!---HONumber=AcomDC_0921_2016-->