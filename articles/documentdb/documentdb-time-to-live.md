<properties
   pageTitle="TTL(Time to live)을 사용하여 DocumentDB에서 데이터 만료 | Microsoft Azure"
   description="TTL을 사용하여 Microsoft Azure DocumentDB는 일정 기간 후에 시스템에서 문서를 자동으로 삭제하는 기능을 제공합니다."
   services="documentdb"
   documentationCenter=""
   keywords="TTL(Time to live)"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# TTL(Time to live)을 사용하여 자동으로 DocumentDB 컬렉션의 데이터 만료

응용 프로그램은 방대한 양의 데이터을 생성하고 저장할 수 있습니다. 컴퓨터에서 생성한 이벤트 데이터, 로그 및 사용자 세션 정보와 같은 이 데이터 중 일부는 한정된 기간에만 사용할 수 있습니다. 데이터가 응용 프로그램의 요구를 넘게 되면 이 데이터를 삭제하고 응용 프로그램의 저장소 요구를 줄이는 것이 안전합니다.

“Time to live” 또는 TTL을 사용하여 Microsoft Azure DocumentDB는 일정 기간 후에 데이터베이스에서 문서를 자동으로 삭제하는 기능을 제공합니다. 기본 TTL(Time to live)을 컬렉션 수준에서 설정할 수 있고 문서별로 재정의할 수 있습니다. TTL을 컬렉션 기본값으로 설정하거나 문서 수준으로 설정하면 DocumentDB는 마지막으로 수정된 이후 해당 기간(초) 후에 존재하는 문서를 자동으로 제거합니다.

문서를 마지막으로 수정한 경우 DocumentDB의 TTL(Time to live)에서는 오프셋을 사용합니다. 이렇게 하려면 모든 문서에 있는 \_ts 필드를 사용합니다. \_ts 필드는 날짜 및 시간을 나타내는 Unix 스타일 Epoch 타임스탬프입니다. \_ts 필드는 문서가 수정될 때마다 업데이트됩니다.

## TTL 동작

TTL 기능은 컬렉션 수준 및 문서 수준 등 두 가지 수준으로 TTL 속성에 의해 제어됩니다. 값은 초 단위로 설정되고 문서가 마지막으로 수정되는 \_ts 필드에서 델타로 처리됩니다.

 1.  컬렉션에 대한 DefaultTTL
  * 누락(또는 null로 설정)된 경우 문서는 자동으로 삭제되지 않습니다.
  
  * 표시되고 현재 값이 "-1" = 무한인 경우 문서는 기본적으로 만료되지 않습니다.
  
  * 표시되고 현재 값이 숫자("n")인 경우 문서는 마지막으로 수정되고 "n"초 후에 만료됩니다.

 2.  문서에 대한 TTL:
  * 속성은 DefaultTTL이 상위 컬렉션에 있는 경우에 적용할 수 있습니다.
  
  * 상위 컬렉션에 대한 DefaultTTL 값을 재정의합니다.

문서가 만료되는 즉시(ttl + \_ts >= 현재 서버 시간) 문서는 "만료"된 것으로 표시됩니다. 이 시간 이후에 어떤 작업도 이러한 문서에 허용되지 않으며 수행되는 쿼리 결과에서 제외됩니다. 문서는 시스템에서 물리적으로 삭제되고 나중에 선택적으로 백그라운드에서 삭제됩니다. 이는 컬렉션 예산에서 [RU(요청 단위)](documentdb-request-units.md)를 사용하지 않습니다.

위의 논리는 다음 행렬에 표시될 수 있습니다.

