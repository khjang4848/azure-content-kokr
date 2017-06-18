<properties
	pageTitle="Azure 배치 풀의 Linux 노드 | Microsoft Azure"
	description="Azure 배치의 Linux 가상 컴퓨터 풀에서 병렬 계산 워크로드를 처리하는 방법에 대해 알아봅니다."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="09/08/2016"
	ms.author="marsma" />

# Azure 배치 풀에서 Linux 계산 노드 프로비전

Azure 배치를 사용하여 Linux 및 Windows 가상 컴퓨터에서 병렬 계산 워크로드를 실행할 수 있습니다. 이 문서에서는 [배치 Python][py_batch_package] 및 [배치 .NET][api_net] 클라이언트 라이브러리를 모두 사용하여 배치 서비스에서 Linux 계산 노드 풀을 만드는 방법에 대해 자세히 설명합니다.

> [AZURE.NOTE] [Application packages](Batch-application-packages.md)는 현재 Linux 계산 노드에서 지원되지 않습니다.

## 가상 컴퓨터 구성

배치에서 계산 노드 풀을 만드는 경우 노드 크기와 운영 체제를 선택할 수 있는 두 가지 옵션인 클라우드 서비스 구성 및 가상 컴퓨터 구성이 있습니다.

**클라우드 서비스 구성**은 Windows 계산 노드*만* 제공합니다. 사용 가능한 계산 노드 크기는 [클라우드 서비스 크기](../cloud-services/cloud-services-sizes-specs.md)에 나열되고 사용 가능한 운영 체제는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](../cloud-services/cloud-services-guestos-update-matrix.md)에 나열됩니다. Azure 클라우드 서비스 노드를 포함하는 풀을 만드는 경우 노드 크기 및 해당 "OS 제품군"만 지정해야 하며 이전에 언급한 문서에서 찾을 수 있습니다. Windows 계산 노드의 풀에는 클라우드 서비스가 가장 일반적으로 사용됩니다.

**가상 컴퓨터 구성**은 계산 노드에 대한 Linux와 Windows 이미지를 제공합니다. 사용 가능한 계산 노드 크기는 [Azure에서 가상 컴퓨터에 대한 크기](../virtual-machines/virtual-machines-linux-sizes.md)(Linux) 및 [Azure에서 가상 컴퓨터에 대한 크기](../virtual-machines/virtual-machines-windows-sizes.md)(Windows)에 나열되어 있습니다. 가상 컴퓨터 구성 노드를 포함하는 풀을 만들 때 노드 크기, 가상 컴퓨터 이미지 참조 및 노드에 설치할 배치 노드 에이전트 SKU도 지정해야 합니다.

### 가상 컴퓨터 이미지 참조

