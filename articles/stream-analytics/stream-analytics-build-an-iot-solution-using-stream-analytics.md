<properties 
	pageTitle="스트림 분석을 사용하여 IOT 솔루션 구축 | Microsoft Azure" 
	description="요금 창구 시나리오의 스트림 분석 iot 솔루션 시작하기 자습서"
	keywords="iot 솔루션, 창 함수"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="jhubbard" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/11/2016" 
	ms.author="jeffstok"
/>

# 스트림 분석을 사용하여 IOT 솔루션 빌드

## 소개

이 자습서에서는 Azure 스트림 분석을 사용하여 데이터에서 실시간으로 통찰력을 얻는 방법을 배웁니다. Azure 스트림 처리 서비스를 통해 개발자는 클릭 스트림과 같은 데이터 스트림, 로그 및 장치에서 생성된 이벤트를 기록 레코드 또는 참조 데이터와 결합함으로써 쉽게 데이터 공간을 처리하여 쉽고 빠르게 비즈니스 인사이트를 얻을 수 있습니다. Azure 스트림 분석은 Microsoft Azure에 호스트된 완전히 관리되는 실시간 스트림 계산 서비스로, 기본 제공 복원력, 낮은 대기 시간 및 확장성을 제공하여 몇 분 안에 실행할 수 있습니다.

이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.

-   Azure 스트림 분석 포털 숙지
-   스트리밍 작업 구성 및 배포
-   스트림 분석 쿼리 언어를 사용하여 실제 문제 명시 및 해결
-   자신있게 Azure 스트리밍 분석을 사용하여 고객에 대한 스트리밍 솔루션 개발
-   모니터링 및 로깅 경험을 사용하여 문제 해결

## 필수 조건

이 자습서를 성공적으로 완료하려면 다음과 같은 필수 조건을 갖추어야 합니다.

