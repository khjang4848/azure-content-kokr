<properties
   pageTitle="Azure 데이터 카탈로그 릴리스 정보 | Microsoft Azure"
   description="Azure Data Catalog에 대한 릴리스 정보입니다."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# Azure 데이터 카탈로그 릴리스 정보

## Azure 데이터 카탈로그의 2015년 11월 20일 릴리스 정보

### Power BI Desktop에서 데이터 원본 열기

**Azure 데이터 카탈로그** 포털에서 "Power BI Desktop에서 열기" 옵션을 사용할 때 Power BI Desktop 응용 프로그램에서 다음 두 가지 문제 중 하나가 발생할 수 있습니다.

- "문서를 열 수 없습니다."라는 제목의 대화 상자가 표시됩니다.
- Power BI Desktop 응용 프로그램이 열리지만 파일이 비어 있는 것으로 표시됩니다.

각 경우에 대해 [PowerBI.com](https://powerbi.com)에서 최신 버전의 Power BI Desktop을 다운로드 및 설치하여 문제를 해결할 수 있습니다.

## Azure 데이터 카탈로그의 2015년 11월 13일 릴리스 정보

### Teradata에 등록 및 연결

Teradata 데이터 원본에 연결 시 사용자는 사용할 소프트웨어의 비트 수(32비트 또는 64비트)와 일치하는 올바른 Teradata ODBC 드라이버를 설치해야 합니다.

이 ADC 릴리스 날짜 현재, 최신 [windows용 Teradata ODBC 드라이버(버전 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows)는 Office 2013과 호환되지만 Office 2016과는 호환되지 않습니다.

## Azure 데이터 카탈로그의 2015년 7월 13일 릴리스 정보

### Oracle 데이터베이스에 등록 및 연결

Oracle 데이터베이스 데이터 원본에 연결 시 사용자는 사용할 소프트웨어의 비트 수(32비트 또는 64비트)와 일치하는 올바른 Oracle 드라이버를 설치해야 합니다.

-	32비트 Windows를 실행하는 컴퓨터에서 Oracle 데이터 원본 등록 시 32비트 Oracle 드라이버가 사용됨
-	64비트 Windows를 실행하는 컴퓨터에서 Oracle 데이터 원본 등록 시 64비트 Oracle 드라이버가 사용됨
-	64비트 Windows를 비롯하여 Microsoft Office 32비트 버전을 실행하는 컴퓨터에서 Excel을 사용하여 Oracle 데이터 원본에 연결 시 32비트 Oracle 드라이버가 사용됨
-	Microsoft Office 64비트 버전을 실행하는 컴퓨터에서 Excel을 사용하여 Oracle 데이터 원본에 연결 시 64비트 Oracle 드라이버가 사용됨

### SQL Server Reporting Services에 등록 및 연결

SSRS(SQL Server Reporting Services) 데이터 원본은 현재 기본 모드 서버로만 제한됩니다. SharePoint 모드에서 SSRS 지원은 이후 릴리스에 추가됩니다.

### Excel에서 데이터 자산 열기

**Azure 데이터 카탈로그** 포털에서 Microsoft Excel의 데이터 자산을 열 때 **Microsoft Excel 보안 공지** 대화 상자가 표시될 수 있습니다. 이는 예상된 표준 동작입니다. 사용자는 **사용**을 선택하여 계속할 수 있습니다.

자세한 내용은 [의심스러운 웹 사이트의 링크 및 파일에 대한 보안 경고 사용 또는 사용 안 함](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)을 참조하세요.

### 프록시 및 정책 구성과 데이터 원본 등록

Azure 데이터 카탈로그 포털에 로그온할 수 있는 상황이 발생할 수 있지만, 데이터 원본 등록 도구에 로그온을 시도할 때 로그온하지 않도록 하는 오류 메시지가 발생합니다.

이 문제 동작에는 다음과 같은 잠재적인 두 가지 원인이 있습니다.

**원인 1: Active Directory Federation Services 구성** 데이터 원본 등록 도구가 폼 인증을 사용하여 Active Directory에 대한 사용자 로그온의 유효성 검사를 수행합니다. 로그온이 성공하려면 Active Directory 관리자가 전역 인증 정책에서 폼 인증을 사용하도록 설정해야 합니다.

일부 상황에서 이 오류 동작은 사용자가 회사 네트워크에 있을 때만 또는 회사 네트워크 외부에서 연결할 때만 발생할 수 있습니다. 전역 인증 정책을 사용하면 인트라넷 및 엑스트라넷 연결을 위해 인증 방법을 개별적으로 사용하도록 설정할 수 있습니다. 폼 인증이 사용자가 연결되는 네트워크에 사용하도록 설정되지 않은 경우 로그온 오류가 발생할 수 있습니다.

자세한 내용은 [인증 정책 구성](https://technet.microsoft.com/library/dn486781.aspx)을 참조하세요.

**원인 2: 네트워크 프록시 구성** 회사 네트워크가 프록시 서버를 사용하는 경우 등록 도구가 프록시를 통해 Azure Active Directory에 연결하지 못할 수도 있습니다. 도구의 구성 파일을 편집하고 이 섹션을 파일에 추가하여 사용자가 등록 도구를 확인할 수 있습니다.


	  <system.net>
	    <defaultProxy useDefaultCredentials="true" enabled="true">
	      <proxy usesystemdefault="True"
	                      proxyaddress="http://<your corporate network proxy url>"
	                      bypassonlocal="True"/>
	    </defaultProxy>
	  </system.net>


RegistrationTool.exe.config 파일을 찾으려면 등록 도구를 시작하여 Windows 작업 관리자 유틸리티를 엽니다. 작업 관리자의 세부 정보 탭에서 RegistrationTool.exe를 마우스 오른쪽 단추로 클릭하고 팝업 메뉴에서 파일 위치 열기를 선택합니다.

<!---HONumber=AcomDC_0921_2016-->