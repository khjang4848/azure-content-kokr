<properties 
   pageTitle="리소스 관리자에서 미리 보기 포털을 사용하여 NSG 관리 | Microsoft Azure"
   description="리소스 관리자에서 미리 보기 포털을 사용하여 NSG를 관리하는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# Preview 포털을 사용하여 NSG 관리

> [AZURE.SELECTOR]
- [포털](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] 클래식 배포 모델.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## 정보 검색

기존 NSG를 보고 기존 NSG에 대한 규칙을 검색하며 NSG가 연결된 리소스에 대해 알아볼 수 있습니다.

### 기존 NSG 보기
구독에서 모든 기존 NSG를 보려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **찾아보기>** > **네트워크 보안 그룹**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. **네트워크 보안 그룹** 블레이드에서 NSG 목록을 확인합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

**RG-NSG** 리소스 그룹에서 NSG 목록을 보려면 다음 단계를 수행합니다.

1. **리소스 그룹 >** > **RG-NSG** > **...**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. 리소스의 목록에서 아래 **리소스** 블레이드에서처럼 NSG 아이콘을 표시하는 항목을 찾습니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
		 
### NSG에 대한 모든 규칙 나열

**NSG-FrontEnd**로 명명된 NSG의 규칙을 보려면 아래 단계를 실행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 **인바운드 보안 규칙**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. **인바운드 보안 규칙** 블레이드는 아래와 같이 표시됩니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. **설정** 탭에서 **아웃바운드 보안 규칙**을 클릭하여 아웃바운드 규칙을 볼 수 있습니다.

>[AZURE.NOTE] 기본 규칙을 보려면 규칙을 표시하는 블레이드 맨 위에서 **규칙 기본** 아이콘을 클릭합니다.

### NSG 연결 보기

**NSG-FrontEnd** NSG가 연결된 리소스를 보려면 아래 단계를 수행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 **서브넷**을 클릭하여 NSG에 연결된 서브넷을 볼 수 있습니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. **설정** 탭에서 **네트워크 인터페이스**를 클릭하여 NSG에 연결된 NIC를 볼 수 있습니다.

## 규칙 관리

기존 NSG에 규칙을 추가하고 기존 규칙을 편집하며 규칙을 제거할 수 있습니다.

### 규칙 추가

컴퓨터에서 **NSG-FrontEnd** NSG에 포트 **443**에 대한 **인바운드** 트래픽을 허용하는 규칙을 추가하려면 아래 단계를 수행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 **인바운드 보안 규칙**을 클릭합니다.
3. **인바운드 보안 규칙** 블레이드에서 **추가**를 클릭합니다. 그런 다음 **인바운드 보안 규칙 추가** 블레이드에서 아래와 같이 값을 입력한 다음 **확인**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. 몇 초 후에 **인바운드 보안 규칙** 블레이드에서 새 규칙이 공지됩니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### 규칙 변경

**인터넷**에서 인바운드 트래픽을 허용하도록 위에서 만든 규칙을 변경하려면는 아래 단계를 수행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 위에서 만든 규칙을 클릭합니다.
3. **allow-https** 블레이드에서 아래와 같이 **원본** 속성을 변경한 다음 **저장**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### 규칙 삭제

위에서 만든 규칙을 삭제하려면 다음 단계를 수행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 위에서 만든 규칙을 클릭합니다.
3. **allow-https** 블레이드에서 **삭제**를 클릭하고 **예**를 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## 연결 관리

NSG를 서브넷 및 NIC에 연결할 수 있습니다. 또한 연결된 모든 리소스에서 NSG를 분리할 수 있습니다.

### NIC에 NSG 연결

**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에 연결하려면 아래 단계를 수행합니다.

1. **네트워크 보안 그룹** 블레이드에서 또는 위에 표시된 **리소스** 블레이드에서 **NSG-FrontEnd**를 클릭합니다.
2. **설정** 탭에서 **네트워크 인터페이스** > **연결** > **TestNICWeb1**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### NIC에서 NSG 분리

**NSG-FrontEnd** NSG를 **TestNICWeb1** NIC에서 분리하려면 아래 단계를 수행합니다.

1. Azure 포털에서 **리소스 그룹 >** > **RG-NSG** > **...** > **TestNICWeb1**을 클릭합니다.
2. **TestNICWeb1** 블레이드에서 **보안 변경...** > **해당 없음**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] 또한 NIC를 기존 NSG에 연결하려면이 블레이드를 사용할 수 있습니다.

### 서브넷에서 NSG 분리

**NSG-FrontEnd** NSG를 **FrontEnd** 서브넷에서 분리하려면 아래 단계를 수행합니다.

1. Azure 포털에서 **리소스 그룹 >** > **RG-NSG** > **...** > **TestVNet**을 클릭합니다.
2. **설정** 블레이드에서 **서브넷** > **FrontEnd** > **네트워크 보안 그룹** > **해당 없음**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. **FrontEnd** 블레이드에서 **저장**을 클릭합니다.

![Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### 서브넷에 NSG 연결

**NSG-FrontEnd** NSG를 **FronEnd** 서브넷에 다시 연결하려면 아래 단계를 수행합니다.

1. Azure 포털에서 **리소스 그룹 >** > **RG-NSG** > **...** > **TestVNet**을 클릭합니다.
2. **설정** 블레이드에서 **서브넷** > **FrontEnd** > **네트워크 보안 그룹** > **NSG-FrontEnd**를 클릭합니다.
3. **FrontEnd** 블레이드에서 **저장**을 클릭합니다.

>[AZURE.NOTE] 또한 NSG의 **설정** 블레이드에서 서브넷에 NSG를 연결할 수 있습니다.

## NSG 삭제

리소스에 연결되지 않은 경우 NSG를 삭제할 수 있습니다. NSG를 삭제하려면 다음 단계를 수행합니다.

1. Azure 포털에서 **리소스 그룹 >** > **RG-NSG** > **...** > **NSG-FrontEnd**를 클릭합니다.
2. **설정** 블레이드에서 **네트워크 인터페이스**를 클릭합니다.
3. NIC가 나열된 경우 NIC를 클릭하고 [NIC에서 NSG 분리](#Dissociate-an-NSG-from-a-NIC)의 2단계를 수행합니다.
4. 각 NIC에 3단계를 반복합니다.
5. **설정** 블레이드에서 **서브넷**을 클릭합니다.
6. 서브넷이 나열된 경우 서브넷을 클릭하고 [서브넷에서 NSG 분리](#Dissociate-an-NSG-from-a-subnet)의 2단계 및 3단계를 수행합니다.
7. **NSG-FrontEnd** 블레이드의 왼쪽으로 스크롤한 다음 **삭제** > **예**를 클릭합니다.

[Azure 포털 - NSG](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## 다음 단계

- NSG에 대한 [로깅을 사용합니다](virtual-network-nsg-manage-log.md).

<!---HONumber=AcomDC_0914_2016-->