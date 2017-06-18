<properties
	pageTitle="클라우드 서비스를 만들고 배포하는 방법 | Microsoft Azure"
	description="Azure에서 빨리 만들기 방법을 사용하여 클라우드 서비스를 만들고 배포하는 방법에 대해 알아봅니다."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="adegeo"/>




# 클라우드 서비스를 만들고 배포하는 방법

> [AZURE.SELECTOR]
- [Azure 포털](cloud-services-how-to-create-deploy-portal.md)
- [Azure 클래식 포털](cloud-services-how-to-create-deploy.md)

Azure 클래식 포털은 클라우드 서비스를 만들고 배포하는 두 가지 방법으로 **빨리 만들기** 및 **사용자 지정 만들기**를 제공합니다.

이 토픽에서는 빠른 생성 방법을 사용하여 새 클라우드 서비스를 만든 다음 **업로드**를 사용하여 Azure에서 클라우드 서비스 패키지를 업로드하고 배포하는 방법에 대해 설명합니다. 이 방법을 사용하는 경우 작업을 진행하면서 모든 요구 사항을 완료하는 데 사용할 수 있는 편리한 링크를 Azure 클래식 포털에서 제공합니다. 클라우드 서비스를 만들 때 배포할 준비가 되면 **사용자 지정 만들기**를 사용하여 동시에 둘 다를 수행할 수 있습니다.

> [AZURE.NOTE] VSTS(Visual Studio Team Services)에서 클라우드 서비스를 게시하려는 경우 빠른 생성을 사용한 다음 **빠른 시작** 또는 대시보드에서 VSTS 게시를 설정합니다. 자세한 내용은 [Visual Studio Team Services를 사용하여 Azure에 지속적인 전송][TFSTutorialForCloudService]을 참조하거나 **빠른 시작** 페이지에 대한 도움말을 참조하세요.

## 개념
Azure에서 응용 프로그램을 클라우드 서비스로 배포하려면 다음과 같은 세 가지 구성 요소가 필요합니다.

- **서비스 정의** 클라우드 서비스 정의 파일(.csdef)은 역할 수를 포함하여 서비스 모델을 정의합니다.

- **서비스 구성** 클라우드 서비스 구성 파일(.cscfg)은 역할 인스턴스 수를 포함하여 클라우드 서비스 및 개별 역할에 대한 구성 설정을 제공합니다.

- **서비스 패키지** 서비스 패키지(.cspkg)에는 응용 프로그램 코드와 구성 및 서비스 정의 파일이 포함됩니다.
  
이러한 구성 요소에 대한 자세한 내용과 패키지를 만드는 방법은 [여기](cloud-services-model-and-package.md)에서 볼 수 있습니다.

