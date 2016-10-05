
<properties
	pageTitle="AD FS와 함께 Azure AD Connec Health 사용 | Microsoft Azure"
	description="온-프레미스 AD FS 인프라를 모니터링하는 방법에 대한 Azure AD Connect Health 페이지입니다."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="femila"
	editor="karavar"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="vakarand"/>

# AD FS와 함께 Azure AD Connect Health 사용
다음 문서는 AZure AD Connect Health와 함께 AD FS 인프라 모니터링에 중점을 둡니다. Azure AD Connect Health와 함께 Azure AD Connect (동기화)를 모니터링하는 방법에 대한 정보는 [동기화를 위해 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)을 참조하세요. 또한 Azure AD Connect Health와 함께 Active Directory 도메인 서비스를 모니터링하는 방법에 대한 정보는 [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)을 참조하세요.

## AD FS의 경고
Azure AD Connect Health 경고 섹션은 활성 경고 목록을 제공합니다. 각 경고에는 관련 정보, 해결 단계 및 관련된 설명서 링크가 포함됩니다. 활성 또는 해결된 경고를 선택하면 추가 정보는 물론, 경고를 해결하기 위해 수행할 수 있는 단계와 추가 설명서 링크가 포함된 새 블레이드가 표시됩니다. 과거에 해결된 경고에 대한 기록 데이터도 볼 수 있습니다.

경고를 선택하면 추가 정보는 물론 경고를 해결하기 위해 수행할 수 있는 단계와 추가 설명서 링크가 제공됩니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/alert2.png)



## AD FS의 사용량 분석
Azure AD Connect Health 사용 현황 분석에서는 페더레이션 서버의 인증 트래픽을 분석합니다. 사용 현황 분석 상자를 선택하면 메트릭 및 그룹화가 표시된 사용 현황 분석 블레이드가 열립니다.

>[AZURE.NOTE] AD FS가 포함된 사용 현황 분석을 사용하려면 AD FS 감사가 사용하도록 설정되어 있어야 합니다. 자세한 내용은 [AD FS 감사 사용](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs)을 참조하십시오.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/report1.png)

추가 메트릭을 선택하거나, 시간 범위를 지정하거나, 그룹화를 변경하려면 간단히 사용 현황 분석 차트를 마우스 오른쪽 단추로 클릭하고 차트 편집을 선택합니다. 그러면 시간 범위를 지정하고, 메트릭을 변경하거나 선택하고, 그룹화를 변경할 수 있습니다. 서로 다른 "메트릭"을 기준으로 인증 트래픽 분포를 보고 아래 설명된 관련 "그룹화 기준" 매개 변수를 사용하여 각 메트릭을 그룹화할 수 있습니다.

