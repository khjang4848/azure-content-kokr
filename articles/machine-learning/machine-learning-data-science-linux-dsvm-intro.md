<properties
	pageTitle="Linux 데이터 과학 가상 컴퓨터 프로비전 | Microsoft Azure"
	description="분석 및 기계 학습을 수행하기 위해 Azure에서 Linux 데이터 과학 가상 컴퓨터를 구성하고 만듭니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="bradsev" />

# Linux 데이터 과학 가상 컴퓨터 프로비전

Linux 데이터 과학 가상 컴퓨터는 다양한 도구가 미리 설치되어 있는 Azure 가상 컴퓨터입니다. 이러한 도구는 보통 데이터 분석 및 기계 학습을 수행하는 데 사용됩니다. 포함된 주요 소프트웨어 구성 요소는 다음과 같습니다.

- Microsoft R Open
- 널리 사용되는 데이터 분석 라이브러리를 포함한 Anaconda Python 배포(버전 2.7 및 3.5)
- Jupyter Notebook(R, Python)
- Azure 저장소 탐색기
- Azure 리소스 관리를 위한 Azure CLI(명령줄 인터페이스)
- PostgresSQL 데이터베이스
- 기계 학습 도구
    - [CNTK(Computational Network Toolkit)](https://github.com/Microsoft/CNTK): Microsoft Research의 심화 학습 소프트웨어 도구 키트
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 지원하는 속성 기계 학습 시스템
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): 기능이 향상된 빠르고 정확한 트리 구현을 제공하는 도구
    - [Rattle](http://rattle.togaware.com/)(R Analytical Tool To Learn Easily): GUI 기반 데이터 탐색 및 모델링을 자동 R 코드 생성과 함께 사용하여 R에서의 데이터 분석 및 기계 학습을 쉽게 시작할 수 있도록 돕는 도구
- Java, Python, node.js, Ruby, PHP의 Azure SDK
- Azure 기계 학습 및 기타 Azure 서비스에서 사용하기 위한 R 및 Python의 라이브러리
- 개발 도구 및 편집기(Eclipse, Emacs, gedit, vi)

데이터 과학을 수행하려면 일련의 작업에 대해 다음 작업을 반복합니다.

1. 데이터 찾기, 로드 및 전처리
2. 모델 빌드 및 테스트
3. 지능형 응용 프로그램에서 사용하기 위해 모델 배포

데이터 과학자는 다양한 도구를 사용하여 이러한 태스크를 완료합니다. 소프트웨어의 적합한 버전을 찾은 다음 다운로드하여 컴파일하고 설치하는 데 시간이 상당히 걸릴 수 있습니다.

Linux 데이터 과학 가상 컴퓨터를 사용하면 이러한 부담을 상당히 줄일 수 있습니다. Linux 데이터 과학 가상 컴퓨터를 사용하여 분석 프로젝트를 바로 시작합니다. R, Python, SQL, Java 및 C++ 등의 다양한 언어로 작업을 수행할 수 있습니다. Eclipse는 사용하기 쉬운 코드를 개발하고 테스트하기 위해 IDE를 제공합니다. VM에 포함된 Azure SDK를 통해 Linux의 다양한 서비스를 사용하여 Microsoft 클라우드 플랫폼용으로 응용 프로그램을 빌드할 수 있습니다. 또한 Ruby, Perl, PHP 및 node.js와 같이 미리 설치된 다른 언어에 액세스해야 합니다.

이 데이터 과학 VM 이미지에 대한 소프트웨어 요금은 부과되지 않습니다. VM 이미지로 프로비전하는 가상 컴퓨터의 크기를 기반으로 평가되는 Azure 하드웨어 사용 요금만 지불합니다. 계산 요금에 대한 자세한 내용은 [Azure Marketplace 파트너 프로그램 사이트](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linuxdsvm/)를 참조하세요.


## 필수 조건

Linux 데이터 과학 가상 컴퓨터를 만들려면 먼저 다음이 있어야 합니다.

- **Azure 구독**: 다운로드하려면 [Azure 무료 평가판 받기](https://azure.microsoft.com/free/)를 참조하세요.
- **Azure 저장소 계정**: 계정을 만들려면 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)를 참조하세요. 기존 계정을 사용하지 않으려는 경우에는 VM 만들기 프로세스의 일부분으로 저장소 계정을 만들 수 있습니다.


## Linux 데이터 과학 가상 컴퓨터 만들기

Linux 데이터 과학 가상 컴퓨터의 인스턴스를 만드는 단계는 다음과 같습니다.

1.	[Azure 포털](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm)에서 가상 컴퓨터 목록으로 이동합니다.
2.	 아래쪽의 **만들기**를 클릭하여 마법사를 실행합니다.![configure-data-science-vm](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.	 다음 섹션에서는 Microsoft 데이터 과학 가상 컴퓨터를 만드는 데 사용되는 마법사의 각 단계(위 그림의 오른쪽에 열거되어 있음)에 필요한 내용을 입력합니다. 다음은 이러한 각 단계를 구성하는 데 필요한 입력입니다.

  	a. **기본 사항**:

  - **이름**: 만들려는 데이터 과학 서버 이름
  - **사용자 이름**: 첫 번째 계정 로그인 ID입니다.
  - **암호**: 첫 번째 계정 암호입니다. 암호 대신 SSH 공개 키를 사용할 수 있습니다.
  - **구독**: 둘 이상의 구독을 보유한 경우, 컴퓨터를 만들고 요금을 청구할 구독 하나를 선택합니다. 이 구독에 대한 리소스 만들기 권한이 있어야 합니다.
  - **리소스 그룹**: 새 그룹을 만들거나 기존 그룹을 사용할 수 있습니다.
  - **위치**: 가장 적합한 데이터 센터를 선택합니다. 이 위치는 대개 대부분의 데이터가 저장되어 있는 위치이거나 사용자의 실제 위치에 가장 가까운 위치(이 경우 네트워크에 가장 빠르게 액세스할 수 있음)입니다.

  	b. **크기**:

  - 기능 요구 사항과 비용 제약 조건에 부합하는 서버 유형 중 하나를 선택합니다. **모두 보기**를 선택하면 더 많은 VM 크기 선택 항목을 확인할 수 있습니다.

  	c. **설정**:

  - **디스크 형식**: SSD(반도체 드라이브)를 선호하는 경우 **프리미엄**을 선택합니다. 그렇지 않은 경우에는 **표준**을 선택합니다.
  - **저장소 계정**: 구독에서 새 Azure 저장소 계정을 만들 수도 있고, 마법사의 **기본 사항** 단계에서 선택했던 동일 위치의 기존 계정을 사용할 수도 있습니다.
  - **기타 매개 변수**: 대부분의 경우에는 기본값만 사용합니다. 기본값 이외의 값을 살펴보려면 정보 링크를 가리켜 특정 필드에 대한 도움말을 확인합니다.

  	d. **요약**:

  - 입력한 모든 정보가 올바른지 확인합니다.

  	e. **구입**:

  - 프로비전을 시작하려면 **구입**을 클릭합니다. 트랜잭션의 조건에는 링크가 제공됩니다. VM은 **크기** 단계에서 선택한 서버 크기에 대한 계산 이외에 추가 요금이 발생하지 않습니다.

프로비전은 약 10-20분 정도 소요됩니다. 프로비전의 상태는 Azure 포털에 표시됩니다.

## Linux 데이터 과학 가상 컴퓨터에 액세스하는 방법

VM을 만든 후 SSH를 사용하여 해당 VM에 로그인할 수 있습니다. 3단계의 **기본 사항** 섹션에서 만든 계정 자격 증명을 텍스트 셸 인터페이스용으로 사용합니다. Windows에서는 [Putty](http://www.putty.org)와 같은 SSH 클라이언트 도구를 다운로드할 수 있습니다. 그래픽 데스크톱(X Windows 시스템)을 사용하려는 경우 Putty에서 X11 전달을 사용하거나 X2Go 클라이언트를 설치할 수 있습니다.

>[AZURE.NOTE] 테스트 결과 X2Go 클라이언트의 성능이 X11 전달보다 훨씬 우수했습니다. 그래픽 데스크톱 인터페이스에서는 X2Go 클라이언트를 사용하는 것이 좋습니다.


## X2Go 클라이언트 설치 및 구성

Linux VM은 이미 X2Go 서버에 프로비전되어 있어 클라이언트 연결을 사용할 수 있습니다. Linux VM 그래픽 데스크톱에 연결하려면 클라이언트에서 다음을 수행합니다.

1. 사용 중인 클라이언트 플랫폼용 X2Go 클라이언트를 [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)에서 다운로드하여 설치합니다.
2. X2Go 클라이언트를 실행하고 **새 세션**을 선택합니다. 여러 탭이 있는 구성 창이 열립니다. 다음 구성 매개 변수를 입력합니다.
    * **세션 탭**:
        - **호스트**: Linux 데이터 과학 VM의 호스트 이름 또는 프로그램 IP 주소입니다.
        - **로그인**: Linux VM의 사용자 이름입니다.
        - **SSH 포트**: 기본값 22를 그대로 사용합니다.
        - **세션 유형**: 값을 XFCE로 변경합니다. 현재 Linux VM은 XFCE 데스크톱만 지원합니다.
    * **미디어 탭**: 사운드 지원 및 클라이언트 인쇄를 사용하지 않으려면 해제할 수 있습니다.
    * **공유 폴더**: 클라이언트 컴퓨터의 디렉터리를 Linux VM에 탑재하려면 이 탭에서 VM과 공유하려는 클라이언트 컴퓨터 디렉터리를 추가합니다.

X2Go 클라이언트를 통해 XFCE 그래픽 데스크톱 또는 SSH 클라이언트를 사용하여 VM에 로그인하고 나면 VM에 설치 및 구성된 도구를 사용할 수 있습니다. XFCE에는 다양한 도구에 대한 응용 프로그램 메뉴 바로 가기와 바탕 화면 아이콘이 표시됩니다.


## Linux 데이터 과학 가상 컴퓨터에 설치된 도구

### Microsoft R Open
R은 가장 많이 사용되는 데이터 분석 및 Machine Learning 언어 중 하나입니다. 분석에 R을 사용하려는 경우 VM에서 MKL(Math Kernel Library)이 포함된 MRO(Microsoft R Open)를 사용합니다. MKL은 분석 알고리즘에 공통되는 수학 연산을 최적화합니다. MRO는 CRAN-R과 완벽하게 호환되며 CRAN에 게시된 모든 R 라이브러리를 MRO에 설치할 수 있습니다. vi, Emacs, gedit 등의 기본 편집기 중 하나에서 R 프로그램을 편집할 수 있습니다. 또한 [RStudio](http://www.rstudio.com) 등의 기타 IDE를 다운로드하여 사용할 수도 있습니다. 사용자의 편의를 위해 **/dsvm/tools** 디렉터리에 RStudio를 설치하는 간단한 스크립트(installRStudio.sh)가 제공됩니다. Emacs 편집기를 사용 중인 경우 Emacs 편집기 내에서 R 파일 작업을 간편하게 수행할 수 있는 Emacs 패키지 ESS(Emacs Speaks Statistics)가 미리 설치되어 있습니다.

R을 시작하려면 셸에서 **R**만 입력하면 됩니다. 이렇게 하면 대화형 환경으로 이동됩니다. R 프로그램을 개발하려면 일반적으로 Emacs, vi, gedit 등의 편집기를 사용한 다음 R 내에서 스크립트를 실행합니다. RStudio를 설치하면 R 프로그램을 개발하기 위한 전체 그래픽 IDE 환경을 사용할 수 있습니다.

또한 원하는 경우 [상위 20개 R 패키지](http://www.kdnuggets.com/2015/06/top-20-r-packages.html)를 설치하기 위해 R 스크립트를 사용할 수도 있습니다. 앞에서 설명한 것처럼 R 대화형 인터페이스를 표시한 다음 셸에 **R**을 입력하여 이 스크립트를 실행할 수 있습니다.

### Python
Python을 사용하여 개발하는 경우를 위해, Anaconda Python 배포 2.7 및 3.5가 설치되었습니다. 이 배포 버전에는 약 300개의 가장 인기 있는 수학, 엔지니어링 및 데이터 분석 패키지와 함께 기본 Python이 포함되어 있습니다. 기본 텍스트 편집기를 사용할 수 있습니다. 또한 Anaconda Python 배포에 번들로 포함된 Python IDE인 Spyder를 사용할 수도 있습니다. Spyder를 사용하려면 그래픽 데스크톱 또는 X11 전달이 필요합니다. 그래픽 데스크톱에 Spyder에 대한 바로 가기가 제공됩니다.

Python은 2.7 및 3.5 버전이 있으므로 현재 세션에서 작업하려는 Python 버전을 활성화해야 합니다. 활성화 프로세스는 PATH 변수를 원하는 Python 버전으로 설정합니다.

Python 2.7을 활성화하려면 셸에서 다음을 실행합니다.

	source /anaconda/bin/activate root

Python 2.7은 */anaconda/bin*에 설치됩니다.

Python 3.5를 활성화하려면 셸에서 다음을 실행합니다.

	source /anaconda/bin/activate py35


Python 3.5는 */anaconda/envs/py35/bin*에 설치됩니다.

Python 대화형 세션을 호출하려는 경우 셸에 **python**만 입력하면 됩니다. 그래픽 인터페이스를 사용 중이거나 X11 전달이 설정된 경우 **spyder**를 입력하면 Python IDE를 시작할 수 있습니다.

### Jupyter Notebook

Anaconda 배포는 코드 및 분석을 공유하는 환경인 Jupyter Notebook도 제공됩니다. JupyterHub을 통해 Jupyter Notebook에 액세스합니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 로그인합니다.

Jupyter Notebook 서버는 Python 2, Python 3 및 R 커널을 사용하여 미리 구성되어 있습니다. 브라우저를 시작하여 노트북 서버에 액세스하는 데 사용할 수 있는 "Jupyter Notebook"이라는 바탕 화면 아이콘이 있습니다. SSH 또는 X2Go 클라이언트를 통해 VM을 사용하는 경우 [https://localhost:8000/](https://localhost:8000/)를 방문하여 Jupyter Notebook 서버에 액세스할 수도 있습니다.

>[AZURE.NOTE] 인증서 경고가 나타나는 경우 계속 진행하세요.

모든 호스트에서 Jupyter Notebook 서버에 액세스할 수 있습니다. *https://\<VM DNS 이름 또는 IP 주소>:8000/*만 입력하면 됩니다.

>[AZURE.NOTE] VM이 프로비전될 때 포트 8000이 방화벽에 기본적으로 열립니다.

Python과 R로 각각 작성된 샘플 노트북이 패키지로 제공됩니다. 로컬 Linux 사용자 이름 및 암호를 사용하여 Jupyter Notebook에 인증하고 나면 노트북 홈페이지에서 샘플 링크를 확인할 수 있습니다. **새로 만들기**를 선택하고 적절한 언어 커널을 선택하여 새 노트북을 만들 수 있습니다. **새로 만들기** 단추가 표시되지 않으면 왼쪽 위에서 **Jupyter** 아이콘을 클릭하여 노트북 서버의 홈페이지로 이동합니다.


### IDE 및 편집기

여러 코드 편집기 중에서 선택할 수 있습니다. 편집기 종류로는 Vi/VIM, Emacs, gEdit 및 Eclipse가 있습니다. gEdit 및 Eclipse는 그래픽 편집기이며 그래픽 데스크톱에 로그인해야 사용할 수 있습니다. 이러한 편집기는 데스크톱 및 응용 프로그램 바로 가기 메뉴를 사용하여 시작할 수 있습니다.

**VIM** 및 **Emacs**는 텍스트 기반 편집기입니다. Emacs의 경우 Emacs 편집기 내에서 R 작업을 쉽게 수행할 수 있도록 해주는 ESS(Emacs Speaks Statistics)라는 추가 기능 패키지가 설치되어 있습니다. 자세한 내용은 [ESS](http://ess.r-project.org/)를 참조하세요.

**Eclipse**는 다국어를 지원하는 확장형 오픈 소스 IDE입니다. Java 개발자 버전은 VM에 설치된 인스턴스입니다. 주요 언어에 사용할 수 있는 몇 가지 플러그 인을 설치하여 Eclipse 환경을 확장할 수 있습니다. 또한 **Eclipse용 Azure 도구 키트**라는 Eclipse에 플러그 인을 설치했습니다. Java와 같은 언어를 지원하는 Eclipse 개발 환경을 사용하여 Azure 응용 프로그램을 만들고 개발, 테스트 및 배포할 수 있습니다. 또한 Java 환경 내에서 다른 Azure 서비스에 액세스할 수 있는 **Java용 Azure SDK**도 있습니다. Eclipse용 Azure 도구 키트에 대한 자세한 내용은 [Eclipse용 Azure 도구 키트](../azure-toolkit-for-eclipse.md)를 참조하세요.

**LaTex**는 texlive 패키지를 통해 Emacs 추가 기능 [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) 패키지와 함께 설치되며 Emacs 내에서의 LaTex 문서 작성을 단순화합니다.

### 데이터베이스

#### Postgres
서비스를 실행 중이며 initdb가 이미 완료된 VM에서는 오픈 소스 데이터베이스 **Postgres**가 제공됩니다. 데이터베이스 및 사용자를 만들어야 합니다. 자세한 내용은 [Postgres 설명서](https://www.postgresql.org/docs/)를 참조하세요.

####  그래픽 SQL 클라이언트
Microsoft SQL Server, Postgres, MySQL 등의 다양한 데이터베이스에 연결하고 SQL 쿼리를 실행할 수 있도록 그래픽 SQL 클라이언트인 **SQuirrel SQL**이 제공됩니다. SQuirrel SQL은 그래픽 데스크톱 세션(예: X2Go 클라이언트 사용)에서 실행할 수 있습니다. SQuirrel SQL을 호출하려는 경우 바탕 화면의 아이콘에서 시작하거나 셸에서 다음 명령을 실행하면 됩니다.

	/usr/local/squirrel-sql-3.7/squirrel-sql.sh

SQuirrel SQL을 처음 사용하기 전에 드라이버 및 데이터베이스 별칭을 설정합니다. JDBC 드라이버는 다음 위치에 있습니다.

*/usr/share/java/jdbcdrivers*

자세한 내용은 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)을 참조하세요.

#### Microsoft SQL Server에 액세스하기 위한 명령줄 도구

SQL Server용 ODBC 드라이버 패키지에서는 다음의 두 가지 명령줄 도구도 제공됩니다.

**bcp**: bcp 유틸리티는 Microsoft SQL Server 인스턴스와 사용자 지정 형식의 데이터 파일 간에 데이터를 대량 복사합니다. bcp 유틸리티를 사용하여 SQL Server 테이블에 많은 수의 새 행을 가져오거나 테이블에서 데이터 파일로 데이터를 내보낼 수 있습니다. 데이터를 테이블로 가져오려면 해당 테이블용으로 만들어진 형식 파일을 사용하거나, 테이블의 구조 및 테이블의 열에 사용 가능한 데이터 형식을 파악해야 합니다.

자세한 내용은 [bcp를 사용하여 연결](https://msdn.microsoft.com/library/hh568446.aspx)을 참조하세요.

**sqlcmd**: sqlcmd 유틸리티를 사용하면 명령 프롬프트에 Transact-SQL 문을 비롯하여 시스템 프로시저와 스크립트 파일을 입력할 수 있습니다. 이 유틸리티는 ODBC를 사용하여 TRANSACT-SQL 일괄 처리를 실행합니다.

자세한 내용은 [sqlcmd를 사용하여 연결](https://msdn.microsoft.com/library/hh568447.aspx)을 참조하세요.

>[AZURE.NOTE] 이 유틸리티는 Linux 플랫폼과 Windows 플랫폼에서 다소 다릅니다. 자세한 내용은 (영문) 설명서를 참조하세요.


#### 데이터베이스 액세스 라이브러리

R 및 Python에는 데이터베이스에 액세스하는 데 사용할 수 있는 라이브러리가 있습니다.

- R에서 **RODBC** 패키지 또는 **dplyr** 패키지를 사용하면 데이터베이스 서버에서 SQL 문을 쿼리 또는 실행할 수 있습니다.
- Python에서 **pyodbc** 라이브러리는 ODBC를 사용한 데이터베이스 액세스를 기본 계층으로 제공합니다.

**Postgres**에 액세스하려면

- R: **RPostgreSQL** 패키지를 사용합니다.
- Python: **psycopg2** 라이브러리를 사용합니다.


### Azure 도구
다음 Azure 도구가 VM에 설치됩니다.

- **Azure 명령줄 인터페이스**: Azure CLI를 사용하여 셸 명령을 통해 Azure 리소스를 만들고 관리할 수 있습니다. Azure 도구를 호출하려는 경우 **azure help**만 입력하면 됩니다. 자세한 내용은 [Azure CLI 설명서 페이지](../virtual-machines-command-line-tools.md)를 참조하세요.
- **Microsoft Azure 저장소 탐색기**: Microsoft Azure 저장소 탐색기는 Azure 저장소 계정에 저장한 개체를 찾아보고 Azure Blob에서 데이터를 업로드 및 다운로드하는 데 사용되는 그래픽 도구입니다. 바탕 화면 바로 가기 아이콘을 사용하여 Storage Explorer에 액세스할 수 있습니다. **StorageExplorer**를 입력하면 셸 프롬프트에서 Storage Explorer를 호출할 수 있습니다. 이렇게 하려면 X2Go 클라이언트에서 로그인하거나 X11 전달을 설정해야 합니다.
- **Azure 라이브러리**: 아래에는 미리 설치된 라이브러리 중 몇 가지가 나와 있습니다.

 - **Python**: Python으로 작성된 Azure 관련 라이브러리 **azure**, **azureml**, **pydocumentdb**, **pyodbc**가 설치되어 있습니다. 처음 세 개의 라이브러리를 사용하면 Azure Storage 서비스, Azure Machine Learning 및 Azure DocumentDB(Azure의 NoSQL Database)에 액세스할 수 있습니다. 네 번째 라이브러리인 pyodbc를 SQL Serve용 Microsoft ODBC 드라이버와 함께 사용하면 ODBC 인터페이스를 사용하여 Python에서 SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse에 액세스할 수 있습니다. **pip list**를 입력하면 나열된 라이브러리를 모두 확인할 수 있습니다. Python 2.7 및 3.5 환경 모두에서 이 명령을 실행해야 합니다.
 - **R**: R로 작성된 Azure 관련 라이브러리 **AzureML** and **RODBC**가 설치되어 있습니다.
 - **Java**: VM의 **/dsvm/sdk/AzureSDKJava** 디렉터리에서 Azure Java 라이브러리 목록을 찾을 수 있습니다. 핵심 라이브러리는 Azure 저장소 및 관리 API, DocumentDB 및 SQL Server용 JDBC 드라이버입니다.

미리 설치된 Firefox 브라우저에서 [Azure 포털](https://portal.azure.com)에 액세스할 수 있습니다. Azure 포털에서 Azure 리소스를 작성, 관리 및 모니터링할 수 있습니다.

### Azure 기계 학습

Azure Machine Learning은 예측 분석 솔루션을 빌드, 배포 및 공유할 수 있는 완전 관리형 클라우드 서비스입니다. Azure Machine Learning Studio에서 실험 및 모델을 빌드합니다. 데이터 과학 가상 컴퓨터의 웹 브라우저에서 [Microsoft Azure 기계 학습](https://studio.azureml.net)을 방문하여 액세스할 수도 있습니다.

Azure Machine Learning Studio에 로그인하고 나면 Machine Learning 알고리즘용 논리적 흐름을 빌드할 수 있는 실험 캔버스에 액세스할 수 있습니다. 또한 Azure Machine Learning에서 호스트되는 Jupyter Notebook에도 액세스할 수 있으며, Machine Learning Studio의 실험을 원활하게 진행할 수 있습니다. 빌드한 기계 학습 모델은 웹 서비스 인터페이스에 래핑하여 운용 가능하도록 설정합니다. 이렇게 하면 모든 언어로 작성된 클라이언트가 기계 학습 모델에서 예측을 호출할 수 있습니다. 자세한 내용은 [Machine Learning 설명서](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.

VM에서 R 또는 Python으로 모델을 빌드한 다음 Azure Machine Learning에서 프로덕션 환경으로 배포할 수도 있습니다. R 및 Python에는 이 기능을 사용하는 데 필요한 라이브러리(각각 **AzureML**과 **azureml**)가 설치되어 있습니다.

R 및 Python으로 작성된 모델을 Azure Machine Learning에 배포하는 방법에 대한 자세한 내용은 [데이터 과학 가상 컴퓨터에서 수행할 수 있는 10가지 작업](machine-learning-data-science-vm-do-ten-things.md), 특히 "R 또는 Python을 사용하여 모델을 빌드하고 Azure Machine Learning을 사용하여 운용 가능하도록 설정" 섹션을 참조하세요.

>[AZURE.NOTE] 이러한 지침은 데이터 과학 VM의 Windows 버전에 대해 작성되었습니다. 하지만 Azure Machine Learning에 모델을 배포하는 방법에 대해 제공되는 정보는 Linux VM에도 적용할 수 있습니다.

### 기계 학습 도구

VM에는 로컬에서 미리 컴파일되어 미리 설치된 몇 가지 기계 학습 도구 및 알고리즘이 포함되어 있습니다. 내용은 다음과 같습니다.

* **CNTK**(Microsoft Research의 Computational Network Toolkit): 심화 학습 도구 키트입니다.
* **Vowpal Wabbit**: 속성 온라인 학습 알고리즘입니다.
* **xgboost**: 최적화되고 향상된 트리 알고리즘을 제공하는 도구입니다.
* **Python**: Anaconda Python에서는 Scikit-learn 등의 라이브러리가 포함된 기계 학습 알고리즘이 번들로 제공됩니다. `pip install`하여 다른 라이브러리를 설치할 수 있습니다.
* **R**: R에서는 다양한 기계 학습 기능이 포함된 라이브러리를 사용할 수 있습니다. 미리 설치된 라이브러리에는 lm, glm, randomForest, rpart 등이 있습니다. 다음 명령을 실행하면 다른 라이브러리를 설치할 수 있습니다.

		install.packages(<lib name>)

아래에서는 목록의 처음 3개 기계 학습 도구에 대해 추가로 설명합니다.

#### CNTK
오픈 소스 심화 학습 도구 키트입니다. 명령줄 도구(cntk)이며 PATH에 이미 포함되어 있습니다.

기본 샘플을 실행하려면 셸에서 다음 명령을 실행합니다.

	# Copy samples to your home directory and execute cntk
	cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
	cd cntkdemo/Data
	cntk configFile=../Config/Simple.cntk

모델 출력은 *~/cntkdemo/Output/Models*에 있습니다.

자세한 내용은 [GitHub](https://github.com/Microsoft/CNTK)의 CNTK 섹션과 [CNTK 위키](https://github.com/Microsoft/CNTK/wiki)를 참조하세요.


#### Vowpal Wabbit

Vowpal Wabbit은 온라인, 해시, allreduce, 축소, learning2search, 활성 및 대화형 학습 등의 기술을 사용하는 기계 학습 시스템입니다.

매우 기본적인 예제에서 도구를 실행하려면 다음 명령을 사용합니다.

	cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
	cd vwdemo
	vw house_dataset

해당 디렉터리에는 더 큰 다른 데모도 있습니다. VW에 대한 자세한 내용은 [GitHub의 이 섹션](https://github.com/JohnLangford/vowpal_wabbit)과 [Vowpal Wabbit 위키](https://github.com/JohnLangford/vowpal_wabbit/wiki)를 참조하세요.

#### xgboost
향상된 (트리) 알고리즘을 위해 디자인되고 최적화된 라이브러리입니다. 이 라이브러리는 확장, 이동성 및 정확도가 높은 대규모 트리 향상 기능을 제공하는 데 필요한 최대 한계까지 기계의 계산 제한을 높이는 데 사용되며

R 라이브러리뿐만 아니라 명령줄로도 제공됩니다.

R에서 이 라이브러리를 사용하려면 셸에서 **R**을 입력하여 대화형 R 세션을 시작하고 라이브러리를 로드합니다.

R 프롬프트에서 실행할 수 있는 간단한 예제는 다음과 같습니다.

	library(xgboost)

	data(agaricus.train, package='xgboost')
	data(agaricus.test, package='xgboost')
	train <- agaricus.train
	test <- agaricus.test
	bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
	pred <- predict(bst, test$data)

xgboost 명령줄을 실행하려면 셸에서 다음 명령을 실행합니다.

	cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
	cd xgboostdemo
	xgboost mushroom.conf


.model 파일이 지정된 디렉터리에 기록됩니다. 이 데모 예제에 대한 정보는 [GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)에서 찾을 수 있습니다.

xgboost에 대한 자세한 내용은 [xgboost 설명서 페이지](https://xgboost.readthedocs.org/en/latest/) 및 해당 [Github 리포지토리](https://github.com/dmlc/xgboost)를 참조하세요.

#### Rattle
Rattle(**R** **A**nalytical **T**ool **T**o **L**earn **E**asily)은 GUI 기반 데이터 탐색 및 모델링 기능을 사용합니다. Rattle은 통계 및 시각적 데이터 요약을 표시하고, 즉시 모델링할 수 있도록 데이터를 변환하고, 해당 데이터에서 감독되거나 감독되지 않는 모델을 빌드하고, 모델의 성능을 그래픽으로 표시하고, 새 데이터 집합의 점수를 매깁니다. 또한 UI에서 작업을 복제하여 R에서 직접 실행하거나 추가 분석을 위한 시작점으로 사용할 수 있는 R 코드를 생성할 수도 있습니다.

Rattle을 실행하려면 그래픽 데스크톱 로그인 세션을 사용 중이어야 합니다. 터미널에서 ```R```을 입력하여 R 환경을 설정합니다. R 프롬프트에서 다음 명령을 입력합니다.

	library(rattle)
	rattle()

이제 그래픽 인터페이스가 열리고 일련의 탭이 표시됩니다. 아래에서는 샘플 날씨 데이터 집합을 사용하여 모델을 빌드하는 데 필요한 Rattle의 빠른 시작 단계를 설명합니다. 아래 일부 단계에서 아직 시스템에 없는 필수 R 패키지를 자동으로 설치 및 로드할 것인지 묻는 메시지가 나타납니다.

>[AZURE.NOTE] 시스템 디렉터리(기본값)에서 패키지를 설치하는 액세스 권한이 없는 경우 R 콘솔 창에 패키지를 개인 라이브러리에 설치할지 여부를 묻는 메시지가 나타납니다. 이러한 메시지가 표시되면 *y*로 응답합니다.

1. **실행**을 클릭합니다.
2. 예제 날씨 데이터 집합을 사용할지 묻는 팝업 대화 상자가 표시됩니다. **예**를 클릭하여 예제를 로드합니다.
3. **모델** 탭을 클릭합니다.
4. **실행**을 클릭하여 의사 결정 트리를 빌드합니다.
5. **그리기**를 클릭하여 의사 결정 트리를 표시합니다.
6. **포리스트** 라디오 단추를 클릭하고 **실행**을 클릭하여 임의 포리스트를 빌드합니다.
7. **평가** 탭을 클릭합니다.
8. **위험** 라디오 단추를 클릭하고 **실행**을 클릭하여 두 개의 위험(누적) 성능 그림을 표시합니다.
9. **로그** 탭을 클릭하여 위 작업에 해당하는 generate R 코드를 표시합니다. 현재 R 릴리스의 버그로 인해, 로그 텍스트의 *Export this log...* 앞에 *#* 문자를 삽입해야 합니다.
10. **내보내기** 단추를 클릭하여 R 스크립트 파일 *weather\_script.R*을 홈 폴더에 저장합니다.

이제 Rattle 및 R을 종료할 수 있습니다. 그러면 생성된 R 스크립트를 수정할 수도 있고, 언제든지 그대로 사용하여 Rattle UI 내에서 실행한 모든 작업을 반복할 수도 있습니다. 특히 R 초보자의 경우 이 손쉬운 방법을 사용하면 그래픽 인터페이스에서 분석 및 기계 학습을 신속하게 수행하는 동시에 R에서 코드를 자동으로 생성하여 수정 및/또는 학습할 수 있습니다.


## 다음 단계
학습과 탐색을 계속하는 방법은 다음과 같습니다.

* [Linux 데이터 과학 가상 컴퓨터의 데이터 과학](machine-learning-data-science-linux-dsvm-walkthrough.md) 연습은 여기에 프로비전된 Linux 데이터 과학을 사용하여 여러 가지 일반적인 데이터 과학 작업을 수행하는 방법을 보여 줍니다.
* 이 문서에 설명된 도구를 사용하여 데이터 과학 VM에서 다양한 데이터 과학 도구를 살펴봅니다. 가상 컴퓨터 내의 셸에서 *dsvm-more-info*를 실행하여 VM에 설치된 도구에 대한 기본 소개 및 자세한 내용을 참조할 수도 있습니다.
* [팀 데이터 과학 프로세스](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)를 사용하여 종단 간 분석 솔루션을 체계적으로 구축하는 방법을 알아봅니다.
* [Cortana 분석 갤러리](http://gallery.cortanaanalytics.com)를 방문하여 Cortana 분석 Suite을 사용하는 기계 학습 및 데이터 분석 샘플을 확인합니다.

<!---HONumber=AcomDC_0914_2016-->