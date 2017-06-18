<properties
	pageTitle=".NET 백 엔드를 사용하여 Windows 스토어 Leaderboard 앱 만들기 | Azure 모바일 서비스"
	description=".NET 백 엔드와 함께 Azure 모바일 서비스를 사용하여 Windows 스토어 Leaderboard 앱을 빌드하는 방법에 대해 알아봅니다."
	documentationCenter="windows"
	authors="rmcmurray"
	manager="wpickett"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="glenga"/>

# Azure Mobile Services .NET 백 엔드로 Leaderboard 앱 만들기

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


이 자습서에서는 .NET 백 엔드와 함께 Azure 모바일 서비스를 사용해서 Windows 스토어 앱을 작성하는 방법을 보여 줍니다. Azure 모바일 서비스는 모바일 앱 작성을 위한 크로스 플랫폼 클라이언트 라이브러리와 함께 기본 제공되는 인증, 모니터링, 푸시 알림 및 기타 기능이 포함된 확장 가능한 보안 백 엔드를 제공합니다. 모바일 서비스를 위한 .NET 백 엔드는 [ASP.NET Web API](http://asp.net/web-api)를 기반으로 하며, .NET 개발자가 REST API를 생성하기 위한 최상의 방법을 제공합니다.

## 개요

Web API는 .NET 개발자가 REST API를 생성하기 위한 최상의 방법을 제공하는 오픈 소스 프레임워크입니다. Web API 솔루션은 Azure 웹 사이트 또는 .NET 백 엔드를 사용하는 Azure 모바일 서비스에서 호스팅하거나 사용자 지정 프로세스로 직접 호스팅할 수도 있습니다. 모바일 서비스는 모바일 앱을 위해 특별히 디자인된 호스팅 환경입니다. 모바일 서비스에서 Web API 서비스를 호스팅할 때는 데이터 저장소 외에도 다음과 같은 이점을 얻을 수 있습니다.

- 소셜 공급자 및 AAD(Azure Active Directory)를 포함하는 기본 제공 인증.
- 장치별 알림 서비스를 사용한 앱에 대한 푸시 알림.
- 어떤 앱에서도 서비스에 쉽게 액세스할 수 있게 해주는 완벽한 클라이언트 라이브러리 집합.
- 기본 제공되는 로깅 및 진단.

이 자습서에서는 다음 작업을 수행합니다.

- Azure 모바일 서비스를 사용하여 REST API를 만듭니다.
- Azure에 서비스를 게시합니다.
- 서비스를 사용하는 Windows 저장소 앱을 만듭니다.
- EF(Entity Framework)를 사용해서 외래 키 관계 및 DTO(데이터 전송 개체)를 만듭니다.
- ASP.NET Web API를 사용하여 사용자 지정 API를 정의합니다.

이 자습서에서는 [Visual Studio 2013 최신 업데이트](http://go.microsoft.com/fwlink/p/?LinkID=390465)가 사용됩니다.


## 샘플 앱 정보

*leaderboard*는 각 플레이어의 점수 및 순위와 함께 게임의 플레이어 목록을 보여 줍니다. leaderboard는 더 큰 게임의 일부이거나 별도의 앱일 수 있습니다. leaderboard는 실제 응용 프로그램이지만 자습서로 사용할 수 있을 정도로 충분히 간단합니다. 이 앱의 스크린샷은 다음과 같습니다.

![][1]

앱을 간단한 상태로 유지하기 위해 실제 게임은 존재하지 않습니다. 대신 플레이어를 추가하고 각 플레이어에 대한 점수를 제출할 수 있습니다. 점수를 제출하면 모바일 서비스가 새로운 순위를 계산합니다. 백 엔드에서는 모바일 서비스가 2개의 테이블이 포함된 데이터베이스를 만듭니다.

- Player. 플레이어 ID와 이름이 포함됩니다.
- PlayerRank. 플레이어의 점수 및 순위가 포함됩니다.

PlayerRank에는 Player에 대한 외래 키가 포함됩니다. 각 플레이어는 0 또는 1의 PlayerRank를 가집니다.

실제 leaderboard 앱에서는 PlayerRank가 게임 ID도 포함할 수 있으므로 플레이어가 두 개 이상의 게임에 대한 점수를 제출할 수 있습니다.

![][2]

클라이언트 앱은 Players에 대해 전체 CRUD 작업 집합을 수행합니다. 이 앱은 기존의 PlayerRank 엔터티를 읽거나 삭제할 수 있지만 이를 직접 만들거나 업데이트할 수 없습니다. 순위 값은 서비스에서 계산되기 때문입니다. 대신 클라이언트가 점수를 제출하고, 서비스가 모든 플레이어의 순위를 업데이트합니다.

완성된 프로젝트는 [여기](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)에서 다운로드합니다.


## 프로젝트 만들기

Visual Studio를 실행하고 새 ASP.NET 웹 응용 프로그램 프로젝트를 만듭니다. 프로젝트의 이름을 Leaderboard로 지정합니다.

![][3]

Visual Studio 2013에서 ASP.NET 웹 응용 프로그램 프로젝트에는 Azure 모바일 서비스에 대한 템플릿이 포함됩니다. 이 템플릿을 선택하고 **확인**을 클릭합니다.

![][4]

프로젝트 템플릿에는 예제 컨트롤러 및 데이터 개체가 포함됩니다.

![][5]

이러한 개체는 자습서에 필요하지 않으므로 프로젝트에서 삭제할 수 있습니다. 또한 WebApiConfig.cs 및 LeaderboardContext.cs에서 TodoItem에 대한 참조를 제거합니다.

## 데이터 모델 추가

[EF Code First](http://msdn.microsoft.com/data/ee712907#codefirst)를 사용하여 데이터베이스 테이블을 정의합니다. DataObjects 폴더 아래에서 `Player`(이)라는 클래스를 추가합니다.

	using Microsoft.WindowsAzure.Mobile.Service;

	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

`PlayerRank`(이)라는 다른 클래스를 추가합니다.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }

	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

두 클래스는 모두 **EntityData** 클래스에서 파생됩니다. **EntityData**에서 파생되기 때문에 Azure 모바일 서비스에 대해 크로스 플랫폼 클라이언트 라이브러리를 사용해서 앱이 데이터를 쉽게 사용할 수 있습니다. **EntityData**는 또한 앱이 [데이터베이스 쓰기 충돌 해결](mobile-services-windows-store-dotnet-handle-database-conflicts.md)을 쉽게 수행할 수 있게 해줍니다.

`PlayerRank` 클래스에는 관련된 `Player` 엔터티를 가리키는 [탐색 속성](http://msdn.microsoft.com/data/jj713564.aspx)이 있습니다. **[ForeignKey]** 특성은 EF에 `Player` 속성이 외래 키를 나타내도록 지정합니다.

## Web API 컨트롤러 추가

그런 다음 `Player` 및 `PlayerRank`에 대한 Web API 컨트롤러를 추가합니다. 일반 Web API 컨트롤러 대신 Azure 모바일 서비스를 위해 특별히 디자인된 *테이블 컨트롤러*라는 특별한 종류의 컨트롤러를 추가합니다.

컨트롤러 폴더 > **추가** > **새 스캐폴드 항목**을 마우스 오른쪽 단추로 클릭합니다.

![][6]

**스캐폴드 추가** 대화 상자에서 왼쪽에 있는 **일반**을 확장하여 **Azure 모바일 서비스**를 선택합니다. 그런 다음 **Azure 모바일 서비스 테이블 컨트롤러**를 선택합니다. **추가**를 클릭합니다.

![][7]

**컨트롤러 추가** 대화 상자에서 다음을 수행합니다.

1.	**모델 클래스** 아래에서 Player를 선택합니다.
2.	**데이터 컨텍스트 클래스** 아래에서 MobileServiceContext를 선택합니다.
3.	컨트롤러 이름을 “PlayerController”로 지정합니다.
4.	**추가**를 클릭합니다.


이 단계에서는 PlayerController.cs라는 파일을 프로젝트에 추가합니다.

![][8]

컨트롤러는 **TableController<T>**에서 파생됩니다. 이 클래스는 **ApiController**를 상속하지만 Azure 모바일 서비스에 맞게 특별히 설정됩니다.

- 라우팅: **TableController**의 기본 경로는 `/tables/{table_name}/{id}`입니다. 여기서 *table\_name*은 엔터티 이름과 일치합니다. 따라서 Player 컨트롤러의 경로는 */tables/player/{id}*입니다. 이 라우팅 규칙에 따라 **TableController**가 모바일 서비스 [REST API](http://msdn.microsoft.com/library/azure/jj710104.aspx)와 일치하게 됩니다.
- 데이터 액세스: 데이터베이스 작업의 경우 **TableController** 클래스는 데이터 액세스의 추상을 정의하는 **IDomainManager** 인터페이스를 사용합니다. 스캐폴딩에는 EF 컨텍스트를 래핑하는 **IDomainManager**의 구체적 구현인 **EntityDomainManager**가 사용됩니다.

이제 PlayerRank 엔터티에 대한 보조 컨트롤러를 추가합니다. 동일한 단계를 수행하지만 모델 클래스에 대해 PlayerRank를 선택합니다. 동일한 데이터 컨텍스트 클래스를 사용하고 새 클래스를 만들지 않습니다. 컨트롤러 이름을 “PlayerRankController”로 지정합니다.

## DTO를 사용하여 관련 엔터티 반환

`PlayerRank`에는 관련된 `Player` 엔터티가 포함되었습니다.

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

모바일 서비스 클라이언트 라이브러리에서는 탐색 속성이 지원되지 않으며, 직렬화되지 않습니다. 예를들어 다음은 GET `/tables/PlayerRank`에 대한 원시 HTTP 응답입니다.

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT

	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

`Player`은(는) 개체 그래프에 포함되지 않았습니다. 플레이어를 포함하려면 DTO(*데이터 전송 개체*)를 정의하여 개체 그래프를 평면화할 수 있습니다.

DTO는 네트워크에서 데이터를 전송하는 방법을 정의하는 개체입니다. DTO는 유선 형식이 데이터베이스 모델과 다르게 보이기를 원할 때마다 유용합니다. `PlayerRank`에 대한 DTO를 만들려면 DataObjects 폴더에 `PlayerRankDto`(이)라는 새 클래스를 추가합니다.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

`PlayerRankController` 클래스에서는 LINQ **Select** 메서드를 사용해서 `PlayerRank` 인스턴스를 `PlayerRankDto` 인스턴스로 변환할 것입니다. `GetAllPlayerRank` 및 `GetPlayerRank` 컨트롤러 메서드를 다음과 같이 업데이트합니다.

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}

	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });

	    return SingleResult<PlayerRankDto>.Create(result);
	}

