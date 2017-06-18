<properties
   pageTitle="인시던트 대응에 Azure Security Center 활용 | Microsoft Azure"
   description="이 문서에서는 인시던트 대응 시나리오에 Azure Security Center를 활용하는 방법을 설명합니다."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# 인시던트 대응에 Azure Security Center 활용
대부분의 조직은 공격을 받은 후에 보안 인시던트에 대응하는 방법을 알아봅니다. 비용 및 손상을 줄이려면 공격이 발생하기 전에 인시던트 대응 계획을 수립하는 것이 중요합니다. Azure Security Center는 인시던트에 대응하는 여러 단계에서 활용할 수 있습니다.

## 인시던트 대응

효과적인 계획은 위협에 대한 보호, 감지 및 대응이라는 세 가지 핵심 기능이 필요합니다. 보호는 인시던트를 방지하는 것이고 감지는 위협을 조기에 식별하는 것이며 대응은 공격자를 제거하고 시스템을 복원하여 위반의 영향을 완화하는 것입니다.

이 문서에서는 다음 다이어그램에 표시된 대로 [클라우드에서 Microsoft Azure 보안 대응](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) 문서에서 보안 인시던트 대응 단계를 사용합니다.

![인시던트 대응 수명 주기](./media/security-center-incident-response/security-center-incident-response-fig1.png)

감지, 평가 및 진단 단계에서 Security Center를 활용할 수 있습니다. 이러한 각 단계에 대한 자세한 내용을 보려면. 초기 인시던트 대응의 세 가지 단계에서 Security Center를 유용하게 사용할 수 있는 예는 다음과 같습니다.

- **감지**: 이벤트 확인의 첫 번째 표시입니다.
	- 예제: 우선 순위가 높은 보안 경고가 Security Center 대시보드에서 발생하는 것을 초기에 확인합니다.
- **평가**: 초기 평가를 수행하여 의심스러운 작업에 대한 자세한 정보를 가져옵니다.
	- 예제: 보안 경고에 대한 자세한 정보를 가져옵니다.
- **진단**: 기술 조사를 수립하고 포함, 완화 및 해결 방법 전략을 식별합니다.
	- 예제: 해당 특정 보안 경고에서 Security Center에 설명한 수정 단계를 따릅니다.

