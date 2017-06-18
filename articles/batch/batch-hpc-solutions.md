<properties
   pageTitle="클라우드의 배치 및 HPC 솔루션 | Microsoft Azure"
   description="Azure의 배치 및 고성능 컴퓨팅(HPC 및 Big Compute) 시나리오와 솔루션 옵션에 대해 알아보기"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/27/2016"
   ms.author="danlep"/>

# Azure 클라우드의 배치 및 HPC 솔루션

Azure는 효율적이고 확장성 있는 *Big Compute*로도 불리는 배치 및 HPC(고성능 컴퓨팅)용 클라우드 솔루션을 소개합니다. 큰 컴퓨팅 워크로드 및 이를 지원하기 위한 Azure 서비스를 알아보거나 이 문서의 뒷부분에 나오는 [솔루션 시나리오](#scenarios)로 직접 이동합니다. 이 문서는 주로 기술 의사 결정자, IT 관리자 및 독립 소프트웨어 공급 업체용 이지만 다른 IT 전문가 및 개발자도 이 솔루션들을 익히기 위해 사용할 수 있습니다.

조직에는 엔지니어링 디자인 및 분석, 이미지 렌더링, 복잡한 모델링, Monte Carlo 시뮬레이션, 재무 위험 계산 및 기타 대규모 컴퓨팅 문제가 있습니다. Azure는 조직에서 필요한 리소스, 규모 및 일정에 발생한 문제를 해결하도록 돕습니다. Azure를 활용하여 조직에서는 다음을 수행할 수 있습니다.

* 하이브리드 솔루션을 만들어서 온-프레미스 HPC 클러스터를 클라우드에 최대 작업 부하를 오프로드 하도록 확장합니다.

* Azure에서 전체 HPC 클러스터 도구 및 워크로드 실행

* [배치](https://azure.microsoft.com/documentation/services/batch/)와 같은 관리되고 확장성 있는 Azure 서비스를 사용하여 계산 인프라를 배포하고 관리할 필요없이 계산 집약적인 워크로드를 실행합니다.

또한 이 문서에서 다루지 않지만 Azure는 개발자와 파트너에게 다양한 기능, 아키텍처 선택 및 개발 도구를 제공하여 대규모의 사용자 지정 빅 컴퓨팅 워크플로를 구축합니다. 그리고 파트너 에코시스템의 증가로 Azure 클라우드에서 빅 컴퓨팅 워크로드의 생산성이 향상될 수 있습니다.


## 배치 및 HPC 응용 프로그램

웹 응용 프로그램 및 많은 기간 업무 응용 프로그램과 달리 배치 및 HPC 응용 프로그램에는 정의 된 시작과 끝이 있으며 때때로 몇 시간 이상 일정이 또는 요청에 따라 실행할 수 있습니다. 대부분이 두 가지 주요 범주인 *기본적으로 병렬*(해결할 문제가 여러 컴퓨터나 프로세서에서 병렬로 실행되므로 때로는 "병렬 처리가 적합"이라고도 함) 및 *밀접하게 결합*으로 나누어집니다. 이러한 응용 프로그램 형식에 대한 자세한 내용은 다음 표를 참조합니다. 일부 Azure 솔루션 방식은 한 형식 또는 다른 형식에 대해 더 잘 작동합니다.

>[AZURE.NOTE] 배치 및 HPC 솔루션에서 응용 프로그램의 실행 중인 인스턴스는 일반적으로 *작업*이라고 하며 각 작업은 *태스크*로 나눌 수 있습니다. 그리고 이 응용 프로그램에 대한 클러스터형 계산 리소스는 종종 *계산 노드*라고 합니다.

유형 | 특성 | 예
------------- | ----------- | ---------------
**본질적 병렬**<br/><br/>![본질적 병렬][parallel] |• 개별 컴퓨터는 응용 프로그램 논리를 독립적으로 실행<br/><br/>• 컴퓨터를 추가하면 응용 프로그램의 크기를 조정하고 계산 시간을 줄일 수 있음<br/><br/>• 응용 프로그램은 별도 실행 파일로 구성되거나 클라이언트에서 호출하는 서비스의 그룹으로 구분됨(서비스 지향 아키텍처, 또는 SOA, 응용 프로그램) |• 재무 위험 모델링<br/><br/>• 이미지 렌더링 및 이미지 처리 <br/><br/>• 미디어 인코딩 및 코드 변환<br/><br/>• Monte Carlo 시뮬레이션<br/><br/>• 소프트웨어 테스트
**밀접하게 결합**<br/><br/>![밀접하게 결합][coupled] |• 응용 프로그램은 중간 결과를 조작하거나 교환하기 위해 계산 노드가 필요함<br/><br/>• 계산 노드는 병렬 컴퓨팅을 위한 일반적인 통신 프로토콜인 인터페이스인 MPI(Message Passing Interface)를 사용하여 통신할 수 있음<br/><br/>• 응용 프로그램은 네트워크 대기 시간 및 대역폭에 민감함<br/><br/>• 응용 프로그램의 성능은 InfiniBand 및 원격 직접 메모리 액세스(RDMA)와 같은 고속 네트워킹 기술을 사용하여 향상시킬 수 있음. |• 석유 및 가스 저장소 모델링<br/><br/>• 컴퓨팅 유체 역학 등 공학 설계 및 분석<br/><br/>•자동차 충돌 및 핵 반응과 같은 물리적 시뮬레이션<br/><br/>• 날씨 예측

### 클라우드에서 배치 및 HPC 응용 프로그램을 실행하기 위한 고려사항

Azure 또는 하이브리드(크로스-프레미스) 환경에대해 온-프레미스 HPC 클러스터에서 실행되도록 설계된 많은 응용 프로그램을 쉽게 마이그레이션할 수 있습니다. 그러나 다음 사항 등 일부 제한사항 또는 고려사항이 있을 수 있습니다.


* **클라우드 리소스의 가용성** - 사용한 클라우드 계산 리소스의 유형에 따라 작업을 실행하는 동안 지속적인 컴퓨터 가용성을 사용하지 못할 수 있습니다. 상태 처리 및 진행 확인 포인팅은 발생 가능한 일시적인 오류를 처리하는 일반적인 기술이며 클라우드 리소스를 사용하는 경우에 더 필요합니다.


* **데이터 액세스** - NFS와 같은 엔터프라이즈 클러스터에서 일반적으로 사용할 수 있는 데이터 액세스 기술은 클라우드에서 특별한 구성이 필요할 수 있습니다. 또는 클라우드에 대한 다른 데이터 액세스 방법 및 패턴을 채택해야 할 수 있습니다.

* **데이터 이동** - 많은 양의 데이터를 처리하는 응용 프로그램의 경우 클라우드 저장소로 데이터를 이동하고 리소스를 계산하기 위한 전략이 필요합니다. [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)와 같은 고속 크로스-프레미스 네트워킹이 필요할 수도 있습니다. 또한 해당 데이터를 저장하거나 액세스하기 위해 법, 규정 또는 정책 제한 사항 고려합니다.


* **라이선스** - 클라우드에서 실행하기 위해 라이선스나 기타 제한 사항에 대한 상용 응용 프로그램을 공급 업체에 확인합니다. 일부 공급 업체는 종량제 라이선스를 제공합니다. 솔루션에 대해 클라우드의 라이센스 서버 또는 온-프레미스 라이선스 서버에 대한 연결을 계획해야 할 수도 있습니다.


### 큰 계산 또는 빅 데이터?

큰 계산 및 빅 데이터 응용 프로그램 간에 구분하는 기준은 항상 명확하지는 않으며 일부 응용 프로그램에는 두 가지 모두의 특성 있을 수 있습니다. 두 가지 모두 컴퓨터의 클러스터에서 일반적으로 대규모 계산을 실행할 것을 포함합니다. 그러나 솔루션 접근 방법과 지원 도구는 다를 수 있습니다.

•**큰 계산**은 엔지니어링 시뮬레이션, 재무 위험 모델링, 디지털 렌더링 등 CPU 성능 및 메모리에 의존하는 응용 프로그램을 포함하는 경향이 있습니다. Big Compute 솔루션의 인프라에는 원시 계산을 수행하는 특수한 멀티 코어 프로세서가 있는 컴퓨터 및 컴퓨터를 연결하는 특수한 고속 네트워킹 하드웨어가 포함될 수 있습니다.

•**빅 데이터**는 단일 컴퓨터 또는 데이터베이스 관리 시스템에 의해 관리될 수 없는 다량의 데이터를 포함하는 데이터 분석 문제를 해결합니다. 예로는 대량의 웹 로그 또는 기타 비즈니스 인텔리전스 데이터가 있습니다. 빅 데이터는 CPU 전원보다 디스크 용량 및 I/O 성능에 더 의존하는 경향이 있습니다. 클러스터를 관리하고 데이터를 분할하는 Apache Hadoop과 같은 특수한 빅 데이터 도구도 있습니다. (Azure HDInsight 및 다른 Azure Hadoop 솔루션에 대한 정보는[Hadoop](https://azure.microsoft.com/solutions/hadoop/)을 참조하십시오.)

## 계산 관리 및 작업 예약

일반적으로 배치 및 HPC 응용 프로그램 실행에는 클러스터형 계산 리소스를 관리하고 작업을 실행하는 응용 프로그램에 할당하는 *클러스터 관리자* 및 *작업 스케줄러*가 포함됩니다. 이러한 함수는 별도 도구나 통합된 도구 또는 서비스로 수행할 수 있습니다.

* **클러스터 관리자** - 계산 리소스 (또는 계산 노드)를 프로비전하고 릴리스하고 관리합니다. 클러스터 관리자는 계산 노드의 운영 체제 이미지 및 응용 프로그램의 설치를 자동화하고, 요구 사항에 따라 계산 리소스의 크기를 조정하고 노드의 성능을 모니터링 할 수 있습니다.

* **작업 스케줄러** - 응용 프로그램이 요구하는 리소스(예: 프로세서 또는 메모리)와 실행될 조건을 지정합니다. 작업 스케줄러는 작업의 큐를 유지하고 할당된 우선순위나 기타 특성을 기준으로 리소스를 할당합니다.

Windows 기반 및 Linux 기반 클러스터용 클러스터링 및 작업 예약 도구는 Azure에 잘 마이그레이션할 수 있습니다. 예를 들어 [Microsoft HPC 팩](https://technet.microsoft.com/library/cc514029)인 Windows 및 Linux HPC 워크로드용 Microsoft의 무료 계산 클러스터 솔루션은 Azure에서 실행하기 위한 여러 옵션을 제공합니다. Linux 클러스터를 빌드하여 Torque 및 SLURM과 같은 오픈 소스 도구를 실행할 수도 있습니다. 또한 [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [IBM Platform Symphony](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592) 및 [Univa Grid Engine](http://www.univa.com/products/grid-engine)과 같은 상업적 그리드 솔루션을 Azure에 가져올 수도 있습니다.

다음 섹션에 나와 있는 것처럼 Azure 서비스를 활용하여 기존 클러스터 관리 도구 없이(또는 외에도) 계산 리소스 및 예약 작업을 관리할 수 있습니다.


## 시나리오

기존 HPC 클러스터 솔루션, Azure 서비스 또는 둘의 조합을 사용하여 Azure에서 Big Compute 워크로드를 실행하는 일반 시나리오는 다음과 같습니다. 각 시나리오를 선택하기 위한 주요 고려 사항이 나열되지만 전체 목록은 아닙니다. 솔루션에서 사용할 수 있는 사용 가능한 Azure 서비스에 대한 내용은 문서의 뒷부분에 있습니다.

 | 시나리오 | 선택한 이유는 무엇인가요?
------------- | ----------- | ---------------
**Azure에 HPC 클러스터 버스트**<br/><br/>[![클러스터 버스트][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> 자세한 정보:<br/>• [HPC 팩으로 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [HPC 팩으로 하이브리드 계산 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [HPC 팩을 사용하여 Azure 배치로 버스트](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/>|• 하이브리드 솔루션에서 [Microsoft HPC 팩](https://technet.microsoft.com/library/cc514029) 또는 기타 온-프레미스 클러스터를 추가적인 Azure 리소스와 결합.<br/><br/>• 빅 컴퓨팅 워크로드를 확장하여 PaaS(Platform as a Service) 가상 컴퓨터 인스턴스(현재 Windows Server만 해당)에서 실행.<br/><br/>• 선택적 Azure 가상 네트워크를 사용하여 온-프레미스 라이선스 서버 또는 데이터 저장소에 액세스|• 기존 HPC 클러스터가 있으며 더 많은 리소스가 필요함 <br/><br/>• 추가 HPC 클러스터 인프라를 구입 및 관리하지 않으려 함<br/><br/>• 일시적인 최대 수요 기간 또는 특수한 프로젝트가 존재함
**Azure에서 HPC 클러스터 전체 만들기**<br/><br/>[![IaaS에서 클러스터][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>자세한 정보:<br/>• [Azure에서 HPC 클러스터 솔루션](./big-compute-resources.md)<br/><br/>|• 표준 또는 사용자 지정 Windows 또는 Linux IaaS(infrastructure as a service) 가상 컴퓨터에 빠르고 일관되게 응용 프로그램 및 클러스터 도구 배포.<br/><br/>• 선택한 작업 예약 솔루션을 사용하여 다양한 Big Compute 워크로드 실행.<br/><br/>• 전체 클라우드 기반 솔루션을 만들기 위해 네트워킹 및 저장소를 비롯한 추가 Azure 서비스 사용. |• 추가 Linux 또는 Windows HPC 클러스터 인프라를 구입 및 관리하지 않으려 함<br/><br/>• 일시적인 최대 수요 기간 또는 특수한 프로젝트가 존재함<br/><br/>• 잠시 동안 추가 클러스터가 필요하지만 배포할 컴퓨터 및 공간에 투자하지 않으려 함<br/><br/>• 클라우드에서 완전히 서비스로 실행되도록 계산 집약적인 응용 프로그램을 오프로드하려 함
**Azure에 병렬 응용 프로그램 규모 확장**<br/><br/>[![Azure 배치][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>자세한 정보:<br/>• [Azure 배치의 기본 사항](./batch-technical-overview.md)<br/><br/>• [.NET용 Azure 배치 라이브러리 시작](./batch-dotnet-get-started.md)|• [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)로 개발하여 Windows 또는 Linux 가상 컴퓨터의 풀에서 실행하는 다양한 Big Compute 워크로드의 규모 확장.<br/><br/>• Azure 플랫폼 서비스를 사용하여 가상 컴퓨터, 작업 예약, 재해 복구, 데이터 이동, 종속성 관리 및 응용 프로그램 배포의 배포 및 자동 크기 조정 관리.|• 계산 리소스 또는 작업 스케줄러를 관리하지 않는 대신 응용 프로그램의 실행에 집중하려고 함<br/><br/>• 클라우드에서 서비스로 실행되도록 계산 집약적인 응용 프로그램을 오프로드하려 함<br/><br/>• 계산 워크로드와 일치하도록 계산 리소스의 크기를 자동 조정하려 함


## 큰 계산에 대한 azure 서비스

Big Compute 솔루션 및 워크플로에 결합할 수 있는 계산, 데이터, 네트워킹 및 관련된 서비스에 대한 내용은 다음과 같습니다. Azure 서비스의 자세한 지침은 Azure 서비스 [설명서](https://azure.microsoft.com/documentation/)를 참조하세요. 이 문서의 앞부분에 있는 [시나리오](#scenarios)는 이러한 서비스를 사용하는 방법을 보여줍니다.

>[AZURE.NOTE] Azure는 정기적으로 시나리오에 유용할 수 있는 새로운 서비스를 도입합니다. 질문이 있는 경우 [Azure 파트너](https://pinpoint.microsoft.com/ko-KR/search?keyword=azure) 또는 전자 메일 *bigcompute@microsoft.com*에 문의하세요.

### 계산 서비스

Azure 계산 서비스는 빅 컴퓨팅 솔루션의 핵심이며 다른 계산 서비스는 다양한 시나리오에 대한 장점을 제공합니다. 기본적인 수준에서 이러한 서비스는 응용 프로그램이 Windows Server Hyper-v 기술을 사용하여 Azure에서 제공하는 가상 컴퓨터 기반 계산 인스턴스에서 실행되도록 서로 다른 모드를 제공합니다. 이러한 인스턴스는 표준 및 사용자 지정 Linux 및 Windows 운영 체제와 도구를 실행할 수 있습니다. Azure는 CPU 코어, 메모리, 디스크 용량 및 기타 특성의 서로 다른 구성을 가진 [인스턴스 크기](../virtual-machines/virtual-machines-windows-sizes.md)를 선택하도록 합니다. 필요에 따라 인스턴스를 수천 개의 코어로 확장하고 리소스가 더 적게 필요할 때는 축소할 수 있습니다.

>[AZURE.NOTE] 짧은 대기 시간 및 처리량이 높은 응용 프로그램 네트워크를 요구하는 병렬 MPI 응용 프로그램을 포함하여 HPC 워크로드의 성능 및 확장성을 향상시키기 위해 Azure 계산 집약적 인스턴스를 활용합니다. [H 시리즈 및 계산 집약적인 A 시리즈 VM 정보](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md)를 참조하세요.

부여 | 설명
------------- | -----------
**[가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Microsoft Hyper-V 기술을 사용하여 서비스(IaaS)로서 계산 인프라를 제공<br/><br/>•사용자가 [Azure 마켓플레이스](https://azure.microsoft.com/marketplace/)에서 표준 Windows Server 또는 공급된 Linux 이미지 또는 사용자가 공급한 이미지와 데이터 디스크에서 나온 영구 클라우드 컴퓨터를 유연하게 프로비전하고 관리할 수 있도록 함<br/><br/>• 용량을 자동으로 증가 또는 감소시키는 자동 크기 조정과 함께, 동일한 가상 컴퓨터로부터 대규모 서비스를 빌드하기 위해 [VM 크기 집합](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)으로 배포 및 관리될 수 있음<br/><br/>• 클라우드에서 온-프레미스 계산 클러스터 도구 및 응용 프로그램을 완전히 실행<br/><br/>
**[클라우드 서비스](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Windows Server를 실행 중인 가상 컴퓨터에 있으며 Azure에서 완전히 관리되는 작업자 역할 인스턴스에서 큰 계산 응용 프로그램을 실행할 수 있음<br/><br/>• 서비스 (PaaS) 모델로 플랫폼에서 실행되는 관리 오버헤드가 낮은 확장 가능하고 신뢰할 수 있는 응용 프로그램을 사용<br/><br/>• 온-프레미스 HPC 클러스터 솔루션으로 통합하는 추가 도구 또는 개발이 필요함
**[배치](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• 완전히 관리되는 서비스에서 대규모 병렬 및 배치 워크로드를 실행<br/><br/>• 작업 예약 및 가상 컴퓨터의 관리된 풀의 자동 크기 조정 제공<br/><br/>• 개발자가 서비스 또는 클라우드 가능 기존 응용 프로그램으로 응용 프로그램을 작성하거나 실행할 수 있도록 함<br/>

### 저장소 서비스

일반적으로 큰 계산 솔루션은 입력된 데이터 집합에 대해 작동하며 그 결과 대한 데이터를 생성합니다. 빅 컴퓨팅 솔루션에서 사용하는 Azure 저장소 서비스 중 일부는 다음과 같습니다.

* [Blob, 테이블 및 큐 저장소](https://azure.microsoft.com/documentation/services/storage/) - 대량의 구조화되지 않은 데이터, NoSQL 데이터 및 워크플로와 통신에 대한 메시지를 각각 관리합니다. 예: 큰 기술 데이터 집합 또는 응용 프로그램이 처리하는 입력된 이미지 또는 미디어 파일에 대해 blob 저장소를 사용할 수 있습니다. 솔루션에서 비동기 통신을 위한 큐를 사용할 수 있습니다. [Microsoft Azure Storage 소개](../storage/storage-introduction.md)를 참조하세요.

* [Azure 파일 저장소](https://azure.microsoft.com/services/storage/files/) - 일부 HPC 클러스터 솔루션에 필요한 표준 SMB 프로토콜을 사용하여 Azure에 일반적인 파일 및 데이터를 공유합니다.

* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) - 배치, 실시간 및 대화형 분석에 유용한 클라우드용 대규모 Apache Hadoop Distributed File System을 제공합니다.

### 데이터 및 분석 서비스

일부 큰 계산 시나리오는 대규모 데이터 흐름이 포함하0거나 추가 처리 또는 분석에 필요한 데이터를 생성합니다. Azure는 다음과 같은 몇 가지 데이터 및 분석 서비스를 제공합니다.

* [데이터 팩터리](https://azure.microsoft.com/documentation/services/data-factory/) - 온-프레미스, 클라우드 기반 및 인터넷 데이터 저장소에서 데이터를 결합, 집계 및 변환하는 데이터 기반 워크플로(파이프라인)를 구축합니다.

* [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/) - 관리되는 서비스에서 Microsoft SQL Server 관계형 데이터베이스 관리 시스템의 주요 기능을 제공합니다.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - 클라우드에서 Windows Server 또는 Linux 기반 Apache Hadoop 클러스터를 배포 및 제공하여 빅 데이터를 관리, 분석, 보고합니다.

* [기계 학습](https://azure.microsoft.com/documentation/services/machine-learning/) - 사용자가 완전히 관리되는 서비스의 예측 분석 솔루션을 만들고, 테스트, 운영, 관리할 수 있도록 돕습니다.

### 서비스

빅 컴퓨팅 솔루션은 온-프레미스 또는 다른 환경에서 리소스에 연결할 다른 Azure 서비스가 필요할 수 있습니다. 예를 들면 다음과 같습니다.

* [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) - Azure에서 논리적으로 분리된 섹션을 만들어 온-프레미스 데이터 센터에 Azure 리소스를 서로 연결합니다. 크로스-프레미스 가상 네트워크를 통해 Big Compute 응용 프로그램은 온-프레미스 데이터, Active Directory 서비스 및 라이선스 서버에 액세스할 수 있습니다.

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - 온-프레미스 또는 공동 배치 환경에 있는 Microsoft 데이터 센터와 인프라 간에 개인 연결을 만들 수 있습니다. ExpressRoute는 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

* [서비스 버스](https://azure.microsoft.com/documentation/services/service-bus/) - Azure 또는 다른 클라우드 플랫폼이나 데이터 센터에 있는지 여부에 관계 없이 응용 프로그램에서 데이터를 통신하거나 교환하는 여러 메커니즘을 제공합니다.

## 다음 단계

* 솔루션을 구축에 대한 기술 지침을 찾으려면 [배치 및 HPC에 대한 기술 리소스](big-compute-resources.md)를 참조하세요.

* Cycle Computing 및 UberCloud를 포함하여 파트너와 Azure 옵션을 살펴봅니다.

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) 및 [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)에서 제공하는 Azure 빅 컴퓨팅 솔루션에 대해 읽습니다.

* 최신 공지 사항은 [Microsoft HPC 및 배치 팀 블로그](http://blogs.technet.com/b/windowshpc/) 및 [Azure 블로그](https://azure.microsoft.com/blog/tag/hpc/)를 참조하세요.

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=AcomDC_0928_2016-->