이러한 변경 사항에 따라 2개의 GET 메서드가 `PlayerRankDto` 개체를 클라이언트에 반환합니다. `PlayerRankDto.PlayerName` 속성은 플레이어 이름으로 설정됩니다. 다음은 이렇게 변경한 후의 예제 응답입니다.

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT

	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

JSON 페이로드에는 이제 플레이어 이름이 포함됩니다.

LINQ Select 문을 사용하는 대신 AutoMapper를 사용하는 옵션도 있습니다. 이 옵션을 사용하려면 추가 설정 코드가 필요하지만 도메인 엔터티에서 DTO로 자동 매핑을 설정할 수 있습니다. 자세한 내용은 [.NET 백 엔드에서 AutoMapper를 사용하여 데이터베이스 유형과 클라이언트 유형 간 매핑](http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx)을 참조하세요.

## 점수 제출을 위한 사용자 지정 API 정의

`PlayerRank` 엔터티에는 `Rank` 속성이 포함됩니다. 이 값은 서버에서 계산되며, 클라이언트가 이를 설정하지 않습니다. 대신, 클라이언트는 사용자 지정 API를 사용해서 플레이어의 점수를 제출합니다. 서버가 새 점수를 얻으면 모든 플레이어 순위를 업데이트합니다.

우선, `PlayerScore`(이)라는 클래스를 DataObjects 폴더에 추가합니다.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

