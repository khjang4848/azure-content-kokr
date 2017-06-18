<properties
	pageTitle="스트레치 마법사에 대해 데이터베이스 사용을 실행하여 시작하기 | Microsoft Azure"
	description="스트레치에 데이터베이스 사용 마법사를 실행하여 스트레치 데이터베이스에 대해 데이터베이스를 구성하는 방법을 알아봅니다."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# 스트레치 마법사에 대한 데이터베이스 사용을 실행하여 시작

스트레치 데이터베이스에 대해 데이터베이스를 구성하려면 스트레치에 데이터베이스 사용 마법사를 실행합니다. 이 항목에서는 마법사에서 입력해야 하는 정보 및 선택해야 하는 항목을 설명합니다.

스트레치 데이터베이스에 대한 자세한 내용은 [스트레치 데이터베이스](sql-server-stretch-database-overview.md)를 참조하세요.

 >   [AZURE.NOTE] 나중에 스트레치 데이터베이스를 비활성화하는 경우 테이블 또는 데이터베이스에 대해 확장 데이터베이스를 사용하지 않도록 설정하면 원격 개체를 삭제하지 않습니다. 원격 테이블 또는 원격 데이터베이스를 삭제하려면 Azure 관리 포털을 사용하여 삭제해야 합니다. 원격 개체를 수동으로 삭제할 때까지 계속해서 Azure 비용이 발생합니다.

## 마법사 시작

1.  SQL Server Management Studio의 개체 탐색기에서 스트레치를 사용하도록 설정할 데이터베이스를 선택합니다.

2.  마우스 오른쪽 단추를 클릭하고 **작업**, **스트레치**, **사용**을 차례로 선택하여 마법사를 시작합니다.

## <a name="Intro"></a>소개
마법사의 용도 및 필수 조건을 검토합니다.

중요한 필수 구성 요소는 다음과 같습니다.

-   데이터베이스 설정을 변경하려면 관리자여야 합니다.
-   Microsoft Azure 구독을 보유해야 합니다.
-   SQL Server에서 원격 Azure 서버와 통신할 수 있어야 합니다.

![스트레치 데이터베이스 마법사의 소개 페이지][StretchWizardImage1]

## <a name="Tables"></a>테이블 선택
스트레치에 사용할 테이블을 선택합니다.

행이 많은 테이블이 정렬된 목록의 위쪽에 나타납니다. 마법사는 테이블 목록을 표시하기 전에 스트레치 데이터베이스에서 현재 지원되지 않는 데이터 형식에 대해 분석합니다.

![스트레치 데이터베이스 마법사의 테이블 페이지 선택][StretchWizardImage2]

|열|설명|
|----------|---------------|
|(제목 없음)|선택한 테이블에서 스트레치를 사용하려면 이 열의 확인란을 선택합니다.|
|**Name**|테이블의 열 이름을 지정합니다.|
|(제목 없음)|이 칼럼의 기호는 스트레치에 대해 선택한 테이블 활성화를 방지하지 않는 경고를 나타낼 수 있습니다. 스트레치에 대해 선택한 테이블 활성화를 방지하는 차단 문제를 나타낼 수도 있습니다. 예를 들어 테이블이 지원되지 않는 데이터 형식을 사용하기 때문입니다. 기호 위에 마우스를 두면 도구 설명에 자세한 내용이 표시됩니다. 자세한 내용은 [스트레치 데이터베이스에 대한 제한](sql-server-stretch-database-limitations.md)을 참조하세요.|
|**확대**|테이블이 스트레치에 대해 이미 사용하도록 설정되었는지 여부를 나타냅니다.|
|**마이그레이션**|전체 테이블(**전체 테이블**)을 마이그레이션하거나 테이블의 기존 열에 필터를 지정할 수 있습니다. 다른 필터 함수를 사용하여 마이그레이션하려는 행을 선택하려는 경우 ALTER TABLE 문을 실행하여 마법사를 종료한 후 필터 함수를 지정합니다. 필터 함수에 대한 자세한 내용은 [필터 함수를 사용하여 마이그레이션할 행 선택](sql-server-stretch-database-predicate-function.md)을 참조하세요. 함수를 적용하는 방법에 대한 자세한 내용은 [테이블에 스트레치 데이터베이스 사용](sql-server-stretch-database-enable-table.md) 또는 [ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)을 참조하세요.|
|**행**|테이블의 행 수를 지정합니다.|
|**크기(KB)**|테이블의 크기를 KB로 지정합니다.|

