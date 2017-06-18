<properties
	pageTitle="Blob 저장소와 함께 Hudson을 사용하는 방법 | Microsoft Azure"
	description="아티팩트 빌드용 리포지토리로 Azure Blob 저장소와 함께 Hudson을 사용하는 방법에 대해 설명합니다."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Hudson Continuous Integration 솔루션과 함께 Azure 저장소 사용

## 개요

다음 정보에서는 Blob 저장소를 Hudson CI(지속적인 통합) 솔루션에서 만든 빌드 아티팩트의 리포지토리로 사용하거나 빌드 프로세스에 사용할 다운로드 가능 파일의 원본으로 사용하는 방법을 보여 줍니다. 이는 민첩한 개발 환경(Java 또는 다른 언어 사용)에서 코딩하고 빌드가 연속 통합을 기반으로 실행 중이며, 다른 조직 구성원이나 고객과 빌드 아티팩트를 공유하거나 보관 파일을 유지 관리할 수 있도록 빌드 아티팩트의 리포지토리가 필요한 경우와 같은 시나리오에서 유용합니다. 다른 시나리오는 빌드 작업 자체에 종속성 등의 다른 파일이 빌드 입력의 다운로드로 필요한 경우입니다.

이 자습서에서는 Microsoft에서 제공하는 Hudson CI용 Azure 저장소 플러그 인을 사용합니다.

## Hudson 소개 ##

Hudson은 개발자가 코드 변경을 쉽게 통합하고 자동으로 자주 빌드를 생성하도록 하여 개발자의 생산성을 증가시켜 소프트웨어 프로젝트의 연속 통합을 가능하게 합니다. 빌드에는 버전이 지정되고 빌드 아티팩트는 다양한 리포지토리에 업로드될 수 있습니다. 이 문서에서는 Azure Blob 저장소를 빌드 아티팩트의 리포지토리로 사용하는 방법을 보여 줍니다. Azure Blob 저장소에서 종속성을 다운로드하는 방법도 보여 줍니다.

Hudson에 관한 자세한 정보는 [Meet Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)(영문)에서 찾을 수 있습니다.

## Blob 서비스를 사용할 경우의 이점 ##

Blob 서비스를 사용하여 Agile 개발 빌드 아티팩트를 호스트할 경우의 이점은 다음과 같습니다.

- 빌드 아티팩트 및/또는 다운로드 가능 종속성의 고가용성
- Hudson CI 솔루션이 빌드 아티팩트를 업로드할 때의 성능
- 고객 및 파트너가 빌드 아티팩트를 다운로드할 때의 성능
- 익명 액세스, 만료 기반 공유 액세스 서명 액세스, 전용 액세스 중에서 선택하여 사용자 액세스 정책 제어

## 필수 조건 ##

Hudson CI 솔루션으로 Blob 서비스를 사용하려면 다음이 있어야 합니다.

- Hudson 연속 통합 솔루션

    현재 Hudson CI 솔루션이 없는 경우 다음 기술을 사용하여 Hudson CI 솔루션을 실행할 수 있습니다.

    1. Java를 사용할 수 있는 컴퓨터의 <http://hudson-ci.org/>에서 Hudson WAR을 다운로드합니다.
    2. Hudson WAR이 포함된 폴더로 열리는 명령 프롬프트에서 Hudson WAR을 실행합니다. 예를 들어 버전 3.1.2를 다운로드한 경우는 다음과 같습니다.

        `java -jar hudson-3.1.2.war`

    3. 브라우저에서 `http://localhost:8080/`을(를) 엽니다. 그러면 Hudson 대시보드가 열립니다.

    4. Hudson을 처음 사용할 때 `http://localhost:8080/`에서 초기 설정을 완료합니다.

    5. 초기 설정을 완료한 후 실행 중인 Hudson WAR의 인스턴스를 취소하고 Hudson WAR을 다시 시작한 다음 Azure 저장소 플러그인을 설치 및 구성하는 데 사용할 Hudson 대시보드(`http://localhost:8080/`)를 다시 엽니다.

        일반적인 Hudson CI 솔루션은 서비스로 실행되도록 설정되지만 이 자습서에서는 명령줄에서 Hudson WAR을 실행해도 충분합니다.

