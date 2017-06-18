<properties 
	pageTitle="데이터 이동 - 데이터 관리 게이트웨이 | Microsoft Azure"
	description="데이터 게이트웨이를 설정하여 온-프레미스와 클라우드 간에 데이터를 이동합니다. Azure Data Factory의 데이터 관리 게이트웨이를 사용하여 데이터를 이동합니다." 
    keywords="데이터 게이트웨이, 데이터 통합, 데이터 이동, 게이트웨이 자격 증명"
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# 온-프레미스 원본과 클라우드 간에 데이터 관리 게이트웨이로 데이터 이동
이 문서에서는 Data Factory를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 통합의 개요를 제공합니다. [데이터 이동 활동](data-factory-data-movement-activities.md) 문서 및 [데이터 집합](data-factory-create-datasets.md)과 [파이프라인](data-factory-create-pipelines.md)과 같은 기타 Data Factory 핵심 개념 문서를 작성합니다.

## 데이터 관리 게이트웨이
온-프레미스 데이터 저장소 간에 데이터 이동을 사용할 수 있도록 온-프레미스 컴퓨터에 데이터 관리 게이트웨이를 설치해야 합니다. 게이트웨이가 데이터 저장소에 연결할 수 있는 한 데이터 저장소와 동일한 컴퓨터 또는 다른 컴퓨터에 게이트웨이를 설치할 수 있습니다. 데이터 관리 게이트웨이에 대한 모든 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.

다음 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 Data Factory를 만드는 방법을 보여 줍니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다.

## 연습: 클라우드에 온-프레미스 데이터 복사
  
## 데이터 팩터리 만들기
이 단계에서는 Azure 포털을 사용하여 **ADFTutorialOnPremDF**라는 Azure Data Factory 인스턴스를 만듭니다. 또한 Azure Data Factory cmdlet을 사용하여 데이터 팩터리를 만들 수도 있습니다.

