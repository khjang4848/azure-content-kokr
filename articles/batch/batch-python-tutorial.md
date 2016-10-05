<properties
	pageTitle="자습서 - Azure 배치 Python 클라이언트 시작 | Microsoft Azure"
	description="Azure 배치의 기본 개념과 간단한 시나리오를 통해 배치 서비스를 개발하는 방법을 알아봅니다."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/27/2016"
	ms.author="marsma"/>

# Azure 배치 Python 클라이언트 시작

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Python에서 작성한 작은 배치 응용 프로그램을 다룬 것처럼 [Azure 배치][azure_batch] 및 [배치 Python][py_azure_sdk] 클라이언트의 기본을 알아봅니다. 두 개의 샘플 스크립트에서는 배치 서비스를 사용하여 클라우드의 Linux 가상 컴퓨터에서 병렬 워크로드를 처리하는 방법과 파일 준비 및 검색을 위해 [Azure Storage](./../storage/storage-introduction.md)와 상호 작용하는 방식을 살펴봅니다. 일반적인 배치 응용 프로그램 워크플로를 습득하고 작업, 태스크, 풀 및 계산 노드와 같은 배치의 주요 구성 요소에 대해 이해합니다.

![배치 솔루션 워크플로(기본)][11]<br/>

## 필수 조건

이 문서에는 사용자가 Python에 대한 실용적인 지식을 가지고 Linux에 익숙하다고 가정합니다. 또한 Azure와 배치 및 저장소 서비스에 대해 아래 지정된 계정 생성 요구 사항을 충족할 수 있다고 가정합니다.

### 계정