## <a name="Filter"></a>필요에 따라 행 필터 제공

필터 함수를 제공하여 마이그레이션하려는 행을 선택하려는 경우 **테이블 선택** 페이지에서 다음 작업을 수행합니다.

1.  **확장하려는 테이블 선택** 목록의 테이블에 대한 행에서 **전체 테이블**을 클릭합니다. **확장하려는 행 선택** 대화 상자가 열립니다.

    ![필터 함수 정의][StretchWizardImage2a]

2.  **확장하려는 행 선택** 대화 상자에서 **행 선택**을 선택합니다.

3.  **이름 필드**에서 필터 함수에 대한 이름을 제공합니다.

4.  **Where** 절의 경우 테이블에서 열을 선택하고 연산자를 선택한 다음 값을 제공합니다.

5. **확인**을 클릭하여 함수를 테스트합니다. 함수가 테이블에서 결과를 반환하는 경우 즉, 조건을 만족하는 마이그레이션할 행이 있는 경우 테스트는 **성공**을 보고합니다.

    >   [AZURE.NOTE] 필터 쿼리를 표시하는 텍스트 상자는 읽기 전용입니다. 텍스트 상자에서 쿼리를 편집할 수 없습니다.

6.  완료를 클릭하여 **테이블 선택** 페이지로 돌아갑니다.

필터 함수는 마법사를 완료하는 경우에 SQL Server에서 생성됩니다. 그때까지는 **테이블 선택** 페이지로 돌아가서 필터 함수를 변경하거나 이름을 변경할 수 있습니다.

![필터 함수 정의 후 테이블 페이지 선택][StretchWizardImage2b]

다른 종류의 필터 함수를 사용하여 마이그레이션할 행을 선택하려면 다음 중 하나를 수행합니다.

-   마법사를 종료하고 ALTER TABLE 문을 실행하여 테이블에 대한 스트레치를 사용하도록 설정하고 필터 함수를 지정합니다. 자세한 내용은 [테이블에 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md)을 참조하세요.

