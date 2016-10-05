<properties
	pageTitle="PlayReady 및/또는 Widevine 동적 일반 암호화 사용 | Microsoft Azure"
	description="Microsoft Azure 미디어 서비스를 사용하면 Microsoft PlayReady DRM으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 Http-Live-Streaming(HLS) 스트림을 배달할 수 있습니다. 또한 Widevine DRM으로 암호화된 DASH를 배달할 수 있습니다. 이 항목에서는 PlayReady 및 Widevine DRM으로 동적으로 암호화하는 방법을 보여 줍니다."
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
	ms.topic="get-started-article" 
	ms.date="09/27/2016"
	ms.author="juliako"/>


#PlayReady 및/또는 Widevine 동적 일반 암호화 사용

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

Microsoft Azure 미디어 서비스를 사용하면 [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HTTP-Live-Streaming(HLS) 스트림을 전달할 수 있습니다. 또한 Widevine DRM 라이선스로 암호화된 DASH 스트림을 전달할 수 있습니다. PlayReady와 Widevine 모두 일반적인 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다. [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)(버전 3.5.1부터 시작) 또는 REST API를 통해 Widevine을 사용하도록 AssetDeliveryConfiguration을 구성할 수 있습니다.

미디어 서비스는 PlayReady와 Widevine DRM 라이선스를 제공하는 서비스를 제공합니다. 또한 미디어 서비스는 사용자가 보호된 콘텐츠를 재생할 때 PlayReady 또는 Widevine DRM 런타임이 적용하려는 권한 및 제한을 구성할 수 있는 API도 제공합니다. 사용자가 사용권 계약에 따라 DRM으로 보호된 콘텐츠를 요청하면 플레이어 응용 프로그램은 AMS 라이선스 서비스에서 라이선스를 요청합니다. 권한이 부여된 경우 AMS 라이선스 서비스는 플레이어에 라이선스를 발급합니다. PlayReady 또는 Widevine 라이선스에는 클라이언트 플레이어가 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 해독 키가 들어 있습니다.


또한 다음 AMS 파트너를 사용하여 Widevine 라이선스를 배달할 수 있습니다. [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). 자세한 내용은 [Axinom](media-services-axinom-integration.md) 및 [castLabs](media-services-castlabs-integration.md)를 이용한 통합을 참조하세요.

미디어 서비스는 키를 요청 하는 사용자에 권한을 부여하는 여러 방법을 지원합니다. 콘텐츠 키 권한 부여 정책에는 열기 또는 토큰 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다. 토큰 제한 정책은 보안 토큰 서비스(STS)에 의해 발급된 토큰이 수반되어야 합니다. 미디어 서비스는 [간단한 웹 토큰](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)(SWT) 형식 및 [JSON 웹 토큰](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)(JWT) 형식의 토큰을 지원합니다. 자세한 내용은 콘텐츠 키 권한 부여 정책 구성을 참조하세요.

동적 암호화를 이용하려면 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산을 만들어야 합니다. 또한 자산의 배달 정책을 구성해야 합니다(이 항목의 뒷부분에서 설명). 이렇게 하면 스트리밍 URL에 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림이 배달되도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, 미디어 서비스에서는 클라이언트의 요청 각각에 따라 적절한 HTTP 응답을 작성하고 제공합니다.

이 항목은 PlayReady 및 Widevine과 같은 여러 DRM으로 보호된 미디어를 제공하는 응용 프로그램으로 작업하는 개발자에게 유용합니다. 이 항목에서는 권한 부여 정책으로 PlayReady 라이선스 배달 서비스를 구성하여 권한이 있는 클라이언트만 PlayReady 또는 Widevine 라이선스를 받을 수 있도록 하는 방법을 보여 줍니다. 또한 DASH에 대해 PlayReady 또는 Widevine DRM으로 동적 암호화를 사용하는 방법을 보여줍니다.

>[AZURE.NOTE]동적 암호화를 사용하려면 하나 이상의 배율 단위(스트리밍 단위라고도 함)를 얻어야 합니다. 자세한 내용은 [미디어 서비스 크기를 조정하는 방법](media-services-portal-manage-streaming-endpoints.md)을 참조하세요.


##샘플 다운로드

[여기](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)에서 이 문서에 설명된 샘플을 다운로드할 수 있습니다.

##동적 일반 암호화 및 DRM 라이선스 전달 서비스 구성

다음은 PlayReady로 자산을 보호하고 미디어 서비스 라이선스 배달 서비스를 사용하며 동적 암호화를 사용할 때 수행해야 하는 일반적인 단계입니다.

1. 자산을 만들고 파일을 자산에 업로드합니다.
1. 파일이 들어 있는 자산을 적응 비트 전송률 MP4 집합으로 인코딩합니다.
1. 콘텐츠 키를 만들고 인코딩된 자산에 연결합니다. 미디어 서비스에서 콘텐츠 키에는 자산의 암호화 키가 들어 있습니다.
1. 콘텐츠 키의 권한 부여 정책을 구성합니다. 콘텐츠 키 권한 부여 정책은 사용자가 구성해야 하며 콘텐츠 키를 클라이언트에 배달하기 위해서는 해당 클라이언트를 충족시켜야 합니다.

콘텐츠 키 인증 정책을 만들 때 다음을 지정해야 합니다. 전달 메서드(PlayReady 또는 Widevine), 제한(열기 또는 토큰) 및 클라이언트에 키가 전달되는 방법을 정의하는 키 전달 형식에 관련된 정보([PlayReady](media-services-playready-license-template-overview.md) 또는 [Widevine](media-services-widevine-license-template-overview.md) 라이선스 템플릿).
1. 자산에 대한 배달 정책을 구성합니다. 배달 정책 구성에는 배달 프로토콜(예: MPEG DASH, HLS, HDS, 부드러운 스트리밍 또는 모두), 동적 암호화 형식(예: 일반 암호화), PlayReady 또는 Widevine 라이선스 획득 URL이 포함됩니다.

동일한 자산의 각 프로토콜에 다른 정책을 적용할 수 있습니다. 예를 들어, Smooth/DASH에 PlayReady 암호화를, HLS에 AES 봉투(envelope)를 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정하는 단일 정책)은 스트리밍에서 차단됩니다. 정의한 자산 배달 정책이 없는 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.
1. 스트리밍 URL을 얻기 위해 주문형 로케이터를 만듭니다.

