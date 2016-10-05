<properties 
	pageTitle="고급 분석을 사용하여 Azure blob 데이터 처리 | Microsoft Azure" 
	description="Azure Blob 저장소에서 데이터 처리" 
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
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>고급 분석을 사용하여 Azure blob 데이터 처리

이 문서에서는 Azure Blob 저장소에 저장된 데이터를 탐색하고 기능을 생성하는 방법을 다룹니다.

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


##<a name="blob-dataexploration"></a>데이터 탐색

다음은 Pandas를 사용하여 데이터를 탐색하는 방법의 예입니다.

1. 행 및 열 수를 검사합니다.

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. 아래와 같이 데이터 집합의 처음 또는 마지막 몇 행을 검사합니다.

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. 다음 샘플 코드를 사용하여 각 열을 가져온 데이터 유형을 확인합니다.
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. 다음과 같이 데이터 집합의 열에 대한 기본 통계를 확인합니다.
 
		dataframe_blobdata.describe()
	
5. 다음과 같이 각 열 값에 대한 항목 수를 확인합니다.

		dataframe_blobdata['<column_name>'].value_counts()

6. 다음 샘플 코드를 사용하여 각 열의 실제 항목 수와 누락된 값을 계산합니다.

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	데이터의 특정 열에 대한 값이 누락된 경우 다음과 같이 해당 데이터를 삭제할 수 있습니다.

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	누락된 값을 대체하는 또 다른 방법으로 mode 함수가 있습니다.
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. 가변 bin을 사용하여 히스토그램 플롯을 만들고 변수 분포 그리기
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. 산점도 또는 기본 제공 상관관계 함수를 사용하여 변수 간의 상관관계를 살펴봅니다.

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()
	
	
##<a name="blob-featuregen"></a>기능 생성
	
다음과 같이 Python을 사용하여 기능을 생성할 수 있습니다.

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

3. 이제 아래 그림과 같이 Azure 기계 학습 [데이터 가져오기][import-data] 모듈을 사용하여 blob에서 데이터를 읽을 수 있습니다.
 
![판독기 blob][1]

[1]: ./media/machine-learning-data-science-process-data-blob/reader_blob.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0921_2016-->