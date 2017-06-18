<properties
	pageTitle="Xamarin에서 Blob 저장소를 사용하는 방법 | Microsoft Azure"
	description="Xamarin용 Azure Storage 클라이언트 라이브러리를 사용하면 개발자들이 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. 이 자습서에서는 Xamarin을 사용하여 Azure Blob 저장소를 사용하는 응용 프로그램을 만드는 방법을 설명합니다."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="micurd;tamram"/>

# Xamarin에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 개요

Xamarin을 사용하면 개발자들이 공유된 C# 코드베이스를 사용하여 기본 사용자 인터페이스를 가진 iOS, Android 및 Windows Store 앱을 만들 수 있습니다. 이 자습서는 Xamarin 응용 프로그램과 함께 Azure Blob 저장소를 사용하는 방법을 보여 줍니다. Azure Storage에 대한 자세한 내용을 보려면 코드를 살펴보기 전에 [Microsoft Azure Storage 소개](storage-introduction.md)를 참조하세요.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## 새 Xamarin 응용 프로그램 만들기

먼저 Android, iOS 및 Windows를 대상으로 하는 앱을 만듭니다. 이 앱은 단순히 컨테이너를 만들고 이 컨테이너에 blob을 업로드합니다. 이 작업을 시작하기 위해 Windows에서 Visual Studio를 사용하게 되지만 Mac OS에서 Xamarin Studio를 사용하여 앱을 만들 때도 동일한 방식을 적용할 수 있습니다.

다음 단계에 따라 응용 프로그램을 만듭니다.

