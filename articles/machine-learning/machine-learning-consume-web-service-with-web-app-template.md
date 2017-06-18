<properties
	pageTitle="웹 앱 템플릿을 사용한 기계 학습 웹 서비스 사용 | Microsoft Azure"
	description="Azure 마켓플레이스에서 웹 앱을 사용하여 Azure 기계 학습의 예측 웹 서비스를 사용합니다."
	keywords="웹 서비스, 운영, REST API, 기계 학습"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="garye;raymondl"/>

# 웹 앱 템플릿을 사용한 Azure 기계 학습 웹 서비스 사용

>[AZURE.NOTE] 이 토픽에서는 기존 웹 서비스에 적용되는 기술을 설명합니다.

예측 모델을 개발하고 기계 학습 스튜디오를 사용하거나 R 또는 Python을 사용하여 Azure 웹으로 배포하면 REST API를 사용하여 운영할 수 있게 된 모델에 액세스할 수 있습니다.

REST API를 사용하고 웹 서비스에 액세스하는 방법은 많습니다. 예를 들어, 웹 서비스를 배포할 때 생성된 샘플 코드를 사용하여 C#, R 또는 Python에서 응용 프로그램을 작성할 수 있습니다(기계 학습 스튜디오의 웹 서비스 대시보드에서 API 도움말 페이지에 사용 가능). 또는 만든 샘플 Microsoft Excel 통합 문서를 사용할 수 있습니다(Studio의 웹 서비스 대시보드에서도 사용 가능).

