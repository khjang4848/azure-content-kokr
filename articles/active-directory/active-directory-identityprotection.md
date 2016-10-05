<properties
	pageTitle="Azure Active Directory ID 보호 | Microsoft Azure"
	description="Azure AD ID 보호를 사용하여 손상된 ID 및 장치를 악용하는 공격자의 능력을 제한하고 이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호할 수 있는 방법을 알아봅니다."
	services="active-directory"
	keywords="Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="markvi"/>

#Azure Active Directory ID 보호 

Azure Active Directory ID 보호는 조직의 ID에 영향을 주는 위험 이벤트와 잠재적 취약성에 대한 통합된 뷰를 제공하는 Azure AD Premium P2 버전의 기능입니다. Microsoft는 10년 넘게 클라우드 기반 ID를 보호했습니다. Microsoft는 Azure AD ID 보호를 사용하여 기업 고객이 이러한 동일한 보호 시스템을 사용할 수 있도록 노력하고 있습니다. ID 보호는 기존 Azure AD의 비정상 감지 기능(Azure AD의 비정상적인 작업 보고서를 통해 사용 가능)을 활용하고 실시간으로 잘못된 부분을 검색할 수 있는 새로운 위험 이벤트 유형을 소개합니다.



##시작하기

대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 공격자는 점점 더 효과적으로 제3자 침해를 활용하고 정교한 피싱 공격을 사용하고 있습니다. 일단 공격자가 더 낮은 권한을 가진 사용자 계정에 대한 액세스 권한을 얻게 되면 측면 이동을 통해 비교적 간단하게 중요한 회사 리소스에 대한 액세스 권한을 얻게 됩니다. 따라서 모든 ID를 보호하고 ID가 손상된 경우 사전에 손상된 ID가 악용되지 않도록 방지하는 것이 필수적입니다.

손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. 다행히도 ID 보호로 도울 수 있습니다. ID 보호는 적응 기계 학습 알고리즘 및 경험적 접근 방법을 사용하여 ID가 손상되었음을 나타낼 수 있는 잘못된 부분 및 위험 이벤트를 감지합니다.
 
ID 보호는 이 데이터를 사용하여 이러한 위험 이벤트를 조사하고 적절한 수정 또는 완화 작업을 수행할 수 있도록 하는 보고서 및 경고를 생성합니다.
 
하지만 Azure Active Directory ID 보호는 모니터링 및 보고 도구 이상입니다. ID 보호는 위험 이벤트에 따라 각 사용자에 대한 위험 수준을 계산하며 이는 위험 기반 정책을 구성하여 조직의 ID를 자동으로 보호할 수 있도록 합니다. Azure Active Directory 및 EMS에서 제공되는 다른 조건부 액세스 제어 외에도 이러한 위험 기반 정책은 암호 재설정 및 Multi-Factor Authentication 적용을 포함하는 적응형 수정 작업을 자동으로 차단하거나 제공할 수 있습니다.

####ID 보호의 기능 살펴보기 

**위험 이벤트 및 위험한 계정 검색:**

- 기계 학습 및 경험적 접근 규칙을 사용하여 6가지 위험 이벤트 유형 검색

- 사용자 위험 수준 계산

- 취약점을 강조 표시하여 전반적인 보안 포스처를 개선하도록 사용자 지정 권장 사항 제공



**위험 이벤트 조사:**

- 위험 이벤트에 대한 알림 보내기

- 관련된 컨텍스트 정보를 사용하여 위험 이벤트 조사

- 기본 워크플로를 제공하여 조사 추적

- 암호 재설정 등 수정 작업에 쉬운 액세스 제공



**위험 기준 조건부 액세스 정책:**

- 로그인을 차단하거나 Multi-Factor Authentication 과제를 요구하여 위험한 로그인을 완화하는 정책입니다.

- 위험한 사용자 계정 차단 및 보호 정책

- Multi-Factor Authentication에 사용자 등록 요구 정책


## 검색 및 위험