`PlayerRankController` 클래스에서 `MobileServiceContext` 변수를 생성자에서 클래스 변수로 이동합니다.

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        MobileServiceContext context = new MobileServiceContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // MobileServiceContext context = new MobileServiceContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


`PlayerRankController`에서 다음 메서드를 삭제합니다.

- `PatchPlayerRank`
- `PostPlayerRank`
- `DeletePlayerRank`

이어서 다음 코드를 `PlayerRankController`에 추가합니다.

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

`PostPlayerScore` 메서드는 `PlayerScore` 인스턴스를 입력으로 사용합니다. (클라이언트가 HTTP POST 요청으로 `PlayerScore`을(를) 전송합니다.) 이 메서드는 다음을 수행합니다.

1.	데이터베이스에 아직 항목이 없으면 플레이어에 대해 새로운 `PlayerRank`을(를) 추가합니다.
2.	플레이어의 점수를 업데이트합니다.
3.	모든 플레이어 순위를 일괄로 업데이트하는 SQL 쿼리를 실행합니다.

**[Route]** 특성은 이 메서드에 대한 사용자 지정 경로를 정의합니다.

	[Route("api/score")]

또한 메서드를 별도의 컨트롤러에 배치할 수도 있습니다. 어느 쪽이든 특별한 이점은 없으며 코드를 구성하려는 방법에 따라 방식이 결정됩니다. **[Route]** 특성에 대한 자세한 내용은 [Web API의 특성 라우팅](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)을 참조하세요.