이 항목의 끝부분에서 전체 .NET 예제가 나와 있습니다.

다음 이미지는 위에서 설명한 워크플로를 보여 줍니다. 여기서는 인증에 토큰을 사용합니다.

![PlayReady로 보호](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

이 항목의 나머지 부분에서는 자세한 설명, 코드 예제 및 위에서 설명한 작업을 수행하는 방법을 보여주는 항목에 대한 링크를 제공합니다.

##현재 제한 사항

자산 배달 정책을 추가하거나 업데이트하는 경우 연결된 로케이터(있는 경우)를 삭제하고 새 로케이터를 만들어야 합니다.

Azure 미디어 서비스를 사용하여 Widevine를 암호화할 때 제한 사항은 현재 여러 콘텐츠 키가 지원되지 않는다는 점입니다.

##자산 만들기 및 파일을 자산에 업로드

관리, 인코딩 및 비디오 스트림을 수행하려면 먼저 콘텐츠를 Microsoft Azure 미디어 서비스에 업로드해야 합니다. 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

자세한 내용은 [미디어 서비스 계정에 파일 업로드](media-services-dotnet-upload-files.md)를 참조하세요.

##파일이 들어 있는 자산을 적응 비트 전송률 MP4 집합으로 인코딩

동적 암호화를 사용하는 경우 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산을 만들기만 하면 됩니다. 이렇게 하면 매니페스트 및 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림을 받을 수 있도록 합니다. 따라서 사용자는 단일 저장소 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, 미디어 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다. 자세한 내용은 [동적 패키징 개요](media-services-dynamic-packaging-overview.md) 항목을 참조하세요.

인코딩하는 방법에 관한 지침은 [미디어 인코더 표준를 사용하여 자산을 인코딩하는 방법](media-services-dotnet-encode-with-media-encoder-standard.md)을 참조하세요.


##<a id="create_contentkey"></a>콘텐츠 키를 만들어 인코딩된 자산에 연결

미디어 서비스에서 콘텐츠 키에는 자산을 암호화할 키가 들어 있습니다.

자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md)를 참조하세요.