### 위험 이벤트

위험 이벤트는 ID 보호에서 의심스러운 것으로 플래그가 지정된 이벤트이며 ID가 손상되었을 수 있음을 나타냅니다. 위험 이벤트의 전체 목록은 [Azure Active Directory ID 보호에서 검색하는 위험 이벤트의 유형](active-directory-identityprotection-risk-events-types.md)을 참조하세요.

이러한 위험 이벤트 중 일부는 Azure 포털에서 Azure AD 비정상적인 작업 보고서를 통해 제공되었습니다. 아래 테이블에서는 다양한 위험 이벤트 유형 및 해당하는 **Azure AD 비정상적인 작업** 보고서를 나열합니다. Microsoft는 이 공간에 계속 투자하며 지속적으로 기존 위험 이벤트의 검색 정확도를 향상시키고 새 위험 이벤트 유형을 추가할 예정입니다.



| ID 보호 위험 이벤트 유형 | 해당하는 Azure AD 비정상적인 작업 보고서 |
| :-- | :-- |
| 유출된 자격 증명 | 자격 증명이 손실된 사용자 |
| 비정상적 위치로 불가능한 이동 |	비정상적인 로그인 작업 |
| 감염된 장치에서 로그인 | 감염 가능성이 있는 장치에서 로그인 |
| 익명 IP 주소에서 로그인 | 알 수 없는 원본에서 로그인 |
| 의심스러운 작업이 있는 IP 주소에서 로그인 |	의심스러운 작업이 있는 IP 주소에서 로그인 |
| 알 수 없는 위치에서 로그인 | - |
| 잠금 이벤트 | - |

다음의 Azure AD 비정상적인 작업 보고서는 Azure AD ID 보호에서 위험 이벤트로 포함되지 않으며 따라서 ID 보호를 통해 사용할 수 없습니다. 이러한 보고서는 Azure 포털에서 사용할 수 있지만 나중에 ID 보호에서 위험 이벤트에 의해 대체되는 경우 사용되지 않게 됩니다.

- 여러 번의 실패 후 로그인
- 여러 지역에서의 로그인

### 위험 수준

위험 이벤트에 대한 위험 수준은 위험 이벤트의 심각도를 표시(높음, 보통 또는 낮음)합니다. 위험 수준은 ID 보호 사용자가 해당 조직에 대한 위험을 줄이기 위해 취해야 하는 작업의 우선 순위를 지정하도록 합니다. 위험 이벤트의 심각도는 신호의 강도를 ID 손상의 예측 변수로 나타내며 일반적으로 생성되는 노이즈의 양과 결합됩니다.

- **높음**: 신뢰도 및 심각도가 높은 위험 이벤트입니다. 이러한 이벤트는 사용자의 ID가 손상되었음을 나타내는 확실한 지표이며 영향을 받는 모든 사용자는 즉시 수정되어야 합니다.

- **보통**: 심각도가 높지만 신뢰도가 낮은 위험 이벤트 또는 그 반대의 경우입니다. 이러한 이벤트는 잠재적으로 위험하며 영향을 받는 사용자 계정은 수정되어야 합니다.

- **낮음**: 신뢰도 및 심각도가 낮은 위험 이벤트입니다. 이 이벤트는 즉각적인 조치가 필요하지 않을 수 있지만 다른 위험 이벤트와 결합되면 ID가 손상되었음을 확실하게 표시할 수 있습니다.


![위험 수준](./media/active-directory-identityprotection/01.png "위험 수준")

 

위험 이벤트는(오프라인으로) 이미 발생한 후에 **실시간** 또는 후처리로 식별됩니다. 현재 ID 보호에서 대부분의 위험 이벤트는 오프라인으로 계산되며 2-4시간 내에 ID 보호에 표시됩니다. 실시간으로 평가되는 동안 실시간 위험 이벤트는 5-10분 내에 ID 보호 콘솔에 표시됩니다.