그러나 웹 서비스를 액세스하는 가장 빠르고 쉬운 방법은 [Azure 웹 앱 마켓플레이스](https://azure.microsoft.com/marketplace/web-applications/all/)에서 사용 가능한 웹 앱 템플릿을 통한 것입니다.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Azure 기계 학습 웹 앱 템플릿

Azure 마켓플레이스에서 사용할 수 있는 웹 앱 템플릿은 웹 서비스의 입력 데이터 및 예상 결과를 알고 있는 사용자 지정 웹 앱을 구축할 수 있습니다. 따라서 웹 앱이 웹 서비스 및 데이터에 액세스하도록 하기만 하면 나머지 작업은 템플릿이 수행합니다.

두 가지 템플릿을 사용할 수 있습니다.

- [Azure ML 요청-응답 서비스 웹 앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Azure ML 일괄 처리 실행 서비스 웹 앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

각 템플릿은 웹 서비스용 API URI 및 키를 사용하여 샘플 ASP.NET 응용 프로그램을 만들고 Azure에 웹 사이트에 배포합니다. 요청-응답 서비스(RRS) 템플릿은 단일 결과를 가져오기 위해 데이터의 단일 행을 웹 서비스에 보낼 수 있도록 웹 앱을 만듭니다. 일괄 처리 실행 서비스(BES) 템플릿은 여러 결과를 가져오기 위해 많은 데이터의 행을 보낼 수 있도록 웹 앱을 만듭니다.

이러한 템플릿을 사용하기 위해 코딩 작업이 필요하지 않습니다. API URI 및 키를 제공하면 템플릿이 응용 프로그램을 빌드합니다.

## 요청-응답 서비스(RRS) 템플릿을 사용하는 방법

웹 서비스를 배포하고 나면 다음 다이어그램에 표시된 것처럼 RRS 웹 앱 템플릿을 사용하여 아래 단계를 따르면 됩니다.

![RRS 웹 템플릿을 사용하는 프로세스][image1]

1. 기계 학습 스튜디오에서 **웹 서비스** 탭을 연 다음, 액세스하려면 웹 서비스를 엽니다. **API 키** 아래에 나열된 키를 복사하고 저장합니다.

	![API 키][image3]

2. **요청/응답** API 도움말 페이지를 엽니다. 도움말 페이지의 맨 위에 있는 **요청** 아래에 있는 **요청 URI** 값을 복사하고 저장합니다. 이 값은 다음과 같이 표시됩니다.

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![요청 URI][image4]

3. [Azure 포털](https://portal.azure.com), **로그인**으로 이동하여 **새로 만들기**를 클릭하고 **Azure ML 요청-응답 서비스 웹앱**을 검색하고 선택한 다음 **만들기**를 클릭합니다.

	- 웹 앱에 고유한 이름을 지정합니다. 웹앱의 URL이 이름 뒤에 됩니다 `.azurewebsites.net.`예를 들어, `http://carprediction.azurewebsites.net.`

	- Azure 구독과 웹 서비스가 실행되는 서비스를 선택합니다.

	- **만들기**를 클릭합니다.

	![웹앱 만들기][image5]

4. Azure가 웹앱 배포를 완료하면 Azure의 웹앱 설정 페이지의 **URL**을 클릭하거나 웹 브라우저에 URL을 입력합니다. 예: `http://carprediction.azurewebsites.net.`

5. 웹앱을 처음 실행할 때 **API 게시 URL** 및**API 키**를 요청합니다. 이전에 저장된 값을 입력합니다.
	- **API 게시 URL**을 위한 API 도움말 페이지의 **요청 URI**
	- **API 키**를 위한 웹 서비스 대시보드의 **API 키**

	**Submit**를 클릭합니다.

	![Post URI 및 API 키 입력][image6]

6. 웹앱은 현재 웹 서비스 설정과 함께 **웹앱 구성** 페이지를 표시합니다. 여기서 웹 앱에서 사용되는 설정을 변경할 수 있습니다.

	> [AZURE.NOTE] 여기서 설정을 변경하면 이 웹 앱에 대 한 설정만 변경됩니다. 웹 서비스의 기본 설정을 변경되지 않습니다. 예를 들어, 여기에서**설명**을 변경하면 기계 학습 스튜디오의 웹 서비스 대시보드에 표시되는 설명은 변경되지 않습니다.

	완료되면 **변경 내용 저장**을 클릭한 다음 **홈페이지로 이동**을 클릭합니다.

7. 홈페이지에서 값을 입력하여 웹 서비스로 보낼 수 있으며, **제출**을 클릭하면 결과가 반환됩니다.

**구성** 페이지로 반환하려는 경우, 웹앱의 `setting.aspx` 페이지로 이동합니다. 예를 들어, `http://carprediction.azurewebsites.net/setting.aspx.` API 키를 다시 입력하라는 메시지가 표시됩니다. 이 키는 해당 페이지에 액세스하고 설정을 업데이트하는 데 필요합니다.

웹앱처럼 Azure 포털에서 웹앱을 중지하거나, 다시 시작하거나, 삭제할 수 있습니다. 실행되는 동안, 홈 웹 주소로 이동하여 새 값을 입력할 수 있습니다.

## 일괄 처리 실행 서비스(BES) 템플릿을 사용하는 방법

만든 웹 앱을 통해 여러 데이터 행을 제출하고 여러 결과를 수신하는 것을 제외하고, RRS 템플릿과 동일한 방식으로 BES 웹 앱 템플릿을 사용할 수 있습니다.

일괄 처리 실행 웹 서비스의 결과는 Azure 저장소 컨테이너에 저장되고, 입력된 값은 Azure 저장소 또는 로컬 파일에서 가져올 수 있습니다. 따라서 웹 앱에 의해 반환된 결과를 저장할 Azure 저장소 컨테이너가 필요하고 입력된 데이터를 준비시켜야 합니다.

![BES 웹 템플릿을 사용하는 프로세스][image2]

1. 다음을 제외하고 RRS 템플릿의 경우와 동일한 절차를 따라 BES 웹 앱을 만듭니다.
	- 웹 서비스의 **일괄 처리 실행** API 도움말 페이지로부터 **요청 URI**를 가져옵니다.
	- [Azure ML 일괄 처리 실행 서비스 웹앱 템플릿](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)으로 이동하여 Azure 마켓플레이스의 BES 템플릿을 열고 **웹앱 만들기**를 클릭합니다.

2. 결과를 저장하려는 위치를 지정하려면 웹 앱 홈페이지에 대상 컨테이너 정보를 입력합니다. 또한 웹 앱이 로컬 파일 또는 Azure 저장소 컨테이너에 있는 입력 값을 가져올 수 있는 위치를 지정합니다. **Submit**를 클릭합니다.

	![저장소 정보][image7]

웹 앱은 작업 상태와 함께 페이지를 표시합니다. 작업이 완료되면 Azure Blob 저장소에서 결과의 위치를 알게 됩니다. 또한 결과를 로컬 파일에 다운로드할 옵션이 있습니다.

## Blob에 대한 자세한 내용은

다음에 대한 자세한 정보

- 기계 학습 스튜디오로 기계 학습 실험 만들기는 [Azure 기계 학습 스튜디오에서 첫 번째 실험 만들기](machine-learning-create-experiment.md)를 참조하세요.

- 웹 서비스로서 기계 학습 실험을 배포하는 방법은 [Azure 기계 학습 웹 서비스 배포](machine-learning-publish-a-machine-learning-web-service.md)를 참조하세요.

- 웹 서비스에 액세스하는 다른 방법은 [Azure 기계 학습 웹 서비스 사용 방법](machine-learning-consume-web-services.md)을 참조하세요.


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_0914_2016-->