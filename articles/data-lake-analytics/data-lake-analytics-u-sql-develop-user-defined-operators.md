<properties 
   pageTitle="Azure 데이터 레이크 분석 작업에 대한 U-SQL 사용자 정의 연산자를 개발 l Azure" 
   description="데이터 레이크 분석 작업에 사용 및 재사용되는 사용자 정의 연산자를 개발하는 방법에 대해 알아봅니다. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# Azure 데이터 레이크 분석 작업에 대한 U-SQL 사용자 정의 연산자를 개발합니다.

데이터 레이크 분석 작업에 사용 및 재사용되는 사용자 정의 연산자를 개발하는 방법에 대해 알아봅니다. 국가 이름을 변환하는 사용자 지정 연산자를 개발합니다.

##필수 조건

- Visual Studio 2015, Visual Studio 2013 update 4, Visual C++가 설치된 Visual Studio 2012
- Microsoft Azure SDK for.NET 버전 2.5 이상. 웹 플랫폼 설치 관리자를 사용하여 설치합니다.
- 데이터 레이크 분석 계정입니다. [Azure 포털을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-portal.md)을 참조하세요.
- [AZure 데이터 레이크 분석 U-SQL 스튜디오 시작하기](data-lake-analytics-u-sql-get-started.md) 자습서를 살펴보세요.
- Azure 연결은 [Azure 데이터 레이크 분석 U-SQL 스튜디오 시작하기](data-lake-analytics-u-sql-get-started.md#connect-to-azure)를 참조하세요 .
- 원본 데이터 업로드는 [Azure 데이터 레이크 분석 U-SQL 스튜디오 시작하기](data-lake-analytics-u-sql-get-started.md#upload-source-data-files)를 참조하세요.

## U-SQL에서 사용자 정의 연산자를 정의하고 사용합니다.

**U-SQL 작업을 만들고 제출하기**

1. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
2. **U-SQL 프로젝트** 유형을 선택합니다.

	![새 U-SQL Visual Studio 프로젝트](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. **확인**을 클릭합니다. Visual Studio에서 Script.usql 파일로 솔루션을 만듭니다.
4. **솔루션 탐색기**에서 Script.usql을 확장하고 **Script.usql.cs**를 두 번 클릭합니다.
5. 다음 코드를 파일에 붙여 넣습니다.

		using Microsoft.Analytics.Interfaces;
		using System.Collections.Generic;
		
		namespace USQL_UDO
		{
			public class CountryName : IProcessor
			{
				private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
				{
					{
						"Deutschland", "Germany"
					},
					{
						"Schwiiz", "Switzerland"
					},
					{
						"UK", "United Kingdom"
					},
					{
						"USA", "United States of America"
					},
					{
						"中国", "PR China"
					}
				};
		
				public override IRow Process(IRow input, IUpdatableRow output)
				{
		
					string UserID = input.Get<string>("UserID");
					string Name = input.Get<string>("Name");
					string Address = input.Get<string>("Address");
					string City = input.Get<string>("City");
					string State = input.Get<string>("State");
					string PostalCode = input.Get<string>("PostalCode");
					string Country = input.Get<string>("Country");
					string Phone = input.Get<string>("Phone");
		
					if (CountryTranslation.Keys.Contains(Country))
					{
						Country = CountryTranslation[Country];
					}
					output.Set<string>(0, UserID);
					output.Set<string>(1, Name);
					output.Set<string>(2, Address);
					output.Set<string>(3, City);
					output.Set<string>(4, State);
					output.Set<string>(5, PostalCode);
					output.Set<string>(6, Country);
					output.Set<string>(7, Phone);
		
					return output.AsReadOnly();
				}
			}
		}

5. Script.usql를 열고 다음 U SQL 스크립트를 붙여넣습니다.

		@drivers =
			EXTRACT UserID      string,
					Name        string,
					Address     string,
					City        string,
					State       string,
					PostalCode  string,
					Country     string,
					Phone       string
			FROM "/Samples/Data/AmbulanceData/Drivers.txt"
			USING Extractors.Tsv(Encoding.Unicode);
		
		@drivers_CountryName =
			PROCESS @drivers
			PRODUCE UserID string,
					Name string,
					Address string,
					City string,
					State string,
					PostalCode string,
					Country string,
					Phone string
			USING new USQL_UDO.CountryName();    
		
		OUTPUT @drivers_CountryName
			TO "/Samples/Outputs/Drivers.csv"
			USING Outputters.Csv(Encoding.Unicode);

6. **솔루션 탐색기**에서 **Script.usql**을 마우스 오른쪽 클릭하고 **빌드 스크립트**를 클릭합니다.
6. **솔루션 탐색기**에서 **Script.usql**을 마우스 오른쪽 클릭하고 **스크립트 제출**을 클릭합니다.
7. Azure 구독에 연결하지 않았다면 Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.
7. **Submit**를 클릭합니다. 제출이 완료되면 결과 창에서 제출 결과 및 작업 링크를 사용할 수 있습니다.
8. 최근 작업 상태와 화면을 새로 고치려면 새로 고침 버튼을 클릭하세요.

**작업 출력 보기**

1. **서버 탐색기**, **Azure** 확장, **데이터 레이크 분석** 확장, 사용자 데이터 레이크 분석 계정 확장, **저장소 계정** 확장에서 기본 저장소를 오른 클릭하고 **탐색기**를 클릭합니다.
2. 샘플 및 출력을 확장하고 **Drivers.csv**를 두 번 클릭합니다.


##참고 항목

- [PowerShell을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-powershell.md)
- [Azure 포털을 사용하여 데이터 레이크 분석 시작하기](data-lake-analytics-get-started-portal.md)
- [U-SQL 응용 프로그램 개발에 Visual Studio용 데이터 레이크 도구 사용하기](data-lake-analytics-data-lake-tools-get-started.md)

<!---HONumber=AcomDC_0914_2016-->