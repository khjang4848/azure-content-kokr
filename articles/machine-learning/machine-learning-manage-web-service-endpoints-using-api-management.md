<properties
	pageTitle="API 관리를 사용하여 AzureML 웹 서비스를 관리하는 방법 알아보기 | Microsoft Azure"
	description="API 관리를 사용하여 AzureML 웹 서비스를 관리하는 방법에 대한 가이드입니다."
	keywords="기계 학습, api 관리"
	services="machine-learning"
	documentationCenter=""
	authors="roalexan"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="roalexan" />


# API 관리를 사용하여 AzureML 웹 서비스를 관리하는 방법에 대해 알아봅니다.

##개요

이 가이드에서는 API 관리를 빠르게 시작하여 AzureML 웹 서비스를 관리하는 방법을 보여 줍니다.

##Azure API 관리란?

Azure API 관리는 사용자 액세스, 사용 제한 및 대시보드 모니터링을 정의하여 REST API 끝점을 관리할 수 있는 Azure 서비스입니다. Azure API 관리에 대한 자세한 내용은 [여기](https://azure.microsoft.com/services/api-management/)를 클릭하세요. Azure API 관리를 시작하는 방법에 대한 설명은 [여기](../api-management/api-management-get-started.md)를 클릭하세요. 이 가이드를 기반으로 하는 이 다른 가이드에서는 알림 구성, 가격 책정 계층, 응답 처리, 사용자 인증, 제품 생산, 개발자 구독 및 사용량 대시보딩을 포함하는 다양한 주제를 다룹니다.

##AzureML이란?

AzureML은 고급 분석 솔루션을 손쉽게 빌드, 배포 및 공유할 수 있도록 하는 기계 학습을 위한 Azure 서비스입니다. AzureML에 대한 자세한 내용은 [여기](https://azure.microsoft.com/services/machine-learning/)를 클릭하세요.

##필수 조건

이 가이드를 완료하려면 다음이 필요합니다.

* Azure 계정. Azure 계정이 없는 경우 무료 평가판 계정을 만드는 방법에 대한 자세한 내용은 [여기](https://azure.microsoft.com/pricing/free-trial/)를 클릭하세요.
* AzureML 계정. AzureML 계정이 없는 경우 무료 평가판 계정을 만드는 방법에 대한 자세한 내용은 [여기](https://studio.azureml.net/)를 클릭하세요.
* AzureML 실험에 대한 작업 영역, 서비스 및 api\_key는 웹 서비스로 배포됩니다. AzureML 실험을 만드는 방법에 대한 자세한 내용은 [여기](machine-learning-create-experiment.md)를 클릭하세요. AzureML 실험을 웹 서비스로 배포하는 방법에 대한 자세한 내용은 [여기](machine-learning-publish-a-machine-learning-web-service.md)를 클릭하세요. 또는 간단한 AzureML 실험을 만들고 테스트하고 이를 웹 서비스로 배포하는 방법에 대한 지침이 부록 A에 있습니다.

##API 관리 인스턴스 만들기

API 관리를 사용하여 AzureML 웹 서비스를 관리하는 단계는 다음과 같습니다. 먼저 서비스 인스턴스를 만듭니다. [클래식 포털](https://manage.windowsazure.com/)에 로그인하고 **새로 만들기** > **앱 서비스** > **API 관리** > **만들기**를 클릭합니다.

![create-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

고유한 **URL**을 지정합니다. 이 가이드에서는 **demoazureml**을 사용합니다. – 다른 방법을 선택해야 합니다. 서비스 인스턴스에 대해 원하는 **구독** 및 **지역**을 선택합니다. 선택한 후에 다음 단추를 클릭합니다.

![create-service-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

**조직 이름**에 대한 값을 지정합니다. 이 가이드에서는 **demoazureml**을 사용합니다. – 다른 방법을 선택해야 합니다. **관리자 메일** 필드에 메일 주소를 입력합니다. 이 전자 메일 주소는 API 관리 시스템에서 알림을 보내는 데 사용됩니다.

![create-service-2](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

확인란을 클릭하여 서비스 인스턴스를 만듭니다. *새로운 서비스를 만드는 데 최대 30분이 걸립니다*.

##API 만들기

서비스 인스턴스를 만든 후 다음 단계는 API를 만드는 것입니다. API는 클라이언트 응용 프로그램에서 호출할 수 있는 작업 집합으로 구성됩니다. API 작업은 기존 웹 서비스로 프록시 처리됩니다. 이 가이드는 기존 AzureML RRS 및 BES 웹 서비스에 대한 프록시인 API를 만듭니다.

API는 Azure 클래식 포털을 통해 액세스할 수 있는 API 게시자 포털에서 생성 및 구성됩니다. 게시자 포털에 연결하려면 서비스 인스턴스를 선택하세요.

![select-service-instance](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

API 관리 서비스에 대해 Azure 클래식 포털에서 **관리**를 클릭합니다.

![manage-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

왼쪽의 **API 관리** 메뉴에서 **API**를 클릭한 다음 **API 추가**를 클릭합니다.

![api-management-menu](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

**Web API 이름**으로 **AzureML 데모 API**를 입력합니다. **https://ussouthcentral.services.azureml.net**을 **웹 서비스 URL**로 입력합니다. **azureml-demo**를 **Web API URL 접미사**로 입력합니다. **HTTPS**를 **Web API URL** 체계로 확인합니다. **시작**을 **제품**으로 선택합니다. 완료되면 **저장**을 클릭하여 API를 만듭니다.

![add-new-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##작업 추가

해당 API에 새 작업을 추가하려면 **작업 추가**를 클릭합니다.

![add-operation](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

**새 작업** 창이 표시되고 **서명** 탭이 기본으로 선택됩니다.

##RRS 작업 추가

먼저 AzureML RRS 서비스에 대한 작업을 만듭니다. **HTTP 동사**로 **POST**를 선택합니다. **URL 템플릿**으로 **/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}**를 입력합니다. **표시 이름**으로 **RRS 실행**을 입력합니다.

![add-rrs-operation-signature](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

왼쪽의 **응답** > **추가**를 클릭하여 **200 OK**를 선택합니다. **저장**을 클릭하여 이 작업을 저장합니다.

![add-rrs-operation-response](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##BES 작업 추가

BES 작업에 대한 스크린샷은 RRS 작업을 추가하는 스크린샷과 매우 유사하므로 포함되어 있지 않습니다.

###일괄 처리 실행 작업 제출(시작하지는 않음)

API에 AzureML BES 작업을 추가하려면 **작업 추가**를 클릭합니다. **HTTP 동사**로 **POST**를 선택합니다. **URL 템플릿**에 **/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}**을 입력합니다. **표시 이름**에 **BES 제출**을 입력합니다. 왼쪽의 **응답** > **추가**를 클릭하여 **200 OK**를 선택합니다. **저장**을 클릭하여 이 작업을 저장합니다.

###일괄 처리 실행 작업 시작

API에 AzureML BES 작업을 추가하려면 **작업 추가**를 클릭합니다. **HTTP 동사**로 **POST**를 선택합니다. **URL 템플릿**에 **/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}**을 입력합니다. **표시 이름**에 **BES 시작**을 입력합니다. 왼쪽의 **응답** > **추가**를 클릭하여 **200 OK**를 선택합니다. **저장**을 클릭하여 이 작업을 저장합니다.

###일괄 처리 실행 작업의 상태 또는 결과 가져오기

API에 AzureML BES 작업을 추가하려면 **작업 추가**를 클릭합니다. **HTTP 동사**로 **GET**을 선택합니다. **URL 템플릿**에 **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}**을 입력합니다. **표시 이름**에 **BES 상태**를 입력합니다. 왼쪽의 **응답** > **추가**를 클릭하여 **200 OK**를 선택합니다. **저장**을 클릭하여 이 작업을 저장합니다.

###일괄 처리 실행 작업 삭제

API에 AzureML BES 작업을 추가하려면 **작업 추가**를 클릭합니다. **HTTP 동사**로 **DELETE**를 선택합니다. **URL 템플릿**에 **/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}**을 입력합니다. **표시 이름**에 **BES 삭제**를 입력합니다. 왼쪽의 **응답** > **추가**를 클릭하여 **200 OK**를 선택합니다. **저장**을 클릭하여 이 작업을 저장합니다.

##개발자 포털에서 작업 호출

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 가이드의 단계에서 **AzureML 데모 API**에 추가된 **RRS 실행** 메서드를 호출합니다. 클래식 포털의 오른쪽 위에 있는 메뉴에서 **개발자 포털**을 클릭합니다.

![developer-portal](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

맨 위 메뉴에서 **API**를 클릭하고 **AzureML 데모 API**를 클릭하여 사용 가능한 작업을 확인합니다.

![demoazureml-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

작업에 대해 **RRS 실행**을 선택합니다. **사용해 보세요.**를 클릭합니다.

![try-it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

요청 매개 변수로는 **apiversion**에 대해 **workspace**, **service**, **2.0**을 입력하고, **details**에 대해 **true**를 입력합니다. AzureML 웹 서비스 대시보드에서 **workspace** 및 **service**를 찾을 수 있습니다(부록 A에서 **웹 서비스 테스트** 참조).

요청 헤더로는 **헤더 추가**를 클릭하고 **Content-Type** 및 **application/json**을 입력하고 나서, **헤더 추가**를 클릭하고 **Authorization** 및 **Bearer<AZUREML 서비스 API 키>**를 입력합니다. AzureML 웹 서비스 대시보드에서 **api key**를 찾을 수 있습니다(부록 A에서 **웹 서비스 테스트** 참조).

요청 본문으로 **{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}**를 입력합니다.

![azureml-demo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

**보내기**를 클릭합니다.

![send](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

작업 호출 후에는 개발자 포털에 백 엔드 서비스의 **요청된 URL** 및 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

![response-status](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##부록 A - 간단한 AzureML 웹 서비스 만들기 및 테스트

###실험 만들기

간단한 AzureML 실험을 만들고 웹 서비스로 배포하는 단계는 다음과 같습니다. 웹 서비스에서는 임의 텍스트 열을 입력으로 사용하고 정수로 표시되는 기능 집합을 반환합니다. 예:

텍스트 | 해시된 텍스트
--- | ---
This is a good day | 1 1 2 2 0 2 0 1

먼저 선택한 브라우저를 사용하여 [https://studio.azureml.net/](https://studio.azureml.net/)으로 이동하고 자격 증명을 입력하여 로그인합니다. 그리고 새 실험을 만듭니다.

![search-experiment-templates](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

실험 이름을 **SimpleFeatureHashingExperiment**로 바꿉니다. **저장된 데이터 집합**을 확장하고 **Amazon의 도서 리뷰**를 실험으로 끌어서 놓습니다.

![simple-feature-hashing-experiment](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

**데이터 변환** 및 **조작**을 확장하고 **데이터 집합의 열 선택**을 실험으로 끌어서 놓습니다. **Amazon의 도서 리뷰**를 **데이터 집합의 열 선택**에 연결합니다.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

**데이터 집합의 열 선택**, **열 선택기 시작**을 차례로 클릭하고 **Col2**를 선택합니다. 확인 표시를 클릭하여 변경 내용을 적용합니다.

![select-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

**텍스트 분석**을 확장하고 **기능 해싱**을 실험으로 끌어서 놓습니다. **데이터 집합의 열 선택**을 **기능 해싱**에 연결합니다.

![connect-project-columns](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

**Hashing bitsize**로 **3**을 입력합니다. 8(23)개 열이 생성됩니다.

![hashing-bitsize](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

이때 **실행**을 클릭하여 실험을 테스트할 수 있습니다.

![run](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###웹 서비스 만들기

이제 웹 서비스를 만듭니다. **웹 서비스**를 확장하고 **입력**을 실험으로 끌어서 놓습니다. **입력**을 **기능 해싱**에 연결합니다. **출력**을 실험으로 끌어서 놓습니다. **출력**을 **기능 해싱**에 연결합니다.

![output-to-feature-hashing](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

**웹 서비스 게시**를 클릭합니다.

![publish-web-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

**예**를 클릭하여 실험을 게시합니다.

![yes-to-publish](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###웹 서비스 테스트

AzureML 웹 서비스는 RSS(요청/응답 서비스) 및 BES(일괄 처리 실행 서비스) 끝점으로 구성됩니다. RSS는 동기 실행에 사용됩니다. BES는 비동기 작업 실행에 사용됩니다. 다음 샘플 Python 소스로 웹 서비스를 테스트하려면 Python용 Azure SDK를 다운로드해야 할 수 있습니다([Python을 설치하는 방법](../python-how-to-install.md) 참조).

또한 다음 샘플 소스에 대한 실험의 **workspace**, **service** 및 **api\_key**가 필요합니다. 웹 서비스 대시보드에서 실험의 **요청/응답** 또는 **일괄 처리 실행**을 클릭하여 workspace 및 service를 찾을 수 있습니다.

![find-workspace-and-service](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

웹 서비스 대시보드에서 실험을 클릭하여 **api\_key**를 찾을 수 있습니다.

![find-api-key](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####RRS 끝점 테스트

#####테스트 단추

RRS 끝점을 테스트하는 간편한 방법은 웹 서비스 대시보드에서 **테스트**를 클릭하는 것입니다.

![test](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

**col2**로 **This is a good day**를 입력합니다. 확인 표시를 클릭합니다.

![enter-data](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

다음과 같이 표시됩니다.

![sample-output](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####샘플 코드

RRS를 테스트하는 또 다른 방법은 클라이언트 코드를 사용하는 것입니다. 대시보드에서 **요청/응답**을 클릭하고 아래쪽으로 스크롤하면 C#, Python 및 R에 대한 샘플 코드가 표시됩니다. 요청 URI, 헤더 및 본문을 포함한 RRS 요청 구문도 표시됩니다.

이 가이드에서는 작동하는 Python 예제를 보여 줍니다. 실험의 **workspace**, **service** 및 **api\_key**를 사용하여 예제를 수정해야 합니다.

	import urllib2
	import json
	workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
	service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
	api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
	data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
	}
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
	headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	body = str.encode(json.dumps(data))
	try:
    	req = urllib2.Request(url, body, headers)
    	response = urllib2.urlopen(req)
    	result = response.read()
    	print "result:" + result
			except urllib2.HTTPError, error:
    	print("The request failed with status code: " + str(error.code))
    	print(error.info())
    	print(json.loads(error.read()))

####BES 끝점 테스트
대시보드에서 **일괄 처리 실행**을 클릭하고 아래쪽으로 스크롤합니다. C#, Python 및 R에 대한 샘플 코드가 표시됩니다. 작업을 제출하고, 작업을 시작하고, 작업의 상태나 결과를 가져오고, 작업을 삭제하기 위한 BES 요청 구문도 표시됩니다.

이 가이드에서는 작동하는 Python 예제를 보여 줍니다. 실험의 **workspace**, **service** 및 **api\_key**를 사용하여 예제를 수정해야 합니다. 또한 **저장소 계정 이름**, **저장소 계정 키** 및 **저장소 컨테이너 이름**을 수정해야 합니다. 마지막으로 **입력 파일**의 위치와 **출력 파일**의 위치를 수정해야 합니다.

	import urllib2
	import json
	import time
	from azure.storage import *
	workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
	service = "<REPLACE WITH YOUR SERVICE ID>"
	api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
	storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
	storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
	storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
	input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
	output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
	input_blob_name = "mydatablob.csv"
	output_blob_name = "myresultsblob.csv"
	def printHttpError(httpError):
	print("The request failed with status code: " + str(httpError.code))
	print(httpError.info())
	print(json.loads(httpError.read()))
	return
	def saveBlobToFile(blobUrl, resultsLabel):
	print("Reading the result from " + blobUrl)
	try:
		response = urllib2.urlopen(blobUrl)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	with open(output_file, "w+") as f:
		f.write(response.read())
	print(resultsLabel + " have been written to the file " + output_file)
	return
	def processResults(result):
	first = True
	results = result["Results"]
	for outputName in results:
		result_blob_location = results[outputName]
		sas_token = result_blob_location["SasBlobToken"]
		base_url = result_blob_location["BaseLocation"]
		relative_url = result_blob_location["RelativeLocation"]
		print("The results for " + outputName + " are available at the following Azure Storage location:")
		print("BaseLocation: " + base_url)
		print("RelativeLocation: " + relative_url)
		print("SasBlobToken: " + sas_token)
		if (first):
			first = False
			url3 = base_url + relative_url + sas_token
			saveBlobToFile(url3, "The results for " + outputName)
	return

	def invokeBatchExecutionService():
	url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
	blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
	print("Uploading the input to blob storage...")
	data_to_upload = open(input_file, "r").read()
	blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
	print "Uploaded the input to blob storage"
	input_blob_path = "/" + storage_container_name + "/" + input_blob_name
	connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
	payload =  {
		"Input": {
			"ConnectionString": connection_string,
			"RelativeLocation": input_blob_path
		},
		"Outputs": {
			"output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
		},
		"GlobalParameters": {
		}
	}
		body = str.encode(json.dumps(payload))
	headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
	print("Submitting the job...")
	# submit the job
	req = urllib2.Request(url + "?api-version=2.0", body, headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	result = response.read()
	job_id = result[1:-1] # remove the enclosing double-quotes
	print("Job ID: " + job_id)
	# start the job
	print("Starting the job...")
	req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
	try:
		response = urllib2.urlopen(req)
	except urllib2.HTTPError, error:
		printHttpError(error)
		return
	url2 = url + "/" + job_id + "?api-version=2.0"

	while True:
		print("Checking the job status...")
		# If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
		req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
		try:
			response = urllib2.urlopen(req)
		except urllib2.HTTPError, error:
			printHttpError(error)
			return
		result = json.loads(response.read())
		status = result["StatusCode"]
		print "status:" + status
		if (status == 0 or status == "NotStarted"):
			print("Job " + job_id + " not yet started...")
		elif (status == 1 or status == "Running"):
			print("Job " + job_id + " running...")
		elif (status == 2 or status == "Failed"):
			print("Job " + job_id + " failed!")
			print("Error details: " + result["Details"])
			break
		elif (status == 3 or status == "Cancelled"):
			print("Job " + job_id + " cancelled!")
			break
		elif (status == 4 or status == "Finished"):
			print("Job " + job_id + " finished!")
			processResults(result)
			break
		time.sleep(1) # wait one second
	return
	invokeBatchExecutionService()

<!---HONumber=AcomDC_0921_2016-->