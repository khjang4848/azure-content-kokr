<properties 
	pageTitle=".NET을 사용하여 미디어 인코더 표준으로 자산 인코딩 | Microsoft Azure" 
	description="이 항목에서는 .NET을 사용하여 미디어 인코더 표준으로 자산을 인코딩하는 방법을 설명합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="09/19/2016"
	ms.author="juliako;anilmur"/>


# .NET을 사용하여 미디어 인코더 표준으로 자산 인코딩

인코딩 작업은 미디어 서비스에서 가장 일반적인 처리 작업 중 하나입니다. 인코딩 작업을 만들어 한 인코딩에서 다른 인코딩으로 미디어 파일을 변환합니다. 인코딩할 때는 미디어 서비스 기본 제공 미디어 인코더를 사용할 수 있습니다. 또한 미디어 서비스 파트너가 제공하는 인코더를 사용할 수도 있습니다. 타사 인코더는 Azure 마켓플레이스를 통해 사용할 수 있습니다.

이 항목에서는 .NET을 사용하여 MES(미디어 인코더 표준)로 자산을 인코딩하는 방법을 설명합니다. 미디어 인코더 표준은 [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

항상 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 인코딩한 다음 [동적 패키징](media-services-dynamic-packaging-overview.md)을 사용하여 원하는 형식으로 집합을 변환하는 것이 좋습니다. 동적 패키징을 이용하려면 먼저 콘텐츠를 배달할 계획인 스트리밍 끝점에 대한 주문형 스트리밍 단위를 하나 이상 가져와야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.

출력 자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조하세요.

>[AZURE.NOTE]MES는 입력 파일 이름의 처음 32개 문자를 포함하는 이름을 가진 출력 파일을 생성합니다. 이름은 미리 설정된 파일에 지정된 내용에 기반합니다. 예를 들어 "FileName": "{Basename}\_{Index}{Extension}"의 경우 {Basename}은 입력 파일 이름의 처음 32자로 대체됩니다.

###MES 형식

[형식 및 코덱](media-services-media-encoder-standard-formats.md)

###MES 기본 설정

미디어 인코더 표준은 [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)에서 설명한 인코더 기본 설정 중 하나를 사용하여 구성됩니다.

###입력 및 출력 메타데이터

MES를 사용하여 입력 자산을 인코딩하는 경우 인코딩 작업이 성공적으로 완료되면 출력 자산을 얻게 됩니다. 출력 자산에는 사용하는 인코딩 기본 설정에 따라 비디오, 오디오, 미리 보기, 매니페스트 등이 포함됩니다.

출력 자산에는 입력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타데이터 XML 파일의 이름 형식은 다음과 같습니다. <asset\_id>\_metadata.xml(예: 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml). 여기서 <asset\_id>는 입력 자산의 AssetId 값입니다. 이 입력 메타데이터 XML의 스키마는 [여기](http://msdn.microsoft.com/library/azure/dn783120.aspx)에 설명됩니다.

출력 자산에는 출력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타데이터 XML 파일의 이름은 <source_file_name>\_manifest.xml 형식입니다(예: BigBuckBunny\_manifest.xml). 이 출력 메타데이터 XML의 스키마는 [여기](http://msdn.microsoft.com/library/azure/dn783217.aspx)에 설명됩니다.

두 메타데이터 파일 중 하나를 검사하려는 경우 SAS 로케이터를 만들고 로컬 컴퓨터에 파일을 다운로드할 수 있습니다. SAS 로케이터를 만들고 미디어 서비스 .NET SDK 확장을 사용하여 파일을 다운로드하는 방법에 대한 예제를 찾을 수 있습니다.

##샘플 다운로드

[여기](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)에서 샘플을 가져와서 실행합니다.

##예

다음 코드 예제에서는 미디어 서비스 .NET SDK를 사용하여 다음 작업을 수행합니다.

- 인코딩 작업을 만듭니다.
- 미디어 인코더 표준 인코더에 대한 참조를 가져옵니다.
- "H264 여러 비트 전송률 720p" 기본 설정을 사용하도록 지정합니다. [여기](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)서 모든 기본 설정을 확인할 수 있습니다. [이 항목](https://msdn.microsoft.com/library/mt269962.aspx)에서 이러한 사전 설정이 따라야 하는 스키마를 살펴볼 수도 있습니다.
- 작업에 단일 인코딩을 추가합니다.
- 인코딩할 입력 자산을 지정합니다.
- 인코딩된 자산을 포함할 출력 자산을 만듭니다.
- 작업 진행 상태를 확인할 이벤트 처리기를 추가합니다.
- 작업을 제출합니다.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Create a task with the encoding details, using a string preset.
		    // In this case "H264 Multiple Bitrate 720p" preset is used.
		    ITask task = job.Tasks.AddNew("My encoding task",
		        processor,
		        "H264 Multiple Bitrate 720p",
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
		    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		    job.Submit();
		    job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		    return job.OutputMediaAssets[0];
		}
		
		private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
		{
		    Console.WriteLine("Job state changed event:");
		    Console.WriteLine("  Previous state: " + e.PreviousState);
		    Console.WriteLine("  Current state: " + e.CurrentState);
		    switch (e.CurrentState)
		    {
		        case JobState.Finished:
		            Console.WriteLine();
		            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
		            break;
		        case JobState.Canceling:
		        case JobState.Queued:
		        case JobState.Scheduled:
		        case JobState.Processing:
		            Console.WriteLine("Please wait...\n");
		            break;
		        case JobState.Canceled:
		        case JobState.Error:
		
		            // Cast sender as a job.
		            IJob job = (IJob)sender;
		
		            // Display or log error details as needed.
		            break;
		        default:
		            break;
		    }
		}
		
		
		private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
		{
		    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		    if (processor == null)
		        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		    return processor;
		}


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##참고 항목 

[.NET과 함께 미디어 인코더 표준을 사용하여 미리 보기를 생성하는 방법](media-services-dotnet-generate-thumbnail-with-mes.md) [미디어 서비스 인코딩 개요](media-services-encode-asset.md)

<!---HONumber=AcomDC_0921_2016-->