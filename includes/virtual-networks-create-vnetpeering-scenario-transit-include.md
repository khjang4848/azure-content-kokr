## 서비스 체이닝 - 피어링된 VNet을 통해 전송

시스템 경로를 사용하면 자동으로 트래픽을 쉽게 배포할 수 있지만 가상 어플라이언스를 통해 패킷 라우팅을 제어하고자 하는 경우도 있습니다. 이 시나리오에서는 아래 다이어그램에서 설명한 대로 구독에 HubVNet 및 VNet1이라는 두 개의 VNet이 있습니다. VNet HubVNet에 NVA(네트워크 가상 어플라이언스)를 배포합니다. HubVNet과 VNet1 간에 VNet 피어링을 설정한 후에 사용자 정의 경로를 설정하고 다음 홉을 HubVNet에 있는 NVA로 지정할 수 있습니다.

![NVA 전송](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] 간단히 하기 위해 여기서는 모든 VNet이 동일한 구독에 있다고 가정합니다. 그러나 구독간 시나리오에서도 작동합니다.

전송 라우팅을 사용할 수 있는 주요 속성은 "전달된 트래픽 허용" 매개 변수입니다. 이를 통해 피어링된 VNet에 있는 NVA 간에 트래픽을 받고 보낼 수 있습니다.

<!---HONumber=AcomDC_0928_2016-->