| 메트릭 | 그룹화 기준 | 그룹화의 의미 및 그룹화가 유용한 이유 |
| ------ | -------- | -------------------------------------------- |
| 전체 요청: 페더레이션 서비스에서 처리하는 전체 요청 수 | 모두 | 요청을 그룹화하지 않고 전체 요청 수를 표시합니다. |
| | 응용 프로그램 | 이 옵션은 대상 신뢰 당사자를 기준으로 전체 요청을 그룹화합니다. 이 그룹화는 전체 트래픽 중 응용 프로그램이 수신하는 트래픽의 비율을 이해하는 데 유용합니다. |
| | 서버 | 이 옵션은 요청을 처리한 서버를 기준으로 전체 요청을 그룹화합니다. 이 그룹화는 전체 트래픽의 부하 분포를 이해하는 데 유용합니다. |
| | 작업 공간 연결 | 이 옵션은 전체 요청을 작업 공간이 연결된(알려진) 장치의 요청인지 아닌지를 기준으로 그룹화합니다. 이 그룹화는 ID 인프라에 알려지지 않은 장치를 사용하여 리소스에 액세스하는 경우를 이해하는 데 유용합니다. |
| | 인증 방법 | 이 옵션은 인증에 사용된 인증 방법을 기준으로 전체 요청을 그룹화합니다. 이 그룹화는 인증에 사용되는 일반적인 인증 방법을 이해하는 데 유용합니다. 가능한 인증 방법은 다음과 같습니다. <ol> <li>Windows 통합 인증(Windows)</li> <li>양식 기반 인증(양식)</li> <li>SSO(Single Sign On)</li> <li>X509 인증서 인증(인증서)</li> <br>페더레이션 서버가 SSO 쿠키와 함께 요청을 수신하면 해당 요청은 SSO(Single Sign On)로 간주됩니다. 이 경우 쿠키가 유효하면 사용자는 자격 증명을 입력할 필요 없이 효율적으로 응용 프로그램에 액세스할 수 있습니다. 페더레이션 서버에서 여러 신뢰 당사자를 보호하는 경우 일반적으로 사용됩니다. |
| | 네트워크 위치 | 이 옵션은 사용자의 네트워크 위치를 기준으로 전체 요청을 그룹화합니다. 네트워크 위치는 인트라넷 또는 엑스트라넷이 될 수 있습니다. 이 그룹화는 인트라넷 트래픽과 엑스트라넷 트래픽의 비율을 파악하는 데 유용합니다. |
| 전체 실패한 요청: 페더레이션 서비스에서 처리하는 전체 실패한 요청 수 <br> (이 메트릭은 Windows Server 2012 R2 용 AD FS에서만 사용할 수 있습니다.)| 오류 유형 | 미리 정의된 오류 유형을 기준으로 오류 수를 표시합니다. 이 그룹화는 오류의 일반적인 유형을 이해하는 데 유용합니다. <ul><li>잘못된 사용자 이름 또는 암호: 잘못된 사용자 이름 또는 암호로 인해 발생하는 오류입니다.</li> <li>"엑스트라넷 잠김": 엑스트라넷에서 잠겨 있는 사용자로부터 요청을 수신하여 발생하는 오류입니다. </li><li> "만료된 암호": 사용자가 만료된 암호로 로그인을 시도하여 발생하는 오류입니다.</li><li>"사용하지 않는 계정": 사용자가 사용하지 않는 계정으로 로그인을 시도하여 발생하는 오류입니다.</li><li>"장치 인증": 사용자가 장치 인증으로 인증을 받지 못하여 발생하는 오류입니다.</li><li>"사용자 인증서 인증": 사용자가 잘못된 인증서로 인해 인증에 실패하여 발생하는 오류입니다.</li><li>"MFA": 사용자가 Multi Factor Authentication으로 인증을 받지 못하여 발생하는 오류입니다.</li><li>"기타 자격 증명": "발급 권한 부여": 권한 부여 실패로 인해 발생하는 오류입니다.</li><li>"발급 위임": 발급 위임 오류로 인해 발생하는 오류입니다.</li><li>"토큰 수용": ADFS가 타사 ID 공급자의 토큰을 거부해서 발생하는 오류입니다.</li><li>"프로토콜": 프로토콜 오류로 인해 발생하는 오류입니다.</li><li>"알 수 없음": 포괄적인 오류입니다. 정의된 카테고리에 맞지 않는 다른 모든 오류입니다.</li> |
| | 서버 | 서버를 기준으로 오류를 그룹화합니다. 서버 전체의 오류 분포를 파악하는 데 유용합니다. 분포가 균일하지 않으면 특정 서버에 문제가 있음을 나타낼 수 있습니다. |
| | 네트워크 위치 | 요청의 네트워크 위치(인트라넷 및 엑스트라넷)를 기준으로 오류를 그룹화합니다. 실패하는 요청의 유형을 이해하는 데 유용합니다. |
| | 응용 프로그램 | 대상 응용 프로그램(신뢰 당사자)을 기준으로 오류를 그룹화합니다. 오류가 가장 많이 발생하는 대상 응용 프로그램을 파악하는 데 유용합니다. |
| 사용자 수: 시스템에서 활성 상태인 고유 사용자 수 평균 | 모두 | 선택한 시간 조각에서 페더레이션 서비스를 사용하는 사용자 수 평균을 제공합니다. 사용자가 그룹화되지 않습니다. <br>평균은 선택한 시간 조각에 따라 달라집니다. |
| | 응용 프로그램 | 대상 응용 프로그램(신뢰 당사자)을 기준으로 평균 사용자 수를 그룹화합니다. 얼마나 많은 사용자가 어떤 응용 프로그램을 사용하는지 파악하는 데 유용합니다. |


