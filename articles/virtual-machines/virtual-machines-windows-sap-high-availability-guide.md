<properties
   pageTitle="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 가이드 | Microsoft Azure"
   description="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 가이드"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 가이드

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "VM 및 VHD용 캐싱"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "소프트웨어 RAID"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure 저장소"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "RDBMS 배포의 구조"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Azure VM에서 고가용성 및 재해 복구"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 이상"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 및 이전 버전"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Microsoft Azure 마켓플레이스에서 SQL Server 이미지 사용"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Azure의 SAP용 일반 SQL Server 요약"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "SQL Server RDBMS에 대한 고유 정보"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "저장소 구성"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "백업 및 복원"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "백업 및 복원에 대한 성능 고려 사항"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "기타"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "SAP 리소스"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "사용자 지정 이미지를 사용하여 VM 배포"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Microsoft Azure에서의 SAP용 VM 배포 시나리오"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Azure PowerShell cmdlet 배포"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "SAP 관련 PowerShell cmdlet 다운로드 및 가져오기"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "온-프레미스 도메인에 VM 가입 - Windows에만 해당"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Azure VM 에이전트 다운로드, 설치 및 사용"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "SAP용 Azure 고급 모니터링 확장 구성"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "SAP용 Azure 고급 모니터링에 대한 준비 검사"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Azure 모니터링 인프라 구성에 대한 상태 검사"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "SAP용 Azure 모니터링 인프라의 추가 문제 해결"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "모니터링 구성"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "프록시 구성"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Azure의 SAP용 종단 간 모니터링 설정 확인 및 문제 해결"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "리소스"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Azure 가상 컴퓨터에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구)"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "SAP 응용 프로그램 서버에 대한 고가용성"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "SAP 인스턴스에 대해 자동 시작 사용"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Azure 지역"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "장애 도메인"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "업그레이드 도메인"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Azure 가용성 집합"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Microsoft Azure 가상 컴퓨터 개념"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 프리미엄 저장소"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "고객별 이미지를 사용하여 VM 배포"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "SAP용 고객별 이미지를 사용하여 VM 배포 준비"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Azure용 SAP로 VM 준비"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Azure 디스크와 Azure 이미지 간 차이점"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "온-프레미스에서 Azure로 VHD 업로드"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Azure 저장소 계정 간 디스크 복사"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "SAP 배포를 위한 VM/VHD 구조"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "연결된 디스크에 대한 자동 탑재 설정"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "SAP 인스턴스의 클라우드 전용 배포 개념"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "SAP용 Azure 모니터링 솔루션"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 프리미엄 저장소"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Microsoft Azure 네트워킹"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "저장소: Microsoft Azure 저장소 및 데이터 디스크"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 가이드"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "필수 조건"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "리소스"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Azure Resource Manager와 클래식 배포 모델 간의 SAP HA 차이점"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "리소스 그룹"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Azure Resource Manager를 사용한 클러스터링과 클래식 배포 모델 비교"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "WSFC(Windows Server 장애 조치 클러스터링)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "쿼럼 모드"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "Windows 장애 조치 클러스터 온-프레미스"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "공유 저장소"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "네트워킹/이름 확인"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Microsoft Azure와 Windows 장애 조치 클러스터"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "SIOS DataKeeper를 사용한 Microsoft Azure의 공유 디스크"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Microsoft Azure의 이름 확인"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Azure IaaS의 SAP NetWeaver 고가용성"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "SAP 응용 프로그램 서버에 대한 고가용성"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "SAP (A)SCS 인스턴스의 고가용성"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Azure에서 Windows 장애 조치 클러스터를 사용한 SAP (A)SCS 인스턴스의 고가용성"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "DBMS 인스턴스의 고가용성"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "가능한 종단간 HA 배포 시나리오"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "인프라 준비"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "프로덕션 사용을 위해 회사 네트워크 연결(크로스-프레미스)을 사용하여 VM 배포"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "테스트/데모용 SAP 인스턴스의 클라우드 전용 배포"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Azure 가상 네트워크("
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "DNS IP 주소"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "SAP VM에 대한 고정 IP 주소 설정"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "ILB(내부 부하 분산 장치)에 대한 고정 IP 주소 설정"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Azure ILB(내부 부하 분산 장치)에 대한 기본 ASCS/SCS 부하 분산 규칙"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Azure ILB(내부 부하 분산 장치)에 대한 ASCS/SCS 기본 부하 분산 규칙 변경"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "도메인에 Windows 컴퓨터 추가"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "SAP ASCS/SCS 인스턴스에 사용되는 클러스터 노드 둘 다에 대한 레지스트리 항목 추가"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터 설정"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "클러스터 구성에서 클러스터 노드 수집"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "클러스터 파일 공유 감시 구성"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "파일 공유 만들기"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 구성"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Microsoft .NET Framework 3.5 기능 추가"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "SIOS DataKeeper 설치"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "SIOS DataKeeper 설정"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "SAP NetWeaver 시스템 설치"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "고가용성 ASCS/SCS 인스턴스를 포함하는 SAP 설치"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "SAP 첫 번째 클러스터 노드 설치"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "ASCS/SCS 인스턴스의 SAP 프로필 수정"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "프로브 포트 추가"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "데이터베이스 인스턴스 설치"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "두 번째 클러스터 노드 설치"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "SAP ERS 인스턴스의 Windows 서비스 시작 유형 변경"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "SAP PAS(기본 응용 프로그램 서버) 설치"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "SAP PAS(추가 응용 프로그램 서버) 설치"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "시작 지점 - SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있습니다."
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "노드 A에서 노드 B로 장애 조치 프로세스"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "최종 결과 - SAP ASCS/SCS 인스턴스가 클러스터 노드 B에서 실행되고 있습니다."


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "SAP 제품 가용성 매트릭스"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

회사에서는 Microsoft Azure를 통해 긴 조달 주기를 거치지 않고도 최단 시간에 계산, 저장소 및 네트워크 리소스를 가져올 수 있습니다. Azure 가상 컴퓨터를 사용하여 기업에서는 SAP NetWeaver 기반 응용 프로그램(ABAP, Java 및 ABAP+Java 스택)과 같은 클래식 응용 프로그램을 Azure에 배포하고, 온-프레미스에서 사용할 수 있는 추가 리소스를 확보하지 않고도 안정성과 가용성을 확장할 수 있습니다. 또한 Azure 가상 컴퓨터는 프레미스 간 연결도 지원하므로 기업에서 Azure 가상 컴퓨터를 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 지형에 적극적으로 통합할 수 있습니다.


이 문서에서는 새 Azure Resource Manager 배포 모델에서 이러한 새 방법을 사용하여 Azure에서 고가용성 SAP 시스템을 배포하는 데 필요한 모든 단계를 자세히 설명합니다. 또한 다음과 같은 주요 단계를 안내합니다.


- [리소스][sap-ha-guide-2] 섹션의 뒷부분에 나오는 해당 SAP 설치 가이드 및 참고 확인.  
  이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

- 현재 Azure 클래식 배포 모델과 이 새로운 Azure Resource Manager 배포 모델 간의 차이점 이해

- WSFC(Windows Server 장애 조치 클러스터) 쿼럼 모드 이해: Azure 배포에 적합한 모델을 선택하는 데 필요

- Azure의 WSFC(Windows Server 장애 조치 클러스터) 공유 저장소 이해

- Azure에서 SAP 단일 실패 지점 구성 요소(예: SAP ASCS/SCS 및 DBMS) 및 중복 구성 요소(예: SAP 응용 프로그램 서버)가 보호되는 방법 이해

- Microsoft Azure를 플랫폼으로 사용하고 새 Azure Resource Manager를 사용하여 WSFC(Windows 장애 조치 클러스터)에서 HA(고가용성) SAP 시스템을 설치 및 구성하는 방법에 대한 단계별 접근 방법

- 온-프레미스 배포에는 필요하지 않으나 Azure의 WSFC에 필요한 추가 단계


배포 및 구성을 단순화하기 위해 고가용성 SAP 시스템에 필요한 전체 인프라의 배포를 자동화하고 SAP 시스템의 원하는 SAP 크기 조정을 지원하는 새로운 SAP 3 계층 HA Azure Resource Manager 템플릿을 사용하고 있습니다.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 필수 조건

시작하기 전에 다음 챕터에서 설명하는 필수 조건을 충족하는지 확인하고 리소스 챕터에 나열된 모든 리소스를 검토하세요.