## Windows 스토어 앱 만들기

이 섹션에서는 모바일 서비스를 사용하는 Windows 스토어 앱에 대해 설명합니다. 하지만 XAML이나 UI에 대해서는 자세히 다루지 않습니다. 대신 응용 프로그램 논리에 대해 자세히 다룹니다. 완성된 프로젝트는 [여기](http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af)에서 다운로드할 수 있습니다.

새로운 Windows 스토어 앱 프로젝트를 솔루션에 추가합니다. 여기에서는 비어 있는 앱(Windows) 템플릿이 사용되었습니다.

![][10]

NuGet 패키지 관리자를 사용해서 모바일 서비스 클라이언트 라이브러리를 추가합니다. Visual Studio의 **도구** 메뉴에서 **NuGet 패키지 관리자**를 선택합니다. 그런 후 **패키지 관리자 콘솔**을 선택합니다. 패키지 관리자 콘솔 창에서 다음 명령을 입력합니다.

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

-Project 스위치는 패키지를 설치할 프로젝트를 지정합니다.

## 모델 클래스 추가

Models라는 폴더를 만들고 다음 클래스를 추가합니다.

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }

	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }

	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

이러한 클래스는 모바일 서비스에 있는 데이터 엔터티와 직접적으로 연관됩니다.

## 뷰 모델 만들기

MVVM(Model-View-ViewModel)은 MVC(Model-View-Controller)의 변형 중 하나입니다. MVVM 패턴은 프레젠테이션에서 응용 프로그램 논리를 구분하는 데 유용합니다.

- 이 모델은 도메인 데이터(플레이어, 플레이어 순위 및 플레이어 점수)를 나타냅니다.
- 이 뷰 모델은 뷰에 대한 추상 표현입니다.
- 이 뷰는 뷰 모델을 표시하고 사용자 입력을 뷰 모델로 전송합니다. Windows 스토어 앱의 경우 뷰는 XAML로 정의됩니다.

![][11]

`LeaderboardViewModel`(이)라는 클래스를 추가합니다.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;

	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;

	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

뷰 모델이 데이터 바인딩에 참여할 수 있도록 뷰 모델에서 **INotifyPropertyChanged**를 구현합니다.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }
    }

그런 다음 관측 가능한 속성을 추가합니다. XAML은 이러한 속성에 데이터를 바인딩합니다.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

`IsPending` 속성은 비동기 작업이 서비스에서 보류 중일 때 true입니다. `ErrorMessage` 속성은 서비스에서 발생하는 모든 오류 메시지를 저장합니다.

마지막으로 호출되는 메서드를 서비스 계층에 추가합니다.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            };

            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }
    }

## MobileServiceClient 인스턴스 추가

*App.xaml.cs* 파일을 열고 **MobileServiceClient** 인스턴스를 `App` 클래스에 추가합니다.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;

	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);


	        // ...
	    }
	}

