<properties
	pageTitle="VM에서 Oracle GoldenGate 구성 | Microsoft Azure"
	description="자습서를 단계별로 수행하여 고가용성 및 재해 복구를 위해 Azure Windows Server VM에서 Oracle GoldenGate를 설정 및 구현합니다."
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="09/06/2016"
	ms.author="rclaus" />


#Azure에 대한 Oracle GoldenGate 구성


이 자습서에서는 고가용성 및 재해 복구를 위해 Azure 가상 컴퓨터 환경에서 Oracle GoldenGate를 설정 및 구현하는 방법에 대해 설명합니다. 이 자습서는 비 RAC Oracle 데이터베이스의 [양방향 복제](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm)에 중점을 두며 두 사이트가 모두 활성 상태여야 합니다.

Oracle GoldenGate는 데이터 분포 및 데이터 통합을 지원합니다. 데이터 분포 및 Oracle-Oracle 복제 구성을 통해 데이터 동기화 솔루션을 설정할 수 있도록 하고 유연한 고가용성 솔루션을 제공 합니다. Oracle GoldenGate는 기존의 Oracle Data Guard에 기업 전반의 정보 배포와 가동 중지 시간 삭제 업그레이드 및 Migration 기능을 추가했습니다. 자세한 내용은 [Oracle Data Guard와 함께 Oracle GoldenGate 사용](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm)을 참조하세요.

oracle GoldenGate는 다음 주요 구성 요소를 포함합니다: 추출, 복제, 데이터 펌프, Replicat, 파일을 추적하거나 추출, 검사점, 관리자 및 수집기. 두 사이트 간의 양방향 복제를 하기 위해서는,두 사이트에서 모든 구성 요소를 만들고 시작 해야 합니다. Oracle GoldenGate 아키텍처에 대한 자세한 내용은 [Oracle GoldenGate 가이드](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)를 참조하세요.