##<a id="configure_key_auth_policy"></a>콘텐츠 키의 인증 정책을 구성합니다.

미디어 서비스는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 권한 부여 정책은 사용자가 구성해야 하며 이 키를 클라이언트에 배달하기 위해서는 해당 클라이언트(플레이어)를 충족시켜야 합니다. 콘텐츠 키 권한 부여 정책에는 열기 또는 토큰 제한과 같은 하나 이상의 권한 부여 제한이 있을 수 있습니다.

자세한 내용은 [콘텐츠 키 권한 부여 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)을 참조하세요.

##<a id="configure_asset_delivery_policy"></a>자산 배달 정책 구성 

자산에 대한 배달 정책을 구성합니다. 자산 배달 정책 구성에는 다음이 포함됩니다.

- DRM 라이선스 획득 URL.
- 자산 배달 프로토콜(예: MPEG DASH, HLS, HDS, 부드러운 스트리밍 또는 모두).
- 동적 암호화 형식.(이 경우 일반 암호화)

자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

##<a id="create_locator"></a>스트리밍 URL을 얻기 위해 주문형 스트리밍 로케이터 만들기

사용자에게 Smooth, DASH 또는 HLS에 대한 스트리밍 URL을 제공해야 합니다.

>[AZURE.NOTE]자산 배달 정책을 추가하거나 업데이트하는 경우 기존 로케이터(있는 경우)를 삭제하고 새 로케이터를 만들어야 합니다.

자산을 게시하고 스트리밍 URL을 작성하는 방법은 [스트리밍 URL 작성](media-services-deliver-streaming-content.md)을 참조하세요.

##테스트 토큰 가져오기