-   최신 [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 또는 무료 [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)
-   컴퓨터에 대한 관리자 권한
-   Microsoft 다운로드 센터에서 [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip)을 다운로드합니다.
-   선택 사항: [GitHub](https://aka.ms/azure-stream-analytics-toll-source)의 TollApp 이벤트 생성기에 대한 소스 코드

## 시나리오 소개 - "안녕, 통행료!"


요금소는 전세계의 많은 고속도로, 다리, 터널에서 흔히 볼 수 있습니다. 각 요금소에는 여러 개의 요금 창구가 있어서 수동인 경우는 정차해서 징수원에게 통행료를 내야 하고 자동인 경우는 요금 창구를 지날 때 창구 위쪽에 있는 센서가 차량 앞 유리에 부착된 RFID 카드를 스캔합니다. 이러한 요금소를 통과하는 차량 흐름을 흥미로운 작업을 수행할 수 있는 이벤트 스트림으로 쉽게 시각화할 수 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## 들어오는 데이터

요금소의 입구와 출구에 설치된 센서에서 생성되는 두 데이터 스트림 및 차량 등록 데이터가 있는 정적 조회 데이터 집합을 사용하여 작업합니다.

### 진입 데이터 스트림

진입 데이터 스트림에는 요금소로 들어가는 자동차에 대한 정보가 포함됩니다.


| TollId | EntryTime | LicensePlate | 시스템 상태 | 계정을 | 모델 | VehicleType | VehicleWeight | Toll | 태그 |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | 1007 BNJ | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |


다음은 열에 대한 간단한 설명입니다.


| TollId | 요금 창구를 고유하게 식별하는 요금 창구 ID |
|--------------|----------------------------------------------------------------|
| EntryTime | 요금 창구에 차량이 진입하는 날짜 및 시간(UTC) |
| LicensePlate | 차량 번호판 번호 |
| 시스템 상태 | 미국의 주 이름 |
| 계정을 | 자동차 제조업체 |
| 모델 | 자동차 모델 번호 |
| VehicleType | 승용차는 1, 화물차는 2 |
| WeightType | 차량 무게(톤), 승용차는 0 |
| Toll | 통행료 값(USD) |
| 태그 | 요금을 수동으로 지불할 때 이 지불을 자동으로 비워놓는 자동차의 e-태그 |


### 데이터 스트림 종료

진출 데이터 스트림에는 요금소를 떠나는 차량에 대한 정보가 포함됩니다.


| **TollId** | **ExitTime** | **LicensePlate** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | 1007 BNJ |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

다음은 열에 대한 간단한 설명입니다.


| 열 | 설명 |
|--------------|-----------------------------------------------------------------|
| TollId | 요금 창구를 고유하게 식별하는 요금 창구 ID |
| ExitTime | 차량이 요금소를 빠져 나가는 날짜 및 시간(UTC) |
| LicensePlate | 차량 번호판 번호 |

### 화물 차량 등록 데이터

화물 차량 등록 데이터베이스의 정적 스냅숏을 사용합니다.


| LicensePlate | RegistrationId | 만료됨 |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |

다음은 열에 대한 간단한 설명입니다.


| 열 | 설명 |
|--------------|-----------------------------------------------------------------|
| LicensePlate | 차량 번호판 번호 |
| RegistrationId | RegistrationId |
| 만료됨 | 차량 등록이 활성화된 경우는 0, 등록이 만료된 경우는 1 |


## Azure 스트림 분석을 위한 환경 설정

이 자습서를 수행하려면 Microsoft Azure 구독이 필요합니다. Microsoft는 Microsoft Azure 서비스에 대한 무료 평가판을 아래와 같이 제공합니다.

Azure 계정이 없는 경우 <http://azure.microsoft.com/pricing/free-trial/>로 이동하여 무료 평가판 버전을 요청할 수 있습니다.

참고: 무료 평가판에 등록하려면 문자 메시지 및 유효한 신용 카드를 받을 수 있는 모바일 장치가 필요합니다.

이 연습 끝에 나오는 "Azure 계정 정리" 섹션 단계를 수행해야 \\$200의 무료 Azure 크레딧을 최대한 활용할 수 있습니다.

## 자습서에 필요한 Azure 리소스 프로비저닝

이 자습서는 "진입" 및 "진출" 데이터 스트림을 받기 위해 2개의 Azure 이벤트 허브가 필요합니다. Azure SQL 데이터베이스를 사용하여 스트림 분석 작업의 결과를 출력합니다. 또한 Azure 저장소를 사용하여 차량 등록에 대한 참조 데이터를 저장합니다.

GitHub에 있는 TollApp 폴더의 Setup.ps1 스크립트를 사용하여 필요한 모든 리소스를 만들 수 있습니다. 시간 형편상 이를 실행하는 것이 좋습니다. Azure 포털에서 이러한 리소스를 구성하는 방법에 대해 자세히 알아보려면 "Azure 포털에서 자습서 리소스 구성" 부록을 참조하세요.

[TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) 폴더 및 파일 지원을 다운로드하고 저장합니다.

"Microsoft Azure PowerShell" 창을 **관리자 권한으로** 엽니다. Azure PowerShell이 아직 없는 경우 [Azure PowerShell 설치 및 구성](../powershell-install-configure.md) 지침에 따라 설치합니다.

Windows가 인터넷에서 다운로드된 ps1, dll 및 exe 파일을 자동으로 차단합니다. 스크립트를 실행하려면 실행 정책을 설정해야 합니다. Azure PowerShell 창이 관리자 권한으로 실행되고 있는지 확인합니다. "Set-ExecutionPolicy unrestricted"를 실행합니다. 메시지가 표시되면 "Y"를 입력합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Get-ExecutionPolicy를 실행하여 명령이 작동하는지 확인합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

스크립트와 생성기 응용 프로그램이 있는 디렉터리로 이동합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

“.\\Setup.ps1”을 입력하여 azure 계정을 설정하고 필요한 모든 리소스를 만들고 구성한 다음 이벤트 생성을 시작합니다. 스크립트가 리소스를 만들 영역을 임의로 선택합니다. 명시적으로 영역을 지정하려면 아래 예제와 같이 -location 매개 변수를 전달하면 됩니다.

**.\\Setup.ps1 -location “Central US”**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

이 스크립트는 Microsoft Azure의 "로그인" 페이지를 엽니다. 계정 자격 증명을 입력합니다.

계정에 여러 구독에 대한 액세스 권한이 있는 경우 자습서에 사용하려는 구독 이름을 입력하라는 메시지가 표시됩니다.

스크립트를 실행하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 출력이 아래 스크린샷처럼 표시됩니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

아래 스크린샷과 비슷한 다른 창도 표시됩니다. 이 응용 프로그램에서 이벤트를 이벤트 허브에 전송하고 자습서의 연습을 실행해야 합니다. 따라서 자습서를 완료할 때까지 응용 프로그램을 중지하거나 이 창을 닫으면 안 됩니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

이제 만들어진 모든 리소스를 Azure 관리 포털에서 볼 수 있습니다. <https://manage.windowsazure.com>으로 이동하여 계정 자격 증명으로 로그인하세요.

### 이벤트 허브(영문)

이전 섹션의 스크립트에서 만든 이벤트 허브를 보려면 Azure 관리 포털의 왼쪽에 있는 "서비스 버스" 메뉴 항목을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

사용할 수 있는 모든 네임스페이스가 구독에 표시됩니다. "TollData"로 시작하는 항목(예제의 TollData4637388511)을 클릭합니다. "이벤트 허브" 탭을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

이 네임스페이스에서 만든 *진입*과 *진출*이라는 두 개의 이벤트 허브가 표시됩니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Azure 저장소 컨테이너

랩에서 사용되는 저장소 컨테이너를 보려면 Azure 관리 포털 왼쪽의 "저장소" 메뉴 항목을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

"tolldata"로 시작하는 항목(예제의 tolldata4637388511)을 클릭합니다. 만들어진 컨테이너를 보려면 "컨테이너" 탭을 엽니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

차량 등록 데이터가 들어 있는 업로드된 JSON 파일을 보려면 "tolldata" 컨테이너를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Azure SQL 데이터베이스

랩에서 사용될 Azure SQL 데이터베이스를 보려면 Azure 관리 포털의 왼쪽에 있는 "SQL 데이터베이스" 메뉴 항목을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

"TollDataDB"를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

포트 번호 없이 서버 이름(예: *servername*.database.windows.net)을 복사합니다.

## Visual Studio에서 데이터베이스에 연결

Visual Studio를 사용하여 출력 데이터베이스의 쿼리 결과에 액세스합니다.

Visual Studio에서 Azure 데이터베이스(대상)에 연결합니다.

1) Visual Studio를 열고 "도구"를 클릭한 다음 "데이터베이스에 연결..." 메뉴 항목을 클릭합니다.

2) 표시되는 메시지에 따라 "Microsoft SQL Server"를 데이터 원본으로 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) 서버 이름 필드에 Azure 포털에서 이전 섹션에서 복사한 SQL Server의 이름(예: *servername*.database.windows.net)을 붙여넣습니다.

