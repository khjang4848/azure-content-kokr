리소스|무료|공유(미리 보기)|Basic|표준|Premium(미리 보기)</th>
---|---|---|---|---|---
[앱 서비스 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)당 [웹, 모바일 또는 API 앱](https://azure.microsoft.com/services/app-service/)<sup>1</sup>|10|100|무제한<sup>2</sup>|무제한<sup>2</sup>|무제한<sup>2</sup>
[앱 서비스 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)당 [논리 앱](https://azure.microsoft.com/services/app-service/logic/)</a><sup>1</sup>|10|10|10|코어 당 20|코어 당 20
[앱 서비스 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|지역당 1개|리소스 그룹 당 10|리소스 그룹당 100|리소스 그룹당 100|리소스 그룹당 100
계산 인스턴스 유형|공유됨|공유됨|전용<sup>3</sup>|전용<sup>3</sup>|전용<sup>3</sup></p>
[확장](../articles/app-service-web/web-sites-scale.md)(최대 인스턴스 수)|1개 공유됨|1개 공유됨|3개 전용됨<sup>3</sup>|10개 전용됨<sup>3</sup>|20개 전용됨(ASE에는 50개)<sup>3,4</sup>
저장소<sup>5</sup>|1GB<sup>5</sup>|1GB<sup>5</sup>|10GB<sup>5</sup>|50GB<sup>5</sup>|500GB<sup>4,5</sup></p>
CPU 시간(단축)<sup>6</sup>|3분|3분|무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|무제한, 표준 요금으로 결제|무제한, 표준 요금으로 결제
CPU 시간(일)<sup>6</sup>|60분|240분|무제한, 표준 [요금](https://azure.microsoft.com/pricing/details/app-service/)으로 결제</a>|무제한, 표준 요금으로 결제|무제한, 표준 요금으로 결제
메모리(1시간)|앱 서비스 계획 당 1024MB|앱 당 1024MB|해당 없음|해당 없음|해당 없음
대역폭|165 MB|무제한, [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 적용|무제한, 데이터 전송 요금이 적용|무제한, 데이터 전송 요금이 적용|무제한, 데이터 전송 요금이 적용
응용 프로그램 아키텍처|32비트|32비트|32비트/64비트|32비트/64비트|32비트/64비트
인스턴스당 웹 소켓<sup>7</sup>|5|35|350|Unlimited|Unlimited
응용 프로그램당 동시 [디버거 연결](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)|1|1|1|5|5
[FTP/S 및 SSL이 포함된 azurewebsites.net 하위 도메인](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
[사용자 지정 도메인](../articles/app-service-web/web-sites-custom-domain-name.md) 지원||X|X|X|X
사용자 지정 도메인 [SSL 지원](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|||Unlimited|무제한, 5개 SNI SSL 및1개 IP SSL 연결 포함|무제한, 5개 SNI SSL 및1개 IP SSL 연결 포함
통합 부하 분산 장치||X|X|X|X
[무중단](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[예약된 백업](../articles/app-service-web/web-sites-backup.md)||||하루에 1회|5분마다 1회<sup>8</sup>
[자동 크기 조정](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md)<sup>9</sup>|X|X|X|X|X
[Azure 스케줄러](https://azure.microsoft.com/services/scheduler/) 지원||X|X|X|X
[끝점 모니터링](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[스테이징 슬롯(미리 보기)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
앱당 사용자 지정 도메인</a>||500|500|500|500
SLA||<p>|99\.9%|99\.95%<sup>10</sup>|99\.95%<sup>10</sup>

<sup>1</sup>별도로 명시되지 않는 한 앱 서비스 계획당 앱 및 저장소 할당량입니다.  
<sup>2</sup>이러한 컴퓨터에서 호스트할 수 있는 실제 앱 수는 앱의 활동, 컴퓨터 인스턴스 크기 및 해당 리소스 사용률에 따라 달라집니다.  
<sup>3</sup>전용 인스턴스의 크기는 다양할 수 있습니다. 자세한 내용은 [앱 서비스 가격 책정](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/)을 참조하십시오.
<sup>4</sup>Premium 계층은 앱 서비스 환경을 사용하는 경우 최대 50개의 계산 인스턴스(가용성에 따라)와 500GB의 디스크 공간이 가능하며, 그렇지 않으면 20개의 계산 인스턴스 및 250GB의 저장소가 가능합니다.  
<sup>5</sup>저장소 제한은 동일한 앱 서비스 계획에서 모든 앱에 걸쳐 총 콘텐츠 크기입니다. [앱 서비스 환경](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>에서 더 많은 저장 옵션을 사용할 수 있습니다. 이러한 리소스는 전용 인스턴스의 실제 리소스(인스턴스 크기 및 인스턴스 수)에 의해 제한됩니다.  
<sup>7</sup>두 인스턴스의 Basic 계층으로 앱을 조정하면 두 인스턴스 각각에 대해 동시에 350개가 연결됩니다.  
<sup>8</sup>Premium 계층에서는 앱 서비스 환경을 사용하는 경우 최대 5분마다 그렇지 않으면 하루에 50번 백업 간격을 낮출 수 있습니다.  
<sup>9</sup>사용자 지정 실행 파일 및/또는 스크립트를 주문형, 예약형 또는 지속형으로 앱 서비스 인스턴스 내에서 백그라운드 작업으로 실행합니다. Always On은 연속 Webjob 실행을 위해 필요합니다. Azure 스케줄러 무료 또는 표준은 예약된 WebJobs에 필요합니다. 앱 서비스 인스턴스를 실행할 수 있는 WebJob의 수에 미리 정의된 제한은 없지만 응용 프로그램 코드가 수행하려는 작업에 의존하는 실질적인 제한이 있습니다.  
<sup>10</sup>장애 조치(failover)에 대해 구성된 Azure 트래픽 관리자와 함께 여러 인스턴스를 사용하는 배포에 대해 99.95%의 SLA가 제공됩니다.  

<!---HONumber=AcomDC_0914_2016-->