키 권한 부여 정책에 사용된 토큰 제한에 따라 테스트 토큰을 가져옵니다.

	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate = 
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the data in the given TokenRestrictionTemplate.
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

	
[AMS 플레이어](http://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용하여 스트림을 테스트할 수 있습니다.

##<a id="example"></a>예제


다음 샘플에서는 .Net - 버전 3.5.2용 Azure 미디어 서비스 SDK에 도입된 기능을 보여줍니다.(특히, Widevine 라이선스 템플릿을 정의하고 Azure 미디어 서비스에서 Widevine 라이선스를 요청하는 기능). 다음 Nuget 패키지 명령은 패키지를 설치하는 데 사용되었습니다.

	PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. 새 콘솔 프로젝트를 만듭니다.
1. 설치하려면 NuGet을 사용하고 Azure 미디어 서비스 .NET SDK를 추가합니다.
2. 추가 참조를 추가합니다. System.Configuration.
2. 계정 이름 및 키 정보가 들어 있는 구성 파일을 추가합니다.
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 스트리밍 단위를 구성합니다. 자세한 내용은 [스트리밍 끝점 구성](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal)을 참조하세요.

1. Program.cs 파일에 있는 코드를 이 섹션에 나와 있는 코드로 덮어씁니다.
	
	입력 파일이 있는 폴더를 가리키도록 변수를 업데이트해야 합니다.
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
		using Newtonsoft.Json;
		
		namespace DynamicEncryptionWithDRM
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, 
																				DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
		            // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).
		             
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);
		
		            Console.ReadLine();
		        }
		
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
			    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
			    {
			        var encodingPreset = "H264 Multiple Bitrate 720p";
			
			        IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
			                                inputAsset.Name,
			                                encodingPreset));
			
			        var mediaProcessors =
			            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
			
			        var latestMediaProcessor =
			            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
			
			        ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
			        encodeTask.InputAssets.Add(inputAsset);
			        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
			
			        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
			        job.Submit();
			        job.GetExecutionProgressTask(CancellationToken.None).Wait();
			
			        return job.OutputMediaAssets[0];
			    }
		
		
		        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
		        {
		            
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryption);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy          
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		            {
		                new ContentKeyAuthorizationPolicyRestriction
		                {
		                    Name = "Open",
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                    Requirements = null
		                }
		            };
		
		            // Configure PlayReady and Widevine license templates.
		            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, PlayReadyLicenseTemplate);
		
		            IContentKeyAuthorizationPolicyOption WidevinePolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("", 
		                    ContentKeyDeliveryType.Widevine, 
		                    restrictions, WidevineLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with no restrictions").
		                        Result;
		
		
		            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
		            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
		            // Associate the content key authorization policy with the content key.
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		            {
		                new ContentKeyAuthorizationPolicyRestriction
		                {
		                    Name = "Token Authorization Policy",
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                    Requirements = tokenTemplateString,
		                }
		            };
		
		            // Configure PlayReady and Widevine license templates.
		            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, PlayReadyLicenseTemplate);
		
		            IContentKeyAuthorizationPolicyOption WidevinePolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.Widevine,
		                        restrictions, WidevineLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
		            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static private string ConfigurePlayReadyLicenseTemplate()
		        {
		            // The following code configures PlayReady License Template using .NET classes
		            // and returns the XML string.
		
		            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
		            //It contains a field for a custom data string between the license server and the application 
		            //(may be useful for custom app logic) as well as a list of one or more license templates.
		            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
		
		            // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
		            // to be returned to the end users. 
		            //It contains the data on the content key in the license and any rights or restrictions to be 
		            //enforced by the PlayReady DRM runtime when using the content key.
		            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
		            //Configure whether the license is persistent (saved in persistent storage on the client) 
		            //or non-persistent (only held in memory while the player is using the license).  
		            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
		
		            // AllowTestDevices controls whether test devices can use the license or not.  
		            // If true, the MinimumSecurityLevel property of the license
		            // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
		            licenseTemplate.AllowTestDevices = true;
		
		            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
		            // It grants the user the ability to playback the content subject to the zero or more restrictions 
		            // configured in the license and on the PlayRight itself (for playback specific policy). 
		            // Much of the policy on the PlayRight has to do with output restrictions 
		            // which control the types of outputs that the content can be played over and 
		            // any restrictions that must be put in place when using a given output.
		            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
		            //then the DRM runtime will only allow the video to be displayed over digital outputs 
		            //(analog video outputs won’t be allowed to pass the content).
		
		            //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
		            // If the output protections are configured too restrictive, 
		            // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.
		
		            // For example:
		            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
		
		            responseTemplate.LicenseTemplates.Add(licenseTemplate);
		
		            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
		        }
		
		
		        private static string ConfigureWidevineLicenseTemplate()
		        {
		            var template = new WidevineMessage
		            {
		                allowed_track_types = AllowedTrackTypes.SD_HD,
		                content_key_specs = new[]
		                {
		                    new ContentKeySpecs
		                    {
		                        required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
		                        security_level = 1,
		                        track_type = "SD"
		                    }
		                },
		                policy_overrides = new
		                {
		                    can_play = true,
		                    can_persist = true,
		                    can_renew = false
		                }
		            };
		
		            string configuration = JsonConvert.SerializeObject(template);
		            return configuration;
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            // Get the PlayReady license service URL.
		            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
			    
			        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
			        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
			        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
			        // to append /? KID =< keyId > to the end of the url when creating the manifest.
			        // As a result Widevine license acquisition URL will have KID appended twice, 
			        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.
			
		            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
		            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
		            uriBuilder.Query = String.Empty;
		            widevineUrl = uriBuilder.Uri;
		
		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		                {
		                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
		                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}
		
		                };
		
					// In this case we only specify Dash streaming protocol in the delivery policy,
					// All other protocols will be blocked from streaming.
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryption,
		                AssetDeliveryProtocol.Dash,
		                assetDeliveryPolicyConfiguration);
		
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		
		        }
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }
		    }
		}


## 다음 단계

미디어 서비스 학습 경로를 검토합니다.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##참고 항목

[다중 DRM 및 액세스 제어가 포함된 CENC](media-services-cenc-with-multidrm-access-control.md)

[AMS로 Widevine 패키징 구성](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Azure 미디어 서비스에서 Google Widevine 라이선스 전달 서비스 발표](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)

<!---HONumber=AcomDC_0928_2016-->