4) 인증 필드에서 SQL Server 인증을 선택합니다.

5) 로그인 이름으로 "tolladmin"을 입력하고 로그인 암호로 "123toll!"를 입력합니다.

6) TollDataDB를 데이터베이스로 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)

7) 확인을 클릭합니다.

8) 서버 탐색기를 엽니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)

9) TollDataDB 데이터베이스에서 만들어진 4개의 테이블을 봅니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## 이벤트 생성기 - TollApp 샘플 프로젝트

PowerShell 스크립트는 TollApp 응용 프로그램 예제를 사용하여 이벤트 보내기를 자동으로 시작합니다. 추가 단계를 수행할 필요가 없습니다.

그러나 구현 세부 정보에 관심이 있는 경우 GitHub [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)에서 TollApp 응용 프로그램의 원본 코드를 찾을 수 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##스트림 분석 작업 만들기

Azure 포털에서 스트림 분석을 열고 페이지의 왼쪽 아래에 있는 "새로 만들기"를 클릭하여 새 분석 작업을 만듭니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

“빠른 생성”을 클릭합니다. 스크립트에서 다른 리소스가 만들어지는 동일한 영역을 선택합니다.

"지역별 모니터링 저장소 계정" 설정에 대해 "새 저장소 계정 만들기"를 선택하고 고유한 이름을 지정합니다. Azure 스트림 분석은 이 계정을 사용하여 이후의 모든 작업에 대해 모니터링 정보를 저장합니다.