배치 서비스는 [가상 컴퓨터 크기 조정 설정](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하여 Linux 계산 노드를 제공합니다. [Azure 마켓플레이스][vm_marketplace]에서 이러한 가상 컴퓨터에 대한 운영 체제 이미지를 제공합니다. 가상 컴퓨터 이미지 참조를 구성할 때 마켓플레이스 가상 컴퓨터 이미지의 속성을 지정합니다. 가상 컴퓨터 이미지 참조를 만들 때 다음 속성이 필요합니다.

| **이미지 참조 속성** | **예제** |
| ----------------- | ------------------------ |
| 게시자 | Canonical |
| 제안 | UbuntuServer |
| SKU | 14\.04.4-LTS |
| 버전 | 최신 |

> [AZURE.TIP] 이러한 속성 및 마켓플레이스 이미지를 나열하는 방법에 대한 자세한 내용은 [CLI 또는 PowerShell로 Azure의 Linux 가상 컴퓨터 이미지 이동 및 선택](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md)에서 알아볼 수 있습니다. 일부 마켓플레이스 이미지가 현재 배치와 호환되지 않습니다. 자세한 내용은 [노드 에이전트 SKU](#node-agent-sku)를 참조하세요..

### 노드 에이전트 SKU

배치 노드 에이전트는 풀의 각 노드에서 실행되고 노드와 배치 서비스 간의 명령 및 컨트롤 인터페이스를 제공하는 프로그램입니다. SKU라고 하는 노드 에이전트의 구현은 서로 다른 운영 체제에 대해 여러 가지가 있습니다. 기본적으로 가상 컴퓨터 구성을 만들 때 먼저 가상 컴퓨터 이미지 참조를 지정한 다음 이미지에 설치할 노드 에이전트를 지정합니다. 일반적으로 각 노드 에이전트 SKU는 여러 가상 컴퓨터 이미지와 호환됩니다. 다음은 노드 에이전트 SKU의 몇 가지 예입니다.

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] 마켓플레이스에서 사용 가능한 일부 가상 컴퓨터 이미지가 현재 사용 가능한 배치 노드 에이전트와 호환되지 않습니다. 사용 가능한 노드 에이전트 SKU 및 호환되는 가상 컴퓨터 이미지를 나열하려면 배치 SDK를 사용해야 합니다. 자세한 내용은 이 문서의 뒷부분에 있는 [가상 컴퓨터 이미지 목록](#list-of-virtual-machine-images)을 참조하세요.

## Linux 풀 만들기: 배치 Python

다음 코드 조각은 [Python용 Microsoft Azure 배치 클라이언트 라이브러리][py_batch_package]를 사용하여 Ubuntu Server 계산 노드의 풀을 만드는 방법의 예를 보여 줍니다. 배치 Python 모듈에 대한 참조 설명서는 문서 읽기의 [azure.batch package ][py_batch_docs] 에서 찾을 수 있습니다.

이 코드 조각은 명시적으로 [ImageReference][py_imagereference]를 만들고 각 속성(게시자, 제품, SKU, 버전)을 지정합니다. 그러나 프로덕션 코드에서는 [list\_node\_agent\_skus][py_list_skus]를 사용하여 런타임 시 사용 가능한 이미지 및 노드 에이전트 SKU 조합을 결정하고 선택하는 것이 좋습니다.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

이전에 언급한 대로 [ImageReference][py_imagereference]를 명시적으로 만드는 대신 현재 지원되는 노드 에이전트/마켓플레이스 이미지 조합에서 동적으로 선택하는 [list\_node\_agent\_skus][py_list_skus] 메서드를 사용하는 것이 좋습니다. 다음 Python 코드 조각에서는 이 메서드의 사용을 보여 줍니다.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Linux 풀 만들기: 배치 .NET

다음 코드 조각은 [배치 .NET][nuget_batch_net] 클라이언트 라이브러리를 사용하여 Ubuntu Server 계산 노드의 풀을 만드는 방법의 예를 보여 줍니다. MSDN에서 [배치 .NET 참조 설명서][api_net]를 찾을 수 있습니다.

다음 코드 조각에서는 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 메서드를 사용하여 현재 지원되는 마켓플레이스 이미지 및 노드 에이전트 SKU 조합의 목록에서 선택합니다. 지원되는 조합 목록이 언제든지 바뀔 수 있으므로 이 기술이 바람직합니다. 가장 일반적으로 지원되는 조합을 추가합니다.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

이전의 코드 조각은 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 메서드를 사용하여 동적으로 나열하고 지원되는 이미지와 노드 에이전트 SKU 조합에서 선택(권장)하지만, [ImageReference][net_imagereference]를 명시적으로 구성할 수도 있습니다.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## 가상 컴퓨터 이미지 목록

다음 표에는 이 문서가 마지막으로 업데이트되었을 때 사용 가능한 배치 노드 에이전트와 호환되는 마켓플레이스 가상 컴퓨터 이미지가 나열되어 있습니다. 이미지와 노드 에이전트는 언제든지 추가 또는 제거될 수 있기 때문에 이 목록은 확정적이지 않습니다. 배치 응용 프로그램 및 서비스에서는 현재 사용 가능한 SKU를 확인하고 선택하는[list\_node\_agent\_skus][py_list_skus](Python) 및 [ListNodeAgentSkus][net_list_skus](배치 .NET)를 항상 사용하는 것이 좋습니다.

> [AZURE.WARNING] 다음 목록은 언제든지 변경될 수 있습니다. 항상 배치 API에서 사용 가능한 **list node agent SKU** 메서드를 사용하여 나열한 다음 배치 작업 실행 시 호환되는 가상 컴퓨터 및 노드 에이전트 SKU에서 선택합니다.

| **게시자** | **제안** | **이미지 SKU** | **버전** | **노드 에이전트 SKU ID** |
| ------- | ------- | ------- | ------- | ------- |
| Canonical | UbuntuServer | 14\.04.0-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.1-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.2-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.3-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.4-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 14\.04.5-LTS | 최신 | batch.node.ubuntu 14.04 |
| Canonical | UbuntuServer | 16\.04.0-LTS | 최신 | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | 최신 | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7\.1 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.2 | 최신 | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7\.0 | 최신 | batch.node.centos 7 |
| SUSE | openSUSE | 13\.2 | 최신 | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | 최신 | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | 최신 | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | 최신 | batch.node.opensuse 42.1 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | 최신 | batch.node.windows amd64 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | 최신 | batch.node.centos 7 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | 최신 | batch.node.windows amd64 |

## Linux 노드에 연결

개발 또는 문제 해결 동안 풀의 노드에 로그인할 필요가 있을 수 있습니다. Windows 계산 노드와 달리 Linux 노드에 연결하기 위해 RDP(원격 데스크톱 프로토콜)를 사용할 수 없습니다. 대신, 배치 서비스는 원격 연결을 위해 각 노드에서 SSH 액세스를 사용하도록 설정합니다.

다음 Python 코드 조각에서는 풀의 각 노드에서 사용자를 만들며 이는 원격 연결에 필요합니다. 그런 다음 각 노드에 대한 SSH(secure shell) 연결 정보를 인쇄합니다.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

다음은 4개의 Linux 노드를 포함하는 풀에 대한 이전 코드의 샘플 출력입니다.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

노드에 사용자를 만들 때 암호 대신 SSH 공개 키를 지정할 수 있습니다. Python SDK에서 [ComputeNodeUser][py_computenodeuser]에 **ssh\_public\_key** 매개 변수를 사용하여 수행할 수 있습니다. .NET에서 [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key] 속성을 사용하여 수행할 수 있습니다.

## 가격

Azure 배치는 Azure 클라우드 서비스 및 Azure 가상 컴퓨터 기술을 기반으로 빌드됩니다. 배치 서비스 자체는 무료로 제공됩니다. 즉, 배치 솔루션에서 사용하는 계산 리소스에 대해서만 요금이 청구됩니다. **Cloud Services 구성**을 선택하는 경우 [Cloud Services 가격 책정][cloud_services_pricing] 구조에 따라 요금이 청구됩니다. **가상 컴퓨터 구성**을 선택하는 경우 [가상 컴퓨터 가격][vm_pricing] 구조에 따라 요금이 청구됩니다.

## 다음 단계

### 배치 Python 자습서

Python을 사용하여 배치로 작업하는 방법에 대한 자세한 자습서를 보려면 [Azure 배치 Python 클라이언트 시작](batch-python-tutorial.md)을 확인합니다. 함께 제공되는 [코드 샘플][github_samples_pyclient]에는 가상 컴퓨터 구성을 가져오기 위한 다른 기법을 보여 주는 도우미 함수(`get_vm_config_for_distro`)가 포함되어 있습니다.

### 배치 Python 코드 샘플

풀, 작업 및 작업 만들기와 같은 일반적인 배치 작업을 수행하는 방법을 보여 주는 몇 가지 스크립트는 GitHub의 [azure-batch-samples][github_samples] 리포지토리에서 기타 [Python 코드 샘플][github_samples_py]을 확인하세요. Python 샘플과 함께 제공되는 [추가 정보][github_py_readme]에는 필요한 패키지를 설치하는 방법에 대한 세부 정보가 있습니다.

### 배치 포럼

MSDN의 [Azure 배치 포럼][forum]은 배치를 설명하고 서비스에 대한 질문을 하는 데 많은 도움이 됩니다. 유용한 "스티커" 게시물을 참조하고 배치 솔루션을 빌드하는 동안 질문이 생기면 게시합니다.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/ko-KR/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

<!---HONumber=AcomDC_0914_2016-->