<properties
	pageTitle="Azure Event Hubs 보관 연습 | Microsoft Azure"
	description="Azure Python SDK를 통해 Event Hubs 보관 기능을 사용하는 방법을 설명하는 예제입니다."
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Event Hubs 보관 연습: Python

Event Hubs 보관은 Event Hubs의 스트림 데이터를 선택한 Azure Blob Storage 계정에 자동으로 전달할 수 있도록 하는 이벤트 허브의 새로운 기능입니다. 이렇게 하면 손쉽게 실시간 스트리밍 데이터에서 일괄 처리를 수행할 수 있습니다. 이 문서에서는 Python과 함께 Event Hubs 보관을 사용하는 방법을 설명합니다. Event Hubs 보관에 대한 자세한 내용은 [개요 문서](event-hubs-archive-overview.md)를 참조하세요.

이 샘플에서는 Azure Python SDK를 사용하여 보관 기능 사용 방법을 보여 줍니다. sender.py가 JSON 형식으로 Event Hubs에 시뮬레이트된 환경 원격 분석을 보냅니다. 이벤트 허브는 보관 기능을 사용하여 Blob Storage에 일괄 작업으로 이 데이터를 작성하도록 구성됩니다. 그런 다음 archivereader.py가 이러한 Blob을 읽고 장치당 추가 파일을 만들어 .csv 파일에 데이터를 작성합니다.

수행될 작업

1.  Azure Portal을 사용하여 Azure Blob Storage 계정 및 Blob 컨테이너 만들기

2.  Azure Portal을 사용하여 이벤트 허브 네임스페이스 만들기

3.  Azure Portal을 사용하여 보관 기능이 활성화된 이벤트 허브 만들기

4.  Python 스크립트로 이벤트 허브에 데이터 보내기

5.  다른 Python 스크립트로 아카이브에서 파일 읽기 및 처리

필수 조건

1.  Python 2.7.x

2.  Azure 구독

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Azure 저장소 계정 만들기

1.  [Azure 포털][]에 로그온합니다.

2.  포털의 왼쪽 탐색 창에서 새로 만들기, 데이터 + 저장소, 저장소 계정을 차례로 클릭합니다.

3.  저장소 계정 블레이드에서 필드를 완성하고 **만들기**를 클릭합니다.

    ![][1]

4.  **배포 성공** 메시지가 나타나면 새 저장소 계정을 클릭하고 **필수** 블레이드에서 **Blob**을 클릭합니다. **Blob service** 블레이드가 열리면 맨 위의 **+ 컨테이너**를 클릭합니다. 컨테이너 이름을 **보관**으로 지정한 다음 **Blob service** 블레이드를 닫습니다.

5.  왼쪽 블레이드에서 **액세스 키**를 클릭하고 저장소 계정 이름과 **key1** 값을 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 저장합니다.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## 이벤트 허브로 이벤트를 보내는 Python 스크립트 만들기

1.  선호하는 Python 편집기(예: [Visual Studio Code][])를 엽니다.

2.  **sender.py**라는 스크립트 만들기 이 스크립트는 이벤트 허브에 200개의 이벤트를 전송합니다. 이 이벤트는 JSON 형식으로 전송한 간단한 환경 판독값입니다.

3.  다음 코드를 sender.py에 붙여넣습니다.

	```
	import uuid
	import datetime
	import random
	import json
	from azure.servicebus import ServiceBusService
	
	sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
	devices = []
	for x in range(0, 10):
	    devices.append(str(uuid.uuid4()))
	
	for y in range(0,20):
	    for dev in devices:
	        reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
	        s = json.dumps(reading)
	        sbs.send\_event('myhub', s)
	    print y
	```
4.  Event Hubs 네임스페이스를 만들 때 얻은 키 값과 네임스페이스 이름을 사용하도록 앞의 코드를 업데이트합니다.

## 보관 파일을 읽는 Python 스크립트 만들기

1.  블레이드를 채우고 **만들기**를 클릭합니다.

2.  **archivereader.py**라는 스크립트를 만듭니다. 이 스크립트는 보관 파일을 읽고 장치마다 파일을 만들어 해당 장치에만 데이터를 씁니다.

3.  archivereader.py에 다음 코드를 붙여넣습니다.

	```
    import os
	import string
	import json
	import avro.schema
	from avro.datafile import DataFileReader, DataFileWriter
	from avro.io import DatumReader, DatumWriter
	from azure.storage.blob import BlockBlobService
	
	def processBlob(filename):
	    reader = DataFileReader(open(filename, 'rb'), DatumReader())
	    dict = {}
	    for reading in reader:
	        parsed\_json = json.loads(reading["Body"])
	        if not 'id' in parsed\_json:
	            return
	        if not dict.has\_key(parsed\_json['id']):
	        list = []
	        dict[parsed\_json['id']] = list
	    else:
	        list = dict[parsed\_json['id']]
	        list.append(parsed\_json)
	    reader.close()
	    for device in dict.keys():
	        deviceFile = open(device + '.csv', "a")
	        for r in dict[device]:
	            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

	def startProcessing(accountName, key, container):
	    print 'Processor started using path: ' + os.getcwd()
	    block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
	    generator = block\_blob\_service.list\_blobs(container)
	    for blob in generator:
	        if blob.properties.content\_length != 0:
	            print('Downloaded a non empty blob: ' + blob.name)
	            cleanName = string.replace(blob.name, '/', '\_')
	            block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
	            processBlob(cleanName)
	            os.remove(cleanName)
	        block\_blob\_service.delete\_blob(container, blob.name)
	startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  호출의 저장소 계정 이름 및 키의 적절한 값을 `startProcessing`에 붙여넣습니다.

## 스크립트 실행

1.  해당 경로에 Python을 포함하는 명령 프롬프트를 열고 다음 명령을 실행하여 Python 필수 구성 요소 패키지를 설치합니다.

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    이전 버전의 Azure Storage나 Azure가 있는 경우 **--upgrade** 옵션을 사용해야 할 수 있습니다.

    또한 다음을 실행해야 할 수도 있습니다(대부분의 시스템에는 필요 없음).

    ```
    pip install cryptography
    ```

2.  sender.py 및 archivereader.py를 저장한 디렉터리로 변경하고 다음 명령을 실행합니다.

    ```
    start python sender.py
    ```
    
    이렇게 하면 발신자를 실행하는 새 Python 프로세스가 시작됩니다.

3. 이제 몇 분 정도 기다리면 보관이 실행됩니다. 그런 다음 원래 명령 창에 다음 명령을 입력합니다.

    ```
    python archivereader.py
    ```

이 보관 프로세서는 로컬 디렉터리를 사용하여 저장소 계정/컨테이너에서 모든 Blob을 다운로드합니다. 비어 있지 않은 모든 Blob을 처리하고 로컬 디렉터리에 .csv 파일로 결과를 작성합니다.

## 다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Event Hubs 보관 개요][]
- [이벤트 허브를 사용하는 샘플 응용 프로그램][] 전체.
- [이벤트 허브를 사용하는 이벤트 처리 확장][] 샘플
- [이벤트 허브 개요][]
 

[Azure 포털]: https://portal.azure.com/
[Event Hubs 보관 개요]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[이벤트 허브 개요]: event-hubs-overview.md
[이벤트 허브를 사용하는 샘플 응용 프로그램]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[이벤트 허브를 사용하는 이벤트 처리 확장]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->