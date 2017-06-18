<properties 
	pageTitle="Azure Media Services를 사용하여 라이브 스트리밍을 수행하여 .NET으로 다중 비트 전송률 스트림을 만드는 방법 | Microsoft Azure" 
	description="이 자습서에서는 .NET SDK를 사용하여 단일 비트 전송률 라이브 스트림을 받아서 다중 비트 전송률 스트림으로 인코딩하는 채널을 만드는 단계를 안내합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="anilmur" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article"
	ms.date="09/15/2016"
	ms.author="juliako;anilmur"/>


#Azure 미디어 서비스를 사용하여 라이브 스트리밍을 수행하여 .NET으로 다중 비트 스트림을 만드는 방법

> [AZURE.SELECTOR]
- [포털](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

>[AZURE.NOTE]
이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](/pricing/free-trial/?WT.mc_id=A261C142F)을 참조하세요.

##개요

이 자습서에서는 단일 비트 전송률 라이브 스트림을 받아서 다중 비트 전송률 스트림으로 인코딩하는 **채널**을 만드는 단계를 안내합니다.

라이브 인코딩에 대해 사용할 수 있는 채널과 관련하여 더욱 개념적인 정보는 [Azure 미디어 서비스를 사용하여 다중 비트 전송률 스트림을 만드는 라이브 스트리밍](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.


##일반적인 라이브 스트리밍 시나리오

다음 단계에서는 일반적인 라이브 스트리밍 응용 프로그램을 만드는 데 포함되는 작업을 설명합니다.

>[AZURE.NOTE] 현재 라이브 이벤트의 최대 권장 기간은 8시간입니다. 더 오랜 시간 채널을 실행해야 하는 경우 amslived@microsoft.com으로 문의하세요.

1. 비디오 카메라를 컴퓨터에 연결합니다. RTMP, 부드러운 스트리밍 또는 RTP(MPEG-TS) 프로토콜 중 하나에서 단일 비트 전송률 스트림을 출력할 수 있는 온-프레미스 라이브 인코더를 시작하고 구성합니다. 자세한 내용은 [Azure 미디어 서비스 RTMP 지원 및 라이브 인코더](http://go.microsoft.com/fwlink/?LinkId=532824)를 참조하세요.

	이 단계는 채널을 만든 후에도 수행할 수 있습니다.

1. 채널을 만들고 시작합니다.

1. 채널 수집 URL을 검색합니다.

	수집 URL은 스트림을 채널로 보내기 위해 라이브 인코더를 통해 사용됩니다.

1. 채널 미리 보기 URL을 검색합니다.

	이 URL을 사용하여 채널이 라이브 스트림을 제대로 받고 있는지 확인합니다.

2. 자산을 만듭니다.
3. 재생하는 동안 자산을 동적으로 암호화하려면 다음을 수행합니다.
1. 콘텐츠 키를 만듭니다.
1. 콘텐츠 키의 인증 정책을 구성합니다.
1. 자산 배달 정책(동적 패키징 및 동적 암호화에서 사용)을 구성합니다.
3. 프로그램을 만들고 만들어진 자산을 사용하도록 지정합니다.
1. 주문형 로케이터를 만들어 프로그램과 관련된 자산을 게시합니다.

	콘텐츠를 스트림하려는 스트리밍 끝점에서 최소 1개의 스트리밍 예약 단위가 있어야 합니다.

1. 스트리밍 및 보관을 시작할 준비가 되었으면 프로그램을 시작합니다.
2. 필요에 따라 라이브 인코더는 광고를 시작하라는 신호를 받을 수 있습니다. 광고는 출력 스트림에 삽입됩니다.
1. 이벤트 스트리밍 및 보관을 중지할 때마다 프로그램을 중지 합니다.
1. 프로그램을 삭제하고 필요에 따라 자산을 삭제합니다.

## 학습할 내용

이 항목에서는 Media Services .NET SDK를 사용하여 채널과 프로그램에 대한 다양한 작업을 실행하는 방법을 보여 줍니다. 많은 작업이 오래 실행되기 때문에 오래 실행되는 작업을 관리하는 .NET API가 사용됩니다.

이 항목에서는 다음을 수행하는 방법을 보여 줍니다.

1. 채널을 만들고 시작합니다. 오래 실행되는 API가 사용됩니다.
1. 채널 수집(입력) 끝점을 가져옵니다. 이 끝점은 단일 비트 전송률 라이브 스트림을 보낼 수 있는 인코더에 제공되어야 합니다.
1. 미리 보기 끝점을 가져옵니다. 이 끝점은 스트림을 미리 보는 데 사용됩니다.
1. 콘텐츠를 저장하는 데 사용할 자산을 만듭니다. 자산 배달 정책도 이 예와 같이 구성해야 합니다.
1. 프로그램을 만들고 이전에 만든 자산을 사용하도록 지정합니다. 프로그램을 시작합니다. 오래 실행되는 API가 사용됩니다.
1. 자산의 로케이터를 만들어 콘텐츠가 게시되고 클라이언트로 스트리밍될 수 있도록 합니다.
1. 슬레이트를 표시하고 숨깁니다. 광고를 시작하고 중지합니다. 오래 실행되는 API가 사용됩니다.
1. 채널과 모든 연결된 리소스를 정리합니다.


##필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

- 이 자습서를 완료하려면 Azure 계정이 필요합니다.
	
	계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](/pricing/free-trial/?WT.mc_id=A261C142F)을 참조하세요. 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 됩니다. 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스 및 기능(예: Azure 앱 서비스의 웹앱 기능)을 사용할 수 있습니다.
- 미디어 서비스 계정. 미디어 서비스 계정을 만들려면 [계정 만들기](media-services-create-account.md)를 참조하세요.
- Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express) 이상 버전.
- 미디어 서비스 .NET SDK 버전 3.2.0.0 이상을 사용해야 합니다.
- 단일 비트 전송률 라이브 스트림을 보낼 수 있는 웹캠 및 인코더.

