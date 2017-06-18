<properties
   pageTitle="PowerShell을 사용하여 Azure 데이터 레이크 저장소로 HDInsight 클러스터 만들기 | Azure"
   description="Azure PowerShell을 사용하여 Azure 데이터 레이크로 HDInsight Hadoop 클러스터 만들기 및 사용"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/01/2016"
   ms.author="nitinme"/>

# Azure PowerShell을 사용하여 데이터 레이크 저장소로 HDInsight 클러스터 만들기

> [AZURE.SELECTOR]
- [포털 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
- [PowerShell 사용](data-lake-store-hdinsight-hadoop-use-powershell.md)


Azure PowerShell을 사용하여 Azure 데이터 레이크 저장소에 대한 액세스 권한을 가진 HDInsight 클러스터(Hadoop, HBase 또는 Storm)를 구성하는 방법에 대해 알아봅니다. 이 릴리스에 대한 일부 중요한 고려 사항:

* **Hadoop 및 Storm 클러스터(Windows 및 Linux)의 경우** 데이터 레이크 저장소는 추가 저장소 계정으로만 사용될 수 있습니다. 이러한 클러스터에 대한 기본 저장소 계정은 여전히 Azure 저장소 Blob(WASB)입니다.

* **HBase 클러스터(Windows 및 Linux)의 경우** 데이터 레이크 저장소는 기본 저장소나 추가 저장소로 사용될 수 있습니다.

> [AZURE.NOTE] 염두해 둘 몇 가지 중요한 사항은 다음과 같습니다.
> 
> * Data Lake 저장소에 액세스할 수 있는 HDInsight 클러스터를 만드는 옵션은 HDInsight 버전 3.2 및 3.4(Windows 및 Linux의 경우)에만 사용할 수 있습니다. Linux에서 Spark 클러스터에 대 한이 옵션은 3.4 HDInsight 클러스터에서 사용할 수만 있습니다.
>
> * 위에서 설명했듯이 일부 클러스터 형식(HBase)에 대한 기본 저장소 및 다른 클러스터 형식(예: Hadoop, Spark, Storm)에 대한 추가 저장소로 Data Lake 저장소를 사용할 수 있습니다. Data Lake 저장소를 추가 저장소 계정으로 사용하면 클러스터에서 저장소로 읽고 쓰는 성능 또는 기능에 영향을 주지 않습니다. Data Lake 저장소를 추가 저장소로 사용하는 시나리오에서 처리하려는 데이터는 Data Lake 저장소 계정에 저장되는 반면 클러스터 관련 파일(예: 로그 등)은 기본 저장소(Azure Blob)에 기록됩니다.


이 문서에서 데이터 레이크 저장소를 추가 저장소로 사용하여 Hadoop 클러스터를 프로비저닝합니다.

PowerShell을 사용하여 데이터 레이크 저장소와 함께 작동하도록 HDInsight를 구성하는 단계는 다음과 같습니다.

* Azure 데이터 레이크 저장소 만들기
* 데이터 레이크 저장소에 대한 역할 기반 액세스를 위한 인증 설정
* 데이터 레이크 저장소에 대한 인증을 사용하여 HDInsight 클러스터 만들기
* 클러스터에서 테스트 작업 실행

## 필수 조건

이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- Data Lake Store 공개 미리 보기에 대해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- **Windows SDK**. [여기](https://dev.windows.com/ko-KR/downloads)에서 설치할 수 있습니다. 이를 사용하여 보안 인증서를 만듭니다.


##Azure PowerShell 1.0 이상 설치

시작하려면 0.9x 버전의 Azure PowerShell을 제거해야 합니다. 설치된 PowerShell의 버전을 확인하려면 PowerShell 창에서 다음 명령을 실행합니다.

	Get-Module *azure*

이전 버전을 제거하려면 제어판에서 **프로그램 및 기능**을 실행하고 PowerShell 1.0보다 이전 버전인 경우 설치된 버전을 제거합니다.

Azure PowerShell을 설치하기 위한 두 가지 주요 옵션이 있습니다.

- [PowerShell 갤러리](https://www.powershellgallery.com/). 관리자 권한 PowerShell ISE 또는 관리자 권한 Windows PowerShell 콘솔에서 다음 명령을 실행합니다.

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM

		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure

		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM

		# Import Azure Service Management module
		Import-Module Azure

	자세한 내용은 [PowerShell 갤러리](https://www.powershellgallery.com/)를 참조하세요.

- [Microsoft WebPI(웹 플랫폼 설치 관리자)](http://aka.ms/webpi-azps). Azure PowerShell 0.9.x가 설치되어 있는 경우 0.9.x를 제거하라는 메시지가 표시됩니다. PowerShell 갤러리에서 Azure PowerShell 모듈을 설치한 경우 일관성 있는 Azure PowerShell 환경이 보장되도록 설치하기 전에 설치 관리자에서 모듈을 제거해야 합니다. 자세한 내용은 [WebPI를 통해 Azure PowerShell 1.0 설치](https://azure.microsoft.com/blog/azps-1-0/)를 참조하세요.

WebPI는 월별 업데이트를 받습니다. PowerShell 갤러리는 지속적으로 업데이트를 받습니다. PowerShell 갤러리에서 설치가 익숙하다면 이는 Azure PowerShell에서 가장 유용한 최신의 첫 번째 채널이 될 것입니다.


## Azure 데이터 레이크 저장소 만들기

다음 단계에 따라 데이터 레이크 저장소를 만듭니다.

1. 바탕 화면에서 새 Azure PowerShell 창을 열고 다음 코드 조각을 입력합니다. 로그인하라는 메시지가 표시되면 구독 관리자/소유자 중 하나로 로그인해야 합니다.

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

	>[AZURE.NOTE] Data Lake 저장소 리소스 공급자를 등록할 때 `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`와 유사한 오류가 나타나는 경우 구독이 Azure Data Lake 저장소에 대한 허용 목록에 추가되지 않았을 수 있습니다. 이 [지침](data-lake-store-get-started-portal.md#signup)에 따라 Data Lake 저장소 공개 미리 보기에 대한 Azure 구독을 활성화해야 합니다.

3. Azure 데이터 레이크 저장소 계정은 Azure 리소스 그룹과 연결됩니다. Azure 리소스 그룹을 만드는 작업부터 시작합니다.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Azure 리소스 그룹 만들기](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Azure 리소스 그룹 만들기")

2. Azure 데이터 레이크 저장소 계정을 만듭니다. 지정하는 계정 이름은 소문자와 숫자만 포함해야 합니다.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Azure 데이터 레이크 계정 만들기](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Azure 데이터 레이크 계정 만들기")

3. 계정이 성공적으로 만들어졌는지 확인합니다.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	이에 대한 출력은 **True**여야 합니다.

4. 일부 샘플 데이터를 Azure 데이터 레이크에 업로드합니다. 나중에 이 문서에서 사용하여 HDInsight 클러스터에서 데이터에 액세스할 수 있는지 확인합니다. 업로드할 일부 샘플 데이터를 찾는 경우 [Azure 데이터 레이크 Git 리포지토리](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)의 **Ambulance Data** 폴더에 있을 수 있습니다.


		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## 데이터 레이크 저장소에 대한 역할 기반 액세스를 위한 인증 설정

모든 Azure 구독은 Azure Active Directory와 연결됩니다. Azure 클래식 포털 또는 Azure 리소스 관리자 API를 사용하여 구독 리소스에 액세스하는 사용자와 서비스는 먼저 해당 Azure Active Directory에 인증해야 합니다. Azure 리소스에 대한 적절한 역할을 할당하여 Azure 구독과 서비스에 액세스 권한을 부여합니다. 서비스의 경우 서비스 주체는 Azure Active Directory(AAD)의 서비스를 식별합니다. 이 섹션에서는 서비스 주체를 만들고 Azure PowerShell을 통해 역할을 할당하여 HDInsight, Azure 리소스(이전에 만든 Azure 데이터 레이크 저장소 계정)에 대한 액세스와 같은 응용 프로그램 서비스를 부여하는 방법을 보여 줍니다.

Azure 데이터 레이크에 대한 Active Directory 인증을 설정하려면 다음 작업을 수행해야 합니다.

* 자체 서명된 인증서 만들기
* Azure Active Directory 및 서비스 주체의 응용 프로그램 만들기

### 자체 서명된 인증서 만들기

이 섹션의 단계를 진행하기 전에 [Windows SDK](https://dev.windows.com/ko-KR/downloads)가 설치되어 있는지 확인합니다. 또한 인증서가 만들어지는 **C:\\mycertdir**과 같은 디렉터리가 만들어져 있어야 합니다.

1. PowerShell 창에서 Windows SDK를 설치한 위치로 이동(일반적으로 `C:\Program Files (x86)\Windows Kits\10\bin\x86`)하고 [MakeCert][makecert] 유틸리티를 사용하여 자체 서명된 인증서와 개인 키를 만듭니다. 다음 명령을 사용합니다.

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	개인 키 암호를 입력하라는 메시지가 표시됩니다. 명령을 성공적으로 실행한 후 지정한 인증서 디렉터리에서 **CertFile.cer** 및 **mykey.pvk**를 확인해야 합니다.

4. [Pvk2Pfx][pvk2pfx] 유틸리티를 사용하여 MakeCert가 생성한 .pvk 및 .cer 파일을 .pfx 파일로 변환합니다. 다음 명령을 실행합니다.

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	메시지가 표시되면 이전에 지정한 개인 키 암호를 입력합니다. **-po** 매개 변수에 대해 지정한 값은 .pfx 파일에 연관된 암호입니다. 명령을 성공적으로 완료한 후 지정한 인증서 디렉터리에서 CertFile.pfx도 또한 확인해야 합니다.

###  Azure Active Directory 및 서비스 주체 만들기

이 섹션에서는 Azure Active Directory 응용 프로그램용 서비스 주체를 만들고, 서비스 주체에 역할을 할당하고, 인증서를 제공하여 서비스 주체로 인증하는 단계를 수행합니다. 다음 명령을 실행하여 Azure Active Directory에서 응용 프로그램을 만듭니다.

1. PowerShell 콘솔 창에 다음 cmdlet을 붙여 넣습니다. **-DisplayName** 속성에 대해 지정한 값이 고유한지 확인합니다. 또한 **-HomePage** 및 **-IdentiferUris**에 대한 값은 자리 표시자이며 확인되지 않습니다.

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"

		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

		$rawCertificateData = $certificatePFX.GetRawCertData()

		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" `
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. 응용 프로그램 ID를 사용하여 서비스 주체를 만듭니다.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

		$objectId = $servicePrincipal.Id

3. 앞에서 만든 데이터 레이크 저장소에 서비스 주체 액세스를 부여합니다.

		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	프롬프트에 **Y**를 입력하여 확인합니다.

## 데이터 레이크 저장소에 대한 인증을 사용하여 HDInsight 클러스터 만들기

이 섹션에서는 HDInsight Hadoop 클러스터를 만듭니다. 이 릴리스의 경우 HDInsight 클러스터와 데이터 레이크 저장소는 동일한 위치(미국 동부 2)에 있어야 합니다.

1. 구독 테넌트 ID 검색을 시작합니다. 나중에 필요합니다.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. 이 릴리스에서 Hadoop 클러스터의 경우 데이터 레이크 저장소는 클러스터에 대해 추가 저장소로만 사용될 수 있습니다. 기본 저장소는 여전히 Azure 저장소 Blob(WASB)이 됩니다. 따라서 먼저 클러스터에 필요한 저장소 계정 및 저장소 컨테이너를 만들어 보겠습니다.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. HDInsight 클러스터를 만듭니다. 다음 cmdlet을 사용합니다.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential

		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	cmdlet이 성공적으로 완료된 후 다음과 같은 출력을 확인해야 합니다.

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts :

## HDInsight 클러스터에서 테스트 작업을 실행하여 데이터 레이크 저장소 사용

HDInsight 클러스터를 구성한 후에 클러스터에서 테스트 작업을 실행하여 HDInsight 클러스터가 데이터 레이크 저장소에 액세스할 수 있는지 테스트할 수 있습니다. 이렇게 하려면 데이터 레이크 저장소에 이전에 업로드한 샘플 데이터를 사용하여 테이블을 만드는 샘플 Hive 작업을 실행합니다.

### Linux 클러스터의 경우

이 섹션에서 클러스터로 SSH하고 샘플 Hive 쿼리를 실행합니다. Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

1. 연결되면 다음 명령을 사용하여 Hive CLI를 시작합니다.

    	hive

2. CLI를 사용하여 다음 문을 입력하여 Data Lake 저장소에서 샘플 데이터를 사용한 **vehicles**라는 새 테이블을 만듭니다.

		DROP TABLE vehicles;
		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
		SELECT * FROM vehicles LIMIT 10;

	다음과 유사한 결과가 표시됩니다.

		1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
		1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
		1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
		1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
		1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
		1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
		1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
		1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
		1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
		1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


### Windows 클러스터의 경우

다음 cmdlet을 사용하여 Hive 쿼리를 실행합니다. 이 쿼리에서 데이터 레이크 저장소의 데이터에서 테이블을 만든 다음 만든 테이블에서 select 쿼리를 실행합니다.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

다음 출력이 표시됩니다. 출력에서 0의 **ExitValue**는 작업이 성공적으로 완료됨을 제안합니다.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

다음 cmdlet을 사용하여 작업에서 출력을 검색합니다.

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

작업 출력은 다음과 유사합니다.

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


## HDFS 명령을 사용한 액세스 데이터 레이크 저장소

데이터 레이크 저장소를 사용하도록 HDInsight 클러스터를 구성한 후 HDFS 셸 명령을 사용하여 저장소에 액세스할 수 있습니다.

### Linux 클러스터의 경우

이 섹션에서 클러스터로 SSH하고 HDFS 명령을 실행합니다. Windows에는 SSH 클라이언트가 기본 제공되지 않습니다. **PuTTY**를 사용하는 것이 좋습니다([http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있음).

PuTTY 사용에 대한 자세한 내용은 [Windows에서 HDInsight의 Linux 기반 Hadoop과 SSH 사용](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)을 참조하세요.

연결되면 다음 HDFS 파일 시스템 명령을 사용하여 데이터 레이크 저장소에 파일을 나열합니다.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.


### Windows 클러스터의 경우

1. 새로운 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **찾아보기**를 클릭하고 **HDInsight 클러스터**를 클릭한 다음 만든 HDInsight 클러스터를 클릭합니다.

3. 클러스터 블레이드에서 **원격 데스크톱**을 클릭한 다음 **원격 데스크톱** 블레이드에서 **연결**을 클릭합니다.

	![HDI 클러스터로 원격](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Azure 리소스 그룹 만들기")

	메시지가 표시되면 원격 데스크톱 사용자에 대해 제공된 자격 증명을 입력합니다.

4. 원격 세션에서 Windows PowerShell을 시작하고 HDFS 파일 시스템 명령을 사용하여 Azure 데이터 레이크 저장소의 파일을 나열합니다.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	데이터 레이크 저장소에 이전에 업로드한 파일이 나열되어야 합니다.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	`hdfs dfs -put` 명령을 사용하여 일부 파일을 데이터 레이크 저장소에 업로드한 다음 `hdfs dfs -ls`을(를) 사용하여 파일이 성공적으로 업로드되었는지 여부를 확인할 수도 있습니다.

## 참고 항목

* [포털: HDInsight 클러스터를 만들어 데이터 레이크 저장소 사용](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0914_2016-->