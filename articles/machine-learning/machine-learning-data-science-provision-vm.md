<properties 
	pageTitle="Microsoft 데이터 과학 가상 컴퓨터 프로비전 | Microsoft Azure" 
	description="분석 및 기계 학습을 수행하기 위해 Azure에서 데이터 과학 가상 컴퓨터 구성 및 만들기" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="jhubbard" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="bradsev" />


# Microsoft 데이터 과학 가상 컴퓨터 프로비전

Microsoft 데이터 과학 가상 컴퓨터는 데이터 분석 및 기계 학습에 흔히 사용되는 몇 가지 인기 있는 도구로 사전 설치 및 구성된 Azure VM(가상 컴퓨터) 이미지입니다. 포함된 도구는 다음과 같습니다.

- Microsoft R Server Developer Edition
- Enthought Python 배포
- Jupyter Notebook(R, Python 커널 포함)
- Visual Studio Community Edition
- Power BI 데스크톱
- SQL Server 2016 Developer Edition
- 기계 학습 도구
    - [CNTK(Computational Network Toolkit)](https://github.com/Microsoft/CNTK): Microsoft Research의 심화 학습 소프트웨어 도구 키트
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 지원하는 속성 기계 학습 시스템
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): 기능이 향상된 빠르고 정확한 트리 구현을 제공하는 도구
    - [Rattle](http://rattle.togaware.com/)(R Analytical Tool To Learn Easily): GUI 기반 데이터 탐색 및 모델링을 자동 R 코드 생성과 함께 사용하여 R에서의 데이터 분석 및 기계 학습을 쉽게 시작할 수 있도록 돕는 도구
    - [mxnet](https://github.com/dmlc/mxnet): 효율성과 유연성을 위해 디자인된 심층 학습 프레임워크
- Azure 기계 학습 및 기타 Azure 서비스에서 사용하기 위한 R 및 Python의 라이브러리
- GitHub, Visual Studio Team Services를 포함하는 소스 코드 리포지토리에 작동하는 Git Bash를 포함하는 Git
- 명령 프롬프트를 통해 액세스할 수 있는 몇 가지 인기 있는 Linux 명령줄 유틸리티(awk, sed, perl, grep, find, wget, curl 등 포함)의 Windows 포트


데이터 과학은 찾기, 로딩 및 전처리 데이터 등 작업 순서의 반복, 모델 구축 및 테스트, 지능형 응용 프로그램에서 사용을 위해 모델 배포와 관련이 있습니다. 데이터 과학자는 다양한 도구를 사용하여 이러한 작업을 완료합니다. 소프트웨어의 적합한 버전을 찾고 다운로드하여 설치하는 데 소비되는 시간이 상당히 걸릴 수 있습니다. Microsoft 데이터 과학 가상 컴퓨터는 몇 가지 인기 있는 도구가 미리 설치되고 구성된 상태로 Azure에 프로비정될 수 있는 사용 가능 이미지를 제공하여 이러한 부담을 덜어줄 수 있습니다.

Microsoft 데이터 과학 가상 컴퓨터는 분석 프로젝트를 빠르게 시작합니다. R, Python, SQL 및 C# 등의 다양한 언어로 작업을 수행할 수 있습니다. Visual Studio는 사용하기 쉬운 코드를 개발하고 테스트하기 위해 IDE를 제공합니다. VM에 포함된 Azure SDK를 통해 Microsoft의 클라우드 플랫폼에서 다양한 서비스를 사용하여 응용 프로그램을 빌드할 수 있습니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. 프로비전하는 가상 컴퓨터의 크기에 따른 Azure 사용 비용만 지불하면 됩니다. 계산 비용에 대한 자세한 내용은 [데이터 과학 가상 컴퓨터](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 페이지의 가격 책정 세부 정보 섹션에 나와 있습니다.


## 필수 조건

Microsoft 데이터 과학 가상 컴퓨터를 만들려면 먼저 다음이 있어야 합니다.

- **Azure 구독**: 다운로드하려면 [Azure 무료 평가판 받기](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)를 참조하세요.

*   **Azure 저장소 계정**: 계정을 만들려면 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 기존 계정을 사용하지 않으려는 경우에는 VM 만들기 프로세스의 일부분으로 저장소 계정을 만들 수 있습니다.


## Microsoft 데이터 과학 가상 컴퓨터 만들기

Microsoft 데이터 과학 가상 컴퓨터의 인스턴스를 만드는 단계는 다음과 같습니다.

1.	[Azure 포털](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm)에서 가상 컴퓨터 목록으로 이동합니다.
2.	 마법사로 이동하려면 아래에 있는 **만들기** 단추를 선택합니다.![configure-data-science-vm](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.	 다음 섹션에서는 Microsoft 데이터 과학 가상 컴퓨터를 만드는 데 사용되는 마법사의 각 **5단계**(위의 그림 오른쪽에 열거됨)에 **입력**을 제공합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.
	
	1.	 **기본 사항**
		1.	 **이름**: 만들려는 데이터 과학 서버 이름
		2.	 **사용자 이름**: 관리자 계정 로그인 ID
		3.	 **암호**: 관리자 계정 암호
		4.	 **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다.
		5.	 **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
		6.	 **위치**: 가장 적합한 데이터 센터를 선택합니다. 이는 대개 대부분의 데이터가 저장되어 있거나 네트워크에 가장 빠르게 액세스하기 위해 물리적 위치와 가장 가까이 있는 데이터 센터입니다.
		 
	2.	 **크기**: 기능 요구 사항과 비용 제약 조건에 부합하는 서버 유형 중 하나를 선택합니다. "모두 보기"를 선택하면 보다 다양한 VM 크기를 선택할 수 있습니다.
	
	3.	 **설정**:
		1.	 **디스크 유형**: SSD(반도체 드라이브)를 선호하는 경우 프리미엄을 선택하고 그렇지 않은 경우 "표준"을 선택합니다.
		2.	 **저장소 계정**: 구독에서 새 Azure Storage 계정을 만들거나 마법사의 **기본 사항** 단계에서 선택했던 것과 동일한 *위치*에 있는 기존 계정을 사용할 수 있습니다.
		3.	 **기타 매개 변수**: 일반적으로 기본값을 사용합니다. 기본값이 아닌 것을 사용하려는 경우 특정 필드의 정보 링크를 가리키면 도움말을 볼 수 있습니다.

	4.	 **요약**: 입력한 모든 정보가 올바른지 확인합니다.
	5.	 **구입**: **구입**을 클릭하여 프로비전을 시작합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다.


>[AZURE.NOTE] 프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## Microsoft 데이터 과학 가상 컴퓨터에 액세스하는 방법

VM이 만들어지면 이전의 **기본 사항** 섹션에서 구성한 관리자 계정 자격 증명을 사용하여 원격 데스크톱으로 액세스할 수 있습니다.

VM이 만들어지고 프로비전되면 여기에 설치 및 구성되는 도구를 사용하여 시작할 준비가 되었습니다. 여러 도구에 대한 시작 메뉴 타일 및 데스크톱 아이콘이 있습니다.

## Jupyter Notebook 서버에 강력한 암호를 만드는 방법 

컴퓨터에 설치된 Jupyter 노트북에 대한 사용자 고유의 강력한 암호를 만들려면 데이터 과학 가상 컴퓨터의 명령 프롬프트에서 다음 명령을 실행합니다.

	c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

메시지가 나타나면 강력한 암호를 선택합니다.

출력에 "sha1:xxxxxx" 형식의 암호 해시가 표시됩니다. 이 암호 해시를 복사하여 매개 변수 이름 ***c.NotebookApp.password***가 포함된 **C:\\ProgramData\\jupyter\\jupyter\_notebook\_config.py**의 Notebook 구성 파일에서 기존 해시를 바꿉니다.

따옴표 안에 있는 기존 해시 값의 (xxxxxx) 부분만 바꿔야 합니다. 따옴표와 매개 변수 값에 대한 ***sha1:*** 접두사는 모두 유지해야 합니다.

마지막으로 **Start\_IPython\_Notebook**이라는 Windows 예약 작업으로 VM에서 실행 중인 Jupyter 서버를 중지하고 다시 시작해야 합니다. 이 작업을 다시 시작한 후에 새 암호가 수락되지 않으면 작업 관리자에서 실행 중인 모든 python 프로세스를 중지하고 예약된 작업을 다시 시작합니다. 또는 가상 컴퓨터를 다시 부팅합니다.

## Microsoft 데이터 과학 가상 컴퓨터에 설치된 도구

### Microsoft R Server Developer Edition
분석에 R을 사용하려는 경우 VM에는 Microsoft R Server Developer Edition이 설치됩니다. Microsoft R Server는 지원되고 확장 가능하며 안전한 R에 따라 광범위하게 배포 가능한 엔터프라이즈급 분석 플랫폼입니다. R Server는 다양한 빅 데이터 통계, 예측 모델링 및 기계 학습 기능을 지원하며 분석-탐색, 분석, 시각화 및 모델링의 전체 범위를 지원합니다. Microsoft R Server는 오픈 소스 R을 사용하고 확장하여 엔터프라이즈 규모에서 데이터를 분석하기 위해 R 스크립트, 함수 및 CRAN 패키지와 완전히 호환됩니다. 또한 데이터의 병렬 및 청크된 처리를 추가하여 오픈 소스 R의 메모리 내 한계를 해결합니다. 이렇게 하면 사용자가 주 메모리에 맞는 것보다 훨씬 큰 데이터에서 분석을 실행할 수 있습니다. VM에 포함된 Visual Studio Community Edition에는 R을 사용하기 위한 전체 IDE를 제공하는 R Tools for Visual Studio 확장이 포함되어 있습니다. [RStudio](http://www.rstudio.com) 등의 다른 IDE를 다운로드하여 사용할 수도 있습니다.

### Python
Python을 사용하여 개발하는 경우를 위해, Anaconda Python 배포 2.7 및 3.5가 설치되었습니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. Visual Studio 2015 Community 버전 또는 IDLE이나 Spyder 등의 Anaconda와 함께 제공되는 IDE 중 하나에 설치된 PTVS(Python Tools for Visual Studio)를 사용할 수 있습니다. 검색 표시줄에서 검색(**Win** + **S** 키)하여 다음 중 하나를 시작할 수 있습니다.

>[AZURE.NOTE] Anaconda Python 2.7 및 3.5의 Python Tools for Visual Studio를 가리키려면 각 버전에 대한 사용자 지정 환경을 만들어야 합니다. Visual Studio 2015 Community Edition에서 이러한 환경 경로를 설정하려면 **도구** -> **Python 도구** -> **Python 환경**으로 이동한 다음 **+ 사용자 지정**을 클릭합니다.

Anaconda Python 2.7은 C:\\Anaconda 아래에 설치되어 있으며 Anaconda Python 3.5는 c:\\Anaconda\\envs\\py35 아래에 설치되어 있습니다. 자세한 단계는 [PTVS 설명서](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)를 참조하세요.

### Jupyter 노트북
Anaconda 배포는 코드 및 분석을 공유하는 환경인 Jupyter 노트북도 제공됩니다. Jupyter 노트북 서버는 Python 2, Python 3 및 R 커널로 미리 구성되었습니다. Notebook 서버에 액세스하려면 브라우저를 시작하는 "Jupyter Notebook이라는 바탕 화면 아이콘이 있습니다. 원격 데스크톱을 통해 VM을 사용 중인 경우 [https://localhost:9999/](https://localhost:9999/)에 방문하여 VM에 로그인되었을 때 Jupyter 노트북 서버에 액세스할 수도 있습니다.
 
>[AZURE.NOTE] 인증서 경고가 나타나는 경우 계속 진행하세요.

샘플 노트북을 Python과 R에 패키징했습니다. Jupyter에 로그인한 후에는 Microsoft R Server, SQL Server 2016 R Services(데이터베이스 내 분석), Python 및 기타 Azure 기술을 사용하는 방법을 보여 주는 샘플 Jupyter 노트북을 찾을 수 있습니다. 이전 단계에서 만든 암호를 사용하여 Jupyter 노트북에 인증한 후에 노트북 홈 페이지에서 샘플에 대한 링크를 볼 수 있습니다.

### Visual Studio 2015 Community edition
VM에 설치된 Visual Studio Community edition 평가 목적이나 작은 팀에 사용할 수 있는 Microsoft의 인기 있는 IDE의 무료 버전입니다. 사용 조건은 [여기](https://www.visualstudio.com/support/legal/mt171547)에서 확인할 수 있습니다. 바탕 화면 아이콘이나 **시작** 메뉴를 두 번 클릭하여 Visual Studio를 엽니다. 또한 **Win** + **S**를 누른 후 "Visual Studio"를 입력하여 프로그램을 검색할 수도 있습니다. 여기서 C#, Python, R, node.js와 같은 언어로 된 프로젝트를 만들 수 있습니다. 또한 Azure Data Catalog, Azure HDInsight(Hadoop, Spark) 및 Azure Data Lake와 같은 Azure 서비스로 작동하기 편하게 하는 플러그 인이 설치되어 있습니다.

>[AZURE.NOTE] 평가 기간이 만료되었다는 메시지가 나타날 수 있습니다. Visual Studio Community Edition에 액세스하는 데 필요한 Microsoft 계정 자격 증명을 입력하거나 무료 계정을 새로 만듭니다.

### SQL Server 2016 Developer Edition
데이터베이스 내 분석을 실행하기 위한 R 서비스가 있는 개발자 버전의 SQL Server 2016이 VM에서 제공됩니다. R 서비스는 지능형 응용 프로그램을 개발하고 배포하기 위한 플랫폼을 제공합니다. 풍부하고 강력한 R 언어와 커뮤니티의 여러 패키지를 사용하여 모델을 만들고 SQL Server 데이터에 대한 예측을 생성할 수 있습니다. R 서비스(데이터베이스 내)는 R 언어를 SQL Server와 통합하기 때문에 데이터에 근접한 분석을 유지할 수 있습니다. 이렇게 하면 데이터 이동과 관련된 비용 및 보안 위험이 없어집니다.

>[AZURE.NOTE] SQL Server 2016 Developer Edition은 개발 및 테스트 목적으로만 사용할 수 있습니다. 프로덕션에서 실행하려면 라이선스가 필요합니다.

**SQL Server Management Studio**를 시작하여 SQL Server에 액세스할 수 있습니다. 사용 중인 VM 이름이 서버 이름으로 사용됩니다. Windows의 관리자 권한으로 로그인할 때 Windows 인증을 사용합니다. SQL Server Management Studio에서 다른 사용자를 만들고, 데이터베이스를 만들며, 데이터를 가져오고, SQL 쿼리를 실행할 수 있습니다.

Microsoft R을 사용하여 데이터베이스 내 분석을 사용하도록 설정하려면 서버 관리자 권한으로 로그인한 후에 SQL Server Management Studio에서 일회성 작업으로 다음 명령을 실행합니다.

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### Azure 
몇 개의 Azure 도구가 VM에 설치됩니다.

- 바탕 화면에 Azure SDK 설명서에 액세스하는 바로 가기가 있습니다.
- **AzCopy**: Microsoft Azure Storage 계정 내부/외부로 데이터를 이동시키는 데 사용합니다. 사용법을 보려면 명령 프롬프트에 **Azcopy**를 입력합니다.
- **Microsoft Azure Storage Explorer**: Azure Storage 계정 내에 저장한 개체를 검색하고, Azure Storage 내부/외부로 데이터를 전송하는 데 사용합니다. 검색에 **Storage Explorer**를 입력하거나 Windows 시작 메뉴에서 찾은 후 이 도구에 액세스할 수 있습니다.
- **Adlcopy**: Azure Data Lake로 데이터를 이동하는 데 사용합니다. 사용법을 보려면 명령 프롬프트에 **adlcopy**를 입력하면 됩니다.
- **dtui**: 클라우드의 Azure DocumentDB, NoSQL 데이터베이스 내부/외부로 데이터를 이동하는 데 사용합니다. 명령 프롬프트에 **dtui**를 입력하면 됩니다.
- **Microsoft 데이터 관리 게이트웨이**: 온-프레미스 데이터 원본과 클라우드 간에 데이터를 이동할 수 있도록 합니다. Azure Data Factory와 같은 도구 내에서 사용됩니다.
- **Microsoft Azure Powershell**: VM에도 설치된 Powershell 스크립트 언어의 Azure 리소스를 관리하는 데 사용되는 도구입니다.

###Power BI

대시보드와 멋진 시각화를 구축할 수 있도록 **Power BI Desktop**이 설치되어 있습니다. 이 도구를 사용하여 여러 원본에서 데이터를 추출하고, 대시보드 및 보고서를 작성하며, 클라우드에 게시합니다. 자세한 내용은 [Power BI](http://powerbi.microsoft.com) 사이트를 참조하세요. 시작 메뉴에서 Power BI 데스크톱을 찾을 수 있습니다.

>[AZURE.NOTE] Power BI에 액세스하기 위해서는 Office 365 계정이 필요합니다.

## 추가 Microsoft 개발 도구
[**Microsoft 웹 플랫폼 설치 관리자**](https://www.microsoft.com/web/downloads/platform.aspx)는 다른 Microsoft 개발 도구를 검색하고 다운로드하는 데 사용할 수 있습니다. 또한 Microsoft 데이터 과학 가상 컴퓨터 데스크톱에서 제공되는 도구에 대한 바로 가기도 있습니다.

## VM의 중요 디렉터리

| 항목 | 디렉터리 |
| ------------------------------| ---------------- |
|Jupyter Notebook 서버 구성 | C:\\ProgramData\\jupyter |
|Jupyter Notebook 예제 홈 디렉터리| c:\\dsvm\\notebooks|
|다른 샘플 | c:\\dsvm\\samples|
| Anaconda(기본값: Python 2.7) | c:\\Anaconda |
| Anaconda Python 3.5 환경 | c:\\Anaconda\\envs\\py35|
|R 서버 독립 실행형 인스턴스 디렉터리(기본 R 인스턴스) | C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER |
| R 서버 데이터베이스 내 인스턴스 디렉터리 | C:\\Program Files\\Microsoft SQL Server\\MSSQL13.MSSQLSERVER\\R\_SERVICES |
| 기타 도구 | c:\\dsvm\\tools|

>[AZURE.NOTE] 1.5.0(2016년 9월 3일 이전) 이전에 만든 Microsoft 데이터 과학 가상 컴퓨터 인스턴스는 이전 표에 지정된 디렉터리 구조와는 약간 다른 디렉터리 구조를 사용합니다.

## 다음 단계
학습 및 탐색을 계속하려면 다음 단계는 다음과 같습니다.

* 시작 메뉴를 클릭하여 메뉴에 나열된 도구를 확인하여 데이터 과학 VM에 다양한 데이터 과학 도구를 탐색합니다.
* 엔터프라이즈 규모에서 데이터 분석을 지원하는 R의 RevoScaleR 라이브러리를 사용하는 샘플은 **C:\\Program Files\\Microsoft SQL Server\\130\\R\_SERVER\\library\\RevoScaleR\\demoScripts**로 이동합니다.
* 문서 참조: [데이터 과학 가상 컴퓨터로 할 수 있는 10가지 일](http://aka.ms/dsvmtenthings)
* 체계적으로 [팀 데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)를 사용하여 종단 간 분석 솔루션을 구축하는 방법을 알아봅니다.
* Cortana Intelligence 모음을 사용하는 기계 학습 및 데이터 분석 샘플은 [Cortana Intelligence 갤러리](http://gallery.cortanaintelligence.com)를 방문하세요. 또한 가상 컴퓨터의 **시작** 메뉴 및 데스크톱에 이 갤러리에 대한 아이콘을 제공합니다.

<!---HONumber=AcomDC_0914_2016-->