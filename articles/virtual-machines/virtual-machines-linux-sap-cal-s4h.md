<properties 
pageTitle="Microsoft Azure에서 S/4 HANA 또는 BW/4 HANA 배포 | Microsoft Azure" 
description="Microsoft Azure에서 S/4 HANA 또는 BW/4 HANA 배포" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/>
<tags  
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/>


# Microsoft Azure에서 S/4 HANA 또는 BW/4 HANA 배포 

이 문서에서는 SAP 클라우드 어플라이언스 라이브러리 3.0을 통해 Microsoft Azure에서 S/4 HANA를 배포하는 방법을 설명합니다. 스크린샷은 프로세스를 단계별로 보여줍니다. 프로세스 관점에서 동일한 방식으로 작동하는 BW/4 HANA와 같은 다른 SAP HANA 기반 솔루션을 배포합니다. 서로 다른 솔루션을 선택하면 됩니다.

SAP 클라우드 어플라이언스 라이브러리(SAP CAL)를 시작하려면 [여기](https://cal.sap.com/)로 이동합니다. 새로운 [SAP 클라우드 어플라이언스 라이브러리 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)에 대한 정보를 제공하는 SAP의 블로그가 있습니다.


다음 스크린샷은 Microsoft Azure에 S/4 HANA를 배포하는 방법을 단계별로 보여줍니다. 이 과정은 BW/4 HANA와 같은 다른 솔루션과 동일한 방식으로 작동합니다.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

첫 번째 그림에서는 Microsoft Azure에서 사용할 수 있는 모든 SAP CAL HANA 기반 솔루션을 보여줍니다. 대표적인 예로 과정을 안내하기 위해 "SAP S/4 HANA 온-프레미스 버전"(스크린샷에서 맨 아래 솔루션)을 선택했습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

먼저 새 SAP CAL 계정을 만들어야 합니다. 현재는 Azure에 대해 두 가지 선택 사항이 있습니다. 표준 Azure와 21Vianet이 중국 본토에서 운영하는 Azure입니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

다음으로, Azure 포털에서 확인할 수 있는 Azure 구독 ID를 입력해야 합니다. 가져오는 방법은 아래를 참조하세요. 그 후에는 Azure 관리 인증서를 다운로드해야 합니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

새 Azure 포털의 왼쪽에서 "구독" 항목을 찾습니다. 이 항목을 클릭하면 사용자에 대한 모든 활성 구독이 표시됩니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

구독 중 하나를 선택한 후 "관리 인증서"를 선택하면 새 Azure Resource Manager 모델에 대해 "서비스 보안 주체"를 사용하는 새로운 개념이 있다는 내용이 설명됩니다. 이 모델에 대해 SAP CAL이 아직 도입되지 않았으며 관리 인증서를 작동하려면 여전히 "클래식" 모델과 이전 Azure 포털이 필요합니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

여기서는 이전 Azure 포털을 볼 수 있습니다. 관리 인증서를 업로드하면 SAP CAL에 고객 구독 내에 가상 컴퓨터를 만들 수 있는 권한이 제공됩니다. "구독" 탭 아래에서 SAP CAL 포털에 입력해야 하는 구독 ID를 확인할 수 있습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

그러면 두 번째 탭에서 SAP CAL에서 이전에 다운로드했던 관리 인증서를 업로드할 수 있습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

다운로드한 인증서 파일을 선택하라는 작은 대화 상자가 나타납니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

인증서를 업로드했으면 SAP CAl 내에서 SAP CAL과 고객 Azure 구독 간의 연결을 테스트할 수 있습니다. 연결이 유효함을 알려 주는 작은 메시지가 나타납니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

계정 설정 후 배포할 솔루션을 선택하고 인스턴스를 만들어야 합니다. "기본" 모드에서는 매우 간단합니다. 인스턴스 이름을 입력하고 Azure 지역을 선택하며 솔루션에 대한 마스터 암호를 정의합니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

솔루션의 크기 및 복잡성에 따라 잠시 시간이 흐른 후(예측값은 SAP CAL로 제공됨) "활성"으로 표시되고 사용할 준비가 된 것입니다. 매우 간단합니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

솔루션에 대한 세부 정보가 표시되면 사용자는 어떤 종류의 VM이 배포되었는지 확인할 수 있습니다. 이 경우 다양한 크기 및 용도의 Azure VM 3개가 생성되었습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Azure 포털에서 가상 컴퓨터가 SAP CAL에 지정된 것과 동일한 인스턴스 이름으로 시작하는 것을 확인할 수 있습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

이제 SAP CAL 포털에서 연결 단추를 통해 솔루션에 연결할 수 있습니다. 솔루션에 사용할 모든 기본 자격 증명을 설명하는 사용자 가이드에 대한 링크가 작은 대화 상자에 포함되어 있습니다.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

다른 옵션은 클라이언트 Windows VM에 로그인하고 예를 들어 미리 구성된 SAP GUI를 시작하는 것입니다.

<!---HONumber=AcomDC_0921_2016-->