## 앱 준비
클라우드 서비스를 배포하려면 먼저 응용 프로그램 코드 및 클라우드 서비스 구성 파일(.cscfg)에서 클라우드 서비스 패키지(.cspkg)를 만들어야 합니다. Azure SDK는 필요한 배포 파일을 준비하는 도구를 제공합니다. [Azure 다운로드](https://azure.microsoft.com/downloads/) 페이지에서 응용 프로그램 코드를 개발하려는 언어로 SDK를 설치할 수 있습니다.

세 가지 클라우드 서비스 기능은 서비스 패키지를 내보내기 전에 특별히 구성해야 합니다.

- 데이터 암호화에 SSL(Secure Sockets Layer)을 사용하는 클라우드 서비스를 배포하려는 경우 SSL에 맞게 [응용 프로그램을 구성](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files)합니다.

- 역할 인스턴스에 대한 원격 데스크톱 연결을 구성하려면 원격 데스크톱에 대한 [역할을 구성](cloud-services-role-enable-remote-desktop.md)합니다.

- 클라우드 서비스에 대해 자세한 모니터링을 구성하려면 클라우드 서비스에 Azure 진단을 사용하도록 설정합니다. *최소 모니터링*(기본 모니터링 수준)에서는 역할 인스턴스(가상 컴퓨터)에 대해 호스트 운영 체제에서 수집된 성능 카운터를 사용합니다. "자세한 모니터링*에서는 역할 인스턴스 내 성능 데이터를 기반으로 추가 메트릭을 수집하여 응용 프로그램 처리 중 발생하는 문제를 보다 자세히 분석할 수 있습니다. Azure 진단을 사용하도록 설정하는 방법에 대해 알아보려면 [Azure에서 진단 사용](cloud-services-dotnet-diagnostics.md)(영문)을 참조하세요.

웹 역할 또는 작업자 역할 배포를 통해 클라우드 서비스를 만들려면 [서비스 패키지를 만들어야](cloud-services-model-and-package.md#servicepackagecspkg) 합니다.

## 시작하기 전에

- Azure SDK를 설치하지 않은 경우 **Azure SDK 설치**를 클릭하여 [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)를 열고 코드를 개발하려는 언어의 SDK를 다운로드합니다. 이 작업은 나중에 수행할 수 있습니다.

- 역할 인스턴스에 인증서가 필요한 경우 인증서를 만듭니다. 클라우드 서비스에는 개인 키가 포함된 .pfx 파일이 필요합니다. 클라우드 서비스를 만들고 배포할 때 [Azure에 인증서를 업로드](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate)할 수 있습니다.

- 클라우드 서비스를 선호도 그룹에 배포하려면 선호도 그룹을 만듭니다. 선호도 그룹을 사용하면 클라우드 서비스 및 다른 Azure 서비스를 지역의 동일한 위치에 배포할 수 있습니다. 선호도 그룹은 Azure 클래식 포털의 **네트워크** 영역에 있는 **선호도 그룹** 페이지에서 만들 수 있습니다.


## 방법: 빠른 생성을 사용하여 클라우드 서비스 만들기

1. [Azure 클래식 포털](http://manage.windowsazure.com/)에서 **새로 만들기** > **계산** > **클라우드 서비스** > **빨리 만들기**를 차례로 클릭합니다.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. **URL**에서 프로덕션 배포의 클라우드 서비스에 액세스하기 위한 공용 URL에 사용할 하위 도메인을 입력합니다. 프로덕션 배포의 URL 형식은 http://*myURL*.cloudapp.net입니다.

3. **지역 또는 선호도 그룹**에서 클라우드 서비스를 배포할 지역 또는 선호도 그룹을 선택합니다. 클라우드 서비스를 지역 내의 다른 Azure 서비스와 동일한 위치에 배포하려면 선호도 그룹을 선택합니다.

4. **클라우드 서비스 만들기**를 클릭합니다.

	![CloudServices\_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	창 아래쪽에 있는 메시지 영역에서 프로세스의 상태를 모니터링할 수 있습니다.

	**클라우드 서비스** 영역이 열리고 새 클라우드 서비스가 표시됩니다. 상태가 만들어짐으로 바뀌면 클라우드 서비스 만들기가 완료된 것입니다.

	![CloudServices\_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## 방법: 클라우드 서비스에 대한 인증서 업로드

1. [Azure 클래식 포털](http://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하고 **인증서**를 클릭합니다.

	![CloudServices\_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. **인증서 업로드** 또는 **업로드**를 클릭합니다.

3. **파일**에서 **찾아보기**를 사용하여 인증서(.pfx 파일)를 선택합니다.

4. **암호**에 인증서의 개인 키를 입력합니다.

5. **확인**(확인 표시)을 클릭합니다.

	![CloudServices\_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	아래와 같이 메시지 영역에서 업로드의 진행률을 확인할 수 있습니다. 업로드가 완료되면 인증서가 테이블에 추가됩니다. 메시지 영역에서 확인을 클릭하여 메시지를 닫습니다.

	![CloudServices\_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## 방법: 클라우드 서비스 배포

1. [Azure 클래식 포털](http://manage.windowsazure.com/)에서 **클라우드 서비스**를 클릭한 다음 클라우드 서비스의 이름을 클릭하고 **대시보드**를 클릭합니다.

2. **새 프로덕션 배포 업로드** 또는 **업로드**를 클릭합니다.

3. **배포 레이블**에 새 배포의 이름(예: MyCloudServicev4)을 입력합니다.

3. **패키지**에서 **찾아보기**를 사용하여 사용할 서비스 패키지 파일(.cspkg)을 선택합니다.

4. **구성**에서 **찾아보기**를 사용하여 사용할 서비스 구성 파일(.cscfg)을 선택합니다.

5. 클라우드 서비스에 인스턴스가 하나만 있는 역할이 포함되어 있으면 **Deploy even if one or more roles contain a single instance** 확인란을 선택하여 배포가 계속 진행되도록 합니다.

    Azure는 모든 역할에 둘 이상의 인스턴스가 있는 경우에만 유지 관리 및 서비스 업데이트 중 클라우드 서비스에 대한 99.95%의 액세스를 보장할 수 있습니다. 필요한 경우 클라우드 서비스를 배포한 후 **크기 조정** 페이지에서 추가 역할 인스턴스를 추가할 수 있습니다. 자세한 내용은 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 참조하세요.

6. **확인**(확인 표시)을 클릭하여 클라우드 서비스 배포를 시작합니다.

	![CloudServices\_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

	메시지 영역에서 배포의 상태를 모니터링할 수 있습니다. 확인을 클릭하여 메시지를 숨깁니다.

	![CloudServices\_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## 배포가 완료되었는지 확인

1. **Dashboard**를 클릭합니다.

	서비스가 **실행 중** 상태로 표시됩니다.

2. **간략 상태**에서 사이트 URL을 클릭하여 웹 브라우저에서 클라우드 서비스를 엽니다.

    ![CloudServices\_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md
 
## 다음 단계

* [클라우드 서비스의 일반 구성](cloud-services-how-to-configure.md)
* [사용자 지정 도메인 이름](cloud-services-custom-domain-name.md) 구성
* [클라우드 서비스를 관리합니다](cloud-services-how-to-manage.md).
* [SSL 인증서](cloud-services-configure-ssl-certificate.md) 구성

<!---HONumber=AcomDC_0914_2016-->