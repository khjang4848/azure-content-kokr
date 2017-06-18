<properties 
   pageTitle="StorSimple 장치 모니터링 | Microsoft Azure"
   description="StorSimple 관리자 서비스를 사용하여 I/O 성능, 용량 사용률, 네트워크 처리량 및 장치 성능을 모니터링하는 방법에 대해 설명합니다."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# StorSimple 관리자 서비스를 사용하여 StorSimple 장치를 모니터링합니다. 

## 개요

StorSimple 관리자 서비스를 사용하여 StorSimple 솔루션 내에서 특정 장치를 모니터링할 수 있습니다. I/O 성능, 용량 사용률, 네트워크 처리량 및 장치 성능 메트릭을 기준으로 사용자 지정 차트를 만들 수 있습니다.

특정 장치에 대한 모니터링 정보를 확인하려면, Azure 클래식 포털에서 StorSimple Manager 서비스를 선택합니다. **모니터** 탭을 클릭한 다음, 장치 목록에서 선택합니다. **모니터** 페이지에는 다음 정보가 포함됩니다.

## I/O 성능 

**I/O 성능**은 호스트 서버의 iSCSI 시작자 인터페이스와 장치 또는 장치와 클라우드 사이의 읽기 및 쓰기 작업의 수와 관련된 메트릭을 추적합니다. 이 성능은 특정 볼륨, 특정 볼륨 컨테이너 또는 모든 볼륨 컨테이너에 대해 측정될 수 있습니다.

아래 차트에서는 프로덕션 장치의 모든 볼륨에 대한 장치 개시자의 I/O를 보여 줍니다. 그려진 메트릭은 초당 읽기 및 쓰기 바이트 수, 초당 읽기 및 쓰기 IO 작업, 읽기 및 쓰기 대기 시간입니다.

