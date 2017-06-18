<properties
   pageTitle="Azure 마켓플레이스용 가상 컴퓨터 이미지를 만들기 위한 기술 필수 조건 | Microsoft Azure"
   description="가상 컴퓨터 이미지를 만들고 다른 사람이 구입할 수 있도록 Azure 마켓플레이스에 배포하기 위한 요구 사항을 이해합니다."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="04/29/2016"
  ms.author="hascipio; v-divte"/>

# Azure 마켓플레이스용 가상 컴퓨터 이미지를 만들기 위한 기술 필수 조건
시작하기 전에 프로세스를 자세히 읽고 각 단계를 어디에서, 왜 수행하는지를 이해해야 합니다. 제품 만들기 프로세스를 시작하기 전에 회사 정보와 기타 데이터를 최대한 많이 준비하고 필요한 도구를 다운로드하고 기술 구성 요소를 만들어야 합니다. 이 문서를 검토하여 이러한 항목에 대해 명확히 알아야 합니다.

## 필요한 도구 및 응용 프로그램 다운로드
프로세스를 시작하기 전에 다음 항목이 준비되어 있어야 합니다.

- 대상으로 하는 운영 체제에 따라 [Azure 다운로드](https://azure.microsoft.com/downloads/) 페이지에서 [Azure PowerShell cmdlet](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) 또는 [Linux 명령줄 인터페이스 도구](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409)를 설치합니다.
- CodePlex에서 Azure 저장소 탐색기를 설치합니다.
- Azure Certified용 인증 테스트 도구를 다운로드하여 설치합니다.
  - [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). 인증 도구를 실행하려면 Windows 기반 컴퓨터가 필요합니다. Windows 기반 컴퓨터를 사용할 수 없는 경우 Azure의 Windows 기반 VM을 사용하여 도구를 실행할 수 있습니다.

## 지원되는 플랫폼
Windows 또는 Linux에서 Azure 기반 VM을 개발할 수 있습니다. Azure 호환 VHD(가상 하드 디스크) 만들기와 같은 게시 프로세스의 일부 요소에서는 사용 중인 운영 체제에 따라 다른 도구와 단계를 사용합니다.

- Linux를 사용 중인 경우에는 [가상 컴퓨터 이미지 게시 가이드](marketplace-publishing-vm-image-creation.md)의 “Azure 호환 VHD 만들기(Linux 기반)” 섹션을 참조하세요.
- Windows를 사용 중인 경우에는 [가상 컴퓨터 이미지 게시 가이드](marketplace-publishing-vm-image-creation.md)의 “Azure 호환 VHD 만들기(Windows 기반)” 섹션을 참조하세요.

> [AZURE.NOTE] Windows 기반 컴퓨터에 액세스해야 합니다.
- 인증 유효성 검사 도구를 실행합니다.
- VHD 인증 제출을 위한 VHD 공유 액세스 서명 URL을 만듭니다.

## VHD 개발
클라우드 또는 온-프레미스에서 VHD를 개발할 수 있습니다.

- 클라우드 기반 개발에서는 모든 개발 단계를 Azure에 있는 VHD에서 원격으로 수행합니다.
- 온-프레미스 개발에서는 VHD를 다운로드하고 온-프레미스 인프라를 사용하여 VHD를 개발해야 합니다. 이 방법은 가능하지만 권장되지는 않습니다. Windows 또는 SQL용을 온-프레미스로 개발하려면 관련 온-프레미스 라이선스 키가 필요합니다. VM을 만든 후에는 SQL Server를 포함하거나 설치할 수 없습니다. 또한 제품은 SQL Azure 포털에서 승인된 이미지를 기반으로 해야 합니다. 온-프레미스로 개발하려는 경우에는 클라우드에서 개발할 때와 다른 몇몇 단계를 수행해야 합니다. 관련 정보는 [온-프레미스 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)를 참조하세요.

## 다음 단계
필수 조건을 검토하고 필요한 작업을 마쳤으므로 [가상 컴퓨터 이미지 게시 가이드](marketplace-publishing-vm-image-creation.md)에 자세히 설명된 가상 컴퓨터 이미지 제품 만들기로 진행할 수 있습니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)
- [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md

<!---HONumber=AcomDC_0921_2016-->