현재 일부 레거시 클라이언트는 실시간 위험 이벤트 감지 및 방지를 지원하지 않습니다. 결과적으로, 이러한 클라이언트에서 로그인을 실시간으로 감지하거나 방지할 수 없습니다.


## 조사
ID 보호를 통한 이동은 일반적으로 ID 보호 대시보드를 시작합니다.

![재구성](./media/active-directory-identityprotection/1000.png "재구성")

대시보드는 다음에 대한 액세스를 제공합니다.
 
- **위험에 대한 플래그가 지정된 사용자**, **위험 이벤트** 및 **취약점**고 같은 보고서
- **보안 정책**, **알림** 및 **Multi-Factor Authentication 등록**의 구성과 같은 설정
 

일반적으로 조사를 위한 시작점이며, 수정 또는 완화 단계가 필요한지, ID가 어떻게 손상되었고 손상된 ID가 어떻게 사용되었는지를 결정하기 위해 작업, 로그 및 위험 이벤트와 관련된 기타 관련 정보를 검토하는 과정입니다.

Azure Active Directory 보호에서 전자 메일을 통해 보내는 [알림](active-directory-identityprotection-notifications.md)에 조사 활동을 연결할 수 있습니다.

다음 섹션에서는 조사와 관련된 자세한 내용 및 단계를 제공합니다.



## 사용자 위험 수준이란?

사용자 위험 수준은 사용자의 ID가 손상된 가능성을 표시(높음, 보통 또는 낮음)합니다. 사용자의 ID와 연결된 사용자 위험 이벤트에 따라 계산됩니다.

위험 이벤트의 상태는 **활성** 또는 **닫힘**입니다. **활성** 상태인 위험 이벤트만이 사용자 위험 계산에 사용됩니다.

사용자 위험 수준은 다음 입력을 사용하여 계산됩니다.

- 사용자에 영향을 주는 활성 위험 이벤트
- 이러한 이벤트의 위험 수준
- 수정 작업이 수행되었는지 여부

![사용자 위험](./media/active-directory-identityprotection/1001.png "사용자 위험")



사용자 위험 수준을 사용하여 위험한 사용자의 로그인을 차단하는 조건부 액세스 정책을 만들 수 있고 해당 암호를 안전하게 변경하도록 강제할 수 있습니다.


## 수동으로 위험 이벤트 닫기

대부분의 경우에서 자동으로 위험 이벤트를 닫도록 다시 설정된 보안 암호와 같은 수정 작업을 수행합니다. 그러나 불가능할 수 있습니다. 예를 들어 다음과 같은 경우에 그렇습니다.

- 활성 위험 이벤트가 있는 사용자가 삭제된 경우
- 조사 결과, 보고된 위험 이벤트가 합법적인 사용자에 의해 수행된 것으로 드러난 경우

**활성** 상태인 위험 이벤트는 사용자 위험 계산에 사용될 수 있기 때문에 수동으로 위험 이벤트를 닫아서 위험 수준을 낮춰야 합니다. 조사하는 과정 동안 이러한 작업을 수행하여 위험 이벤트의 상태를 변경할 수 있습니다.

![작업](./media/active-directory-identityprotection/34.png "작업")

- **해결** - 위험 이벤트를 검사한 후에 ID 보호 외부에서 적절한 수정 작업을 수행하고 위험 이벤트가 닫힌 것으로 간주되어야 한다고 판단하는 경우 이벤트를 "해결됨"으로 표시합니다. 해결된 이벤트는 위험 이벤트의 상태를 닫힘으로 설정하고 위험 이벤트는 더 이상 사용자 위험에 영향을 주지 않습니다.

- **가양성으로 표시** - 경우에 따라 위험 이벤트를 조사하여 플래그 지정이 위험으로 잘못 지정되었음을 발견할 수 있습니다. 위험 이벤트를 가양성으로 표시하여 이러한 발생 횟수를 줄일 수 있습니다. 기계 학습 알고리즘이 나중에 비슷한 이벤트를 분류하는 작업을 개선하도록 합니다. 가양성 이벤트가 **닫힘** 상태이면 더 이상 사용자 위험에 영향을 주지 않습니다.