- Azure 계정. <http://www.azure.com>에서 Azure 계정에 등록할 수 있습니다.

- Azure 저장소 계정. 저장소 계정이 아직 없으면 [저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)에 설명된 단계를 따라 계정을 만들 수 있습니다.

- Hudson CI 솔루션은 필수는 아니지만 알아 두는 것이 좋습니다. 다음 내용에서 기본 예제를 통해 Blob 서비스를 Hudson CI 빌드 아티팩트의 리포지토리로 사용할 때 필요한 단계를 보여 주기 때문입니다.

## Hudson CI로 Blob 서비스를 사용하는 방법 ##

Hudson으로 Blob 서비스를 사용하려면 Azure 저장소 플러그 인을 설치하고 저장소 계정을 사용하도록 플러그 인을 구성한 다음 빌드 아티팩트를 저장소 계정에 업로드하는 빌드 후 작업을 만들어야 합니다. 이와 관련된 단계가 다음 섹션에 설명되어 있습니다.

## Azure 저장소 플러그인을 설치하는 방법 ##

1. Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2. **Manage Hudson** 페이지에서 **Manage Plugins**를 클릭합니다.
3. **Available** 탭을 클릭합니다.
4. **Others**를 클릭합니다.
5. **Artifact Uploaders** 섹션에서 **Microsoft Azure 저장소 플러그인**을 선택합니다.
6. **Install**을 클릭합니다.
7. 설치가 완료되면 Hudson을 다시 시작합니다.

## 저장소 계정을 사용하도록 Azure 저장소 플러그 인을 구성하는 방법 ##

