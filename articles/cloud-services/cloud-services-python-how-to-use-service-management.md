<properties
	pageTitle="서비스 관리 API를 사용하는 방법(Python) - 기능 가이드"
	description="Python에서 프로그래밍 방식으로 일반 서비스 관리 작업을 수행하는 방법에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter="python"
	authors="lmazuel"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="lmazuel"/>

# Python에서 서비스 관리를 사용하는 방법

> [AZURE.NOTE] 서비스 관리 API는 현재 미리 보기 릴리스에서 사용할 수 있는 새로운 리소스 관리 API로 대체되고 있습니다. Python에서 새로운 리소스 관리 API를 사용하는 방법에 대한 자세한 내용은 [Azure 리소스 관리 설명서](http://azure-sdk-for-python.readthedocs.org/)를 참조하세요.

이 가이드에서는 Python에서 프로그래밍 방식으로 일반 서비스 관리 작업을 수행하는 방법을 보여 줍니다. **Python용 Azure SDK**의 [ServiceManagementService](https://github.com/Azure/azure-sdk-for-python) 클래스는 [Azure 클래식 포털][management-portal]에서 사용할 수 있는 대부분의 서비스 관리 관련 기능에 대해 프로그래밍 방식의 액세스를 지원합니다(예: **클라우드 서비스, 배포, 데이터 관리 서비스, 가상 컴퓨터 만들기, 업데이트 및 삭제**). 이 기능은 서비스 관리에 프로그래밍 방식으로 액세스해야 하는 응용 프로그램을 빌드하는 데 유용할 수 있습니다.

## <a name="WhatIs"> </a>서비스 관리 정의
서비스 관리 API는 [Azure 클래식 포털][management-portal]을 통해 사용할 수 있는 대부분의 서비스 관리 기능에 대해 프로그래밍 방식의 액세스를 제공합니다. Python용 Azure SDK를 사용하여 클라우드 서비스 및 저장소 계정을 관리할 수 있습니다.

서비스 관리 API를 사용하려면 [Azure 계정을 만들어야](https://azure.microsoft.com/pricing/free-trial/) 합니다.

## <a name="Concepts"> </a>개념
Python용 Azure SDK는 REST API인 [Azure 서비스 관리 API][svc-mgmt-rest-api]를 래핑합니다. 모든 API 작업은 SSL을 통해 수행되고 X.509 v3 인증서를 사용하여 서로 인증됩니다. 관리 서비스는 Azure에서 실행 중인 서비스 내에서 액세스할 수 있거나, HTTPS 요청을 보내고 HTTPS 응답을 받을 수 있는 응용 프로그램에서 인터넷을 통해 직접 액세스할 수 있습니다.

## <a name="Installation"> </a>설치

이 문서에서 설명한 모든 기능은 `azure-servicemanagement-legacy` 패키지에서 사용할 수 있으며, 이 패키지는 pip을 사용하여 설치할 수 있습니다. (예를 들어 Python을 처음 사용한다면) 설치에 관한 자세한 내용은 이 문서 [Python 설치 및 Azure SDK](../python-how-to-install.md)를 참조합니다.

## <a name="Connect"> </a>방법: 서비스 관리에 연결
서비스 관리 끝점에 연결하려면 Azure 구독 ID 및 유효한 관리 인증서가 있어야 합니다. [Azure 클래식 포털][management-portal]을 통해 구독 ID를 얻을 수 있습니다.

> [AZURE.NOTE] Python용 Azure SDK v0.8.0부터 이제 Windows에서 실행할 때 OpenSSL로 만든 인증서를 사용할 수 있습니다. Python 2.7.4 이상이 필요합니다. .pfx 인증서 지원은 나중에 제거될 가능성이 크기 때문에 사용자는 .pfx 대신 OpenSSL을 사용하는 것이 좋습니다.

### Windows/Mac/Linux의 관리 인증서(OpenSSL)
[OpenSSL](http://www.openssl.org/)을 사용하여 관리 인증서를 만들 수 있습니다. 실제로 서버용(`.cer` 파일)과 클라이언트용(`.pem` 파일)으로 두 개의 인증서를 만들어야 합니다. `.pem` 파일을 만들려면 다음을 실행합니다.

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

`.cer` 인증서를 만들려면 다음을 실행합니다.

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Azure 인증서에 대한 자세한 내용은 [Azure 클라우드 서비스 인증서 개요](./cloud-services-certs-create.md)를 참조하세요. OpenSSL 매개 변수에 대한 자세한 설명은 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html)(영문)의 자료를 참조하세요.

이러한 파일을 만든 후에는 [Azure 클래식 포털][management-portal]에서 "설정" 탭의 "업로드" 동작을 통해 `.cer` 파일을 Azure에 업로드해야 하고, `.pem` 파일을 저장한 위치를 적어 두어야 합니다.

구독 ID를 얻어 인증서를 만들고 `.cer` 파일을 Azure에 업로드하고 나면 구독 ID 및 `.pem` 파일의 경로를 **ServiceManagementService**에 전달하여 Azure 관리 끝점에 연결할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

앞의 예제에서 `sms`은 **ServiceManagementService** 개체입니다. **ServiceManagementService** 클래스는 Azure 서비스를 관리하는 데 사용되는 주 클래스입니다.

### Windows의 관리 인증서(MakeCert)

`makecert.exe`를 사용하여 컴퓨터에 자체 서명된 관리 인증서를 만들 수 있습니다. **Visual Studio 명령 프롬프트**를 **관리자**로 열고 *AzureCertificate*를 사용할 인증서 이름으로 바꿔서 다음 명령을 사용합니다.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

이 명령은 `.cer` 파일을 만들고 만든 파일을 **개인** 인증서 저장소에 설치합니다. 자세한 내용은 [Azure 클라우드 서비스 인증서 개요](./cloud-services-certs-create.md)를 참조하세요.

인증서를 만든 후에는 [Azure 클래식 포털][management-portal]에서 "설정" 탭의 "업로드" 동작을 통해 `.cer` 파일을 Azure에 업로드해야 합니다.

구독 ID를 얻어 인증서를 만들고 `.cer` 파일을 Azure에 업로드하고 나면 구독 ID 및 **개인** 인증서 저장소에 있는 인증서의 위치를 **ServiceManagementService**에 전달하여(다시 말해, *AzureCertificate*를 인증서 이름으로 바꿈) Azure 관리 끝점에 연결할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

앞의 예제에서 `sms`은 **ServiceManagementService** 개체입니다. **ServiceManagementService** 클래스는 Azure 서비스를 관리하는 데 사용되는 주 클래스입니다.

## <a name="ListAvailableLocations"> </a>방법: 사용 가능한 위치 나열

서비스를 호스트하는 데 사용할 수 있는 위치를 나열하려면 **list\_locations** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

클라우드 서비스 또는 저장소 서비스를 만드는 경우 유효한 위치를 제공해야 합니다. **list\_locations** 메서드는 항상 현재 사용 가능한 위치의 최신 목록을 반환합니다. 현재 사용 가능한 위치는 다음과 같습니다.

- 서유럽
- 북유럽
- 동남아시아
- 동아시아
- 미국 중부
- 미국 중북부
- 미국 중남부
- 미국 서부
- 미국 동부
- 일본 동부
- 일본 서부
- 브라질 남부
- 오스트레일리아 동부
- 오스트레일리아 남동부

## <a name="CreateCloudService"> </a>방법: 클라우드 서비스 만들기

응용 프로그램을 만들어 Azure에서 실행하면 코드와 구성은 모두 Azure [클라우드 서비스](이전 Azure 릴리스에서는 *호스팅 서비스*라 함)라고 합니다. **create\_hosted\_service** 메서드를 통해 호스팅 서비스 이름(Azure에서 고유해야 함), 레이블(base64로 자동 인코딩됨), 설명 및 위치를 제공하여 새 호스팅 서비스를 만들 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

**list\_hosted\_services** 메서드로 구독의 모든 호스팅 서비스를 나열할 수 있습니다.

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

특정 호스팅 서비스에 대한 정보를 가져오려면 호스팅 서비스 이름을 **get\_hosted\_service\_properties** 메서드에 전달하여 가져올 수 있습니다.

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

클라우드 서비스를 만든 후에는 **create\_deployment** 메서드로 코드를 서비스에 배포할 수 있습니다.

## <a name="DeleteCloudService"> </a>방법: 클라우드 서비스 삭제

서비스 이름을 **delete\_hosted\_service** 메서드에 전달하여 클라우드 서비스를 삭제할 수 있습니다.

	sms.delete_hosted_service('myhostedservice')

서비스의 모든 배포를 먼저 삭제해야 서비스를 삭제할 수 있습니다. 자세한 내용은 [방법: 배포 삭제](#DeleteDeployment)를 참조하세요.

## <a name="DeleteDeployment"> </a>방법: 배포 삭제

배포를 삭제하려면 **delete\_deployment** 메서드를 사용합니다. 다음 예제에서는 이름이 `v1`인 배포를 삭제하는 방법을 보여 줍니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>방법: 저장소 서비스 만들기

[저장소 서비스](../storage/storage-create-storage-account.md)로 Azure [Blob](../storage/storage-python-how-to-use-blob-storage.md), [테이블](../storage/storage-python-how-to-use-table-storage.md), [큐](../storage/storage-python-how-to-use-queue-storage.md)에 액세스할 수 있습니다. 저장소 서비스를 만들려면 서비스(3자에서 24자 사이의 소문자로서 Azure 내에서 고유해야 함), 설명, 레이블(base64로 자동 인코딩되며 최대 100자까지 가능) 및 위치에 대해 이름이 있어야 합니다. 다음 예제에서는 위치를 지정하여 저장소 서비스를 만드는 방법을 보여 줍니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

앞의 예제에서 **create\_storage\_account** 작업 상태는 **create\_storage\_account**가 반환한 결과를 **get\_operation\_status** 메서드에 전달하여 검색할 수 있습니다.

**list\_storage\_accounts** 메서드로 저장소 계정 및 그 속성을 나열할 수 있습니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>방법: 저장소 서비스 삭제

저장소 서비스 이름을 **delete\_storage\_account** 메서드에 전달하여 저장소 서비스를 삭제할 수 있습니다. 저장소 서비스를 삭제하면 그 서비스에 저장되어 있는 모든 데이터(Blob, 테이블, 큐)가 삭제됩니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>방법: 사용 가능한 운영 체제 나열

서비스를 호스팅하는 데 사용할 수 있는 운영 체제를 나열하려면 **list\_operating\_systems** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

또는 운영 체제를 제품군별로 그룹화하는 **list\_operating\_system\_families** 메서드를 사용할 수 있습니다.

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>방법: 운영 체제 이미지 만들기

운영 체제 이미지를 이미지 리포지토리에 추가하려면 **add\_os\_image** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

사용 가능한 운영 체제 이미지를 나열하려면 **list\_os\_images** 메서드를 사용합니다. 여기에는 모든 플랫폼 이미지 및 사용자 이미지가 포함됩니다.

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>방법: 운영 체제 이미지 삭제

사용자 이미지를 삭제하려면 **delete\_os\_image** 메서드를 사용합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>방법: 가상 컴퓨터 만들기

가상 컴퓨터를 만들려면 먼저 [클라우드 서비스](#CreateCloudService)를 만들어야 합니다. 그런 다음 **create\_virtual\_machine\_deployment** 메서드를 사용하여 가상 컴퓨터 배포를 만듭니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-ko-KR-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>방법: 가상 컴퓨터 삭제

가상 컴퓨터를 삭제하려면 **delete\_deployment** 메서드를 사용하여 먼저 배포를 삭제합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

그런 다음 **delete\_hosted\_service** 메서드를 사용하여 클라우드 서비스를 삭제할 수 있습니다.

	sms.delete_hosted_service(service_name='myvm')

##방법: 캡처된 가상 컴퓨터 이미지에서 가상 컴퓨터 만들기

VM 이미지를 캡처하기 위해 먼저 **capture\_vm\_image** 메서드를 호출합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

그런 다음 이미지가 캡처되었는지 확인하기 위해 **list\_vm\_images** API를 사용하여 이미지가 결과에 표시되는지 확인합니다.

	images = sms.list_vm_images()

최종적으로 캡처된 이미지를 사용하여 가상 컴퓨터를 만들기 위해 이전처럼 **create\_virtual\_machine\_deployment** 메서드를 사용하지만, 이번에는 대신 vm\_image\_name을 전달합니다.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Linux 가상 컴퓨터를 캡처하는 방법에 대한 자세한 내용은 [Linux 가상 컴퓨터를 캡처하는 방법](../virtual-machines/virtual-machines-linux-classic-capture-image.md)을 참조하세요.

Windows 가상 컴퓨터를 캡처하는 방법에 대한 자세한 내용은 [Windows 가상 컴퓨터를 캡처하는 방법](../virtual-machines/virtual-machines-windows-classic-capture-image.md)을 참조하세요.

## <a name="What's Next"> </a>다음 단계

서비스 관리의 기본 사항을 배웠으므로 이제 [Azure Python SDK에 대한 전체 API 참조 설명서](http://azure-sdk-for-python.readthedocs.org/)에 액세스하고 쉽게 복잡한 작업을 수행하여 Python 응용 프로그램을 관리할 수 있습니다.

자세한 내용은 [Python 개발자 센터](/develop/python/)를 참조하세요.

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[클라우드 서비스]: https://azure.microsoft.com/documentation/services/cloud-services/

<!---HONumber=AcomDC_0914_2016-->