이 자습서에서는 사용자에게 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 및 Oracle 데이터베이스의 고가용성 및 재해 복구 개념에 대한 이론 및 실무 지식이 있다고 가정합니다. 자세한 내용은 [Oracle 웹 사이트](http://www.oracle.com/technetwork/database/features/availability/index.html)를 참조하세요.

또한 자습서에서는 다음과 같은 전제 조건이 이미 구현되었다고 가정합니다.

- [Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-windows-classic-oracle-considerations.md) 항목에서 고가용성 및 재해 복구 고려 사항 섹션을 이미 검토했을 것입니다. Azure는 독립 실행형 Oracle 데이터베이스 인스턴스를 지원하지만 Oracle RAC(Oracle Real Application Clusters)는 현재 지원하지 않습니다.

- [Oracle 다운로드](http://www.oracle.com/us/downloads/index.html) 웹 사이트에서 Oracle GoldenGate 소프트웨어를 다운로드했습니다. Oracle Fusion 미들웨어 제품 팩 - 데이터 통합을 선택 했습니다. 그런 다음, Oracle GoldenGate Oracle v11.2.1 미디어 팩에서 Microsoft Windows x64(64 비트)에 대한 Oracle 11g 데이터베이스를 선택 했습니다. 다음으로 Windows 2008(64bit)에서 구동되는 Oracle 11g 64bit용의 Oracle GoldenGate V11.2.1.0.3을 다운로드 합니다.

- Windows Server에서 Oracle Enterprise Edition을 사용하여 Azure에서 두 VM(가상 컴퓨터)을 만들었습니다. 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 하려면 [동일한 클라우드 서비스](virtual-machines-linux-load-balance.md) 및 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 가상 컴퓨터가 있어야 합니다.

- Azure 클래식 포털에서 가상 컴퓨터 이름을 Site A에 대해서는 “MachineGG1” 로 Site B에 대해서는 “MachineGG2”로 설정했습니다.

- 사이트 A와 사이트 B에서 "TestGG2"에서 "TestGG1" 테스트 데이터베이스를 만든 후

- 관리자 그룹의 구성원 또는 **ORA\_DBA**그룹의 구성원으로 Windows 서버에 로그온합니다.

이 자습서에서는 다음을 수행합니다.

1. 사이트 A와 사이트 B에서 데이터베이스를 설치 합니다.

	1. 초기 데이터 로드를 수행 합니다.

2. 사이트 A와 사이트 B의 데이터베이스 복제를 준비합니다.

3. DDL 복제를 지원 하기 위해 필요한 모든 개체를 만듭니다.

4. 사이트 A와 사이트 B에서 GoldenGate 관리자를 구성합니다.

5. 사이트 A와 사이트 B에서 추출 그룹 및 데이터 펌프 프로세스를 생성합니다.

	1. 사이트 A에서 추출 및 데이터 펌프 프로세스를 만듭니다.

	2. 사이트 B에서 GoldenGate 검사점 테이블을 만듭니다.

	3. 사이트 B에서 REPLICAT을 추가합니다.

	4. 사이트 B에서 추출 및 데이터 펌프 프로세스를 만듭니다.

	5. 사이트 A에서 GoldenGate 검사점 테이블을 만듭니다.

	6. 사이트 A에서 REPLICAT을 추가합니다.

	7. 사이트 A와 사이트 B에서 trandata를 추가합니다.

	8. 사이트 A에서 추출 및 데이터 펌프 프로세스를 시작 합니다.

	9. 사이트 B에서 추출 및 데이터 펌프 프로세스를 시작 합니다.

	10. 사이트 A에서 REPLICAT 프로세스를 시작합니다.

	11. 사이트 B에서 REPLICAT 프로세스를 시작합니다.

6. 양방향 복제 프로세스를 확인 합니다.

>[AZURE.IMPORTANT] 이 자습서에서는 다음 소프트웨어 구성에 대해 설정 및 테스트되었습니다.
>
>| | **사이트 A 데이터베이스** | **사이트 B 데이터베이스** |
>|------------------------|----------------------------------|----------------------------------|
>| **Oracle 릴리스** | Oracle11g 릴리스2 – (11.2.0.1) | Oracle11g 릴리스2 – (11.2.0.1) |
>| **컴퓨터 이름** | MachineGG1 | MachineGG2 |
>| **운영 체제** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | TESTGG1 | TESTGG2 |
>| **복제 스키마** | SCOTT | SCOTT |

Oracle 데이터베이스 및 Oracle GoldenGate의 후속 릴리스에서 구현하는 데 필요한 일부 추가 변경 내용이 있을 수 있습니다. 버전별 최신 정보는 Oracle 웹 사이트에서 [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 및 [Oracle 데이터베이스](http://www.oracle.com/us/corporate/features/database-12c/index.html) 설명서를 참조하세요. 예를 들어, 릴리스 11.2.0.4 원본 데이터베이스와 이후 버전에서는 DDL의 캡처 logmining 서버에 의해 비동기적으로 수행되고, 특별한 트리거, 테이블 또는 따로 설치해야 하는 다른 데이터베이스 개체가 필요 없습니다. 사용자 응용 프로그램을 중지 하지 않고 oracle GoldenGate 업그레이드를 수행할 수 있습니다. DDL 트리거 및 지원 개체의 사용은 추출물이11.2.0.4 버전 보다 이전 Oracle 11g 원본 데이터베이스의 통합 모드에 있을 때 필요 합니다. 자세한 지침은 [Oracle 데이터베이스용 Oracle GoldenGate 설치 및 구성](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf)을 참조하세요.

##1\. 사이트 A와 사이트 B에서 데이터베이스를 설치 합니다.
이 섹션에서는 데이터베이스 필수 구성 요소에 사이트 A와 사이트 B를 모두 수행 하는 방법에 대해 설명합니다. 이 섹션의 모든 단계를 두 사이트에서 수행 해야 합니다: 사이트 A와 사이트 B

첫째, 원격 데스크톱과 사이트 A 및 사이트 B는 Azure 클래식 포털을 통해 연결합니다. Windows 명령 프롬프트를 열고 Oracle GoldenGate 설치 파일에 대한 홈 디렉터리를 만듭니다.

	mkdir C:\OracleGG

그런 다음의 압축을 풀고 이 폴더에 Oracle GoldenGate 소프트웨어를 설치 합니다. 이 단계를 수행한 후 다음 명령을 실행하여 GoldenGate 소프트웨어 명령 인터프리터 (GGSCI)를 시작할 수 있습니다.

	C:\OracleGG\.\ggsci

[GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm)를 사용하여 Oracle GoldenGate를 구성, 제어 및 모니터링하는 여러 명령을 실행할 수 있습니다.

그런 다음 설치 패키지에서 모든 하위 폴더를 만들려면 다음 명령을 실행 합니다.

	GGSCI (Hostname) 1> CREATE SUBDIRS

GGSCI 명령 프롬프트를 종료 하려면 다음 명령을 실행 합니다.

	GGSCI (Hostname) 1> EXIT

그런 다음 Oracle GoldenGate 관리자, 추출 및 복제 프로세스에 의해 사용되는 데이터베이스 사용자를 만들어야 합니다. 참고로, 각 프로세스에 대한 개별 사용자를 만들거나 또는 하나의 공통 사용자를 구성할 수 있습니다. 이 자습서에서는 ggate로 불리는 한 명의 사용자를 만듭니다. 그런 다음 해당 사용자에게 필요한 권한을 부여합니다. 사이트 A와 사이트 B에서 다음 작업을 꼭 수행해야 하는 것을 잊지 마세요.

다음과 같은 **SYSDBA**를 사용하여 데이터베이스 관리자 권한으로 사이트 A와 사이트 B에서 SQL*Plus 명령 창을 엽니다.

	Enter username: / as sysdba

실행합니다.

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

다음으로 %ORACLE\_HOME%\\database 폴더에 있는 INIT<DatabaseSID>.ORA 파일을 찾아서 INITTEST.ora에 다음과 같은 데이터베이스 매개 변수를 추가 합니다.

	UNDO\_MANAGEMENT=AUTO
	UNDO\_RETENTION=86400

모든 Oracle GoldenGate GGSCI 명령의 전체 목록은 [Windows용 Oracle GoldenGate 참조](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm)를 참조하세요.

### 초기 데이터 로드를 수행 합니다.

다음의 여러 메서드에 이용하여 데이터베이스에서 초기 데이터 로드를 수행할 수 있습니다. 예를 들어 [Oracle GoldenGate 직접 로드](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) 또는 일반적인 내보내기 및 가져오기 유틸리티를 사용하여 사이트 A에서 사이트 B로 테이블 데이터를 내보낼 수 있습니다.

Oracle GoldenGate 복제 프로세스를 설명 하기 위해 이 자습서는 사이트 A와 사이트 B에서 다음 명령을 사용 하여 테이블을 만드는 방법을 보여줍니다.

먼저 SQL* Plus 명령 창을 열고, 사이트 A와 사이트 B의 데이터베이스에 재고 테이블을 만들기 위해 다음 명령을 실행 합니다.

	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

다음으로 제약 조건을 사이트 A와 사이트 B 데이터베이스에서 새로 만든 테이블에 추가합니다:

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

그리고, 사이트 A와 사이트 b의 사용자 ggate에게 새 인벤토리 테이블에 대한 모든 권한을 부여합니다.

	grant all on scott.inventory to ggate;

다음으로, 만약 사용자가 ggate가 아닌 경우, 모든 트랜잭션이 새 테이블에 기록되었는지 확인하기 위해 데이터베이스 트리거, VENTORY\_CDR\_TRG,를 새로 만든 테이블에 생성하고 사용할 있게 합니다. 사이트 A와 사이트 B에 대해 이 작업을 수행 합니다.

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/


##2\. 사이트 A와 사이트 B의 데이터베이스 복제를 준비합니다.
이 섹션에서는 데이터베이스 복제를 위한 사이트 A와 사이트 B를 준비 하는 방법에 설명 합니다. 이 섹션의 모든 단계를 두 사이트에 수행 해야 합니다: 사이트 A와 사이트 B

첫째, 원격 데스크톱과 사이트 A 및 사이트 B는 Azure 클래식 포털을 통해 연결합니다. 데이터베이스를 SQL*Plus 명령창을 사용하여 archivelog 모드로 전환합니다.

	sql>shutdown immediate
	sql>startup mount
	sql>alter database archivelog;
	sql>alter database open;


그런 다음, 사용할 수 있는 최소한의[보완 로깅](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm)은 다음과 같습니다.

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

다음으로, DDL (데이터 정의 언어) 복제를 지원 하기 위해 데이터베이스를 준비 합니다.

	SQL> alter system set recyclebin=off scope=spfile;

그런 다음, 종료하고 데이터베이스를 다시 시작 합니다.

	sql>shutdown immediate
	sql>startup


##3\. DDL 복제를 지원 하기 위해 필요한 모든 개체를 만듭니다.
이 섹션에는 DDL복제를 지원하는 모든 필요한 개체를 만들기 위해 필요한 스크립트를 나열 합니다. 사이트 A와 사이트 B 모두에 이 섹션에 지정된 스크립트를 실행 해야 합니다.

Windows 명령 프롬프트를 열고 C:\\OracleGG 같은 Oracle GoldenGate 폴더로 이동 합니다. SQL * Plus 명령 프롬프트를 **SYSDBA**와 같은 데이터 베이스 관리자 권한을 사용하여 사이트 A와 사이트 B에서 실행합니다.

다음 스크립트를 실행 합니다.

	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


Oracle GoldenGate 도구는 DDL (데이터 정의 언어)을 지원하기 위해 테이블 레벨 로그인이 필요 합니다. 그 이유는. ADD TRANDATA 명령을 사용하여 테이블 레벨에서 보완 로깅을 사용할 수 있도록 하기 위해서입니다. Oracle GoldenGate 명령 해석기 창을 열고, 데이터베이스에 로그인 후, ADD TRANDATA 명령을 실행 합니다.

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4\. 사이트 A와 사이트 B에서 GoldenGate 관리자를 구성합니다.
Oracle GoldenGate 관리자는 다른 Golden gate 프로세스를 시작하거나, 추적 로그 파일 관리 및 보고하는 여러 기능을 수행합니다.

사이트 A와 사이트 B 모두에서 Oracle GoldenGate 관리자 프로세스를 구성해야 합니다. 그러기 위해서, 사이트 A와 사이트 B에서 다음 단계를 수행합니다.

Windows 명령 창을 열고 Oracle GoldenGate 명령 인터프리터를 시작합니다.

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


데이터베이스에 영향을 주는 명령을 실행할 수 있도록 GGSCI 세션을 데이터베이스에 기록 합니다.

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

시스템에서 모든 관리자, 추출 및 Replicat 프로세스에 대한 상태와 경과(관련 있는 경우)를 표시합니다.

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

시스템에서 모든 관리자, 추출 및 Replicat 프로세스에 대한 상태와 경과(관련 있는 경우)를 표시합니다.

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

데이터베이스에 영향을 주는 명령을 실행할 수 있도록 GGSCI 세션을 데이터베이스에 기록 합니다.

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

관리자 프로세스를 시작 합니다.

	GGSCI (HostName) 48> start manager
	Manager started.

##5\. 사이트 A와 사이트 B에서 추출 그룹 및 데이터 펌프 프로세스를 생성합니다.

###사이트 A에서 추출 및 데이터 펌프 프로세스를 만듭니다.

사이트 A와 사이트 B에 추출 및 데이터 펌프 프로세스를 만들어야 합니다. 원격 데스크톱과 사이트 A와 사이트 B는 Azure 클래식 포털을 통해 연결됩니다. GGSCI 명령 해석기 창을 여십시오. 사이트 A에서 다음 명령을 실행합니다

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

PARAMS 편집 명령을 사용하여 매개 변수 파일을 열고 다음 정보를 추가 하십시오: GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###사이트 B에서 GoldenGate 검사점 테이블을 만듭니다.

다음으로 사이트 B에서 검사점 테이블을 추가 하려면 GoldenGate 명령 해석기 창을 열고 실행 해야 합니다.

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

그리고, ggate가 소유자인 곳의 데이터베이스에 검사점 테이블을 추가 합니다.

	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

대상 서버의 GLOBALS파일에 검사점 테이블의 이름을 합니다. 대상 서버는 사이트 B입니다. 사이트 B에서 GLOBALS 파일을 편집:

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

그런 다음 CHECKPOINTTABLE 매개 변수를 기존 GLOBALS 파일에 추가 합니다.

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

마지막 단계로, 저장 하고 전역 매개 변수 파일을 닫습니다.


###사이트 B에서 REPLICAT을 추가합니다.
이 섹션에서는 사이트 B에 REPLICAT 프로세스 "REP2"를 추가 하는 방법을 설명합니다.

ADD REPLICAT 명령을 사용하여 사이트 b에서 Replicat 그룹 만들기

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###사이트 B에서 추출 및 데이터 펌프 프로세스를 만듭니다.

이 섹션에서는 사이트 b에서 새 추출 프로세스 "EXT2" 및 "DPUMP2" 새 데이터 펌프 프로세스를 만드는 방법을 설명 합니다.

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###사이트 A에서 GoldenGate 검사점 테이블을 만듭니다.

Oracle GoldenGate 명령 해석기 창을 열고 검사점 테이블을 만듭니다.

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

사이트 A에서 검사점 테이블의 이름을 GLOBALS팔일에 추가합니다.

Oracle GoldenGate 명령 해석기 창을 열고 사이트 A에서 GLOBALS 파일을 편집:

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

저장하고 전역 매개 변수 파일을 닫습니다.

###사이트 A에서 REPLICAT을 추가합니다.

이 섹션에서는 사이트 A에 REPLICAT 프로세스 "REP1"을 추가 하는 방법 설명합니다.

다음 명령은 경로 이름으로 된Replicat 그룹 rep1과, 관련된 검사점 테이블을 만듭니다.

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

PARAMS 편집 명령을 사용 하여 매개 변수 파일을 열고 다음 정보를 추가 합니다.

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### 사이트 A와 사이트 B에서 trandata를 추가합니다.

ADD TRANDATA 명령을 사용하여 테이블 레벨에서 보완 로깅을 사용합니다. Oracle GoldenGate 명령 해석기 창을 열고, 데이터 베이스에 로그인 후, ADD TRANDATA 명령을 실행합니다.

MachineGG1을 원격 데스크톱으로 연결하고, Oracle GoldenGate 명령 인터프리터를 열고 실행 합니다.

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

MachineGG2를 원격 데스크톱으로 연결하고, Oracle GoldenGate 명령 인터프리터를 열고 실행 합니다.

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

테이블 수준 보완 로깅의 상태에 대한 정보를 표시합니다.

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###사이트 A와 사이트 B에서 trandata를 추가합니다.

ADD TRANDATA 명령을 사용하여 테이블 레벨에서 보완 로깅을 사용합니다. Oracle GoldenGate 명령 해석기 창을 열고, 데이터 베이스에 로그인 후, ADD TRANDATA 명령을 실행합니다.

MachineGG1을 원격 데스크톱으로 연결하고, Oracle GoldenGate 명령 인터프리터를 열고 실행 합니다.

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

MachineGG2를 원격 데스크톱으로 연결하고, Oracle GoldenGate 명령 인터프리터를 열고 실행 합니다.

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###사이트 A에서 추출 및 데이터 펌프 프로세스를 시작 합니다.

사이트 a에서 추출 프로세스 ext1를 실행합니다.

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

사이트 a에서 데이터 펌프 프로세서 dpump1을 실행합니다.

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
추출 그룹 ext1에 대한 정보가 표시합니다: GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

시스템에서 모든 관리자, 추출 및 Replicat 프로세스에 대한 상태와 경과(관련 있는 경우)를 표시합니다.

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###사이트 B에서 추출 및 데이터 펌프 프로세스를 시작 합니다.

사이트 b에서 추출 프로세스 ext2를 시작 합니다.

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

사이트 b에서 데이터 펌프 프로세스 dpump2를 시작 합니다.

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

시스템에서 모든 관리자, 추출 및 Replicat 프로세스에 대한 상태와 경과(관련 있는 경우)를 표시합니다.

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### 사이트 A에서 REPLICAT 프로세스를 시작합니다.

이 섹션에서는 사이트 A에서 "REP1" REPLICAT 프로세스를 시작 하는 방법을 설명 합니다.

사이트 A에서 Replicat 프로세스를 시작 합니다.

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

Replicat 그룹의 상태를 표시 합니다.

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###사이트 B에서 REPLICAT 프로세스를 시작합니다.

이 섹션에서는 사이트 B에서 "REP2" REPLICAT 프로세스를 시작 하는 방법 설명

사이트 B에서 Replicat 프로세스를 시작 합니다.

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

Replicat 그룹의 상태를 표시 합니다.

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6\. 양방향 복제 프로세스를 확인 합니다.

Oracle GoldenGate 구성을 확인 하려면 사이트 A에서 데이터베이스에 행을 삽입합니다. 원격 데스크톱을 사이트 A에 연결 후, SQL*Plus 명령 창을 열고 실행: SQL > v$ 데이터베이스에서 이름을 선택합니다.

	NAME
	———
	TESTGG

	SQL> insert into inventory values  (100,’TV’,100,sysdate);

	1 row created.

	SQL> commit;

	Commit complete.

그런 다음 해당 행이 사이트 B에서 복제됐는지 확인하려면 원격 데스크톱을 사이트 B에 연결하고, SQL Plus 창을 열고 실행합니다.

	SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

사이트 B에 새 레코드를 삽입 합니다.

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.

	SQL> commit;

	Commit complete.

원격 데스크톱을 사이트 A와 연결 후 복사가 되었는지 확인합니다.

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##추가 리소스
[Azure용 Oracle 가상 컴퓨터 이미지](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0914_2016-->