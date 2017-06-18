<properties
	pageTitle="5분 만에 Azure 저장소 시작 | Microsoft Azure"
	description="Azure 저장소 빠른 시작, Visual Studio 및 Azure 저장소 에뮬레이터를 사용하여 Microsoft Azure Blob, 테이블 및 큐에 대해 간략하게 알아봅니다. 5분 만에 첫 번째 Azure 저장소 응용 프로그램을 실행합니다."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="09/20/2016"
	ms.author="dineshm;tamram"/>

# 5분 만에 Azure 저장소 시작

## 개요

Azure 저장소 개발을 쉽게 시작할 수 있습니다. 이 자습서에서는 Azure 저장소 응용 프로그램을 신속하게 시작 및 실행하는 방법을 보여 줍니다. .NET 용 Azure SDK에 포함된 빠른 시작 템플릿을 사용할 것입니다. 이러한 빠른 시작 템플릿에는 Azure 저장소를 사용한 몇 가지 기본 프로그래밍 시나리오를 보여주는 즉시 실행 가능한 코드가 포함되어 있습니다.

코드를 살펴보기 전에 Azure 저장소에 대해 자세히 알아보려면 [다음 단계](#next-steps)를 참조하세요.

## 필수 조건

시작하려면 다음과 같은 필수 조건을 갖추어야 합니다.

1. 응용 프로그램을 컴파일 및 빌드하려면 컴퓨터에 [Visual Studio](https://www.visualstudio.com/)가 설치되어 있어야 합니다.

2. 최신 버전의 [.NET용 Azure SDK](https://azure.microsoft.com/downloads/)를 설치합니다. SDK에는 Azure 빠른 시작 샘플 프로젝트, Azure 저장소 에뮬레이터 및 [.NET 용 Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)가 포함되어 있습니다.

3. [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) 이 자습서에서 사용할 Azure 빠른 시작 샘플 프로젝트에 필요하므로 컴퓨터에 이 프로그램이 설치되어 있어야 합니다.

	컴퓨터에 .NET Framework의 어느 버전이 설치되어 있는지 모를 경우 [방법: 설치된 .NET Framework 버전 결정](https://msdn.microsoft.com/vstudio/hh925568.aspx)을 참조하세요. 또는 **시작** 단추나 Windows 키를 누르고 **제어판**을 입력합니다. 그런 다음 **프로그램** > **프로그램 및 기능**을 클릭하고 설치된 프로그램 중에 .NET Framework 4.5가 나열되어 있는지 여부를 결정합니다.

4. Azure 구독 및 Azure Storage 계정이 필요합니다.

    - Azure 구독을 만들려면 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/), [구입 옵션](https://azure.microsoft.com/pricing/purchase-options/) 및 [회원 제안](https://azure.microsoft.com/pricing/member-offers/)(MSDN, Microsoft 파트너 네트워크, BizSpark 및 기타 Microsoft 프로그램의 회원인 경우)을 참조하세요.
    - Azure에서 저장소 계정을 만들려면 [저장소 계정을 만드는 방법](storage-create-storage-account.md#create-a-storage-account)을 참조하세요.

## 클라우드의 Azure 저장소에 대해 첫 번째 Azure 저장소 응용 프로그램 실행

계정이 생성된 후 Visual Studio에서 Azure 빠른 시작 샘플 프로젝트 중 하나를 사용하여 간단한 Azure 저장소 응용 프로그램을 만들 수 있습니다. 이 자습서에서는 Azure 저장소에 대한 샘플 프로젝트 **Azure 저장소: Blob**, **Azure 저장소: 파일**, **Azure 저장소: 큐** 및 **Azure 저장소: 테이블**을 집중적으로 다룹니다.

1. Visual Studio를 시작합니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿** > **Visual C#** > **클라우드** > **빠른 시작** > **데이터 서비스**를 클릭합니다. a. 템플릿 **Azure 저장소: Blob**, **Azure 저장소: 파일**, **Azure 저장소: 큐**, **Azure 저장소: 테이블** 중 하나를 선택합니다. b. **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다.
	- 3\.c. 다음과 같이 프로젝트에 대한 이름을 지정하고 새 Visual Studio 솔루션을 만듭니다.

	![Azure 빠른 시작][Image1]

응용 프로그램을 실행하기 전에 소스 코드를 검토하는 것이 좋습니다. 코드를 검토하려면 Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. 그런 다음 Program.cs 파일을 두 번 클릭합니다.

다음으로, 샘플 응용 프로그램을 실행합니다.

1.	Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. App.config 파일을 열고 다음과 같이 Azure 저장소 에뮬레이터에 대한 연결 문자열을 주석으로 처리합니다.

	`<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.	다음과 같이 Azure 저장소 서비스에 대한 연결 문자열의 주석 처리를 제거하고 App.config 파일에서 저장소 계정 이름과 액세스 키를 제공합니다. `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

	저장소 계정 액세스 키를 검색하려면 [저장소 액세스 키 다시 관리](storage-create-storage-account.md#manage-your-storage-access-keys)를 참조하세요.

3.	App.config 파일에서 저장소 계정 이름과 액세스 키를 제공한 후 **파일** 메뉴에서 **모두 저장**을 클릭하여 모든 프로젝트 파일을 저장합니다.
4.	**빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다.
5.	**디버그** 메뉴에서 **F11** 키를 눌러 단계별로 솔루션을 실행하거나 **F5** 키를 눌러 솔루션을 실행합니다.


## Azure 저장소 에뮬레이터에 대해 첫 번째 Azure 저장소 응용 프로그램 실행

[Azure 저장소 에뮬레이터](storage-use-emulator.md)는 개발 목적으로 Azure Blob, 큐 및 테이블 서비스를 에뮬레이트하는 로컬 환경을 제공합니다. 저장소 에뮬레이터를 사용하면 Azure 구독 또는 저장소 계정을 만들지 않고도, 그리고 비용 발생 없이 저장소 응용 프로그램을 로컬로 테스트할 수 있습니다.

이제 Visual Studio에서 Azure 빠른 시작 샘플 프로젝트 중 하나를 사용하여 간단한 Azure 저장소 응용 프로그램을 만들어 보겠습니다. 이 자습서는 **Azure Blob 저장소**, **Azure 테이블 저장소** 및 **Azure 큐 저장소** 샘플 프로젝트를 중점적으로 다룹니다.

1. Visual Studio를 시작합니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3. **새 프로젝트** 대화 상자에서 **설치됨** > **템플릿** > **Visual C#** > **클라우드** > **빠른 시작** > **데이터 서비스**를 클릭합니다. a. 템플릿 **Azure 저장소: Blob**, **Azure 저장소: 파일**, **Azure 저장소: 큐**, **Azure 저장소: 테이블** 중 하나를 선택합니다. b. **.NET Framework 4.5**가 대상 프레임워크로 선택되었는지 확인합니다. c. 다음과 같이 프로젝트에 대한 이름을 지정하고 새 Visual Studio 솔루션을 만듭니다.

	![Azure 빠른 시작][Image1]

4.	Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. App.config 파일을 열고, Azure 저장소 계정을 이미 추가했으면 Azure 저장소 계정에 대한 연결 문자열을 주석으로 처리합니다. 그런 다음 Azure 저장소 에뮬레이터에 대한 연결 문자열의 주석을 제거합니다.

	`<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

응용 프로그램을 실행하기 전에 소스 코드를 검토하는 것이 좋습니다. 코드를 검토하려면 Visual Studio의 **보기** 메뉴에서 **솔루션 탐색기**를 선택합니다. 그런 다음 Program.cs 파일을 두 번 클릭합니다.

다음으로, Azure 저장소 에뮬레이터에서 샘플 응용 프로그램을 실행합니다.

1.	**시작** 단추나 Windows 키를 누르고 *Microsoft Azure 저장소 에뮬레이터*를 검색하고 응용 프로그램을 시작합니다. 에뮬레이터가 시작되면 Windows 작업 표시줄 영역에 아이콘과 알림이 표시됩니다.
2.	Visual Studio의 **빌드** 메뉴에서 **솔루션 빌드**를 클릭합니다.
3.	**디버그** 메뉴에서 **F11** 키를 눌러 단계별로 솔루션을 실행하거나 **F5** 키를 눌러 솔루션을 처음부터 끝까지 실행합니다.

## 다음 단계

Azure 저장소에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Microsoft Azure 저장소 소개](storage-introduction.md)
* [Azure 저장소 탐색기 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [.NET을 사용하여 Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)
* [.NET을 사용하여 Azure 테이블 저장소 시작](storage-dotnet-how-to-use-tables.md)
* [.NET을 사용하여 Azure 큐 저장소 시작](storage-dotnet-how-to-use-queues.md)
* [Windows에서 Azure 파일 저장소 시작](storage-dotnet-how-to-use-files.md)
* [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
* [Azure 저장소 설명서](https://azure.microsoft.com/documentation/services/storage/)
* [.NET용 Microsoft Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Azure 저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png

<!---HONumber=AcomDC_0921_2016-->