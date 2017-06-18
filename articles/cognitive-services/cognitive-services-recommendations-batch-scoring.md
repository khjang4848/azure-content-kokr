
<properties
	pageTitle="배치의 권장 사항 가져오기: 기계 학습 권장 사항 API | Microsoft Azure"
	description="Azure 기계 학습 권장 사항--배치의 권장 사항 가져오기"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="luisca"/>

# 배치의 권장 사항 가져오기

>[AZURE.NOTE] 배치의 권장 사항을 가져오는 것은 권장 사항을 한 번에 하나씩 가져오는 것보다 더 복잡합니다. 단일 요청에 대해 권장 사항을 가져오는 방법에 대한 정보는 API를 확인하세요.

> [항목-항목 권장 사항](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [사용자-항목 권장 사항](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> 배치 점수 매기기는 2016년 7월 21일 이후에 생성된 빌드에 대해서만 작동합니다.


한 번에 둘 이상의 항목에 대한 권장 사항을 가져와야 하는 경우가 있습니다. 예를 들어 권장 사항 캐시 만들기 또는 가져오는 권장 사항의 형식에 대한 분석에 관심이 있을 수 있습니다.

호출하므로 배치 점수 매기기 작업은 비동기 작업입니다. 요청을 제출하고 작업이 완료될 때까지 기다린 다음 결과를 수집해야 합니다.

보다 정확하게 하기 위해 다음 단계를 수행합니다.

1.	아직 없는 경우 Azure Storage 컨테이너를 만듭니다.
2.	Azure Blob 저장소에 대한 각 권장 사항 요청을 설명하는 입력 파일을 업로드합니다.
3.	점수 매기기 배치 작업을 시작합니다.
4.	비동기 작업이 완료될 때까지 기다립니다.
5.	작업이 완료되면 Blob 저장소에서 결과 수집합니다.

이러한 각 단계를 살펴보겠습니다.

## 아직 없는 경우 저장소 컨테이너 만들기

[Azure 포털](https://portal.azure.com)로 이동하고 아직 없는 경우 새 저장소 계정을 만듭니다. 이 작업을 수행하려면 **새로 만들기** > **데이터** + **저장소** > **저장소 계정**으로 이동합니다.

저장소 계정을 가진 후 배치 실행의 입력 및 출력을 저장하는 Blob 컨테이너를 만들어야 합니다.

Blob 저장소에 대한 각 권장 사항 요청을 설명하는 입력 파일을 업로드합니다. 여기에서는 input.json 파일입니다. 컨테이너를 만든 후 권장 사항 서비스에서 수행해야 하는 각 요청을 설명하는 파일을 업로드해야 합니다.

배치는 특정 빌드에서 한 가지 유형의 요청만을 수행할 수 있습니다. 다음 섹션에서 이 정보를 정의하는 방법을 설명합니다. 현재로서는 특정 빌드 중에서 항목 권장 사항을 수행하는 것으로 가정합니다. 입력 파일은 각 요청에 대한 입력 정보를 포함합니다(이 경우 시드 항목).

input.json 파일이 어떤 모습인지 보여 주는 예제입니다.

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

여기에서 볼 수 있듯이 파일은 각 요청이 권장 사항 요청을 보내는 데 필요한 정보를 갖는 JSON 파일입니다. 수행해야 하는 요청에 대해 비슷한 JSON 파일을 만들고 Blob 저장소에서 방금 만든 컨테이너에 복사합니다.

## 배치 작업 시작

다음 단계는 새 배치 작업을 제출하는 것입니다. 자세한 내용은 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)를 확인하세요.

API의 요청 본문은 입력, 출력 및 오류 파일이 저장되어야 하는 위치를 정의해야 합니다. 해당 위치에 액세스하는 데 필요한 자격 증명도 정의해야 합니다. 또한 전체 배치(요청에 대한 권장 사항 형식, 사용할 모델/빌드, 호출당 결과 수 등)에 적용하는 일부 매개 변수를 지정해야 합니다.

요청 본문이 어떤 모습인지 보여 주는 예제입니다.

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

유의해야 할 두 가지 중요한 사항:

-	현재 **authenticationType**은 항상 **PublicOrSas**로 설정되어야 합니다.

-	권장 사항 API를 Blob 저장소 계정 간 읽기 및 쓰기를 허용하도록 SAS(공유 액세스 서명) 토큰을 가져와야 합니다. SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [권장 사항 API 페이지](../storage/storage-dotnet-shared-access-signature-part-1.md)에서 확인할 수 있습니다.

-	항목-항목 권장 사항에 사용되는 현재 지원되는 유일한 **apiName**은 **ItemRecommend**입니다. 일괄 처리가 현재 사용자-항목 권장 사항을 지원하지 않습니다.

## 비동기 작업이 완료될 때까지 기다립니다.

배치 작업을 시작하면 응답은 작업을 추적하는 데 필요한 정보를 제공하는 작업 위치 헤더를 반환합니다. 빌드 작업의 작업 추적에 대해 수행하는 것과 마찬가지로 [작업 상태 API 검색](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da)을 사용하여 작업을 추적합니다.

## 결과 가져오기

작업이 완료된 후 오류가 없었던 것으로 가정하면 출력 Blob 저장소에서 결과를 수집할 수 있습니다.

아래 예제에서는 출력이 어떤 모습인지 보여 줍니다. 이 예제에서는 간단한 설명을 위해 두 가지 요청만으로 배치에 대한 결과를 보여 줍니다.

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## 제한 사항에 알아보기

-	한 번에 구독당 하나의 배치 작업만을 호출할 수 있습니다.
-	배치 작업 입력 파일은 2MB 이상일 수 없습니다.

<!---HONumber=AcomDC_0914_2016-->