페이지 맨 아래에 "스트림 분석 작업 만들기"를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## 입력 원본 정의

포털에서 만든 분석 작업을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

"입력" 탭을 열어 원본 데이터를 정의합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

"입력 추가"를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

첫 번째 페이지에서 "데이터 스트림"을 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

마법사의 두 번째 페이지에서 "이벤트 허브"를 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

입력 별칭으로 "EntryStream"을 입력합니다.

"이벤트 허브" 드롭다운을 클릭하고 "TollData"로 시작하는 항목(예: TollData9518658221)을 선택합니다.

이벤트 허브 이름으로 "진입"을 선택하고 이벤트 허브 정책 이름으로 "모두"를 선택합니다.

설정이 다음과 같이 표시됩니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

다음 페이지로 이동합니다. 값을 JSON, UTF8 인코딩으로 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

마법사를 완료하려면 대화 상자 맨 아래에서 확인을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

동일한 단계 시퀀스를 따라 진출 이벤트가 있는 스트림에 대해 두 번째 이벤트 허브 입력을 만들어야 합니다. 세 번째 페이지에서는 아래의 스크린샷대로 값을 입력해야 합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

이제 두 개의 입력 스트림을 정의했습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

다음은 차량 등록 데이터가 있는 blob 파일에 대해 "참조" 데이터 입력을 추가합니다.

"입력 추가"를 클릭합니다. "참조 데이터"를 선택합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

다음 페이지에서 "tolldata"로 시작하는 저장소 계정을 선택합니다. 컨테이너 이름이 "tolldata"여야 하고 패턴 경로의 blob 이름이 "registration.json"이어야 합니다. 이 파일 이름은 대/소문자를 구분하므로 모두 소문자여야 합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

다음 페이지에서 아래 표시된 것과 같이 값을 선택하고 확인을 클릭하여 마법사를 마칩니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

이제 모든 입력이 정의되었습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## 출력 정의

"출력" 탭으로 이동하고 "출력 추가"를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

"Sql 데이터베이스"를 선택합니다.

"Visual Studio에서 데이터베이스에 연결"에 사용된 서버 이름을 선택합니다. 데이터베이스 이름은 TollDataDB여야 합니다.

사용자 이름으로 “tolladmin”을 입력하고 암호로 “123toll!”를 입력합니다. 테이블 이름은 "TollDataRefJoin"으로 설정해야 합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Azure 스트림 분석 쿼리

쿼리 탭에는 들어오는 데이터에 대해 변환을 수행하는 SQL 쿼리가 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

이 자습서를 통해 요금 데이터와 관련된 몇 가지 비즈니스 질문에 대답하고 관련 대답을 제공하기 위해 Azure 스트림 분석에서 사용할 수 있는 스트림 분석 쿼리를 생성해봅니다.

첫 번째 Azure 스트림 분석 작업을 시작하기 전에 몇 가지 시나리오 및 쿼리 구문을 살펴보겠습니다.

## Azure 스트림 분석 쿼리 언어 소개
-----------------------------------------------------

요금 창구에 들어가는 차량 수를 계산해야 한다고 가정해봅시다. 이 작업은 연속 이벤트 스트림이므로 반드시 "기간"을 정의해야 합니다. 따라서 질문을 "3분 동안 요금 창구에 들어가는 차량 수"로 수정해야 합니다. 이를 일반적으로 연속 개수(Tumbling Count)라고 합니다.

이 질문에 대답하는 Azure 스트림 분석 쿼리를 살펴보겠습니다.

SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count FROM EntryStream TIMESTAMP BY EntryTime GROUP BY TUMBLINGWINDOW(minute, 3), TollId

여기에서 볼 수 있는 것처럼 Azure 스트림 분석은 쿼리에서 시간과 관련된 부분을 지정하기 위해 추가로 몇 가지의 확장을 가진 SQL 유사 쿼리 언어를 사용합니다.