- **무시** - 수정 작업을 수행하지 않지만 위험 이벤트를 활성 목록에서 제거하려면 위험 이벤트를 무시로 표시할 수 있고 이벤트 상태가 닫히게 됩니다. 무시된 이벤트는 사용자 위험에 영향을 주지 않습니다. 이 옵션은 이례적인 상황에서만 사용해야 합니다.

- **다시 활성화** - 수동으로 닫힌 위험 이벤트(**해결**, **가양성** 또는 **무시**를 선택하여)는 이벤트 상태를 다시 **활성**으로 설정하여 다시 활성화될 수 있습니다. 다시 활성화된 위험 이벤트는 사용자 위험 수준 계산에 영향을 줍니다. 수정(예: 보안 암호를 다시 설정)을 통해 닫힌 위험 이벤트를 다시 활성화할 수 없습니다.




**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드의 **조사** 아래에서 **위험 이벤트**를 클릭합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1002.png "수동 암호 다시 설정")

2. **위험 이벤트** 목록에서 위험을 클릭합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1003.png "수동 암호 다시 설정")

2. 위험 블레이드에서 사용자를 마우스 오른쪽 단추로 클릭합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1004.png "수동 암호 다시 설정")



### 사용자에 대한 모든 위험 이벤트를 수동으로 닫기

수동으로 사용자에 대한 위험 이벤트를 개별적으로 닫는 대신 Azure Active Directory ID 보호는 한 번의 클릭으로 사용자에 대한 모든 이벤트를 닫는 메서드를 제공합니다.


![작업](./media/active-directory-identityprotection/2222.png "작업")

**모든 이벤트 해제**를 클릭하면 모든 이벤트가 닫히고 영향을 받는 사용자가 더 이상 위험에 노출되지 않습니다.



## 사용자 위험 이벤트 수정

수정은 이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호하는 작업입니다. 수정 작업은 ID 또는 장치를 안전한 상태로 복원하고 ID 또는 장치와 연결된 이전 위험 이벤트를 확인합니다.

사용자 위험 이벤트를 수정하려면 다음을 수행할 수 있습니다.

- 보안 암호 재설정을 수행하여 수동으로 사용자 위험 이벤트 수정

- 사용자 위험 보안 정책을 구성하여 자동으로 사용자 위험 이벤트 완화 또는 수정

- 감염된 장치를 재이미징


### 수동으로 안전한 암호 다시 설정

안전한 암호 다시 설정은 여러 위험 이벤트를 효과적으로 수정하며 수행될 때 자동으로 이러한 위험 이벤트를 닫고 사용자 위험 수준을 다시 계산합니다. ID 보호 대시보드를 사용하여 위험한 사용자에 대한 암호 다시 설정을 시작할 수 있습니다.

관련 대화 상자에 암호를 다시 설정할 수 있는 두 가지 방법이 제공됩니다.

**암호 다시 설정** - **사용자가 암호를 다시 설정해야 합니다**를 선택하여 사용자가 Multi-Factor Authentication에 등록하는 경우 자체 복구할 수 있도록 합니다. 사용자가 다음 번에 로그인하는 동안 사용자는 Multi-Factor Authentication 과제를 성공적으로 해결해야 하며 암호를 변경하도록 강제됩니다. 사용자 계정이 Multi-Factor Authentication에 등록되지 않은 경우 이 옵션을 사용할 수 없습니다.

**임시 암호** - **임시 암호 생성**을 선택하여 즉시 기존 암호를 무효화하고 사용자에 대한 새 임시 암호를 만듭니다. 사용자에 대한 대체 전자 메일 주소 또는 사용자의 관리자에게 새 임시 암호를 보냅니다. 암호가 임시적이기 때문에 사용자는 로그인 시 암호를 변경하라는 메시지를 받습니다.