| | 컬렉션에서 DefaultTTL 누락/설정되지 않음 | 컬렉션에서 DefaultTTL = -1 | 컬렉션에서 DefaultTTL = "n"|
| ------------- |:-------------|:-------------|:-------------|
| 문서에서 TTL 누락| 문서와 컬렉션에 TTL의 개념이 없으므로 문서 수준에서 아무 것도 재정의할 수 없습니다. | 이 컬렉션에서 만료되는 문서가 없습니다. | 간격 n이 경과하면 이 컬렉션에서 문서가 만료됩니다. |
| 문서에서 TTL = -1 | 문서가 재정의할 수 있는 DefaultTTL 속성을 수집이 정의하지 않으므로 문서 수준에서 아무 것도 재정의되지 않습니다. 문서에서 TTL은 시스템에 의해 해석되지 않습니다. | 이 컬렉션에서 만료되는 문서가 없습니다. | 이 컬렉션에서 TTL = -1인 문서는 만료되지 않습니다. 다른 모든 문서는 "n" 간격 후에 만료됩니다. |
| 문서에서 TTL = n | 문서 수준에서 아무 것도 재정의하지 않습니다. 문서에서 TTL은 시스템에 의해 해석되지 않습니다. | TTL = n인 문서는 간격 n(초) 후에 만료됩니다. 다른 문서는 간격 -1을 상속하며 만료되지 않습니다. | TTL = n인 문서는 간격 n(초) 후에 만료됩니다. 다른 문서는 컬렉션에서 "n" 간격을 상속합니다. |


## TTL 구성

기본적으로 TTL(Time to live)은 모든 DocumentDB 컬렉션 및 문서에서 기본적으로 비활성화됩니다.

## TTL 사용

컬렉션 또는 컬렉션 내 문서에서 TTL을 사용하려면 컬렉션의 DefaultTTL 속성을 -1 또는 0이 아닌 양수로 설정해야 합니다. DefaultTTL을 -1로 설정하면 기본적으로 컬렉션에서 모든 문서가 계속 존재하게 되지만 DocumentDB 서비스는 이 기본값을 재정의한 문서에 대해 이 컬렉션을 모니터링해야 합니다.

## 컬렉션에서 기본 TTL 구성

기본 수명을 컬렉션 수준에서 구성할 수 있습니다.

컬렉션에서 TTL을 설정하려면 문서(\_ts)의 타임스탬프를 마지막으로 수정한 이후 컬렉션에 있는 모든 문서를 만료할 기간(초)을 나타내는 0이 아닌 양수 값을 제공해야 합니다.

또는 기본값을 -1로 설정할 수 있습니다. 즉, 컬렉션에 삽입된 모든 문서가 기본적으로 영원히 지속됩니다.

## 문서에서 TTL 설정

컬렉션에서 기본 TTL을 설정하는 것 외에도 특정 문서 수준에서 TTL을 설정할 수 있습니다. 이렇게 하면 컬렉션의 기본값을 재정의합니다.

문서에서 TTL을 설정하려면 문서(\_ts)의 타임스탬프를 마지막으로 수정한 이후 문서를 만료할 기간(초)을 나타내는 0이 아닌 양수 값을 제공해야 합니다.

이 만료 오프셋을 설정하려면 문서에서 TTL 필드를 설정합니다.

문서에 TTL 필드가 없는 경우 컬렉션의 기본값이 적용됩니다.

컬렉션 수준에서 TTL을 사용하지 않으면 문서에서 TTL 필드는 컬렉션에 다시 TTL을 사용할 때까지 무시됩니다.


## 기존 문서에서 TTL 확장

문서에 쓰기 작업을 수행하여 TTL을 재설정할 수 있습니다. 이렇게 하여 \_ts를 현재 시간으로 설정하면 문서에 대한 만료 카운트다운이 TTL에서 설정한 대로 다시 시작됩니다.

문서의 TTL을 변경하려는 경우 설정할 수 있는 다른 필드에서처럼 필드를 업데이트할 수 있습니다.


## 문서에서 TTL 제거

문서에서 TTL을 설정했지만 해당 문서가 더 이상 만료되지 않게 하려면 문서를 검색하고 TTL 필드를 제거하여 서버에 있는 문서를 바꿀 수 있습니다.

문서에서 TTL 필드를 제거하면 컬렉션의 기본값이 적용됩니다.

문서가 만료되지 않도록 중지하고 컬렉션에서 상속하지 않으려면 TTL 값을 -1로 설정해야 합니다.


## TTL 사용 안 함

컬렉션에서 TTL을 사용하지 않고 만료된 문서를 찾는 백그라운드 프로세스를 중지하려면 컬렉션에서 DefaultTTL 속성을 삭제해야 합니다.

이 속성을 삭제하는 것은 -1로 설정하는 것과 다릅니다. -1로 설정하면 컬렉션에 추가된 새 문서는 계속 존재하게 되지만 컬렉션에 있는 특정 문서에서 이를 재정의할 수 있습니다.