자세한 내용을 보려면 MSDN의 쿼리에 사용된 [시간 관리](https://msdn.microsoft.com/library/azure/mt582045.aspx) 및 [창 작업](https://msdn.microsoft.com/library/azure/dn835019.aspx) 구문을 참조하세요.

## Azure 스트림 분석 쿼리 테스트

이제 첫 번째 Azure 스트림 분석 쿼리를 작성했으므로 아래 경로의 TollApp 폴더에 있는 샘플 데이터 파일을 사용하여 테스트해보겠습니다.

**..\\TollApp\\TollApp\\Data**

이 폴더에는 다음 파일이 포함되어 있습니다.

1.  Entry.json

2.  Exit.json

3.  Registration.json

## 질문 1 - 요금 창구에 들어가는 차량 수

Azure 관리 포털을 열고 만든 Azure 스트림 분석 작업으로 이동합니다. 쿼리 탭을 열고 이전 섹션에서 쿼리를 복사하여 붙여넣습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

샘플 데이터에 대해 이 쿼리의 유효성을 검사하려면 테스트 단추를 클릭합니다. 대화 상자가 열리면 EntryTime 이벤트 스트림의 샘플 데이터가 포함된 Entry.json 파일로 이동합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

쿼리의 출력이 예상대로인지 확인합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## 질문 2 - 각 차량이 요금 창구를 통과하는 총 시간 보고

효율성 및 고객 환경을 평가하기 위해 차량이 요금소를 통과하는 데 필요한 평균 시간을 구하려고 합니다.

이를 위해 EntryTime이 포함된 스트림과 ExitTime이 포함된 스트림을 조인해야 합니다. TollId 및 LicencePlate 열의 스트림을 조인하려고 합니다. JOIN 연산자에서는 조인된 이벤트 간에 허용할 수 있는 시간 차이를 설명하는 임시 해석 폭을 지정해야 합니다. DATEDIFF 함수를 사용하여 이벤트 사이의 간격이 15분 이하가 되도록 지정합니다. DATEDIFF 함수를 진출과 진입 시간에 적용하여 차량이 요금소에서 사용하는 실제 시간을 계산합니다. SELECT 문에서 DATEDIFF를 사용할 때 JOIN 조건에서 사용하는 것에 비해 어떤 차이가 있는지 적어둡니다.

SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes FROM EntryStream TIMESTAMP BY EntryTime JOIN ExitStream TIMESTAMP BY ExitTime ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate) AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

이 쿼리를 테스트하려면 작업의 쿼리 탭에서 쿼리를 업데이트합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

테스트를 클릭하고 EntryTime 및 ExitTime에 대한 샘플 입력 파일을 지정합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

쿼리를 테스트하고 출력을 보려면 확인란을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## 질문 3 – 등록 기간이 만료된 모든 화물 차량 보고

Azure 스트림 분석은 데이터의 정적 스냅숏을 사용하여 임시 데이터 스트림과 조인할 수 있습니다. 이 기능을 보여 주기 위해 다음 샘플 질문을 사용하겠습니다.

화물 차량이 요금 회사에 등록된 경우 검사받기 위해 정차하지 않고 요금 창구를 통과할 수 있습니다. 화물 차량 등록 조회 테이블을 사용하여 등록 기간이 만료된 모든 화물 차량을 식별할 수 있습니다.

SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId FROM EntryStream TIMESTAMP BY EntryTime JOIN Registration ON EntryStream.LicensePlate = Registration.LicensePlate WHERE Registration.Expired = '1'

참조 데이터를 사용하여 쿼리를 테스트하려면 5단계에서 수행한 것처럼 참조 데이터에 대한 입력 원본이 정의되어 있어야 합니다.

이 쿼리를 테스트하려면 쿼리 탭에 쿼리를 붙여넣고 테스트를 클릭한 다음 2개의 입력 원본을 지정합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

쿼리 출력을 확인합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## 스트림 분석 작업 시작


이제 첫 번째 Azure 스트림 분석 쿼리를 작성했으므로 구성을 완료하고 작업을 시작하겠습니다. 질문 3의 쿼리를 저장하면 출력 테이블 **TollDataRefJoin**의 스키마와 일치하는 출력이 생성됩니다.

작업 대시보드로 가서 시작을 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

