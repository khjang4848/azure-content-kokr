**기본 데이터 레이크 분석 프로세스:**
	
![Azure 데이터 레이크 분석 프로세스 흐름 다이어그램](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)
	
1. 데이터 레이크 분석 계정을 만듭니다.
2. 원본 데이터를 준비합니다. 데이터 레이크 분석 작업은 Azure 데이터 레이크 저장소 계정 또는 Azure Blob 저장소 계정에서 데이터를 읽을 수 있습니다.
3. U-SQL 스크립트를 개발합니다.
4. 작업(U-SQL 스크립트)을 데이터 레이크 분석 계정에 제출합니다. 이 작업은 원본 데이터를 읽고 U-SQL 스크립트에서 지시한 대로 데이터를 처리한 다음 출력을 데이터 레이크 저장소 계정 또는 Blob 저장소 계정에 저장합니다.

<!---HONumber=AcomDC_0921_2016-->