![초기자에서 장치로의 IO 성능](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

동일한 장치에 대한 I/O 작업은 장치에서 모든 볼륨 컨테이너에 대한 클라우드로의 데이터에 대해 그려집니다. 이 장치에서 데이터는 선형 계층에만 있으며 클라우드로는 아무것도 넘어가지 않습니다. 장치에서 클라우드로 가는 읽기/쓰기 작업은 없습니다. 따라서 장치와 서비스 사이에서 하트비트를 검사하는 빈도인 5분 간격으로 차트에 최대치가 표시됩니다.

![장치에서 클라우드로의 IO 성능](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


동일한 장치에 대해 오후 2시부터 볼륨 데이터에 대한 클라우드 스냅숏이 생성됩니다. 그 결과 장치에서 클라우드로 데이터 흐름이 발생합니다. 읽기/쓰기는 이 기간 동안 클라우드에 제공됩니다. IO 차트에는 스냅숏이 생성된 시간에 해당하는 다양한 메트릭의 최대값이 표시됩니다.

![클라우드 스냅숏 후 장치에서 클라우드로의 IO 성능](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## 용량 사용률 

**용량 사용률**은 볼륨, 볼륨 컨테이너 또는 장치에서 사용되는 데이터 저장소 공간의 양과 관련된 메트릭을 추적합니다. 기본 저장소, 클라우드 저장소 또는 장치 저장소 용량 사용률을 기반으로 보고서를 만들 수 있습니다. 용량 사용률은 특정 볼륨, 특정 볼륨 컨테이너 또는 모든 볼륨 컨테이너에 대해 측정될 수 있습니다.


기본, 클라우드 및 장치 저장소 용량은 다음과 같이 설명할 수 있습니다.

###기본 저장소 용량 사용률
 
이 차트는 데이터가 중복 제거 및 압축되기 전에 StorSimple 볼륨에 기록된 데이터의 양을 나타냅니다. 모든 볼륨 또는 단일 볼륨에 대한 기본 저장소 사용률을 볼 수 있습니다.

두 경우 모두에서 각 개별 볼륨 대비 모든 볼륨의 기본 저장소 볼륨 용량 사용률 차트를 보고 기본 데이터를 합산할 때 두 숫자가 일치하지 않을 수 있습니다. 모든 볼륨의 전체 주 데이터는 개별 볼륨의 기본 데이터 총 합계에 합산되지 않을 수 있습니다. 이것은 다음 중 하나 때문일 수 있습니다.

- **모든 볼륨에 대해 포함된 스냅숏 데이터**: 이 동작은 업데이트 3 이전 버전을 실행하는 경우에만 나타납니다. 모든 볼륨에 대해 표시되는 기본 데이터는 각 볼륨과 스냅숏 데이터에 대한 기본 데이터의 합계입니다. 지정된 볼륨에 대해 표시되는 기본 데이터는 해당 볼륨에 할당된 데이터 크기에만 해당합니다(해당 볼륨 스냅숏 데이터를 포함하지 않음).

	이 상황은 다음 수식으로도 설명할 수 있습니다.

	*주 데이터(모든 볼륨) = (주 데이터(볼륨 i) + 스냅숏 데이터 크기(볼륨 i) )의 합*
	
	*여기서 (Primary data (volume i)는 볼륨 i에 할당된 기본 데이터의 크기입니다.*
 
	스냅숏이 서비스를 통해 삭제될 경우 삭제는 백그라운드에서 비동기로 수행됩니다. 스냅숏 삭제에 따라 볼륨 데이터 크기가 업데이트되는 데는 다소 시간이 걸릴 수 있습니다.

    업데이트 3 이상을 실행하는 경우 스냅숏 데이터는 볼륨 데이터에 포함되지 않습니다. 기본 사용률은 다음과 같이 계산됩니다.

    *Primary data (All volumes) = Sum of (Primary data (volume i)
    
    *여기서 (Primary data (volume i)는 볼륨 i에 할당된 기본 데이터의 크기입니다.*
 
- **모든 볼륨에 모니터링을 사용하지 않는 볼륨이 포함되어 있음**: 모니터링을 사용하지 않는 볼륨이 장치에 있는 경우 해당 개별 볼륨의 모니터링 데이터는 차트에 제공되지 않습니다. 그러나 차트의 모든 볼륨에는 모니터링을 사용하지 않는 볼륨이 포함됩니다.
 
- **모든 볼륨에 대해 라이브 연결 백업이 있는 볼륨이 삭제됨**: 스냅숏 데이터를 포함하는 보륨이 삭제되었으나 연결된 스냅숏이 여전히 존재할 경우 불일치가 발생할 수 있습니다.

- **모든 볼륨에 대해 포함된 볼륨이 삭제됨**: 일부 경우 구 볼륨이 삭제 후에도 존재할 수 있습니다. 삭제의 효과나 나타나지 않으며 장치가 더 낮은 가용 용량을 표시할 수 있습니다. 이러한 볼륨을 제거하려면 Microsoft 지원에 문의해야 합니다.

다음 차트에서는 클라우드 스냅숏을 생성하기 전과 생성한 후의 StorSimple 장치 기본 저장소 용량 사용률을 보여 줍니다. 볼륨 데이터만 표시되기 때문에 클라우드 스냅숏으로 인해 기본 저장소가 변경되어서는 안 됩니다. 볼 수 있듯이 차트에서 클라우드 스냅숏을 생성한 후의 기본 용량 사용률에는 아무런 차이가 없습니다. 해당 장치에서 클라우드 스냅숏은 오후 2시 근처에 시작되었습니다.

![클라우드 스냅숏 전 기본 용량 사용률](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![클라우드 스냅숏 후 기본 용량 사용률](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

업데이트 2 이상 실행하는 경우 아래 나와 있는 것처럼 개별 볼륨, 모든 볼륨, 모든 계층화된 볼륨 및 모든 로컬 고정 볼륨으로 기본 저장소 용량 사용률을 구분할 수 있습니다. 모든 로컬 고정 볼륨별로 분석하면 로컬 계층이 얼마나 사용되는지 빠르게 확인할 수 있습니다.

![모든 로컬 고정 볼륨에 대한 기본 용량 사용률](./media/storsimple-monitor-device/localvolumes.png)


###클라우드 저장소 용량 사용률

이 차트는 사용한 클라우드 저장소의 크기를 표시합니다. 이 데이터는 중복 제거되고 압축됩니다. 이 양에는 모든 기본 볼륨에 반영되지 않으며 레거시 또는 필수 보존 목적을 위해 유지되는 데이터를 포함할 수 있는 클라우드 스냅숏이 포함되어 있습니다. 기본 및 클라우드 저장소 소비 수치를 비교하여 정확한 숫자는 아니지만 데이터 축소 비율을 파악할 수 있습니다. 다음 차트에서는 클라우드 스냅숏을 생성하기 전과 후의 StorSimple 장치 클라우드 저장소 용량 사용률을 보여 줍니다. 클라우드 스냅숏은 해당 장치에서 약 오후 2시에 시작되었으며 동시에 클라우드 용량 사용률도 5.73MB에서 4.04GB로 급증한 것을 알 수 있습니다.

![클라우드 스냅숏 전 클라우드 용량 사용률](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![클라우드 스냅숏 후 클라우드 용량 사용률](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###장치 저장소 용량 사용률

SSD 선형 계층을 포함하므로 기본 저장소 사용률 이상인 장치에 대한 총 사용률을 보여 줍니다. 이 계층에는 장치의 다른 계층에 있는 데이터의 양이 포함되어 있습니다. SSD 선형 계층의 용량은 순환되어 새로운 데이터가 들어오면 이전 데이터가 HDD 계층(이때 중복 제거되고 압축됨) 및 이후에 클라우드로 이동됩니다.

시간이 지남에 따라 기본 용량 사용률 및 장치 용량 사용률은 데이터가 클라우드로 티어링되기 시작할 때까지 함께 증가할 가능성이 높습니다. 이때 장치 용량 사용률이 안정 상태를 유지하기 시작하지만 기본 용량 사용률은 더 많은 데이터가 기록될수록 증가합니다.

다음 차트에서는 클라우드 스냅숏을 생성하기 전과 생성한 후의 StorSimple 장치 기본 저장소 용량 사용률을 보여 줍니다. 클라우드 스냅숏은 오후 2시에 시작했고, 그와 동시에 장치 용량 사용률이 줄기 시작했습니다. 장치 저장소 용량 사용률이 11.58GB에서 7.48GB로 낮아졌습니다. 이는 선영 SSD 계층의 압축되지 않은 데이터가 중복 제거, 압축되어 HDD 계층으로 이동한 것을 나타낼 가능성이 큽니다. 장치의 SSD와 HDD 계층에 이미 많은 양의 데이터가 있으면 이러한 감소가 보이지 않을 수도 있습니다. 이 예에서는 장치에는 적은 양의 데이터가 있습니다.

![클라우드 스냅숏 전 장치 용량 사용률](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![클라우드 스냅숏 후 장치 용량 사용률](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## 네트워크 처리량

**네트워크 처리량**은 호스트 서버 및 장치의 iSCSI 초기자 네트워크 인터페이스와 장치와 클라우드 사이에서 전송되는 데이터의 양과 관련된 메트릭을 추적합니다. 장치의 각 iSCSI 네트워크 인터페이스에 대한 해당 메트릭을 모니터링할 수 있습니다.

다음 차트에서는 모두 장치의 1GbE 네트워크 인터페이스인 데이터 0과 데이터 4에 대한 네트워크 처리량을 보여 줍니다. 이 경우 데이터 0은 클라우드를 사용하고 데이터 4는 iSCSI를 사용합니다. StorSimple 장치에 대한 인바운드 및 아웃바운드 트래픽을 모두 볼 수 있습니다. 차트에서 오후 3시 24분에 시작하는 평평한 선은 데이터를 5분마다 수집한다는 사실 때문에 나타나며, 따라서 무시해야 합니다.

![Data4에 대한 네트워크 처리량](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Data4에 대한 네트워크 처리량](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## 장치 성능 

**장치 성능**은 장치의 성능과 관련된 메트릭을 추적합니다. 다음 차트에서는 프로덕션 환경에서 장치의 CPU 사용률 통계를 보여 줍니다.

![장치의 CPU 사용률](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## 다음 단계

- [StorSimple Manager 서비스 장치 대시보드를 사용](storsimple-device-dashboard.md)하는 방법을 알아봅니다.

- [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0914_2016-->