대화 상자가 나타나면 시작 출력 시간을 사용자 지정 시간으로 변경합니다. 시간을 편집하여 한 시간 뒤로 설정합니다. 그러면 자습서의 시작 부분에서 이벤트를 생성하기 시작한 시점 이후 이벤트 허브의 모든 이벤트를 처리합니다. 이제 확인 표시를 클릭하여 작업을 시작합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

작업을 시작하는 데 몇 분 정도 걸릴 수 있습니다. 스트림 분석의 최상위 페이지에서 상태를 볼 수 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## Visual Studio에서 결과 확인

Visual Studio 서버 탐색기를 열고 TollDataRefJoin 테이블을 마우스 오른쪽 단추로 클릭합니다. "테이블 데이터 표시"를 선택하면 작업의 출력을 볼 수 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## Azure 스트림 분석 작업 규모 확장

Azure 스트림 분석은 탄력적으로 크기를 조정하여 높은 부하의 데이터를 처리할 수 있도록 디자인되었습니다. Azure 스트림 분석 쿼리에서 **PARTITION BY** 절을 사용하면 이 단계에서 확장하는 시스템을 알 수 있습니다. PartitionId는 입력(이벤트 허브)의 파티션 ID와 일치하는 시스템에서 추가한 특수 열입니다.

SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

현재 작업을 중지하고 쿼리 탭에서 쿼리를 업데이트하고 크기 조정 탭을 엽니다.

스트리밍 단위는 작업이 검색할 수 있는 계산 능력의 크기를 정의합니다.

슬라이더를 6으로 이동합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

출력 탭으로 이동하고 SQL 테이블 이름을 "TollDataTumblingCountPartitioned"로 변경하세요.

이제 작업을 시작하면 Azure 스트림 분석이 더 많은 계산 리소스에 작업을 배포하고 처리량을 높일 수 있습니다. TollApp 응용 프로그램이 TollId로 분할된 이벤트를 보내고 있다는 것에 유의하세요.

## 모니터링

모니터링 탭에는 실행 중인 작업에 대한 통계가 들어 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

대시보드 탭에서 작업 로그에 액세스할 수 있습니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

특정 이벤트에 대한 추가 정보를 보려면 이벤트를 선택하고 "세부 정보" 단추를 클릭합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## 결론

이 자습서에서는 Azure 스트림 분석 서비스에 대해 소개합니다. 스트림 분석 작업에 대해 입력 및 출력을 구성하는 방법을 보여 줍니다. 지금까지 요금 데이터 시나리오를 통해 동작 중인 데이터 공간에서 발생하는 일반적인 문제 유형과 Azure 스트림 분석에서 간단한 SQL 유사 쿼리를 사용하여 이러한 문제를 해결하는 방법을 설명했습니다. 임시 데이터 작업을 위한 SQL 확장 구성을 설명했습니다. 데이터 스트림을 조인하는 방법 및 정적 참조 데이터를 사용하여 데이터 스트림을 보강하는 방법을 설명했습니다. 처리량을 높일 수 있도록 쿼리를 확장하는 방법을 설명했습니다.

이 자습서가 개요에 해당하는 내용을 잘 소개하고는 있지만 완전하지는 않습니다. [여기](stream-analytics-stream-analytics-query-patterns.md)에 있는 SAQL 언어를 사용하여 더 많은 쿼리 패턴을 찾을 수 있습니다. Azure 스트림 분석에 대한 자세한 내용을 알아보려면 [온라인 설명서](https://azure.microsoft.com/documentation/services/stream-analytics/)를 참조하세요.

## Azure 계정 정리

Azure 포털에서 스트림 분석 작업을 중지하세요.

Setup.ps1 스크립트는 2개의 Azure 이벤트 허브 및 Azure SQL 데이터베이스 서버를 만듭니다. 다음 지침을 통해 자습서 끝부분에서 리소스를 정리할 수 있습니다.

PowerShell 창에서 ".\\Cleanup.ps1"을 입력합니다. 그러면 자습서에서 사용한 리소스를 삭제하는 스크립트가 시작됩니다.

리소스는 이름으로 식별됩니다. 제거를 확인하기 전에 각 항목을 신중하게 검토해야 합니다.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0914_2016-->