<properties
	pageTitle="스트림 분석 Data Lake 저장소 출력 | Microsoft Azure"
	description="스트림 분석 작업에서 Azure Data Lake 저장소의 인증 및 권한 부여 구성"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/30/2016"
	ms.author="jeffstok"
/>

# 스트림 분석 Data Lake 저장소 출력

스트림 분석 작업은 여러 출력 메서드를 지원하며 그 중 하나는 [Azure Data Lake 저장소](https://azure.microsoft.com/services/data-lake-store/)입니다. Azure 데이터 레이크 저장소는 빅 데이터 분석 작업을 위한 엔터프라이즈 수준 하이퍼 스케일 리포지토리입니다. Data Lake 저장소를 사용하면 작동 및 예비 분석에 대해 모든 크기, 형식 및 수집 속도의 데이터를 저장할 수 있습니다.

## Data Lake 저장소 계정 권한 부여

1.  Data Lake 저장소를 Azure 관리 포털에서 출력으로 선택하는 경우 기존 Data Lake 저장소의 사용 권한을 부여하거나 Azure 클래식 포털을 통해 Data Lake 저장소 미리 보기에 대한 액세스를 요청하는 메시지가 표시됩니다.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)

2.  Data Lake 저장소에 대한 액세스 권한이 이미 있는 경우 "지금 권한 부여"를 클릭하고 "권한 부여로 리디렉션..."을 나타내는 페이지가 잠깐 나타납니다. 페이지가 자동으로 닫히고 Data Lake 저장소 출력을 구성할 수 있도록 하는 페이지와 함께 나타납니다.

Data Lake Store Preview에 등록하지 않은 경우 "지금 등록" 링크를 클릭하여 등록 페이지로 이동해 요청을 시작하거나 [시작 지침](../data-lake-store/data-lake-store-get-started-portal.md)을 따릅니다.

## Data Lake 저장소 출력 속성 구성

Data Lake 저장소 계정을 인증하면 사용자가 Data Lake 저장소 출력에 대한 속성을 구성할 수 있습니다. 다음 테이블은 속성 이름 및 해당 설명의 목록으로 Data Lake 저장소 출력을 구성합니다.

<table>
<tbody>
<tr>
<td><B>속성 이름</B></td>
<td><B>설명</B></td>
</tr>
<tr>
<td>출력 별칭</td>
<td>쿼리 출력을 이 Data Lake 저장소로 직접 보내기 위해 쿼리에서 사용되는 식별 이름입니다.</td>
</tr>
<tr>
<td>Data Lake 저장소 계정</td>
<td>출력을 보내는 저장소 계정의 이름 포털에 로그인한 사용자가 액세스할 수 있는 Data Lake 저장소 계정이 드롭다운 목록으로 나타납니다.</td>
</tr>
<tr>
<td>경로 접두사 패턴[<I>선택 사항</I>]</td>
<td>지정된 Data Lake 저장소 계정 내에서 파일을 작성하는 데 사용되는 파일 경로입니다. <BR>{date}, {time}<BR>예제 1: folder1/logs/{date}/{time}<BR>예제 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>날짜 형식[<I>선택 사항</I>]</td>
<td>접두사 경로에 날짜 토큰을 사용하는 경우 파일을 구성하는 날짜 형식을 선택할 수 있습니다. 예: YYYY/MM/DD</td>
</tr>
<tr>
<td>시간 형식[<I>선택 사항</I>]</td>
<td>접두사 경로에 시간 토큰을 사용하는 경우 파일을 구성하는 시간 형식을 지정합니다. 현재 지원되는 유일한 값은 HH입니다.</td>
</tr>
<tr>
<td>이벤트 직렬화 형식</td>
<td>출력 데이터에 대한 직렬화 형식입니다. JSON, CSV, 및 Avro를 지원합니다.</td>
</tr>
<tr>
<td>인코딩</td>
<td>CSV 또는 JSON 형식인 경우 인코딩을 지정해야 합니다. 지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.</td>
</tr>
<tr>
<td>구분 기호</td>
<td>CSV 직렬화에만 적용됩니다. 스트림 분석은 CSV 데이터를 직렬화하기 위해 다양하고 일반적인 구분 기호를 지원합니다. 지원되는 값은 쉼표, 세미콜론, 공백, 탭 및 세로 막대입니다.</td>
</tr>
<tr>
<td>형식</td>
<td>JSON 직렬화에만 적용됩니다. 구분된 줄은 출력이 각 JSON 개체를 새 줄로 구분된 형식이 되도록 지정합니다. 배열은 출력의 형식을 JSON 개체의 배열로 지정합니다.</td>
</tr>
</tbody>
</table>

## Data Lake 저장소 권한 부여 갱신

현재 Data Lake 저장소 출력을 포함하는 모든 작업에 대해 90일 마다 인증 토큰을 수동으로 새로 고쳐야 하는 제한 사항이 있습니다. 또한 작업을 만들거나 마지막으로 인증한 후에 암호를 변경한 경우 Data Lake 저장소 계정을 다시 인증해야 합니다. 이 문제의 증상은 다시 인증이 필요하다고 나타내는 작업 로그의 작업 출력 및 오류입니다.

이 문제를 해결하려면 실행 중인 작업을 중지하고 Data Lake 저장소 출력으로 이동합니다. "권한 부여 갱신" 링크를 클릭하면 "권한 부여로 리디렉션..."을 나타내는 페이지가 잠깐 나타납니다. 페이지가 자동으로 닫히고 성공하면 "권한 부여를 성공적으로 갱신했습니다"가 표시됩니다. 페이지의 맨 아래에서 "저장"을 클릭해야 하고 데이터 손실을 방지하도록 마지막 중지 시간에서 작업을 다시 시작하여 진행할 수 있습니다.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

<!---HONumber=AcomDC_0914_2016-->