- **Azure 계정**: Azure 구독이 아직 없는 경우 [무료 Azure 계정][azure_free_account]을 만듭니다.
- **배치 계정**: Azure 구독이 있으면 [Azure 배치 계정을 만듭니다](batch-account-create-portal.md).
- **저장소 계정**: [Azure 저장소 계정 정보](../storage/storage-create-storage-account.md#create-a-storage-account)의 [저장소 계정 만들기](../storage/storage-create-storage-account.md) 섹션을 참조하세요.

### 코드 샘플

Python 자습서 [코드 샘플][github_article_samples]은 GitHub의 [azure-batch-samples][github_samples] 리포지토리에서 찾은 많은 배치 코드 샘플 중 하나입니다. 리포지토리 홈 페이지에서 **복제 또는 다운로드 > ZIP 다운로드** 단추를 클릭하거나 [azure-batch-samples-master.zip][github_samples_zip] 직접 다운로드 링크를 클릭하여 모든 샘플을 다운로드할 수 있습니다. ZIP 파일의 내용을 추출하면 이 자습서에 대한 두 개의 스크립트는 `article_samples` 디렉터리에 있습니다.

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/> `/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### Python 환경

로컬 워크스테이션에서 *python\_tutorial\_client.py* 샘플 스크립트를 실행하려면 **2.7** 또는 **3.3+**와 호환되는 **Python 인터프리터**가 필요합니다. 스크립트는 Linux와 Windows 모두에서 테스트되었습니다.

### 암호화 종속성

`azure-batch` 및 `azure-storage` Python 패키지에 필수적인 [암호화][crypto] 라이브러리에 대한 종속성을 설치해야 합니다. 자세한 내용은 플랫폼에 적합한 다음 작업 중 하나를 수행하거나 [암호화 설치][crypto_install] 세부 정보를 참조하세요.

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] Linux에서 Python 3.3+를 설치하는 경우 Python 종속성에 해당하는 python3을 사용합니다. 예를 들어 Ubuntu에서 `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### Azure 패키지

또한 **Azure Batch** 및 **Azure Storage** Python 패키지를 설치합니다. 다음에 위치한 **pip** 및 *requirements.txt*를 사용하여 수행할 수 있습니다.

`/azure-batch-samples/Python/Batch/requirements.txt`

다음 **pip** 명령을 실행하여 배치 및 저장소 패키지를 설치합니다.

`pip install -r requirements.txt`

또는 [azure-batch][pypi_batch] 및 [azure-storage][pypi_storage] Python 패키지를 수동으로 설치할 수 있습니다.

`pip install azure-batch`<br/> `pip install azure-storage`

> [AZURE.TIP] 권한 없는 계정을 사용하는 경우 명령의 접두사로 `sudo`을 사용합니다. 예: `sudo pip install -r requirements.txt` Python 패키지를 설치하는 방법에 대한 자세한 내용은 readthedocs.io에서 [패키지 설치][pypi_install]를 참조하세요.

## 배치 Python 자습서 코드 샘플

배치 Python 자습서 코드 샘플은 두 개의 Python 스크립트 및 몇 가지 데이터 파일로 구성됩니다.

- **python\_tutorial\_client.py**: 배치 및 저장소 서비스와 상호 작용하여 계산 노드(가상 컴퓨터)에서 병렬 워크로드를 실행합니다. 로컬 워크스테이션에서 *python\_tutorial\_client.py* 스크립트를 실행합니다.

- **python\_tutorial\_task.py**: 실제 작업을 수행하기 위해 Azure의 계산 노드에서 실행되는 스크립트입니다. 이 예제에서 *python\_tutorial\_task.py*는 Azure Storage에서 다운로드한 파일(입력 파일)의 텍스트를 구문 분석합니다. 그 후 입력 파일에 표시되는 맨 위 단어 세 개를 포함하는 텍스트 파일(출력 파일)을 생성합니다. 출력 파일을 만든 후에 *python\_tutorial\_task.py*는 Azure Storage에 파일을 업로드합니다. 그러면 워크스테이션에서 실행되는 클라이언트 스크립트에 다운로드할 수 있습니다. *python\_tutorial\_task.py*는 배치 서비스의 여러 계산 노드에서 병렬로 실행합니다.

- **./data/taskdata*.txt**: 이러한 세 개의 텍스트 파일은 계산 노드에서 실행되는 태스크에 대한 입력을 제공합니다.

다음 다이어그램에서는 클라이언트 및 작업 스크립트에서 수행되는 기본 작업을 보여 줍니다. 이러한 기본 워크플로는 배치를 사용하여 만드는 많은 계산 솔루션의 일반적인 형태입니다. 배치 서비스에서 사용 가능한 모든 기능을 보여 주지 않지만 거의 모든 배치 시나리오는 이 워크플로의 일부를 포함합니다.

![배치 예제 워크플로][8]<br/>

[**1단계.**](#step-1-create-storage-containers) Azure Blob 저장소에 **컨테이너**를 만듭니다.<br/> [**2단계.**](#step-2-upload-task-script-and-data-files) 컨테이너에 태스크 스크립트 및 입력 파일을 업로드합니다.<br/> [**3단계.**](#step-3-create-batch-pool) 배치 **풀**을 만듭니다.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3a.** **StartTask** 풀은 노드가 풀에 연결되면 태스크 스크립트 (python\_tutorial\_task.py)를 노드에 다운로드합니다.<br/> [**4단계.**](#step-4-create-batch-job) 배치 **작업**을 만듭니다.<br/> [**5단계.**](#step-5-add-tasks-to-job) 작업에 **태스크**를 추가합니다.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5a.** 태스크는 노드에서 실행되도록 예약됩니다.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5b.** 각 태스크는 Azure Storage에서 입력 데이터를 다운로드한 다음 실행을 시작합니다.<br/> [**6단계.**](#step-6-monitor-tasks) 태스크를 모니터링합니다.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6a.** 태스크가 완료되면 출력 데이터를 Azure Storage에 업로드합니다.<br/> [**7단계.**](#step-7-download-task-output) 저장소에서 태스크 출력을 다운로드합니다.

언급한 바와 같이, 모든 Batch 솔루션이 정확히 이러한 단계를 수행하는 것은 아니며, 훨씬 더 많은 단계를 포함할 수 있지만 이 샘플은 배치 솔루션에서 찾을 수 있는 일반적인 프로세스를 보여 줍니다.

## 클라이언트 스크립트 준비

이 샘플을 실행하기 전에 배치 및 저장소 계정 자격 증명을 *python\_tutorial\_client.py*에 추가합니다. 아직 수행하지 않은 경우 좋아하는 편집기에서 파일을 열고 자격 증명으로 다음 줄을 업데이트합니다.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

[Azure 포털][azure_portal]의 각 서비스의 계정 블레이드 내에서 배치 및 저장소 계정 자격 증명을 찾을 수 있습니다.

![포털의 배치 자격 증명][9] ![포털의 저장소 자격 증명][10]<br/>

다음 섹션에서는 스크립트에서 사용하는 단계를 분석하여 배치 서비스의 워크로드를 처리합니다. 문서의 나머지 부분에 대해 알아보는 동안 편집기에서 스크립트를 정기적으로 참조하는 것이 좋습니다.

**python\_tutorial\_client.py**의 다음 줄로 이동하여 1단계부터 시작합니다.

```python
if __name__ == '__main__':
```

## 1단계: 저장소 컨테이너 만들기

![Azure 저장소에 컨테이너 만들기][1] <br/>

배치에는 Azure 저장소와의 상호 작용을 위해 기본 제공되는 지원이 포함됩니다. 저장소 계정 내의 컨테이너는 배치 계정에서 실행되는 태스크에 필요한 파일을 제공합니다. 컨테이너는 태스크가 생성하는 출력 데이터를 저장할 공간도 제공합니다. *python\_tutorial\_client.py* 스크립트가 실행하는 첫 번째 작업은 [Azure Blob 저장소](../storage/storage-introduction.md#blob-storage)에 세 개의 컨테이너를 만드는 것입니다.

- **응용 프로그램**: 이 컨테이너는 *python\_tutorial\_task.py* 태스크에서 실행하는 Python 스크립트를 저장합니다.
- **입력**: 태스크가 *입력* 컨테이너에서 처리할 데이터 파일을 다운로드합니다.
- **출력**: 태스크가 입력 파일의 처리를 완료하면 그 결과를 *출력* 컨테이너에 업로드합니다.

저장소 계정과 상호 작용하고 컨테이너를 만들기 위해 [azure-storage][pypi_storage] 패키지를 사용하여 [BlockBlobService][py_blockblobservice] 개체인 "Blob 클라이언트"를 만듭니다. 그런 다음 Blob 클라이언트를 사용하여 저장소 계정에 세 개의 컨테이너를 만듭니다.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

컨테이너를 만든 후 이제 응용 프로그램은 작업에 의해 사용될 파일을 업로드할 수 있습니다.

> [AZURE.TIP] [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md)는 Azure 저장소 컨테이너 및 Blob을 사용한 작업의 개요를 제공합니다. 배치 작업 시작 단계 읽기 목록의 거의 위쪽에 있습니다.

## 2단계: 작업 스크립트 및 데이터 파일 업로드

![컨테이너에 작업 응용 프로그램 및 입력(데이터) 파일 업로드][2] <br/>

파일 업로드 작업에서 *python\_tutorial\_client.py*는 먼저 로컬 컴퓨터에 있는 **응용 프로그램**의 컬렉션 및 **입력** 파일 경로를 지정합니다. 그런 다음 이전 단계에서 만든 컨테이너에 이러한 파일을 업로드합니다.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

목록 축약을 사용하여 컬렉션의 각 파일에 `upload_file_to_container` 함수를 호출하고 두 개의 [ResourceFile][py_resource_file] 컬렉션이 채워집니다. `upload_file_to_container` 함수가 다음과 같이 나타납니다.

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### ResourceFiles

[ResourceFile][py_resource_file]은 해당 작업을 실행하기 전에 계산 노드에 다운로드되는 Azure Storage의 파일에 대한 URL로 배치의 작업을 제공합니다. [ResourceFile][py_resource_file].**blob\_source** 속성은 Azure Storage에 있는 파일의 전체 URL을 지정합니다. URL에는 파일에 대한 보안 액세스를 제공하는 SAS(공유 액세스 서명)가 포함될 수 있습니다. 배치에서 대부분의 태스크 형식은 다음을 포함하는 *ResourceFiles* 속성을 포함합니다.

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

이 샘플은 JobPreparationTask 또는 JobReleaseTask 태스크 유형을 사용하지 않지만 [Azure 배치 계산 노드에서 작업 준비와 완료 태스크 실행](batch-job-prep-release.md)에서 이에 대해 자세히 알아볼 수 있습니다.

### 공유 액세스 서명(SAS)

공유 액세스 서명은 Azure 저장소의 컨테이너 및 Blob에 대한 보안 액세스를 제공하는 문자열입니다. *python\_tutorial\_client.py* 스크립트는 Blob 및 컨테이너 공유 액세스 서명을 모두 사용하고 저장소 서비스에서 이러한 공유 액세스 서명 문자열을 가져오는 방법을 보여 줍니다.

- **Blob 공유 액세스 서명**: 풀의 StartTask는 저장소에서 태스크 스크립트 및 입력 데이터 파일을 다운로드하는 경우 Blob 공유 액세스 서명을 사용합니다(아래 [3단계](#step-3-create-batch-pool) 참조). *python\_tutorial\_client.py*의 `upload_file_to_container` 함수는 각 Blob의 공유 액세스 서명을 가져오는 코드를 포함합니다. 저장소 모듈에서 [BlockBlobService.make\_blob\_url][py_make_blob_url]를 호출하여 수행합니다.

- **컨테이너 공유 액세스 서명**: 각 태스크가 계산 노드에서 작업을 완료하면 해당 출력 파일을 Azure Storage의 *출력* 컨테이너에 업로드합니다. 이렇게 하려면 *python\_tutorial\_task.py*는 컨테이너에 대한 쓰기 권한을 제공하는 컨테이너 공유 액세스 서명을 사용합니다. *python\_tutorial\_client.py*의 `get_container_sas_token` 함수는 컨테이너의 공유 액세스 서명을 가져오며 이는 태스크에 대한 명령줄 인수로 전달됩니다. 5단계 [작업에 태스크 추가](#step-5-add-tasks-to-job)에서는 컨테이너 SAS를 사용하는 방법을 설명합니다.

> [AZURE.TIP] 공유 액세스 서명에 대해 두 부분으로 이루어진 시리즈, [1부: SAS 모델 이해](../storage/storage-dotnet-shared-access-signature-part-1.md) 및 [2부: Blob 서비스를 통해 SAS 만들기 및 사용](../storage/storage-dotnet-shared-access-signature-part-2.md)을 확인하여 저장소 계정의 데이터에 대한 보안 액세스 제공에 대한 자세한 내용을 확인합니다.

## 3단계: 배치 풀 만들기

![배치 풀 만들기][3] <br/>

배치 **풀**은 배치가 작업의 태스크를 실행하는 계산 노드(가상 컴퓨터)의 컬렉션입니다.

저장소 계정에 태스크 스크립트 및 데이터 파일을 업로드한 후에 *python\_tutorial\_client.py*은 배치 Python 모듈을 사용하여 배치 서비스와 상호 작용하기 시작합니다. 이렇게 하려면 [BatchServiceClient][py_batchserviceclient]를 만듭니다.

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

그런 다음 `create_pool`에 대한 호출을 사용하여 배치 계정에서 계산 노드의 풀을 만듭니다.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

풀을 만들 경우 풀에 대한 몇 가지 속성을 지정하는 [PoolAddParameter][py_pooladdparam]를 정의합니다.

- 풀의 **ID**(*id* - 필수)<p/>배치에서 대부분의 엔터티처럼 새 풀은 배치 계정 내에서 고유 ID를 가지고 있어야 합니다. 코드는 해당 ID를 사용하여 이 풀을 참조하고 이를 통해 Azure [포털][azure_portal]에서 풀을 확인합니다.

- **계산 노드 수**(*target\_dedicated* - 필수)<p/>이 속성은 풀에 배포할 VM 수를 지정합니다. 모든 배치 계정에서 배치 계정에 있는 **코어**(및 계산 노드)의 수를 제한하는 기본 **할당량**이 있어야 합니다. 기본 할당량 및 [할당량을 증가](batch-quota-limit.md#increase-a-quota)하는 방법에 대한 지침(예: 배치 계정의 최대 코어 수)은 [Azure 배치 서비스에 대한 할당량 및 제한](batch-quota-limit.md)에서 찾을 수 있습니다. "풀이 X 노드보다 더 멀리 도달할 수 없나요?"하는 질문의 경우 이 코어 할당량이 원인일 수 있습니다.

- 노드에 대한 **운영 체제**(*virtual\_machine\_configuration* **또는** *cloud\_service\_configuration* - 필수)<p/>*python\_tutorial\_client.py*에서 [VirtualMachineConfiguration][py_vm_config]을 사용하여 Linux 노드의 풀을 만듭니다. `common.helpers`에서 `select_latest_verified_vm_image_with_node_agent_sku` 함수는 [Azure Virtual Machines 마켓플레이스][vm_marketplace] 이미지를 사용하는 방법을 간소화합니다. 마켓플레이스 이미지를 사용하는 방법에 대한 자세한 내용은 [Azure Batch 풀에서 Linux 계산 노드 프로비전](batch-linux-nodes.md)을 참조하세요.

- **계산 노드 크기**(*vm\_size* - 필수)<p/>[VirtualMachineConfiguration][py_vm_config]에 Linux 노드를 지정하기 때문에 [Azure의 가상 컴퓨터에 대한 크기](../virtual-machines/virtual-machines-linux-sizes.md)에서 VM 크기(이 샘플의 `STANDARD_A1`)를 지정합니다. 다시 자세한 내용은 [Azure 배치 풀에서 Linux 계산 노드 프로비전](batch-linux-nodes.md)을 참조하세요.

- **시작 태스크**(*start\_task* - 필요하지 않음)<p/>위의 실제 노드 속성과 함께 풀에 [StartTask][py_starttask](필요하지 않음)을 지정할 수도 있습니다. StartTask는 해당 노드가 풀을 연결할 때 각 노드에서 실행하고 이 때마다 노드가 다시 시작됩니다. StartTask는 태스크가 실행하는 응용 프로그램을 설치하는 등 태스크를 실행하기 위해 계산 노드를 준비하는 데 특히 유용합니다.<p/>이 샘플 응용 프로그램에서 StartTask는 저장소(StartTask의 **resource\_files** 속성을 사용하여 지정됨)에서 다운로드하는 파일을 StartTask *작업 디렉터리*에서 노드에서 실행되는 모든 태스크가 액세스할 수 있는 *공유* 디렉터리에 복사합니다. 기본적으로 노드가 풀에 조인하면 각 노드의 공유 디렉터리에 `python_tutorial_task.py`를 복사하므로 노드에서 실행되는 모든 태스크가 공유 디렉터리에 액세스할 수 있습니다.

`wrap_commands_in_shell` 도우미 함수에 대한 호출을 볼 수 있습니다. 이 함수는 별도 명령의 컬렉션을 사용하고 작업의 명령줄 속성에 적절한 단일 명령줄을 만듭니다.

위의 코드 조각에서 주목할 만한 것은 StartTask의 **command\_line** 속성에서 두 개의 환경 변수(`AZ_BATCH_TASK_WORKING_DIR` 및 `AZ_BATCH_NODE_SHARED_DIR`) 사용입니다. 배치 풀 내의 각 계산 노드는 배치에 해당하는 몇 가지 환경 변수를 사용하여 자동으로 구성됩니다. 태스크에 의해 실행되는 모든 프로세스는 이러한 환경 변수에 대한 액세스를 갖습니다.

> [AZURE.TIP] 태스크 작업 디렉터리에 대한 정보뿐만 아니라 배치 풀의 계산 노드에 사용할 수 있는 환경 변수에 대한 자세한 내용은 [Azure 배치 기능 개요](batch-api-basics.md)에서 **태스크에 대한 환경 설정** 및 **파일 및 디렉터리** 섹션을 참조하세요.

## 4단계: 배치 작업 만들기

![배치 작업 만들기][4]<br/>

배치 **작업**은 태스크의 컬렉션이며 계산 노드의 풀과 관련됩니다. 작업의 태스크는 연결된 풀의 계산 노드에서 실행됩니다.

워크로드와 관련된 태스크를 구성하고 추적하는 것뿐만 아니라, 작업(더 나아가, 태스크)에 대한 최대 실행 시간은 물론 배치 계정 내 다른 작업 대비 작업 우선 순위와 같은 제약 조건을 부과하는 데도 작업을 사용합니다. 하지만 이 예제에서 작업은 3단계에서 만든 풀에만 연결됩니다. 추가적으로 구성되는 다른 속성은 없습니다.

모든 배치 작업은 특정 풀에 연결됩니다. 연결은 작업의 태스크가 실행되는 노드를 나타냅니다. 아래 코드 조각에 표시된 것처럼 [PoolInformation][py_poolinfo] 속성을 사용하여 풀을 지정할 수 있습니다.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

이제 작업이 만들어졌으므로 작업은 작업 수행에 추가됩니다.

## 5단계: 작업에 태스크 추가

![작업에 태스크 추가][5]<br/> *(1) 태스크가 작업에 추가됨, (2) 태스크가 노드에서 실행되도록 예약됨, (3) 태스크가 처리할 데이터 파일을 다운로드함*

배치 **태스크**는 계산 노드에서 실행되는 개별 작업 단위입니다. 작업에는 명령줄이 있으며 해당 명령줄에서 지정한 스크립트 또는 실행 파일을 실행합니다.

실제로 작업을 수행하려면 태스크가 작업에 추가되어야 합니다. 각 [CloudTask][py_task]는 명령줄 속성 및 명령줄이 자동으로 실행되기 전에 태스크가 노드에 다운로드하는 [ResourceFiles][py_resource_file](풀의 StartTask와 마찬가지로)을 사용하여 구성됩니다. 이 샘플에서 각 작업은 하나의 파일만을 처리합니다. 따라서 ResourceFiles 컬렉션은 단일 요소를 포함합니다.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] `$AZ_BATCH_NODE_SHARED_DIR` 같은 환경 변수에 액세스하거나 노드의 `PATH`에서 찾을 수 없는 응용 프로그램을 실행하는 경우, 태스크 명령줄은 `/bin/sh -c MyTaskApplication $MY_ENV_VAR`과 같은 쉘을 명시적으로 호출해야 합니다. 태스크가 노드의 `PATH`에서 응용 프로그램을 실행하는 경우 이 요구 사항이 필요하지 않으며 환경 변수를 참조하지 않습니다.

위의 코드 조각의 `for` 루프 내에서 태스크에 대한 명령줄이 *python\_tutorial\_task.py*에 전달되는 다섯 개의 명령줄 인수로 구성되어 있는 것을 확인할 수 있습니다.

1. **filepath**: 노드에 있는 것과 같은 파일에 대한 로컬 경로입니다. 위의 2단계에서 `upload_file_to_container`에 ResourceFile 개체를 만드는 경우 이 속성(ResourceFile 생성자에 대한 `file_path` 매개 변수)에 파일 이름이 사용됩니다. 따라서 *python\_tutorial\_task.py*와 동일한 노드의 디렉터리에서 파일을 찾을 수 있습니다.

2. **numwords**: 상위 *N* 단어를 출력 파일에 써야 한다는 것을 지정합니다.

3. **storageaccount**: 태스크 출력을 업로드해야 하는 컨테이너를 소유하는 저장소 계정 이름입니다.

4. **storagecontainer**: 출력 파일을 업로드해야 하는 저장소 컨테이너 이름입니다.

5. **sastoken**: Azure Storage의 **출력** 컨테이너에 쓰기 액세스를 제공하는 SAS(공유 액세스 서명)입니다. *python\_tutorial\_task.py* 스크립트가 BlockBlobService 참조를 만들 경우 이 공유 액세스 서명을 사용합니다. 저장소 계정에 대한 액세스 키를 요구하지 않고 컨테이너에 대한 쓰기 액세스를 제공합니다.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## 6단계: 작업 모니터링

![작업 모니터링][6]<br/> *스크립트는 (1) 완성 상태에 대한 태스크를 모니터링하고 (2) 태스크는 결과 데이터를 Azure Storage에 업로드합니다*

태스크가 작업에 추가되면 작업에 연결된 풀 내에서 계산 노드에서 실행되도록 자동으로 큐에 대기 및 예약됩니다. 지정한 설정에 따라 배치는 대기, 예약, 다시 시도하는 모든 작업 및 기타 담당 작업 관리 업무를 처리합니다.

태스크 실행을 모니터링하는 방법은 여러 가지가 있습니다. *python\_tutorial\_client.py*의 `wait_for_tasks_to_complete` 함수는 특정 상태, 이 경우에는 [완료][py_taskstate] 상태에 대한 태스크를 모니터링하는 간단한 예제를 제공합니다.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## 7단계: 작업 출력 다운로드

![저장소에서 작업 출력 다운로드][7]<br/>

이제 작업이 완료되었으므로 태스크의 출력을 Azure 저장소에서 다운로드할 수 있습니다. 이 작업은 *python\_tutorial\_client.py*에서 `download_blobs_from_container`에 대한 호출로 수행됩니다.

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] *python\_tutorial\_client.py*에서 `download_blobs_from_container`에 대한 호출은 홈 디렉터리에 다운로드해야 하는 파일을 지정합니다. 이 출력 위치를 수정해도 됩니다.

## 8단계: 컨테이너 삭제

Azure 저장소에 있는 데이터에 대한 요금이 부과되므로 배치 작업에 더 이상 필요 없는 모든 Blob을 제거하는 것이 좋습니다. *python\_tutorial\_client.py*에서 [BlockBlobService.delete\_container][py_delete_container]를 세 번 호출하여 수행합니다.

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## 9단계: 작업 및 풀 삭제

마지막 단계로, *python\_tutorial\_client.py* 스크립트에서 만든 작업 및 풀을 삭제하라는 메시지가 표시됩니다. 작업 및 태스크 자체에 대한 요금이 부과되지 않지만 계산 노드에 대한 요금이 청구*됩니다*. 따라서 노드를 필요할 때만 할당하는 것이 좋습니다. 사용하지 않는 풀을 삭제하는 것이 유지 관리 프로세스의 일부가 될 수 있습니다.

BatchServiceClient의 [JobOperations][py_job] 및 [PoolOperations][py_pool]에는 해당하는 삭제 메서드가 있고 이는 삭제를 확인하는 경우 호출됩니다.

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] 계산 리소스에 대해 요금이 부과되고 사용하지 않는 풀 삭제는 비용을 최소화한다는 점을 유의하세요. 풀 삭제는 해당 풀 내의 모든 계산 노드를 삭제하고 노드의 모든 데이터는 풀이 삭제되면 복구할 수 없게 됩니다.

## 샘플 스크립트 실행

자습서 [코드 샘플][github_article_samples]에서 *python\_tutorial\_client.py* 스크립트를 실행하는 경우 콘솔 출력은 다음과 유사합니다. 풀의 계산 노드를 만들고 시작하고 풀의 시작 태스크에서 명령을 실행하는 동안 `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...`에서 일시 중지가 발생합니다. [Azure 포털][azure_portal]을 사용하여 실행 중 및 실행 후에 풀, 계산 노드, 작업 및 태스크를 모니터링합니다. [Azure 포털][azure_portal] 또는 [Microsoft Azure Storage 탐색기][storage_explorer]를 사용하여 응용 프로그램에서 만든 저장소 리소스(컨테이너 및 Blob)를 봅니다.

>[AZURE.TIP] `azure-batch-samples/Python/Batch/article_samples` 디렉터리 내에서 *python\_tutorial\_client.py* 스크립트를 실행합니다. `common.helpers` 모듈 가져오기에 대한 상대 경로를 사용하므로 이 디렉터리 내에서 스크립트를 실행하지 않는 경우 `ImportError: No module named 'common'`가 표시될 수 있습니다.

기본 구성에서 샘플을 실행하는 경우 일반적인 실행 시간은 **약 5-7분**입니다.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## 다음 단계

*python\_tutorial\_client.py* 및 *python\_tutorial\_task.py*를 자유롭게 변경하여 다른 계산 시나리오를 실험합니다. 예를 들어, *python\_tutorial\_task.py*에 실행 지연을 추가하여 장기 실행 태스크를 시뮬레이션하고 포털에서 모니터링합니다. 더 많은 태스크를 추가하거나 계산 노드 수를 조정합니다. 논리를 추가하여 실행 시간을 줄이기 위해 실행 기존 풀의 사용을 확인하고 허용합니다.

이제 배치 솔루션의 기본 워크플로에 익숙하다면 배치 서비스의 추가 기능을 살펴볼 시간입니다.

- 이 서비스를 처음 사용하는 경우 [Azure 배치 기능 개요](batch-api-basics.md) 문서를 검토하는 것이 좋습니다.
- [배치 학습 경로][batch_learning_path]의 **개발 세부 정보** 아래에서 다른 배치 개발 문서를 시작하세요.
- [TopNWords][github_topnwords] 샘플의 배치를 사용하여 "상위 N개 단어" 워크로드 처리의 다른 구현을 확인하세요.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Azure 저장소에 컨테이너 만들기"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "컨테이너에 작업 응용 프로그램 및 입력(데이터) 파일 업로드"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "배치 풀 만들기"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "배치 작업 만들기"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "작업에 태스크 추가"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "작업 모니터링"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "저장소에서 작업 출력 다운로드"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "배치 솔루션 워크플로(전체 다이어그램)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "포털의 배치 자격 증명"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "포털의 저장소 자격 증명"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "배치 솔루션 워크플로(최소 다이어그램)"

<!---HONumber=AcomDC_0928_2016-->