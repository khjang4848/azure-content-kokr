<properties
 pageTitle="IoT Hub HA 및 DR | Microsoft Azure"
 description="재해 복구 기능을 사용하여 고가용성 IoT 솔루션을 빌드할 수 있도록 지원하는 기능을 설명합니다."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# IoT Hub 고가용성 및 재해 복구

Azure 서비스로 IoT Hub는 Azure 지역 수준에서 중복을 사용하여 솔루션의 추가 작업 없이도 HA(고가용성)를 제공합니다. 또한 Azure는 필요한 경우 DR(재해 복구) 기능 또는 지역 간 가용성을 사용하여 솔루션을 구축할 수 있는 다양한 기능을 제공합니다. 장치 또는 사용자에게 전역, 지역 간 고가용성을 제공하기 위해서는 이러한 DR 기능을 활용하는 솔루션을 설계 및 준비해야 합니다. [Azure 비즈니스 연속성 기술 지침](../resiliency/resiliency-technical-guidance.md) 문서에서는 비즈니스 연속성 및 DR에 대한 Azure의 기본 제공 기능을 설명합니다. [Azure 응용 프로그램에 대한 재해 복구 및 고가용성][] 문서는 Azure 응용 프로그램에서 HA 및 DR 달성하기 위한 전략에 대한 아키텍처 지침을 제공합니다.

## Azure IoT Hub DR
역내 HA 외에도 IoT Hub는 사용자 개입이 필요 없는 재해 복구용 장애 조치(Failover) 메커니즘을 구현합니다. IoT Hub DR는 자체적으로 시작되고 2-26시간의 복구 시간 목표(RTO)과 다음 복구 지점 목표(RPO)를 가집니다.

| 기능 | RPO |
| ------------- | --- |
| 레지스트리 및 통신 작업에 대 한 서비스 가용성 | CName 손실 가능성 |
| 장치 ID 레지스트리의 ID 데이터 | 0-5분 데이터 손실 |
| 장치-클라우드 메시지 | 읽지 않은 메시지가 모두 손실됨 |
| 작업 모니터링 메시지 | 읽지 않은 메시지가 모두 손실됨 |
| 클라우드-장치 메시지 | 0-5분 데이터 손실 |
| 클라우드-장치 피드백 큐 | 읽지 않은 메시지가 모두 손실됨 |

## IoT Hub를 통한 국가별 장애 조치

IoT 솔루션에서 배포 토폴로지에 대한 전체 내용은 이 문서의 범위를 벗어나지만 고가용성 및 재해 복구 목적으로 *국가별 장애 조치* 배포 모델을 고려해보겠습니다.

국가별 장애 조치 모델에서 솔루션 백 엔드는 주로 한 데이터 센터 위치에서 실행되지만 추가 IoT Hub 및 백 엔드는, 기본 데이터 센터의 IoT Hub가 가동 중단되거나 장치에서 기본 데이터 센터로 네트워크 연결이 어떠한 이유로 중단되는 경우 장애 조치 용도로 다른 데이터 센터 위치로 배포됩니다. 장치는 기본 게이트웨이에 연결할 수 없는 경우 항상 보조 서비스 끝점을 사용합니다. 지역 간 장애 조치 기능을 통해 솔루션 가용성을 단일 지역의 고가용성 이상으로 향상시킬 수 있습니다.

높은 수준에서 IoT Hub로 국가별 장애 조치를 구현하려면 다음이 필요합니다.

* **보조 IoT Hub 및 장치 라우팅 논리** - 주 지역에서 서비스 중단이 발생하는 경우 장치에서 보조 지역으로 연결을 시작해야 합니다. 관련된 대부분의 서비스가 상태를 인식하는 특성이 있으므로 일반적으로 솔루션 관리자는 국가 간 장애 조치 프로세스를 트리거합니다. 프로세스의 제어를 유지하면서 새 끝점에서 장치로 통신하는 가장 좋은 방법은 현재 활성 끝점에 대해 *안내자* 서비스를 정기적으로 확인하는 것입니다. 안내자 서비스는 DNS-리디렉션 기술을 사용하여 복제되고 연결을 유지할 수 있는 간단한 웹 응용 프로그램입니다(예: [Azure 트래픽 관리자][] 사용).
* **ID 레지스트리 복제** - 사용하려면 보조 IoT Hub가 솔루션에 연결할 수 있는 데 필요한 모든 장치 ID를 포함해야 합니다. 솔루션은 지역에서 복제된 장치 ID의 백업을 유지하고 이를 보조 IoT Hub로 업로드한 후 장치에 대한 활성 끝점을 전환해야 합니다. 이 경우 IoT Hub의 장치 ID 내보내기 기능은 매우 유용합니다. 자세한 내용은 [IoT Hub 개발자 가이드 - ID 레지스트리][]를 참조하세요.
* **논리 병합** - 주 지역을 다시 사용할 수 있게 된 경우 보조 사이트에 생성된 모든 상태 및 데이터를 주 지역으로 다시 마이그레이션해야 합니다. 이것은 주로 장치 ID 및 응용 프로그램 메타데이터와 관련되며 기본 IoT Hub 및 주 지역의 기타 가능한 응용 프로그램별 저장소에 병합되어야 합니다. 이 단계를 간소화하기 위해 일반적으로 멱등원 작업을 사용하는 것이 좋습니다. 그러면 이벤트의 최종적인 일관된 배포뿐만 아니라 이벤트의 중복 또는 순서 비지정 배달로 인한 부작용이 최소화됩니다. 또한 응용 프로그램 논리는 잠재적인 불일치 또는 "약간"의 날짜 중단 상태를 허용할 수 있도록 설계되어야 합니다. 복구 지점 목표(RPO)에 기반한 "치료"에 시스템은 추가 시간이 걸리기 때문입니다.

## 다음 단계

Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [IoT Hub Azure란?][]

[Azure resiliency technical guidance]: ../resiliency/resiliency-technical-guidance.md
[Azure 응용 프로그램에 대한 재해 복구 및 고가용성]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Failsafe: Guidance for Resilient Cloud Architectures]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Azure 트래픽 관리자]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub 개발자 가이드 - ID 레지스트리]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Azure란?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0921_2016-->