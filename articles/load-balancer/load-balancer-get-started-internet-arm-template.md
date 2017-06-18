<properties
   pageTitle="템플릿을 사용하여 Resource Manager에서 인터넷 연결 부하 분산 장치 만들기 | Microsoft Azure"
   description="템플릿을 사용하여 Resource Manager에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# 템플릿을 사용하여 인터넷 연결 부하 분산 장치 구성 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 이 문서에서는 Resource Manager 배포 모델에 대해 설명합니다. 또한 [클래식 배포 모델을 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-classic-portal.md).


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## 클릭하여 배포하는 방식으로 템플릿 배포

공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭하여 배포하는 방식으로 이 템플릿을 배포하려면 [이 링크](http://go.microsoft.com/fwlink/?LinkId=544801)에 따라 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

## PowerShell을 사용하여 템플릿 배포

PowerShell을 사용하여 다운로드한 템플릿을 배포하려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](../../articles/powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.

2. **New-AzureRmResourceGroupDeployment** cmdlet을 실행하고 템플릿을 사용하여 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'

## Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용하여 템플릿을 배포하려면 아래 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../../articles/xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 Resource Manager 모드로 전환합니다.

		azure config mode arm

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    New mode is arm

3. 브라우저에서 **https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json**으로 이동하고 json 파일의 내용을 복사하여 컴퓨터에 새 파일로 붙여넣습니다. 이 시나리오의 경우 아래 값을 **c:\\lb\\azuredeploy.parameters.json**이라는 파일로 복사합니다.
4. 위에서 다운로드하여 수정한 템플릿과 매개 변수 파일로 **azure group deployment create** cmdlet을 실행하여 새 부하 분산 장치를 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->