1.	[Azure 포털](https://portal.azure.com)에 로그인한 후에 왼쪽 아래 모서리에서 **새로 만들기**를 클릭하고 **만들기** 블레이드에서 **데이터 분석**을 선택한 다음 **데이터 분석** 블레이드에서 **데이터 팩터리**를 클릭합니다.

	![새로 만들기->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. **새 데이터 팩터리** 블레이드에서 다음을 수행합니다.
	1. **ADFTutorialOnPremDF**를 **이름**으로 입력합니다.
	2. **리소스 그룹 이름**을 클릭하고 **ADFTutorialResourceGroup**을 선택합니다. 기존 리소스 그룹을 선택하거나 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들려면
		1. **새 리소스 그룹 만들기**를 클릭합니다.
		2. **리소스 그룹 만들기 블레이드**에서 리소스 그룹의 **이름**을 입력하고 **확인**을 클릭합니다.

7. **새 데이터 팩터리** 블레이드에서 **시작 보드에 추가**가 선택되어 있는지 확인합니다.

	![시작 보드에 추가](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. **새 데이터 팩터리** 블레이드에서 **만들기**를 클릭합니다.

	Azure Data Factory 이름은 전역적으로 고유해야 합니다. **데이터 팩터리 이름 “ADFTutorialOnPremDF”를 사용할 수 없습니다.** 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialOnPremDF) 다시 만듭니다. 이 자습서의 나머지 단계를 수행하는 동안 ADFTutorialOnPremDF 대신에 이 이름을 사용합니다.

9. 만들기 프로세스 중 다음 이미지에서처럼 제목 표시줄의 **알림** 단추를 클릭하여 알림을 검색합니다. 다시 클릭하면 알림 창이 닫힙니다.

	![알림 허브](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 만들기가 완료되면 다음 이미지에서 보여준 대로 **데이터 팩터리** 블레이드가 표시됩니다.

	![데이터 팩터리 홈 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## 게이트웨이 만들기
5. **데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
6.	데이터 팩터리 편집기에서 도구 모음의 **...(말줄임표)**를 클릭한 다음 **새 데이터 케이트웨이**를 클릭합니다.

	![도구 모음의 새 데이터 게이트웨이](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. **만들기** 블레이드에서 **adftutorialgateway**를 **이름**으로 입력하고 **확인**을 클릭합니다.

	![게이트웨이 만들기 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. 블레이드 **구성**에서 **이 컴퓨터에 직접 설치**를 클릭합니다. 이렇게 하면 컴퓨터에 게이트웨이의 설치 패키지가 다운로드되고 게이트웨이가 설치, 구성 및 등록됩니다.

	> [AZURE.NOTE] 
	Internet Explorer 또는 Microsoft ClickOnce 호환 웹 브라우저를 사용합니다.
	> 
	> Chrome을 사용하는 경우 [Chrome 웹 스토어](https://chrome.google.com/webstore/)로 이동하여 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택해 설치합니다.
	>  
	> Firefox의 경우에도 동일한 작업을 수행합니다(추가 기능 설치). 도구 모음(상단 오른쪽 모서리의 **가로 줄 세 개**)의 **열기 메뉴** 단추를 클릭하고 **추가 기능**을 클릭하고 "ClickOnce" 키워드로 검색하고 ClickOnce 확장 중 하나를 선택하고 설치합니다.

	![게이트웨이 - 구성 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	이 방법은 하나의 단계로 게이트웨이를 다운로드, 설치, 구성 및 등록하는 가장 쉬운 방법(한 번 클릭)입니다. **Microsoft 데이터 관리 게이트웨이 구성 관리자** 응용프로그램이 컴퓨터에 설치된 것을 확인할 수 있습니다. **C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** 폴더에서 **ConfigManager.exe** 실행 파일을 찾을 수도 있습니다.

	또한 이 블레이드에서 링크를 사용하여 게이트웨이를 수동으로 다운로드하여 설치하고 **새 키** 텍스트 상자에 표시된 키를 사용하여 등록할 수도 있습니다.
	
	게이트웨이에 대한 모든 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.

	>[AZURE.NOTE] 데이터 관리 게이트웨이를 성공적으로 설치 및 구성하려면 로컬 컴퓨터의 관리자여야 합니다. 데이터 관리 게이트웨이 사용자 로컬 Windows 그룹에 사용자를 더 추가할 수 있습니다. 이 그룹의 구성원은 데이터 관리 게이트웨이 구성 관리자 도구를 사용하여 게이트웨이를 구성할 수 있습니다.

5. 몇 분 정도 기다리면 컴퓨터에서 **데이터 관리 게이트웨이 구성 관리자** 응용 프로그램이 시작됩니다. **검색** 창에서 **데이터 관리 게이트웨이**를 입력하여 이 유틸리티에 액세스합니다. **C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared** 폴더에서 **ConfigManager.exe** 실행 파일을 찾을 수도 있습니다.

	![게이트웨이 구성 관리자](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 될 때까지 기다립니다.
	2. **게이트웨이 이름**이 **adftutorialgateway**로 설정됩니다.
	4. 맨 아래 상태 표시줄에 **녹색 확인 표시**와 함께 **클라우드 서비스에 연결됨**이 표시됩니다.

	**홈** 탭에서 다음을 수행할 수 있습니다. - 등록 단추를 사용하여 Azure 포털에서 키가 있는 게이트웨이 **등록**.- 게이트웨이 컴퓨터에서 실행 중인 데이터 관리 게이트웨이 호스트 서비스 **중지** - 특정 시간에 설치되도록 **업데이트 예약** - 게이트웨이가 **마지막으로 업데이트**된 시기 보기.

8. **설정** 탭으로 전환합니다. **인증서** 섹션에 지정된 인증서는 포털에서 지정한 온-프레미스 데이터 저장소에 대한 자격 증명을 암호화/해독하는 데 사용됩니다. (선택 사항) **변경**을 클릭하여 고유한 인증서를 대신 사용합니다. 기본적으로 게이트웨이는 데이터 팩터리 서비스에서 자동으로 생성되는 인증서를 사용합니다.

	![게이트웨이 인증서 구성](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

	설정 탭에서 다음을 수행할 수도 있습니다. - 게이트웨이에서 사용한 인증서 보기 또는 내보내기 - 게이트웨이에서 사용한 HTTPS 끝점 변경 -
9. (선택 사항) 게이트웨이와 관련된 모든 문제를 해결하는 데 사용할 수 있는 자세한 정보 로깅을 활성화하려면 **진단** 탭으로 전환하고 **자세한 정보 로깅 사용** 옵션을 체크합니다. 로깅 정보는 **응용 프로그램 및 서비스 로그** -> **데이터 관리 게이트웨이** 노드 아래의 **이벤트 뷰어**에서 찾을 수 있습니다.

	![진단 탭](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	**진단** 탭에서 다음을 수행할 수도 있습니다.
	
	- 게이트웨이를 사용하여 온-프레미스 데이터 원본에 대한 **연결 테스트** 섹션을 사용합니다.
	- **로그 보기**를 클릭하여 이벤트 뷰어 창에서 데이터 관리 게이트웨이 로그를 확인합니다.
	- **로그 보내기**를 클릭하여 지난 7일 간의 로그가 포함된 zip 파일을 Microsoft에 업로드하여 문제를 원활하게 해결할 수 있습니다.
10. Azure 포털의 **구성** 블레이드, **새 데이터 게이트웨이** 블레이드에서 차례로 **확인**을 클릭합니다.
6. 왼쪽 트리의 **데이터 게이트웨이** 아래에 **adftutorialgateway**가 표시되어야 합니다. 이 항목을 클릭하면 연결된 JSON이 나타납니다.
	

## 연결된 서비스 만들기 
이 단계에서는 두 개의 연결된 서비스인 **AzureStorageLinkedService** 및 **SqlServerLinkedService**를 만듭니다. **SqlServerLinkedService**는 온-프레미스 SQL Server 데이터베이스를 연결하며 **AzureStorageLinkedService** 연결된 서비스는 Azure Blob 저장소를 Data Factory에 연결합니다. 이 연습의 뒷부분에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만듭니다.

#### 온-프레미스 SQL Server 데이터베이스에 연결된 서비스 추가
1.	**데이터 팩터리 편집기**의 도구 모음에서 **새 데이터 저장소**를 클릭하고 **SQL Server**를 선택합니다.

	![새로운 SQL Server 연결 서비스](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
3.	**JSON 편집기**에서 다음을 수행합니다.
	1. **gatewayName**에 **adftutorialgateway**를 지정합니다.
	2. Windows 인증을 사용하는 경우
		1. **connectionString**에서 **Integrated Security**를 **true**로 설정하고, 데이터베이스 **서버 이름**과 **데이터베이스 이름**을 지정하고, **사용자 ID**과 **암호**를 제거합니다.
		3. **userName** 및 **password** 속성에 사용자 이름과 암호를 지정합니다.
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\<user>",
            		"password": "<Specify password for the user account>"
        		}

	4. SQL 인증을 사용하는 경우
		1. **connectionString**에서 데이터베이스 **server name**, **database name**, **User ID** 및**Password**를 지정합니다.
		2. 마지막 두 JSON 속성, 즉 **userName** 및 **password**를 JSON에서 제거합니다.
		3. gatewayName** 속성 **값을 지정하는 행의 마지막에서** `,` (쉼표)를 제거합니다.

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	
		자격 증명은 Data Factory 서비스가 소유하는 인증서를 사용하여 **암호화**됩니다. 대신 데이터 관리 게이트웨이와 연결된 인증서를 사용하려는 경우 [안전하게 자격 증명 설정](#set-credentials-and-security)을 참조하세요.
    
2.	명령 모음에서 **배포**를 클릭하여 SQL Server 연결 서비스를 배포합니다.

#### Azure 저장소 계정에 대한 연결된 서비스 추가
 
1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure 저장소**를 클릭합니다.
2. **계정 이름**에 Azure 저장소 계정의 이름을 입력합니다.
3. **계정 키**에 Azure 저장소 계정의 키를 입력합니다.
4. **배포**를 클릭하여 **AzureStorageLinkedService**를 배포합니다.
   
 
## 데이터 집합 만들기
이 단계에서는 복사 작업(온-프레미스 SQL Server 데이터베이스 = > Azure Blob 저장소)의 입력 및 출력 데이터를 나타내는 입력 및 출력 데이터 집합을 만듭니다. 데이터 집합 또는 테이블(사각형 데이터 집합)을 만들려면 먼저 다음을 수행해야 합니다(자세한 단계는 목록 다음에 나옴).

- 데이터 팩터리에 연결된 서비스로 추가한 SQL Server 데이터베이스에서 **emp**라는 테이블을 만들고 테이블에 몇 가지 샘플 항목을 삽입합니다.
- 데이터 팩터리에 연결된 서비스로 추가한 Azure BLOB 저장소 계정에서 **adftutorial**로 명명된 BLOB 컨테이너를 만듭니다.

### 자습서에 필요한 온-프레미스 SQL Server 준비

1. 온-프레미스 SQL Server 연결된 서비스(**SqlServerLinkedService**)에 대해 지정된 데이터베이스에서, 다음 SQL 스크립트를 사용하여 데이터베이스에 **emp** 테이블을 만듭니다.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 테이블에 일부 샘플을 삽입합니다.


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 입력 테이블 만들기

1. **데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **SQL Server 테이블**을 클릭합니다.
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	다음 사항에 유의하세요.
	
	- **type**을 **SqlServerTable**로 설정합니다.
	- **tablename**을 **emp**로 설정합니다.
	- **linkedServiceName**을 **SqlServerLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- Azure Data Factory의 다른 파이프라인에서 생성되지 않는 입력 테이블의 경우 **external**을 **true**로 설정해야 합니다. 이 섹션은 입력 데이터가 Azure Data Factory 서비스 외부에서 생성되었음을 나타냅니다. **Policy** 섹션에서 **externalData** 요소를 사용하여 외부 데이터 정책을 선택적으로 지정할 수 있습니다.

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference]를 참조하세요.

2. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **테이블이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.


### 출력 테이블 만들기

1.	**데이터 팩터리 편집기**의 명령 모음에서 **새 데이터 집합**을 클릭하고 **Azure Blob 저장소**를 클릭합니다.
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	다음 사항에 유의하세요.
	
	- **type**을 **AzureBlob**으로 설정합니다.
	- **linkedServiceName**을 **AzureStorageLinkedService**(2단계에서 만든 연결된 서비스)로 설정합니다.
	- **folderPath**를 **adftutorial/outfromonpremdf**로 설정합니다. 여기서 outfromonpremdf는 adftutorial 컨테이너의 폴더입니다. 아직 없는 경우 **adftutorial** 컨테이너를 만듭니다.
	- **availability**는 **hourly**(**frequency**는 **hour**로, **interval**은 **1**로 설정)로 설정됩니다. 데이터 팩터리 서비스는 Azure SQL 데이터베이스의 **emp** 테이블에 출력 데이터 조각을 1시간마다 생성합니다.

	**입력 테이블**의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial]의 샘플 파일을 참조하세요.
 
	**출력 테이블**의 **fileName**을 지정하지 않는 경우, **folderPath**에 생성되는 파일의 이름은 다음과 같은 형식으로 지정됩니다. Data.<Guid>.txt(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).

	**SliceStart** 시간을 기준으로 **folderPath** 및 **fileName**을 동적으로 설정하려면 partitionedBy 속성을 사용합니다. 다음 예제에서 folderPath는 SliceStart(처리 중인 조각의 시작 시간)의 연도, 월 및 일을 사용하고 fileName은 SliceStart의 시간을 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference]를 참조하세요.

2.	명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **테이블이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.
  

## 파이프라인 만들기
이 단계에서는 **EmpOnPremSQLTable**을 입력으로, **OutputBlobTable**을 출력으로 사용하는 **복사 작업**을 포함하는 **파이프라인**을 만듭니다.

1.	**데이터 팩터리** 블레이드에서 **작성자 및 배포** 타일을 클릭하여 데이터 팩터리에 대한 **편집기**를 시작합니다.

	![작성 및 배포 타일](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2.	명령 모음에서 **새 파이프라인**을 클릭합니다. 단추가 표시되지 않는 경우 **...(줄임표)**를 클릭하여 명령 모음을 확장합니다.
2.	오른쪽 창의 JSON을 다음 텍스트로 바꿉니다.
	
			{
				"name": "ADFTutorialPipelineOnPrem",
				"properties": {
		    	"description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    	"activities": [
		      	{
		        	"name": "CopyFromSQLtoBlob",
			        "description": "Copy data from on-prem SQL server to blob",
		        	"type": "Copy",
		        	"inputs": [
		          	{
		            	"name": "EmpOnPremSQLTable"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "OutputBlobTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "SqlSource",
			            "sqlReaderQuery": "select * from emp"
			          },
			          "sink": {
			            "type": "BlobSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2016-07-05T00:00:00Z",
			    "end": "2016-07-06T00:00:00Z",
			    "isPaused": false
			  }
			}

	다음 사항에 유의하세요.
 
	- activities 섹션에는 **type**이 **Copy**로 설정된 작업 하나밖에 없습니다.
	- 작업에 대한 **입력**을 **EmpOnPremSQLTable**로 설정하고 작업에 대한 **출력**을 **OutputBlobTable**로 설정합니다.
	- **변형** 섹션에서 **SqlSource**를 **원본 형식**으로 지정하고 **BlobSink**를 **싱크 형식**으로 지정합니다.
	- **SqlSource**의 **sqlReaderQuery** 속성에 대해 SQL 쿼리 `select * from emp`을 지정합니다.

	**start** 속성 값을 현재 날짜로 바꾸고 **end** 값을 다음 날짜로 바꿉니다. start 및 end 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다.
	
	**end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **end** 속성 값으로 **9/9/9999**를 지정합니다.
	
	각 Azure 데이터 팩터리 테이블에 대해 정의된 **가용성** 속성을 기준으로 데이터 조각이 처리되는 기간이 정의됩니다.
	
	예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

	
2. 명령 모음에서 **배포**를 클릭하여 데이터 집합을 배포합니다(테이블은 사각형 데이터 집합임). 제목 표시줄에 **파이프라인이 성공적으로 배포됨** 메시지가 표시되는지 확인합니다.
5. 이제 **X**를 클릭하여 **편집기** 블레이드를 닫습니다. **X**를 다시 클릭하여 도구 모음 및 트리 뷰가 있는 ADFTutorialDataFactory 블레이드를 닫습니다. **저장하지 않은 편집 내용이 삭제됩니다** 메시지가 표시되면 **확인**을 클릭합니다.
6. **ADFTutorialOnPremDF**의 **데이터 팩터리** 블레이드로 돌아갑니다.

**축하합니다.** Azure Data Factory, 연결된 서비스, 테이블 및 파이프라인을 성공적으로 만들고 파이프라인을 예약했습니다.

#### 다이어그램 뷰에서 데이터 팩터리 보기 
1. **Azure 포털**에서 **ADFTutorialOnPremDF** 데이터 팩터리의 홈페이지에 있는 **다이어그램** 타일을 클릭합니다.

	![다이어그램 링크](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 다음과 유사한 다이어그램이 표시됩니다.

	![다이어그램 뷰](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	확대, 축소, 100% 확대, 크기에 맞게, 자동으로 파이프라인 및 테이블 위치 지정, 계보 정보 표시(선택한 항목의 업스트림 및 다운스트림 항목 강조 표시)를 수행할 수 있습니다. 개체(입출력 테이블 또는 파이프라인)를 두 번 클릭하면 해당 속성을 볼 수 있습니다.

## 파이프라인 모니터링
이 단계에서는 Azure 포털을 사용하여 Azure Data Factory에서 어떤 일이 일어나는지 모니터링합니다. 또한 PowerShell cmdlet을 사용하여 데이터 집합과 파이프라인을 모니터링할 수도 있습니다. 모니터링에 대한 자세한 내용은 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서를 참조하세요.

1. 닫혀 있는 경우 **Azure 포털**로 이동합니다.
2. **ADFTutorialOnPremDF**에 대한 블레이드가 열려있지 않은 경우, **시작 보드**에서 **ADFTutorialOnPremDF**를 클릭하여 엽니다.
3. 만든 테이블과 파이프라인의 **개수** 및 **이름**이 이 블레이드에 표시됩니다.

	![데이터 팩터리 홈 페이지](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. 이제 **데이터 집합** 타일을 클릭합니다.
5. **데이터 집합** 블레이드에서 **EmpOnPremSQLTable**을 클릭합니다.

	![EmpOnPremSQLTable 조각](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 현재 시간까지의 데이터 조각이 이미 생성되어 **Ready** 상태입니다. SQL Server 데이터베이스에 데이터를 삽입했기 때문이며 데이터가 데이터베이스에 항상 있습니다. 맨 아래의 **Problem slices(문제 조각)** 섹션에 표시되는 조각이 없는지 확인합니다.
	
	**Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록은 둘 다 **마지막 업데이트 시간**을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.

	**Set-AzureRmDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
	
	실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.

	목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.

	조각 시작/종료 시간을 기준으로 정렬된 데이터 조각을 보려면 **데이터 조각(조각 시간별)** 타일을 클릭합니다.
7. 이제 **데이터 집합** 블레이드에서 **OutputBlobTable**을 클릭합니다.

	![OputputBlobTable 조각](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
8. 현재 시간까지 조각이 생성되었고 **Ready** 상태인지 보고 확인합니다. 현재 시간까지 조각의 상태가 **Ready**로 설정될 때까지 기다립니다.
9. 목록에서 아무 데이터 조각이나 클릭하면 **데이터 조각** 블레이드가 표시됩니다.

	![데이터 조각 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	조각이 **Ready** 상태가 아닌 경우 **Upstream slices that are not ready(준비되지 않은 업스트림 조각)** 목록에서 Ready 상태가 아니고 현재 조각의 실행을 차단하는 업스트림 조각을 확인할 수 있습니다.

10. 맨 아래 목록에서 **작업 실행**을 클릭하여 **작업 실행 세부 정보**를 표시합니다.

	![작업 실행 세부 정보 블레이드](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

11. **X**를 클릭하여 모든 블레이드를 닫아
12. **ADFTutorialOnPremDF**의 홈 블레이드로 돌아갑니다.
14. (선택 사항) **파이프라인**을 클릭하고 **ADFTutorialOnPremDF**를 클릭한 다음 입력 테이블(**Consumed**) 또는 출력 테이블(**Produced**)을 드릴스루합니다.
15. **Azure 저장소 탐색기**와 같은 도구를 사용하여 출력을 확인합니다.

	![Azure 저장소 탐색기](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 다음 단계

- 데이터 관리 게이트웨이에 대한 모든 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.
- 복사 작업을 사용하여 원본 데이터 저장소에서 싱크 데이터 저장소에 데이터로 이동하는 방법에 대해 자세히 알아보려면 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->