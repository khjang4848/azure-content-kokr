<properties 
   pageTitle="U-SQL 작업 디버그 | Microsoft Azure" 
   description="Visual Studio를 사용하여 U-SQL의 실패한 정점을 디버깅하는 방법을 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#Data Lake Analytics 작업에 대한 U-SQL에서 C# 코드 디버깅 

Azure Data Lake Visual Studio 도구를 사용하여 사용자 코드 내부 버그에 의해 발생한 실패한 U-SQL 작업을 디버깅하는 방법을 알아봅니다.

Visual Studio 도구를 사용하면 클러스터에서 컴파일된 코드와 필요한 꼭짓점 데이터를 다운로드한 후 실패한 작업을 추적 및 디버깅할 수 있습니다.

일반적으로 빅 데이터 시스템은 Java, C#, Python 등과 같은 언어를 통해 확장성 모델을 제공합니다. 이러한 대부분의 시스템은 제한된 런타임 디버깅 정보를 제공하여 사용자 지정 코드에서 런타임 오류를 디버깅하기가 어렵습니다. 최신 Visual Studio 도구에는 "실패한 정점 디버그(Failed Vertex Debug)"라는 기능이 함께 제공됩니다. 이 기능을 사용하여 Azure에서 로컬 워크스테이션으로 런타임 데이터를 다운로드할 수 있으므로 클라우드에서 동일한 런타임과 정확한 입력 데이터를 사용하여 실패한 사용자 지정 C# 코드를 디버깅할 수 있습니다. 문제를 해결한 후에는 도구에서 Azure의 수정된 코드를 다시 실행할 수 있습니다.

이 기능의 비디오 프레젠테이션은 [Azure Data Lake Analytics에서 사용자 지정 코드 디버깅](https://mix.office.com/watch/1bt17ibztohcb)을 참조하세요.

>[AZURE.NOTE] Visual Studio는 두 가지 Windows 업그레이드, [Microsoft Visual C++ 2015 재배포 가능 패키지 업데이트 2](https://www.microsoft.com/download/details.aspx?id=51682), [Windows용 유니버설 C 런타임](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)이 없으면 중단되거나 응답하지 않을 수 있습니다.


##필수 조건
-	[시작](data-lake-analytics-data-lake-tools-get-started.md) 문서를 숙지합니다.

## 디버그 프로젝트를 만들고 구성합니다.

Data Lake Visual Studio 도구에서 실패한 작업을 열면 경고가 발생합니다. 자세한 오류 정보가 오류 탭에 표시되고 창 위쪽에 노란색 경고 표시줄이 나타납니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 정점 다운로드](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**정점을 다운로드하고 디버그 솔루션을 만들려면**

1.	실패한 U-SQL 작업을 Visual Studio에서 엽니다.
2.	**다운로드**를 클릭하여 모든 필수 리소스 및 입력 스트림을 다운로드합니다. 다운로드가 실패하면 **다시 시도**를 클릭합니다.
3.	다운로드가 완료된 후 **열기**를 클릭하여 로컬 디버그 프로젝트를 만듭니다. 이름이 **VertexDebug**이고 빈 프로젝트 **LocalVertexHost**를 포함하는 새 Visual Studio 솔루션이 만들어집니다.

사용자 정의 연산자가 U-SQL 코드 숨김(Script.usql.cs)에서 사용되는 경우 사용자 정의 연산자 코드로 클래스 라이브러리 C# 프로젝트를 만들고 VertexDebug 솔루션에 프로젝트를 포함해야 합니다.

Data Lake 분석 데이터베이스에 .dll 어셈블리를 등록한 경우 VertexDebug 솔루션에 어셈블리의 소스 코드를 추가해야 합니다.
 
U-SQL 코드에 대해 별도의 C# 클래스 라이브러리를 작성했고 Data Lake Analytics 데이터베이스에 .dll 어셈블리를 등록한 경우 어셈블리의 소스 C# 프로젝트를 VertexDebug 솔루션에 추가해야 합니다.

일부 드문 경우 원본 솔루션의 U-SQL 코드 숨김(Script.usql.cs) 파일에 사용자 정의된 연산자를 사용합니다. 연산자가 작동하도록 하려면 소스 파일을 포함하는 C# 라이브러리를 만들고 어셈블리 이름을 클러스터에 등록된 것으로 변경해야 합니다. 클러스터에서 실행 중인 스크립트를 확인하여 클러스터에 등록된 어셈블리 이름을 가져올 수 있습니다. U-SQL 작업을 열고 작업 패널에서 "스크립트"를 클릭하여 수행할 수 있습니다.

**솔루션을 구성하려면**

1.	솔루션 탐색기에서 방금 만든 C# 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **속성**을 클릭합니다.
2.	출력 경로를 LocalVertexHost 프로젝트 작업 디렉터리 경로로 설정합니다. LocalVertexHost 속성을 통해 LocalVertexHost 프로젝트 작업 디렉터리 경로를 가져올 수 있습니다.
3.	LocalVertexHost 프로젝트 작업 디렉터리에 .pdb 파일을 저장하기 위해 C# 프로젝트를 빌드하거나 .pdb 파일을 이 폴더에 수동으로 복사할 수 있습니다.
4.	**예외 설정**에서 공용 언어 런타임 예외를 확인합니다.

![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 설정](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##작업 디버그

정점을 다운로드하여 디버그 솔루션을 만들고 환경을 구성한 후 U-SQL 코드 디버깅을 시작할 수 있습니다.

1.	솔루션 탐색기에서 방금 만든 **LocalVertexHost** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 가리킨 다음 **새 인스턴스 시작**을 클릭합니다. LocalVertexHost를 시작 프로젝트로 설정해야 합니다. 처음으로 다음과 같은 메시지가 표시될 수 있습니다. 이 메시지는 무시해도 됩니다. 디버그 화면으로 이동하는 데 최대 1분 정도 걸릴 수 있습니다.
 
    ![Azure Data Lake Analytics U-SQL 디버그 Visual Studio 경고](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.	Visual Studio 기반 디버깅 환경(조사식, 변수, 등)을 사용하여 문제를 해결합니다.
5.	문제를 식별 한 후 코드를 수정하고 모든 문제가 해결될 때까지 C# 프로젝트를 다시 빌드한 후 다시 테스트합니다. 디버그가 완료되면 다음과 같은 메시지가 표시되는 출력 창이 나타납니다.

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##작업 다시 제출

U-SQL 코드의 디버깅을 마친 후에 실패한 작업을 다시 제출할 수 있습니다.

1. ADLA 데이터베이스에 새 .dll 어셈블리를 등록합니다.

    1.	Data Lake Visual Studio 도구의 서버 탐색기/클라우드 탐색기에서 **데이터베이스** 노드를 확장합니다.
    2.	어셈블리를 마우스 오른쪽 단추로 클릭하여 어셈블리를 등록합니다.
    3.	ADLA 데이터베이스에 새 .dll 어셈블리를 등록합니다.
 
2.	또는 script.usql.cs-C# 코드 숨김 파일에 C# 코드를 복사합니다.
3.	작업을 다시 제출합니다.

##다음 단계

- [자습서: Azure 데이터 레이크 분석 U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)
- [자습서: Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure 데이터 레이크 분석 작업에 대한 U-SQL 사용자 정의 연산자를 개발합니다.](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0914_2016-->