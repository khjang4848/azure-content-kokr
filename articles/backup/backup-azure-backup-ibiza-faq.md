<properties
   pageTitle="복구 서비스 자격 증명 모음 FAQ | Microsoft Azure"
   description="이 버전의 FAQ는 Azure 백업 서비스의 공개 미리 보기 릴리스를 지원합니다. 백업 에이전트, 백업 및 보존, 복구, 보안 및 Azure 백업 솔루션과 관련하여 자주 묻는 질문에 대한 대답입니다."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="백업 솔루션; 백업 서비스"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="08/21/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# 복구 서비스 자격 증명 모음 - FAQ

> [AZURE.SELECTOR]
- [클래식 모드에 대한 백업 FAQ](backup-azure-backup-faq.md)
- [Resource Manager 모드에 대한 백업 FAQ](backup-azure-backup-ibiza-faq.md)

이 문서에서는 복구 서비스 자격 증명 모음에 지정된 정보를 제공하고 [Azure 백업 FAQ](backup-azure-backup-faq.md)를 보완합니다. Azure 백업 FAQ는 Azure 백업 서비스에 대한 질문 및 답변의 전체 집합을 제공합니다.

이 문서 또는 관련 문서의 Disqus 섹션에서 Azure 백업에 대한 질문을 할 수 있습니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure 백업 서비스에 대한 질문도 게시할 수 있습니다.

## Recovery Services 자격 증명 모음은 Resource Manager에 기반합니다. Backup 자격 증명 모음(기본 모드)은 계속 지원되나요? <br/>
예, 백업 자격 증명 모음도 계속 지원됩니다. [클래식 포털](https://manage.windowsazure.com)에서 백업 자격 증명 모음을 만듭니다. [Azure 포털](https://portal.azure.com)에서 복구 서비스 자격 증명 모음을 만듭니다. 그러나 이후의 모든 향상된 기능은 복구 서비스 자격 증명 모음에서만 사용할 수 있으므로 복구 서비스 자격 증명 모음을 만드는 것이 가장 좋습니다.

## 복구 서비스 자격 증명 모음에 백업 자격 증명 모음을 마이그레이션할 수 있나요? <br/>
아니요, 안타깝지만 지금은 백업 저장소의 컨텐츠를 복구 서비스 자격 증명 모음에 마이그레이션할 수 없습니다. 이 기능을 추가하려고 노력하고 있지만 공개 미리 보기의 일환으로 사용할 수 없습니다.

## 복구 서비스 자격 증명 모음은 클래식 VM 또는 Resource Manager 기반 VM을 지원하나요? <br/>
복구 서비스 자격 증명 모음은 두 모델을 모두 지원합니다. 복구 서비스 자격 증명 모음에 클래식 포털에서 만들어진 VM(즉, 클래식 모드 VM) 또는 Azure 포털에서 만들어진 VM(즉, Resource Manager 기반)을 백업할 수 있습니다.

## 백업 자격 증명 모음에 내 클래식 VM을 백업했습니다. 이제 클래식 모드에서 Resource Manager 모드로 내 VM을 마이그레이션하려고 합니다. 복구 서비스 자격 증명 모음에서 백업하려면 어떻게 해야 하나요?
클래식 모드에서 Resource Manager 모드로 VM을 마이그레이션하는 경우 백업 자격 증명 모음에 있는 클래식 VM을 백업하는 작업은 복구 서비스 자격 증명 모음에 자동으로 마이그레이션되지 않습니다. VM 백업의 마이그레이션을 위한 다음 단계를 따르세요.

1. 백업 자격 증명 모음에서 **보호된 항목** 탭으로 이동하고 VM을 선택합니다. [보호 중지](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)를 클릭합니다. *연결된 백업 데이터 삭제* 옵션을 **검사하지 않음**으로 둡니다.
2. 클래식 모드에서 Resource Manager 모드로 가상 컴퓨터를 마이그레이션합니다. 가상 컴퓨터에 해당하는 저장소 및 네트워크가 Resource Manager 모드로 마이그레이션되도록 합니다.
3. 자격 증명 모음 대시보드를 기반으로 **백업** 작업을 사용하여 복구 서비스 자격 증명 모음을 만들고 마이그레이션된 가상 컴퓨터에 백업을 구성합니다. [복구 서비스 자격 증명 모음에서 백업을 사용하도록 설정](backup-azure-vms-first-look-arm.md)하는 방법에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0928_2016-->