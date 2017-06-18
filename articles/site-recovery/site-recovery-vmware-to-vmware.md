<properties
	pageTitle="보조 사이트에 온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버 복제 | Microsoft Azure"
	description="Azure Site Recovery를 사용하여 VMware VM 또는 Windows/Linux 물리적 서버를 보조 사이트에 복제하려면 이 문서를 사용합니다."
	services="site-recovery"
	documentationCenter=""
	authors="nsoneji"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="nisoneji"/>


# 보조 사이트에 온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버 복제


## 개요

Azure Site Recovery의 InMage Scout는 온-프레미스 VMWare 사이트 간의 실시간 복제 기능을 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다.


## 필수 조건

**Azure 계정**: [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. 사이트 복구 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/).


## 1단계: 자격 증명 모음 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **데이터 서비스** > **복구 서비스** > **사이트 복구 자격 증명 모음**을 클릭합니다.
3. **새로 만들기** > **빠른 생성**을 클릭합니다.
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.
5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격](https://azure.microsoft.com/pricing/details/site-recovery/)을 참조합니다.

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 **복구 서비스** 페이지에서 **활성**으로 나열됩니다.

## 2단계: 자격 증명 모음을 구성하고 InMage Scout 구성 요소 다운로드

1. **자격 증명 모음 만들기**를 클릭합니다.
2. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 **빠른 시작** 페이지를 엽니다.
3. 드롭다운 목록에서 **두 개의 온-프레미스 VMWare 사이트 간**을 선택합니다.
4. InMage Scout를 다운로드합니다. 모든 필수 구성 요소에 대한 설치 파일은 다운로드한 zip 파일에 있습니다.


## 3단계: 구성 요소 업데이트를 설치합니다.

최신 [업데이트](#updates)에 대해 읽습니다. 다음 순서로 서버에 업데이트 파일을 설치합니다.

1. 하나인 경우 RX 서버
2. 서버 구성
3. 프로세스 서버
3. 마스터 대상 서버
4. vContinuum 서버.
5. 원본 서버(Windows server에만 해당)

다음과 같이 업데이트를 설치합니다.

1. [update](https://aka.ms/asr-scout-update3) zip 파일을 다운로드합니다. 이 zip 파일에는 다음 파일이 포함됩니다.

	-  RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz
	-  CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe
	-  UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe
	-  UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip
	-  vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe

2. .zip 파일의 압축을 풉니다.
3. **RX 서버**: **RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz**를 RX 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
4. **구성 서버/프로세스 서버**: **CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe**를 구성 서버 및 프로세스 서버에 복사합니다. 실행하려면 두 번 클릭합니다.
5. **Windows 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면 **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe**를 마스터 대상 서버에 복사합니다. 실행하려면 두 번 클릭합니다. 통합된 에이전트는 원본 서버에 적용할 수도 없습니다. 이 목록의 뒷부분에서 설명했듯이 원본 서버에도 설치해야 합니다.
6. **Linux 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면 **UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip**를 마스터 대상 서버로 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.
7. **vContinuum 서버**: **vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe**를 vContinuum 서버에 복사합니다. VContinuum 마법사를 닫았는지 확인합니다. 실행하려면 파일을 두 번 클릭합니다.
8. **Windows 원본 서버**: 통합된 에이전트를 업데이트하려면 **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe**를 원본 서버에 복사합니다. 실행하려면 두 번 클릭합니다.

## 4단계: 복제 설정
1. 원본과 대상 VMware 사이트 간 복제를 설정합니다.
2. 지침은 제품과 함께 다운로드된 InMage Scout 설명서를 사용합니다. 또는 다음과 같이 설명서에 액세스할 수 있습니다.

	- [릴리스 정보](https://aka.ms/asr-scout-release-notes)
	- [호환성 매트릭스](https://aka.ms/asr-scout-cm)
	- [사용자 가이드](https://aka.ms/asr-scout-user-guide)
	- [RX 사용자 가이드](https://aka.ms/asr-scout-rx-user-guide)
	- [빠른 설치 가이드](https://aka.ms/asr-scout-quick-install-guide)


## 업데이트

### Azure Site Recovery 서비스 Scout 8.0.1 업데이트 3
업데이트 3에는 다음 버그 수정 및 향상된 기능이 포함됩니다.

- 구성 서버 및 RX가 프록시 뒤에 있는 경우 Site Recovery 자격 증명 모음에 등록하는 데 실패했습니다.
- 복구 지점 목표(RPO)가 충족되지 않은 시간 수는 상태 보고서에 업데이트 되지 않습니다.
- ESX 하드웨어 세부 설명 및 네트워크 세부 설명이 UTF-8 문자를 포함하는 경우 구성 서버는 RX와 동기화하지 않습니다.
- Windows Server 2008 R2 도메인 컨트롤러가 복구 후 부팅되지 않습니다.
- 오프라인 동기화는 예상대로 작동하지 않습니다.
- 가상 컴퓨터(VM) 장애 조치 후 복제 쌍 삭제는 오랜 시간 동안 CX UI에서 중단되고 사용자는 장애 복구를 완료하거나 작업을 재개할 수 없습니다.
- 응용 프로그램을 감소시킬 수 있도록 최적화된 일관성 작업에 의해 수행된 전체 스냅숏 작업은 SQL 클라이언트와 같은 연결을 끊습니다.
- Windows에서 스냅숏을 만드는 데 필요한 메모리 사용량을 줄여 일관성 도구 (VACP.exe)의 성능이 향상되었습니다.
- 암호가 16자 이상인 경우 푸시 설치 서비스가 충돌합니다.
- 자격 증명을 변경할 경우 vContinuum은 새로운 vCenter 자격 증명을 검사하고 묻는 메시지를 표시하지 않습니다.
- Linux에서 마스터 대상 캐시 관리자(cachemgr)는 복제 쌍을 제한하는 프로세스 서버에서 파일을 다운로드하지 않습니다.
- 물리적 장애 조치 클러스터 (MSCS) 디스크 순서가 모든 노드에서 같지 않을 때 클러스터 볼륨 중 일부에 복제를 설정하지 않습니다. <br/>클러스터를 다시 보호해야 이 수정을 활용할 수 있다는 점에 유의하세요.
- RX가 Scout 7.1에서 Scout 8.0.1로 업그레이드된 후에는 SMTP 기능이 예상대로 작동하지 않습니다.
- 롤백 작업에 대한 로그에서 자세한 통계를 추가하여 완료하는 데 걸린 시간을 추적했습니다.
- 원본 서버에서 Linux 운영 체제에 대한 지원이 추가되었습니다.
	- RHEL(Red Hat Enterprise Linux) 6 업데이트 7
	- CentOS 6 업데이트 7
- CX 및 RX UI는 이제 비트맵 모드로 전환되는 쌍에 대한 알림을 보여 줍니다.
- 다음 보안 수정이 RX에 추가되었습니다.

**문제 설명**|**구현 절차**
---|---
매개 변수 변조를 통해 권한 부여 바이패스|적합하지 않은 사용자에게 제한된 액세스
교차 사이트 요청 위조|모든 페이지에 대해 임의로 생성되는 구현된 페이지 토큰 개념입니다. <br/>이를 사용하면 다음이 나타납니다: <li> 같은 사용자에 대해 단일 로그인 인스턴스만 있습니다 </li><li>페이지 새로 고침이 작동하지 않습니다-대시보드로 리디렉션됩니다.</li>
악성 파일 업로드|특정 확장에 대해 제한된 파일입니다. 허용된 확장자: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, and zip.
영구 사이트 간 스크립팅 | 추가된 입력 유효성 검사


>[AZURE.NOTE]
>
>-	모든 사이트 복구 업데이트는 누적됩니다. 업데이트3은 업데이트1 및 업데이트2를 모두 수정합니다. 업데이트3은 8.0.1 GA에 직접 적용할 수 있습니다.
>-	구성 서버 및 RX 업데이트는 시스템에 적용하면 다시 롤백할 수 없습니다.

### Azure Site Recovery 서비스 Scout 8.0.1 업데이트 2(2015년 12월 3일 업데이트)

업데이트 2에서 수정 사항은 다음과 같습니다.

- **구성 서버**: 사이트 복구에 구성 서버가 등록되었을 때 31일 무료 계량 기능이 예상대로 작동하지 않는 문제 해결.
- **통합 에이전트**: 버전 8.0에서 8.0.1로 업그레이드할 때 마스터 대상 서버에 업데이트가 설치되지 않는 업데이트 1 문제 해결.


### Azure Site Recovery 서비스 Scout 8.0.1 업데이트 1

업데이트 1에는 다음 버그 수정 및 새로운 기능이 포함됩니다.

- 서버 인스턴스 당 31일간 무료 보호됩니다. 기능을 테스트 하거나 개념 증명을 설정할 수 있습니다.
	- 장애 조치 및 장애 복구를 포함한 서버의 모든 작업은 사이트 복구 Scout를 사용하여 서버를 먼저 보호한 시점부터 시작하여 처음 31일에 대해 무료입니다.
	- 32일째부터는 보호된 각 서버가 사이트를 소유하는 고객에게 Azure Site Recovery 보호를 위해 표준 인스턴스 요금으로 청구됩니다.
	- 언제든지 현재 부과되고 있는 보호된 서버의 수는 Azure Site Recovery 자격 증명의 대시보드 페이지에서 사용할 수 있습니다.
- vSphere 명령줄 인터페이스 (vCLI) 5.5 업데이트 2에 대한 지원이 추가됩니다.
- 원본 서버에서 Linux 운영 체제에 대한 추가 지원:
	- RHEL 6 업데이트 6
	- RHEL 5 업데이트 11
	- CentOS 6 업데이트 6
	- CentOS 5 업데이트 11
- 다음과 같은 문제를 해결하는 버그 수정:
	- 서버 구성 또는 RX 서버에 대한 자격 증명 모음 등록에 실패합니다.
	- 다시 시작할 때 가상 컴퓨터가 다시 보호되면 클러스터 볼륨이 예상대로 표시되지 않습니다.
	- 장애 복구는 마스터 대상 서버가 온-프레미스 프로덕션 가상 컴퓨터에서 다른 ESXi 서버에서 호스트 되는 경우 실패합니다.
	- 구성 파일 사용 권한은 8.0.1로 업그레이드 중 변경되어 보호 및 작업에 영향을 줍니다.
	- 다시 동기화 임계값은 예상대로 적용되지 않으며, 일치하지 않는 복제 동작을 수행합니다.
	- 구성 서버 인터페이스에 제대로 나타나지 않는 RPO 설정입니다. 압축되지 않은 데이터 값은 압축된 값을 잘못 표시합니다.
	-  제거 작업은 vContinuum 마법사에서 예상대로 삭제되지 않으며 복제는 구성 서버 인터페이스에서 삭제되지 않습니다.
	-  vContinuum 마법사에서 MSCS 가상 컴퓨터를 보호하는 동안 디스크 보기에서 **세부 정보**를 클릭하면 디스크는 자동으로 선택되지 않습니다.
	- P2V(physical-to-virtual) 시나리오 도중에 CIMnotify나 CqMgHost와 같은 필요한 HP 서비스는 가상 컴퓨터 복구에서 수동으로 이동되지 않습니다. 이 때문에 부팅 시간이 늘어납니다.
	- 마스터 대상 서버에 26개 이상의 디스크가 있으면 Linux 가상 컴퓨터 보호가 실패합니다.

## 다음 단계

문의 사항이 있으시다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시하세요.

<!---HONumber=AcomDC_0921_2016-->