![정책](./media/active-directory-identityprotection/1005.png "정책")


**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드에서 **위험에 대한 플래그가 지정된 사용자**를 클릭합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1006.png "수동 암호 다시 설정")


2. 사용자 목록에서 하나 이상의 위험 이벤트와 사용자를 선택합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1007.png "수동 암호 다시 설정")


2. 사용자 블레이드에서 **암호 재설정**을 클릭합니다.

	![수동 암호 다시 설정](./media/active-directory-identityprotection/1008.png "수동 암호 다시 설정")





## 사용자 위험 보안 정책

사용자 위험 보안 정책은 특정 사용자에 대한 위험 수준을 평가하고 미리 정의된 조건 및 규칙에 따라 수정 및 완화 작업을 적용하는 조건부 액세스 정책입니다.


![사용자 ridk 정책](./media/active-directory-identityprotection/1009.png "사용자 ridk 정책")


Azure AD ID 보호를 사용하면 다음을 사용하여 위험에 플래그가 지정된 사용자의 완화 및 수정을 관리할 수 있습니다.

- 정책이 적용되는 사용자 및 그룹 설정:

	![사용자 ridk 정책](./media/active-directory-identityprotection/1010.png "사용자 ridk 정책")


- 정책을 트리거하는 사용자 위험 수준 임계값 설정(낮음, 보통 또는 높음):

	![사용자 ridk 정책](./media/active-directory-identityprotection/1011.png "사용자 ridk 정책")


- 정책이 트리거할 때 적용될 컨트롤 설정:

	![사용자 ridk 정책](./media/active-directory-identityprotection/1012.png "사용자 ridk 정책")


- 정책 상태 전환:

	![사용자 ridk 정책](./media/active-directory-identityprotection/403.png "MFA 등록")


- 활성화하기 전에 변경의 영향 검토 및 평가:

	![사용자 ridk 정책](./media/active-directory-identityprotection/1013.png "사용자 ridk 정책")


**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. 그러나 정책에서 위험에 플래그가 지정된 **낮음** 및 **보통** 사용자를 제외하며 이는 이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호하지 않을 수 있습니다.

정책을 설정 하는 경우

- 많은 가양성(개발자, 보안 분석가)을 생성할 수 있는 사용자를 제외합니다.

- 정책을 사용하지 않는 것이 실용적이지 않은 로캘에서 사용자를 제외합니다(예: 기술 지원팀에 액세스 권한 없음).

- 초기 정책이 롤아웃하는 동안 또는 최종 사용자가 확인하는 과제를 최소화해야 하는 경우 **높음** 임계값을 사용합니다.

- 조직이 보안을 강화해야 하는 경우 **낮음** 임계값을 사용합니다. **낮은** 임계값을 선택하면 추가 사용자 로그인 과제가 발생하지만 보안이 강화됩니다.