컬렉션에서 이 속성을 완전히 제거하면 이전의 기본값을 명시적으로 재정의한 문서가 있더라도 문서가 만료되지 않습니다.


## FAQ

**TTL의 비용은 얼마인가요?**

문서에 TTL을 설정하는 추가 비용은 없습니다.

**TTL이 실행되면 문서를 삭제하는 데 얼마나 걸리나요?**

문서가 만료되는 즉시(ttl + \_ts >= 현재 서버 시간) 문서는 사용할 수 없다고 표시됩니다. 이 시간 이후에 어떤 작업도 이러한 문서에 허용되지 않으며 수행되는 쿼리 결과에서 제외됩니다. 문서는 백그라운드에서 시스템에 의해 물리적으로 삭제됩니다. 컬렉션의 예산에서 RU를 사용하지 않습니다.

**문서를 삭제하는 시간이 걸리면 삭제 될 때까지 할당량(및 청구서)으로 계산되나요?**

아니요, 문서가 만료되면 이 문서의 저장소는 청구되지 않으며 문서 크기는 컬렉션에 대한 저장소 할당량에 포함되지 않습니다.

**문서에서 TTL은 RU 요금에 영향을 주나요?**

아니요, DocumentDB 내의 모든 문서에서 수행된 작업에 대한 RU 요금에 영향을 주지 않습니다.

**문서를 삭제하면 내 컬렉션에서 프로비전한 처리량에 영향을 주나요?**

아니요, 컬렉션에 대한 요청을 처리하는 작업은 문서를 삭제하는 백그라운드 프로세스를 실행하는 것보다 우선적으로 처리됩니다. 모든 문서에 TTL을 추가하는 작업이 영향을 주지 않습니다.

**문서가 만료되면 삭제될 때까지 내 컬렉션에 얼마 동안 유지되나요?**

기간이 만료되는 즉시 더 이상 액세스할 수 없습니다. 문서가 실제로 삭제되기 전에 컬렉션에 유지되는 정확한 시간은 결정적이지 않으며 백그라운드 프로세스가 문서를 삭제할 때에 따라 달라 집니다.

**만료된 문서가 모든 노드에 걸쳐 삭제되거나 “결과적으로 일관성이 있나요?”**

문서는 동시에 모든 노드 및 모든 지역에서 사용할 수 없게 됩니다.

**TTL 모니터링 백그라운드 작업에 대한 RU 비용이 있나요?**

아니요, 이에 대한 RU 비용이 들지 않습니다.

**TTL 만료는 얼마나 자주 확인되나요?**

TTL 만료 검사는 백그라운드 프로세스로 발생하지 않습니다. 요청에 응답하는 경우 백 엔드 서비스에서는 온라인으로 검사를 수행하고 만료된 모든 문서를 제외합니다. 실제 문서를 삭제하는 작업은 백그라운드에서 비동기적으로 실행되는 유일한 프로세스입니다. 이 프로세스의 빈도는 컬렉션에 사용할 수 있는 RU에 의해 결정됩니다.

**TTL 기능이 전체 문서에 적용되나요, 또는 개별 문서 속성 값을 만료할 수 있나요?**

TTL은 문서 전체에 적용됩니다. 문서의 일부만 만료하고 싶다면 "링크된" 별도 문서의 기본 문서에서 부분을 추출한 다음 추출된 문서에서 TTL을 사용하는 것이 좋습니다.

**TTL 기능에는 특정 인덱싱 요구 사항이 있나요?**

예. 컬렉션에는 지연 또는 일관성 중 하나에 대한 [인덱싱 정책 집합](documentdb-indexing-policies.md)이 있어야 합니다. 인덱싱 설정을 사용하여 컬렉션에서 DefaultTTL을 없음으로 설정하면 DefaultTTL이 이미 설정되어 있는 컬렉션에서 인덱싱을 해제하는 것과 같은 오류가 발생합니다.


## 다음 단계

Azure DocumentDB에 대해 자세히 알아보려면 서비스 [*설명서*](https://azure.microsoft.com/documentation/services/documentdb/) 페이지를 참조하세요.

<!---HONumber=AcomDC_0720_2016-->