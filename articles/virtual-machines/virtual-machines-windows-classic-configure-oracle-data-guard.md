<properties
	pageTitle="VM에서 Oracle 데이터 가드 구성 | Microsoft Azure"
	description="고가용성 및 재해복구에 대한 Azure 가상 컴퓨터의 Oracle Data Guard 설치 및 구현을 단계별로 설명합니다."
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

#Azure용 Oracle Data Guard 구성하기


이 자습서에서는 고가용성 및 재해 복구를 위해 Azure Virtual Machines 환경에서 Oracle Data Guard를 설정 및 구현하는 방법에 대해 설명합니다. 이 자습서에서는 비 RAC Oracle 데이터베이스에 대한 단방향 복제에 초점을 맞춥니다.

Oracle Data Guard는 Oracle 데이터베이스에 대한 데이터 보호 및 재해 복구를 지원합니다. 재해 복구, 데이터 보호 및 전체 Oracle 데이터베이스에 대한 고가용성을 위한 간편하고 성능이 뛰어난 드롭인 솔루션입니다.

이 자습서에서는 사용자에게 Oracle 데이터베이스의 고가용성 및 재해 복구 개념에 대한 이론 및 실무 지식이 이미 있다고 가정합니다. 자세한 내용은 [Oracle 웹 사이트](http://www.oracle.com/technetwork/database/features/availability/index.html) 및 [Oracle Data Guard 개념 및 관리 가이드](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm)를 참조하세요.

또한 자습서에서는 다음과 같은 전제 조건이 이미 구현되었다고 가정합니다.

- [Oracle 가상 컴퓨터 이미지 - 기타 고려 사항](virtual-machines-windows-classic-oracle-considerations.md) 항목에서 고가용성 및 재해 복구 고려 사항 섹션을 이미 검토했을 것입니다. Azure는 독립 실행형 Oracle 데이터베이스 인스턴스를 지원하지만 Oracle RAC(Oracle Real Application Clusters)는 현재 지원하지 않습니다.


- Oracle Enterprise Edition 이미지를 제공하는 동일한 플랫폼을 사용하여 Azure에서 두 VM(가상 컴퓨터)을 만들었습니다. 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 하려면 [동일한 클라우드 서비스](virtual-machines-windows-load-balance.md) 및 동일한 가상 네트워크에 Virtual Machines가 있어야 합니다. 또한 Azure이 개별 장애 도메인 및 업그레이드 도메인에 VM을 배치할 수 있도록 동일한 [가용성 집합](virtual-machines-windows-manage-availability.md)에 VM을 배치하는 것이 좋습니다. Oracle Data Guard는 Oracle Database Enterprise Edition으로만 사용할 수 있습니다. 각 컴퓨터에는 최소 2GB의 메모리 및 5GB의 디스크 공간이 있어야 합니다. 플랫폼에서 제공되는 VM 크기에 대한 최신 정보는 [Azure용 가상 컴퓨터 크기](virtual-machines-windows-sizes.md)를 참조하세요. VM에 대한 추가 디스크 볼륨이 필요한 경우 추가 디스크를 연결할 수 있습니다. 자세한 내용은 [가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-windows-classic-attach-disk.md)을 참조하세요.



- Azure 클래식 포털에서 기본 VM은 “Machine1”, 대기 VM은 “Machine2”로 가상 컴퓨터 이름을 설정했습니다.

- `C:\OracleDatabase\product\11.2.0\dbhome_1\database`와(과) 같이 기본 및 대기 가상 컴퓨터에서 동일한 Oracle 루트 설치 경로를 가리키도록 **ORACLE\_HOME** 환경 변수를 설정했습니다.

- **관리자** 그룹의 구성원 또는 **ORA\_DBA** 그룹의 구성원으로 Windows 서버에 로그온합니다.

이 자습서에서는 다음을 수행합니다.

실제 대기 데이터베이스 환경 구현

1. 주 데이터베이스 만들기

2. 대기 데이터베이스를 만들기 위한 주 데이터베이스 준비

	1. 강제 로깅을 사용하도록 설정

	2. 암호 파일 만들기

	3. 대기 다시 실행 로그 구성

	4. Enable Archiving

	5. 주 데이터베이스 초기화 매개 변수 설정

실제 대기 데이터베이스 만들기

1. 대기 데이터베이스에 대한 초기화 매개 변수 파일 준비

2. 주 및 대기 컴퓨터에서 데이터베이스를 지원하기 위해 수신기 및 tnsnames 구성

	1. 두 데이터베이스 모두에 대한 항목을 보유하도록 두 서버에 모두 listener.ora 구성

	2. 주 및 대기 데이터베이스에 대한 항목을 보유하기 위해 주 및 대기 Virtual Machines에서 tnsnames.ora를 구성합니다.

	3. 수신기를 시작하고 두 서비스에 대해 두 가상 컴퓨터 모두의 tnsping를 확인합니다.

3. nomount 상태의 대기 인스턴스 시작

4. 데이터베이스 복제 및 대기 데이터베이스 만들기를 위해 RMAN 사용

5. 관리되는 복구 모드에서 실제 대기 데이터베이스 시작

6. 실제 대기 데이터베이스 확인

> [AZURE.IMPORTANT] 이 자습서에서는 다음 하드웨어 및 소프트웨어 구성에 대해 설정 및 테스트되었습니다.
>
>| | **주 데이터베이스** | **대기 데이터베이스** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Oracle 릴리스** | Oracle11g Enterprise 릴리스(11.2.0.4.0) | Oracle11g Enterprise 릴리스(11.2.0.4.0) |
>| **컴퓨터 이름** | Machine1 | Machine2 |
>| **운영 체제** | Windows 2008 R2 | Windows 2008 R2 |
>| **Oracle SID** | 테스트 | TEST\_STBY |
>| **메모리** | 최소 2GB | 최소 2GB |
>| **디스크 공간** | 최소 5GB | 최소 5GB |

Oracle 데이터베이스 및 Oracle Data Guard의 후속 릴리스에서 구현하는 데 필요한 일부 추가 변경 내용이 있을 수 있습니다. 최신 버전 특정 정보는 Oracle 웹 사이트에서 [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 및 [Oracle 데이터베이스](http://www.oracle.com/us/corporate/features/database-12c/index.html) 설명서를 참조하세요.

##실제 대기 데이터베이스 환경 구현
### 1\. 주 데이터베이스 만들기

- 기본 가상 컴퓨터에서 주 데이터베이스 “테스트”를 만듭니다. 자세한 내용은 Oracle 데이터베이스 만들기 및 구성을 참조하세요.
- 데이터베이스의 이름을 보려면 SQL*Plus 명령 프롬프트에서 SYSDBA 역할이 있는 SYS 사용자로 데이터베이스에 연결하고 다음 문을 실행합니다.

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- 그런 다음 dba\_data\_files 시스템 뷰에서 데이터베이스 파일의 이름을 쿼리합니다.

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. 대기 데이터베이스를 만들기 위한 주 데이터베이스 준비

대기 데이터베이스를 만들기 전에 주 데이터베이스가 제대로 구성되었는지 확인하는 것이 좋습니다. 다음은 수행에 필요한 단계 목록입니다.

1. 강제 로깅을 사용하도록 설정
2. 암호 파일 만들기
3. 대기 다시 실행 로그 구성
4. Enable Archiving
5. 주 데이터베이스 초기화 매개 변수 설정

#### 강제 로깅을 사용하도록 설정

대기 데이터베이스를 구현하려면 주 데이터베이스에서 '강제 로깅'을 사용하도록 설정해야 합니다. 이 옵션을 사용하면 'nologging' 작업이 완료된 경우에도 강제 로깅이 우선 적용되며 모든 작업이 다시 실행 로그로 로그됩니다. 그러므로 주 데이터베이스의 모든 항목이 로그되고 대기에 대한 복제에 주 데이터베이스의 모든 작업이 포함됩니다. 강제 로깅을 사용하도록 설정하려면 alter database 문을 실행합니다.

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### 암호 파일 만들기

기본 서버에서 대기 서버로 보관된 로그를 전달 및 적용할 수 있으려면 sys 암호가 기본 및 대기 서버 모두와 동일해야 합니다. 이 때문에 주 데이터베이스에서 암호 파일을 만들고 대기 서버로 이를 복사하는 것입니다.

>[AZURE.IMPORTANT] Oracle 데이터베이스 12c 사용 시 Oracle Data Guard를 관리하는 데 사용할 수 있는 새 사용자 **SYSDG**가 있습니다. 자세한 내용은 [Oracle 데이터베이스 12c 릴리스의 변경 내용](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404)을 참조하세요.

또한 ORACLE\_HOME 환경이 Machine1에 이미 정의되어 있어야 합니다. 그렇지 않은 경우 환경 변수 대화 상자를 사용하여 환경 변수로 이를 정의해야 합니다. 이 대화 상자에 액세스하려면 **제어판**에서 시스템 아이콘을 두 번 클릭하여 **시스템** 유틸리티를 시작한 다음 **고급** 탭을 클릭하고 **환경 변수**를 선택합니다. 환경 변수를 설정하려면 **시스템 변수**에서 **새로 만들기** 단추를 클릭합니다. 환경 변수를 설정한 후 기존 Windows 명령 프롬프트를 닫고 새 프롬프트를 엽니다.

C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database 같은 Oracle\_Home 디렉터리로 전환하려면 다음 문을 실행합니다.

	cd %ORACLE_HOME%\database

그런 다음 암호 파일 만들기 유틸리티 [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm)를 사용하여 암호 파일을 만듭니다. Machine1에서 동일한 Windows 명령 프롬프트에서 **SYS**의 암호로 암호 값을 설정하여 다음 명령을 실행합니다.

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

이 명령은 ORACLE\_HOME\\database 디렉터리에 PWDTEST.ora라는 암호 파일을 만듭니다. 이 파일을 Machine2의 %ORACLE\_HOME%\\database 디렉터리에 수동으로 복사해야 합니다.

#### 대기 다시 실행 로그 구성

그런 다음 대기가 될 때 기본이 다시 실행을 제대로 받을 수 있도록 대기 다시 실행 로그를 구성해야 합니다. 여기서 미리 만들면 대기 다시 실행 로그가 대기에서 자동으로 만들어지도록 할 수 있습니다. 온라인 다시 실행 로그와 동일한 크기로 SRL(대기 다시 실행 로그)을 구성하는 것이 중요합니다. 현대 대기 다시 실행 로그 파일의 크기는 현재 주 데이터베이스 온라인 다시 실행 로그 파일의 크기와 정확히 일치해야 합니다.

Machine1의 SQL*PLUS 명령 프롬프트에서 다음 문을 실행합니다. v$logfile은 다시 실행 로그 파일에 대한 정보를 포함하는 시스템 뷰입니다.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


52428800은 50메가바이트입니다.

그런 다음 SQL*PLUS 창에서 다음 문을 실행하여 대기 데이터베이스에 새로운 대기 다시 실행 로그 파일 그룹을 추가하고 GROUP 절을 사용하여 그룹을 식별하는 번호를 지정합니다. 그룹 번호를 사용하면 대기 다시 실행 로그 파일 그룹을 더 쉽게 관리할 수 있습니다.

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

다음으로 다음 시스템 뷰를 실행하여 다시 실행 로그 파일에 대한 정보를 나열합니다. 또한 이 작업은 대기 다시 실행 로그 파일 그룹이 만들어졌는지 확인합니다.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### 보관 사용

그런 다음 ARCHIVELOG 모드에서 주 데이터베이스를 배치하고 자동 보관을 사용하도록 설정하려면 다음 문을 실행하여 보관을 사용하도록 설정합니다. 데이터베이스를 탑재하고 archivelog 명령을 실행하여 아카이브 로그 모드를 설정할 수 있습니다.

먼저, sysdba로 로그인합니다. Windows 명령 프롬프트에서 다음을 실행합니다.

	sqlplus /nolog

	connect / as sysdba

그런 다음 SQL*PLUS 명령 프롬프트에서 데이터베이스를 종료합니다.

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

그리고 startup mount 명령을 실행하여 데이터베이스를 탑재합니다. 이렇게 하면 Oracle이 지정된 데이터베이스와 인스턴스를 연결합니다.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

이어서 다음을 실행합니다.

	SQL> alter database archivelog;
	Database altered.

Open 절로 Alter database 문을 실행하여 데이터베이스의 일반 사용이 가능하도록 합니다.

	SQL> alter database open;

	Database altered.

#### 주 데이터베이스 초기화 매개 변수 설정

Data Guard를 구성하려면 먼저 일반 pfile에서 대기 매개 변수를 만들고 구성해야 합니다. pfile이 준비되면 서버 매개 변수 파일(SPFILE)로 변환해야 합니다.

INIT.ORA 파일의 매개 변수를 사용하여 Data Guard 환경을 제어할 수 있습니다. 이 자습서의 지침을 따를 때 주 또는 대기의 두 역할을 모두 유지할 수 있도록 주 데이터베이스 INIT.ORA를 업데이트해야 합니다.

	SQL> create pfile from spfile;
	File created.

다음으로 대기 매개 변수를 추가하려면 pfile을 편집해야 합니다. 이를 위해서 %ORACLE\_HOME%\\database의 해당 위치에서 INITTEST.ORA를 엽니다. 다음으로 INITTEST.ora 파일에 다음 문을 추가합니다. INIT.ORA 파일에 대한 명명 규칙은 INIT<YourDatabaseName>.ORA입니다.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


이전 문 블록은 다음과 같이 중요한 세 가지 설정 항목을 포함합니다.
-	**LOG\_ARCHIVE\_CONFIG...:** 이 문을 사용하여 고유한 데이터베이스 ID를 정의합니다.
-	**LOG\_ARCHIVE\_DEST\_1...:** 이 문을 사용하여 로컬 보관 폴더 위치를 정의합니다. 데이터베이스의 보관 요구를 위해 새 디렉터리를 만들고 Oracle의 기본 폴더인 %ORACLE\_HOME%\\database\\archive를 사용하기 보다는 이 문을 명시적으로 사용하여 로컬 보관 위치를 지정하는 것이 좋습니다.
-	**LOG\_ARCHIVE\_DEST\_2 .... LGWR ASYNC...:** LGWR(비동기 로그 기록기 프로세스)를 정의하여 트랜잭션 다시 실행 데이터를 수집하고 이를 대기 대상으로 전송합니다. 여기서는 DB\_UNIQUE\_NAME이 대상 대기 서버에서 데이터베이스의 고유한 이름을 지정합니다.

새 매개 변수 파일이 준비되면 spfile을 만들어야 합니다.

먼저, 데이터베이스를 종료합니다.

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

다음으로 startup nomount 명령을 다음과 같이 실행합니다.

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

이제는 spfile을 만듭니다.

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

그런 다음 데이터베이스를 종료합니다.

	SQL> shutdown immediate;

	ORA-01507: database not mounted

그리고 startup 명령을 사용하여 인스턴스를 시작합니다.

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##실제 대기 데이터베이스 만들기
이 섹션에서는 실제 대기 데이터베이스를 준비하기 위해 Machine2에서 수행해야 하는 단계에 초점을 맞춥니다.

먼저, Azure 클래식 포털을 통해 Machine2에 원격 데스크톱을 연결해야 합니다.

그런 다음 대기 서버(Machine2)에서 C:\\<YourLocalFolder>\\TEST와 같이 대기 데이터베이스에 필요한 모든 폴더를 만듭니다. 이 자습서를 수행하는 동안 controlfile, datafiles, redologfiles, udump, bdump 및 cdump 파일과 같이 필요한 모든 파일을 유지하기 위해 해당 폴더 구조와 Machine1의 폴더 구조가 일치해야 합니다. 또한 Machine2에서 ORACLE\_HOME 및 ORACLE\_BASE 환경 변수를 정의합니다. 그렇지 않은 경우 환경 변수 대화 상자를 사용하여 이를 환경 변수로 정의합니다. 이 대화 상자에 액세스하려면 **제어판**에서 시스템 아이콘을 두 번 클릭하여 **시스템** 유틸리티를 시작한 다음 **고급** 탭을 클릭하고 **환경 변수**를 선택합니다. 환경 변수를 설정하려면 **시스템 변수**에서 **새로 만들기** 단추를 클릭합니다. 환경 변수를 설정한 후 변경 내용을 보기 위해 기존 Windows 명령 프롬프트를 닫고 새 프롬프트를 열어야 합니다.

이어서 다음 단계를 수행합니다.

1. 대기 데이터베이스에 대한 초기화 매개 변수 파일 준비

2. 주 및 대기 컴퓨터에서 데이터베이스를 지원하기 위해 수신기 및 tnsnames 구성

	1. 두 데이터베이스 모두에 대한 항목을 보유하도록 두 서버에 모두 listener.ora 구성

	2. 주 및 대기 데이터베이스에 대한 항목을 보유하는 주 및 대기 가상 컴퓨터에서 tnsnames.ora 구성

	3. 수신기를 시작하고 두 서비스에 대해 두 가상 컴퓨터 모두의 tnsping를 확인합니다.

3. nomount 상태의 대기 인스턴스 시작

4. 데이터베이스 복제 및 대기 데이터베이스 만들기를 위해 RMAN 사용

5. 관리되는 복구 모드에서 실제 대기 데이터베이스 시작

6. 실제 대기 데이터베이스 확인

### 1\. 대기 데이터베이스에 대한 초기화 매개 변수 파일 준비

이 섹션에서는 대기 데이터베이스에 대한 초기화 매개 변수 파일을 준비하는 방법을 설명합니다. 이를 위해서는 먼저 Machine1에서 Machine2로 INITTEST.ORA 파일을 수동으로 복사합니다. 두 컴퓨터에서 모두 %ORACLE\_HOME%\\database 폴더의 INITTEST.ORA 파일을 볼 수 있어야 합니다. 그런 다음 아래 지정된 대로 대기 역할 설정을 위해 Machine2에서 INITTEST.ORA 파일을 수정합니다.

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


이전 문 블록은 다음과 같이 중요한 두 가지 설정 항목을 포함합니다.

-	***.LOG\_ARCHIVE\_DEST\_1:** Machine2에 c:\\OracleDatabase\\TEST\_STBY\\archives 폴더를 수동으로 만들어야 합니다.
-	***.LOG\_ARCHIVE\_DEST\_2:** 이 단계는 선택 사항입니다. 주 컴퓨터가 유지 관리 중이고 대기 컴퓨터가 주 데이터베이스가 될 때 필요할 수 있기 때문에 이 단계를 설정합니다.

그런 다음 대기 인스턴스를 시작해야 합니다. 대기 데이터베이스 서버에서 Windows 서비스를 만들어 Oracle 인스턴스를 만들기 위해 Windows 명령 프롬프트에서 다음 명령을 입력합니다.

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

**Oradim** 명령은 Oracle 인스턴스를 만들지만 시작하지는 않습니다. C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN 디렉터리에서 찾을 수 있습니다.

##주 및 대기 컴퓨터에서 데이터베이스를 지원하기 위해 수신기 및 tnsnames 구성
대기 데이터베이스를 만들기 전에 구성에서 주 및 대기 데이터베이스가 서로 통신할 수 있는지 확인해야 합니다. 이렇게 하려면 수동으로 또는 네트워크 구성 유틸리티 NETCA를 사용하여 수신기와 TNSNames를 모두 구성해야 합니다. 이는 RMAN(복구 관리자 유틸리티)를 사용할 때 필수 작업입니다.

### 두 데이터베이스 모두에 대한 항목을 보유하도록 두 서버에 모두 listener.ora 구성

Machine1에 원격 데스크톱을 설정하고 아래 지정된 대로 listener.ora 파일을 편집합니다. listener.ora 파일을 편집할 때에는 항상 여는 괄호와 닫는 괄호가 항상 동일한 열에 배열되도록 합니다. c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\ 폴더에서 listener.ora 파일을 찾을 수 있습니다.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

이어서 Machine2에 원격 데스크톱을 설정하고 다음과 같이 listener.ora 파일을 편집합니다. # listener.ora Network Configuration File: C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### 주 및 대기 데이터베이스에 대한 항목을 보유하는 주 및 대기 가상 컴퓨터에서 tnsnames.ora 구성

Machine1에 원격 데스크톱을 설정하고 아래 지정된 대로 tnsnames.ora 파일을 편집합니다. c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\ 폴더에서 tnsnames.ora 파일을 찾을 수 있습니다.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Machine2에 원격 데스크톱을 설정하고 다음과 같이 tnsnames.ora 파일을 편집합니다.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### 수신기를 시작하고 두 서비스에 대해 두 가상 컴퓨터 모두의 tnsping를 확인합니다.

기본 및 대기 가상 컴퓨터에서 모두 새 Windows 명령 프롬프트를 열고 다음 문을 실행합니다.

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##nomount 상태의 대기 인스턴스 시작
대기 Virtual Machine(MACHINE2)에서 대기 데이터베이스를 지원하도록 환경을 설정합니다.

먼저 기본 컴퓨터(Machine1)에서 대기 컴퓨터(Machine2)로 암호 파일을 수동으로 복사합니다. 이는 **sys** 암호가 두 컴퓨터에서 동일해야 하기 때문에 필요합니다.

그런 다음, Machine2에서 Windows 명령 프롬프트를 열고 다음과 같이 대기 데이터베이스를 가리키도록 환경 변수를 설정합니다.

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

그리고 nomount 상태에서 대기 데이터베이스를 시작한 다음 spfile을 생성합니다.

다음과 같이 데이터베이스를 시작합니다.

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##데이터베이스 복제 및 대기 데이터베이스 만들기를 위해 RMAN 사용
RMAN(복구 관리자 유틸리티)을 사용하여 주 데이터베이스의 백업 복사본을 가져와 실제 대기 데이터베이스를 만들 수 있습니다.

대기 가상 컴퓨터(MACHINE2)에 원격 데스크톱을 설정하고 대상(주 데이터베이스, Machine1) 및 보조(대기 데이터베이스, Machine2) 인스턴스에 모두 전체 연결 문자열을 지정하여 RMAN 유틸리티를 실행합니다.

>[AZURE.IMPORTANT] 대기 서버 컴퓨터에는 아직 데이터베이스가 없기 때문에 운영 체제 인증을 사용하지 않습니다.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##관리되는 복구 모드에서 실제 대기 데이터베이스 시작
이 자습서에서는 실제 대기 데이터베이스를 만드는 방법을 설명합니다. 논리적 대기 데이터베이스를 만드는 방법은 Oracle 설명서를 참조하십시오.

SQL*PLUS 명령 프롬프트를 열고 다음과 같이 대기 가상 컴퓨터 또는 서버(MACHINE2)에서 Data Guard를 사용하도록 설정합니다.

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

**탑재** 모드에서 대기 데이터베이스를 열 때 보관 로그 전달이 계속되며 관리되는 복구 프로세스가 대기 데이터베이스에서 로그 적용을 계속합니다. 이렇게 하면 대기 데이터베이스는 주 데이터베이스와 함께 최신 상태로 유지됩니다. 이 동안 대기 데이터베이스는 보고를 목적으로 액세스할 수 없습니다.

**읽기 전용** 모드에서 대기 데이터베이스를 열 때 보관 로그 전달이 계속됩니다. 하지만 관리되는 복구 프로세스는 중지됩니다. 이로 인해 대기 데이터베이스는 관리되는 복구 프로세스가 다시 시작될 때까지 점점 최신 상태에서 벗어납니다. 이 동안 보고를 목적으로 대기 데이터베이스에 액세스할 수 있지만 데이터는 최신 변경 내용을 반영하지 않을 수 있습니다.

일반적으로 **탑재** 모드에서 대기 데이터베이스를 유지하여 주 데이터베이스의 오류 발생 시에도 대기 데이터베이스의 데이터를 최신으로 유지하는 것이 좋습니다. 하지만 응용 프로그램의 요구 사항에 따라 보고를 목적으로 **읽기 전용** 모드에서 대기 데이터베이스를 유지할 수 있습니다. 다음 단계에서는 SQL*PLUS를 사용하여 읽기 전용 모드에서 Data Guard를 사용하도록 설정하는 방법에 대해 설명합니다.

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##실제 대기 데이터베이스 확인
이 섹션에서는 관리자 권한으로 고가용성 구성을 확인하는 방법을 보여줍니다.

SQL*PLUS 명령 프롬프트 창을 열고 대기 가상 컴퓨터(Machine2)에서 보관된 다시 실행 로그를 확인합니다.

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

SQL*PLUS 명령 프롬프트 창을 열고 기본 컴퓨터(Machine1)에서 logfiles을 전환합니다.

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

대기 가상 컴퓨터(Machine2)에서 보관된 다시 실행 로그를 확인합니다.

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

대기 가상 컴퓨터(Machine2)에서 간격이 있는지 확인합니다.

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

다른 확인 방법은 대기 데이터베이스를 장애 조치되도록 한 다음 주 데이터베이스로 장애 복구할 수 있는지 테스트하는 것입니다. 주 데이터베이스로 대기 데이터베이스를 활성화하려면 다음 문을 사용합니다.

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

원래 주 데이터베이스에서 플래시백을 설정하지 않은 경우 원래 주 데이터베이스를 중단하고 대기 데이터베이스로 다시 만드는 것이 좋습니다.

주 및 대기 데이터베이스에서 플래시백 데이터베이스를 사용하도록 설정하는 것이 좋습니다. 장애 조치가 발생하면 주 데이터베이스가 장애를 복구하기 이전으로 플래시백하여 대기 데이터베이스로 빠르게 변환할 수 있습니다.

##추가 리소스
[Azure용 Oracle 가상 컴퓨터 이미지](virtual-machines-windows-classic-oracle-images.md)

<!---HONumber=AcomDC_0914_2016-->