-   마법사를 종료한 후 ALTER TABLE 문을 실행하여 필터 함수를 지정합니다. 필요한 단계는 [마법사를 실행한 후 필터 함수 추가](sql-server-stretch-database-predicate-function.md#addafterwiz)를 참조하세요.

## <a name="Configure"></a>Azure 배포 구성

1.  Microsoft 계정을 사용하여 Microsoft Azure에 로그인합니다.

    ![Azure에 로그인 - 스트레치 데이터베이스 마법사][StretchWizardImage3]

2.  스트레치 데이터베이스에 사용할 기존 Azure 구독을 선택합니다.

3.  Azure 지역을 선택합니다.
    -   새 서버를 만드는 경우 이 지역에 서버가 만들어집니다.
    -   선택한 지역에 기존 서버가 있는 경우 **기존 서버**를 선택하면 마법사는 해당 내용을 나열합니다.

    대기 시간을 최소화하려면 SQL Server가 있는 Azure 지역을 선택합니다. 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.

4.  기존 서버를 사용할지 또는 새 Azure 서버를 만들지 지정합니다.

    SQL Server의 Active Directory가 Azure Active Directory와 페더레이션된 경우 선택적으로 SQL Server에 대해 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신할 수 있습니다. 이 옵션을 위한 요구 사항에 대한 자세한 내용은 [ALTER DATABASE SET 옵션(Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)을 참조하세요.

	-   **새 서버 만들기**

        1.  서버 관리자의 로그인 및 암호를 만듭니다.

        2.  필요에 따라 SQL Server에 대해 페더레이션된 서비스 계정을 사용하여 원격 Azure 서버와 통신할 수 있습니다.

		![새 Azure 서버 만들기 - 스트레치 데이터베이스 마법사][StretchWizardImage4]

    -   **기존 서버**

        1.  기존 Azure 서버를 선택합니다.

        2.  인증 방법을 선택합니다.

            -   **SQL Server 인증**을 선택하는 경우 관리자 로그인 및 암호를 제공합니다.

            -   **Active Directory 통합 인증**을 선택하여 원격 Azure 서버와 통신하도록 SQL Server에 대한 페더레이션 서비스 계정을 사용합니다. 선택한 서버가 Azure Active Directory와 통합되지 않는 경우 이 옵션이 표시되지 않습니다.

		![기존 Azure 서버 선택 - 스트레치 데이터베이스 마법사][StretchWizardImage5]

## <a name="Credentials"></a>보안 자격 증명
스트레치 데이터베이스가 원격 데이터베이스에 연결하는 데 사용하는 자격 증명을 보호하려면 데이터베이스 마스터 키가 있어야 합니다.

데이터베이스 마스터 키가 이미 있는 경우 암호를 입력합니다.

![스트레치 데이터베이스 마법사의 보안 자격 증명 페이지][StretchWizardImage6b]

데이터베이스에 기존 마스터 키가 없는 경우 강력한 암호를 입력하여 데이터베이스 마스터 키를 만듭니다.

![스트레치 데이터베이스 마법사의 보안 자격 증명 페이지][StretchWizardImage6]

데이터베이스 마스터 키에 대한 자세한 내용은 [마스터 키 만들기(Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) 및 [데이터베이스 마스터 키](https://msdn.microsoft.com/library/aa337551.aspx)를 참조하세요. 마법사에서 만들어지는 자격 증명에 대한 자세한 내용은 [데이터베이스 범위 자격 증명 만들기(Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)를 참조하세요.

## <a name="Network"></a>IP 주소 선택
서브넷 IP 주소 범위(권장) 또는 SQL Server의 공용 IP 주소를 사용하여 SQL Serve에서 원격 Azure 서버와 통신할 수 있도록 해주는 Azure 방화벽 규칙을 만듭니다.

이 페이지에서 제공하는 IP 주소 또는 주소는 SQL Server에서 시작된 들어오는 데이터, 쿼리 및 관리 작업을 허용하도록 Azure 서버에 지시하여 Azure 방화벽을 통과합니다. 마법사는 SQL Server의 방화벽 설정에서 아무 것도 변경하지 않습니다.

![스트레치 데이터베이스 마법사의 IP 주소 페이지 선택][StretchWizardImage7]

## <a name="Summary"></a>요약
입력한 값과 마법사에서 선택한 옵션 및 Azure의 예상 비용을 검토합니다. 그런 다음 **마침**을 선택하여 스트레치를 사용하도록 설정합니다.

![스트레치 데이터베이스 마법사의 요약 페이지][StretchWizardImage8]

## <a name="Results"></a>결과
결과를 검토합니다.

데이터 마이그레이션의 상태를 모니터링하려면 [데이터 마이그레이션 모니터링 및 문제 해결(스트레치 데이터베이스)](sql-server-stretch-database-monitor.md)을 참조하세요.

![스트레치 데이터베이스 마법사의 결과 페이지][StretchWizardImage9]

## <a name="KnownIssues"></a>마법사 문제 해결
**스트레치 데이터베이스 마법사에 실패했습니다.** 스트레치 데이터베이스가 아직 서버 수준에서 사용하도록 설정되지 않은 경우 이를 사용하도록 설정하기 위해 시스템 관리자 권한 없이 마법사를 실행하면 마법사에 실패합니다. 시스템 관리자에게 로컬 서버 인스턴스에서 스트레치 데이터베이스를 사용하도록 설정해 달라고 요청한 다음 마법사를 다시 실행합니다. 자세한 내용은 [필수 구성 요소: 서버에서 스트레치 데이터베이스를 사용할 수 있는 권한](sql-server-stretch-database-enable-database.md#EnableTSQLServer)을 참조하세요.

## 다음 단계
스트레치 데이터베이스에 추가 테이블 사용 데이터 마이그레이션 모니터링 및 스트레치 지원 데이터베이스 및 테이블 관리

-   [테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md)하여 추가 테이블 사용

-   [데이터 마이그레이션을 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md)하여 데이터 마이그레이션의 상태 보기

-   [스트레치 데이터베이스 일시 중지 및 다시 시작](sql-server-stretch-database-pause.md)

-   [스트레치 데이터베이스 관리 및 문제 해결](sql-server-stretch-database-manage.md)

-   [스트레치 지원 데이터베이스 백업](sql-server-stretch-database-backup.md)

## 참고 항목

[데이터베이스에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-database.md)

[테이블에 대해 스트레치 데이터베이스를 사용하도록 설정](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

<!-----HONumber=AcomDC_0810_2016-->