대부분의 조직에 권장되는 기본값은 **보통** 임계값에 대한 규칙을 구성하여 유용성과 보안 간의 균형을 유지할 수 있습니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [손상된 계정 복구 흐름](active-directory-identityprotection-flows.md#compromised-account-recovery)

- [손상된 계정 차단됨 흐름](active-directory-identityprotection-flows.md#compromised-account-blocked)


**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드의 **구성** 섹션에서 **사용자 위험 정책**을 클릭합니다.

	![사용자 ridk 정책](./media/active-directory-identityprotection/1009.png "사용자 ridk 정책")






## 사용자 위험 이벤트 완화
관리자는 사용자 위험 보안 정책을 설정하여 위험 수준에 따라 로그인 시 사용자를 차단할 수 있습니다.

로그인 차단:
 
- 영향을 받는 사용자에 대한 새 사용자 위험 이벤트의 생성 방지

- 관리자가 사용자의 ID에 영향을 주는 위험 이벤트를 수동으로 수정하고 안전한 상태로 복원할 수 있음



## 로그인 위험 수준이란?

로그인 위험 수준은 특정 로그인에 대해 누군가 사용자의 ID로 인증하려고 시도할 가능성(높음, 중간 또는 낮음)을 나타냅니다. 로그인 위험 수준은 로그인 당시에 평가되며, 해당 특정 로그인에 대해 실시간으로 검색된 표시기 및 위험 이벤트를 고려합니다.

## 로그인 위험 이벤트 완화 
완화는 ID 또는 장치를 안전한 상태로 복원하지 않고 손상된 ID 또는 장치를 악용하는 공격자의 능력을 제한하는 작업입니다. 완화 조치는 ID 또는 장치와 연결된 이전 로그인 위험 이벤트를 해결하지 않습니다.

Azure AD ID 보호에 조건부 액세스를 사용하여 자동으로 로그인 위험 이벤트를 완화할 수 있습니다. 이러한 정책을 사용하여 사용자 또는 로그인의 위험 수준을 고려하여 위험한 로그인을 차단하거나 사용자가 Multi-Factor Authentication을 수행하도록 합니다. 이러한 작업은 공격자가 피해를 일으키는 도난 당한 ID를 악용하지 않도록 방지하며 ID를 보호할 시간을 제공할 수 있습니다.


## 로그인 위험 보안 정책

로그인 위험 정책은 특정 로그인에 대한 위험을 평가하고 미리 정의된 조건 및 규칙에 따라 완화를 적용하는 조건부 액세스 정책입니다.

![로그인 위험 정책](./media/active-directory-identityprotection/1014.png "로그인 위험 정책")


Azure AD ID 보호를 사용하면 다음을 사용하여 위험한 로그인을 완화하도록 관리할 수 있습니다.

- 정책이 적용되는 사용자 및 그룹 설정:

	![로그인 위험 정책](./media/active-directory-identityprotection/1015.png "로그인 위험 정책")


- 정책을 트리거하는 로그인 위험 수준 임계값 설정(낮음, 보통 또는 높음):

	![로그인 위험 정책](./media/active-directory-identityprotection/1016.png "로그인 위험 정책")


- 정책이 트리거할 때 적용될 컨트롤 설정:

	![로그인 위험 정책](./media/active-directory-identityprotection/1017.png "로그인 위험 정책")


- 정책 상태 전환:

	![MFA 등록](./media/active-directory-identityprotection/403.png "MFA 등록")

- 활성화하기 전에 변경의 영향 검토 및 평가:

	![로그인 위험 정책](./media/active-directory-identityprotection/1018.png "로그인 위험 정책")

 
**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다.
 
그러나 정책에서 위험에 플래그가 지정된 **낮은** 및 **보통** 로그인을 제외하며 이는 공격자가 손상된 ID를 악용하지 않도록 차단하지 않을 수 있습니다.

정책을 설정 하는 경우

- 다단계 인증이 없는/있을 수 없는 사용자 제외

- 정책을 사용하지 않는 것이 실용적이지 않은 로캘에서 사용자를 제외합니다(예: 기술 지원팀에 액세스 권한 없음).

- 많은 가양성(개발자, 보안 분석가)을 생성할 수 있는 사용자를 제외합니다.

- 초기 정책이 롤아웃하는 동안 또는 최종 사용자가 확인하는 과제를 최소화해야 하는 경우 **높음** 임계값을 사용합니다.

- 조직이 보안을 강화해야 하는 경우 **낮음** 임계값을 사용합니다. **낮은** 임계값을 선택하면 추가 사용자 로그인 과제가 발생하지만 보안이 강화됩니다.

대부분의 조직에 권장되는 기본값은 **보통** 임계값에 대한 규칙을 구성하여 유용성과 보안 간의 균형을 유지할 수 있습니다.

 
로그인 위험 정책은 다음과 같습니다.

- 최신 인증을 사용하여 모든 브라우저 트래픽 및 로그인에 적용합니다.
- ADFS 등 페더레이션된 IDP에서 WS-Trust 끝점을 사용하지 않도록 설정하여 이전 보안 프로토콜을 사용하는 응용 프로그램에 적용하지 않습니다.

ID 보호 콘솔의 **위험 이벤트** 페이지에서는 모든 이벤트를 나열합니다.

- 이 정책은 다음에 적용되었습니다.
- 작업을 검토하고 작업이 적절한지 여부를 결정할 수 있습니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [위험한 로그인 복구](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

- [위험한 로그인 차단됨](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [위험한 로그인 시 Multi-Factor Authentication 등록](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드의 **구성** 섹션에서 **로그인 위험 정책**을 클릭합니다.

	![사용자 ridk 정책](./media/active-directory-identityprotection/1014.png "사용자 ridk 정책")





## Multi-Factor Authentication 등록 정책

Azure Multi-Factor Authentication은 사용자 이름 및 암호 이외의 다른 내용을 사용해야 하는 사람인지를 확인하는 메서드입니다. 사용자 로그인 및 트랜잭션에 대한 보안의 두 번째 계층을 제공합니다. 다음과 같은 이유로, 사용자 로그인에 Azure Multi-Factor Authentication을 요구하는 것이 좋습니다.

- 다양한 간편한 확인 옵션과 함께 강력한 인증 제공

- 조직에서 계정 손상을 방지 및 복구하도록 준비하는 데 중요한 역할 수행

![사용자 ridk 정책](./media/active-directory-identityprotection/1019.png "사용자 ridk 정책")



자세한 세부 내용은 [Azure Multi-Factor Authentication 정의](../multi-factor-authentication/multi-factor-authentication.md)를 참조하세요.


Azure AD ID 보호를 사용하면 다음을 지원하는 정책을 구성하여 Multi-Factor Authentication 등록의 롤아웃을 관리할 수 있습니다.



- 정책이 적용되는 사용자 및 그룹 설정:

	![MFA 정책](./media/active-directory-identityprotection/1020.png "MFA 정책")



- 정책이 트리거할 때 적용될 컨트롤 설정:

	![MFA 정책](./media/active-directory-identityprotection/1021.png "MFA 정책")


- 정책 상태 전환:

	![MFA 정책](./media/active-directory-identityprotection/403.png "MFA 정책")

- 현재 등록 상태 보기:

	![MFA 정책](./media/active-directory-identityprotection/1022.png "MFA 정책")


관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Multi-Factor Authentication 등록 흐름](active-directory-identityprotection-flows.md#multi-factor-authentication-registration)

- [위험한 로그인 시 Multi-Factor Authentication 등록](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**관련 구성 대화 상자를 열려면**

1. **Azure AD ID 보호** 블레이드의 **구성** 섹션에서 **Multi-Factor Authentication 등록**을 클릭합니다.

	![MFA 정책](./media/active-directory-identityprotection/1019.png "MFA 정책")





## 다음 단계

 - [Channel 9: Azure AD 및 ID 표시: ID 보호 미리 보기](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Azure Active Directory ID 보호에서 검색한 위험 이벤트의 유형](active-directory-identityprotection-risk-events-types.md)
 - [Azure Active Directory ID 보호에서 검색하는 취약성](active-directory-identityprotection-vulnerabilities.md)
 - [Azure Active Directory ID 보호 알림](active-directory-identityprotection-notifications.md)
 - [Azure Active Directory ID 보호 흐름](active-directory-identityprotection-flows.md)
 - [Azure Active Directory ID 보호 플레이 북](active-directory-identityprotection-playbook.md)
 - [Azure Active Directory ID 보호 용어집](active-directory-identityprotection-glossary.md)

 - [Azure Active Directory ID 보호 활성화](active-directory-identityprotection-enable.md)
 - [Azure Active Directory ID 보호 - 사용자를 차단 해제하는 방법](active-directory-identityprotection-unblock-howto.md)

 - [Azure Active Directory ID 보호 및 Microsoft Graph 시작](active-directory-identityprotection-graph-getting-started.md)

<!---HONumber=AcomDC_0921_2016-->