## AD FS의 모니터링 성능
Azure AD Connect Health 성능 모니터링은 메트릭에 대한 모니터링 정보를 제공합니다. 모니터링 상자를 선택하면 메트릭에 대한 자세한 정보를 제공하는 블레이드가 열립니다.


![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health/perf1.png)


블레이드 맨 위의 옵션을 선택하여 서버별로 필터링하면 개별 서버의 메트릭을 확인할 수 있습니다. 메트릭을 변경하려면 간단히 모니터링 블레이드 아래 모니터링 차트를 마우스 오른쪽 단추로 클릭하고 차트 편집을 선택합니다. 그런 다음 열리는 새 블레이드의 드롭다운에서 추가 메트릭을 선택하여 성능 데이터를 볼 시간 범위를 지정할 수 있습니다.

## AD FS에 대한 보고서
Azure AD Connect Health는 AD FS의 활동 및 성능에 대한 보고서를 제공합니다. 이러한 보고서는 관리자가 AD FS 서버의 활동을 파악하는 데 도움이 됩니다.

### 사용자 이름/암호 로그인이 실패한 상위 사용자 50명

AD FS 서버에서 인증 요청이 실패하는 일반적인 이유 중 하나는 잘못된 자격 증명, 다시 말해서 사용자 이름 또는 암호가 올바르지 않은 것입니다. 이는 일반적으로 암호가 복잡하거나, 암호를 잊어버리거나, 오타를 입력하여 발생합니다.

하지만 AD FS 서버에서 이러한 요청을 예상보다 훨씬 많이 처리하게 되는 또 다른 이유가 있습니다. 여기에는 만료된 사용자 자격 증명을 캐시하는 응용 프로그램 또는 잘 알려진 암호를 사용하여 사용자의 계정에 로그인하려고 시도하는 악의적인 사용자가 포함됩니다.

Azure AD Connect Health for ADFS는 사용자 이름 또는 암호가 잘못되어 로그인 시도가 실패한 상위 사용자 50명에 대한 보고서를 제공합니다. 보고서는 팜의 모든 AD FS 서버에서 생성한 감사 이벤트를 처리하여 작성됩니다.

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report1a.png)

이 보고서 내에서 다음 정보에 간편하게 액세스할 수 있습니다.

- 지난 30일 동안 잘못된 사용자 이름/암호를 사용하여 실패한 요청 수
- 잘못된 사용자 이름/암호로 로그인하여 실패한 일별 평균 사용자 수

이 파트를 클릭하면 추가 세부 정보를 제공하는 주 보고서 블레이드로 이동합니다. 여기에는 잘못된 사용자 이름 또는 암호를 사용한 요청의 기준을 설정할 수 있는 추세 정보와 실패한 요청 수가 포함된 상위 사용자 50명의 목록을 제공하는 그래프가 포함됩니다.

이 그래프는 다음 정보를 제공합니다.

- 잘못된 사용자 이름/암호로 인해 실패한 일별 총 로그인 수
- 로그인에 실패한 일별 총 고유 사용자 수

![Azure AD Connect Health 포털](./media/active-directory-aadconnect-health-adfs/report2a.png)

보고서는 다음 정보를 제공합니다.

| 보고서 항목 | 설명
| ------ | -------- |
|사용자 ID| 사용된 사용자 ID를 표시합니다. 값은 사용자가 입력한 것이며 경우에 따라 잘못된 사용자 ID가 사용되는 것을 볼 수 있습니다.|
|실패한 시도|특정 사용자 ID에 대한 총 실패 횟수를 보여 줍니다. 테이블은 가장 높은 실패 횟수부터 내림차순으로 정렬됩니다.|
|마지막 실패|마지막 실패가 발생한 타임 스탬프를 보여 줍니다.



>[AZURE.NOTE] 이 보고서는 해당 시간 내에 수집된 새 정보를 바탕으로 2시간마다 자동으로 업데이트됩니다. 따라서 마지막 2시간 내에 발생하는 로그인 시도가 보고서에 포함되지 않을 수 있습니다.



## 관련 링크

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health Agent 설치](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 작업](active-directory-aadconnect-health-operations.md)
* [동기화에 대한 Azure AD Connect Health 사용](active-directory-aadconnect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 버전 내역](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0928_2016-->