1. Hudson 대시보드에서 **Manage Hudson**을 클릭합니다.
2. **Manage Hudson** 페이지에서 **Configure System**을 클릭합니다.
3. **Microsoft Azure Storage Account Configuration** 섹션에서:

    a. [Azure 포털](https://portal.azure.com)에서 가져올 수 있는 저장소 계정 이름을 입력합니다.

    b. [Azure 포털](https://portal.azure.com)에서 확인 가능한 저장소 계정 키도 입력합니다.

    c. 공용 Azure 클라우드를 사용 중이면 **Blob Service Endpoint URL**에 기본값을 사용합니다. 다른 Azure 클라우드를 사용 중이면 [Azure 포털](https://portal.azure.com)에서 저장소 계정에 대해 지정된 끝점을 사용합니다.

    d. **Validate storage credentials**를 클릭하여 저장소 계정의 유효성을 검사합니다.

    e. [옵션] Hudson CI에 사용할 수 있는 추가 저장소 계정이 있으면 **Add more storage accounts**를 클릭합니다.

    f. **Save**를 클릭하여 설정을 저장합니다.

## 빌드 아티팩트를 저장소 계정으로 업로드하는 빌드 후 작업을 만드는 방법 ##

설명할 목적으로 우선 몇 개의 파일을 만들 작업을 만든 다음 그 파일을 저장소 계정에 업로드할 빌드 후 작업을 추가해야 합니다.

1. Hudson 대시보드에서 **New Job**을 클릭합니다.
2. **MyJob**이라고 이름을 지정하고 **Build a free-style software job**을 클릭한 후 **OK**를 클릭합니다.
3. 작업 구성의 **Build** 섹션에서 **Add build step**을 클릭하고 **Execute Windows batch command**를 선택합니다.
4. **Command**에서 다음 명령을 사용합니다.

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. 작업 구성의 **Post-build Actions** 섹션에서 **Upload artifacts to Microsoft Azure Blob storage**를 클릭합니다.
6. **Storage Account Name**에서는 사용할 저장소 계정을 선택합니다.
7. **컨테이너 이름**에 대해 컨테이너 이름을 지정합니다. (컨테이너는 빌드 아티팩트가 업로드될 때 없으면 만들어집니다.) 환경 변수를 사용할 수 있으며, 이 예제에서는 컨테이너 이름으로 **${JOB\_NAME}**를 입력합니다.

    **팁**

    **Execute Windows batch command** 스크립트를 입력한 아래 **Command** 섹션은 Hudson에서 인식하는 환경 변수에 대한 링크입니다. 환경 변수 이름과 설명을 알아보려면 이 링크를 클릭합니다. **BUILD\_URL** 환경 변수와 같은 특수 문자가 포함된 환경 변수는 컨테이너 이름이나 일반 가상 경로로 사용할 수 없습니다.

8. 이 예의 경우 **Make new container public by default**를 클릭합니다. (개인 컨테이너를 사용하려는 경우 액세스를 허용하려면 공유 액세스 서명을 만들어야 합니다. 이 내용은 이 문서에서 다루지 않습니다. [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)에서 공유 액세스 서명에 대한 자세한 내용을 알아볼 수 있습니다.)
9. [옵션] 빌드 아티팩트를 업로드하기 전에 컨테이너에서 내용을 지우려면 **Clean container before uploading**을 클릭합니다. 컨테이너의 내용을 지우지 않으려면 선택 취소한 상태로 둡니다.
10. **List of Artifacts to upload**에 대해 **text/*.txt**를 입력합니다.
11. **Common virtual path for uploaded artifacts**에 대해 **${BUILD\_ID}/${BUILD\_NUMBER}**를 입력합니다.
12. **Save**를 클릭하여 설정을 저장합니다.
13. Hudson 대시보드에서 **Build Now**를 클릭하여 **MyJob**을 실행합니다. 콘솔 출력을 점검하여 상태를 확인합니다. 빌드 후 작업이 빌드 아티팩트를 업로드하기 시작하면 Azure 저장소 상태 메시지가 콘솔 출력에 포함됩니다.
14. 작업이 성공적으로 완료되었을 때 공용 Blob을 열어 빌드 아티팩트를 검사할 수 있습니다.

    a. [Azure 포털](https://portal.azure.com)에 로그인합니다.

    b. **저장소**를 클릭합니다.

    c. Hudson에 사용할 저장소 계정 이름을 클릭합니다.

    d. **컨테이너**를 클릭합니다.

    e. Hudson 작업을 만들었을 때 할당한 작업 이름의 소문자 버전인 **myjob**이라는 컨테이너를 클릭합니다. 컨테이너 이름 및 Blob 이름은 Azure 저장소에서 소문자(및 대/소문자 구분)입니다. **myjob** 컨테이너의 Blob 목록 내에서 **hello.txt** 및 **date.txt**가 표시됩니다. 이 항목 중 하나의 URL을 복사하여 브라우저에서 엽니다. 빌드 아티팩트로 업로드된 텍스트 파일이 표시됩니다.

아티팩트를 Azure Blob 저장소에 업로드하는 빌드 후 작업은 작업당 하나만 만들 수 있습니다. 아티팩트를 Azure Blob 저장소로 업로드하는 단일 빌드 후 작업에서는 세미콜론을 구분 기호로 사용하여 **List of Artifacts to upload**에서 다른 파일(와일드카드 포함) 및 파일 경로를 지정할 수 있습니다. 예를 들어 Hudson 빌드에서 작업 영역의 **build** 폴더에 JAR 파일과 TXT 파일을 생성한 상태에서 두 개를 모두 Azure Blob 저장소에 업로드하려면 **업로드할 아티팩트 목록** 값에 **build/*.jar;build/*.txt**를 사용합니다. 이중 콜론 구문을 사용하여 Blob 이름 내에 사용할 경로를 지정할 수도 있습니다. 예를 들어 Blob 경로에 **binaries**를 사용하여 JAR을 업로드하고 Blob 경로에 **notices**를 사용하여 TXT 파일을 업로드하려면 **업로드할 아티팩트 목록** 값에 **build/*.jar::binaries;build/*.txt::notices**를 사용합니다.

## Azure Blob 저장소에서 다운로드하는 빌드 단계를 만드는 방법 ##

다음 단계에서는 Azure Blob 저장소에서 항목을 다운로드하는 빌드 단계를 구성하는 방법을 보여 줍니다. Azure Blob 저장소에 유지하는 JAR와 같은 항목을 빌드에 포함하려는 경우에 유용합니다.

1. 작업 구성의 **Build** 섹션에서 **Add build step**을 클릭하고 **Download from Azure Blob storage**를 선택합니다.
2. **Storage account name**에서는 사용할 저장소 계정을 선택합니다.
3. **Container name**에서 다운로드할 Blob이 있는 컨테이너의 이름을 지정합니다. 환경 변수를 사용할 수 있습니다.
4. **Blob name**에서 Blob 이름을 지정합니다. 환경 변수를 사용할 수 있습니다. Blob 이름의 첫 부분 글자를 지정한 후에 와일드카드로 별표를 사용할 수도 있습니다. 예를 들어 **project***는 이름이 **project**로 시작하는 모든 Blob을 지정합니다.
5. [옵션] **Download path**에서 Azure Blob 저장소로부터 파일을 다운로드할 Hudson 컴퓨터의 경로를 지정합니다. 환경 변수도 사용할 수 있습니다. **Download path**에 값을 제공하지 않으면 Azure Blob 저장소의 파일이 작업에 해당하는 작업 영역으로 다운로드됩니다.

Azure Blob 저장소에서 다운로드할 추가 항목이 있는 경우에는 추가 빌드 단계를 만들 수 있습니다.

빌드를 실행하고 나면 빌드 기록 콘솔 출력을 보거나 다운로드 위치를 보고 원하는 Blob이 다운로드되었는지 확인할 수 있습니다.

## Blob 서비스에서 사용하는 구성 요소 ##

다음은 Blob 서비스 구성 요소의 개요를 제공합니다.

- **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 이는 Blob 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 전체 크기가 100TB를 초과하지 않을 경우 한 계정에 포함될 수 있는 컨테이너 수는 제한이 없습니다.
- **컨테이너**: 컨테이너는 Blob 집합 그룹화를 제공합니다. 모든 Blob은 컨테이너에 있어야 합니다. 한 계정에 포함될 수 있는 컨테이너 수에는 제한이 없습니다. 한 컨테이너에 저장될 수 있는 Blob 수에도 제한이 없습니다.
- **Blob**: 모든 형식과 크기의 파일입니다. Azure Blob 저장소 서비스에 저장할 수 있는 Blob 유형에는 블록과 페이지 Blob 두 가지가 있습니다. 대부분의 파일은 블록 Blob입니다. 단일 블록 Blob의 크기는 최대 200GB일 수 있습니다. 이 자습서에서는 블록 Blob을 사용합니다. 다른 Blob 유형인 페이지 Blob의 크기는 최대 1TB일 수 있으며, 파일의 바이트 범위가 자주 수정되는 경우 더 효율적입니다. Blob에 대한 자세한 내용은 [블록 Blob, 추가 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.
- **URL 형식**: Blob에 다음 URL 형식을 사용하여 주소를 지정할 수 있습니다.

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    위의 형식은 공용 Azure 클라우드에 적용됩니다. 다른 Azure 클라우드를 사용 중이면 [Azure 포털](https://portal.azure.com) 내의 끝점을 사용하여 URL 끝점을 확인합니다.)

    위 형식에서 `storageaccount`은(는) 저장소 계정 이름을 나타내고, `container_name`은(는) 컨테이너 이름을 나타내고, `blob_name`은(는) Blob 이름을 각각 나타냅니다. 컨테이너 이름 내에 슬래시(**/**)로 구분하여 여러 경로를 포함할 수 있습니다. 이 자습서에서 컨테이너 이름의 예는 **MyJob**이었고 **${BUILD\_ID}/${BUILD\_NUMBER}**는 일반 가상 경로에 사용되었으므로 Blob의 URL 형식은 다음과 같습니다.

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## 다음 단계

- [Meet Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [Java용 Azure 저장소 SDK](https://github.com/azure/azure-storage-java)
- [Azure 저장소 클라이언트 SDK 참조](http://dl.windowsazure.com/storage/javadoc/)
- [Azure 저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)

자세한 내용은 [Java개발자 센터](https://azure.microsoft.com/develop/java/)를 참조하세요.

<!---HONumber=AcomDC_0914_2016-->