<properties
   pageTitle="Linux의 Azure 서비스 패브릭 | Microsoft Azure"
   description="서비스 패브릭 클러스터는 Linux 및 Java를 지원하므로 Linux에서 Java로 작성된 서비스 패브릭 응용 프로그램을 배포 및 호스트할 수 있습니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Linux의 서비스 패브릭

[이 블로그 게시물](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/)에 발표된 것처럼 9월 26일에는 미리 보기가 공개될 예정입니다. Linux의 서비스 패브릭 미리 보기를 사용하면 Windows에서와 마찬가지로 Linux에서도 서비스 패브릭을 통해 해당 환경에서 가용성 및 확장성이 뛰어난 응용 프로그램을 빌드, 배포 및 관리할 수 있습니다. 또한 이제 뛰어난 수준의 서비스 패브릭 프레임워크(Reliable Services 및 Reliable Actors)를 Linux의 Java에서 사용할 수 있습니다.

> [AZURE.VIDEO service-fabric-linux-preview]

## 지원되는 운영 체제 및 프로그래밍 언어

제한된 미리 보기에서는 Ubuntu Server 16.04를 실행하는 Azure에서 통합 개발 클러스터 및 다중 컴퓨터 클러스터 만들기를 지원합니다.

어떤 언어 또는 프레임워크에서도 [게스트 실행 서비스](service-fabric-deploy-existing-app.md)를 빌드할 수 있습니다. 또한 Java 또는 C#를 사용하여 Docker 컨테이너를 오케스트레이션하면서 Reliable Services 및 Reliable Actor 프레임워크를 기반으로 서비스를 빌드할 수 있습니다.

>[AZURE.NOTE] 신뢰할 수 있는 컬렉션은 Linux에서 아직 지원되지 않습니다.

Linux의 서비스 패브릭은 Windows의 서비스 패브릭과 개념적으로 동일합니다(OS 사양 및 프로그래밍 언어 지원 제외). 따라서 대부분의 [기존 설명서](http://aka.ms/servicefabricdocs)를 통해 이 기술을 익힐 수 있습니다.

## 다음 단계

[Reliable Actors](service-fabric-reliable-actors-introduction.md) 및 [Reliable Services](service-fabric-reliable-services-introduction.md) 프로그래밍 프레임워크에 대해 알아봅니다.

<!---HONumber=AcomDC_0921_2016-->