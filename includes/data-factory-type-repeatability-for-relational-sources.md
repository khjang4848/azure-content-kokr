## 복사 중 반복성

관계형 저장소로부터 또는 관계형 저장소에 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다.

조각은 지정된 재시도 정책에 따라 Azure Data Factory에서 자동으로 다시 실행할 수 있습니다. 일시적인 오류를 방지할 수 있게 재시도 정책을 설정하는 것이 좋습니다. 이 때문에 반복성은 데이터 이동 중에 유념해 두어야 할 중요한 부분입니다.

**원본으로:**

> [AZURE.NOTE] 다음 예제는 Azure SQL에 대한 것이지만, 직사각 데이터 집합을 지원하는 모든 데이터 저장소에 적용할 수 있습니다. 데이터 저장소에 대해 소스의 **type** 및 **query** 속성(예: sqlReaderQuery 대신 query)을 조정해야 할 수도 있습니다.

일반적으로 관계형 저장소에서 읽어올 때는 해당 조각에 대한 데이터만 읽고자 할 것입니다. 이것은 Azure 데이터 팩터리에서 제공하는 WindowStart 및 WindowEnd 변수를 사용하면 됩니다. [예약 및 실행](../articles/data-factory/data-factory-scheduling-and-execution.md) 문서의 Azure 데이터 팩터리 부분에서 변수 및 함수에 대해 확인해 보세요. 예제:
	
	  "source": {
	    "type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
	  },

이 쿼리에서는 조각 기간 범위 안에 해당하는 'MyTable'에서 데이터를 읽을 것입니다. 이 조각을 다시 실행하면 항상 해당 동작을 확인하게 됩니다.

다른 경우 전체 테이블을 읽고(단 한 번의 이동이라고 가정할 때) 다음과 같이 sqlReaderQuery를 정의하고자 할 수 있습니다.

	
	"source": {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	          },
	

<!---HONumber=AcomDC_0914_2016-->