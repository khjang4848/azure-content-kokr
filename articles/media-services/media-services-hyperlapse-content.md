<properties
	pageTitle="미디어 파일 및 Azure 미디어 Hyperlapse | Microsoft Azure"
	description="Azure 미디어 Hyperlapse는 1인칭 또는 액션 카메라 콘텐츠에서 부드러운 시간 경과 비디오를 만듭니다. 이 항목에서는 미디어 인덱서를 사용하는 방법을 보여 줍니다."
	services="media-services"
	documentationCenter=""
	authors="asolanki"
	manager="johndeu"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/19/2016"  
	ms.author="adsolank"/>


# Hyperlapse 미디어 파일 및 Azure 미디어 Hyperlapse

Azure 미디어 Hyperlapse는 1인칭 또는 액션 카메라 콘텐츠에서 부드러운 시간 경과 비디오를 만드는 미디어 프로세서(MP)입니다. [Microsoft Research의 데스크톱 Hyperlapse Pro 및 전화 기반 Hyperlapse 모바일](http://aka.ms/hyperlapse)에 대한 클라우드 기반 형제 제품인 Azure 미디어 서비스용 Microsoft Hyperlapse는 Azure 미디어 서비스 미디어 처리 플랫폼의 상당 부분을 활용하여 대량의 Hyperlapse 처리를 수평적으로 확장하고 병렬 처리합니다.

>[AZURE.IMPORTANT]Microsoft Hyperlapse는 이동 카메라를 사용한 1인칭 콘텐츠에 최적으로 작동하도록 설계되었습니다. 스틸 카메라 영상에도 작동할 수 있지만 다른 형식의 콘텐츠에서는 Azure 미디어 Hyperlapse 미디어 프로세서의 성능 및 품질을 보장할 수 없습니다. Azure 미디어 서비스용 Microsoft Hyperlapse에 대해 자세히 알아보고 예제 비디오를 보려면 공개 미리 보기 상태인 [소개 블로그 게시물](http://aka.ms/azurehyperlapseblog)을 확인하세요.

Azure 미디어 Hyperlapse 작업은 MP4, MOV 또는 WMV 자산 파일 및 어떤 비디오 프레임을 어떤 속도로 시간 경과시킬지 지정(예: 2배속으로 처음 10,000프레임)하는 구성 파일을 입력으로 사용합니다. 출력은 입력 비디오의 안정화되고 시간 경과된 표현입니다.

최신 Azure 미디어 Hyperlapse 업데이트는 [미디어 서비스 블로그](https://azure.microsoft.com/blog/topics/media-services/)(영문)를 참조하세요.

## 자산 Hyperlapse

먼저 원하는 입력 파일을 Azure 미디어 서비스로 업로드해야 합니다. 콘텐츠 업로드 및 관리와 관련된 개념에 대해 자세히 알아보려면 [콘텐츠 관리 문서](media-services-portal-vod-get-started.md)를 읽어보세요.

###  <a id="configuration"></a>Hyperlapse에 대한 구성 사전 설정

콘텐츠가 미디어 서비스 계정에 있는 경우 구성 사전 설정을 생성해야 합니다. 다음 표에서는 사용자 지정 필드에 대해 설명합니다.

 필드 | 설명
-------|-------------
StartFrame|Microsoft Hyperlapse 처리를 시작할 프레임입니다.
NumFrames|처리할 프레임 수
속도|입력 비디오를 가속화하는 요소입니다.

다음은 XML 및 JSON에서 규칙을 따르는 구성 파일의 예입니다.

**XML 사전 설정:**

	<?xml version="1.0" encoding="utf-16"?>
	<Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
		<Sources>
			<Source StartFrame="0" NumFrames="10000" />
		</Sources>
		<Options>
			<Speed>12</Speed>
		</Options>
	</Preset>

**JSON 사전 설정:**

	{
		"Version":1.0,
		"Sources": [
			{
				"StartFrame":0,
				"NumFrames":2147483647
			}
		],
		"Options": {
			"Speed":1,
			"Stabilize":false
		}
	}

###  <a id="sample_code"></a> Microsoft Hyperlapse 및 AMS .NET SDK

다음 메서드는 미디어 파일을 자산으로 업로드하고 Azure 미디어 Hyperlapse 미디어 프로세서로 작업을 만듭니다.

> [AZURE.NOTE] 이 코드가 작동하도록 하려면 "context" 이름의 범위에 CloudMediaContext가 이미 있어야 합니다. 이에 대한 자세히 알아보려면 [콘텐츠 관리 문서](media-services-dotnet-get-started.md)를 읽어보세요.

> [AZURE.NOTE] 문자열 인수 "hyperConfig"가 위에 설명된 대로 JSON 또는 XML에서 규칙을 따르는 구성 사전 설정이어야 합니다.

static bool RunHyperlapseJob(string input, string output, string hyperConfig) { // create asset with input file IAsset asset = context .Assets .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

// grab instances of Azure Media Hyperlapse MP IMediaProcessor mp = context .MediaProcessors .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

// create Job with Hyperlapse task IJob job = context .Jobs .Create(String.Format("Hyperlapse {0}", input));

if (String.IsNullOrEmpty(hyperConfig)) { // config cannot be empty return false; }

hyperConfig = File.ReadAllText(hyperConfig);

ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task", mp, hyperConfig, TaskOptions.None); hyperlapseTask.InputAssets.Add(asset); hyperlapseTask.OutputAssets.AddNew("Hyperlapse output", AssetCreationOptions.None);


job.Submit();

// Create progress printing and querying tasks Task progressPrintTask = new Task(() => {

IJob jobQuery = null; do { var progressContext = context; jobQuery = progressContext.Jobs .Where(j => j.Id == job.Id) .First(); Console.WriteLine(string.Format("{0}\\t{1}\\t{2}", DateTime.Now, jobQuery.State, jobQuery.Tasks[0].Progress)); Thread.Sleep(10000); } while (jobQuery.State != JobState.Finished && jobQuery.State != JobState.Error && jobQuery.State != JobState.Canceled); }); progressPrintTask.Start();

			Task progressJobTask = job.GetExecutionProgressTask(
												 CancellationToken.None);
			progressJobTask.Wait();

			// If job state is Error, the event handling
			// method for job progress should log errors.  Here we check
			// for error state and exit if needed.
			if (job.State == JobState.Error)
			{
				ErrorDetail error = job.Tasks.First().ErrorDetails.First();
				Console.WriteLine(string.Format("Error: {0}. {1}",
												error.Code,
												error.Message));  
				return false;                  
			}

		DownloadAsset(job.OutputMediaAssets.First(), output);
		return true;
	}

	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
		foreach (IAssetFile file in asset.AssetFiles)
		{
			file.Download(Path.Combine(outputDirectory, file.Name));
		}
	}


	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = context.Assets.Create(assetName, options);

	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);

	    return asset;
	}

	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();

	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));

	    return processor;
	}

### <a id="file_types"></a>지원되는 파일 형식

- MP4
- MOV
- WMV



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##관련 링크

[Azure 미디어 서비스 분석 개요](media-services-analytics-overview.md)

[Azure 미디어 분석 데모](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0921_2016-->