로컬로 디버그할 때는 모바일 서비스가 IIS Express에서 실행됩니다. Visual Studio는 무작위 포트 번호를 할당하므로, 로컬 URL은 http://localhost:*port*이고, 여기서 *port*는 포트 번호입니다. 포트 번호를 가져오려면 Visual Studio에서 F5를 눌러 디버그하여 서비스를 시작합니다. Visual Studio가 브라우저를 실행하고 서비스 URL로 이동합니다. 또한 프로젝트 속성의 **웹** 아래에서 로컬 URL을 찾을 수 있습니다.

## 기본 페이지 만들기

기본 페이지에서 뷰 모델의 인스턴스를 추가합니다. 그런 후 뷰 모델을 페이지에 대한 **DataContext**로 설정합니다.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


앞에서 설명한 것처럼 앱의 모든 XAML은 설명하지 않습니다. MVVM 패턴의 한 가지 장점은 프레젠테이션을 앱 논리로부터 구분하여, 샘플 앱이 마음에 들지 않을 경우 UI를 쉽게 변경할 수 있다는 점입니다.

플레이어 목록은 **ListBox**에 표시됩니다.

	<ListBox Width="200" Height="400" x:Name="PlayerListBox"
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

순위는 **ListView**에 표시됩니다.

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

모든 데이터 바인딩은 뷰 모델을 통해 발생합니다.

## 모바일 서비스 게시

이 단계에서는 모바일 서비스를 Microsoft Azure에 게시하고 라이브 서비스를 사용하도록 앱을 수정합니다.

솔루션 탐색기에서 Leaderboard 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![][12]

**게시** 대화 상자에서 **Azure 모바일 서비스**를 클릭합니다.

![][13]

아직 Azure 계정에 로그인되지 않은 경우 **로그인**을 클릭합니다.

![][14]


기존 모바일 서비스를 선택하거나 **새로 만들기**를 클릭하여 새 항목을 만듭니다. 그런 다음 **확인**을 클릭하여 게시합니다.

![][15]

게시 프로세스에서는 데이터베이스가 자동으로 생성되며, 사용자가 연결 문자열을 구성할 필요가 없습니다.

이제는 leaderboard 앱을 라이브 서비스에 연결할 준비가 되었습니다. 다음 두 가지 항목이 필요합니다.

- 서비스의 URL
- 응용 프로그램 키

두 가지 항목 모두 Azure 클래식 포털에서 가져올 수 있습니다. 포털에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다. 서비스 URL은 대시보드 탭에 나열됩니다. 응용 프로그램 키를 가져오려면 **키 관리**를 클릭합니다.

![][16]

**액세스 키 관리** 대화 상자에서 응용 프로그램 키 값을 복사합니다.

![][17]


서비스 URL 및 응용 프로그램 키를 **MobileServiceClient** 생성자에 전달합니다.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

이제 앱을 실행하면 실제 서비스와 통신이 수행됩니다.

## 다음 단계

* [Azure 모바일 서비스에 대해 자세히 알아보기]
* [Web API에 대해 자세히 알아보기]
* [데이터베이스 쓰기 충돌 처리]
* [푸시 알림 추가](예: 다른 사람이 새 플레이어를 추가하거나 점수를 업데이트하는 경우)
* [인증 시작]

<!-- Anchors. -->
[Overview]: #overview
[About the sample app]: #about-the-sample-app
[Create the project]: #create-the-project
[Add data models]: #add-data-models
[Add Web API controllers]: #add-web-api-controllers
[Use a DTO to return related entities]: #use-a-dto-to-return-related-entities
[Define a custom API to submit scores]: #define-a-custom-api-to-submit-scores
[Create the Windows Store app]: #create-the-windows-store-app
[Add model classes]: #add-model-classes
[Create a view model]: #create-a-view-model
[Add a MobileServiceClient instance]: #add-a-mobileserviceclient-instance
[Create the main page]: #create-the-main-page
[Publish your mobile service]: #publish-your-mobile-service
[Next Steps]: #next-steps

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/09leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png

<!-- URLs. -->

[Azure 모바일 서비스에 대해 자세히 알아보기]: /develop/mobile/resources/
[Web API에 대해 자세히 알아보기]: http://asp.net/web-api
[데이터베이스 쓰기 충돌 처리]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[푸시 알림 추가]: ../notification-hubs-windows-store-dotnet-get-started.md
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-dotnet

<!---HONumber=AcomDC_0921_2016-->