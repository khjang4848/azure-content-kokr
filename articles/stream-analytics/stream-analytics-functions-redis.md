<properties
	pageTitle="Azure Functions를 사용하여 Azure 스트림 분석에서 Azure Redis Cache로 출력 | Microsoft Azure"
	description="서비스 버스 큐에 연결된 Azure 함수를 사용하여 스트림 분석 작업의 출력으로 Azure Redis Cache를 채우는 방법을 알아봅니다."
	keywords="데이터 스트림, Redis Cache, 서비스 버스 큐"
	services="stream-analytics"
	authors="ryancrawcour"
	manager="jhubbard"
    documentationCenter=""
	/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/09/2016"
	ms.author="ryancraw"/>

# Azure Functions를 사용하여 Azure Redis Cache에 Azure 스트림 분석의 데이터를 저장하는 방법

Azure 스트림 분석을 통해 장치, 센서, 인프라, 응용 프로그램 또는 데이터 스트림에서 실시간 정보를 얻는 저비용 분석 솔루션을 빠르게 개발하고 배포할 수 있습니다. 그뿐 아니라 실시간 관리 및 모니터링, 명령 및 컨트롤, 부정행위 감지, 연결된 자동차 등의 다양한 사용 사례를 허용합니다. 이러한 많은 시나리오에서 Azure 스트림 분석에 의해 출력된 데이터를 Azure Redis Cache와 같은 분산된 데이터 저장소에 저장할 수 있습니다.

통신 회사에 근무하고 있다고 가정해 보세요. 다중 호출이 동일한 ID에서 시작되지만, 동시에 지리적으로 다른 위치에서도 발생하는 SIM 부정 상황을 감지하려고 합니다. 잠재적인 모든 사기성 전화 통화를 Azure Redis Cache에 저장하는 업무를 맡게되었습니다. 이 블로그에 이러한 작업을 쉽게 완료하는 방법에 대한 지침이 제공됩니다.

## 필수 조건
ASA에 대한 [실시간 사기 감지][fraud-detection] 연습 완료

## 아키텍처 개요
![아키텍처 스크린샷](./media/stream-analytics-functions-redis/architecture-overview.png)

위 그림에 나와 있는 것처럼 스트림 분석을 사용하여 스트리밍 입력 데이터를 쿼리하고 출력으로 전송할 수 있습니다. 출력에 따라, Azure Functions에서 일부 형식의 이벤트를 트리거할 수 있습니다.

이 블로그에서는 이 파이프라인의 Azure Functions 부분을 좀 더 중점적으로 살펴보고 사기성 데이터를 캐시에 저장하는 이벤트 트리거에 대해서도 구체적으로 설명합니다. [실시간 사기 감지][fraud-detection] 자습서를 완료하면 입력(이벤트 허브), 쿼리 및 출력(Blob 저장소)이 구성되어 실행되고 있을 것입니다. 이 블로그에서는 대신 서비스 버스 큐를 사용하여 출력을 변경합니다. 그런 다음 이 큐에 Azure Function을 연결합니다.

## 서비스 버스 큐 출력 만들기 및 연결
서비스 버스 큐를 만들려면 [서비스 버스 큐 시작][servicebus-getstarted]의 .NET 섹션 1~2단계를 수행합니다. 이제 이전 사기 감지 연습에서 만든 스트림 분석 작업에 큐를 연결해 보겠습니다.



1. Azure 포털에서 작업의 **출력** 블레이드로 이동한 후 페이지 맨 위의 **추가**를 선택합니다.

	![출력 추가](./media/stream-analytics-functions-redis/adding-outputs.png)

2. **서비스 버스 큐**로 **싱크**를 선택하고 화면의 지시를 따릅니다. [서비스 버스 큐 시작][servicebus-getstarted]에서 만든 서비스 버스 큐의 네임스페이스를 선택해야 합니다. 작업이 완료되었으면 “오른쪽” 단추를 클릭합니다.
3. 다음 값을 지정합니다.
	- **이벤트 직렬 변환기 형식**: JSON
	- **인코딩**: UTF8
	- **형식**: 줄로 구분

4. **만들기** 단추를 클릭하여 이 소스를 추가하고 스트림 분석이 저장소 계정에 성공적으로 연결될 수 있는지 확인합니다.