다음에 나오는 시나리오에서는 보안 인시던트의 감지, 평가 및 진단/대응 단계 동안 Security Center를 활용하는 방법을 보여 줍니다. Security Center에서 [보안 인시던트](security-center-incident.md)는 [kill 체인](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. 인시던트는 [보안 경고](security-center-managing-and-responding-alerts.md) 타일 및 블레이드에 표시됩니다. 인시던트는 관련된 경고 목록을 표시하므로 각 항목에 대한 자세한 정보를 얻을 수 있습니다. 또한 Security Center는 의심스러운 활동을 추적하는 데 사용될 수 있는 독립 실행형 보안 경고를 나타냅니다.

## 시나리오

Contoso는 일부 가상 컴퓨터 기반 비즈니스 워크로드 및 SQL Database 줄을 포함하여 Azure에 일부 해당 온-프레미스 리소스를 최근에 마이그레이션했습니다. 현재 Contoso의 CSIRT(주요 컴퓨터 보안 인시던트 대응 팀)에는 현재 인시던트 대응 도구와 통합된 보안 인텔리전스의 부족으로 인해 보안 문제를 조사하는 데 문제가 있습니다. 조사의 부족으로 인해 감지(너무 많은 가양성) 및 평가와 진단 단계에서 문제가 발생합니다. 이 마이그레이션의 일부로 이 문제를 해결할 수 있도록 Security Center에 옵트인하기로 했습니다.

이 마이그레이션의 첫 번째 단계는 Security Center에서 모든 리소스를 등록하고 모든 보안 권장 사항을 해결한 후에 완료되었습니다. Contoso CSIRT는 컴퓨터 보안 인시던트를 처리하는 초점입니다. 팀은 보안 인시던트를 해결할 책임이 있는 사용자의 그룹으로 구성됩니다. 팀 구성원에게는 대응의 모든 영역을 다루어야 하는 의무가 명확하게 정의되었습니다.

이 시나리오의 목적상 Contoso CSIRT의 일부인 다음 가상 사용자의 역할에 집중하겠습니다.

![인시던트 대응 수명 주기](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Judy는 보안 작업 중이며 그녀는 다음과 같은 업무를 담당합니다.
- 보안 위협에 대한 24시간 모니터링 및 대응
- 클라우드 워크로드 소유자 또는 필요에 따라 보안 분석가에 에스컬레이션

Sam은 보안 분석가이며 다음과 같은 업무를 담당합니다.
- 공격 조사
- 경고 수정
- 워크로드 소유자를 사용하여 완화 확인 및 적용

여기에서 볼 수 있듯이 Judy와 Sam에게는 다른 책임이 있지만 Security Center에서 얻은 정보를 공유해야 합니다.

## 권장된 솔루션

Judy와 Sam에게는 다른 역할이 있지만 Security Center의 다른 영역을 사용하여 일상적인 작업에 대한 관련 정보를 얻을 수 있습니다. Judy는 매일 모니터링 작업의 일부로 보안 경고를 사용합니다.

![보안 경고](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Judy는 감지 및 평가 단계에서 보안 경고를 사용합니다. Judy가 초기 평가를 완료하면 추가 조사가 필요한 경우 Sam에게 문제를 에스컬레이션할 수 있습니다. 이 시점에서 Sam은 Security Center에서 제공한 정보를 사용하여 경우에 따라 다른 데이터 원본과 함께 진단 단계로 진행합니다.


## 이 솔루션을 구현하는 방법 

인시던트 대응 시나리오에서 Azure Security Center를 사용하는 방법을 확인하려면 감지 및 평가 단계에서 Judy의 단계를 따라가고 문제를 진단하기 위해 Sam이 수행하는 작업을 참조하겠습니다.

### 감지 및 평가 인시던트 대응 단계 

Judy는 Azure Portal에 로그인하고 보안 센터 콘솔에 위치합니다. 그녀는 매일 모니터링 작업의 일부로 다음 단계를 수행하여 우선 순위가 높은 보안 경고를 검토하기 시작합니다.

1. **보안 경고** 타일을 클릭하고 **보안 경고** 블레이드에 액세스합니다. ![보안 경고 블레이드](./media/security-center-incident-response/security-center-incident-response-fig4.png)

	> [AZURE.NOTE] 이 시나리오의 목적상 Judy는 위의 그림에서 볼 수 있듯이 악의적인 SQL 작업 경고에 대한 평가를 수행합니다.
2. **악의적인 SQL 작업** 경고를 클릭하고**악의적인 SQL 작업** 블레이드에서 공격받는 리소스를 검토합니다. ![인시던트 세부 정보](./media/security-center-incident-response/security-center-incident-response-fig5.png)
	
	이 블레이드에서 Judy는 공격 받은 리소스에 관하여 공격이 발생한 빈도 및 감지된 시기를 적을 수 있습니다.
3. **공격받은 리소스**를 클릭하여 이 공격에 대한 자세한 정보를 얻을 수 있습니다.

설명을 읽은 후에 이것이 가양성이 아니며 Sam에게 이 경우를 에스컬레이션해야 한다는 점을 Judy는 확신하게 됩니다.

### 인시던트 대응 진단 단계 

Sam은 Judy에게서 이 경우를 받고 Security Center에서 제공하는 수정 단계를 검토하기 시작합니다.

![인시던트 대응 수명 주기](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### 추가 리소스

인시던트 대응 팀은 [Security Center Power BI](security-center-powerbi.md)를 활용하여 시각화할 추가로 조사 중에 사용자를 도울 수 있는 다양한 유형의 보고서를 확인하고 권장 사항 및 보안 경고를 필터링할 수 있습니다. 조사 프로세스 중에 해당 SIEM(보안 정보 및 이벤트 관리) 솔루션을 사용하는 회사의 경우 [솔루션과 Security Center를 통합](security-center-integrating-alerts-with-log-integration.md)할 수도 있습니다. Azure 감사 로그 및 VM 보안 이벤트는 [Azure 로그 통합 도구](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/)를 사용하여 통합될 수도 있습니다. 이 정보는 Security Center에서 제공된 정보와 함께 공격을 조사하는 데 사용할 수 있습니다.


## 결론

인시던트가 발생하기 전에 팀을 구성하는 작업은 조직에 있어서 매우 중요하며 인시던트를 다루는 방법에 긍정적인 영향을 주게 됩니다. 리소스를 모니터링하는 적합한 도구가 있다면 팀이 보안 인시던트를 수정하는 정확한 단계를 수행하는 데 도움이 됩니다. Security Center [감지 기능](security-center-detection-capabilities.md)은 IT를 도와 신속하게 보안 인시던트에 대응하고 보안 문제를 해결할 수 있습니다.

<!---HONumber=AcomDC_0921_2016-->