<properties 
	pageTitle="Panda를 사용하여 Azure blob 저장소 데이터에 대한 기능 만들기 | Microsoft Azure" 
	description="Panda Python 패키지를 사용하여 Azure blob 컨테이너에 저장된 데이터에 대한 기능을 만드는 방법" 
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
	ms.date="09/19/2016" 
	ms.author="bradsev;garye" />

#Panda를 사용하여 Azure blob 저장소 데이터에 대한 기능 만들기

이 문서는 [Panda](http://pandas.pydata.org/) Python 패키지를 사용하여 Azure Blob 컨테이너에 저장된 데이터에 대한 기능을 만드는 방법을 보여 줍니다. Panda 데이터 프레임에 데이터를 로드하는 방법을 설명한 후, 표시기 값과 범주화 기능과 함께 Python 스크립트를 사용하여 범주 기능을 생성하는 방법을 보여줍니다.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

이 **메뉴**는 다양한 환경에서 데이터에 대한 기능을 만드는 방법을 설명하는 토픽으로 연결되는 링크입니다. 이 작업은 [TDSP(팀 데이터 과학 프로세스)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)의 단계입니다.


## 필수 조건
이 문서에서는 Azure blob 저장소 계정을 만들고 여기에 데이터를 저장했다고 가정합니다. 계정을 설정하는 지침이 필요한 경우 [Azure 저장소 계정 만들기](../hdinsight-get-started.md#storage)를 참조하세요.


## Pandas 데이터 프레임에 데이터 로드
데이터 집합을 탐색 및 조작하려면 Blob 원본에서 로컬 파일로 다운로드한 다음 Pandas 데이터 프레임에 로드해야 합니다. 이 절차를 수행하는 단계는 다음과 같습니다.

1. blob 서비스를 사용하여 다음 샘플 Python 코드로 Azure blob에서 데이터를 다운로드합니다. 아래의 코드 변수를 사용자가 원하는 값으로 대체합니다.

	    from azure.storage.blob import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. 다운로드한 파일에서 데이터를 Pandas 데이터 프레임으로 읽습니다.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

이제 데이터를 탐색하고 이 데이터 집합에 기능을 생성할 준비가 완료되었습니다.
	
##<a name="blob-featuregen"></a>기능 생성
	
다음 두 섹션에는 Python 스크립트를 사용하여 표시기 값과 범주화 기능으로 범주 기능을 생성하는 방법을 보여줍니다.

###<a name="blob-countfeature"></a>표시기 값 기반 기능 생성

범주 기능은 다음과 같은 방법으로 만들 수 있습니다.

1. 범주 열의 분포를 검사합니다.
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. 각 열 값에 대한 표시기 값을 생성합니다.

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. 표시기 열을 원래 데이터 프레임에 조인합니다.
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. 원래 변수 자체를 제거합니다.

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>범주화 기능 생성

범주화된 기능을 생성하려면 다음 단계를 진행합니다.

1. 열 시퀀스를 추가하여 숫자 열을 범주화합니다.
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. 범주화를 부울 변수 시퀀스로 변환합니다.

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. 마지막으로 더미 변수를 다시 원래 데이터 프레임에 조인합니다.

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>다시 Azure blob에 데이터를 쓰고 Azure 기계 학습에서 데이터 사용

데이터를 탐색하고 필요한 기능을 만든 후에는 다음 단계에 따라 샘플링한 또는 기능화한 데이터를 Azure blob에 업로드하여 Azure 기계 학습에서 사용할 수 있습니다. Azure 기계 학습 스튜디오에서 추가 기능을 만들 수도 있습니다.
1. 로컬 파일에 데이터 프레임을 씁니다.

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 다음과 같이 Azure blob에 데이터를 업로드합니다.

		from azure.storage.blob import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 이제 아래 그림과 같이 Azure 기계 학습 [데이터 가져오기](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) 모듈을 사용하여 blob에서 데이터를 읽을 수 있습니다.
 
![판독기 blob](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0921_2016-->