5. **쿼리** 탭에서 현재 쿼리를 다음으로 바꿉니다. *[YOUR SERVICE BUS NAME]*을 3단계에서 만들 출력 이름으로 바꿉니다.

    ```    

	    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2

        INTO [YOUR SERVICE BUS NAME]
    
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
	    JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    
        WHERE CS1.SwitchNum != CS2.SwitchNum
    
    ```

## Azure Redis Cache 만들기
***캐시 클라이언트 구성*** 섹션이 나올 때까지 [Azure Redis Cache를 사용하는 방법][use-rediscache]의 .NET 섹션을 따라 Azure Redis Cache를 만듭니다. 완료되면 새 Redis Cache가 생성됩니다. **모든 설정** 아래에서 **액세스 키**를 선택하고 ***기본 연결 문자열***을 적어둡니다.

![아키텍처 스크린샷](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## Azure 함수 만들기
[첫 번째 Azure 함수 만들기][functions-getstarted] 자습서에 따라 Azure Functions를 시작합니다. 사용하려는 Azure 함수가 이미 있는 경우 [Redis Cache에 쓰기](#Writing-to-Redis-Cache)로 건너뜁니다.

1. 포털의 왼쪽 탐색 모음에서 앱 서비스를 선택하고 Azure 함수 앱 이름을 클릭하여 함수의 앱 웹 사이트로 이동합니다. ![앱 서비스 함수 목록 스크린샷](./media/stream-analytics-functions-redis/app-services-function-list.png)

2. **새 함수 > ServiceBusQueueTrigger – C#**을 클릭합니다. 다음 필드에 대해 다음 지침을 따릅니다.
	- **큐 이름**: [서비스 버스 큐 시작][servicebus-getstarted]에서 큐를 만들 때 입력한 이름과 같습니다(서비스 버스 이름은 아님). 스트림 분석 출력에 연결된 큐를 사용해야 합니다.
	- **서비스 버스 연결**: **연결 문자열 추가**를 선택합니다. 연결 문자열을 찾으려면 클래식 포털로 이동한 후 화면 아래쪽에 있는 **서비스 버스**, 만든 서비스 버스 및 **연결 정보**를 차례로 선택합니다. 이 페이지의 주 화면에 계속 있는지 확인합니다. 연결 문자열을 복사한 후 붙여 넣습니다. 연결 이름은 원하는 대로 입력해도 됩니다.
	
		![서비스 버스 연결 스크린샷](./media/stream-analytics-functions-redis/servicebus-connection.png)
	- **AccessRights**: **관리**를 선택합니다.


3. **만들기**를 클릭합니다.

## Redis Cache에 쓰기
이제 서비스 버스 큐에서 읽는 Azure 함수가 만들어졌습니다. 이제 이 함수를 사용하여 이 데이터를 Redis Cache에 쓰면 됩니다.

1. 새로 만든 **ServiceBusQueueTrigger**를 선택하고 오른쪽 위 구석에서 **함수 앱 설정**을 클릭합니다. **앱 서비스 설정으로 이동 > 설정 > 응용 프로그램 설정**을 선택합니다.

2. 연결 문자열 섹션의 **이름** 섹션에 이름을 만듭니다. **Redis Cache 만들기** 단계에서 찾은 기본 연결 문자열을 **값** 섹션에 붙여 넣습니다. **SQL 데이터베이스**라고 표시되는 **사용자 지정**을 선택합니다.

3. 위쪽에서 **저장**을 클릭합니다.

	![서비스 버스 연결 스크린샷](./media/stream-analytics-functions-redis/function-connection-string.png)

4. 이제 앱 서비스 설정으로 다시 이동한 후 **도구 > 앱 서비스 편집기(미리 보기) > 켜기 > 이동**을 선택합니다.

	![서비스 버스 연결 스크린샷](./media/stream-analytics-functions-redis/app-service-editor.png)

5. 선택한 편집기에서 다음을 사용하여 **project.json**이라는 JSON 파일을 만든 후 로컬 디스크에 저장합니다.

        {
            "frameworks": {
		        "net46": {
		            "dependencies": {
				        "StackExchange.Redis":"1.1.603",
				        "Newtonsoft.Json": "9.0.1"
			        }
		        }
	        }
        }

6. 함수의 루트 디렉터리(WWWROOT 아님)에 이 파일을 업로드합니다. **project.lock.json**이라는 파일이 자동으로 표시되면 "StackExchange.Redis" 및 "Newtonsoft.Json" Nuget 패키지를 가져왔는지 확인합니다.

7. **run.csx** 파일에서 미리 생성된 코드를 다음 코드로 바꿉니다. LazyConnection 함수에서 "CONN NAME"을 **Redis Cache에 데이터 저장**의 2단계에서 만든 이름으로 바꿉니다.

````

	using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;
    
    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");
    
        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
		new Lazy<ConnectionMultiplexer>(() =>
    		{
				var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
        		return ConnectionMultiplexer.Connect();
    		});
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## 스트림 분석 작업 시작

1. telcodatagen.exe 응용 프로그램을 시작합니다. 사용법은 다음과 같습니다. ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````

2. 포털의 스트림 분석 작업 블레이드에서 페이지 위쪽의 **시작**을 클릭합니다.

	![작업 시작 스크린샷](./media/stream-analytics-functions-redis/starting-job.png)

3. 나타나는 **작업 시작** 블레이드에서 **지금**을 클릭하고 화면 아래쪽에서 **시작** 단추를 클릭합니다. 작업 상태가 시작 중으로 변경되고, 잠시 후에는 실행 중으로 변경됩니다.
 
	![작업 시작 시간 선택 스크린샷](./media/stream-analytics-functions-redis/start-job-time.png)

## 솔루션 실행 및 결과 확인
**ServiceBusQueueTrigger** 페이지로 돌아가면 로그 설명이 표시됩니다. 이러한 로그는 서비스 버스 큐에서 일부 항목을 가져와 데이터베이스에 추가한 후 시간을 키로 사용해서 항목을 가져왔음을 보여 줍니다.

Redis cache에 해당 데이터가 있는지 확인하려면 새 포털의 Redis Cache 페이지로 이동한 후(앞의 [Azure Redis Cache 만들기](#Create-an-Azure-Redis-Cache) 단계 참조) 콘솔을 선택합니다.

이제 해당 데이터가 실제로 캐시에 있는지를 확인하는 Redis 명령을 작성할 수 있습니다.

![Redis 콘솔 스크린샷](./media/stream-analytics-functions-redis/redis-console.png)

## 다음 단계
Azure Functions 및 스트림 분석으로 수행할 수 있는 새로운 작업이 많이 있으며 이를 통해 새로운 가능성이 열릴 수 있기 바랍니다. 앞으로 원하는 기능에 대한 의견이 있는 경우 [Azure UserVoice 사이트](https://feedback.azure.com/forums/270577-stream-analytics)를 사용하세요.

Microsoft Azure를 처음 사용하는 경우 [무료 Azure 평가판 계정](https://azure.microsoft.com/pricing/free-trial/)을 등록하여 사용해볼 수 있습니다. 스트림 분석을 처음 사용하는 경우 [첫 번째 스트림 분석 작업을 만들어](stream-analytics-create-a-job.md) 볼 수 있습니다.

도움말이 필요하거나 질문이 있는 경우 [MSDN](https://social.msdn.microsoft.com/Forums/ko-KR/home?forum=AzureStreamAnalytics) 또는 [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) 포럼에 게시하세요.

다음 리소스를 참조할 수도 있습니다.

- [Azure Functions 개발자 참조](../azure-functions/functions-reference.md)
- [Azure Functions C# 개발자 참조](../azure-functions/functions-reference-csharp.md)
- [Azure Functions F# 개발자 참조](../azure-functions/functions-reference-fsharp.md)
- [Azure Functions NodeJS 개발자 참조](../azure-functions/functions-reference.md)
- [Azure Functions 트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md)
- [Azure Redis Cache를 모니터링하는 방법](../redis-cache/cache-how-to-monitor.md)

최신 소식과 기능에 대한 최신 동향을 파악하려면 Twitter의 [@AzureStreaming](https://twitter.com/AzureStreaming)을 팔로우하세요.


[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

<!---HONumber=AcomDC_0921_2016-->