##고려 사항

- 현재 라이브 이벤트의 최대 권장 기간은 8시간입니다. 더 오랜 시간 채널을 실행해야 하는 경우 amslived@microsoft.com으로 문의하세요.
- 콘텐츠를 스트림하려는 스트리밍 끝점에서 최소 1개의 스트리밍 예약 단위가 있어야 합니다.

##샘플 다운로드

[여기](https://azure.microsoft.com/documentation/samples/media-services-dotnet-encode-live-stream-with-ams-clear/)에서 샘플을 가져와서 실행합니다.


##.NET용 미디어 서비스 SDK를 사용한 개발을 위한 설정

1. Visual Studio를 사용하여 콘솔 응용 프로그램을 만듭니다.
1. 미디어 서비스 NuGet 패키지를 사용하여 .NET용 미디어 서비스 SDK를 콘솔 응용 프로그램에 추가합니다.

##미디어 서비스에 연결
app.config 파일을 사용하여 미디어 서비스 이름 및 계정 키를 저장하는 것이 좋습니다.

>[AZURE.NOTE]이름 및 키 값을 찾으려면 Azure 클래식 포털로 이동하여 미디어 서비스 계정을 선택하고 포털 창의 하단에 있는 "키 관리" 아이콘을 클릭합니다. 각 텍스트 상자 옆에 있는 아이콘을 클릭하면 값을 시스템 클립보드에 복사합니다.

app.config 파일에 appSettings 섹션을 추가하고 미디어 서비스 계정 이름 및 계정 키 값을 설정합니다.


	<?xml version="1.0"?>
	<configuration>
	  <appSettings>
	      <add key="MediaServicesAccountName" value="YouMediaServicesAccountName" />
	      <add key="MediaServicesAccountKey" value="YouMediaServicesAccountKey" />
	  </appSettings>
	</configuration>
	 
	
##코드 예제

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Net;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace EncodeLiveStreamWithAmsClear
	{
	    class Program
	    {
	        private const string ChannelName = "channel001";
	        private const string AssetlName = "asset001";
	        private const string ProgramlName = "program001";
	
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            IChannel channel = CreateAndStartChannel();
	
	            // The channel's input endpoint:
	            string ingestUrl = channel.Input.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Intest URL: {0}", ingestUrl);
	
	
	            // Use the previewEndpoint to preview and verify 
	            // that the input from the encoder is actually reaching the Channel. 
	            string previewEndpoint = channel.Preview.Endpoints.FirstOrDefault().Url.ToString();
	
	            Console.WriteLine("Preview URL: {0}", previewEndpoint);
	
	            // When Live Encoding is enabled, you can now get a preview of the live feed as it reaches the Channel. 
	            // This can be a valuable tool to check whether your live feed is actually reaching the Channel. 
	            // The thumbnail is exposed via the same end-point as the Channel Preview URL.
	            string thumbnailUri = new UriBuilder
	            {
	                Scheme = Uri.UriSchemeHttps,
	                Host = channel.Preview.Endpoints.FirstOrDefault().Url.Host,
	                Path = "thumbnails/input.jpg"
	            }.Uri.ToString();
	
	            Console.WriteLine("Thumbain URL: {0}", thumbnailUri);
	
	            // Once you previewed your stream and verified that it is flowing into your Channel, 
	            // you can create an event by creating an Asset, Program, and Streaming Locator. 
	            IAsset asset = CreateAndConfigureAsset();
	
	            IProgram program = CreateAndStartProgram(channel, asset);
	
	            ILocator locator = CreateLocatorForAsset(program.Asset, program.ArchiveWindowLength);
	
	            // You can use slates and ads only if the channel type is Standard.  
	            StartStopAdsSlates(channel);
	
	            // Once you are done streaming, clean up your resources.
	            Cleanup(channel);
	
	        }
	
	        public static IChannel CreateAndStartChannel()
	        {
	            var channelInput = CreateChannelInput();
	            var channePreview = CreateChannelPreview();
	            var channelEncoding = CreateChannelEncoding();
	
	
	            ChannelCreationOptions options = new ChannelCreationOptions
	            {
	                EncodingType = ChannelEncodingType.Standard,
	                Name = ChannelName,
	                Input = channelInput,
	                Preview = channePreview,
	                Encoding = channelEncoding
	            };
	
	            Log("Creating channel");
	            IOperation channelCreateOperation = _context.Channels.SendCreateOperation(options);
	            string channelId = TrackOperation(channelCreateOperation, "Channel create");
	
	            IChannel channel = _context.Channels.Where(c => c.Id == channelId).FirstOrDefault();
	
	            Log("Starting channel");
	            var channelStartOperation = channel.SendStartOperation();
	            TrackOperation(channelStartOperation, "Channel start");
	
	            return channel;
	        }
	
	        /// <summary>
	        /// Create channel input, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelInput CreateChannelInput()
	        {
	            return new ChannelInput
	            {
	                StreamingProtocol = StreamingProtocol.RTPMPEG2TS,
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelInput001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel preview, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelPreview CreateChannelPreview()
	        {
	            return new ChannelPreview
	            {
	                AccessControl = new ChannelAccessControl
	                {
	                    IPAllowList = new List<IPRange>
	                    {
	                        new IPRange
	                        {
	                            Name = "TestChannelPreview001",
	                            Address = IPAddress.Parse("0.0.0.0"),
	                            SubnetPrefixLength = 0
	                        }
	                    }
	                }
	            };
	        }
	
	        /// <summary>
	        /// Create channel encoding, used in channel creation options. 
	        /// </summary>
	        /// <returns></returns>
	        private static ChannelEncoding CreateChannelEncoding()
	        {
	            return new ChannelEncoding
	            {
	                SystemPreset = "Default720p",
	                IgnoreCea708ClosedCaptions = false,
	                AdMarkerSource = AdMarkerSource.Api,
	                // You can only set audio if streaming protocol is set to StreamingProtocol.RTPMPEG2TS.
	                AudioStreams = new List<AudioStream> { new AudioStream { Index = 103, Language = "eng" } }.AsReadOnly()
	            };
	        }
	
	        /// <summary>
	        /// Create an asset and configure asset delivery policies.
	        /// </summary>
	        /// <returns></returns>
	        public static IAsset CreateAndConfigureAsset()
	        {
	            IAsset asset = _context.Assets.Create(AssetlName, AssetCreationOptions.None);
	
	            IAssetDeliveryPolicy policy =
	                _context.AssetDeliveryPolicies.Create("Clear Policy",
	                AssetDeliveryPolicyType.NoDynamicEncryption,
	                AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
	
	            asset.DeliveryPolicies.Add(policy);
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Create a Program on the Channel. You can have multiple Programs that overlap or are sequential;
	        /// however each Program must have a unique name within your Media Services account.
	        /// </summary>
	        /// <param name="channel"></param>
	        /// <param name="asset"></param>
	        /// <returns></returns>
	        public static IProgram CreateAndStartProgram(IChannel channel, IAsset asset)
	        {
	            IProgram program = channel.Programs.Create(ProgramlName, TimeSpan.FromHours(3), asset.Id);
	            Log("Program created", program.Id);
	
	            Log("Starting program");
	            var programStartOperation = program.SendStartOperation();
	            TrackOperation(programStartOperation, "Program start");
	
	            return program;
	        }
	
	        /// <summary>
	        /// Create locators in order to be able to publish and stream the video.
	        /// </summary>
	        /// <param name="asset"></param>
	        /// <param name="ArchiveWindowLength"></param>
	        /// <returns></returns>
	        public static ILocator CreateLocatorForAsset(IAsset asset, TimeSpan ArchiveWindowLength)
	        {
             	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.            
	            var locator = _context.Locators.CreateLocator
	                (
	                    LocatorType.OnDemandOrigin,
	                    asset,
	                    _context.AccessPolicies.Create
	                        (
	                            "Live Stream Policy",
	                            ArchiveWindowLength,
	                            AccessPermissions.Read
	                        )
	                );
	
	            return locator;
	        }
	
	        /// <summary>
	        /// Perform operations on slates.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void StartStopAdsSlates(IChannel channel)
	        {
	            int cueId = new Random().Next(int.MaxValue);
	            var path = Path.GetFullPath(Path.Combine(AppDomain.CurrentDomain.BaseDirectory, @"..\\..\\SlateJPG\\DefaultAzurePortalSlate.jpg"));
	
	            Log("Creating asset");
	            var slateAsset = _context.Assets.Create("Slate test asset " + DateTime.Now.ToString("yyyy-MM-dd HH-mm"), AssetCreationOptions.None);
	            Log("Slate asset created", slateAsset.Id);
	
	            Log("Uploading file");
	            var assetFile = slateAsset.AssetFiles.Create("DefaultAzurePortalSlate.jpg");
	            assetFile.Upload(path);
	            assetFile.IsPrimary = true;
	            assetFile.Update();
	
	            Log("Showing slate");
	            var showSlateOpeartion = channel.SendShowSlateOperation(TimeSpan.FromMinutes(1), slateAsset.Id);
	            TrackOperation(showSlateOpeartion, "Show slate");
	
	            Log("Hiding slate");
	            var hideSlateOperation = channel.SendHideSlateOperation();
	            TrackOperation(hideSlateOperation, "Hide slate");
	
	            Log("Starting ad");
	            var startAdOperation = channel.SendStartAdvertisementOperation(TimeSpan.FromMinutes(1), cueId, false);
	            TrackOperation(startAdOperation, "Start ad");
	
	            Log("Ending ad");
	            var endAdOperation = channel.SendEndAdvertisementOperation(cueId);
	            TrackOperation(endAdOperation, "End ad");
	
	            Log("Deleting slate asset");
	            slateAsset.Delete();
	        }
	
	        /// <summary>
	        /// Clean up resources associated with the channel.
	        /// </summary>
	        /// <param name="channel"></param>
	        public static void Cleanup(IChannel channel)
	        {
	            IAsset asset;
	            if (channel != null)
	            {
	                foreach (var program in channel.Programs)
	                {
	                    asset = _context.Assets.Where(se => se.Id == program.AssetId)
	                                            .FirstOrDefault();
	
	                    Log("Stopping program");
	                    var programStopOperation = program.SendStopOperation();
	                    TrackOperation(programStopOperation, "Program stop");
	
	                    program.Delete();
	
	                    if (asset != null)
	                    {
	                        Log("Deleting locators");
	                        foreach (var l in asset.Locators)
	                            l.Delete();
	
	                        Log("Deleting asset");
	                        asset.Delete();
	                    }
	                }
	
	                Log("Stopping channel");
	                var channelStopOperation = channel.SendStopOperation();
	                TrackOperation(channelStopOperation, "Channel stop");
	
	                Log("Deleting channel");
	                var channelDeleteOperation = channel.SendDeleteOperation();
	                TrackOperation(channelDeleteOperation, "Channel delete");
	            }
	        }
	
	
	        /// <summary>
	        /// Track long running operations.
	        /// </summary>
	        /// <param name="operation"></param>
	        /// <param name="description"></param>
	        /// <returns></returns>
	        public static string TrackOperation(IOperation operation, string description)
	        {
	            string entityId = null;
	            bool isCompleted = false;
	
	            Log("starting to track ", null, operation.Id);
	            while (isCompleted == false)
	            {
	                operation = _context.Operations.GetOperation(operation.Id);
	                isCompleted = IsCompleted(operation, out entityId);
	                System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	            }
	            // If we got here, the operation succeeded.
	            Log(description + " in completed", operation.TargetEntityId, operation.Id);
	
	            return entityId;
	        }
	
	        /// <summary> 
	        /// Checks if the operation has been completed. 
	        /// If the operation succeeded, the created entity Id is returned in the out parameter.
	        /// </summary> 
	        /// <param name="operationId">The operation Id.</param> 
	        /// <param name="channel">
	        /// If the operation succeeded, 
	        /// the entity Id associated with the sucessful operation is returned in the out parameter.</param>
	        /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	        private static bool IsCompleted(IOperation operation, out string entityId)
	        {
	
	            bool completed = false;
	
	            entityId = null;
	
	            switch (operation.State)
	            {
	                case OperationState.Failed:
	                    // Handle the failure. 
	                    // For example, throw an exception. 
	                    // Use the following information in the exception: operationId, operation.ErrorMessage.
	                    Log("operation failed", operation.TargetEntityId, operation.Id);
	                    break;
	                case OperationState.Succeeded:
	                    completed = true;
	                    entityId = operation.TargetEntityId;
	                    break;
	                case OperationState.InProgress:
	                    completed = false;
	                    Log("operation in progress", operation.TargetEntityId, operation.Id);
	                    break;
	            }
	            return completed;
	        }
	
	
	        private static void Log(string action, string entityId = null, string operationId = null)
	        {
	            Console.WriteLine(
	                "{0,-21}{1,-51}{2,-51}{3,-51}",
	                DateTime.Now.ToString("yyyy'-'MM'-'dd HH':'mm':'ss"),
	                action,
	                entityId ?? string.Empty,
	                operationId ?? string.Empty);
	        }
	    }
	}	


##다음 단계

미디어 서비스 학습 경로를 검토합니다.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### 다른 정보를 찾으시나요?

이 항목이 예상했던 사항을 포함하지 않거나 누락한 경우 또는 일부가 사용자 요구를 충족하지 않은 경우 아래 Disqus 스레드를 사용하여 피드백을 주시기 바랍니다.

<!---HONumber=AcomDC_0921_2016-->