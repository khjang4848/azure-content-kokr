<properties
	pageTitle="Azure 가상 컴퓨터에서 SQL Server 개요 | Microsoft Azure"
	description="Azure 가상 컴퓨터에서 전체 SQL Server 버전을 실행하는 방법을 알아봅니다. 모든 SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/21/2016"
	ms.author="jroth"/>

# Azure 가상 컴퓨터에서 SQL Server 개요

이 항목에서는 [포털 이미지에 대한 링크](#option-1-deploy-a-sql-vm-per-minute-licensing) 및 [일반적인 작업](#manage-your-sql-vm)의 개요와 함께 Azure 가상 컴퓨터에 SQL Server를 실행하는 옵션을 설명합니다.

>[AZURE.NOTE] SQL Server에 이미 익숙하고 SQL Server VM을 배포하는 방법을 참조하려는 경우 [Azure 포털에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

## 개요
온-프레미스 SQL Server 작업을 클라우드로 이동하려는 데이터베이스 관리자일 수도 있습니다. 또는 Azure 응용 프로그램에 대한 SQL Server의 관계형 데이터베이스 기능을 고려하는 개발자일 수도 있습니다. Azure 가상 컴퓨터에서 SQL Server 작업을 실행하는 이점은 무엇입니까? 다음 개요 비디오는 이점을 설명하고 기술적 개요를 제공합니다.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## 이점 평가

시작하기 전에 먼저 Azure VM에서 SQL Server를 사용하여 얻을 수 있는 것을 평가합니다.

엔터프라이즈 응용 프로그램과 같은 다른 작업을 Azure로 이동할 경우 성능 향상을 위해 Azure에 모든 종속 SQL Server 데이터베이스를 이동할 수도 있습니다. 하지만 Azure VM에서 SQL Server를 호스트하면 다른 이점도 제공합니다. 예를 들어 글로벌 서비스 및 재해 복구를 위해 여러 데이터 센터에 대한 액세스를 자동으로 가집니다. 시나리오 및 이점의 전체 목록은 [Azure VM 제품 페이지의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)를 참조하세요.

> [AZURE.NOTE] Azure VM에서 SQL Server를 평가하고 Azure에서 [SQL 데이터베이스](../sql-database/sql-database-technical-overview.md), [SQL 데이터 웨어하우스](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 및 [SQL Server 스트레치 데이터베이스](../sql -server-stretch-database/sql-server-stretch-database-overview.md)와 같은 다른 저장소 및 SQL 옵션을 검토합니다. 한 가지 자세한 비교는 [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md)를 참조하세요.

Azure VM에서 SQL Server를 실행하도록 결정한 후 첫 번째 결정 사항 중 하나는 SQL Server 라이선스 비용을 포함하는 VM 이미지를 사용할지 여부입니다. 또 다른 옵션은 BYOL(Bring Your Own License) 및 VM 자체에 대해서만 지불하는 것입니다. 다음 두 섹션에서는 이러한 옵션을 설명합니다.

## 새 SQL VM 만들기
다음 섹션에서는 SQL Server 가상 컴퓨터 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다. 선택한 이미지에 따라 SQL Server 라이선스 비용을 분당 기준으로 지불하거나 사용자 라이선스가 필요할 수 있습니다(BYOL).

이 프로세스에 대한 단계별 지침은 자습서의 [Azure Portal에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-windows-portal-sql-server-provision.md)에 있습니다. 또한 프로비전 중 사용 가능한 적절한 컴퓨터 크기 및 다른 기능을 선택하는 방법을 설명하는 [SQL Server VM에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 검토합니다.

## 옵션 1: 분당 라이선스를 사용한 SQL VM 만들기
다음 표에서는 가상 컴퓨터 갤러리의 사용 가능한 SQL Server 이미지의 매트릭스를 제공합니다. 모든 링크를 클릭하여 지정된 버전, 버전 및 운영 체제로 새 SQL VM 만들기를 시작합니다.

|버전|운영 체제|버전|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## 옵션 2: 기존 라이선스를 사용한 SQL VM 만들기
사용자 라이선스가 필요할 수도 있습니다(BYOL). 이 시나리오에서는 SQL Server 라이선스에 대한 추가 비용 없이 VM에 대해서만 지불합니다. 사용자 고유 라이선스를 사용하려면 아래의 SQL Server 버전, 버전 및 운영 체제의 매트릭스를 사용합니다. 포털에서 이러한 이미지 이름에는 접두사 **{BYOL}**이 붙습니다.

|버전|운영 체제|버전|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] BYOL VM 이미지를 사용하려면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)으로 기업 계약을 체결해야 합니다. 또한 사용하려는 SQL Server의 버전/버전에 대한 유효한 라이선스가 필요합니다. VM 프로비전의 **10**일 내에 [필요한 BYOL 정보를 Microsoft에 제공](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf)해야 합니다.

## SQL VM 관리
SQL Server VM을 프로비전한 후 여러 가지 선택적 관리 작업이 있습니다. 여러 측면에서 정확하게 온-프레미스 SQL Server 인스턴스를 관리한 대로 SQL Server를 구성 및 관리합니다. 하지만 일부 태스크는 Azure에 특정됩니다. 다음 섹션에서는 자세한 정보에 대한 링크로 이러한 영역 중 일부를 강조 표시합니다.

### 데이터 마이그레이션

기존 데이터베이스가 있는 경우 새로 프로비전된 SQL VM으로 이동할 수 있습니다. 마이그레이션 옵션 목록 및 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

### 고가용성 구성

고가용성이 필요한 경우 SQL Server 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 여러 Azure VM이 포함됩니다. Azure 포털에는 사용자를 위해 이 구성을 설정하는 템플릿이 있습니다. 자세한 내용은 [Azure Resource Manager 가상 컴퓨터에서 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 참조하세요. 가용성 그룹과 연결된 수신기를 수동으로 구성하려면 [Azure VM의 AlwaysOn 가용성 그룹 구성](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)을 참조하세요.

다른 고가용성 고려 사항은 [Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.

### 데이터 백업
Azure VM은 [자동화된 백업](virtual-machines-windows-sql-automated-backup.md)을 이용할 수 있으며 정기적으로 Blob 저장소에 데이터베이스의 백업을 만듭니다. 수동으로 이 기술을 사용할 수 있습니다. 자세한 내용은 [SQL Server 백업 및 복원에 Azure Storage 사용](virtual-machines-windows-use-storage-sql-server-backup-restore.md)을 참조하세요. 모든 백업 및 복원 옵션에 대한 개요는 [Azure 가상 컴퓨터에서 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요.

### 업데이트 자동화
Azure VM은 [자동화된 패치](virtual-machines-windows-sql-automated-patching.md)를 사용하여 중요한 Windows 및 SQL Server 업데이트를 자동으로 설치하는 유지 관리 창을 예약할 수 있습니다.

### CEIP(사용자 환경 개선 프로그램)
CEIP(사용자 환경 개선 프로그램)를 사용하도록 기본 설정되어 있습니다. 프로비전한 후 CEIP를 사용하지 않도록 설정하려는 경우가 아니면 관리 작업이 아닙니다. 원격 데스크톱을 사용하여 VM에 연결하여 CEIP를 사용자 지정하거나 사용하지 않도록 설정할 수 있습니다. 그런 다음 **SQL Server 오류 및 사용 보고** 유틸리티를 실행합니다. 보고를 해제하려면 지침을 따릅니다.

## 다음 단계
Azure 가상 컴퓨터의 SQL Server에 대한 [학습 경로를 탐색](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)합니다.

추가 질문이 있나요? 먼저 [Azure 가상 컴퓨터의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)를 참조하세요. 또한 SQL VM 항목의 맨 아래에 질문 또는 의견을 추가하여 Microsoft 및 커뮤니티와 상호 의견을 교환하세요.

<!---HONumber=AcomDC_0921_2016-->