3 계층 SAP NetWeaver에 대한 Azure Resource Manager 템플릿을 사용하고 있습니다. [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

SAP Azure Resource Manager 템플릿의 개요는 다음에 제공됩니다. [https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> 리소스

다음 추가 가이드는 Azure의 SAP 배포 항목에 대해 사용할 수 있습니다.

- [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드][planning-guide]
- [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드][deployment-guide]
- [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드][dbms-guide]
- [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 가이드(이 가이드)][sap-ha-guide]


> [AZURE.NOTE] 가능한 경우 SAP 설치 가이드에 대한 링크가 사용됩니다([SAP 설치 가이드][sap-installation-guides] 참조). SAP NetWeaver 설치 가이드는 Microsoft Azure 가상 컴퓨터에 설치된 SAP NetWeaver 기반 시스템에 대한 특정 작업만 처리하므로 필수 구성 요소 및 설치 프로세스 부분을 신중하게 읽어야 합니다.

다음 SAP 정보는 Azure의 SAP 항목과 관련이 있습니다.


| Note 번호 | 제목                                                    
| ------------- |----------------------------------------------------------
| [1928533] | Azure의 SAP 응용 프로그램: 지원 제품 및 크기 조정 
| [2015553] | Microsoft Azure의 SAP: 지원 필수 조건         
| [1999351] | 향상된 SAP용 Azure 모니터링                        
| [2178632] | Microsoft Azure의 SAP용 주요 모니터링 메트릭        
| [1999351] | Windows에서의 가상화: 향상된 모니터링           


Azure 구독의 일반적인 기본 제한 및 최대 제한은 [이 문서][azure-subscription-service-limits-subscription]에서 확인할 수 있습니다.

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Azure Resource Manager와 클래식 배포 모델 간의 SAP HA 차이점 

> [AZURE.NOTE] 클래식 배포 모델을 ASM(Azure 서비스 관리) 모델이라고도 합니다.

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> 리소스 그룹
리소스 그룹은 동일한 수명 주기(예: 동시에 만들어지고 삭제됨)를 갖는 모든 리소스를 포함하는 새로운 개념입니다. 리소스 그룹에 대한 자세한 내용은 이 문서를 참조하세요.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure Resource Manager를 사용한 클러스터링과 클래식 배포 모델 비교 

새 Azure Resource Manager 모델은 클래식 배포 모델과 비교할 때 다음과 같이 변경된 HA를 제공합니다.

- Azure ILB(내부 부하 분산 장치)를 사용하기 위해 클라우드 서비스가 있어야 할 필요는 없습니다.

이전 Azure 클래식 모델을 계속 사용하려면 [Azure에서 SAP NetWeaver - SIOS Datakeeper를 통해 Azure에서 Windows Server 장애 조치 클러스터를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링](http://go.microsoft.com/fwlink/?LinkId=613056) 문서에 설명된 절차를 따라야 합니다.

> [AZURE.NOTE] 새 Azure Resource Manager 배포 모델은 클래식 배포 모델과 비교할 때 여러 이점을 제공하므로 SAP 설치에는 이 모델을 사용하는 것이 좋습니다. 자세한 내용은 [이 문서][virtual-machines-azure-resource-manager-architecture-benefits-arm]를 참조하세요.


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> WSFC(Windows Server 장애 조치 클러스터링) 

Microsoft WSFC는 Windows의 고가용성 SAP ASCS/SCS 설치 및 DBMS에 대한 기술적 기반입니다.

장애 조치 클러스터는 함께 작동하여 응용 프로그램 및 서비스의 가용성을 높이는 1+n개 독립 서버(노드) 그룹입니다. 노드 장애가 발생하는 경우 WSFC는 정의된 응용 프로그램 및/또는 서비스를 제공하기 위해 정상 클러스터를 계속 유지하면서 발생할 수 있는 오류 수를 확인해야 합니다. 이를 위해 다양한 쿼럼 모드를 사용할 수 있습니다.
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> 쿼럼 모드

WSFC와 함께 네 개의 다른 쿼럼 모드를 사용할 수 있습니다.

- **노드 과반수:** 각 노드에서 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 이 옵션은 노드 수가 홀수일 때 권장됩니다. 예를 들어 7 노드 클러스터 중에서 3개의 노드가 실패하면 클러스터는 여전히 과반수 조건을 충족하므로 계속 실행됩니다.

- **노드 및 디스크 과반수:** 사용 가능하고 통신이 설정되어 있을 때마다 각 노드와 클러스터 저장소의 지정된 디스크를 더한 크기("디스크 감시")가 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 이 모드는 노드 수가 짝수인 클러스터 환경에 적합합니다. 디스크 외에 노드 절반이 온라인 상태이면 클러스터는 정상 상태를 유지합니다.

- **노드 및 파일 공유 과반수:** 사용 가능하고 통신이 설정되어 있을 때마다 각 노드와 관리자가 만든 지정된 파일 공유를 더한 크기(파일 공유 감시)가 투표할 수 있습니다. 클러스터는 투표 수의 과반수, 즉 절반 이상일 때만 작동합니다. 이 모드의 노드 수가 짝수인 클러스터 환경에 적합하며, 감시 디스크 대신 감시 파일 공유를 사용하지만 노드 및 디스크 과반수 모드와 비슷합니다. 구현하기는 쉽지만 파일 공유 자체의 가용성이 낮을 경우 단일 실패 지점이 될 수 있습니다.

- **과반수 없음: 디스크만:** 하나의 노드를 사용할 수 있고 클러스터 저장소의 특정 디스크와 통신이 설정된 경우 클러스터에는 쿼럼이 하나 있습니다. 해당 디스크와도 통신하는 노드만 클러스터에 참여할 수 있습니다. 이 모드는 사용하지 않는 것이 좋습니다.  

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows 장애 조치 클러스터 온-프레미스

이 예제에는 두 노드로 구성된 클러스터가 있습니다. 두 노드가 모두 작동되고 있는 동안 노드 간 네트워크 연결이 끊어지면, 클러스터의 응용 프로그램 및 서비스를 계속 제공할 노드를 명확 히 구분해야 합니다. 쿼럼 디스크 또는 파일 공유는 이 용도로 사용됩니다. 쿼럼 디스크 또는 파일 공유에 액세스할 수 있는 노드는 서비스의 액세스 가능성을 보장합니다.

이 예제에서는 2 노드 클러스터를 사용합니다. 이러한 이유로 노드 및 파일 공유 쿼럼 모드를 선택했습니다. 노드 및 디스크 과반수도 유효한 옵션입니다. 프로덕션 환경에서 쿼럼 디스크를 대신 사용하고, 네트워크 및 저장소 시스템 기술을 활용하여 가용성을 높이는 것이 좋습니다.

![그림 1: Azure에서 SAP ASCS/SCS에 대해 제안된 Windows Server 장애 조치 클러스터 구성][sap-ha-guide-figure-1000]

_**그림 1:** Azure에서 SAP ASCS/SCS에 대해 제안된 Windows Server 장애 조치 클러스터 구성_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 공유 저장소

위의 그림은 두 개의 노드가 있는 공유 저장소 클러스터를 보여 줍니다. 온-프레미스의 공유 저장소 클러스터에는 클러스터 내의 모든 노드에 대해 표시되는 공유 저장소가 있습니다. 잠금 메커니즘은 손상으로부터 데이터를 보호합니다. 또한 모든 노드는 다른 노드에 장애가 있는지 알 수 있습니다. 한 노드가 실패하면 나머지 하나가 저장소 리소스의 소유권을 가지며, 서비스의 가용성을 보장합니다.

> [AZURE.NOTE] SQL Server와 같은 일부 DBMS를 사용하여 고가용성을 보장하려는 경우에는 공유 디스크가 필요하지 않습니다. SQL Server AlwaysOn은 한 클러스터 노드의 로컬 디스크에서 다른 클러스터 노드의 로컬 디스크로 DBMS 데이터 및 로그 파일을 복제합니다. 따라서 Windows 클러스터 구성에는 공유 디스크가 필요하지 않습니다.

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> 네트워킹/이름 확인

클러스터에는 DNS 서버에서 제공하는 가상 IP 주소 및 가상 호스트 이름을 통해 연결할 수 있습니다. 온-프레미스의 노드와 DNS 서버는 여러 개의 IP 주소를 처리할 수 있습니다.

표준 설치에서는 다음 두 가지 이상의 네트워크 연결이 사용됩니다.

- 저장소에 대한 전용 연결
- 하트비트에 대한 클러스터 내부 네트워크 연결
- 클라이언트에서 클러스터에 연결하는 데 사용하는 공용 네트워크



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Microsoft Azure와 Windows 장애 조치 클러스터

베어 메탈 또는 사설 클라우드 배포에 비해, Microsoft Azure 가상 컴퓨터는 WSFC를 구성하기 위한 추가 단계가 필요합니다. 공유 클러스터 디스크를 만들려면 SAP ASCS/SCS 인스턴스에 여러 개의 IP 주소 및 가상 호스트 이름이 필요합니다.

아래에서는 Microsoft Azure에서 SAP HA 중앙 서비스 클러스터를 만들 때 필요한 추가 개념 및 단계에 대해 논의합니다. 타사 도구 SIOS DataKeeper를 설정하고 Azure 내부 부하 분산 장치를 구성하는 방법을 보여 주는 단계가 제공됩니다. 이러한 도구는 Microsoft Azure에서 파일 공유 감시를 사용하여 Windows 장애 조치 클러스터를 만들 수 있도록 지원합니다.


![그림 2: 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치 클러스터 구성 스키마][sap-ha-guide-figure-1001]

_**그림 2:** 공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치 클러스터 구성 스키마_


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> SIOS DataKeeper를 사용한 Microsoft Azure의 공유 디스크

2016년 9월을 기준으로 Microsoft Azure는 공유 저장소 클러스터를 만들기 위한 공유 저장소를 제공하지 않습니다. 그러나 고가용성 SAP ASCS/SCS 인스턴스에는 클러스터 공유 저장소가 필요합니다.

타사 소프트웨어 SIOS DataKeeper Cluster Edition을 사용하면 클러스터 공유 저장소를 시뮬레이션하는 미러링된 저장소를 만들 수 있습니다. SIOS 솔루션은 실시간 동기 데이터 복제 기능을 제공합니다. 클러스터에 대한 공유 디스크 리소스를 만드는 방법은 다음과 같습니다.

- Windows 클러스터 구성에 있는 각 VM에 추가 Azure VHD 연결
- SIOS DataKeeper Cluster Edition을 두 VM 노드에서 실행
- 원본 VM의 추가 VHD 연결 볼륨의 콘텐츠를 대상 VM의 추가 VHD 연결 볼륨에 미러링하는 방식으로 SIOS DataKeeper Cluster Edition 구성 SIOS DataKeeper는 원본 및 대상 로컬 볼륨을 추상화한 다음 Windows 장애 조치(Failover) 클러스터에 단일 공유 디스크로 제공합니다.

SIOS DataKeeper 제품에 대한 자세한 내용은 [http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)를 참조하세요.

 ![그림 3: SIOS DataKeeper를 사용하는 Azure의 Windows Server 장애 조치 클러스터 구성 스키마][sap-ha-guide-figure-1002]

_**그림 3:** SIOS DataKeeper를 사용하는 Azure의 Windows Server 장애 조치 클러스터 구성 스키마_

> [AZURE.NOTE] SQL Server와 같은 일부 DBMS를 사용하여 고가용성을 보장하려는 경우에는 공유 디스크가 필요하지 않습니다. SQL Server AlwaysOn은 한 클러스터 노드의 로컬 디스크에서 다른 클러스터 노드의 로컬 디스크로 DBMS 데이터 및 로그 파일을 복제합니다. 따라서 Windows 클러스터 구성에는 공유 디스크가 필요하지 않습니다.

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Microsoft Azure의 이름 확인

Microsoft Azure 클라우드 플랫폼은 가상 IP 주소(예: 부동 IP)를 구성할 가능성을 제공하지 않습니다. 이러한 이유로 클라우드의 클러스터 리소스에 연결하도록 가상 IP 주소를 설정하기 위한 대체 솔루션이 필요합니다. Azure는 ILB(내부 부하 분산 장치)를 제공합니다. ILB를 사용하면 클러스터의 가상 IP 주소를 통해 클러스터에 연결할 수 있습니다. 따라서 클러스터 노드를 포함하는 리소스 그룹에 ILB를 배포해야 합니다. 그런 후 ILB의 프로브 포트를 사용하여 필요한 모든 포트 전달 규칙을 구성해야 합니다. 클라이언트는 가상 호스트 이름을 통해 연결할 수 있습니다. DNS 서버는 클러스터 IP 주소를 확인하고 ILB는 클러스터의 활성 노드에 대한 전달을 처리합니다.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure IaaS의 SAP NetWeaver 고가용성

앞서 HA 챕터 [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드][planning-guide-11]에서 논의한 것처럼 SAP 응용 프로그램 고가용성(예: SAP 소프트웨어 구성 요소의 HA)을 얻으려면 다음 구성 요소를 보호해야 합니다.

- SAP 응용 프로그램 서버
- SAP ASCS/SCS 인스턴스
- DBMS 서버

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> SAP 응용 프로그램 서버에 대한 고가용성

일반적으로 SAP 응용 프로그램 서버/대화 상자 인스턴스의 경우 특정 고가용성 솔루션을 고려할 필요가 없습니다. 중복성으로 고가용성을 달성하고 다른 Azure 가상 컴퓨터에 여러 대화 상자 인스턴스를 구성합니다. 두 개의 Azure VM에 2개 이상의 SAP 응용 프로그램 인스턴스가 설치되어 있어야 합니다.

![그림 4: SAP 응용 프로그램 서버 HA][sap-ha-guide-figure-2000]

_**그림 4:** SAP 응용 프로그램 서버 HA_


SAP 응용 프로그램 서버를 호스트하는 모든 가상 컴퓨터를 동일한 **Azure 가용성 집합**에 배치해야 합니다. Azure 가용성 집합은 다음을 보장합니다.

- 모든 VM이 동일한 업그레이드 도메인에 속하는지 확인합니다. 예를 들어 VM이 계획된 유지 관리 동안 동시에 업데이트되지 않도록 해야 합니다.
- 모든 VM이 동일한 장애 도메인에 속하는지 확인합니다. 예를 들어 VM은 모든 VM의 가용성에 영향을 줄 수 있는 단일 실패 지점을 피하는 방식으로 배포되어야 합니다.

자세한 내용은 [가상 컴퓨터의 가용성 관리][virtual-machines-manage-availability] 문서를 참조하세요.

Azure Storage 계정은 잠재적인 단일 실패 지점일 수 있으므로 두 개 이상의 VM이 배포될 2개 이상의 Azure Storage 계정이 있어야 합니다. 이상적으로 SAP 대화 상자 인스턴스가 실행되는 모든 VM을 다른 저장소 계정에 배포해야 합니다.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> SAP (A)SCS 인스턴스의 고가용성 

![그림 5: 고가용성 SAP ASCS/SCS 인스턴스 HA][sap-ha-guide-figure-2001]

_**그림 5:** 고가용성 SAP ASCS/SCS 인스턴스 HA_


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Azure에서 Windows 장애 조치 클러스터를 사용한 SAP (A)SCS 인스턴스의 고가용성

베어 메탈 또는 사설 클라우드 배포에 비해, Microsoft Azure 가상 컴퓨터는 WSFC를 구성하기 위한 추가 단계가 필요합니다. Windows 장애 조치 클러스터, 공유 클러스터 디스크, 일부 IP 주소 및 가상 호스트 이름을 구축하려면 SAP ASCS/SCS 인스턴스 클러스터링에 대해 Azure ILB(내부 부하 분산 장치)가 필요합니다.

세부 사항은 이 문서 뒷부분에 자세히 설명되어 있습니다.

![그림 6: SIOS DataKeeper를 사용하는 Azure의 SAP ASCS/SCS용 Windows Server 장애 조치 클러스터 구성 스키마][sap-ha-guide-figure-1002]

_**그림 6:** SIOS DataKeeper를 사용하는 Azure의 SAP ASCS/SCS용 Windows Server 장애 조치 클러스터 구성 스키마_


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> DBMS 인스턴스의 고가용성

DBMS는 SAP 시스템의 SPOF이기도 하므로 HA 솔루션을 사용하여 보호해야 합니다. 다음은 Windows Server 장애 조치 클러스터 및 Azure 내부 부하 분산 장치를 사용한 Azure의 SQL Server AlwaysOn HA 솔루션 예제입니다. SQL Server AlwaysOn은 자체 DBMS 복제를 사용하여 DBMS 데이터 및 로그 파일을 복제합니다. 따라서 전체 설정을 단순화하는 공유 디스크 클러스터가 필요하지 않습니다.


![그림 7: SAP DBMS 서버 HA – SQL Server AlwaysOn HA 설정 예제][sap-ha-guide-figure-2003]

_**그림 7:** SAP DBMS 서버 HA – SQL Server AlwaysOn HA 설정 예제_


이 문서는 DBMS의 클러스터링을 다루지 않습니다.

Azure Resource Manager 배포 모델을 사용하는 Azure의 SQL Server 클러스터링에 대한 자세한 내용은 다음 문서를 참조하세요.

- [수동으로 Azure VM의 Always On 가용성 그룹 구성 - 리소스 관리자][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Azure에서 AlwaysOn 가용성 그룹에 대한 내부 부하 분산 장치 구성][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> 가능한 종단간 HA 배포 시나리오

다음은 SAP ASCS/SCS 인스턴스에 대해 하나의 전용 클러스터를 사용하고 DBMS에 대해 다른 클러스터를 사용하는 Azure의 전체 SAP NetWeaver HA 아키텍처 예제입니다.

![그림 8: SAP HA 아키텍처 템플릿 1 – ASCS/SCS용 전용 클러스터 및 DBMS 인스턴스용 전용 클러스터][sap-ha-guide-figure-2004]

_**그림 8:** SAP HA 아키텍처 템플릿 1 – ASCS/SCS용 전용 클러스터 및 DBMS 인스턴스용 전용 클러스터_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> 인프라 준비

SAP에 필요한 리소스의 배포를 간소화하기 위해 SAP용 Azure Resource Manager 템플릿을 개발했습니다.

이러한 3 계층 템플릿은 다음과 같은 고가용성 시나리오도 지원합니다.

- **아키텍처 템플릿 1** – SAP ASCS/SCS 및 DBMS의 SAP 단일 실패 지점에 대해 각각 하나씩 2개의 클러스터

시나리오 1에 대한 Azure Resource Manager 템플릿은 다음에서 사용할 수 있습니다.

- Azure 마켓플레이스 이미지: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- 사용자 지정 이미지: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

SAP 3 계층 마켓플레이스 이미지를 클릭하면 Azure 포털에서 다음 UI가 표시됩니다.

![그림 9: SAP HA Azure Resource Manager 매개 변수 지정][sap-ha-guide-figure-3000]

_**그림 9:** SAP HA Azure Resource Manager 매개 변수 지정_


SYSTEMAVAILABILITY 옵션으로 **HA**를 선택해야 합니다.

템플릿은 다음을 만듭니다.

- 다음에 필요한 모든 **VM**:
    - SAP 응용 프로그램 서버 VM: `<SAPSystemSID>-di-<Number>`
    - ASCS/SCS 클러스터 VM: `<SAPSystemSID>-ascs-<Number>`
    - DBMS 클러스터: `<SAPSystemSID>-db-<Number>`
- **연결된 IP 주소를 갖는 모든 VM에 대한 네트워크 카드**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Azure 저장소 계정**
- 다음에 대한 **가용성 그룹**:
    - SAP 응용 프로그램 서버 VM: `<SAPSystemSID>-avset-di`
    - SAP ASCS /SCS 클러스터 VM: `<SAPSystemSID>-avset-ascs`
    - DBMS 클러스터 VM: `<SAPSystemSID>-avset-db`
- ASCS/SCS 인스턴스 및 IP 주소 `<SAPSystemSID>-lb-ascs`에 대한 모든 포트가 있는 **Azure ILB(내부 부하 분산 장치)**
-	SQL Server DBMS 및 IP 주소 `<SAPSystemSID>-lb-db`에 대한 모든 포트가 있는 **Azure ILB(내부 부하 분산 장치)**
- **네트워크 보안 그룹**: `<SAPSystemSID>-nsg-ascs-0` `<SAPSystemSID>-ascs-0` VM에 대한 외부 RDP 포트 포함


> [AZURE.NOTE]  네트워크 카드와 Azure ILB의 모든 IP 주소는 처음에 **동적**으로 생성됩니다. 문서 뒷부분에 설명된 대로 이를 **고정** IP 주소로 변경해야 합니다.


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 프로덕션 사용을 위해 회사 네트워크 연결(크로스-프레미스)을 사용하여 VM 배포

프로덕션 SAP 시스템의 경우 Azure VPN(사이트 간) 또는 Azure Express 경로를 사용하여 [회사 네트워크 연결(크로스-프레미스)][planning-guide-2.2]을 통해 Azure VM을 배포합니다.

> [AZURE.NOTE]  이 경우 Azure VNET 및 서브넷이 이미 생성되어 준비된 상태여야 합니다.


**NEWOREXISTINGSUBNET** 필드에서 기존 항목을 선택합니다.

Azure VM을 배포하려는 경우 **SUBNETID** 텍스트 필드에 준비된 Azure 네트워크 서브넷 ID의 전체 문자열을 추가해야 합니다.

다음 PowerShell 명령을 사용하여 모든 Azure 네트워크 서브넷 목록을 가져올 수 있습니다.

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


**SUBNETID**는 필드 ID에 표시됩니다.

모든 **SUBNETID** 목록은 다음 PowerShell 명령을 사용하여 가져올 수 있습니다.

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID**는 다음과 유사합니다.

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> 테스트/데모용 SAP 인스턴스의 클라우드 전용 배포

또한 소위 말하는 클라우드 전용 배포 모델에 HA SAP 시스템을 배포할 수도 있습니다.

이 배포는 주로 데모 사용 사례에 적합하며 프로덕션 사용 사례에는 적합하지 않습니다.

NEWOREXISTINGSUBNET 필드에서 _**new**_를 선택합니다. SUBNETID 필드는 **비워** 둡니다.

Azure VNET 및 서브넷은 SAP Azure Resource Manager 템플릿에서 자동으로 생성됩니다.

> [AZURE.NOTE] 또한 동일한 VNET에 AD/DNS에 대한 하나 이상의 전용 VM을 배포해야 합니다. 이러한 VM은 템플릿에 의해 생성되지 않습니다.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 가상 네트워크

이 예제에서 Azure VNET의 주소 공간은 10.0.0.0/16입니다. _**Subnet**_이라는 서브넷이 하나 있으며 주소 범위는 10.0.0.0/24입니다. 모든 VM 및 ILB가 이 VNET에 배포됩니다.
  
> [AZURE.NOTE] 게스트 내의 네트워크 설정(예: IP 주소, DNS 서버, 서브넷 등)은 변경하지 마세요. 모든 네트워크 설정은 Azure를 통해 수행되고 DHCP 서비스를 통해 전파됩니다.

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP 주소

VNET **DNS 서버** 옵션이 **사용자 지정 DNS**로 설정되어 있는지 확인합니다. 다음 같은 경우:

- **[회사 네트워크 연결(크로스-프레미스)][planning-guide-2.2]**: 온-프레미스 DNS 서버의 IP 주소를 추가합니다. 온-프레미스 DNS 서버를 Azure에서 실행 중인 VM으로 확장할 수 있습니다. 이 경우 DNS 서비스를 실행하도록 구성된 Azure VM의 IP 주소를 추가할 수 있습니다.

-	**[클라우드 전용 배포][planning-guide-2.1]**: 동일한 VNET에 추가 VM을 배포합니다. 이 VM은 DNS 서버로 작동합니다. DNS 서비스를 실행하도록 구성된 Azure VM의 IP 주소를 추가합니다.


![그림 10: Azure VNET에 대 한 DNS 서버 구성][sap-ha-guide-figure-3001]

_**그림 10:** Azure VNET에 대 한 DNS 서버 구성_

> [AZURE.NOTE] DNS 서버의 IP 주소를 변경하는 경우 변경 내용을 적용하고 새 DNS 서버를 전파하기 위해 Azure VM을 다시 부팅해야 합니다. 이 예제에서는 DNS 서비스가 설치되어 다음 Windows VM에 구성됩니다.



| VM 역할 | VM 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소  
| ---------------|-------------|--------------------|-------------------
| 첫 번째 DNS 서버 | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 두 번째 DNS 서버 | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소

온-프레미스와 마찬가지로, 다음과 같은 예약된 호스트 이름/IP 주소가 필요합니다.

| 가상 호스트 이름 역할 | 가상 호스트 이름 | 가상 고정 IP 주소 
| ----------------------------------------------------------------------------|------------------|---------------------------
| SAP ASCS/SCS 첫 번째 클러스터 가상 호스트 이름(클러스터 관리에 사용) | pr1-ascs-vir | 10\.0.0.42                 
| SAP ASCS/SCS **INSTANCE** 가상 호스트 이름 | pr1-ascs-sap | `10.0.0.43`             
| SAP DBMS 두 번째 클러스터 가상 호스트 이름(클러스터 관리에 사용) | pr1-dbms-vir | 10\.0.0.32                 
 

클러스터 자체를 관리하는 데 사용되는 가상 호스트 이름 _**pr1-ascs-vir**_ 및 _**pr1-dbms-vir**_과 연결된 IP 주소는 [클러스터 구성의 클러스터 노드 수집][sap-ha-guide-8.12.1]에 설명된 대로 클러스터를 만드는 동안 만들어집니다.

클러스터형 SAP ASCS/SCS 인스턴스 및 클러스터형 DBMS 인스턴스ㄹ에 사용되는 다른 두 개의 가상 호스트 이름 _**pr1 ascs sap**_ 및 _**pr1 dbms sap**_와 연결된 IP 주소는 [클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기][sap-ha-guide-9.1.1] 챕터에 설명된 대로 DNS 서버에서 수동으로 만들 수 있습니다.

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP VM에 대한 고정 IP 주소 설정

클러스터링을 위한 가상 컴퓨터를 배포한 후에 모든 VM에 대한 고정 IP 주소를 설정해야 합니다. 이 작업은 게스트 OS 내에서 수행할 수 없으며 Azure 가상 네트워크 구성에서 구성해야 합니다.

이 작업을 수행하는 한 가지 방법은 Azure 포털을 사용하는 것입니다. Azure 포털에서 다음으로 이동합니다.

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

필드 할당을 **동적**에서 **고정**으로 변경하고 원하는 **IP 주소**를 입력합니다.

> [AZURE.NOTE] 네트워크 카드의 IP 주소를 변경하는 경우 변경 내용을 적용하기 위해 Azure VM을 다시 부팅해야 합니다.


![그림 11: 각 VM의 네트워크 카드에 대한 고정 IP 주소 구성][sap-ha-guide-figure-3002]

_**그림 11:** 각 VM의 네트워크 카드에 대한 고정 IP 주소 구성_

AD/DNS 서비스에 사용할 VM을 포함하여 모든 VM에 대한 모든 네트워크 인터페이스에 대해 이 단계를 반복합니다.

예제에서는 다음 VM 및 고정 IP 주소를 사용합니다.

| VM 역할 | VM 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소  
| ----------------------------------------|--------------|--------------------|-------------------
| 첫 번째 SAP 응용 프로그램 서버 | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 두 번째 SAP 응용 프로그램 서버 | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| 마지막 SAP 응용 프로그램 서버 | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| DBMS 인스턴스의 첫 번째 클러스터 노드 | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| DBMS 인스턴스의 두 번째 클러스터 노드 | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> ILB(내부 부하 분산 장치)에 대한 고정 IP 주소 설정

SAP Azure Resource Manager 템플릿은 SAP ASCS/SCS 인스턴스 클러스터 및 DBMS 클러스터에 사용되는 Azure ILB(내부 부하 분산 장치)를 만듭니다.

초기 배포는 ILB IP 주소를 **동적**으로 설정합니다. IP 주소를 **고정**으로 변경해야 합니다.

이 예제에는 다음 고정 IP 주소를 가진 두 개의 Azure ILB가 있습니다.

| Azure ILB 역할 | Azure ILB 이름 | 고정 IP 주소 
| ---------------------------|----------------|-------------------
| SAP ASCS/SCS 인스턴스 ILB | pr1-lb-ascs | `10.0.0.43`         
| SAP DBMS ILB | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**SAP ASCS/SCS의 가상 호스트 IP 주소 = SAP ASCS/SCS Azure 부하 분산 장치 pr1-l b-ascs의 IP 주소** **DBMS의 가상 이름 IP 주소 = DBMS Azure 부하 분산 장치 pr1-l b-dbms의 IP 주소**

이 예제에서는 내부 부하 분산 장치 _pr1-l b-ascs_의 IP 주소를 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 IP 주소(`10.0.0.43`)로 설정합니다.

![그림 12: SAP ASCS/SCS 인스턴스에 대한 ILB(내부 부하 분산 장치)에 대한 고정 IP 주소 설정][sap-ha-guide-figure-3003]

_**그림 12:** SAP ASCS/SCS 인스턴스에 대한 ILB(내부 부하 분산 장치)에 대한 고정 IP 주소 설정_

같은 방식으로 내부 부하 분산 장치 _pr1-lb-dbms_의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소(예제의 10.0.0.33)로 설정합니다.

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure ILB(내부 부하 분산 장치)에 대한 기본 ASCS/SCS 부하 분산 규칙

기본적으로 SAP Azure Resource Manager 템플릿은 다음에 대해 필요한 모든 포트를 만듭니다.

- 기본 인스턴스 번호가 **00**인 ABAP ASCS 인스턴스
- 기본 인스턴스 번호가 **01**인 Java SCS 인스턴스

따라서 SAP ASCS/SCS 인스턴스 설치 중에 ABAP ASCS 및/또는 Java SCS 인스턴스에 대해 이러한 기본 인스턴스 번호인 00과 01을 사용해야 합니다.

다음 Azure ILB 끝점은 SAP NetWeaver ABAP ASCS 포트에 대해 필요하며 생성됩니다.


| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 00인 ASCS 인스턴스)(ERS가 10)에 대한 구체적인 포트  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| 서버를 큐에 넣기/_lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| ABAP 메시지 서버/_lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| 내부 ABAP 메시지/_lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| 메시지 서버 HTTP/_Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| SAP 시작 서비스 ASCS HTTP/_Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| SAP 시작 서비스 ASCS HTTP/_Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| 복제를 큐에 넣기/_Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| SAP 시작 서비스 ERS HTTP/_Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| SAP 시작 서비스 ERS HTTP _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| 파일 공유 _Lbrule445_ | | 445                                                                      

_**표 1:** SAP NetWeaver ABAP ASCS 인스턴스의 포트 번호_


다음 Azure ILB 끝점은 SAP NetWeaver Java SCS 포트에 대해 필요하므로 생성되어야 합니다.

| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 01인 SCS 인스턴스)(ERS가 11)에 대한 구체적인 포트  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| 서버를 큐에 넣기/_lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| 게이트웨이 서버/_lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Java 메시지 서버/_lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| 메시지 서버 HTTP/_Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| SAP 시작 서비스 SCS HTTP/_Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| SAP 시작 서비스 SCS HTTP/_Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| 복제를 큐에 넣기/_Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| SAP 시작 서비스 ERS HTTP _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| SAP 시작 서비스 ERS HTTP _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| 파일 공유 _Lbrule445_ | | 445                                                                      

_**표 2:** SAP NetWeaver Java SCS 인스턴스의 포트 번호_


![그림 13: Azure ILB(내부 부하 분산 장치)에 대한 기본 ASCS/SCS 부하 분산 규칙][sap-ha-guide-figure-3004]

_**그림 13:** Azure ILB(내부 부하 분산 장치)에 대한 기본 ASCS/SCS 부하 분산 규칙_

같은 방식으로 내부 부하 분산 장치 _**pr1-lb-dbms**_의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소(예제의 _**10.0.0.33**_)로 설정합니다.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure ILB(내부 부하 분산 장치)에 대한 ASCS/SCS 기본 부하 분산 규칙 변경

SAP ASCS 또는 SCS 인스턴스에 대해 다른 인스턴스 번호를 사용하려는 경우 해당 포트의 이름 및 값을 업데이트해야 합니다.

업데이트하는 한 가지 방법은 Azure 포털을 사용하는 것입니다.

`<SID>-lb-ascs load balancer -> Load Balancing Rules`으로 이동합니다.

SAP ASCS 또는 SCS 인스턴스에 속하는 모든 부하 분산 규칙이 변경됩니다.

- 이름
- 포트
- 백 엔드 포트

예를 들어 기본 ASCS 인스턴스 번호를 00에서 31과 같은 수로 변경하려는 경우 _**표 1:** SAP NetWeaver ABAP ASCS 인스턴스의 포트 번호_에 나열된 모든 포트에 대해 이러한 변경을 수행해야 합니다.

다음은 포트 _lbrule3200_에 대한 업데이트 예제입니다.

![그림 14: Azure ILB(내부 부하 분산 장치)에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-figure-3005]

_**그림 14:** Azure ILB(내부 부하 분산 장치)에 대한 ASCS/SCS 기본 부하 분산 규칙 변경_


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 도메인에 Windows 컴퓨터 추가

VM에 고정 IP 주소를 할당한 후 도메인에 VM을 추가합니다.

![그림 15: 도메인에 VM 추가][sap-ha-guide-figure-3006]

_**그림 15:** 도메인에 VM 추가_


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS 인스턴스에 사용되는 클러스터 노드 둘 다에 대한 레지스트리 항목 추가

Azure 내부 부하 분산 장치를 포함하는 Azure 부하 분산 장치는 특정 기간(유휴 시간 제한) 동안 이러한 연결이 유휴 상태일 때 연결을 닫습니다. 반면에 대화 상자 인스턴스의 SAP 작업 프로세스는 첫 번째 큐에 넣지/큐에서 제거 요청이 전송되는 즉시 SAP 큐에 넣기 프로세스에 대한 연결을 엽니다. 이러한 연결은 일반적으로 작업 프로세스 또는 큐에 넣기 프로세스가 다시 시작될 때까지 설정 상태를 유지합니다. 그러나 일정 시간 동안 연결이 유휴 상태이면 Azure ILB에서 연결을 닫습니다. 큐에 넣기 프로세스가 더 이상 없으면 SAP 작업 프로세스에서 해당 프로세스에 대한 연결을 다시 설정한 후에도 문제가 되지 않습니다. 그러나 이러한 활동은 SAP 프로세스의 개발자 추적에 문서화되므로, 실제로 적절하지 않은 이유로 해당 추적에 많은 콘텐츠가 생성됩니다. 따라서 두 클러스터 노드에서 TCP/IP `KeepAliveTime` 및 `KeepAliveInterval`을 변경하는 것이 좋습니다. TCP/IP 매개 변수는 이 문서의 뒷부분에서 설명하는 SAP 프로필 매개 변수와 함께 변경해야 합니다.

SAP ASCS/SCS에 대한 두 Windows 클러스터 노드에 대해 다음 Windows 레지스트리 항목을 추가합니다.

| Path | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 변수 이름 | `KeepAliveTime`                                              
| 변수 유형 | REG\_DWORD(10진수)                                        
| 값 | 120000                                                     
| 설명서 링크 | [https://technet.microsoft.com/ko-KR/library/cc957549.aspx](https://technet.microsoft.com/ko-KR/library/cc957549.aspx) 


_**표 3:** 변경할 첫 번째 TCP/IP 매개 변수_


| Path | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 변수 이름 | `KeepAliveInterval`                                          
| 변수 유형 | REG\_DWORD(10진수)                                        
| 값 | 120000                                                     
| 설명서 링크 | [https://technet.microsoft.com/ko-KR/library/cc957548.aspx](https://technet.microsoft.com/ko-KR/library/cc957548.aspx)


_**표 4:** 변경할 두 번째 TCP/IP 매개 변수_

그런 다음 **두 클러스터 노드를 다시 부팅**하여 변경 내용을 적용합니다.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터 설정

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> 클러스터 구성에서 클러스터 노드 수집

첫 번째 단계는 두 클러스터 노드에 장애 조치 클러스터링 기능을 추가하는 것입니다. 이 작업은 "**역할 및 기능 추가 마법사**"를 통해 수행되며 추가 설명은 필요하지 않습니다.

두 번째 단계는 Windows 장애 조치 클러스터 관리자를 사용하여 장애 조치 클러스터를 설정하는 것입니다.

장애 조치 클러스터 관리자 MMC에서 클러스터 만들기를 클릭하고 첫 번째 클러스터 노드 A의 이름(예: _**pr1-ascs-0**_)만 추가합니다. 두 번째 노드는 아직 추가하지 마세요. 이후 단계에서 추가됩니다.

![그림 16: 장애 조치 클러스터 구성을 추가하는 첫 번째 단계 - 클러스터 노드여야 하는 첫 번째 노드의 서버/VM 이름 추가][sap-ha-guide-figure-3007]

_**그림 16:** 장애 조치 클러스터 구성을 추가하는 첫 번째 단계 - 클러스터 노드여야 하는 첫 번째 노드의 서버/VM 이름 추가_

다음 단계에서 클러스터의 네트워크 이름(가상 호스트 이름)을 묻는 메시지가 표시됩니다.

![그림 17: 장애 조치 클러스터 구성을 추가하기 위한 두 번째 단계 - 클러스터의 이름 정의][sap-ha-guide-figure-3008]

_**그림 17:** 장애 조치 클러스터 구성을 추가하기 위한 두 번째 단계 - 클러스터의 이름 정의_


클러스터가 생성되면 클러스터 유효성 검사 테스트가 실행됩니다.

![그림 18: 장애 조치 클러스터 구성을 추가하기 위한 마지막 단계 – 클러스터 유효성 검사 실행][sap-ha-guide-figure-3009]

_**그림 18:** 장애 조치 클러스터 구성을 추가하기 위한 마지막 단계 – 클러스터 유효성 검사 실행_


![그림 19: 장애 조치 클러스터 구성을 추가하기 위한 마지막 단계 – 클러스터 유효성 검사 후 쿼림 디스크가 없다는 경고 표시][sap-ha-guide-figure-3010]

_**그림 19:** 장애 조치 클러스터 구성을 추가하기 위한 마지막 단계 – 클러스터 유효성 검사 후 쿼림 디스크가 없다는 경고 표시_

이 단계에서 디스크에 대한 모든 경고는 무시해도 되며, 나중에 SIOS 공유 디스크와 함께 파일 공유 감시가 추가됩니다. 이 단계에서는 쿼럼을 고려하지 않습니다.

![그림 20: IP 주소를 갖는 클러스터 코어 리소스 정의 – 새 IP 주소 필요][sap-ha-guide-figure-3011]

_**그림 20:** IP 주소를 갖는 클러스터 코어 리소스 정의 – 새 IP 주소 필요_


서버의 IP 주소는 VM 노드 중 하나를 가리키므로 클러스터가 시작될 수 없습니다. 이제 핵심 클러스터 서비스의 IP 주소를 변경해야 합니다.

예를 들어 클러스터 가상 호스트 이름 _**pr1-ascs-vir**_에 대한 IP 주소(이 예제에서 _**10.0.0.42**_)를 할당해야 합니다. 이 작업은 아래와 같이 핵심 클러스터 서비스의 IP 리소스에 대한 속성 페이지에서 수행됩니다.

![그림 21: '속성'을 사용하여 올바른 IP 주소 변경][sap-ha-guide-figure-3012]

_**그림 21:** '속성'을 사용하여 올바른 IP 주소 변경_


![그림 22: 클러스터에 대해 예약된 IP 주소 할당][sap-ha-guide-figure-3013]

_**그림 22:** 클러스터에 대해 예약된 IP 주소 할당_

IP 주소를 변경한 후 클러스터 가상 호스트 이름을 온라인 상태로 만듭니다.

![그림 23: 올바른 IP 주소를 사용하여 클러스터 핵심 서비스 실행][sap-ha-guide-figure-3014]

_**그림 23:** 올바른 IP 주소를 사용하여 클러스터 핵심 서비스 실행_

핵심 클러스터 서비스가 작동 및 실행되고 있으므로 두 번째 클러스터 노드를 추가할 수 있습니다.

![그림 24: 두 번째 클러스터 노드 추가][sap-ha-guide-figure-3015]

_**그림 24:** 두 번째 클러스터 노드 추가_

![그림 25: 두 번째 클러스터 노드의 호스트 이름(예: pr1-ascs-1) 추가][sap-ha-guide-figure-3016]

_**그림 25:** 두 번째 클러스터 노드의 호스트 이름(예: pr1-ascs-1) 추가_

![그림 26: 확인란을 선택하지 마세요.][sap-ha-guide-figure-3017]

_**그림 26:** 확인란을 선택하지 마세요._

> [AZURE.NOTE]  
"클러스터에 사용할 수 있는 모든 저장소를 추가하세요." 확인란을 선택하지 **않았는지** 확인합니다.

![그림 27: 디스크 쿼럼에 대한 경고 다시 무시][sap-ha-guide-figure-3018]

_**그림 27:** : 디스크 쿼럼에 대한 경고 다시 무시_

쿼럼 및 디스크에 대한 경고는 무시해도 됩니다. 쿼럼 및 공유 디스크 설정은 **[SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치][sap-ha-guide-8.12.3]** 챕터에 설명된 것처럼 나중에 구성됩니다.

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> 클러스터 파일 공유 감시 구성

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> 파일 공유 만들기

쿼럼 디스크 대신 파일 공유 감시를 선택합니다. 이 옵션은 SIOS DataKeeper에서 지원됩니다.

이 문서의 그림에 나오는 구성에서는 Azure에서 실행되며 _**domcontr 0**_이라고 하는 AD/DNS 서버에 파일 공유 감시가 구성됩니다. Azure에 대한 VPN 연결(사이트 간 또는 Express 경로)을 구성했으므로 AD/DNS는 온-프레미스에 있고, 결과적으로 파일 공유 감시를 실행하는 데 적합하지 않을 것입니다.

> [AZURE.NOTE] AD/DNS가 온-프레미스에서만 실행되는 경우 온-프레미스에서 실행되는 AD/DNS Windows OS에서는 파일 공유 감시를 구성하지 않도록 합니다. Azure에서 실행되는 클러스터 노드와 AD/DNS 온-프레미스에서 실행되는 클러스터 노드 간의 네트워크 대기 시간이 너무 길어질 수 있으며 연결 문제가 발생할 수 있기 때문입니다. 클러스터 노드에서 가깝게 실행되도록 Azure Windows VM에서 파일 공유 감시를 구성해야 합니다.

쿼럼 드라이브에는 1024MB 이상의 여유 공간이 필요합니다. 권장 크기는 2048MB입니다.

첫 번째 단계는 클러스터 이름 개체를 추가하는 것입니다.

![그림 28: 클러스터 이름 개체의 공유에 대한 권한 할당][sap-ha-guide-figure-3019]

_**그림 28:** : 클러스터 이름 개체의 공유에 대한 권한 할당_

클러스터 이름 개체(예제의 _**pr1-ascs-vir$**_)에 대한 공유에서 데이터를 변경할 수 있는 권한이 포함되는지 확인합니다. 클러스터 이름 개체를 위에 표시된 목록에 추가하려면 '**추가**'를 누른 다음 아래에 표시된 것 외에 컴퓨터 개체를 확인하도록 필터를 변경해야 합니다.

![그림 29: 컴퓨터 개체도 포함하도록 개체 유형 변경][sap-ha-guide-figure-3020]

_**그림 29:** : 컴퓨터 개체도 포함하도록 개체 유형 변경_

![그림 30: 컴퓨터 개체에 대한 확인란 선택][sap-ha-guide-figure-3021]

_**그림 30:** : 컴퓨터 개체에 대한 확인란 선택_

그런 후에 그림 29와 같이 클러스터 이름 개체를 입력합니다. 레코드가 지금 만들어져야 하므로 그림 28에서처럼 사용 권한을 변경할 수 있습니다.

다음 단계는 공유의 ‘보안’ 탭을 사용하여 클러스터 이름 개체에 대해 보다 구체적인 사용 권한을 정의하는 것입니다.

![그림 31: 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정][sap-ha-guide-figure-3022]

_**그림 31:** 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> 장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 구성

다음 단계는 장애 조치 클러스터 관리자를 사용하여 클러스터 구성을 파일 공유 감시로 변경하는 것입니다.

![그림 32: 여기에 표시된 것처럼 '클러스터 쿼럼 설정 구성 마법사' 호출][sap-ha-guide-figure-3023]

_**그림 32:** 여기에 표시된 것처럼 '클러스터 쿼럼 설정 구성 마법사' 호출_

![그림 33: 다른 쿼럼 구성의 선택 화면][sap-ha-guide-figure-3024]

_**그림 33:** 다른 쿼럼 구성의 선택 화면_

이러한 선택에서는 "_**쿼럼 감시 선택**_"을 선택해야 합니다.

![그림 34: 파일 공유 감시의 선택 화면][sap-ha-guide-figure-3025]

_**그림 34:** 파일 공유 감시의 선택 화면_

예제에서는 "_**파일 공유 감시 구성**_"을 선택해야 합니다.

![그림 35: 감시 공유에 대한 파일 공유 위치 정의][sap-ha-guide-figure-3026]

_**그림 35:** 감시 공유에 대한 파일 공유 위치 정의_


파일 공유에 대한 UNC 경로를 입력합니다(예제의 `\\domcontr-0\FSW`).

‘다음'을 누르면 수행하려는 변경 목록이 표시됩니다. 확인한 후 ‘다음'을 다시 눌러 클러스터 구성을 변경합니다.

![그림 36: 클러스터의 성공적인 재구성을 보여 주는 화면][sap-ha-guide-figure-3027]

_**그림 36:** 클러스터의 성공적인 재구성을 보여 주는 화면_

이 마지막 단계에서는 클러스터 구성을 성공적으로 재구성할 수 있어야 합니다.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치

현재 상태는 Azure에서 작동 중인 Windows Server 장애 조치 클러스터 구성이 있는 것입니다. 그러나 이 클러스터 구성에는 아직 공유 디스크 리소스가 없습니다. SAP ASCS/SCS를 설치하려면 공유 디스크 리소스가 필요합니다. SIOS DataKeeper Cluster Edition은 바로 여기에서 실행됩니다. Azure에서는 필요한 기능을 갖춘 공유 디스크 리소스를 만들도록 허용하지 않으므로, 이 기능을 제공하기 위해 SIOS DataKeeper와 같은 프로그램을 사용해야 합니다.

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Microsoft .NET Framework 3.5 기능 추가

최근 Windows Server 릴리스에서는 Microsoft .NET Framework 3.5가 자동으로 사용되도록 설정되거나 설치되지 않습니다. 그러나 SIOS DataKeeper를 사용하려면 제품이 설치되는 모든 노드에서 .NET framework가 필요합니다. 따라서 다른 VM의 모든 게스트 OS에 .NET 3.5를 설치해야 합니다.

.NET 3.5 Framework는 두 가지 방법으로 추가합니다. 첫 번째 가능성은 아래와 같이 Windows에서 "**역할 및 기능 추가**"를 사용하는 것입니다.

![그림 37: '역할 및 기능 추가 마법사'를 통해 .Net framework 3.5 설치][sap-ha-guide-figure-3028]

_**그림 37:** '역할 및 기능 추가 마법사'를 통해 .Net framework 3.5 설치_

![그림 38: '역할 및 기능 추가 마법사'를 통해 .Net framework 3.5를 설치하는 진행률 표시줄][sap-ha-guide-figure-3029]

_**그림 38:** '역할 및 기능 추가 마법사'를 통해 .Net framework 3.5를 설치하는 진행률 표시줄_

.NET framework 3.5 기능을 사용하도록 설정하는 두 번째 방법은 명령줄 도구 _**dism.exe**_를 사용하는 것입니다. 이 유형의 설치에서는 Windows 설치 미디어의 'sxs' 디렉터리를 액세스 가능하게 만들어야 합니다. 관리자 권한 명령줄 창에서 다음 명령을 실행해야 합니다.

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper 설치

SIOS DataKeeper Cluster Edition을 설치해 보겠습니다. 클러스터의 두 노드 각각에 설치해야 합니다. SIOS DataKeeper는 동기화된 미러를 만들고 클러스터 공유 저장소를 시뮬레이션하여 가상 공유 저장소를 만들 수 있도록 합니다.

SIOS 소프트웨어를 설치하기 전에 도메인 사용자 _**DataKeeperSvc**_를 만들어야 합니다.

> [AZURE.NOTE] 이 _**DataKeeperSvc**_ 사용자를 두 클러스터 노드의 **로컬 관리자** 그룹에 추가합니다.
  
두 클러스터 노드에서 SIOS 소프트웨어 설치

![SIOS 설치 관리자][sap-ha-guide-figure-3030]

![그림 39: SIOS DataKeeper 설치의 첫 번째 화면][sap-ha-guide-figure-3031]

_**그림 39:** SIOS DataKeeper 설치의 첫 번째 화면_

![그림 40: DataKeeper에서 서비스 불가능 상태를 알림][sap-ha-guide-figure-3032]

_**그림 40:** DataKeeper에서 서비스 불가능 상태를 알림_

그림 40의 팝업이 표시되면 "_**예**_"를 선택합니다.

![그림 41: SIOS DataKeeper에 대한 사용자 선택][sap-ha-guide-figure-3033]

_**그림 41:** SIOS DataKeeper에 대한 사용자 선택_


위 화면에서 _**도메인 또는 서버 계정**_을 선택하는 것이 좋습니다.

![그림 42: SIOS DataKeeper 설치에 도메인 사용자 및 암호 제공][sap-ha-guide-figure-3034]

_**그림 42:** SIOS DataKeeper 설치에 도메인 사용자 및 암호 제공_

SIOS DataKeeper에 대해 만든 도메인 계정과 해당 계정의 암호를 지정합니다.

![그림 43: SIOS DataKeeper 라이선스 제공][sap-ha-guide-figure-3035]

_**그림 43:** SIOS DataKeeper 라이선스 제공_

그림 43에서처럼 SIOS DataKeeper에 대한 라이선스 키를 설치합니다. 설치 후에 **VM을 다시 부팅**하라는 메시지가 표시됩니다.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper 설정

두 노드에 SIOS DataKeeper를 설치한 후 구성을 시작해야 합니다. 각 VM에 연결된 추가 VHD 간에 동기식으로 데이터를 복제하기 위해 이러한 구성이 필요합니다. 다음 단계에서는 두 노드의 구성을 보여 줍니다.

![그림 44: DataKeeper 관리 및 구성 도구][sap-ha-guide-figure-3036]

_**그림 44:** DataKeeper 관리 및 구성 도구_


DataKeeper의 관리 및 구성 도구를 시작하고 "_**서버 연결**_" 링크(위에 빨간색 원 표시)를 누릅니다.

![그림 45: 첫 번째 노드의 TCP/IP 주소 이름을 삽입한 후 관리 도구가 연결할 두 번째 노드에 대해서도 같은 작업을 수행합니다.][sap-ha-guide-figure-3037]

_**그림 45:** 첫 번째 노드의 TCP/IP 주소 이름을 삽입한 후 관리 도구가 연결할 두 번째 노드에 대해서도 같은 작업을 수행합니다._

다음 단계는 두 노드 간에 복제 작업을 만드는 것입니다.

![그림 46: 복제 작업 만들기][sap-ha-guide-figure-3038]

_**그림 46:** 복제 작업 만들기_

마법사가 해당 과정을 안내합니다.

![그림 47: 복제 작업의 이름 정의][sap-ha-guide-figure-3039]

_**그림 47:** 복제 작업의 이름 정의_

![그림 48: 현재 원본 노드에 해당하는 노드에 대한 기본 데이터 정의][sap-ha-guide-figure-3040]

_**그림 48:** 현재 원본 노드에 해당하는 노드에 대한 기본 데이터 정의_

첫 번째 단계에서 원본 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의해야 합니다. 두 번째 단계는 대상 노드를 정의하는 것입니다. 다시, 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의해야 합니다.

![그림 49: 현재 대상 노드에 해당하는 노드에 대한 기본 데이터 정의][sap-ha-guide-figure-3041]

_**그림 49:** 현재 대상 노드에 해당하는 노드에 대한 기본 데이터 정의_

다음 단계는 적용할 압축 알고리즘을 정의하는 것입니다. 이 예제에 따르면 복제 스트림을 압축하는 것이 좋습니다. 특히 재동기화 상황에서는 복제 스트림을 압축하면 재동기화 시간을 크게 단축할 수 있습니다. 압축에는 VM의 CPU 및 RAM 리소스가 사용됩니다. 따라서 압축률이 높을수록 CPU 사용률이 더 높아집니다. 나중에 이 설정을 조정하고 변경할 수 없습니다.

확인해야 하는 또 다른 설정은 복제가 비동기식 또는 동기식 중에서 어떤 방식으로 실행되는가 입니다. **SAP ASCS/SCS 구성을 보호하기 위해서는 동기 복제 설정이 필요합니다**.

![그림 50: 복제의 세부 정보 정의][sap-ha-guide-figure-3042]

_**그림 50:** 복제의 세부 정보 정의_

마지막 단계는 복제 작업에 의해 복제되는 볼륨을 WSFC 클러스터 구성에 공유 디스크로 나타낼지 여부를 정의하는 것입니다. SAP ASCS/SCS 구성의 경우 Windows 클러스터가 복제된 볼륨을 클러스터 볼륨으로 사용할 수 있는 공유 디스크로 인식하도록 '예'를 선택해야 합니다.

![그림 51: 복제된 볼륨을 클러스터 볼륨으로 사용하도록 설정하려면 '예' 누르기][sap-ha-guide-figure-3043]

_**그림 51:** 복제된 볼륨을 클러스터 볼륨으로 사용하도록 설정하려면 '예' 누르기_

다 만든 후에는 DataKeeper 관리 도구에 해당 복제 작업이 활성 상태로 표시됩니다.

![그림 52: SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨][sap-ha-guide-figure-3044]

_**그림 52:** SAP ASCS/SCS 공유 디스크에 대한 DataKeeper 동기 미러링이 활성화됨_

결과적으로 디스크는 이제 아래와 같이 Windows 장애 조치 클러스터 관리자에서 DataKeeper 디스크로 표시될 수 있습니다.

![그림 53: DataKeeper에 의해 복제된 디스크가 장애 조치 클러스터 관리자에 표시됨][sap-ha-guide-figure-3045]

_**그림 53:** DataKeeper에 의해 복제된 디스크가 장애 조치 클러스터 관리자에 표시됨_


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver 시스템 설치

사용되는 DBMS 시스템에 따라 설치가 달라지므로 DBMS 설치에 대해서는 설명하지 않을 것입니다. 그러나 다른 DBMS 공급업체가 Azure에 대해 지원하는 기능으로 DBMS의 고가용성 문제가 해결된다고 가정합니다. 예: SQL Server 및 Oracle용 Oracle Data Guard에 대한 AlwaysOn 또는 데이터베이스 미러링. 이 설명서에 사용되는 예제 시나리오에서는 DBMS를 추가적으로 보호하지 않았습니다.

또한 Azure의 이러한 클러스터형 SAP ASCS/SCS 구성과 상호 작용하기 위해 다른 DBMS에 대해 특별히 고려할 사항은 없습니다.

> [AZURE.NOTE]  
SAP NetWeaver ABAP 시스템, Java 시스템 및 ABAP+Java 시스템의 설치 절차는 거의 동일합니다. 가장 큰 차이점은 SAP ABAP 시스템에 ASCS 인스턴스가 하나 있다는 것입니다. SAP Java 시스템에서는 하나의 SCS 인스턴스가 있고, SAP ABAP+Java 시스템에서는 동일한 Microsoft 장애 조치 클러스터 그룹에서 하나의 ASCS와 하나의 SCS 인스턴스가 실행되고 있습니다. 각 SAP NetWeaver 설치 스택에 대한 설치 차이점은 명시적으로 언급될 것입니다. 다른 모든 부분은 동일한 것으로 간주됩니다.

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 고가용성 ASCS/SCS 인스턴스를 포함하는 SAP 설치

> [AZURE.NOTE]  
DataKeeper에서 지원되지 않으므로 DataKeeper 미러 볼륨에 페이지 파일을 배치하지 마세요. 페이지 파일은 Azure에서 VM을 배포할 때 이미 배치된 Azure VM의 임시 D:\\ 드라이브에 둘 수 있습니다. 그렇지 않을 경우 이를 수정하고 Azure VM의 D:\\ 드라이브에 Windows 페이지 파일을 저장합니다.

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> 클러스터형 SAP ASCS/SCS에 대한 가상 호스트 이름 만들기

첫 번째 단계는 ASCS/SCS 인스턴스의 가상 호스트 이름에 필요한 DNS 항목을 만드는 것입니다. 이 작업을 수행하는 데 사용하는 도구는 Windows DNS 관리자입니다. 가상 호스트 이름 외에, 가상 호스트 이름이 할당된 IP 주소도 정의해야 합니다.

> [AZURE.NOTE]  
**ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 IP 주소는 Azure 부하 분산 장치에 할당한 IP 주소와 동일해야 합니다(`<sid>-lb-ascs`). 가상 SAP ASCS/SCS 호스트 이름의 IP 주소 `(pr1-ascs-sap)` = Azure 부하 분산 장치의 IP 주소 `(pr1-lb-ascs)`**

즉, 하나의 SAP 장애 조치 클러스터 역할(예: ABAP 시스템의 경우 단일 ASCS 인스턴스, Java 시스템의 경우 단일 SCS 인스턴스, ABAP+Java의 경우 하나의 ASCS와 하나의 SCS 인스턴스)만 Azure의 단일 Windows Server 장애 조치 클러스터에서 실행될 수 있습니다.

> [AZURE.NOTE] SAP 설치 가이드에 설명된 다중 SID 클러스터링([SAP 설치 가이드][sap-installation-guides] 참조)는 현재 Azure에서 작동하지 않습니다.

![그림 54: SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의][sap-ha-guide-figure-3046]

_**그림 54:** SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의_

이 항목은 다음 그림과 같이 도메인 아래의 DNS 관리자에 표시됩니다.

![그림 55: SAP ASCS/SCS 클러스터 구성에 대해 새 가상 이름 및 TCP/IP 주소 나열][sap-ha-guide-figure-3047]

_**그림 55:** SAP ASCS/SCS 클러스터 구성에 대해 새 가상 이름 및 TCP/IP 주소 나열_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP 첫 번째 클러스터 노드 설치

첫 번째 ASCS/SCS 클러스터 노드의 설치는 SAP 설치 설명서에 설명된 방식과 다르지 않습니다.
- 클러스터 노드 A에서 첫 번째 클러스터 노드 옵션을 실행합니다(예: 우리 예제에서는 _**pr1-ascs-0**_ 호스트).

Azure 내부 부하 분산 장치에 대한 기본 포트를 유지하려는 경우 다음을 선택합니다.

- **ABAP 시스템** - **ASCS** 인스턴스 번호 **00**
- **Java 시스템** - **SCS** 인스턴스 번호 **01**
- **ABAP+JAVA 시스템** - **ASCS** 인스턴스 번호 **00** 및 **SCS** 인스턴스 번호 **01**

먼저 ABAP ASCS 인스턴스에 대해 00, Java SCS 인스턴스에 대해 01 이외의 다른 인스턴스 번호를 사용하려는 경우 **[Azure ILB(내부 부하 분산 장치)에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-8.9]**에 설명된 대로 Azure ILB 기본 부하 분산 규칙을 먼저 변경해야 합니다.

이 단계가 끝나면 일반적인 SAP 설치 설명서에 설명되지 않은 몇 가지 단계를 수행해야 합니다.

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS 인스턴스의 SAP 프로필 수정

새 프로필 매개 변수를 추가해야 합니다. 이 프로필 매개 변수는 연결이 너무 오랫동안 유휴 상태일 때 SAP 작업 프로세스와 큐에 넣기 서버 사이의 연결이 닫히지 않도록 합니다. 이 문서의 **[SAP ASCS/SCS 인스턴스에 사용되는 클러스터 노드 둘 다에 대한 레지스트리 항목 추가][sap-ha-guide-8.11]** 챕터에 문제 시나리오가 나와 있습니다. 이 섹션에서는 몇 가지 기본 TCP/IP 연결 매개 변수에 대한 두 가지 변경 내용도 설명되어 있습니다. 두 번째 단계에서는 연결이 Azure ILB의 유휴 임계값에 도달하지 않게 **keep\_alive** 신호를 보내도록 큐에 추가 서버를 구성해야 합니다. 이 작업을 위해 다음 프로필 매개 변수를

```
enque/encni/set_so_keepalive = true
```

SAP ASCS/SCS 인스턴스 프로필에 추가합니다. 이 예제에서 경로는 다음과 같습니다.

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

예: SAP SCS 인스턴스 프로필 및 해당 경로

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 프로브 포트 추가

전체 클러스터 구성이 Azure 부하 분산 장치에서 작동하기 하기 위해 내부 부하 분산 장치의 프로브 기능을 활용해야 합니다. 일반적으로 Azure 내부 부하 분산 장치는 참여하는 가상 컴퓨터 간에 동일하게 들어오는 작업 부하를 분산합니다. 그러나 이러한 클러스터 구성에서는 인스턴스 중 하나만 활성 상태이고 다른 인스턴스는 수동이라서 작업을 받아들일 수 없으므로 이러한 방식이 작동되지 않습니다. Azure 내부 부하 분산 장치가 활성 인스턴스에만 작업을 할당하는 구성을 허용하기 위해 프로브 기능이 설정되었습니다. 해당 기능을 통해 내부 부하 분산 장치는 활성 상태인 인스턴스를 확인하고, 해당 인스턴스만 작업 분산 대상으로 지정할 수 있습니다. 먼저 클러스터 구성에 지정된 VM 중 하나에서만 다음 PowerShell 명령을 실행하여 현재 _**ProbePort**_ 설정을 확인해 보겠습니다.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![그림 56: 클러스터 구성의 프로브 포트는 기본적으로 0임][sap-ha-guide-figure-3048]

_**그림 56:** 클러스터 구성의 프로브 포트는 기본적으로 0임_

기본적으로 프로브 포트 번호는 0으로 설정됩니다. 구성이 작동하려면 포트를 정의해야 합니다. 이 예제에서는 프로브 포트 _**62300**_이 SAP Azure Resource Manager 템플릿에 정의되어 있으므로 이 포트를 사용해야 합니다. 해당 포트 번호는 다음 두 명령을 사용하여 할당할 수 있습니다.

먼저 SAP 가상 호스트 이름 클러스터 리소스 _**SAP WAC IP**_ 가져오기

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

그런 다음 프로브 포트를 62300으로 설정

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

변경 내용을 활성화하려면 _**SAP PR1**_ 클러스터 역할을 중지했다가 시작해야 합니다.

_**SAP PR1**_ 클러스터 역할을 온라인으로 전환한 후에 _**ProbePort**_가 새 값으로 설정되어 있는지 확인합니다.

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![그림 57: 변경 후 클러스터의 프로브 포트][sap-ha-guide-figure-3049]

_**그림 57:** 변경 후 클러스터의 프로브 포트_

이제 _**ProbePort**_가 _**62300**_으로 설정되어 있는 것을 볼 수 있습니다. 이제 _**ascsha-dbas**_ 등의 다른 호스트에서 _**\\\ascsha-clsap\\sapmnt**_ 파일 공유에 액세스할 수 있습니다.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 데이터베이스 인스턴스 설치

데이터베이스 인스턴스 설치 방법은 SAP 설치 설명서에 설명된 프로세스와 별로 다르지 않습니다. 따라서 여기서는 설명하지 않습니다.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 두 번째 클러스터 노드 설치

역시, 두 번째 클러스터 노드의 설치는 SAP 설치 설명서와 별로 다르지 않습니다. 따라서 두 번째 클러스터 노드를 설치하기 위한 설치 가이드의 단계를 수행합니다.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS 인스턴스의 Windows 서비스 시작 유형 변경

두 클러스터 노드에서 SAP ERS Windows 서비스의 시작 유형을 _**자동(지연된 시작)**_으로 변경합니다.

![그림 58: SAP ERS의 서비스 유형을 지연된 자동으로 변경][sap-ha-guide-figure-3050]

_**그림 58:** SAP ERS의 서비스 유형을 지연된 자동으로 변경_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP PAS(기본 응용 프로그램 서버) 설치

PAS 호스트용으로 지정된 VM에 대한 기본 응용 프로그램 서버 인스턴스 `<sid>-di-0` 설치를 실행합니다. Azure 또는 DataKeeper 사양과는 관련이 없습니다.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP PAS(추가 응용 프로그램 서버) 설치

SAP 응용 프로그램 서버 호스트용으로 지정된 모든 VM(예: `<sid>-di-1`부터 `<sid>-di-<n>`)에서 추가 SAP 응용 프로그램 서버 설치를 실행합니다.

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트

_**장애 조치 클러스터 관리자**_ 및 SIOS DataKeeper UI를 사용하여 SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 디스크 복제를 쉽게 테스트하고 모니터링할 수 있습니다.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> 시작 지점 - SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있습니다.

_**SAP WAC**_ 클러스터 그룹이 클러스터 노드 A(예: _**ascsha-clna**_)에서 실행되고 있습니다. _**SAP WAC**_ 클러스터 그룹에 속하고 ASCS/SCS 인스턴스에 사용되는 공유 디스크 S:가 클러스터 노드 A에 할당됩니다.

![그림 59: 장애 조치 클러스터 관리자: SAP <SID> 클러스터 그룹이 클러스터 노드 A에서 실행되고 있음][sap-ha-guide-figure-5000]

_**그림 59:** : 장애 조치 클러스터 관리자: SAP <SID> 클러스터 그룹이 클러스터 노드 A에서 실행되고 있음_

SIOS DataKeeper UI를 사용하여 공유 디스크 데이터가 클러스터 노드 A(예: _**ascsha-clna [10.0.0.41]**_)의 원본 볼륨 S:에서 클러스터 노드 B(예: _**ascsha-clnb [10.0.0.42]**_)의 대상 볼륨 _**S:**_에 동기식으로 복제되는 것을 볼 수 있습니다.

![그림 60: SIOS DataKeeper: 클러스터 노드 A에 클러스터 노드 B로 로컬 볼륨 복제][sap-ha-guide-figure-5001]

_**그림 60:** SIOS DataKeeper: 클러스터 노드 A에 클러스터 노드 B로 로컬 볼륨 복제_


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> 노드 A에서 노드 B로 장애 조치 프로세스

클러스터 노드 A에서 클러스터 노드 B로 SAP <SID> 클러스터 그룹의 장애 조치를 시작할 수 있습니다.

- 장애 조치 클러스터 관리자 사용

- 장애 조치 PowerShell 사용

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- Windows 게스트 OS 내에서 클러스터 노드 A 다시 시작(이렇게 하면 노드 A에서 노드 B로의 SAP <SID> 클러스터 그룹 자동 장애 조치가 시작됨)

- Azure 포털에서 클러스터 노드 A 다시 시작(이렇게 하면 노드 A에서 노드 B로의 SAP <SID> 클러스터 그룹 자동 장애 조치가 시작됨)

- Azure PowerShell을 사용하여 클러스터 노드 A 다시 시작(이렇게 하면 노드 A에서 노드 B로의 SAP <SID> 클러스터 그룹 자동 장애 조치가 시작됨)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> 최종 결과 - SAP ASCS/SCS 인스턴스가 클러스터 노드 B에서 실행되고 있습니다.

장애 조치 후에 `SAP <SID>` 클러스터 그룹이 클러스터 노드 B(예: _**ascsha-clnb**_)에서 실행되고 있습니다.

![그림 61: 장애 조치 클러스터 관리자: SAP <SID> 클러스터 그룹이 클러스터 노드 B에서 실행되고 있음][sap-ha-guide-figure-5002]

_**그림 61:**: 장애 조치 클러스터 관리자: SAP <SID> 클러스터 그룹이 클러스터 노드 B에서 실행되고 있음_

이제 공유 디스크는 클러스터 노드 B에 탑재됩니다. SIOS DataKeeper는 클러스터 노드 B(예: _**ascsha-clnb [10.0.0.42]**_)의 원본 볼륨 S:에서 클러스터 노드 A(예: _**ascsha-clna [10.0.0.41]**_)의 대상 볼륨 S:로 데이터를 복제합니다.

![그림 62: SIOS DataKeeper: 클러스터 노드 B에 클러스터 노드 A로 로컬 볼륨 복제][sap-ha-guide-figure-5003]

_**그림 62:** SIOS DataKeeper: 클러스터 노드 B에 클러스터 노드 A로 로컬 볼륨 복제_

<!---HONumber=AcomDC_0907_2016-->
