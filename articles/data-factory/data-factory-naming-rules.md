<properties 
	pageTitle="Data Factory - 이름 지정 규칙 | Microsoft Azure" 
	description="데이터 팩터리 엔터티에 대한 이름 지정 규칙을 설명합니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리 - 이름 지정 규칙 
다음 테이블에서 데이터 팩터리 아티팩트에 대한 이름 지정 규칙을 제공합니다.



이름 | 이름 고유성 | 유효성 검사
:--- | :-------------- | :----------------
데이터 팩터리 | Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. 즉, MyDF 및 mydf는 같은 데이터 팩터리를 의미합니다. |<ul><li>각 데이터 팩터리는 정확히 하나의 Azure 구독에 연결됩니다.</li><li>개체 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.</li><li>모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다. 컨테이너 이름에서 연속 대시는 허용되지 않습니다.</li><li>이름의 길이는 3-63자까지 가능합니다.</li></ul>
연결된 서비스/테이블/파이프라인 | 데이터 팩터리에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | <ul><li>테이블 이름에서 최대 문자수는 260개입니다.</li><li>개체 이름은 문자, 숫자 또는 밑줄로 시작해야 합니다 (_).</li><li>다음 문자는 사용할 수 없습니다. “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul>
리소스 그룹 | Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | <ul><li>최대 문자수는 1000개입니다.</li><li>이름은 문자, 숫자 및 다음 문자를 포함할 수 있습니다. “-”, “_”, “,” 및 “.”.</li></ul>

<!---HONumber=AcomDC_0914_2016-->