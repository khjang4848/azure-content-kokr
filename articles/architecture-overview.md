<properties 
	pageTitle="Microsoft Azure의 응용 프로그램 아키텍처 | Microsoft Azure" 
	description="일반적인 디자인 패턴에 설명하는 아키텍처 개요" 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/13/2016" 
	ms.author="robb"/>

#Microsoft Azure의 응용 프로그램 아키텍처
Microsoft Azure를 사용하는 응용 프로그램을 빌드하기 위한 리소스입니다. 여기에는 시각적으로 소프트웨어 시스템을 설명하는 다이어그램을 그릴 수 있는 도구가 포함됩니다.

##디자인 패턴 포스터

Microsoft Patterns & Practices에서 [클라우드 디자인 패턴](http://msdn.microsoft.com/library/dn568099.aspx)이라는 책을 출간했습니다. 이 책은 MSDN에서 제공되며 PDF로 다운로드할 수도 있습니다. 모든 패턴이 나열된 큰 판형의 포스터도 제공됩니다.

![Patterns & Practices 클라우드 패턴 포스터](./media/architecture-overview/PnPPatternPosterThumb.jpg)

##Microsoft 아키텍처 인증 과정

Microsoft는 최근에 Microsoft 인증 시험 70-534를 지원하는 아키텍처 과정을 만들었습니다. 이 과정은 [EDX.ORG에서 무료로 이용](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x)할 수 있습니다. 이 과정에는 [3D 청사진 Visio 템플릿](#3d-blueprint-visio-template)이 사용됩니다.

![Microsoft 아키텍처 인증 과정](./media/architecture-overview/EDXCourse.png)


##Microsoft 솔루션

Microsoft는 Microsoft 제품을 사용하여 특정 유형의 시스템을 빌드하는 방법을 보여 주는 높은 수준의 일련의 [솔루션 아키텍처](http://aka.ms/azblueprints)를 게시합니다.

Microsoft는 이전에 예제 아키텍처를 보여 주는 일련의 청사진을 게시했습니다. 이전 기능은 앞에서 언급한 솔루션 아키텍처로 대체되었고 청사진 링크는 이 솔루션 아키텍처를 가리키도록 리디렉션되었습니다. 어떤 이유로 이전의 청사진 자료에 액세스해야 하는 경우 요청을 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 보내주세요.

청사진 및 솔루션 아키텍처 다이어그램은 [클라우드 및 엔터프라이즈 기호 집합](#Drawing-symbol-and-icon-sets)의 일부를 사용합니다.

![Microsoft 아키텍처 청사진 3D 다이어그램](./media/architecture-overview/BluePrintThumb.jpg)



##3D 청사진 Visio 템플릿

지금은 작동하지 않는 [Microsoft 아키텍처 청사진](http://aka.ms/azblueprints)의 3D 버전은 타사 도구에서 처음 만들었습니다. Visio 2013(및 이상) 템플릿은 [EDX.ORG에 분산된 Microsoft 아키텍처 인증 과정](#microsoft-architecture-certification-course)의 일부로 2015년 8월 5일에 제공되었습니다.

이 템플릿을 과정 외부에서도 사용할 있습니다.

- 먼저 [교육 비디오를 시청](http://aka.ms/3dBlueprintTemplateVideo)하여 이 템플릿으로 무엇을 할 수 있는지 알아보십시오.
- [Microsoft 3D 청사진 Visio 템플릿](http://aka.ms/3DBlueprintTemplate) 다운로드
- 3D 템플릿과 함께 사용할 [클라우드 및 엔터프라이즈 기호](#drawing-symbol-and-icon-sets)를 다운로드합니다.

교육 자료에 없는 내용이 궁금하거나 의견을 보내주실 분은 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 전자 메일을 보내주십시오. 템플릿은 더 이상 활발하게 개발하지 않지만 PNG 또는 [클라우드 및 엔터프라이즈 기호](#drawing-symbol-and-icon-sets)를 사용할 수 있기 때문에 관련된 유용한 기능으로 업데이트되었습니다.

![Microsoft 3D 청사진 Visio 템플릿](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)


##기호 및 아이콘 집합 그리기 

[Visio 및 기호 교육 비디오를 시청](http://aka.ms/CnESymbolsVideo)한 후 Azure, Windows Server, SQL Server 등을 설명하는 기술 자료를 작성하는 데 도움이 되는 [클라우드 및 엔터프라이즈 기호 집합을 다운로드](http://aka.ms/CnESymbols)하십시오. Microsoft 제품의 사용 방법을 설명하는 교재인 경우 이 기호를 아키텍처 다이어그램, 교육 자료, 프레젠테이션, 데이터시트, 인포그래픽, 백서 및 타사 책에도 사용할 수 있습니다. 하지만 사용자 인터페이스에는 사용할 수 없습니다.

CnE 기호는 Visio, SVG 및 PNG 형식입니다. PowerPoint에서 기호를 쉽게 사용하는 방법에 대한 추가 지침이 집합에 포함되어 있습니다.

기호 집합은 분기별로 제공되며 새로운 서비스가 출시될 때 업데이트됩니다.

Microsoft Office 및 관련 기술에 대한 추가 기호는 CnE 집합처럼 아키텍처 다이어그램에 최적화되지는 않았지만 [Microsoft Office Visio 스텐실](http://www.microsoft.com/ko-KR/download/details.aspx?id=35772)에서 사용할 수 있습니다.

**피드백:** CnE 기호를 사용해보신 분은 간단한 5문항 [설문 조사](http://aka.ms/azuresymbolssurveyv2)를 작성해주십시오. 또는 궁금한 점이 있거나 문제를 발견하신 경우 [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com)으로 전자 메일을 보내주십시오. 저희는 기호 집합에 계속 시간을 투자할 수 있도록 긍정적인 의견을 포함하여 여러분의 다양한 의견을 기다리고 있습니다.

![클라우드 및 엔터프라이즈 기호/아이콘 집합](./media/architecture-overview/CnESymbols.png)

##아키텍처 Infographics

Microsoft에서는 포스터/Infographics 관련 아키텍처를 여러 개 게시합니다. 여기에는 [실제 클라우드 응용 프로그램 빌드](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) 및 [클라우드 서비스를 사용하여 크기 조정](https://azure.microsoft.com/documentation/infographics/cloud-services/)이 포함됩니다.

![Azure 아키텍처 인포그래픽](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=AcomDC_0921_2016-->