1. 아직 하지 않은 경우 [Visual Studio용 Xamarin](https://www.xamarin.com/download)을 다운로드하여 설치합니다.
2. Visual Studio를 열고 비어 있는 앱(네이티브 공유)을 만듭니다. **파일 > 새로 만들기 > 프로젝트 > 플랫폼 간 > 비어 있는 앱(네이티브 공유)**
3. 솔루션 탐색기 창에서 해당 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션에 대한 NuGet 패키지 관리**를 선택합니다. **WindowsAzure.Storage**를 검색하고 솔루션의 모든 프로젝트에 안정적인 최신 버전을 설치합니다.
4. 프로젝트를 빌드한 후 실행합니다.

이제 단추를 클릭하고 카운터를 증가시켜주는 응용 프로그램이 완료되어야 합니다.

> [AZURE.NOTE] Xamarin용 Azure Storage 클라이언트 라이브러리는 Xamarin.Forms 응용 프로그램에서도 작동합니다.

## 컨테이너 만들기 및 blob 업로드

다음으로, 공유 클래스 `MyClass.cs`에 컨테이너를 만들고 blob을 이 컨테이너에 업로드하는 일부 코드를 추가합니다. `MyClass.cs`는 다음과 같이 표시되어야 합니다.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using System.Threading.Tasks;

	namespace XamarinApp
	{
		public class MyClass
		{
			public MyClass ()
			{
			}

		    public static async Task createContainerAndUpload()
		    {
		        // Retrieve storage account from connection string.
		        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

		        // Create the blob client.
		        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

		        // Retrieve reference to a previously created container.
		        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

				// Create the container if it doesn't already exist.
            	await container.CreateIfNotExistsAsync();

		        // Retrieve reference to a blob named "myblob".
		        CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

		        // Create the "myblob" blob with the text "Hello, world!"
		        await blockBlob.UploadTextAsync("Hello, world!");
		    }
		}
	}

"your\_account\_name\_here" 및 "your\_account\_key\_here"를 실제 계정 이름과 계정 키로 바꾸어야 합니다. 그런 후 IOS, Android 및 Windows Phone 응용 프로그램에서 이 공유 클래스를 사용할 수 있습니다. 각 프로젝트에 `MyClass.createContainerAndUpload()`를 간단히 추가할 수 있습니다. 예:

### XamarinApp.Droid > MainActivity.cs

	using Android.App;
	using Android.Widget;
	using Android.OS;

	namespace XamarinApp.Droid
	{
		[Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
		public class MainActivity : Activity
		{
			int count = 1;

			protected override async void OnCreate (Bundle bundle)
			{
				base.OnCreate (bundle);

				// Set our view from the "main" layout resource
				SetContentView (Resource.Layout.Main);

				// Get our button from the layout resource,
				// and attach an event to it
				Button button = FindViewById<Button> (Resource.Id.myButton);

				button.Click += delegate {
					button.Text = string.Format ("{0} clicks!", count++);
				};

	      	  await MyClass.createContainerAndUpload();
			}
		}
	}

### XamarinApp.iOS > ViewController.cs

	using System;
	using UIKit;

	namespace XamarinApp.iOS
	{
		public partial class ViewController : UIViewController
		{
			int count = 1;

			public ViewController (IntPtr handle) : base (handle)
			{
			}

			public override async void ViewDidLoad ()
			{
				base.ViewDidLoad ();
				// Perform any additional setup after loading the view, typically from a nib.
				Button.AccessibilityIdentifier = "myButton";
				Button.TouchUpInside += delegate {
					var title = string.Format ("{0} clicks!", count++);
					Button.SetTitle (title, UIControlState.Normal);
				};

	            await MyClass.createContainerAndUpload();
	    	}

			public override void DidReceiveMemoryWarning ()
			{
				base.DidReceiveMemoryWarning ();
				// Release any cached data, images, etc that aren't in use.
			}
		}
	}

### XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

	using Windows.UI.Xaml.Controls;
	using Windows.UI.Xaml.Navigation;

	// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

	namespace XamarinApp.WinPhone
	{
	    /// <summary>
	    /// An empty page that can be used on its own or navigated to within a Frame.
	    /// </summary>
	    public sealed partial class MainPage : Page
	    {
	        int count = 1;

	        public MainPage()
	        {
	            this.InitializeComponent();

	            this.NavigationCacheMode = NavigationCacheMode.Required;
	        }

	        /// <summary>
	        /// Invoked when this page is about to be displayed in a Frame.
	        /// </summary>
	        /// <param name="e">Event data that describes how this page was reached.
	        /// This parameter is typically used to configure the page.</param>
	        protected override async void OnNavigatedTo(NavigationEventArgs e)
	        {
	            // TODO: Prepare page for display here.

	            // TODO: If your application contains multiple pages, ensure that you are
	            // handling the hardware Back button by registering for the
	            // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
	            // If you are using the NavigationHelper provided by some templates,
	            // this event is handled for you.
	            Button.Click += delegate {
	                var title = string.Format("{0} clicks!", count++);
	                Button.Content = title;
	            };

	            await MyClass.createContainerAndUpload();
	        }
	    }
	}


## 응용 프로그램 실행

이제 Android 또는 Windows Phone 에뮬레이터에서 이 응용 프로그램을 실행할 수 있습니다. iOS 에뮬레이터에서 이 응용 프로그램을 실행할 수도 있지만 Mac이 있어야 합니다. 이 작업을 수행하는 방법에 대한 특정 지침은 [Visual Studio를 Mac에 연결](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) 설명서를 참조하세요.

앱을 일단 실행하면 저장소 계정에 컨테이너 `mycontainer`가 만들어집니다. 여기에는 `Hello, world!` 텍스트를 포함하는 Blob `myblob`가 있어야 합니다. [Microsoft Azure Storage Explorer](http://storageexplorer.com/)를 사용하여 이 사실을 확인할 수 있습니다.

## 다음 단계

작업 시작을 위해 Xamarin에서 Azure Storage를 사용하는 플랫폼 간 응용 프로그램을 만드는 방법을 배웠습니다. 이 과정은 Blob 저장소의 특정 시나리오를 집중적으로 활용했습니다. 그러나 Blob 저장소 뿐만 아니라 테이블, 파일 및 큐 저장소로도 많은 작업을 수행할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.
- [.NET을 사용하여 Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)
- [.NET을 사용하여 Azure 테이블 저장소 시작](storage-dotnet-how-to-use-tables.md)
- [.NET을 사용하여 Azure 큐 저장소 시작](storage-dotnet-how-to-use-queues.md)
- [Windows에서 Azure 파일 저장소 시작](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!---HONumber=AcomDC_0921_2016-->