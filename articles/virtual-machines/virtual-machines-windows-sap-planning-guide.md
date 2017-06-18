<properties
   pageTitle="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드 | Microsoft Azure"
   description="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
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
   ms.author="sedusch"/>

# Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드

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
[planning-guide-11.4]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Azure 가상 컴퓨터에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구)"
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
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]: virtual-machines-windows-capture-image.md#prepare-the-vm-for-image-capture
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

회사에서는 Microsoft Azure를 통해 긴 조달 주기를 거치지 않고도 최단 시간에 계산 및 저장소 리소스를 가져올 수 있습니다. Azure 가상 컴퓨터를 사용하여 기업에서는 SAP NetWeaver 기반 응용 프로그램과 같은 클래식 응용 프로그램을 Azure에 배포하고, 온-프레미스에서 사용할 수 있는 추가 리소스를 확보하지 않고도 안정성과 가용성을 확장할 수 있습니다. 또한 Azure 가상 컴퓨터 서비스는 프레미스 간 연결도 지원하므로 기업에서 Azure 가상 컴퓨터를 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 지형에 적극적으로 통합할 수 있습니다. 이 백서에서는 Microsoft Azure 가상 컴퓨터의 기본 사항에 대해 설명하고 Azure의 SAP NetWeaver 설치에 대한 계획 연습 과정 및 구현 고려 사항을 제공하므로 Azure에서 SAP NetWeaver의 실제 배포를 시작하기 전에 이 문서를 읽으면 도움이 됩니다. 이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## 요약
클라우드 컴퓨팅은 중소 기업에서 대기업 및 다국적 기업까지 IT 업계 내에서 점점 더 중요해지는 널리 사용되는 용어입니다.

Microsoft Azure는 다양한 새로운 가능성을 제공하는 Microsoft의 클라우드 서비스 플랫폼입니다. 이제 고객은 클라우드의 서비스로 응용 프로그램을 신속하게 프로비전 및 프로비전 해제할 수 있으므로 기술 또는 예산 제한에 제한되지 않습니다. 하드웨어 인프라에 시간과 예산을 투자하는 대신 기업은 고객 및 사용자를 위한 응용 프로그램, 비즈니스 프로세스 및 그 이점에 집중할 수 있습니다.

Microsoft Azure 가상 컴퓨터 서비스와 함께 Microsoft는 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. SAP NetWeaver 기반 응용 프로그램은 Azure 가상 컴퓨터(IaaS)에서 지원됩니다. 이 백서는 선택한 플랫폼으로 Microsoft Azure 내에서 SAP NetWeaver 기반 응용 프로그램을 계획하고 구현하는 방법에 대해 설명합니다.

또한 다음 두 가지 주요 측면을 집중적으로 설명합니다.

* 첫 번째 부분에서는 Azure의 SAP NetWeaver 기반 응용 프로그램에 대해 지원되는 두 가지 배포 패턴을 설명합니다. 또한 SAP 배포를 고려한 Azure의 일반적인 사용에 대해서도 설명합니다.
* 두 번째 부분에서는 첫 번째 부분에서 설명하는 두 가지 다른 시나리오의 구현에 대해 자세히 설명합니다.

추가 리소스에 대해서는 이 문서의 [리소스][planning-guide-1.2] 장을 참조하세요.

### 사전 정의
문서 전체에서 다음과 같은 용어를 사용 합니다.

* IaaS: 서비스 제공 인프라
* PaaS: Platform as a Service
* SaaS: Software as a Service.
* ARM : Azure Resource Manager
* SAP 구성 요소: ECC, BW, Solution Manager 또는 EP 등의 개별 SAP 응용 프로그램. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 응용 프로그램을 기반으로 사용할 수 있습니다.
* SAP 환경: 하나 이상의 SAP 구성 요소가 논리적으로 그룹화되어 개발, QAS, 학습, DR 또는 프로덕션과 같은 비즈니스 기능을 수행합니다.
* SAP 배경: 고객 IT 환경의 전체 SAP 자산입니다. SAP 지형에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
* SAP 시스템: 예를 들어 SAP ERP 개발 시스템, SAP BW 테스트 시스템, SAP CRM 프로덕션 시스템 등의 응용 프로그램 계층과 DBMS 계층의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층을 나눌 수 없습니다. 즉, SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됩니다. 그러나 Azure 또는 온-프레미스에는 SAP 지형의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다.
* 클라우드 전용 배포: Azure 구독이 사이트 간 연결 또는 Express 경로 연결을 통해 온-프레미스 네트워크 인프라에 연결되지 않는 배포입니다. 공통 Azure 설명서에서 이러한 종류의 배포는 '클라우드 전용' 배포로도 설명됩니다. 이 방법으로 배포된 가상 컴퓨터는 인터넷과 공용 IP 주소 및/또는 Azure의 VM에 할당된 공용 DNS 이름을 통해 액세스됩니다. Microsoft Windows의 경우 이러한 유형의 배포에서 온-프레미스 AD(Active Directory) 및 DNS가 Azure로 확장되지 않습니다. 따라서 VM은 온-프레미스 Active Directory에 속하지 않습니다. 예를 들어 OpenLDAP와 Kerberos를 함께 사용하는 Linux 구현에서도 마찬가지입니다.

> [AZURE.NOTE] 이 문서에서 클라우드 전용 배포는 온-프레미스에서 Active Directory/OpenLDAP 또는 이름 확인을 공용 클라우드로 확장하지 않고 Azure에서 단독으로 실행 중인 전체 SAP 지형으로 정의됩니다. Azure에서 호스트되는 SAP 시스템과 온-프레미스에 상주하는 리소스 간에 SAP STMS 또는 기타 온-프레미스 리소스를 사용해야 하는 프로덕션 SAP 시스템 또는 구성에 대해서는 클라우드 전용 구성이 지원되지 않습니다.

* 프레미스 간: VM이 온-프레미스 데이터 센터와 Azure 간에 사이트-사이트, 다중 사이트 또는 Express 경로 방식으로 연결되는 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 프레미스 간 시나리오라고도 합니다. 연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory/OpenLDAP 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이렇게 확장된 VM은 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 시나리오에서는 대부분의 SAP 자산이 배포된다고 예상할 수 있습니다. 자세한 내용은 [이][vpn-gateway-cross-premises-options] 문서 및 [이][vpn-gateway-site-to-site-create] 문서를 참조하세요.

> [AZURE.NOTE] 프로덕션 SAP 시스템의 경우 SAP 시스템을 실행 중인 Azure 가상 컴퓨터가 온-프레미스 도메인의 멤버인 SAP 시스템의 프레미스 간 배포가 지원됩니다. 일부 또는 전체 SAP 지형을 Azure로 배포하기 위한 프레미스 간 구성이 지원됩니다. Azure에서 전체 SAP 지형을 실행하려고 해도 이러한 VM이 온-프레미스 도메인 및 ADS/OpenLDAP에 포함되어야 합니다. 이전 버전의 문서에서 하이브리드-IT 시나리오에 대해 설명했습니다. '하이브리드'란 온-프레미스와 Azure가 프레미스 간 연결을 사용한다는 사실을 기반으로 합니다. 또한 Azure의 VM이 온-프레미스 Active Directory/OpenLDAP의 일부이기도 합니다.

일부 Microsoft 문서에서는 특히 DBMS HA 구성의 경우 프레미스 간 시나리오를 약간 다르게 설명합니다. SAP 관련 문서의 경우 프레미스 간 시나리오가 사이트 간 또는 개인(Express 경로) 방식으로 연결되고 SAP 지형이 온-프레미스와 Azure 간에 분산되는 것으로 된다고 요약되어 있습니다.

### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>리소스
다음 추가 가이드는 Azure의 SAP 배포 항목에 대해 사용할 수 있습니다.

* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드(이 문서)][planning-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드][deployment-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드][dbms-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 배포 가이드][ha-guide]

> [AZURE.IMPORTANT] 가능한 경우 SAP 설치 가이드에 대한 링크가 사용됩니다(InstGuide-01, <http://service.sap.com/instguides> 참조). SAP NetWeaver 설치 가이드는 Microsoft Azure 가상 컴퓨터에 설치된 SAP NetWeaver 시스템에 대한 특정 작업만 처리하므로 필수 구성 요소 및 설치 프로세스 부분을 신중하게 읽어야 합니다.

다음 SAP 정보는 Azure의 SAP 항목과 관련이 있습니다.

| Note 번호 | 제목 |
|--------------|-------|
| [1928533] | Azure의 SAP 응용 프로그램: 지원 제품 및 크기 조정 |
| [2015553] | Microsoft Azure의 SAP: 지원 필수 조건 |
| [1999351] | SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] | Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] | Windows에서의 가상화: 향상된 모니터링 |
| [2191498] | Azure와 Linux의 SAP: 향상된 모니터링
| [2243692] | Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제
| [1984787] | SUSE LINUX Enterprise Server 12: 설치 참고
| [2002167] | Red Hat Enterprise Linux 7.x: 설치 및 업그레이드

Linux용 SAP 정보를 모두 포함하는 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)를 읽어 보세요.

Azure 구독의 일반적인 기본 제한 및 최대 제한은 [이 문서][azure-subscription-service-limits-subscription]에서 확인할 수 있습니다.

## 가능한 시나리오
SAP는 기업 내에서 중요 업무용 응용 프로그램 중 하나로 보는 경우가 많습니다. 이러한 응용 프로그램의 아키텍처 및 작업은 대부분 매우 복잡하며, 가용성 및 성능에 대한 요구를 충족하는 것이 매우 중요합니다.

따라서 기업에서는 선택한 클라우드 공급자와는 별개로, 공용 클라우드 환경에서 실행할 수 있는 응용 프로그램을 신중히 고려해야 합니다.

공용 클라우드 내에서 SAP NetWeaver 기반 응용 프로그램을 배포하기 위한 가능한 시스템 유형은 아래와 같습니다.

1. 중간 규모의 프로덕션 시스템
1. 개발 시스템
1. 테스트 시스템
1. 프로토타입 시스템
1. 학습/데모 시스템

Azure IaaS 또는 일반적인 IaaS에 SAP 시스템을 성공적으로 배포하려면 전형적인 외주 업체나 호스팅 서비스 공급자 및 IaaS 제품 간의 주요 차이점을 이해해야 합니다. 기존 호스팅 서비스 공급자 또는 외주 업체은 고객이 호스트하려는 작업에 맞게 인프라(네트워크, 저장소 및 서버 유형)를 조정하지만 IaaS 배포에 적합한 작업을 선택하는 것은 고객의 책임입니다.

첫 번째 단계로, 고객은 다음 항목을 확인해야 합니다.

* SAP에서 지원하는 Azure의 VM 유형
* Azure의 SAP 지원 제품/릴리스
* Azure의 특정 SAP 릴리스에 대해 지원되는 OS 및 DBMS 릴리스
* 여러 다른 Azure SKU에서 제공하는 SAP 처리량

이러한 질문에 대한 답변은 SAP 정보 [1928533]에서 읽을 수 있습니다.

두 번째 단계로, Azure 리소스 및 대역폭 제한을 온-프레미스 시스템의 실제 리소스 사용량과 비교해야 합니다. 따라서 고객은 다음 영역에서 SAP가 지원하는 Azure 유형의 다양한 기능에 익숙해져야 합니다.

* 여러 VM 유형의 CPU 및 메모리 리소스
* 여러 VM 유형의 IOPS 대역폭
* 여러 VM 유형의 네트워크 기능.

대부분의 데이터는 [여기][virtual-machines-sizes]에서 찾을 수 있습니다.

위의 링크에 나와 있는 제한은 상한입니다. 모든 리소스에 대해 제한이 있는 것은 아닙니다. 예를 들어 IOPS는 모든 상황에서 제공될 수 있습니다. 선택한 VM 유형의 CPU 및 메모리 리소스는 예외입니다. SAP에서 지원하는 VM 유형의 경우 CPU 및 메모리 리소스가 예약되므로 VM 내에서 언제든지 사용 가능합니다.

다른 IaaS 플랫폼과 같은 Microsoft Azure Platform은 다중 테넌트 플랫폼입니다. 즉, 저장소, 네트워크 및 기타 리소스가 테넌트 간에 공유됩니다. 한 테넌트가 다른 테넌트(시끄러운 이웃)의 성능에 심각하게 영향을 주지 못하게 하기 위해 지능적 제한 및 할당량 논리가 사용됩니다. Azure의 논리는 대역폭에서 비교적 작은 변화를 경험하게 하려는 것이지만 공유가 많이 되는 플랫폼의 경우 고객이 온-프레미스 배포에서 익숙해진 것보다 리소스/대역폭 가용성에서 더 큰 차이를 나타내는 경향이 있습니다. 결과적으로 네트워킹 또는 저장소 I/O(대기 시간 및 볼륨)와 관련해서 분당 대역폭 수준이 다른 것을 경험할 수 있습니다. Azure의 SAP 시스템이 온-프레미스 시스템의 경우보다 더 큰 차이를 경험할 가능성을 고려해야 합니다.

마지막 단계는 가용성 요구 사항을 평가하는 것입니다. 기본 Azure 인프라를 업데이트해야 하고 VM이 실행되는 호스트를 재부팅해야 하는 경우가 있을 수 있습니다. 이러한 경우 해당 호스트에서 실행되는 VM을 종료한 후 다시 시작할 수 있습니다. 이러한 유지 관리 시간은 특정 지역의 경우 핵심적인 업무 시간이 아니지만 다시 시작이 진행되는 몇 시간의 기간은 비교적 긴 시간입니다. 이러한 업데이트의 영향 중 일부 또는 전체를 완화하도록 Azure 플랫폼 내의 다양한 기술을 구성할 수 있습니다. Azure 플랫폼, DBMS 및 SAP 응용 프로그램은 앞으로 이러한 다시 시작이 미치는 영향을 최소화하도록 개선될 것입니다.

Azure에 SAP 시스템을 성공적으로 배포하려면 온-프레미스 SAP 시스템 운영 체제, 데이터베이스 및 SAP 응용 프로그램이 SAP Azure 지원 매트릭스에 표시되고, Azure 인프라가 제공할 수 있고 가용성 SLA Microsoft Azure 제공 서비스에서 작동될 수 있는 리소스에 잘 맞아야 합니다. 이러한 시스템이 파악되면 다음과 같은 두 가지 배포 시나리오 중 하나를 결정해야 합니다.

### <a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포
 
![SAP 데모 또는 학습 시나리오가 Azure에 배포된 단일 VM][planning-guide-figure-100]

이 시나리오는 단일 VM 내에 SAP 및 비 SAP 소프트웨어의 모든 구성 요소가 설치되는 학습 또는 데모 시스템에 일반적입니다. 프로덕션 SAP 시스템은 이 배포 시나리오에서 지원되지 않습니다. 일반적으로 이 시나리오는 다음 요구 사항을 충족합니다.

* VM 자체는 공용 네트워크를 통해 액세스할 수 있습니다. VM 내에서 실행되는 응용 프로그램을 데모 또는 학습 콘텐츠를 소유하는 회사나 고객의 온-프레미스 네트워크에 직접 연결할 필요는 없습니다.
* 교육 또는 데모 시나리오를 나타내는 여러 VM의 경우 VM 간에 네트워크 통신 및 이름 확인이 작동해야 합니다. 하지만 여러 VM 집합을 간섭 없이 나란히 배포할 수 있도록 VM 집합 간의 통신을 격리해야 합니다.
* 최종 사용자가 Azure에서 호스트되는 VM에 원격으로 로그인하려면 인터넷에 연결되어야 합니다. 게스트 OS에 따라 터미널 서비스/RDS 또는 VNC/ssh를 통해 VM에 액세스한 후 학습 작업을 수행하거나 데모를 수행합니다. 3200, 3300 및 3600과 같은 SAP 포트도 노출될 수 있는 경우 인터넷에 연결된 모든 데스크톱에서 SAP 응용 프로그램 인스턴스에 액세스할 수 있습니다.
* SAP 시스템(및 VM)은 최종 사용자 액세스를 위한 공용 인터넷 연결만 필요하고 Azure의 다른 VM에는 연결하지 않아도 되는 Azure의 독립 실행형 시나리오를 나타냅니다.
* SAPGUI 및 브라우저는 VM에서 직접 설치되고 실행됩니다.
* VM을 원래 상태로 빠르게 다시 복원해야 하고 해당 원래 상태의 새 배포가 필요합니다.
* 여러 VM에서 실현되는 데모 및 교육 시나리오의 경우 각 VM 집합에 Active Directory/OpenLDAP 및/또는 DNS 서비스가 필요합니다.


![Azure 클라우드 서비스의 단일 데모 또는 학습 시나리오를 나타내는 VM 그룹][planning-guide-figure-200]

각 집합의 VM은 동시에 배포해야 하며 각 집합의 VM 이름이 동일해야 합니다.

### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포
 
![사이트 간 연결이 있는 VPN(프레미스 간)][planning-guide-figure-300]

이 시나리오는 가능한 여러 배포 패턴을 포함하는 프레미스 간 시나리오입니다. 간단히 말해서 온-프레미스에서 SAP 지형의 일부를 실행하고 SAP 지형의 다른 부분을 Azure에서 실행하는 것으로 설명할 수 있습니다. SAP 구성 요소 중 일부가 Azure에서 실행된다는 사실의 모든 측면이 최종 사용자에게 투명하게 공개되어야 합니다. 따라서 SAP Transport Correction System(STMS), RFC 통신, 인쇄, 보안(예: SSO) 등은 Azure에서 실행되는 SAP 시스템에서 원활하게 작동합니다. 그러나 프레미스 간 시나리오도 고객의 도메인 및 DNS가 Azure로 확장된 상태로 Azure에서 전체 SAP 지형이 실행되는 시나리오를 설명합니다.

> [AZURE.NOTE] 이것은 생산성이 뛰어난 SAP 시스템을 실행하기 위해 지원되는 배포 시나리오입니다.

온-프레미스 네트워크를 Microsoft Azure에 연결하는 방법에 대한 자세한 내용은 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]를 읽으세요.

> [AZURE.IMPORTANT] Azure 및 온-프레미스 고객 배포 간의 프레미스 간 시나리오에 대해 논의할 경우 전체 SAP 시스템의 세분성을 고려하게 됩니다. 프레미스 간 시나리오에서 _지원되지 않는_ 시나리오는 다음과 같습니다.
> 
> * 배포 방법마다 SAP 응용 프로그램의 다른 계층을 실행합니다. 예를 들어 온-프레미스에서는 DBMS 계층을 실행하지만 Azure VM으로 배포된 VM에서는 SAP 응용 프로그램 계층을 실행하거나 그 반대로 실행할 수 있습니다.
> * SAP 계층 구성 요소 일부는 Azure에 있고, 일부는 온-프레미스에 있습니다. 예를 들어 온-프레미스와 Azure VM 간에 SAP 응용 프로그램 계층의 인스턴스를 분할합니다.
> * 단일 시스템의 SAP 인스턴스를 실행하는 VM을 여러 Azure 영역에 배포하는 것은 지원되지 않습니다.
> 
> 이러한 제한이 적용되는 이유는 단일 SAP 시스템 내에서, 특히 SAP 시스템의 응용 프로그램 인스턴스와 DBMS 계층 사이에서 대기 시간이 짧은 고성능 네트워크를 보장하기 위해서입니다.



### 지원되는 OS 및 데이터베이스 릴리스

* Azure 가상 컴퓨터 서비스에 대해 지원되는 Microsoft 서버 소프트웨어는 이 문서에 나열되어 있습니다. <http://support.microsoft.com/kb/2721672>
* 지원되는 운영 체제 릴리스, SAP 소프트웨어와 연계해서 Azure 가상 컴퓨터 서비스에서 지원되는 데이터베이스 시스템 릴리스는 SAP 정보 [1928533]에 설명되어 있습니다.
* Azure 가상 컴퓨터 서비스에서 지원되는 SAP 응용 프로그램 및 릴리스는 SAP 정보 [1928533]에 설명되어 있습니다.
* SAP용 Azure 시나리오에서는 64비트 이미지만 게스트 VM으로 실행될 수 있습니다. 즉, 64비트 SAP 응용 프로그램 및 데이터베이스만 지원됩니다.

## Microsoft Azure 가상 컴퓨터 서비스
Microsoft Azure Platform은 Microsoft 데이터 센터에서 호스트되고 작동되는 인터넷 규모 클라우드 서비스 플랫폼입니다. 이 플랫폼에는 Microsoft Azure 가상 컴퓨터 서비스(Infrastructure as a Service 또는 IaaS) 및 풍부한 PaaS(Platform as a Service) 기능 집합이 포함되어 있습니다.

Azure 플랫폼을 사용하면 사전에 기술 및 인프라를 구입할 필요가 줄어듭니다. 이 플랫폼은 웹 응용 프로그램 및 연결된 응용 프로그램을 호스트, 확장 및 관리하기 위한 주문형 컴퓨팅 및 저장소를 제공하여 응용 프로그램 유지 관리 및 운영을 간소화합니다. 인프라 관리는 사용 요구에 맞는 종량제 가격 모델 옵션을 사용하도록 하여 고가용성 및 동적 확장을 충족하도록 설계된 플랫폼으로 자동화됩니다.


 
![Microsoft Azure 가상 컴퓨터 서비스 위치 지정][planning-guide-figure-400]

Azure 가상 컴퓨터 서비스를 사용하여 사용자 지정 서버 이미지를 Azure에 IaaS 인스턴스로 배포할 수 있습니다(그림 4 참조). Azure의 가상 컴퓨터는 Hyper-V VHD(가상 하드 드라이브)를 기준으로 하며 다른 운영 체제를 게스트 OS로 실행할 수 있습니다.

운영 측면에서 Azure 가상 컴퓨터 서비스는 온-프레미스에 배포된 가상 컴퓨터와 유사한 환경을 제공합니다. 그러나 인프라를 조달, 운영 및 관리할 필요가 없다는 중요한 이점이 있습니다. 개발자와 관리자는 이러한 가상 컴퓨터 내의 운영 체제 이미지에 대해 모든 권한을 가집니다. 관리자는 이러한 가상 컴퓨터에 원격으로 로그온하여 소프트웨어 배포 작업 뿐만 아니라 유지 관리 및 문제 해결 작업을 수행할 수 있습니다. 배포 측면에서는 Azure VM의 크기 및 기능만 제한됩니다. 배포는 온-프레미스에서 수행될 수 있으므로 이러한 제한이 구성에서 그리 세밀한 부분이 아닐 수 있습니다. 다음을 조합한 다양한 VM 유형을 선택할 수 있습니다.

* vCPU 수
* 메모리
* 연결할 수 있는 VHD 수
* 네트워크 및 저장소 대역폭

제공되는 다양한 가상 컴퓨터의 크기 및 크기 제한은 [이 문서][virtual-machines-sizes]의 테이블에서 확인할 수 있습니다.

알게 되겠지만 다른 가상 컴퓨터 제품군 또는 시리즈가 있습니다. 2015년 12월을 기준으로 다음 VM 제품군을 구별할 수 있습니다.

* A0-A7 VM 유형: 모든 VM이 SAP용으로 인증된 것은 아닙니다. Azure IaaS가 도입된 첫 번째 VM 시리즈입니다.
* A8-A11 VM 유형: 고성능 컴퓨팅 인스턴스. 기타 A 시리즈 VM보다 더 나은 성능의 다른 컴퓨팅 호스트에서 실행됩니다.
* D 시리즈 VM 유형: A0-A7보다 더 나은 성능을 제공합니다. 모든 VM 유형이 SAP용으로 인증된 것은 아닙니다.
* DS 시리즈 VM 형식: D 시리즈와 동일한 호스트를 사용하지만 Azure 프리미엄 저장소에 연결할 수 있습니다(이 문서의 [Azure 프리미엄 저장소][planning-guide-3.3.2] 챕터 참조). 마찬가지로 모든 VM 유형이 SAP용으로 인증된 것은 아닙니다.
* G 시리즈 VM 유형: 높은 메모리 VM 유형입니다.
* GS 시리즈 VM 형식: G 시리즈와 같지만 Azure 프리미엄 저장소를 사용하기 위한 옵션이 포함되어 있습니다(이 문서의 [Azure 프리미엄 저장소][planning-guide-3.3.2] 챕터 참조). GS 시리즈 VM을 데이터베이스 서버로 사용할 경우 DB 데이터 및 트랜잭션 로그 파일을 위해 반드시 프리미엄 저장소를 사용해야 합니다.


다른 VM 시리즈에서 동일한 CPU 및 메모리 구성을 찾을 수도 있습니다. 그렇지만 다른 시리즈 중에서 이러한 VM의 처리량 성능을 조회해보면 크게 다르다는 것을 알 수 있습니다. CPU 및 메모리 구성이 같더라도 마찬가지입니다. 이유는 여러 VM 유형을 도입할 때 기본 호스트 서버 하드웨어가 다른 처리량 특성을 갖기 때문입니다. 일반적으로 처리량 성능에서 나타나는 차이가 다른 VM의 가격에서도 반영됩니다.

각 Azure 지역에 모두 다른 VM 시리즈가 제공되는 것은 아닐 수 잇습니다(Azure 지역에 대해서는 다음 장 참조). 또한 일부 VM 또는 VM 시리즈만 SAP용으로 인증되어 있습니다.

> [AZURE.IMPORTANT] SAP NetWeaver 기반 응용 프로그램을 사용하는 경우 SAP 정보 [1928533]에 나열된 VM 형식 및 구성 일부만 지원됩니다.

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure 지역
Microsoft는 소위 말하는 'Azure 영역'에 가상 컴퓨터를 배포할 수 있도록 합니다. Azure 지역은 지리적으로 근접한 하나 또는 여러 개의 데이터 센터일 수 있습니다. 전 세계의 지정학적 지역 대부분에 대해 Microsoft는 2개 이상의 Azure 지역을 유지합니다. 예를 들어 유럽의 경우 '북유럽' 및 '유럽 서부'라는 Azure 지역이 있습니다. 이러한 지정학적 지역 한 곳 내의 두 Azure 지역은 충분히 먼 거리로 구분되어 있으므로 자연 또는 기술적 재해가 같은 지정학적 지역의 두 Azure 지역에 영향을 주지 않습니다. Microsoft는 꾸준히 전 세계의 여러 다른 지정학적 지역에 새로운 Azure 지역을 구축하고 있으므로 이러한 지역의 수가 꾸준히 늘어나고 있으며 2015년 12월을 기준으로 이미 발표된 추가 지역을 포함하여 20개의 Azure 지역이 구축되었습니다. 고객은 중국의 두 Azure 지역을 포함하는 이러한 모든 지역에 SAP 시스템을 배포할 수 있습니다. Azure 지역에 대한 최신 정보를 보려면 이 웹 사이트를 참조하세요. <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure 가상 컴퓨터 개념
Microsoft Azure는 온-프레미스 가상화 솔루션과 비슷한 기능을 갖는 가상 컴퓨터를 호스트하기 위해 IaaS(Infrastructure as a Service) 솔루션을 제공합니다. Azure 포털, PowerShell 또는 CLI 내에서도 배포 및 관리 기능을 제공하는 가상 컴퓨터를 만들 수 있습니다.

Azure Resource Manager를 사용하면 선언적 템플릿을 통해 응용 프로그램을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 응용 프로그램 수명 주기의 각 단계 중에 응용 프로그램을 반복해서 배포합니다.

ARM 템플릿 사용에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

* [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리][virtual-machines-linux-cli-deploy-templates]
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

또 다른 흥미로운 기능은 가상 컴퓨터에서 이미지를 만들 수 있다는 것입니다. 이를 통해 요구 사항을 충족하는 가상 컴퓨터 인스턴스를 빠르게 배포할 수 있는 특정 리포지토리를 준비할 수 있습니다.

가상 컴퓨터에서 이미지를 만드는 방법에 대한 자세한 내용은 [이 문서(Windows)][virtual-machines-windows-capture-image] 또는 [(Linux)][virtual-machines-linux-capture-image]에서 찾을 수 있습니다.

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>장애 도메인
장애 도메인은 데이터 센터에 포함된 물리적 인프라와 매우 밀접하게 관련되어 있는 실패의 물리적 단위를 나타내며, 물리적 블레이드나 랙이 장애 도메인으로 간주될 수 있는 경우 둘 간의 직접적인 일대일 매핑은 없습니다.

여러 가상 컴퓨터를 Microsoft Azure 가상 컴퓨터 서비스에서 단일 SAP 시스템의 일부로 배포할 경우 Azure 패브릭 컨트롤러에 영향을 주어 응용 프로그램을 여러 다른 장애 도메인으로 배포할 수 있으므로 Microsoft Azure SLA의 요구 사항을 충족할 수 있습니다. 그러나 Azure 배율 단위(수백 개의 컴퓨터 노드 또는 저장소 노드 및 네트워킹 컬렉션)에 장애 도메인을 배포하거나 특정 장애 도메인에 VM을 할당하는 것은 직접 제어할 수 없는 영역입니다. Azure 패브릭 컨트롤러가 여러 다른 장애 도메인에 VM 집합을 배포하도록 하려면 배포 시에 Azure 가용성 집합을 하는 VM에 할당해야 합니다. Azure 가용성 집합에 대한 자세한 내용은 이 문서의 [Azure 가용성 집합][planning-guide-3.2.3] 챕터를 참조하세요.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>업그레이드 도메인
업그레이드 도메인은 여러 VM에서 실행되는 SAP 인스턴스를 구성하는 SAP 시스템 내의 VM이 업데이트되는 방식을 결정하는 데 도움이 되는 논리적 단위를 나타냅니다. 업그레이드가 수행될 때 Microsoft Azure는 이러한 업그레이드 도메인을 하나씩 업데이트합니다. 배포 시에 여러 업그레이드 도메인에 VM을 분산하여 잠재적인 가동 중지 시간으로부터 SAP 시스템을 일부 보호할 수 있습니다. 강제로 Azure가 여러 업그레이드 도메인에 SAP 시스템의 VM을 분산 배포하게 하려면 각 VM의 배포 시에 특정 특성을 설정해야 합니다. 장애 도메인과 마찬가지로 Azure 배율 단위는 여러 업그레이드 도메인으로 나뉩니다. Azure 패브릭 컨트롤러가 여러 다른 업그레이드 도메인에 VM 집합을 배포하도록 하려면 배포 시에 Azure 가용성 집합을 하는 VM에 할당해야 합니다. Azure 가용성 집합에 대한 자세한 내용은 아래의 [Azure 가용성 집합][planning-guide-3.2.3] 챕터를 참조하세요.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure 가용성 집합
단일 Azure 가용성 집합 내의 Azure 가상 컴퓨터는 Azure 패브릭 컨트롤러에 의해 여러 장애 도메인 및 업그레이드 도메인에 배포됩니다. 여러 장애 도메인과 업그레이드 도메인에 배포하는 목적은 단일 장애 도메인 내에서 인프라 유지 관리를 수행하거나 오류가 발생하는 경우 SAP 시스템의 모든 VM이 종료되지 않도록 하기 위해서입니다. 기본적으로 VM은 가용성 집합에 속하지 않습니다. 가용성 집합에 VM을 포함하는 작업은 배포 시에 또는 VM의 재구성이나 재배포에 의해 나중에 정의합니다.

Azure 가용성 집합의 개념 및 가용성 집합이 장애 및 업그레이드 도메인과 관련되는 방식을 이해하려면 [이 문서][virtual-machines-manage-availability]를 읽으세요.

json 템플릿을 통해 ARM에 대한 가용성 집합을 정의하려면 [rest-api 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json)을 참조하고 "가용성"을 검색합니다.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>저장소: Microsoft Azure Storage 및 데이터 디스크
Microsoft Azure 가상 컴퓨터는 다양한 저장소 유형을 활용합니다. Azure 가상 컴퓨터 서비스에서 SAP를 구현할 때는 다음 두 가지 주요 저장소 유형 간의 차이점을 이해하는 것이 중요합니다.

* 비영구 휘발성 저장소
* 영구 저장소

비영구 저장소는 실행 중인 가상 컴퓨터에 직접 연결되고 계산 노드 자체, 즉 로컬 인스턴스 저장소(임시 저장소)에 상주합니다. 크기는 배포 시작 시 선택한 가상 컴퓨터의 크기에 따라 달라집니다. 이 저장소 유형은 휘발성이므로 가상 컴퓨터 인스턴스를 다시 시작하면 디스크가 초기화됩니다. 일반적으로 운영 체제용 페이지 파일은 이 임시 디스크에 있습니다.

___

> ![Windows][Logo_Windows] Windows
>
> Windows VM에서 임시 드라이브는 배포된 VM의 D:\\ 드라이브로 탑재됩니다.
>
> ![Linux][Logo_Linux] Linux
> 
> Linux VM에서는 /mnt/resource 또는 /mnt로 탑재됩니다. 자세한 내용은 다음을 참조하세요.
> 
> * [Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx>

___

실제 드라이브는 호스트 서버 자체에 저장되므로 휘발성입니다. 재배포 시 VM이 이동되면(예: 호스트에 대한 유지 관리 또는 종료 및 다시 시작으로 인해) 드라이브의 내용이 유실됩니다. 따라서 이 드라이브에 중요한 데이터를 저장하는 것은 바람직하지 않습니다. 이러한 유형의 저장소에 사용되는 미디어 유형은 2015년 6월을 기준으로 다음과 같이 매우 다른 성능 특성을 나타내며 VM 시리즈 간에 차이를 보입니다.

* A5-A7: 매우 제한된 성능. 페이지 파일 이외의 다른 항목에는 권장되지 않습니다.
* A8-A11: 매우 양호한 성능(수 만 IOPS 및 1GB/초 이상의 처리량)
* D 시리즈: 매우 양호한 성능(수 만 IOPS 및 1GB/초 이상의 처리량)
* DS 시리즈: 매우 양호한 성능(수 만 IOPS 및 1GB/초 이상의 처리량)
* G 시리즈: 매우 양호한 성능(수 만 IOPS 및 1GB/초 이상의 처리량)
* GS 시리즈: 매우 양호한 성능(수 만 IOPS 및 1GB/초 이상의 처리량)

위의 문은 SAP에서 인증된 VM 형식에 적용됩니다. 일부 DBMS 기능에서 사용하는 데 필요한 뛰어난 IOPS 및 처리량을 제공하는 VM 시리즈입니다. 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요.

Microsoft Azure 저장소는 영구 저장소와 SAN 저장소에서 확인되는 일반적인 수준의 보호 및 중복성을 제공합니다. Azure 저장소를 기준으로 하는 디스크는 Azure 저장소 서비스에 있는 VHD(가상 하드 디스크)입니다. 로컬 OS 디스크(Windows C:\\, Linux / ( /dev/sda1 ))는 Azure 저장소에 저장되고, VM에 탑재된 추가 볼륨/디스크도 여기에 저장됩니다.

온-프레미스에서 기존 VHD를 업로드하거나 Azure 내에서 빈 VHD를 만든 후에 배포된 VM에 연결할 수 있습니다. 이러한 VHD는 Azure 디스크로 참조됩니다.

VHD를 만들거나 Azure 저장소에 업로드한 후 기존 가상 컴퓨터에 탑재하고 연결하고 기존(분리된) VHD를 복사할 수 있습니다.

해당 VHD는 지속되므로 해당 VHD 내의 데이터 및 변경 내용은 재부팅한 후 가상 컴퓨터 인스턴스를 다시 만들어도 안전하게 유지됩니다. 인스턴스를 삭제하는 경우에도 이러한 VHD가 안전하게 유지되며 재배포될 수 있으며, OS가 아닌 디스크의 경우에도 다른 VM에 탑재할 수 있습니다.

Azure 저장소의 네트워크 내에서 다음과 같은 여러 다른 중복 수준을 구성할 수 있습니다.

* 선택할 수 있는 최소 수준은 '로컬 중복성'으로, Azure 지역의 동일한 데이터 센터 내에 데이터의 3개 복제본이 있는 것과 같습니다([Azure 지역][planning-guide-3.1] 챕터 참조).
* 동일한 Azure 지역 내의 여러 다른 데이터 센터로 3개의 이미지를 분산하는 영역 중복 저장소.
* 기본 중복성 수준은 콘텐츠를 데이터의 다른 3개 이미지 형태로, 동일한 지정학적 지역에 호스트된 다른 Azure 지역에 비동기식으로 복제하는 지리적 중복성입니다.

또한 다른 중복성 옵션에 대해서는 이 문서의 맨 위에 있는 테이블을 참조하세요. <https://azure.microsoft.com/pricing/details/storage/>

Azure 저장소에 관한 자세한 내용은 다음에서 찾을 수 있습니다.

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>


#### Azure 표준 저장소
Azure 표준 BLOB 저장소는 Azure IaaS가 출시되었을 때 사용할 수 있던 저장소의 유형입니다. IOPS 할당량이 단일 VHD 단위로 적용되었습니다. 대기 시간도 온-프레미스에 호스트된 고성능 SAP 시스템에 대해 일반적으로 배포되는 SAN/NAS 장치와 동일한 수준이 아니었습니다. 그럼에도 불구하고 Azure 표준 저장소는 Azure에 배포되는 수백 가지 SAP 시스템에 충분한 것으로 입증되었습니다.

표준 Azure 저장소는 저장된 실제 데이터, 저장소 트랜잭션 볼륨, 아웃바운드 데이터 전송 및 선택한 중복성 옵션에 따라 비용이 청구됩니다. 많은 VHD를 최대 1TB의 크기로 만들 수 있지만 비어 있는 경우에는 무료입니다. VHD를 각각 100GB로 채우면 VHD가 만들어질 때의 보통 크기가 아니라 100GB를 저장하기 위한 비용이 청구됩니다.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure 프리미엄 저장소
2015년 4월 Microsoft는 Azure 프리미엄 저장소를 도입했습니다. 프리미엄 저장소는 다음을 제공하는 것을 목표로 도입되었습니다.

* 더 나은 I/O 대기 시간
* 향상된 처리량
* 변동이 작은 I/O 대기 시간

이를 위해 여러 가지 중에서 가장 중요한 다음 두 가지 변경이 적용되었습니다.

* Azure 저장소 노드에 SSD 디스크 사용
* Azure 계산 노드의 로컬 SSD에서 지원하는 새 캐시 읽기

해당 기능이 디스크(또는 VHD)의 크기에 따라 달라지지 않는 표준 저장소와 달리, 프리미엄 저장소는 이 문서 끝의 FAQ 섹션 이전에 제공되는 3가지 다른 디스크 범주로 구성됩니다. <https://azure.microsoft.com/pricing/details/storage/>

IOPS/VHD 및 디스크 처리량/VHD는 디스크의 크기 범주에 따라 달라집니다.

프리미엄 저장소의 경우 이러한 VHD에 저장된 실제 데이터 볼륨이 아니라 VHD 내에 저장된 데이터 양과는 관계없는 이러한 VHD의 크기 범주에 따라 비용이 부과됩니다.

표시된 크기 범주에 직접 매핑되지 않는 VHD를 프리미엄 저장소에 만들 수도 있습니다. 표준 저장소의 VHD를 프리미엄 저장소에 복사하는 경우가 여기에 해당됩니다. 이러한 경우에는 다음으로 가장 큰 프리미엄 저장소 디스크 옵션으로 매핑됩니다.

특정 VM 시리즈만 Azure 프리미엄 저장소에서 이점을 얻을 수 있습니다. 2015년 12월을 기준으로 DS 및 GS 시리즈가 여기에 해당합니다. 기본적으로 DS 시리즈는 Azure 표준 저장소에 호스트된 VHD에 추가적으로 프리미엄 저장소 기반 VM을 탑재할 수 있다는 점을 제외하고 D 시리즈와 같습니다. GS 시리즈와 G 시리즈를 비교할 때도 마찬가지입니다.

[이 문서][virtual-machines-sizes]에서 DS 시리즈 VM의 해당 부분을 확인하게 되면 프리미엄 저장소 VHD에 VM 세분성 수준에 따른 데이터 볼륨 제한이 있다는 것을 알 수 있습니다. 다른 DS 시리즈 또는 GS 시리즈 VM에도 탑재할 수 있는 VHD 수와 관련해서 다른 제한 사항이 있습니다. 이러한 제한도 위에 언급된 문서에 설명되어 있습니다. 하지만 기본적으로 P30 디스크/VHD 32개를 단일 DS14 VM에 탑재한다고 해도 P30 디스크 최대 처리량의 32배를 얻을 수는 없습니다. 대신 이 문서에 설명된 것처럼 VM 수준의 최대 처리량에 따라 데이터 처리량이 제한됩니다.

프리미엄 저장소에 대한 자세한 내용은 여기에서 확인할 수 있습니다. <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### Azure 저장소 계정
Azure에서 서비스 또는 VM을 배포할 때 VHD 및 VM 이미지의 배포는 Azure 저장소 계정이라는 단위로 구성해야 합니다. Azure 배포를 계획할 때는 Azure의 제한 사항을 신중히 고려해야 합니다. 한쪽 측면에서는 Azure 구독당 저장소 계정 수가 제한되어 있습니다. 각 Azure 저장소 계정은 많은 수의 VHD 파일을 보유할 수 있지만 저장소 계정당 총 IOPS 수의 한도는 고정되어 있습니다. DBMS 시스템에 수백 개의 SAP VM을 배포하여 많은 I/O 호출을 발생하는 경우 여러 Azure 저장소 계정 간에 높은 IOPS DBMS VM을 배포하는 것이 좋습니다. 구독당 적용되는 Azure 저장소 계정의 현재 제한을 초과하지 않도록 주의해야 합니다. 저장소는 SAP 시스템의 데이터베이스 배포에서 필수적인 부분이므로 이 개념이 앞서 언급된 [DBMS 배포 가이드][dbms-guide]에는 좀 더 자세히 설명되어 있습니다.

Azure 저장소 계정에 대한 자세한 내용은 [이 문서][storage-scalability-targets]에서 찾을 수 있습니다. 이 문서를 읽으면 Azure 표준 저장소 계정과 프리미엄 저장소 계정에서 제한 사항이 서로 다르다는 것을 알게 될 것입니다. 주요 차이점은 이러한 저장소 계정 내에 저장할 수 있는 데이터의 볼륨입니다. 표준 저장소에서 이 볼륨은 프리미엄 저장소를 사용할 때보다 훨씬 더 큽니다. 그 밖에도 표준 저장소 계정은 IOPS에서 크게 제한되지만('총 요청 속도’ 열 참조) Azure 프리미엄 저장소 계정에는 이러한 제한이 없습니다. SAP 시스템, 특히 DBMS 서버의 배포를 설명할 때 이러한 차이점의 세부 정보 및 결과를 살펴보겠습니다.

저장소 계정 내에서는 여러 다른 VHD를 구성하고 분류하기 위해 다른 컨테이너를 만들 수 있습니다. 이러한 컨테이너는 일반적으로 다른 VM의 VHD를 분리하는 데 사용됩니다. 단일 Azure 저장소 계정 아래에서 컨테이너를 하나만 사용하거나 여러 개를 사용할 경우 성능상의 문제는 없습니다.

Azure 내에서 VHD 이름은 Azure 내에서 VHD에 대해 고유한 이름을 제공해야 하는 다음과 같은 명명 관계를 따릅니다.

	http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

언급한 것처럼 위의 문자열은 Azure 저장소에 저장된 VHD를 고유하게 식별해야 합니다.

### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure 네트워킹
Microsoft Azure는 구현하려는 모든 시나리오의 SAP 소프트웨어 매핑을 가능하게 하는 네트워크 인프라를 제공합니다. 해당 기능은 다음과 같습니다.

* 외부에서 Windows 터미널 서비스 또는 ssh/VNC를 통해 VM에 직접 액세스
* VM 내의 응용 프로그램에서 사용되는 서비스 및 특정 포트에 액세스
* Azure VM으로 배포된 VM 그룹 간의 내부 통신 및 이름 확인
* 고객의 온-프레미스 네트워크와 Azure 네트워크 간의 프레미스 간 연결
* Azure 지역 간 연결 또는 Azure 사이트 간 데이터 센터 연결

자세한 내용은 여기 <https://azure.microsoft.com/documentation/services/virtual-network/>를 참조하세요.

Azure에서 다양한 방식으로 이름 및 IP 확인을 구성할 수 있습니다. 이 문서에서 클라우드 전용 시나리오는 Azure DNS를 사용하는 기본 방식에 의존합니다(자체 DNS 서비스를 정의하는 방식 아님). 사용자 고유의 DNS 서버를 설정하는 대신 사용할 수 있는 새 Azure DNS 서비스도 있습니다. 자세한 내용은 [이 문서][virtual-networks-manage-dns-in-vnet] 및 [이 페이지](https://azure.microsoft.com/services/dns/)에서 찾을 수 있습니다.

프레미스 간 시나리오의 경우 온-프레미스 AD/OpenLDAP/DNS를 VPN 또는 개인 연결을 통해 Azure로 확장했다는 사실에 의존합니다. 여기에 설명된 특정 시나리오에서는 Azure에 AD/OpenLDAP 복제본을 설치해야 할 수 있습니다.

네트워킹 및 이름 확인은 SAP 시스템의 데이터베이스 배포에서 필수적인 부분이므로 이 개념이 [DBMS 배포 가이드][dbms-guide]에 좀 더 자세히 설명되어 있습니다.


##### Azure 가상 네트워크

Azure 가상 네트워크를 구축하여 Azure DHCP 기능에 의해 할당된 개인 IP 주소의 주소 범위를 정의할 수 있습니다. 프레미스 간 시나리오에서 정의된 IP 주소 범위는 여전히 DHCP를 사용하여 Azure에서 할당됩니다. 그러나 도메인 이름 확인은 온-프레미스에서 수행되므로(VM을 온-프레미스 도메인의 일부로 가정) 다른 Azure 클라우드 서비스 이외의 주소를 확인할 수 있습니다.

[comment]: <> (MSSedusch still needed? TODO Originally an Azure Virtual Network was bound to an Affinity Group. With that a Virtual Network in Azure got restricted to the Azure Scale Unit that the Affinity Group got assigned to. In the end, this meant the Virtual Network was restricted to the resources available in the Azure Scale Unit. This has since changed and now Azure Virtual Networks can stretch across more than one Azure Scale Unit. 그렇지만 Azure 가상 네트워크는 생성 시에 더 이상 선호도 그룹과 연결되지 **않아야 합니다**. 1년 전에는 권장 사항과 반대되는 지침을 제공했으나 **더 이상 Azure 선호도 그룹을 사용하지 않아야 합니다**. 자세한 내용은 <https://azure.microsoft.com/blog/regional-virtual-networks/>를 참조하세요.)

Azure의 모든 가상 컴퓨터를 가상 네트워크에 연결해야 합니다.

자세한 내용은 [이 문서][resource-groups-networking] 및 [이 페이지](https://azure.microsoft.com/documentation/services/virtual-network/)에서 찾을 수 있습니다.

[comment]: <> (MShermannd TODO Couldn't find an article which includes the OpenLDAP topic + ARM; ) 
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [AZURE.NOTE] 기본적으로 VM이 배포되면 가상 네트워크 구성을 변경할 수 없습니다. TCP/IP 설정은 Azure DHCP 서버에 유지되어야 합니다. 기본 동작은 동적 IP 할당입니다.

가상 네트워크 카드의 MAC 주소는 변경될 수 있으며(예: 크기 조정) 이 경우 Windows 또는 Linux 게스트 OS는 새 네트워크 카드를 선택한 후 자동으로 DHCP를 사용하여 IP 및 DNS 주소를 할당합니다.

##### 고정 IP 할당
Azure 가상 네트워크 내의 VM에 고정 또는 예약된 IP 주소를 할당할 수 있습니다. Azure 가상 네트워크의 VM을 실행하면 일부 시나리오에 필요한 경우 이 기능이 사용될 확률이 높아집니다. IP 할당은 VM이 실행 중인지 또는 종료되었는지에 관계없이 VM이 존재하는 동안 계속 유효합니다. 결과적으로 가상 네트워크의 IP 주소 범위를 정의할 때 VM의 전체 수(실행 중인 VM 및 중지된 VM)를 고려해야 합니다. IP 주소는 VM 및 해당 네트워크 인터페이스가 삭제되거나 IP 주소가 다시 할당 취소될 때까지 할당된 상태로 유지됩니다. [이 문서][virtual-networks-static-private-ip-arm-pportal]에서 자세한 내용을 참조하세요.

##### VM당 여러 NIC
Azure 가상 컴퓨터에 대해 여러 개의 vNIC(가상 네트워크 인터페이스 카드)를 정의할 수 있습니다. 여러 vNIC를 정의할 수 있으면 클라이언트 트래픽이 한 vNIC를 통해 라우팅되고 백 엔드 트래픽이 또 다른 vNIC를 통해 라우팅되는 네트워크 트래픽 분리를 설정할 수 있습니다. VM의 유형에 따라, vNIC의 수와 관련해서 다른 제한이 적용될 수 있습니다. 정확한 세부 정보, 기능 및 제한 사항은 다음 문서에서 찾을 수 있습니다.
 
* [여러 NIC를 사용하여 VM 만들기][virtual-networks-multiple-nics]
* [템플릿을 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-template]
* [PowerShell을 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-ps]
* [Azure CLI를 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-cli]

#### 사이트 간 연결
프레미스 간에서는 투명한 영구 VPN 연결을 사용하여 Azure VM과 온-프레미스가 연결되어 있습니다. 이것이 Azure에서 가장 일반적인 SAP 배포 패턴이 될 것입니다. 그렇지만 Azure의 SAP 인스턴스에 대한 운영 절차 및 프로세스가 투명하게 진행되어야 합니다. 즉, 이러한 시스템에서 결과를 출력하고 SAP TMS(전송 관리 시스템)를 사용하여 Azure의 개발 시스템 변경 내용을 온-프레미스에 배포되는 테스트 시스템으로 전송할 수 있어야 합니다. 사이트 간 연결에 대한 추가 설명서는 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]에서 찾을 수 있습니다.

##### VPN 터널 장치
사이트 간 연결(온-프레미스 데이터에서 Azure 데이터 센터로의 연결)을 만들려면 VPN 장치를 얻은 후 구성하거나 Windows Server 2012에 소프트웨어 구성 요소로 도입된 RRAS(라우팅 및 원격 액세스 서비스)를 사용해야 합니다.

* [PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기][vpn-gateway-create-site-to-site-rm-powershell]
* [사이트 간 VPN 게이트웨이 연결에 대한 VPN 장치 정보][vpn-gateway-about-vpn-devices]
* [VPN 게이트웨이 FAQ][vpn-gateway-vpn-faq]

![온-프레미스와 Azure 간의 사이트 간 연결][planning-guide-figure-600]

위의 그림에서는 Azure의 가상 네트워크에서 사용하도록 예약된 IP 주소 하위 범위가 두 개의 Azure 구독에 있는 경우를 보여 줍니다. 온-프레미스 네트워크에서 Azure로의 연결은 VPN을 통해 설정됩니다.

#### 지점 및 사이트 간 VPN
지점 및 사이트 간 VPN에서는 모든 클라이언트 컴퓨터가 자체 VPN을 통해 Azure에 연결되어야 합니다. 우리가 보고 있는 SAP 시나리오의 경우 지점 및 사이트 간 연결이 가능하지 않습니다. 따라서 지점 및 사이트 간 VPN 연결에 대한 추가 참조는 제공되지 않습니다.

[comment]: <> (MSSedusch -- More information can be found here) 
[comment]: <> (MShermannd TODO Link no longer valid; But ARM is anyway not supported - see next link below)
[comment]: <> (MSSedusch -- <http://msdn.microsoft.com/library/azure/dn133798.aspx>.) 
[comment]: <> (MShermannd TODO Point to Site not supported yet with ARM ) 
[comment]: <> (MSSedusch -- <https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### 다중 사이트 VPN
현재 Azure는 단일 Azure 구독에 대해 다중 사이트 VPN 연결을 만들 수 있도록 합니다. 이전에는 구독이 1개 있으면 사이트 간 VPN 연결 1개로 제한되었습니다. 이 제한은 단일 구독에 대한 다중 사이트 VPN 연결을 통해 해결되었습니다. 따라서 프레미스 간 구성을 통해 특정 구독에 대해 둘 이상의 Azure 지역을 사용할 수 있습니다.

추가 설명서를 보려면 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]를 참조하세요. 
[comment]: <> (MShermannd TODO found no ARM docu link)

#### VNet 간 연결
다중 사이트 VPN을 사용할 경우 각 지역에 별도의 Azure 가상 네트워크를 구성해야 합니다. 그러나 다른 지역의 소프트웨어 구성 요소가 서로 통신해야 하는 경우가 상당히 많습니다. 이상적으로 이러한 통신은 한 Azure 지역에서 온-프레미스로 라우팅되었다가 다시 여기서 다른 Azure 지역으로 라우팅되면 안 됩니다. 간단히 말해서 Azure는 한 지역의 Azure 가상 네트워크에서 다른 지역의 다른 Azure 가상 네트워크로의 연결을 구성할 수 있도록 합니다. 이 기능을 VNet 간 연결이라고 합니다. 이 기능에 대한 자세한 내용은 여기에서 찾을 수 있습니다. <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>

#### Azure에 대한 개인 연결 - Express 경로
Microsoft Azure Express 경로를 사용하여 Azure 데이터 센터와 고객의 온-프레미스 인프라 또는 공동 배치 환경 간에 개인 연결을 만들 수 있습니다. 다양한 MPLS(패킷 전환) VPN 공급자 또는 기타 네트워크 서비스 공급자에서 Express 경로를 제공합니다. Express 경로 연결은 공용 인터넷을 통해 이동하지 않습니다. Express 경로 연결은 인터넷을 통한 일반 연결보다 더 높은 보안을 제공하고 여러 병렬 회로를 통해 안정성을 높이며 더 빠른 속도와 짧은 대기 시간을 제공합니다.

Azure Express 경로 및 제공 서비스에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express 경로는 여기에 설명된 것처럼 단일 Express 경로 회로를 통해 여러 Azure 구독을 가능하게 합니다.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>


#### 프레미스 간 강제 터널링
사이트 간, 지점 및 사이트 간 또는 Express 경로를 통해 온-프레미스 도메인에 가입된 VM의 경우 모든 사용자를 위해 인터넷 프록시 설정이 배포되는지 확인해야 합니다. 기본적으로 해당 VM에서 실행되는 소프트웨어나 브라우저를 사용하여 인터넷에 액세스하는 사용자는 회사 프록시를 거치지 않으며 Azure를 통해 인터넷에 바로 연결됩니다. 하지만 프록시를 확인하는 것은 소프트웨어 및 서비스의 책임이므로 프록시 설정만으로 트래픽이 회사 프록시를 통과해서 전송되도록 할 수 있는 것은 아닙니다. VM에서 실행되는 소프트웨어가 이러한 작업을 수행하지 않으며 관리자가 설정을 조작하므로 인터넷에 대한 트래픽은 다시 Azure에서 인터넷으로 직접 우회될 수 있습니다.

이 문제를 방지하기 위해 온-프레미스와 Azure 사이에 사이트 간 연결이 사용되는 강제 터널링을 구성할 수 있습니다. 강제 터널링 기능에 대한 자세한 설명은 여기에 게시되어 있습니다. <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Express 경로를 사용한 강제 터널링은 Express 경로 BGP 피어링 세션을 통해 기본 경로를 보급한 고객으로 활성화됩니다.

#### Azure 네트워킹 요약
이 장에서는 Azure 네트워킹에 대한 여러 가지 중요 사항이 포함되어 있습니다. 중요 사항은 다음과 같이 요약할 수 있습니다.


* Azure 가상 네트워크는 사용자의 요구에 따라 네트워크를 설정하도록 허용
* Azure 가상 네트워크를 활용하여 VM에 IP 주소 범위를 할당하거나 VM에 고정 IP 주소 할당 가능
* 사이트 간 또는 지점 및 사이트 간 연결을 설정하려면 먼저 Azure 가상 네트워크를 만들어야 함
* 가상 컴퓨터를 배포한 후에는 VM에 할당된 가상 네트워크를 더 이상 변경할 수 없음

### Azure 가상 컴퓨터 서비스의 할당량
저장소 및 네트워크 인프라가 Azure 인프라에서 다양한 서비스를 실행하는 VM 간에 공유된다는 사실을 명확히할 필요가 있습니다. 또한 고객 소유의 데이터 센터와 마찬가지로 일부 인프라 리소스가 과도하게 프로비전되는 경우도 발생하고 있습니다. Microsoft Azure Platform은 디스크, CPU, 네트워크 및 기타 할당량을 사용하여 리소스 소비를 제한하며 일관되고 명확한 성능을 유지합니다. VM 유형(A5, A6, 등)마다 디스크 수, CPU, RAM, 네트워크에 대해 각기 다른 할당량을 갖습니다.

> [AZURE.NOTE] SAP에서 지원하는 VM 유형의 CPU 및 메모리 리소스는 호스트 노드에서 미리 할당됩니다. 즉, VM을 배포한 후 VM 유형에 따라 정의된 대로 호스트의 리소스를 사용할 수 있게 됩니다.

Azure 솔루션에 대해 SAP를 계획하고 크기를 조정할 때는 각 가상 컴퓨터 크기에 대한 할당량을 고려해야 합니다. VM 할당량은 [여기][virtual-machines-sizes]에 설명되어 있습니다.

설명된 할당량은 이론적인 최대값을 나타냅니다. VHD당 IOPS 제한은 작은 IO(8kb)로는 충족되지만 큰 IO(1Mb)로는 충족되지 않을 수 있습니다. IOPS 제한은 단일 VHD의 세분성에 적용됩니다.

SAP 시스템이 Azure 가상 컴퓨터 서비스 및 해당 기능에 적합한지 여부 또는 기존 시스템을 Azure에 배포하기 위해 다르게 구성해야 하는지 여부를 결정하기 위한 대략적인 의사 결정 트리로서, 아래의 의사 결정 트리를 사용할 수 있습니다.
 
![Azure에서 SAP를 배포하는 기능을 결정하는 의사 결정 트리][planning-guide-figure-700]

**1단계**: 시작하기 위한 가장 중요한 정보는 지정된 SAP 시스템의 SAPS 요구 사항입니다. SAP 시스템이 이미 2계층 구성으로 온-프레미스에 배포되더라도 SAPS 요구 사항을 DBMS 부분과 SAP 응용 프로그램 부분으로 구분해야 합니다. 기존 시스템의 경우 사용 중인 하드웨어 관련된 SAPS를 기존 SAP 벤치마크를 기준으로 결정하거나 예측할 수 있습니다. 해당 결과는 여기에서 확인할 수 있습니다. <http://global.sap.com/campaigns/benchmark/index.epx> 새로 배포된 SAP 시스템의 경우 시스템의 SAPS 요구 사항을 결정해야 하는 크기 조정 연습 과정을 거쳐야 합니다. Azure의 SAP 크기 조정에 대해서는 이 블로그 및 첨부된 문서를 참조하세요. <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2단계**: 기존 시스템의 경우 DBMS 서버의 I/O 볼륨 및 초당 I/O 작업 수를 측정해야 합니다. 새로 예정된 시스템의 경우는 새 시스템에 대한 크기 조정 연습 과정을 진행하면서 DBMS에 대한 I/O 요구 사항을 대략적으로 알 수 있습니다. 확실하지 않은 경우 결국 개념 증명을 수행해야 합니다.

**3단계**: DBMS 서버의 SAPS 요구 사항과 여러 다른 VM 형식의 Azure가 제공할 수 있는 SAPS를 비교합니다. 다른 Azure VM 형식의 SAPS 관련 정보는 SAP 정보 [1928533]에 나와 있습니다. 데이터베이스 계층이 대부분의 배포에서는 확장되지 않는 SAP NetWeaver 시스템의 계층이므로 먼저 DBMS VM을 중점적으로 설명합니다. 반면 SAP 응용 프로그램 계층은 확장될 수 있습니다. SAP 지원 Azure VM 유형 중 어떤 유형도 필요한 SAPS를 제공할 수 없는 경우 계획된 SAP 시스템의 작업을 Azure에서 실행할 수 없습니다. 따라서 온-프레미스에 시스템을 배포해야 하거나 시스템에 대한 작업 볼륨을 변경해야 합니다.

**4단계**: [여기][virtual-machines-sizes]에 설명된 대로 Azure는 사용자가 표준 저장소를 사용하든 프리미엄 저장소를 사용하든지에 관계없이 VHD당 IOPS 할당량을 적용합니다. VM 유형에 따라 탑재 가능한 VHD 수가 달라집니다. 따라서 각 VM 유형에서 얻을 수 있는 최대 IOPS 수를 계산할 수 있습니다. 데이터베이스 파일 레이아웃에 따라 게스트 OS에서 단일 볼륨이 되도록 VHD를 스트라이프할 수 있습니다. 그러나 배포된 SAP 시스템의 현재 IOPS 볼륨이 Azure의 가장 큰 VM 유형에 대해 계산된 제한을 초과하고 더 많은 메모리로 보상할 수 없으면 SAP 시스템의 작업은 심각한 영향을 받을 수 있습니다. 이러한 경우 Azure에 시스템을 배포하지 않아야 하는 상황이 올 수 있습니다.

**5단계**: 특히 2계층 구성의 온-프레미스에 배포된 SAP 시스템을 3계층 구성의 Azure에서 구성해야 할 가능성이 있습니다. 이 단계에서는 SAP 응용 프로그램 계층에 확장할 수 없고 다른 Azure VM 유형에서 제공하는 CPU 및 메모리 리소스에 적합하지 않은 구성 요소가 있는지 여부를 확인해야 합니다. 실제로 이러한 구성 요소가 있는 경우 SAP 시스템과 해당 작업을 Azure에 배포할 수 없습니다. 하지만 SAP 응용 프로그램 구성 요소를 여러 Azure VM으로 확장할 수 있으면 시스템을 Azure에 배포할 수 있습니다.

**6단계**: DBMS 및 SAP 응용 프로그램 계층 구성 요소를 Azure VM에서 실행할 수 있으면 다음과 관련해서 구성을 정의해야 합니다.

* Azure VM의 수
* 개별 구성 요소에 대한 VM 유형
* 충분한 IOPS를 제공하기 위한 DBMS VM의 VHD 수

## Azure 자산 관리

### Azure 포털
Azure 포털은 Azure VM 배포를 관리하기 위한 세 가지 인터페이스 중 하나입니다. 이미지에서 VM을 배포하는 등의 기본적인 관리 작업은 Azure 포털을 통해 수행할 수 있습니다. 또한 저장소 계정, 가상 네트워크 및 기타 Azure 구성 요소 만들기 작업은 Azure 포털에서 매우 효율적으로 처리할 수 있는 작업이기도 합니다. 그러나 온-프레미스에서 Azure로 VHD를 업로드하거나, Azure 내에서 VHD를 복사하는 등의 기능을 수행하려면 타사 도구나 PowerShell 또는 CLI를 통한 관리가 필요합니다.
 
![Microsoft Azure 포털 - 가상 컴퓨터 개요][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>) 
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

가상 컴퓨터 인스턴스에 대한 관리 및 구성 작업은 Azure 포털 내에서 수행할 수 있습니다.

가상 컴퓨터를 다시 시작하고 종료하는 것 외에 가상 컴퓨터 인스턴스용 데이터 디스크를 연결, 분리 및 생성하여 이미지 준비를 위해 인스턴스를 캡처하고 가상 컴퓨터 인스턴스의 크기를 구성할 수도 있습니다.

Azure 포털은 VM 및 기타 여러 Azure 서비스를 배포 및 구성하기 위한 기본 기능을 제공합니다. 그러나 Azure 포털에서 모든 기능을 제공하는 것은 아닙니다. Azure 포털에서는 다음과 같은 작업을 수행할 수 없습니다.

* Azure에 VHD 업로드
* VM 복사

[comment]: <> (MShermannd TODO what about automation service for SAP VMs ? ) 
[comment]: <> (MSSedusch deployment of multiple VMs os meanwhile possible)
[comment]: <> (MSSedusch Also any type of automation regarding deployment is not possible with the Azure portal. Tasks such as scripted deployment of multiple VMs is not possible via the Azure Portal.)

### Microsoft Azure PowerShell cmdlet을 통한 관리
Windows PowerShell은 Azure에서 많은 수의 시스템을 배포하는 고객에 의해 널리 채택되어온 강력하고 확장 가능한 프레임워크입니다. 데스크톱, 랩톱 또는 전용 관리 스테이션에 PowerShell cmdlet을 설치한 후 원격으로 실행할 수 있습니다.

Azure PowerShell cmdlet을 사용할 수 있게 로컬 데스크톱/랩톱을 사용하도록 설정하는 프로세스와 Azure 구독에 따라 사용하도록 구성하는 방법은 [이 문서][powershell-install-configure]에 설명되어 있습니다.

Azure PowerShell cmdlet을 설치, 업데이트 및 구성하는 방법에 대한 자세한 단계는 [배포 가이드의 이 챕터][deployment-guide-4.1]에서도 확인할 수 있습니다.

지금까지 고객이 경험한 바에 따르면 PS(PowerShell)는 분명히 VM을 배포하고 VM의 배포에서 사용자 지정 단계를 만들 수 있는 더 강력한 도구입니다. Azure에서 SAP 인스턴스를 실행하는 모든 고객은 PS cmdlet을 사용하여 Azure 포털에서 수행하는 관리 작업을 보완하거나 PS cmdlet만 사용해서 Azure의 배포를 관리하고 있습니다. Azure 관련 cmdlet은 2,000개가 넘는 Windows 관련 cmdlet과 동일한 명명 규칙을 공유하기 때문에 Windows 관리자가 이러한 cmdlet을 활용하는 것은 쉽습니다.

여기에서 예제를 참조하세요. <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO describe new CLI command when tested ) 
SAP용 Azure 모니터링 확장의 배포(이 문서의 [SAP용 Azure 모니터링 솔루션][planning-guide-9.1] 챕터 참조)는 PowerShell 또는 CLI를 통해서만 가능합니다. 따라서 Azure에서 SAP NetWeaver 시스템을 배포 또는 관리하는 경우 PowerShell 또는 CLI를 반드시 설치하고 구성해야 합니다.

Azure에서는 더 많은 기능을 제공하므로 cmdlet 업데이트를 요구하는 새로운 PS cmdlet이 추가될 예정입니다. 따라서 매월 한 번 이상 Azure 다운로드 사이트 <https://azure.microsoft.com/downloads/>를 확인하여 새 버전의 cmdlet이 있는지 확인하는 것이 좋습니다. 새 버전은 이전 버전 위에 설치됩니다.

Azure 관련 PowerShell 명령의 일반적인 목록은 여기에서 확인하세요. <https://msdn.microsoft.com/library/azure/dn708514.aspx>

### Microsoft Azure CLI 명령을 통한 관리

Linux를 사용하고 Azure 리소스를 관리하려는 고객의 경우에는 Powershell을 사용하지 못할 수 있습니다. Microsoft는 대안으로 Azure CLI를 제공합니다. Azure CLI는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. Azure CLI는 Azure 포털에 있는 동일한 기능을 대부분 제공합니다.

설치 및 구성에 대한 내용과 CLI 명령을 사용하여 Azure 작업을 수행하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CLI 설치][xplat-cli]
* [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리][virtual-machines-linux-cli-deploy-templates]
* [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용][xplat-cli-azure-resource-manager]

또한 [배포 가이드][planning-guide]의 [Linux VM용 Azure CLI][deployment-guide-4.5.2] 챕터에서 Azure CLI를 사용하여 SAP용 Azure 모니터링 확장을 배포하는 방법을 읽어보세요.

## Azure에서 SAP용 VM을 배포하는 다른 방법
이 장에서는 Azure에서 VM을 배포하는 다양한 방법을 알아봅니다. 이 장에서는 Azure의 VM 및 VHD의 처리뿐만 아니라 추가 준비 절차도 설명합니다.

### SAP용 VM 배포
Microsoft Azure는 VM 및 관련 디스크를 배포하기 위한 여러 가지 방법을 제공합니다. 따라서 배포 방법에 따라 VM 준비가 달라질 수 있으므로 차이점을 이해해야 합니다. 일반적으로 다음과 같은 시나리오를 살펴보겠습니다.

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동
온-프레미스에서 Azure로 특정 SAP 시스템을 이동하려고 합니다. 이 작업은 OS, SAP 바이너리 및 DBMS 바이너리를 포함하는 VHD와 DBMS 데이터와 로그 파일이 있는 VHD를 Azure에 업로드하여 수행할 수 있습니다. [아래의 시나리오 2][planning-guide-5.1.2]와 달리 Azure VM에서 호스트 이름, SAP SID 및 SAP 사용자 계정을 온-프레미스 환경에서 구성된 대로 유지합니다. 그러므로 이미지 일반화는 필요하지 않습니다. 일반화되지 않은 VM 또는 VHD를 온-프레미스에서 준비하고 Azure로 업로드하는 방법에 대해서는 [일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비][planning-guide-5.2.1] 챕터를 참조하세요. Azure에서 이러한 이미지를 배포하는 자세한 단계에 대해서는 [배포 가이드][deployment-guide]의 [시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동][deployment-guide-3.4] 챕터를 읽어보세요.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>고객별 이미지를 사용하여 VM 배포
OS 또는 DBMS 버전의 특정 패치 요구 사항으로 인해 Azure 마켓플레이스에 제공된 이미지가 용도에 맞지 않을 수 있습니다. 따라서 '개인' OS/DBMS VM 이미지를 사용하는 VM을 만들어 이후 여러 번 배포할 수 있습니다. 중복성 유지를 위해 이러한 '개인' 이미지를 준비하려면 다음 항목을 고려해야 합니다.

___

> ![Windows][Logo_Windows] Windows
>
> sysprep 명령을 통해 Windows 설정(예: Windows SID 및 호스트 이름)을 온-프레미스 VM에서 추상화/일반화해야 합니다. 
[comment]: <> (MSSedusch > See more details here :) 
[comment]: <> (MShermannd TODO first link is about classic model. Didn't find an Azure docu article) 
[comment]: <> (MSSedusch > <https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>) 
[comment]: <> (MSSedusch > <http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
>
> ![Linux][Logo_Linux] Linux
>
> 이러한 문서에서 [SUSE][virtual-machines-linux-create-upload-vhd-suse] 또는 [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]에 대해 설명된 단계를 수행하여 Azure에 업로드할 VHD를 준비하세요.

___

온-프레미스 VM(특히 2계층 시스템)에 SAP 콘텐츠를 이미 설치한 경우 Azure VM 배포 후 SAP Software Provisioning Manager에서 지원하는 인스턴스 이름 변경 절차를 통해 SAP 시스템 설정을 적용할 수 있습니다(SAP Note [1619720]). 온-프레미스 준비 단계 및 Azure에 대한 일반화된 VM 업로드에 대해서는 이 문서의 [SAP용 고객별 이미지를 사용하여 VM 배포 준비][planning-guide-5.2.2] 및 [온-프레미스에서 Azure로 VHD 업로드][planning-guide-5.3.2] 챕터를 참조하세요. Azure에서 이러한 이미지를 배포하는 자세한 단계는 [배포 가이드][deployment-guide]의 [시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.3] 챕터를 참조하세요.

#### Azure 마켓플레이스에서 VM 배포
Azure 마켓플레이스에서 Microsoft 또는 타사에서 제공한 VM 이미지를 사용할 수 있습니다. Azure에서 VM을 배포한 후 온-프레미스 환경에서와 동일한 지침 및 도구를 사용하여 VM 내부에 SAP 소프트웨어 및/또는 DBMS를 설치합니다. 자세한 배포 설명을 보려면 [배포 가이드][deployment-guide]의 [시나리오 1: SAP용 Azure 마켓플레이스에서 VM 배포][deployment-guide-3.2] 챕터를 참조하세요.

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Azure용 SAP로 VM 준비
VM을 Azure에 업로드하기 전에 VM 및 VHD가 특정 요구 사항을 충족하는지 확인해야 합니다. 사용되는 배포 방법에 따라 약간의 차이가 있습니다.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비
일반적인 배포 방법은 SAP 시스템을 실행하는 기존 VM을 온-프레미스에서 Azure으로 이동하는 것입니다. 해당 VM 및 VM의 SAP 시스템은 동일한 호스트 이름과 거의 동일한 SAP SID를 사용하여 Azure에서 실행되어야 합니다. 이 경우 다중 배포를 위해 VM의 게스트 OS를 일반화해야 합니다. 온-프레미스 네트워크가 Azure로 확장된 경우(이 문서의 [프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포][planning-guide-2.2] 챕터 참조) 온-프레미스에서 이전에 사용된 것과 같은 동일한 도메인 계정을 VM 내에서 사용할 수 있습니다.

고유한 Azure VM 디스크를 준비할 때의 요구 사항은 다음과 같습니다.

* 원래 운영 체제가 포함된 VHD의 최대 크기는 127GB로 제한되어 있었습니다. 이 제한은 2015년 3월 말에 없어졌습니다. 이제 운영 체제를 포함하는 VHD도 다른 Azure 저장소 호스트 VHD처럼 최대 크기가 1TB까지 가능합니다. 
[comment]: <> (MShermannd TODO have to check if CLI also converts to static )
* 고정된 VHD 형식이어야 합니다. 동적 VHD 또는 VHDx 형식의 VHD는 Azure에서 아직 지원되지 않습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* VM에 탑재되고 Azure에서 다시 VM으로 탑재되어야 하는 VHD도 고정 VHD 형식이어야 합니다. OS 디스크의 동일한 크기 제한이 데이터 디스크에도 적용됩니다. VHD의 최대 크기는 1TB일 수 있습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* Microsoft 기술 지원 서비스에서 사용할 수 있거나 VM이 배포되고 적절한 추가 사용자가 사용할 수 있게 될 때까지 서비스 및 응용 프로그램이 실행되기 위한 컨텍스트로 할당될 수 있는 관리자 권한의 다른 로컬 계정을 추가합니다.
* 이 배포 방법과 함께 클라우드 전용 배포 시나리오를 사용하는 경우(이 문서의 [클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포][planning-guide-2.1] 챕터 참조) Azure에 Azure 디스크가 배포되면 도메인 계정이 작동하지 않을 수 있습니다. DBMS 또는 SAP 응용 프로그램과 같은 서비스를 실행하는 데 사용되는 계정의 경우가 특히 여기에 해당합니다. 따라서 이러한 도메인 계정을 VM 로컬 계정으로 바꾸고 VM에서 온-프레미스 도메인 계정을 삭제해야 합니다. VM 이미지에 온-프레미스 도메인 사용자를 유지하는 것은 [프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포][planning-guide-2.2] 챕터에 설명된 대로 VM을 프레미스 간 시나리오에서 배포할 때는 문제가 되지 않습니다.
* 시스템을 온-프레미스에서 실행할 때 도메인 계정을 DBMS 로그인 또는 사용자로 사용하고 해당 VM을 클라우드 전용 시나리오에서 배포해야 할 경우 도메인 사용자를 삭제해야 합니다. 로컬 관리자와 다른 VM 로컬 사용자가 관리자 권한이 있는 로그인/사용자로 DBMS에 추가되는지도 확인해야 합니다.
* 특정 배포 시나리오에 필요할 수 있으므로 다른 로컬 계정을 추가합니다.

___

> ![Windows][Logo_Windows] Windows
>
> 이 시나리오에서는 Azure에서 VM을 업로드 및 배포하기 위해 VM의 일반화(sysprep)가 필요하지 않습니다. D:\\ 드라이브가 사용되지 않는지 확인하고 이 문서의 [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 챕터에 설명된 대로 연결된 디스크에 대해 디스크 자동 탑재를 설정하세요.
> 
> ![Linux][Logo_Linux] Linux
>
> 이 시나리오에서는 Azure에서 VM을 업로드 및 배포하기 위해 VM의 일반화(waagent -deprovision)가 필요하지 않습니다. /mnt/resource가 사용되지 않는지와 모든 디스크가 uuid를 통해 탑재되는지 확인합니다. OS 디스크의 경우 부팅 로더 항목에 uuid 기반 탑재가 반영되는지도 확인합니다.

___

#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>SAP용 고객별 이미지를 사용하여 VM 배포 준비
일반화된 OS를 포함하는 VHD 파일은 Azure 저장소 계정의 컨테이너에도 저장됩니다. [배포 가이드][deployment-guide]의 [시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.3] 챕터에 설명된 것처럼 VHD를 배포 템플릿 파일에서 원본 VHD로 참조하여 이러한 이미지 VHD에서 새 VM을 배포할 수 있습니다.

고유한 Azure VM 이미지를 준비할 때의 요구 사항은 다음과 같습니다.

* 원래 운영 체제가 포함된 VHD의 최대 크기는 127GB로 제한되어 있었습니다. 이 제한은 2015년 3월 말에 없어졌습니다. 이제 운영 체제를 포함하는 VHD도 다른 Azure 저장소 호스트 VHD처럼 최대 크기가 1TB까지 가능합니다. 
[comment]: <> (MShermannd TODO have to check if CLI also converts to static )
* 고정된 VHD 형식이어야 합니다. 동적 VHD 또는 VHDx 형식의 VHD는 Azure에서 아직 지원되지 않습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* VM에 탑재되고 Azure에서 다시 VM으로 탑재되어야 하는 VHD도 고정 VHD 형식이어야 합니다. OS 디스크의 동일한 크기 제한이 데이터 디스크에도 적용됩니다. VHD의 최대 크기는 1TB일 수 있습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* VM에 사용자로 등록된 모든 도메인 사용자가 클라우드 전용 배포 시나리오에 존재하는 것은 아니므로(이 문서의 [클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포][planning-guide-2.1] 챕터 참조) Azure에 이미지가 배포되면 이러한 도메인 계정을 사용하는 서비스가 작동하지 않을 수 있습니다. DBMS 또는 SAP 응용 프로그램과 같은 서비스를 실행하는 데 사용되는 계정의 경우가 특히 여기에 해당합니다. 따라서 이러한 도메인 계정을 VM 로컬 계정으로 바꾸고 VM에서 온-프레미스 도메인 계정을 삭제해야 합니다. VM 이미지에 온-프레미스 도메인 사용자를 유지하는 것은 [프레미스 간 - 온-프레미스 네트워크에 요구 사항을 완전히 통합하고 Azure에 단일 또는 다중 SAP VM 배포][planning-guide-2.2] 챕터에 설명된 대로 VM을 프레미스 간 시나리오에서 배포할 때는 문제가 되지 않을 수 있습니다.
* Microsoft 기술 지원 서비스에서 문제를 조사하는 데 사용할 수 있거나 VM이 배포되고 적절한 추가 사용자가 사용할 수 있게 될 때까지 서비스 및 응용 프로그램이 실행되기 위한 컨텍스트로 할당될 수 있는 관리자 권한의 다른 로컬 계정을 추가합니다.
* 클라우드 전용 배포이면서 시스템을 온-프레미스에서 실행할 때 도메인 계정을 DBMS 로그인 또는 사용자로 사용하는 경우 도메인 사용자를 삭제해야 합니다. 로컬 관리자와 다른 VM 로컬 사용자가 관리자 권한이 있는 로그인/사용자로 DBMS에 추가되는지도 확인해야 합니다.
* 특정 배포 시나리오에 필요할 수 있으므로 다른 로컬 계정을 추가합니다.
* 이미지에 SAP NetWeaver의 설치가 포함되어 있고 Azure 배포 시에 호스트 이름을 원래 이름에서 다르게 변경할 가능성이 있는 경우 SAP Software Provisioning Manager DVD의 최신 버전을 템플릿으로 복사하는 것이 좋습니다. 이렇게 하면 새 복사가 시작되는 즉시, SAP 제공 이름 바꾸기 기능을 사용하여 배포된 VM 이미지 내에서 변경된 호스트 이름을 손쉽게 조정하고 SAP 시스템의 SID를 변경할 수 있습니다.

___

> ![Windows][Logo_Windows] Windows
>
> D:\\ 드라이브가 사용되지 않는지 확인하고 이 문서의 [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 챕터에 설명된 대로 연결된 디스크에 대해 디스크 자동 탑재를 설정하세요.
> 
> ![Linux][Logo_Linux] Linux
>
> /mnt/resource가 사용되지 않는지와 모든 디스크가 uuid를 통해 탑재되는지 확인합니다. OS 디스크의 경우 부팅 로더 항목에 uuid 기반 탑재가 반영되는지도 확인합니다.

___

* SAP GUI(관리 및 설치용)를 이러한 템플릿에 미리 설치할 수 있습니다.
* 프레미스 간 시나리오에서 VM을 성공적으로 실행하는 데 필요한 기타 소프트웨어가 VM의 이름 바꾸기 기능에서 문제 없이 작동되면 설치해도 됩니다.

VM이 범용으로 준비되고, 결과적으로 대상 Azure 배포 시나리오에서 사용할 수 없는 계정/사용자와 독립될 경우 이러한 이미지 일반화의 마지막 준비 단계가 수행됩니다.

##### VM 일반화 

___

[comment]: <> (MShermannd TODO have to find better articles / docu about generalizing the VMs for ARM )
> ![Windows][Logo_Windows] Windows
>
> 마지막 단계는 관리자 계정을 사용하여 VM에 로그인하는 것입니다. '관리자 권한'으로 Windows 명령 창을 엽니다. …\\windows\\system32\\sysprep로 이동한 후 sysprep.exe를 실행합니다. 작은 창이 나타납니다. '일반화' 옵션을 선택하고(기본적으로 선택 취소됨) 종료 옵션을 기본 설정인 ‘다시 부팅'에서 '종료'로 변경해야 합니다. 이 절차에서는 sysprep 프로세스가 VM의 게스트 OS에서 온-프레미스로 실행된다고 가정합니다. Azure에서 VM이 이미 실행되고 있는 상태에서 이 절차를 수행하려는 경우 [이 문서][virtual-machines-windows-capture-image]에 설명된 단계를 따르세요.
> 
> ![Linux][Logo_Linux] Linux
>
> [Linux 가상 컴퓨터를 캡처하여 Resource Manager 템플릿으로 사용하는 방법][virtual-machines-linux-capture-image]

___

### 온-프레미스와 Azure 간에 VM 및 VHD 전송
Azure 포털을 통해서는 Azure에 VM 이미지와 디스크를 업로드할 수 없으므로 Azure PowerShell cmdlet 또는 CLI를 사용해야 합니다. 'AzCopy' 도구를 사용할 수도 있습니다. 이 도구는 온-프레미스와 Azure 간에 VHD를 복사할 수 있습니다(양방향으로). 또한 Azure 지역 간에 VHD를 복사할 수도 있습니다. AzCopy의 다운로드 및 사용에 대해서는 [이 설명서][storage-use-azcopy]를 참조하세요.

세 번째 방법은 다양한 타사 GUI 기반 도구를 사용하는 것입니다. 그러나 이러한 도구가 Azure 페이지 Blob을 지원하는지 확인하세요. 여기서는 작업 목적에 맞게 Azure 페이지 Blob 저장소를 사용해야 합니다(차이점은 여기에 설명되어 있습니다. <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>). 또한 Azure에서 제공하는 도구는 업로드해야 하는 VM 및 VHD를 압축하는 데 매우 효율적입니다. 이러한 압축 효율성은 업로드 시간(온-프레미스 시설과 대상 Azure 배포 지역에서 인터넷으로의 업로드 링크에 따라 다름)을 단축하므로 중요합니다. 따라서 유럽 위치에서 미국의 Azure 데이터 센터로 VM 또는 VHD를 업로드하는 것은 같은 VM/VHD를 유럽의 Azure 데이터 센터로 업로드하는 것보다 더 오래 걸린다고 가정할 수 있습니다.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>온-프레미스에서 Azure로 VHD 업로드
온-프레미스 네트워크의 기존 VM 또는 VHD를 업로드하려면 이러한 VM 또는 VHD가 이 문서의 [일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로의 VM 이동 준비][planning-guide-5.2.1] 챕터에 설명된 요구 사항을 충족해야 합니다.

이러한 VM은 일반화할 필요가 없으며 온-프레미스 쪽에서 종료된 이후의 상태 및 형태 그대로 업로드할 수 있습니다. 운영 체제에 포함하지 않는 추가 VHD도 마찬가지입니다.

##### VHD를 업로드한 후 Azure 디스크로 만들기
이러한 경우 OS를 포함하거나 포함하지 않는 VHD를 업로드하려고 하며 VM을 데이터 디스크로 탑재하거나 OS 디스크로 사용할 것입니다. 이 작업은 다중 단계로 진행됩니다.

__PowerShell__

* _Login-AzureRmAccount_을 사용하여 구독에 로그인
* _Set-AzureRmContext_ 및 매개 변수 SubscriptionId 또는 SubscriptionName을 사용하여 컨텍스트의 구독 설정 - <https://msdn.microsoft.com/library/mt619263.aspx> 참조
* _Add-AzureRmVhd_를 사용하여 Azure 저장소 계정에 VHD 업로드 - <https://msdn.microsoft.com/library/mt603554.aspx> 참조
* _Set-AzureRmVMOSDisk_를 사용하여 새 VM 구성의 OS 디스크를 VHD로 설정 - <https://msdn.microsoft.com/library/mt603746.aspx> 참조
* _New-AzureRmVM_을 사용하여 VM 구성에서 새 VM 만들기 - <https://msdn.microsoft.com/library/mt603754.aspx> 참조
* _Add-AzureRmVMDataDisk_를 사용하여 새 VM에 데이터 디스크 추가 - <https://msdn.microsoft.com/library/mt603673.aspx> 참조

__Azure CLI__

* _azure config mode arm_을 사용하여 Azure Resource Manager 모드로 전환
* _azure login_을 사용하여 구독에 로그인
* _azure account set `<subscription name or id`을(를) 사용하여 구독 선택>_
* _azure storage blob upload_를 사용하여 VHD 업로드 - [Azure Storage와 Azure CLI 사용][storage-azure-cli] 참조
* _azure vm create_ 및 매개 변수 -d를 사용하여 업로드한 VHD를 OS 디스크로 지정하고 새 VM 만들기
* _vm disk attach-new_를 사용하여 데이터 디스크를 새 VM에 추가

__템플릿__

* Powershell 또는 Azure CLI를 사용하여 VHD 업로드
* [이 예제 JSON 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json)에 표시된 대로 VHD를 참조하는 JSON 템플릿을 사용하여 VM을 배포합니다.

#### VM 이미지 배포
온-프레미스 네트워크의 기존 VM 또는 VHD를 Azure VM 이미지로 사용하려면 이러한 VM 또는 VHD가 이 문서의 [SAP용 고객별 이미지를 사용하여 VM 배포 준비][planning-guide-5.2.2] 챕터에 나열된 요구 사항을 충족해야 합니다.

* Windows의 경우 _sysprep_, Linux의 경우 _waagent-deprovision_을 사용하여 VM을 일반화합니다. [Resource Manager 배포 모델에서 Windows 가상 컴퓨터를 캡처하는 방법][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] 또는 [Linux 가상 컴퓨터를 캡처하여 리소스 관리자 템플릿으로 사용하는 방법][virtual-machines-linux-capture-image-capture]을 참조하세요.
* _Login-AzureRmAccount_을 사용하여 구독에 로그인
* _Set-AzureRmContext_ 및 매개 변수 SubscriptionId 또는 SubscriptionName을 사용하여 컨텍스트의 구독 설정 - <https://msdn.microsoft.com/library/mt619263.aspx> 참조
* _Add-AzureRmVhd_를 사용하여 Azure 저장소 계정에 VHD 업로드 - <https://msdn.microsoft.com/library/mt603554.aspx> 참조
* _Set-AzureRmVMOSDisk -SourceImageUri -CreateOption fromImage_를 사용하여 새 VM 구성의 OS 디스크를 VHD로 설정 - <https://msdn.microsoft.com/library/mt603746.aspx> 참조
* _New-AzureRmVM_을 사용하여 VM 구성에서 새 VM 만들기 - <https://msdn.microsoft.com/library/mt603754.aspx> 참조

__Azure CLI__

* Windows의 경우 _sysprep_, Linux의 경우 _waagent-deprovision_을 사용하여 VM을 일반화합니다. [Resource Manager 배포 모델에서 Windows 가상 컴퓨터를 캡처하는 방법][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] 또는 Linux의 경우는 [Linux 가상 컴퓨터를 캡처하여 리소스 관리자 템플릿으로 사용하는 방법][virtual-machines-linux-capture-image-capture]을 참조하세요.
* _azure config mode arm_을 사용하여 Azure Resource Manager 모드로 전환
* _azure login_을 사용하여 구독에 로그인
* _azure account set `<subscription name or id`을(를) 사용하여 구독 선택>_
* _azure storage blob upload_를 사용하여 VHD 업로드 - [Azure Storage와 Azure CLI 사용][storage-azure-cli] 참조
* _azure vm create_ 및 매개 변수 -Q를 사용하여 업로드한 VHD를 OS 디스크로 지정하고 새 VM 만들기

__템플릿__

* Windows의 경우 _sysprep_, Linux의 경우 _waagent-deprovision_을 사용하여 VM을 일반화합니다. [Resource Manager 배포 모델에서 Windows 가상 컴퓨터를 캡처하는 방법][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] 또는 Linux의 경우는 [Linux 가상 컴퓨터를 캡처하여 리소스 관리자 템플릿으로 사용하는 방법][virtual-machines-linux-capture-image-capture]을 참조하세요.
* Powershell 또는 Azure CLI를 사용하여 VHD 업로드
* [이 예제 JSON 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json)에 표시된 대로 이미지 VHD를 참조하는 JSON 템플릿을 사용하여 VM을 배포합니다.

#### 온-프레미스로 VHD 다운로드
Azure Infrastructure as a Services는 VHD 및 SAP 시스템을 업로드만 할 수 있는 단방향 경로가 아닙니다. Azure에서 온-프레미스 환경으로도 SAP 시스템을 다시 이동할 수 있습니다.

다운로드하는 동안 VHD는 활성 상태일 수 없습니다. VM에 탑재된 VHD를 다운로드하는 경우에도 VM을 종료해야 합니다. 온-프레미스에서 새 시스템을 설정하는 데 사용해야 하는 데이터베이스 콘텐츠만 다운로드하려고 하며 다운로드 도중 및 새 시스템을 설정하는 동안 Azure의 시스템이 계속 작동되게 하려면 VHD에 압축된 데이터베이스 백업을 수행하여 OS 기반 VM은 다운로드하지 않고 해당 VHD만 다운로드함으로써 가동 중지 시간이 길어지지 않도록 할 수 있습니다.

#### Powershell

SAP 시스템이 중지되고 VM이 종료되면 온-프레미스 대상에 대해 PowerShell cmdlet Save-AzureRmVhd를 사용하여 VHD 디스크를 온-프레미스 환경으로 다시 다운로드할 수 있습니다. 이를 위해 Azure 포털의 '저장소 섹션'에서 찾을 수 있는 VHD의 URL이 있어야 하며(저장소 계정 및 VHD가 만들어진 저장소 컨테이너로 이동해야 함) VHD를 복사할 위치를 알아야 합니다.

그런 후 매개 변수 SourceUri를 다운로드할 VHD의 URL로 정의하고 LocalFilePath를 VHD의 물리적 위치(해당 이름 포함)로 정의하여 이 명령을 활용할 수 있습니다. 명령은 다음과 같습니다.

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Save-AzureRmVhd cmdlet에 대한 자세한 내용은 여기 <https://msdn.microsoft.com/library/mt622705.aspx>에서 확인하세요.

#### CLI

SAP 시스템이 중지되고 VM이 종료되면 온-프레미스 대상에 대해 Azure CLI 명령 azure storage blob download를 사용하여 VHD 디스크를 온-프레미스 환경으로 다시 다운로드할 수 있습니다. 이를 위해 Azure 포털의 '저장소 섹션'에서 찾을 수 있는 VHD의 이름 및 컨테이너가 있어야 하며(저장소 계정 및 VHD가 만들어진 저장소 컨테이너로 이동해야 함) VHD를 복사할 위치를 알아야 합니다.

그런 후 다운로드할 VHD의 blob 및 container 매개 변수를 VHD의 URL로 정의하고 destination을 VHD의 물리적 대상 위치(해당 이름 포함)로 정의하여 이 명령을 활용할 수 있습니다. 명령은 다음과 같습니다.

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download> 
```

### Azure 내에서 VM 및 VHD 전송

#### Azure 내에서 SAP 시스템 복사

SAP 시스템 또는 SAP 응용 프로그램 계층을 지원하는 전용 DBMS 서버까지도 SAP 데이터베이스의 바이너리 또는 데이터 및 로그 파일과 함께 OS를 포함하는 여러 VHD로 구성될 수 있습니다. VHD를 복사하는 Azure의 기능과 VHD를 저장하는 Azure의 기능 모두 여러 VHD의 스냅숏을 동기식으로 만드는 동기화 메커니즘을 사용하지 않습니다. 따라서 복사 또는 저장된 VHD의 상태는 같은 VM에 탑재되는 경우에도 다를 수 있습니다 즉, 여러 다른 VHD에 다른 데이터 및 로그 파일이 포함된 구체적인 경우에도 데이터베이스는 일관되지 않게 됩니다.

**결론: SAP 시스템 구성의 일부로 VHD를 복사하거나 저장하려면 SAP 시스템을 중지하고 배포된 VM도 종료해야 합니다. 그런 후에만 VHD 집합을 복사 또는 다운로드하여 Azure 또는 온-프레미스에서 SAP 시스템의 복사본을 만들 수 있습니다.**

데이터 디스크는 Azure 저장소 계정에 VHD 파일로 저장되며 가상 컴퓨터에 직접 연결되거나 이미지로 사용될 수 있습니다. 이 경우 VHD는 다른 위치로 복사된 후에 가상 컴퓨터에 연결됩니다. Azure에서 VHD 파일의 전체 이름은 Azure 내에서 고유해야 합니다. 이미 앞서 언급한 것처럼 이 이름은 다음과 같은 3부분으로 구성됩니다.

	http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### Powershell
[이 문서][storage-powershell-guide-full-copy-vhd]에 표시된 것처럼 Azure PowerShell cmdlet을 사용하여 VHD를 복사할 수 있습니다.

##### CLI
[이 문서][storage-azure-cli-copy-blobs]에 표시된 것처럼 Azure CLI를 사용하여 VHD를 복사할 수 있습니다.

##### Azure 저장소 도구

* <http://azurestorageexplorer.codeplex.com/releases/view/125870>

여기에서 찾을 수 있는 Azure 저장소 탐색기의 전문가용 버전도 있습니다.

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>


저장소 계정 내에서 VHD 자체를 복사하는 작업은 몇 초면 끝나는 프로세스입니다(지연 복사 및 기록 중 복사를 사용하여 스냅숏을 만드는 SAN 하드웨어와 유사). VHD 파일의 복사본을 만든 후에는 가상 컴퓨터에 연결하거나 VHD 복사본을 가상 컴퓨터에는 연결하기 위한 이미지로 사용할 수 있습니다.

##### Powershell

```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### CLI
```
azure config mode arm 

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Azure 저장소 계정 간 디스크 복사
Azure 포털에서는 이 작업을 수행할 수 없습니다. Azure PowerShell cmdlet, Azure CLI 또는 타사 저장소 브라우저를 사용할 수 있습니다. 저장소 계정 간 및 Azure 구독 내에서 지역 간에 Blob을 비동기식으로 복사하는 기능이 있는 PowerShell cmdlet 또는 CLI 명령으로 Blob을 만들고 관리할 수 있습니다.

##### Powershell 

구독 간에 VHD를 복사하는 것도 가능합니다. 자세한 내용은 [이 문서][storage-powershell-guide-full-copy-vhd]를 참조하세요.

PS cmdlet 논리의 기본 흐름은 다음과 같습니다.

* _New-AzureStorageContext_를 사용하여 원본 저장소 계정의 저장소 계정 컨텍스트 만들기 - <https://msdn.microsoft.com/library/dn806380.aspx> 참조
* _New-AzureStorageContext_를 사용하여 대상 저장소 계정의 저장소 계정 컨텍스트 만들기 - <https://msdn.microsoft.com/library/dn806380.aspx> 참조
* 복사 시작

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* 루프의 복사본 상태 확인
 
```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* 위에서 설명한 것처럼 가상 컴퓨터에 새 VHD를 연결합니다.

예제는 [이 문서][storage-powershell-guide-full-copy-vhd]를 참조하세요.

##### CLI
* 복사 시작

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* 루프의 복사본 상태 확인

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```
  
* 위에서 설명한 것처럼 가상 컴퓨터에 새 VHD를 연결합니다.

예제는 [이 문서][storage-azure-cli-copy-blobs]를 참조하세요.

### 디스크 처리
#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP 배포를 위한 VM/VHD 구조
이상적으로 VM 및 관련된 VHD의 구조를 처리하는 작업은 간단해야 합니다. 온-프레미스 설치에서 고객은 서버 설치를 구성하는 여러 가지 방법을 개발했습니다.

* OS와 DBMS 및/또는 SAP의 모든 바이너리가 들어 있는 하나의 기본 VHD. 2015년 3월부터 이전의 127GB 제한이 없어지면서 이 VHD는 최대 1TB까지 포함할 수 있습니다.
* SAP 데이터베이스의 DBMS 로그 파일과 DBMS 임시 저장소 영역의 로그 파일(DBMS에서 지원하는 경우)이 포함된 하나 또는 여러 개의 VHD. 데이터베이스 로그 IOPS 요구 수준이 높은 경우 필요한 IOPS 볼륨에 도달하기 위해 여러 VHD를 스트라이프해야 합니다.
* SAP 데이터베이스의 하나 또는 두 개의 데이터베이스 파일 및 DBMS 임시 데이터 파일(DBMS에서 지원하는 경우)을 포함하는 많은 수의 VHD

![SAP용 Azure IaaS VM의 참조 구성][planning-guide-figure-1300]

[comment]: <> (MShermannd TODO describe Linux structure )

___

> ![Windows][Logo_Windows] Windows
>
> 많은 고객에게서 OS가 설치된 c:\\ 드라이브에 SAP 및 DBMS 바이너리가 설치되지 않은 구성을 볼 수 있었습니다. 그 이유는 다양하지만 10~15년 전에는 근본적으로는 드라이브가 작고 OS 업그레이드를 하려면 추가 공간이 필요했기 때문일 것입니다. 두 조건이 오늘날에는 그렇게 많지 않은 편입니다. 현재는 c:\\ 드라이브를 큰 볼륨의 디스크나 VM에 매핑할 수 있습니다. 구조 내에서 배포를 단순하게 유지하려면 Azure의 SAP NetWeaver 시스템에 대해 다음 배포 패턴을 따르는 것이 좋습니다.
>
> Windows 운영 체제 페이지 파일은 D: 드라이브(비영구 디스크)에 있어야 합니다.
> 
> ![Linux][Logo_Linux] Linux
>
> [이 문서][virtual-machines-linux-agent-user-guide]에 설명된 대로 Linux에서는 /mnt/mnt/resource 아래에 Linux 스왑 파일을 배치합니다. 스왑 파일은 Linux 에이전트 /etc/waagent.conf의 구성 파일에서 구성할 수 있습니다. 다음 설정을 추가 또는 변경합니다.

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

변경 내용을 활성화하려면 다음을 사용하여 Linux 에이전트를 다시 시작해야 합니다.

```
sudo service waagent restart
```

권장 스왑 파일 크기에 대한 자세한 내용은 SAP 정보 [1597355]를 읽어보세요.

___

이러한 VHD가 호스트되는 Azure 저장소의 형식과 DBMS 데이터 파일에 사용되는 VHD의 수는 IOPS 요구 사항 및 필요한 대기 시간에 따라 결정되어야 합니다. 정확한 할당량은 [이 문서][virtual-machines-sizes]에 설명되어 있습니다.

지난 2년 동안의 SAP 배포 경험을 통해 다음과 같은 몇 가지 교훈을 얻었습니다.

* 기존 고객 시스템에는 SAP 데이터베이스를 나타내는 다양한 크기의 데이터 파일이 있을 수 있으므로 여러 다른 데이터 파일에 대한 IOPS 트래픽이 항상 동일한 것은 아닙니다. 따라서 여러 VHD를 사용하여 데이터 파일 LUN을 분리해서 배치하는 것보다 RAID 구성을 사용하는 것이 더 나은 것으로 확인되었습니다. 특히 IOPS 속도가 DBMS 트랜잭션 로그에 대한 단일 VHD의 할당량에 도달하는 Azure 표준 저장소에서 이러한 경우가 확인되었습니다. 이러한 시나리오에서는 프리미엄 저장소를 사용하거나 소프트웨어 RAID를 사용하여 여러 표준 저장소 VHD를 집계하는 것이 좋습니다.

___

> ![Windows][Logo_Windows] Windows
>
> * [Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례][virtual-machines-sql-server-performance-best-practices]
> 
> ![Linux][Logo_Linux] Linux
>
> * [Linux에서 소프트웨어 RAID 구성][virtual-machines-linux-configure-raid]
> * [Azure에서 Linux VM에 LVM 구성][virtual-machines-linux-configure-lvm]
> * [Azure 저장소 암호 및 Linux I/O 최적화](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)

___

* 프리미엄 저장소는 중요한 트랜잭션 로그 쓰기에서 특히 더 나은 성능을 나타냅니다. 프로덕션 수준의 성능을 제공할 것으로 기대되는 SAP 시나리오에서는 Azure 프리미엄 저장소를 활용할 수 있는 VM 시리즈를 사용하는 것이 강력히 권장됩니다.

OS와 SAP의 바이너리 및 데이터베이스(기본 VM)도 포함하는 VHD는 더 이상 127GB로 제한되지 않습니다. 이제 최대 1TB의 크기가 가능합니다. 이 크기는 SAP 배치 작업 로그를 비롯하여 필요한 모든 파일을 보관하는 데 충분한 공간이어야 합니다.

특히 DBMS VM과 관련된 추가 제안 사항 및 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요.


#### 디스크 처리
대부분의 시나리오에서는 VM으로 SAP 데이터베이스를 배포하기 위해 추가 디스크를 만들 필요가 있습니다. 이 문서의 [SAP 배포를 위한 VM/VHD 구조][planning-guide-5.5.1] 챕터에서는 VHD 수에 대한 고려 사항을 논의했습니다. Azure 포털을 사용하면 기본 VM을 배포한 후 디스크를 연결 및 분리할 수 있습니다. VM이 가동 및 실행 중일 때와 중지될 때 디스크를 연결/분리할 수 있습니다. 디스크를 연결할 때 Azure 포털은 빈 디스크 또는 기존 디스크(이 시점에 다른 VM에 연결되지 않은)에 연결할 수 있도록 합니다.

**참고**: VHD는 언제나 한 번에 하나의 VM에만 연결할 수 있습니다.
 
![Azure 표준 저장소에서 디스크 연결/분리][planning-guide-figure-1400]

새 VHD 또는 빈 VHD(기본 VM의 경우와 동일한 저장소 계정에 만들어짐)를 만들지 또는 이전에 업로드되었으며 이제 VM에 연결해야 하는 기존 VHD를 선택할지를 결정해야 합니다.

**중요**: Azure 표준 저장소에서 호스트 캐싱을 사용하지 **않으려고 합니다**. 호스트 캐시 기본 설정을 기본값인 NONE으로 유지해야 합니다. Azure 프리미엄 저장소를 사용하는 경우 I/O 특성이 데이터베이스 데이터 파일에 대한 일반적인 I/O 트래픽으로 해석되는 경우 읽기 캐싱을 사용하도록 설정해야 합니다. 데이터베이스 트랜잭션 로그 파일의 경우 캐싱 없음이 권장됩니다.

___

> ![Windows][Logo_Windows] Windows
>
> [Azure 포털에서 데이터 디스크를 연결하는 방법][virtual-machines-windows-attach-disk-portal]
>
> 디스크를 연결하는 경우 VM에 로그인하여 Windows 디스크 관리자를 열어야 합니다. [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 챕터에 권장된 대로 자동 탑재를 사용하도록 설정하지 않으면 새로 연결된 볼륨을 온라인 상태로 만든 후 초기화해야 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 디스크가 연결되는 경우 VM에 로그인하고 [이 문서][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]에 설명된 대로 디스크를 초기화해야 합니다.

___

새 디스크가 비어 있는 디스크이면 포맷도 필요합니다. 포맷 시, 특히 DBMS 데이터 및 로그 파일의 경우, DBMS 완전 배포의 경우와 동일한 권장 지침이 적용됩니다.

[Microsoft Azure 가상 컴퓨터 개념][planning-guide-3.2] 챕터에서 이미 언급한 것처럼 Azure 저장소 계정은 I/O 볼륨, IOPS 및 데이터 볼륨 측면에서 무한 리소스를 제공하지 않습니다. 일반적으로 DBMS VM이 그 영향을 가장 많이 받게 됩니다. Azure 저장소 계정 볼륨의 제한을 벗어나지 않기 위해서는 배포할 높은 I/O 볼륨 VM이 거의 없을 경우 각 VM에 대해 별도 저장소 계정을 사용하는 것이 가장 좋을 수 있습니다. 그렇지 않으면 각 단일 저장소 계정 제한에 도달하지 않으면서 개별 저장소 계정 간에 이러한 VM을 골고루 분산하는 방법을 찾아야 합니다. 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요. 또한 순수한 SAP 응용 프로그램 서버 VM 또는 결과적으로는 추가 VHD를 요구할 수 있는 기타 VHD의 경우에도 이러한 제한에 유의해야 합니다.

저장소 계정과 관련된 또 다른 주제는 저장소 계정의 VHD가 지역에서 복제되는지 여부입니다. 지역에서 복제 기능은 VM 수준이 아닌 저장소 계정 수준에서 사용하거나 사용하지 않도록 설정됩니다. 지역에서 복제 기능이 사용되도록 설정되면 저장소 계정 내의 VHD가 동일한 지역 내의 다른 Azure 데이터 센터로 복제됩니다. 이를 결정하기 전에 다음 제한 사항을 고려해야 합니다.

Azure 지역에서 복제 기능은 VM의 각 VHD에서 로컬로 작동하며, VM의 여러 VHD에 걸쳐 시간순으로 IO를 복제하지 않습니다. 따라서 기본 VM을 나타내는 VHD와 VM에 연결된 추가 VHD는 서로 독립적으로 복제됩니다. 즉, 여러 다른 VHD에서 변경된 내용은 동기화되지 않습니다. IO가 기록된 순서와는 별도로 복제된다는 사실은 여러 VHD에 걸쳐 데이터베이스가 분산되어 있는 데이터베이스 서버의 경우에는 지역에서 복제 기능이 의미가 없음을 나타냅니다. DBMS 외에도 다른 VHD에서 데이터를 쓰거나 조작하는 프로세스의 응용 프로그램이나 변경 순서가 중요한 응용 프로그램도 있을 수 있습니다. 이러한 경우가 요구되면 Azure의 지역에서 복제 기능을 사용하지 않도록 설정해야 합니다. 다른 VM 집합이 아닌 특정 VM 집합에 대해 지역에서 복제 기능이 필요한지 또는 이 기능을 원하는지에 따라, 이미 VM 및 관련된 VHD를 지역에서 복제 기능이 사용되거나 사용되지 않도록 설정된 다른 저장소 계정으로 분류할 수 있습니다.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>연결된 디스크에 대한 자동 탑재 설정

___


> ![Windows][Logo_Windows] Windows
> 
> 자체 이미지나 디스크에서 만든 VM의 경우 automount 매개 변수를 확인하고 설정해야 합니다. 이 매개 변수를 설정하면 Azure에서 VM을 다시 시작하거나 재배포한 후에 VM이 연결/탑재된 드라이브를 자동으로 다시 탑재할 수 있습니다. Azure 마켓플레이스에서 Microsoft가 제공하는 이미지의 경우 이 매개 변수가 설정되어 있습니다.
>
> 자동 탑재를 설정하려면 명령줄 실행 파일인 diskpart.exe의 설명서를 참조하세요.
> 
> * [DiskPart 명령줄 옵션](https://technet.microsoft.com/library/cc766465.aspx)
> * [Automount](http://technet.microsoft.com/library/cc753703.aspx)
> 
> Windows 명령줄 창은 관리자 권한으로 열어야 합니다.
> 
> 디스크를 연결하는 경우 VM에 로그인하여 Windows 디스크 관리자를 열어야 합니다. [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 챕터에 권장된 대로 자동 탑재를 사용하도록 설정하지 않으면 새로 연결된 볼륨을 온라인 상태로 만든 후 초기화해야 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> [이 문서][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]에 설명된 대로 새로 연결된 빈 디스크를 초기화해야 합니다. /etc/fstab에 새 디스크를 추가해야 합니다.

___


### 최종 배포
특히 SAP 확장 모니터링의 배포와 관련해서 최종 배포 및 정확한 단계를 보려면 [배포 가이드][deployment-guide]를 참조하세요.

## Azure VM 내에서 실행되는 SAP 시스템에 액세스
클라우드 전용 시나리오의 경우 SAP GUI를 사용하여 공용 인터넷을 통해 해당 SAP 시스템에 연결하려고 할 수 있습니다. 이러한 경우 다음 절차를 적용해야 합니다.

이 문서 뒷부분에서는 사이트 간 연결(VPN 터널) 또는 온-프레미스 시스템과 Azure 시스템 간의 Azure Express 경로 연결이 있는 프레미스 간 배포에서 SAP 시스템에 연결하는 다른 주요 시나리오가 나와 있습니다.


### SAP 시스템에 대한 원격 액세스

Azure Resource Manager를 사용할 경우 더 이상 이전의 클래식 모델과 같은 기본 끝점이 없습니다. 다음과 같은 조건이 충족되면 ARM Azure VM의 모든 포트는 열려 있습니다.

1. 서브넷 또는 네트워크 인터페이스에 대해 정의된 네트워크 보안 그룹이 없습니다. Azure VM으로의 네트워크 트래픽이 "네트워크 보안 그룹"을 통해 보호될 수 있습니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?][virtual-networks-nsg]을 참조하세요.
1. 네트워크 인터페이스에 대해 정의된 Azure Load Balancer가 없습니다.
 
[이 문서][virtual-machines-azure-resource-manager-architecture]에 설명된 클래식 모델과 ARM 간의 아키텍처 차이를 참조하세요.
 
#### 클라우드 전용 시나리오에 대한 SAP 시스템 및 SAP GUI 연결 구성
이 항목을 자세히 설명하는 이 문서를 참조하세요. <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### VM 내의 방화벽 설정 변경
SAP 시스템으로의 인바운드 트래픽을 허용하도록 가상 컴퓨터에서 방화벽을 구성해야 할 수도 있습니다.

___

> ![Windows][Logo_Windows] Windows
>
> 기본적으로 Azure 배포 VM 내의 Windows 방화벽은 켜져 있습니다. 이제 SAP 포트가 열리도록 허용해야 하며 그러지 않으면 SAP GUI에 연결할 수 없게 됩니다. 다음을 수행합니다.
>
>  * 제어판\\시스템 및 보안\\Windows 방화벽의 ‘고급 설정’을 엽니다.
>  * 인바운드 규칙을 마우스 오른쪽 단추로 클릭하고 ‘새 규칙’을 선택합니다.
>  * 다음 마법사에서 새 '포트' 규칙을 만들도록 선택합니다.
>  * 마법사의 다음 단계에서 TCP의 설정을 그대로 두고 열려는 포트 번호를 입력합니다. 여기서 SAP 인스턴스 ID는 00이므로 3200을 사용했습니다. 인스턴스의 인스턴스 번호가 다른 경우 이전에 해당 인스턴스 번호를 기준으로 정의한 포트가 열립니다.
>  * 마법사의 다음 부분에서는 '연결 허용' 항목을 선택된 상태로 두어야 합니다.
>  * 마법사의 다음 단계에서는 해당 규칙이 도메인, 개인 및 공용 네트워크에 적용될지 여부를 정의해야 합니다. 필요한 경우 요구에 맞게 조정합니다. 그러나 공용 네트워크를 통해 외부에서 SAP GUI에 연결하는 경우 해당 규칙을 공용 네트워크에 적용해야 합니다.
>  * 마법사의 마지막 단계에서는 규칙 이름을 지정하고 '마침'을 눌러 규칙을 저장해야 합니다.
>
>  규칙은 즉시 적용됩니다.
>
> ![포트 규칙 정의][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Azure 마켓플레이스의 Linux 이미지는 기본적으로 iptables 방화벽을 사용하도록 설정하지 않으며 SAP 시스템에 대한 연결이 작동합니다. Iptables 또는 다른 방화벽을 사용하도록 설정한 경우 iptables 또는 사용한 방화벽의 설명서를 참조하여 포트 32xx로의 인바운드 tcp 트래픽을 허용하세요(여기서 xx는 SAP 시스템의 시스템 번호임).

___

#### 보안 권장 사항

SAP GUI는 실행 중인 어떤 SAP 인스턴스(포트 32xx)에도 직접 연결되지 않으며 먼저 열린 포트를 통해 SAP 메시지 서버 프로세스(포트 36xx)로 연결됩니다. 이전에는 응용 프로그램 인스턴스에 대한 내부 통신을 위해 메시지 서버에서 동일한 포트가 사용되었습니다. 온-프레미스 응용 프로그램 서버가 Azure의 메시지 서버와 우연히 통신하는 일을 방지하기 위해 통신 포트를 변경할 수 있습니다. SAP 메시지 서버와 해당 응용 프로그램 인스턴스 간의 내부 통신을 온-프레미스 시스템에서 복제된 시스템(프로젝트 테스트를 위한 개발 복제본 등)의 다른 포트 번호로 변경할 것을 강력히 권장합니다. 이 작업은 기본 프로필 매개 변수를 사용하여 수행할 수 있습니다.

>	rdisp/msserv_internal

자세한 내용은 다음을 참조하세요. <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm>

## <a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>SAP 인스턴스의 클라우드 전용 배포 개념

### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM
 
![Azure 클라우드 서비스에 격리된 같은 VM 이름의 단일 VM SAP 데모 시스템 실행][planning-guide-figure-1700]

이 시나리오에서는(이 문서의 [클라우드 전용][planning-guide-2.1] 챕터 참조) 전체 학습/데모 시나리오가 단일 VM 내에 포함된 전형적인 학습/데모 시스템 시나리오를 구현합니다. VM 이미지 템플릿을 통해 배포했다고 가정합니다. 또한 이러한 여러 데모/학습 VM을 동일한 이름의 VM과 함께 배포해야 한다고 가정합니다.

그뿐 아니라 이 문서의 [Azure용 SAP로 VM 준비][planning-guide-5.2] 챕터에 포함된 일부 섹션에 설명된 것처럼 VM 이미지를 만들었다고 가정합니다.

시나리오를 구현하는 이벤트의 순서는 다음과 같습니다.

[comment]: <> (MShermannd TODO have to provide ARM sample / description using json template + clarification regarding unique VM name within ARM virtual network )
##### Powershell

* 모든 학습/데모 환경에 대한 새 리소스 그룹 만들기

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* 새 저장소 계정 만들기

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* 모든 학습/데모 환경에 대해 새 가상 네트워크를 만들어 동일한 호스트 이름 및 IP 주소를 사용하도록 설정합니다. 가상 네트워크는 포트 3389로의 트래픽만 허용하여 SSH에 대해 원격 데스크톱 액세스 및 포트 22를 사용하도록 설정하는 네트워크 보안 그룹에 의해 보호됩니다.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* 인터넷에서 가상 컴퓨터에 액세스하는 데 사용할 수 있는 새 공용 IP 주소 만들기

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* 가상 컴퓨터에 대한 새 네트워크 인터페이스 만들기

```powershell 
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip 
```

* 가상 컴퓨터를 만듭니다. 클라우드 전용 시나리오의 경우 모든 VM이 동일한 이름을 갖습니다. 해당 VM에 있는 SAP NetWeaver 인스턴스의 SAP SID도 동일하게 유지됩니다. Azure 리소스 그룹 내에서 VM의 이름은 고유해야 하지만 서로 다른 Azure 리소스 그룹에서는 동일한 이름의 VM을 실행할 수 있습니다. Windows의 기본 '관리자' 계정 또는 Linux의 '루트'는 유효하지 않습니다. 따라서 새 관리자 사용자 이름을 암호와 함께 정의해야 합니다. 또한 VM의 크기를 정의해야 합니다.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* 필요에 따라 디스크를 더 추가하고 필요한 콘텐츠를 복원합니다. 모든 blob 이름(blob URL)은 Azure 내에서 고유해야 합니다.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### CLI

다음 코드 예제는 Linux에서 사용할 수 있습니다. Windows의 경우 위에서 설명한 것처럼 PowerShell을 사용하거나, 예제를 조정하여 $rgName 대신 %rgName%을 사용하고 Windows 명령 _set_를 사용하여 환경 변수를 설정합니다.

* 모든 학습/데모 환경에 대한 새 리소스 그룹 만들기

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* 새 저장소 계정 만들기

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* 모든 학습/데모 환경에 대해 새 가상 네트워크를 만들어 동일한 호스트 이름 및 IP 주소를 사용하도록 설정합니다. 가상 네트워크는 포트 3389로의 트래픽만 허용하여 SSH에 대해 원격 데스크톱 액세스 및 포트 22를 사용하도록 설정하는 네트워크 보안 그룹에 의해 보호됩니다.

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol * --source-address-prefix * --source-port-range * --destination-address-prefix * --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol * --source-address-prefix * --source-port-range * --destination-address-prefix * --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* 인터넷에서 가상 컴퓨터에 액세스하는 데 사용할 수 있는 새 공용 IP 주소 만들기

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* 가상 컴퓨터에 대한 새 네트워크 인터페이스 만들기

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet 
```

* 가상 컴퓨터를 만듭니다. 클라우드 전용 시나리오의 경우 모든 VM이 동일한 이름을 갖습니다. 해당 VM에 있는 SAP NetWeaver 인스턴스의 SAP SID도 동일하게 유지됩니다. Azure 리소스 그룹 내에서 VM의 이름은 고유해야 하지만 서로 다른 Azure 리소스 그룹에서는 동일한 이름의 VM을 실행할 수 있습니다. Windows의 기본 '관리자' 계정 또는 Linux의 '루트'는 유효하지 않습니다. 따라서 새 관리자 사용자 이름을 암호와 함께 정의해야 합니다. 또한 VM의 크기를 정의해야 합니다.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* 필요에 따라 디스크를 더 추가하고 필요한 콘텐츠를 복원합니다. 모든 blob 이름(blob URL)은 Azure 내에서 고유해야 합니다.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### Template
github의 azure-quickstart-templates 저장소에 있는 샘플 템플릿을 사용할 수 있습니다.

* [간단한 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [간단한 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [이미지의 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### Azure 내에서 통신해야 하는 VM 집합 구현
이 클라우드 전용 시나리오는 데모 및 학습 시나리오를 나타내는 소프트웨어가 여러 VM에 걸쳐 분산되어 있는 학습 및 데모용 일반 시나리오입니다. 여러 VM에 설치된 다양한 구성 요소는 서로 통신해야 합니다. 또한 이 시나리오에서는 온-프레미스 네트워크 통신이나 프레미스 간 시나리오가 필요하지 않습니다.

이 시나리오는 [SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM][planning-guide-7.1] 챕터에 설명된 설치의 확장입니다. 이 경우 더 많은 가상 컴퓨터가 기존 리소스 그룹에 추가됩니다. 다음 예제에서 교육 환경은 SAP ASCS/SCS VM, DBMS가 실행되는 VM 및 SAP 응용 프로그램 서버 인스턴스 VM으로 구성됩니다.

이 시나리오를 작성하기 전에 시나리오에서 이미 실행한 기본 설정을 고려해야 합니다.

#### 리소스 그룹 및 가상 컴퓨터 이름 지정
모든 리소스 그룹 이름은 고유해야 합니다. `<rg-name`>-접미사와 같은 리소스의 고유한 이름 지정 체계를 개발합니다.

가상 컴퓨터 이름은 리소스 그룹 내에서 고유해야 합니다.

#### 여러 VM 간의 통신을 위한 네트워크 설정
 
![Azure 가상 네트워크 내의 VM 집합][planning-guide-figure-1900]

동일한 학습/데모 환경의 복제본과의 이름 충돌을 방지하려면 모든 환경에 대해 Azure 가상 네트워크를 만들어야 합니다. Azure에서 제공하는 DNS 이름 확인을 사용하거나 Azure 외부에서 자체 DNS 서버를 구성할 수도 있습니다(여기서는 자세히 다루지 않음). 이 시나리오에서는 자체 DNS를 구성하지 않습니다. 단일 Azure 가상 네트워크 내의 모든 가상 컴퓨터의 경우 호스트 이름을 통한 통신이 사용되도록 설정됩니다.

단지 리소스 그룹만이 아니라 가상 네트워크를 기준으로 학습 또는 데모 환경을 구분하는 이유는 다음과 같습니다.

* 설정된 SAP 지형에는 자체 AD/OpenLDAP가 필요하고 도메인 서버는 각 지형의 일부가 되어야 합니다.
* 설정된 SAP 지형에는 고정된 IP 주소를 사용해야 하는 구성 요소가 있습니다.

Azure 가상 네트워크 및 이러한 네트워크를 정의하는 방법에 대한 자세한 내용은 [이 문서][virtual-networks-create-vnet-arm-pportal]에서 찾을 수 있습니다.

## 회사 네트워크 연결을 사용하여 SAP VM 배포(프레미스 간)

SAP 지형을 실행하고 고급 DBMS 서버용 운영 체제 미설치 영역, 응용 프로그램 계층 및 보다 작은 2계층으로 구성된 SAP 시스템과 Azure IaaS에 대한 온-프레미스 가상화 환경 간에 배포를 나눌 수 있습니다. 기본 가정은 단일 SAP 지형 내의 SAP 시스템은 배포 형식에 관계없이 서로 간에, 그리고 회사에 배포된 여러 다른 소프트웨어 구성 요소와 통신해야 한다는 것입니다. SAP GUI를 사용하여 연결하는 최종 사용자와 다른 인터페이스를 사용하여 연결하는 사용자는 배포 형식에 따른 차이를 느끼지 못해야 합니다. 이러한 조건은 온-프레미스 Active Directory/OpenLDAP 및 DNS 서비스를 사이트 간/다중 사이트 연결 또는 개인 연결(예: Azure Express 경로)을 통해 Azure 시스템으로 확장할 때만 충족될 수 있습니다.

Azure의 SAP 구현에 대한 자세한 배경 정보를 알고 싶으면 이 문서에서 Azure의 일부 기본 구조와 Azure의 SAP 응용 프로그램에서 이러한 구조가 사용되는 방식을 설명하는 [SAP 인스턴스의 클라우드 전용 배포 개념][planning-guide-7] 챕터를 읽어보세요.

### SAP 지형 시나리오

프레미스 간 시나리오는 아래 그래픽과 같이 대략적으로 나타낼 수 있습니다.
 
![온-프레미스와 Azure 자산 간의 사이트 간 연결][planning-guide-figure-2100]

위에 나와 있는 시나리오는 온-프레미스 AD/OpenLDAP 및 DNS가 Azure로 확장되는 시나리오를 설명합니다. 온-프레미스 쪽에서 특정 IP 주소 범위는 Azure 구독을 기준으로 예약됩니다. IP 주소 범위는 Azure 쪽의 Azure 가상 네트워크에 할당됩니다.

#### 보안 고려 사항

최소 요구 사항은 Azure 서비스의 브라우저 액세스를 위해서는 SSL/TLS, 시스템 액세스를 위해서는 VPN 기반 연결과 같은 보안 통신 프로토콜의 사용입니다. 단, 회사에서는 회사 네트워크와 Azure 간의 VPN 연결을 매우 다르게 처리하는 것을 전제로 합니다. 일부 회사에서는 모든 포트를 완전히 열어둘 수 있습니다. 또 다른 회사에서는 열어야 하는 포트를 매우 정확하게 지정하려고 할 수 있습니다.

아래 표에는 일반적인 SAP 통신 포트가 나와 있습니다. 기본적으로 SAP 게이트웨이 포트만 열어도 충분합니다.

| 부여 | 포트 이름 | 예: `<nn`> = 01 | 기본 범위(최소-최대) | 주석 |
|---------|-----------|-------------------|-------------------------|---------|
| 디스패처 | sapdp`<nn>` 참조: * | 3201 | 3200 – 3299 | SAP 디스패처, Windows 및 Java용 SAP GUI에서 사용 |
| 메시지 서버 | sapms`<sid`> 참조: ** | 3600 | 제한 없는 sapms`<anySID`> | sid = SAP-System-ID |
| 게이트웨이 | sapgw`<nn`> 참조: * | 3301 | 제한 없음 | SAP 게이트웨이, CPIC 및 RFC 통신에 사용 |
| SAP 라우터 | sapdp99 | 3299 | 제한 없음 | CI(중앙 인스턴스) 서비스 이름만 설치한 후에 /etc/services에서 임의 값으로 재할당될 수 있습니다. |

*) nn = SAP 인스턴스 번호

**) sid = SAP-System-ID

여러 SAP 제품에 필요한 포트 또는 SAP 제품 서비스에 대한 자세한 내용은 여기 <http://scn.sap.com/docs/DOC-17124>에서 확인할 수 있습니다. 이 문서를 사용할 경우 특정 SAP 제품 및 시나리오에 필요한 VPN 장치에서 전용 포트를 열 수 있습니다.

이러한 시나리오에서 VM을 배포할 경우 다른 보안 조치는 [네트워크 보안 그룹][virtual-networks-nsg]을 만들어 액세스 규칙을 정의하는 조치를 취할 수 있습니다.

### 다른 가상 컴퓨터 시리즈 처리

지난 12개월 동안 Microsoft는 vCPU 수, 메모리 또는 좀 더 중요하게 VM이 실행되는 하드웨어가 다른 VM 유형을 추가했습니다. 이러한 모든 VM이 SAP에서 지원되는 것은 아닙니다(SAP 정보 [1928533]에서 지원되는 VM 형식 참조). 해당 VM 중 일부는 다른 호스트 하드웨어 세대에서 실행됩니다. 이러한 호스트 하드웨어 세대는 Azure 확장 단위 세분성에 따라 배포됩니다. 선택한 다른 크기의 VM을 같은 확장 단위로 실행할 수 없는 골치 아픈 경우가 발생할 수 있습니다. 가용성 집합은 다른 하드웨어를 기반으로 확장 단위를 넓히는 능력이 제한되어 있습니다. 예를 들어 DBMS는 A5-A11 VM에서 실행하고, SAP 응용 프로그램 계층은 G-Series VM에서 실행하려는 경우 단일 SAP 시스템 또는 다른 SAP 시스템을 다른 가용성 집합 내에서 배포할 수 밖에 없습니다.


#### Azure의 SAP 인스턴스에서 로컬 네트워크 프린터로 인쇄
##### 프레미스 간 시나리오에서 TCP/IP를 통해 인쇄


Azure VM에서 온-프레미스 TCP/IP 기반 네트워크 프린터를 설정하는 것은 VPN 사이트 간 터널 또는 Express 경로 연결을 설정했다고 가정할 경우 회사 네트워크에서 작업하는 경우와 전반적으로 동일합니다.

___

> ![Windows][Logo_Windows] Windows
>
> 다음을 수행합니다.
> - 일부 네트워크 프린터에는 Azure VM에서 프린터를 쉽게 설정할 수 있도록 하는 구성 마법사가 포함되어 있습니다. 마법사 소프트웨어가 프린터와 함께 배포되지 않은 경우 프린터를 설정하는 "수동" 방법은 새 TCP/IP 프린터 포트를 만드는 것입니다.
> - 열기 제어판 -> 장치 및 프린터 -> 프린터 추가 열기
> - TCP/IP 주소 또는 호스트 이름을 사용하여 프린터 추가 선택
> - 프린터의 IP 주소 입력
> - 프린터 포트 표준 9100
> - 필요한 경우 적절한 프린터 드라이버를 수동으로 설치합니다.
> 
> ![Linux][Logo_Linux] Linux
>
> - Windows의 경우처럼 네트워크 프린터를 설치하는 표준 절차를 따릅니다.
> - [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) 또는 [Red Hat](https://access.redhat.com/documentation/ko-KR/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html)의 공용 Linux 가이드에서 프린터 추가 방법을 따르세요.

___

 
![네트워크 인쇄][planning-guide-figure-2200]



##### 프레미스 간 시나리오에서 SMB를 통한 호스트 기반 프린터(공유 프린터)

호스트 기반 프린터는 기본적으로 네트워크와 호환되지 않습니다. 그러나 전원이 켜진 컴퓨터에 프린터가 연결되기만 하면 네트워크 상의 컴퓨터 간에 호스트 기반 프린터가 공유될 수 있습니다. 회사 네트워크를 사이트 간 또는 Express 경로에 연결하고 로컬 프린터를 공유합니다. SMB 프로토콜은 이름 서비스로 DNS가 아닌 NetBIOS를 사용합니다. NetBIOS 호스트 이름은 DNS 호스트 이름과 다를 수 있습니다. NetBIOS 호스트 이름과 DNS 호스트 이름이 동일한 경우가 일반적인 경우입니다. DNS 도메인은 NetBIOS 네임스페이스에서는 의미가 없습니다. 따라서 DNS 호스트 이름과 DNS 도메인으로 이루어진 정규화된 DNS 호스트 이름은 NetBIOS 네임스페이스에서 사용하지 말아야 합니다.

프린터 공유는 네트워크에서 고유한 이름으로 식별됩니다.

* SMB 호스트의 호스트 이름(항상 필수)
* 공유의 이름(항상 필수)
* 프린터 공유가 SAP 시스템과 같은 도메인에 있지 않은 경우 도메인의 이름
* 또한 프린터 공유에 액세스하기 위해 사용자 이름과 암호가 필요할 수 있습니다.

방법:

___

> ![Windows][Logo_Windows] Windows
>
> 로컬 프린터를 공유합니다. Azure VM에서 Windows 탐색기를 열고 프린터의 공유 이름을 입력합니다. 프린터 설치 마법사가 설치 프로세스를 안내합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 다음은 Linux의 네트워크 프린터 구성에 대한 설명서의 몇 가지 예입니다. 여기에는 Linux의 인쇄에 대한 장도 포함되어 있습니다. VM이 VPN에 속할 경우 Azure Linux VM의 경우와 동일한 방식으로 작동합니다.
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL <https://access.redhat.com/documentation/ko-KR/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>

___


##### USB 프린터(프린터 전달) 

Azure에서는 원격 세션에서 사용자가 로컬 프린터 장치에 액세스할 수 있도록 하는 원격 데스크톱 서비스의 기능을 사용할 수 없습니다.

___

> ![Windows][Logo_Windows] Windows
>
> Windows를 사용한 인쇄에 대한 자세한 내용은 여기에서 찾을 수 있습니다. <http://technet.microsoft.com/library/jj590748.aspx>

___

 
#### 프레미스 간에 SAP Azure 시스템을 Correction and Transport System(TMS)에 통합

SAP Change and Transport System(TMS)은 지형 내에서 시스템 간에 전송 요청을 내보내고 가져오도록 구성되어야 합니다. QA(품질 보증) 및 PRD(생산 시스템)은 온-프레미스에 있지만 SAP 시스템의 DEV(개발) 인스턴스는 Azure에 있다고 가정해 보겠습니다. 또한 중앙 전송 디렉터리가 있다고 가정합니다.

##### 전송 도메인 구성
[전송 도메인 컨트롤러 구성](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)에 설명된 대로 전송 도메인 컨트롤러로 지정한 시스템에서 전송 도메인을 구성합니다. 시스템 사용자 TMSADM이 만들어지고 필수 RFC 대상이 생성됩니다. 트랜잭션 SM59를 사용하여 이러한 RFC 연결을 확인할 수 있습니다. 전송 도메인 전체에서 호스트 이름 확인을 사용할 수 있어야 합니다.

방법:

* 이 시나리오에서는 온-프레미스 QAS 시스템을 CTS 도메인 컨트롤러로 결정했습니다. 트랜잭션 STMS를 호출합니다. TMS 대화 상자가 나타납니다. 전송 도메인 구성 대화 상자가 표시됩니다. 이 대화 상자는 전송 도메인을 아직 구성하지 않은 경우에만 나타납니다.
* 자동으로 만들어진 사용자 TMSADM에 권한이 부여되었는지 확인합니다(SM59 -> ABAP 연결 -> TMSADM@E61.DOMAIN\_E61 -> 세부 정보 -> 유틸리티(M) -> 권한 부여 테스트). 여기에 표시된 것처럼 트랜잭션 STMS의 초기 화면에는 이 SAP 시스템이 전송 도메인의 컨트롤러로 작동하는지 표시되어야 합니다.
 
![도메인 컨트롤러의 트랜잭션 STMS 초기 화면][planning-guide-figure-2300]

#### 전송 도메인에 SAP 시스템 포함

전송 도메인에 SAP 시스템을 포함하는 순서는 다음과 같습니다.

* Azure의 DEV 시스템에서 전송 시스템(클라이언트 000)으로 이동한 다음 트랜잭션 STMS를 호출합니다. 대화 상자에서 기타 구성을 선택하고 도메인에 시스템 포함 작업을 계속 진행합니다. 도메인 컨트롤러를 대상 호스트로 지정합니다([전송 도메인에 SAP 시스템 포함](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). 이제 시스템은 전송 도메인에 포함될 때까지 대기합니다.
* 보안상의 이유로 도메인 컨트롤러로 다시 돌아가 사용자 요청을 확인해야 합니다. 대기 중인 시스템의 시스템 개요 및 승인을 선택합니다. 그런 후 메시지를 확인하면 구성이 배포됩니다.

이제 이 SAP 시스템에는 전송 도메인의 다른 모든 SAP 시스템에 대해 필요한 정보가 포함됩니다. 동시에, 새 SAP 시스템의 주소 데이터가 다른 모든 SAP 시스템으로 전송되고 SAP 시스템은 전송 제어 프로그램의 전송 프로필에 입력됩니다. 도메인의 전송 디렉터리에 대한 액세스와 RFC가 작동하는지 여부를 확인합니다.

[시스템 변경 및 전송](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm) 설명서에 설명된 대로 전송 시스템 구성을 계속 진행합니다.

방법:

* 온-프레미스의 STMS가 올바르게 구성되어 있는지 확인합니다.
* 전송 도메인 컨트롤러의 호스트 이름을 Azure에서 가상 컴퓨터로 확인하고 그 반대로도 확인할 수 있는지 검토합니다.
* 트랜잭션 STMS 호출 -> 기타 구성 -> 도메인에 시스템 포함
* 온-프레미스 TMS 시스템의 연결을 확인합니다.
* 전송 경로, 그룹 및 계층을 일반적인 방식으로 구성합니다.

사이트 간에 연결된 프레미스 간 시나리오에서 온-프레미스와 Azure 간의 대기 시간이 여전히 길 수 있습니다. 개발 및 테스트 시스템에서 프로덕션으로 개체를 전송하는 순서를 따르거나 전송 또는 지원 패키지를 다른 시스템에 적용하려는 경우 중앙 전송 디렉터리의 위치에 따라, 일부 시스템에서 중앙 전송 디렉터리에서 데이터를 읽거나 쓸 때 긴 대기 시간이 발생합니다. 이러한 상황은 데이터 센터 간에 멀리 떨어져 있는 서로 다른 데이터 센터에 여러 다른 시스템이 분산되어 있는 SAP 지형 구성과 유사합니다.

이러한 대기 시간 문제를 해결하고 시스템이 더 빠르게 전송 디렉터리에서 읽거나 전송 디렉터리로 쓸 수 있도록 하려면 두 개의 STMS 전송 도메인(온-프레미스용 1개와 Azure의 시스템을 포함하는 1개)을 설정하고 전송 도메인을 연결할 수 있습니다. SAP TMS의 이 개념과 관련된 원리를 설명하는 다음 설명서를 확인하세요. <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>

방법:

* 트랜잭션 STMS를 사용하여 각 위치(온-프레미스 및 Azure)에서 전송 도메인 설정 <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* 도메인 링크를 사용하여 도메인을 연결하고 두 도메인 간의 연결을 확인합니다. <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* 연결된 시스템으로 구성을 배포합니다.

#### Azure 및 온-프레미스에 있는 SAP 인스턴스 간의 RFC 트래픽(프레미스 간)

온-프레미스 및 Azure에 있는 시스템 간에 RFC 트래픽이 작동해야 합니다. 연결을 설정하려면 대상 시스템에 대해 RFC 연결을 정의해야 하는 원본 시스템에서 트랜잭션 SM59를 호출합니다. 해당 구성은 RFC 연결의 표준 설정과 비슷합니다.

프레미스 간 시나리오에서는 서로 통신해야 하는 SAP 시스템을 실행하는 VM이 동일한 도메인에 있다고 가정합니다. 따라서 SAP 시스템 간의 RFC 연결을 설정하는 작업이 온-프레미스 시나리오의 설정 단계 및 입력과 다르지 않습니다.

#### Azure에 있는 SAP 인스턴스의 '로컬' 파일 공유에 액세스하기 또는 그 반대로 액세스하기

Azure에 있는 SAP 인스턴스는 회사 프레미스 내에 있는 파일 공유에 액세스해야 합니다. 또한 온-프레미스 SAP 인스턴스는 Azure에 있는 파일 공유에 액세스해야 합니다. 파일 공유를 사용하도록 설정하려면 로컬 시스템에서 사용 권한 및 공유 옵션을 구성해야 합니다. VPN의 포트 또는 Azure와 데이터 센터 간의 Express 경로 연결을 열어야 합니다.

## 지원 가능성
### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>SAP용 Azure 모니터링 솔루션
Azure에서 업무에 중요한 SAP 시스템의 모니터링을 사용하도록 설정하기 위해 SAP 모니터링 도구인 SAPOSCOL 또는 SAP 호스트 에이전트가 SAP용 Azure 모니터링 확장을 통해 Azure 가상 컴퓨터 서비스 호스트에서 데이터를 가져옵니다. SAP의 요구는 SAP 응용 프로그램에 따라 크게 달라지므로 Microsoft는 일반적으로 Azure에 필요한 기능을 구현하지 않기로 결정했지만, 고객이 Azure에서 실행되는 가상 컴퓨터에 필요한 모니터링 구성 요소와 구성을 배포할 수 있도록 허용합니다. 그러나 모니터링 구성 요소의 배포 및 수명 주기 관리는 대부분 Azure에서 자동으로 수행됩니다.

#### 솔루션 디자인

SAP 모니터링을 사용 가능하도록 하기 위해 개발된 솔루션은 Azure VM 에이전트 및 확장 프레임워크의 아키텍처를 기반으로 합니다. Azure VM 에이전트 및 확장 프레임워크는 VM 내에 있는 Azure VM 확장 갤러리에서 소프트웨어 응용 프로그램을 설치할 수 있도록 허용합니다. 이 개념의 원리는 특수한 기능을 VM에 배포하고 배포 시에 이러한 소프트웨어를 구성할 수 있도록 하는 것입니다(SAP용 Azure 모니터링 확장과 같음).

2014년 2월부터 Azure 포털에서 VM을 생성할 때 기본적으로 VM 내의 특정 Azure VM 확장을 처리할 수 있도록 하는 'Azure VM 에이전트'가 Windows에 주입됩니다. SUSE 또는 Red Hat Linux의 경우 VM 에이전트가 이미 Azure 마켓플레이스 이미지의 일부입니다. Linux VM을 온-프레미스에서 Azure로 업로드하는 경우 VM 에이전트를 수동으로 설치해야 합니다.


Azure에서 SAP용 모니터링 솔루션의 기본 구성 요소는 다음과 같습니다.
 
![Microsoft Azure 확장 구성 요소][planning-guide-figure-2400]

위의 블록 다이어그램에 나와 있는 것처럼 SAP용 모니터링 솔루션의 한 부분은 Azure VM 이미지 및 Azure Operations에 의해 관리되는 전역 복제 리포지토리인 Azure 확장 갤러리에서 호스트됩니다. Azure Operations를 사용하여 SAP용 Azure 모니터링 확장의 새 버전을 게시하는 것은 Azure의 SAP 구현과 관련해서 작업하는 공동 SAP/MS 팀의 책임입니다. 이러한 SAP용 Azure 모니터링 확장 기능은 MAD(Microsoft Azure 진단) 확장 또는 LAD(Linux Azure 진단)를 사용하여 필요한 정보를 가져옵니다.

새 Windows VM을 배포하는 경우 'Azure VM 에이전트'가 VM에 자동으로 추가됩니다. 이 에이전트의 기능은 SAP NetWeaver Systems의 모니터링을 위해 Azure 확장의 로드 및 구성을 조정하는 것입니다. Linux VM의 경우 Azure VM 에이전트가 이미 Azure 마켓플레이스 OS 이미지의 일부입니다.

그러나 고객이 실행해야 하는 단계는 여전히 남아 있습니다. 바로 성능 컬렉션의 활성화 및 구성입니다. '구성' 관련 프로세스는 PowerShell 스크립트 또는 CLI 명령에 의해 자동화됩니다. [배포 가이드][deployment-guide]에 설명된 대로 Microsoft Azure 스크립트 센터에서 PowerShell 스크립트를 다운로드할 수 있습니다.

SAP용 Azure 모니터링 솔루션의 전반적인 아키텍처는 다음과 같습니다.
 
![SAP NetWeaver용 Azure 모니터링 솔루션][planning-guide-figure-2500]

**배포 동안 이러한 PowerShell cmdlet 또는 CLI 명령을 사용하는 정확한 방법 및 자세한 단계는 [배포 가이드][deployment-guide]에 제공된 지침을 따르세요.**

### Azure 배치 SAP 인스턴스를 SAProuter에 통합

Azure에서 실행되는 SAP 인스턴스도 SAProuter에서 액세스할 수 있어야 합니다.
 
![SAP-라우터 네트워크 연결][planning-guide-figure-2600]

SAProuter를 사용하면 직접적인 IP 연결이 없는 경우에도 참여 시스템 간에 TCP/IP 통신이 가능해집니다. 이를 통해 네트워크 수준에서 통신 파트너 간의 종단 간 연결이 없어도 된다는 이점을 얻게 됩니다. SAProuter는 기본적으로 3299 포트에서 수신 대기합니다. SAProuter를 통해 SAP 인스턴스를 연결하려면 연결을 시도할 때 SAProuter 문자열과 호스트 이름을 지정해야 합니다.

## SAP NetWeaver AS Java

지금까지 이 문서는 일반적으로 SAP NetWeaver 또는 SAP NetWeaver ABAP 스택에 중점을 두었습니다. 이 작은 섹션에서는 SAP Java 스택에 대한 특별한 고려 사항이 표시됩니다. SAP NetWeaver Java만을 기반으로 하는 가장 중요한 응용 프로그램 중 하나는 SAP Enterprise Portal입니다. SAP PI 및 SAP Solution Manager와 같은 기타 SAP NetWeaver 기반 응용 프로그램은 SAP NetWeaver ABAP 및 Java 스택을 모두 사용합니다. 따라서 SAP NetWeaver Java 스택과 관련된 특정 측면도 반드시 고려해야 합니다.

### SAP Enterprise Portal

프레미스 간 시나리오에서 배포하는 경우 Azure 가상 컴퓨터의 SAP Portal 설정은 온-프레미스 설치와 다르지 않습니다. DNS는 온-프레미스에 의해 수행되므로 개별 인스턴스의 포트 설정은 구성된 온-프레미스로 수행될 수 있습니다. 지금까지 이 문서에 설명된 권장 사항 및 제한 사항은 일반적으로 SAP Enterprise Portal 또는 SAP NetWeaver Java 스택과 같은 응용 프로그램에 적용됩니다.

![공개된 SAP 포털][planning-guide-figure-2700]

가상 컴퓨터 호스트가 사이트 간 VPN 터널 또는 Express 경로 통해 회사 네트워크에 연결되어 있는 동안 일부 고객의 특별한 배포 시나리오에서 SAP Enterprise Portal이 인터넷에 직접 노출됩니다. 이러한 시나리오의 경우 특정 포트가 열려 있고 방화벽이나 네트워크 보안 그룹에 의해 차단되지 않았는지 확인해야 합니다. 클라우드 전용 시나리오에서 온-프레미스에서 SAP Java 인스턴스로 연결하려는 경우에도 동일한 방식이 적용되어야 합니다.

초기 포털 URI는 http(s):`<Portalserver`>:5XX00/irj입니다. 여기서 포트는 50000 + (Systemnumber × 100)으로 구성됩니다. SAP 시스템 00의 기본 포털 URI는 `<dns name`>.`<azure region`>.Cloudapp.azure.com:PublicPort/irj입니다. 자세한 내용은 <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>의 내용을 참조하세요.
 
![끝점 구성][planning-guide-figure-2800]

SAP Enterprise Portal의 URL 및/또는 포트를 사용자 지정하려는 경우 다음 설명서를 확인하세요.

* [포털 URL 변경](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [기본 포트 번호, 포털 포트 번호 변경](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)


## Azure 가상 컴퓨터에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구)
### 용어 정의

용어 **HA(고가용성)**은 일반적으로 **동일한** 데이터 센터 내의 중복성, 내결함성 또는 장애 조치(Failover) 보호 구성 요소를 통해 IT 서비스의 비즈니스 연속성을 제공함으로써 IT 작업 중단을 최소화하는 일련의 기술과 관련되어 있습니다. 우리의 경우에는 단일 Azure 지역 내부에서의 고가용성이 고려됩니다.

**DR(재해 복구)** 또한 IT 서비스 중단을 최소화하고 복구를 수행하는 것을 목표로 하지만 일반적으로 수백 킬로미터 거리에 있는 **여러 다른** 데이터 센터 간에 발생하게 됩니다. 우리의 경우에는 동일한 지정학적 지역 내의 다양한 Azure 지역 간이나 고객이 설정한 Azure 지역 사이를 의미합니다.

### 고가용성 개요
Azure의 SAP 고가용성에 대한 논의는 다음 두 부분으로 구분할 수 있습니다.

* **Azure 인프라 고가용성** - 예를 들어 계산(VM), 네트워크, 저장소 등의 HA와 SAP 응용 프로그램 가용성에 주는 이점
* **SAP 응용 프로그램 고가용성** - 예를 들어 SAP 소프트웨어 구성 요소 HA:
	* SAP 응용 프로그램 서버
	* SAP ASCS/SCS 인스턴스
	* DB 서버

및 Azure 인프라 HA와 결합되는 방식

Azure의 SAP 고가용성은 온-프레미스 물리적 또는 가상 환경의 SAP 고가용성에 비해 몇 가지 차이점이 있습니다. SAP의 다음 백서에서는 가상화된 Windows 환경의 표준 SAP 고가용성 구성에 대해 설명합니다. <http://scn.sap.com/docs/DOC-44415> Windows의 경우와 마찬가지로 Linux용 sapinst 통합 SAP-HA 구성은 없습니다. Linux용 온-프레미스 SAP HA에 대한 자세한 내용은 여기에서 찾을 수 있습니다. <http://scn.sap.com/docs/DOC-8541>

### Azure 인프라 고가용성
현재 Azure 가상 컴퓨터에서 사용할 수 있는 단일 VM SLA는 없습니다. 단일 VM의 가용성을 어떻게 나타낼 수 있는지 이해하기 위해 사용 가능한 여러 Azure SLA 제품을 간단히 빌드할 수 있습니다. <https://azure.microsoft.com/support/legal/sla/>

이 계산은 한 달이 30일 또는 43200분이라는 기준에서 출발합니다. 따라서 가동 중지 시간 0.05%는 21.6분에 해당합니다. 일반적으로 다양한 서비스의 가용성은 다음과 같이 배가됩니다.

(가용성 서비스 #1/100) * (가용성 서비스 #2/100) * (가용성 서비스 #3/100) *...

결과는 다음과 같습니다.

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 또는 전체 가용성 99.75%

#### VM(가상 컴퓨터) 고가용성

가상 컴퓨터의 가용성에 영향을 줄 수 있는 두 가지 유형의 Azure 플랫폼 이벤트인 계획된 유지 관리와 계획되지 않은 유지 관리가 있습니다.

* 계획된 유지 관리 이벤트는 가상 컴퓨터가 실행 중인 플랫폼 인프라의 전체적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft가 기본 Azure 플랫폼에 적용하는 주기적인 업데이트입니다.
* 계획되지 않은 유지 관리 이벤트는 가상 컴퓨터의 기반이 되는 하드웨어 또는 물리적 인프라에 어떠한 식으로든지 오류가 있을 때 발생합니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지될 때 Azure 플랫폼은 가상 컴퓨터를 호스트 중인 비정상 물리적 서버에서 정상 물리적 서버로 가상 컴퓨터를 자동으로 마이그레이션합니다. 이러한 이벤트는 흔치 않지만 가상 컴퓨터가 재부팅되도록 할 수도 있습니다.

이 설명서에서 자세한 내용을 찾을 수 있습니다. <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### Azure 저장소 중복성

Microsoft Azure 저장소 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생하는 경우에도 Azure 저장소 SLA를 충족합니다.

Azure 저장소는 기본적으로 데이터 이미지 3개를 유지하므로 여러 Azure 디스크에 있는 RAID5 또는 RAID1은 필요하지 않습니다.

이 문서에서 자세한 내용을 찾을 수 있습니다. <http://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### Azure 인프라 VM 다시 시작을 활용하여 SAP 응용 프로그램의 "고가용성" 확보

WSFC(Windows 서버 장애 조치 클러스터링) 같은 기능이나 Linux의 비슷한 기능(SAP 소프트웨어와 연계해서 Azure에서는 아직 지원되지 않음)을 사용하지 않기로 결정하는 경우 Azure VM 다시 시작 기능을 활용하여 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 예정되었거나 갑작스러운 가동 중지 시간으로부터 SAP 시스템을 보호합니다.
 
> [AZURE.NOTE] Azure VM 다시 시작 기능은 응용 프로그램이 아닌 VM을 일차적으로 보호합니다. VM 다시 시작 기능은 SAP 응용 프로그램에 대해 고가용성을 제공하지 않지만 특정 수준의 인프라 가용성을 제공하므로 간접적으로 SAP 시스템의 "고가용성"을 제공합니다. 또한 예정되었거나 갑작스러운 호스트 중단 후 VM을 다시 시작하는 데 걸리는 시간에 대한 SLA는 없습니다. 따라서 이러한 방식의 '고가용성'은 (A)SCS 또는 DBMS와 같은 SAP 시스템의 중요한 구성 요소에 적합하지 않습니다.

고가용성을 위한 또 다른 중요한 인프라 요소는 저장소입니다. 예: Azure 저장소 SLA는 99,9%의 가용성을 자랑합니다. 디스크가 있는 모든 VM을 단일 Azure 저장소 계정에 배포하는 경우 Azure 저장소를 사용할 수 없게 되어 해당 Azure 저장소 계정에 배치된 모든 VM을 사용할 수 없게 되고 해당 VM 내에서 실행되는 모든 SAP 구성 요소로 사용할 수 없게 될 수 있습니다.

모든 VM을 단일 Azure 저장소 계정에 두는 대신, 각 VM에 대해 전용 저장소 계정을 사용할 수 있습니다. 이러한 방식에서는 여러 독립적인 Azure 저장소 계정을 사용하여 전반적인 VM 및 SAP 응용 프로그램 가용성이 증가하게 됩니다.

Azure 인프라 HA를 사용하는 SAP NetWeaver 시스템의 샘플 아키텍처는 다음과 같습니다.
 
![HA Azure 인프라를 활용하여 SAP 응용 프로그램의 "더 높은" 가용성 확보][planning-guide-figure-2900]

중요한 SAP 구성 요소에 대해 현재까지 다음이 지원됩니다.

* SAP AS(응용 프로그램 서버)의 고가용성

SAP 응용 프로그램 서버 인스턴스는 중복 구성 요소입니다. 각 SAP AS 인스턴스는 자체 VM에 배포되고, 다른 Azure 장애 및 업그레이드 도메인에서 실행됩니다([장애 도메인][planning-guide-3.2.1] 및 [업그레이드 도메인][planning-guide-3.2.2] 챕터 참조). 이 기능은 Azure 가용성 집합을 사용하면 보장됩니다([Azure 가용성 집합][planning-guide-3.2.3] 챕터 참조) Azure 결함 또는 업그레이드 도메인을 예정에 따라 또는 갑작스럽게 사용할 수 없게 되면 SAP AS 인스턴스에서 제한된 수의 VM을 사용할 수 없게 됩니다. 각 SAP AS 인스턴스는 자체 Azure 저장소 계정에 배치됩니다. Azure 저장소 계정 하나를 사용할 수 없게 되면 해당 SAP AS가 있는 단일 VM을 사용할 수 없게 됩니다. 그러나 단일 Azure 구독 내에서는 Azure 저장소 계정 수가 제한됩니다. VM을 다시 부팅한 후 (A)SCS 인스턴스가 자동으로 시작되게 하려면 [SAP 인스턴스에 대해 자동 시작 사용][planning-guide-11.5] 챕터에 설명된 대로 (A)SCS 인스턴스의 시작 프로필에 Autostart 매개 변수를 설정해야 합니다. 또한 [SAP 응용 프로그램 서버에 대한 고가용성][planning-guide-11.4.1] 챕터에서 자세한 내용을 참조하세요.

* SAP (A)SCS 인스턴스의 _더 높은_ 가용성
 
여기서는 Azure VM 다시 시작 기능을 활용하여 SAP (A)SCS 인스턴스가 설치된 VM을 보호합니다. Azure 서버의 예정되었거나 갑작스러운 가동 중지 시간의 경우 VM은 사용 가능한 다른 서버에서 다시 시작됩니다. 앞서 언급한 것처럼 Azure VM 다시 시작 기능은 응용 프로그램(이 경우 (A)SCS 인스턴스)이 아닌 VM을 주로 보호합니다. VM 다시 시작을 통해 SAP (A)SCS 인스턴스의 "높은 가용성"에 간접적으로 도달하게 됩니다. VM을 다시 부팅한 후 (A)SCS 인스턴스가 자동으로 시작되게 하려면 [SAP 인스턴스에 대해 자동 시작 사용][planning-guide-11.5] 챕터에 설명된 대로 (A)SCS 인스턴스의 시작 프로필에 Autostart 매개 변수를 설정해야 합니다. 즉, 단일 VM에서 SPOF(단일 실패 지점)로 실행되는 (A)SCS 인스턴스는 전체 SAP 지형의 가용성을 결정하는 요소가 됩니다.

* DBMS 서버의 _높은_ 가용성

여기서는 SAP (A)SCS 인스턴스 사용 사례와 마찬가지로 Azure VM 다시 시작을 활용하여 DBMS 소프트웨어가 설치된 VM을 보호하고, VM 다시 시작을 통해 DBMS 소프트웨어의 "높은 가용성"을 획득합니다. 단일 VM에서 실행되는 DBMS는 SPOF이기도 하며 전체 SAP 지형의 가용성을 결정하는 중요한 요소가 됩니다.

### Azure IaaS의 SAP 응용 프로그램 고가용성
전체 SAP 시스템의 고가용성을 달성하려면 모든 중요 SAP 시스템 구성 요소(예: 중복 SAP 응용 프로그램 서버), SAP (A)SCS 인스턴스 및 DBMS와 같은 고유한 구성 요소(예: 단일 실패 지점)를 보호해야 합니다.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP 응용 프로그램 서버에 대한 고가용성
SAP 응용 프로그램 서버/대화 상자 인스턴스의 경우 특정 고가용성 솔루션을 고려할 필요가 없습니다. 고가용성은 단순히 중복성에 의해 획득되므로 다른 가상 컴퓨터에서 충분히 확보될 수 있습니다. 예정된 유지 관리 가동 중지와 동시에 VM이 업데이트되지 않도록 하려면 모두 동일한 Azure 가용성 집합에 두어야 합니다. Azure 배율 단위 내의 여러 다른 업그레이드 도메인 및 장애 도메인에서 빌드되는 기본 기능은 이미 [업그레이드 도메인][planning-guide-3.2.2] 챕터에서 살펴보았습니다. Azure 가용성 집합은 이 문서의 [Azure 가용성 집합][planning-guide-3.2.3] 챕터에 설명되어 있습니다.

Azure 배율 단위 내에서 Azure 가용성 집합이 사용할 수 있는 장애 도메인 및 업그레이드 도메인 수는 제한되어 있습니다. 즉, 둘 이상의 VM이 결과적으로 동일한 장애 또는 업그레이드 도메인에 포함된다는 사실을 고려할 때 조만간 많은 수의 VM을 하나의 가용성 집합에 포함시키게 될 것입니다.

전용 VM에서 일부 SAP 응용 프로그램 서버 인스턴스를 배포하고 업그레이드 도메인을 5개 받았다고 가정하면 결과적으로 다음과 같은 그림이 완성됩니다. 가용성 집합 내의 장애 및 업그레이드 도메인의 실제 최대 개수는 나중에 변경될 수 있습니다.
 
![Azure에 있는 SAP 응용 프로그램 서버의 HA][planning-guide-figure-3000]

이 설명서에서 자세한 내용을 찾을 수 있습니다. <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>


#### Windows에서 SAP (A)SCS 인스턴스의 고가용성

WSFC(Windows Server 장애 조치 클러스터)는 SAP (A)SCS 인스턴스를 보호하기 위해 자주 사용되는 솔루션입니다. 이 솔루션은 "HA 설치"의 형태로 sapinst에도 통합되어 있습니다. 현재 Azure 인프라는 온-프레미스의 경우와 동일한 방식으로 필수 Windows Server 장애 조치 클러스터를 설정하는 기능을 제공할 수 없습니다.

2016년 1월을 기준으로 Windows 운영 체제를 실행하는 Azure 클라우드 플랫폼에서 두 개의 Azure VM 간에 공유되는 디스크에서 클러스터 공유 볼륨을 사용할 수 없습니다.

그렇지만 올바른 해결 방법은 WSFC에 통합될 수 있는 동기식의 투명한 디스크 복제에 의해 공유 볼륨을 제공하는 타사 소프트웨어를 사용하는 것입니다. 이 방법은 활성 클러스터 노드만 특정 시점에 디스크 복사본 중 하나에 액세스할 수 있음을 의미합니다. 2016년 1월을 기준으로 타사 소프트웨어인 SIOS DataKeeper와 함께 Azure VM의 Windows 게스트 OS에 있는 SAP (A)SCS 인스턴스를 보호하기 위해 이 HA 구성이 지원됩니다.

SIOS DataKeeper 솔루션은 다음을 준비하여 Windows 장애 조치(Failover) 클러스터에 공유 디스크 클러스터 리소스를 제공합니다.

* Windows 클러스터 구성에 있는 각 VM(가상 컴퓨터)에 연결된 추가 Azure VHD
* 두 VM 노드에서 실행되는 SIOS DataKeeper Cluster Edition
* 원본 VM의 추가 VHD 연결 볼륨의 콘텐츠를 대상 VM의 추가 VHD 연결 볼륨에 동기식으로 미러링하는 방식으로 SIOS DataKeeper Cluster Edition 구성.
* SIOS DataKeeper는 원본 및 대상 로컬 볼륨을 추상화한 다음 Windows 장애 조치(Failover) 클러스터에 단일 공유 디스크로 제공합니다.
 
SIOS Datakeeper 및 SAP를 사용하여 Windows 장애 조치 클러스터를 설치하는 방법에 대한 자세한 내용은 [SIOS Datakeeper와 함께 Azure에서 Windows Server 장애 조치(Failover) 클러스터를 사용하여 SAP ASCS 인스턴스 클러스터링][ha-guide-classic] 백서를 참조하세요.

#### Linux에서 SAP (A)SCS 인스턴스의 고가용성
 
2015년 12월을 기준으로 Azure에서 Linux VM용 공유 디스크 WSFC에 해당하는 기능이 제공되지 않습니다. Azure의 Linux에서 SAP를 실행하는 경우에 SIOS for Windows와 같은 타사 소프트웨어를 실행해도 괜찮은지는 아직 검증되지 않았습니다.



#### SAP 데이터베이스 인스턴스의 고가용성
일반적인 SAP DBMS HA 설치는 DBMS 고가용성 기능이 활성 DBMS 인스턴스에서 두 번째 VM의 수동 DBMS 인스턴스로 데이터를 복제하는 데 사용되는 경우 두 개의 DBMS VM을 기반으로 합니다.

일반적인 DBMS 및 특정 DBMS에 대한 고가용성 및 재해 복구 기능은 [DBMS 배포 가이드][dbms-guide]에 설명되어 있습니다.


#### 전체 SAP 시스템에 대한 종단 간 고가용성

Azure의 전체 SAP NetWeaver HA 아키텍처의 두 가지 예는 Windows용과 Linux용입니다. 아래에 설명된 개념은 많은 SAP 시스템을 배포하고 배포된 VM 수가 구독당 저장소 계정의 최대 제한을 초과할 경우 약간의 조정이 필요할 수 있습니다. 이러한 경우 VM의 VHD를 단일 저장소 계정 내에 결합해야 합니다. 일반적으로는 이를 위해 여러 다른 SAP 시스템에 있는 SAP 응용 프로그램 계층 VM의 VHD를 결합할 수 있습니다. 여기서는 서로 다른 SAP 시스템에 있는 여러 DBMS VM의 다른 VHD를 단일 Azure 저장소 계정에 결합했습니다. Azure 저장소 계정의 IOPS 제한에 유의하세요(<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>).

##### ![Windows][Logo_Windows] Windows의 HA

![Azure IaaS의 SQL Server를 사용한 SAP NetWeaver 응용 프로그램 HA 아키텍처][planning-guide-figure-3200]

다음 Azure 구성은 인프라 문제 및 호스트 패치에 의한 영향을 최소화하기 위해 SAP NetWeaver 시스템에서 사용됩니다.

* 전체 시스템이 Azure에 배포됩니다(필수 - DBMS 계층, (A)SCS 인스턴스 및 전체 응용 프로그램 계층이 같은 위치에서 실행되어야 함).
* 전체 시스템이 단일 Azure 구독 내에서 실행됩니다(필수).
* 전체 시스템이 단일 Azure 가상 네트워크 내에서 실행됩니다(필수).
* 모든 VM이 동일한 가상 네트워크에 있는 경우에도 단일 SAP 시스템의 VM을 3개의 가용성 집합으로 구분할 수 있습니다.
* 단일 SAP 시스템의 DBMS 인스턴스를 실행하는 모든 VM이 하나의 가용성 집합에 있습니다. SQL Server AlwaysOn 또는 Oracle Data Guard와 같은 네이티브 DBMS 고가용성 기능이 사용되므로 시스템당 둘 이상의 VM에서 DBMS 인스턴스가 실행된다고 가정할 수 있습니다.
* DBMS 인스턴스를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. DBMS 데이터 및 로그 파일은 데이터를 동기화하는 DBMS 고가용성 기능을 사용하여 한 저장소 계정에서 다른 저장소 계정으로 복제됩니다. 하나의 저장소 계정을 사용할 수 없는 경우 전체 SQL Server 서비스가 아니라 SQL Windows 클러스터 노드 하나만 사용할 수 없게 됩니다.
* 단일 SAP 시스템의 (A)SCS 인스턴스를 실행하는 모든 VM이 하나의 가용성 집합에 있습니다. 해당 VM의 내부에서 WSFC(Windows Sever 장애 조치 클러스터)가 (A)SCS 인스턴스를 보호하도록 구성됩니다.
* (A)SCS 인스턴스를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. (A)SCS 인스턴스 파일 및 SAP 전역 폴더는 SIOS DataKeeper 복제를 사용하여 한 저장소 계정에서 다른 저장소 계정으로 복제됩니다. 하나의 저장소 계정을 사용할 수 없는 경우 전체 (A)SCS 서비스가 아니라 (A)SCS Windows 클러스터 노드 하나만 사용할 수 없게 됩니다.
* SAP 응용 프로그램 서버 계층을 나타내는 모든 VM은 세 번째 가용성 집합에 있습니다.
* SAP 응용 프로그램 서버를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. 저장소 계정 하나를 사용할 수 없으면 하나의 SAP 응용 프로그램 서버를 사용할 수 없게 되며 다른 SAP AS는 계속 실행됩니다.

##### ![Linux][Logo_Linux] Linux의 HA

Azure의 Linux에 대한 SAP HA 아키텍처는 기본적으로 위에 설명된 것처럼 Windows의 경우와 동일합니다. 2016년 1월을 기준으로 다음과 같은 두 가지 제한 사항이 적용됩니다.

* 어떤 ASE 복제 기능도 없는 SAP ASE 16만 Azure의 Linux에서 지원됩니다.
* SAP (A)SCS HA 솔루션은 아직Azure의 Linux에서 지원되지 않습니다.

결과적으로 2016년 1월을 기준으로 (A)SCS 인스턴스 및 단일 인스턴스 SAP ASE 데이터베이스에 대한 HA가 없으므로 SAP-Linux-Azure 시스템은 SAP-Windows-Azure 시스템과 동일한 가용성을 제공할 수 없습니다.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>SAP 인스턴스에 대해 자동 시작 사용

SAP는 VM 내에서 운영 체제가 시작된 후 즉시 SAP 인스턴스를 시작하기 위한 기능을 제공합니다. 정확한 단계는 SAP 기술 자료 문서 [1909114] - 매개 변수 Autostart를 사용하여 SAP 인스턴스를 자동으로 시작하는 방법에 나와 있습니다. 그러나 SAP는 인스턴스 다시 시작 순서가 제어되지 않고 둘 이상의 VM이 영향을 받거나 VM당 여러 인스턴스가 실행되었다고 가정하므로 이러한 설정을 사용하는 것을 더 이상 권장하지 않습니다. VM 하나에 단일 SAP 응용 프로그램 서버 인스턴스가 있는 일반적인 Azure 시나리오와 단일 VM이 다시 시작되는 경우를 가정하면, 자동 시작은 실제로 중요하지 않으며 이 매개 변수를 다음에 추가하여 사용되도록 설정할 수 있습니다.

	Autostart = 1

SAP ABAP 및/또는 Java 인스턴스의 시작 프로필

> [AZURE.NOTE] 
Autostart 매개 변수에도 일부 단점이 있을 수 있습니다. 구체적으로 말하면, 이 매개 변수는 인스턴스의 관련 Windows/Linux 서비스가 시작될 때 SAP ABAP 또는 Java 인스턴스가 시작되도록 트리거합니다. 운영 체제가 부팅되는 경우에 확실히 여기에 해당됩니다. 그러나 SAP 서비스를 다시 시작하는 것도 SUM이나 기타 업데이트 또는 업그레이드와 같은 SAP 소프트웨어 수명 주기 관리 기능의 일반적인 측면에 해당합니다. 이러한 기능에서 항상 인스턴스가 자동으로 다시 시작될 것으로 예상되지는 아닙니다. 따라서 이러한 작업을 실행하기 전에 Autostart 매개 변수를 사용하지 않도록 설정해야 합니다. Autostart 매개 변수를 ASCS/SCS/CI 같이 클러스터링되는 SAP 인스턴스에는 사용하지 말아야 합니다.

SAP 인스턴스의 자동 시작과 관련된 자세한 내용은 다음을 참조하세요.

* [Unix 서버 시작/중지에 따른 SAP 시작/중지](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [SAP NetWeaver 관리 에이전트 시작 및 중지](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [HANA 데이터베이스의 자동 시작을 사용하도록 설정하는 방법](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)


### 대형 3계층 SAP 시스템
3계층 SAP 구성의 고가용성 측면은 이전 섹션에서 이미 설명되었습니다. 그러나 DBMS 서버 요구 사항이 너무 커서 Azure에 포함할 수 없는 시스템의 경우는 어떨까요? SAP 응용 프로그램 계층을 Azure에 배포할 수 있을까요?

#### 3계층 SAP 구성의 위치

응용 프로그램 계층 자체를 분할하거나 응용 프로그램 및 DBMS 계층을 온-프레미스와 Azure 간에 분할하는 것은 지원되지 않습니다 SAP 시스템은 온-프레미스 또는 Azure에 완전히 배포됩니다. 또한 일부 응용 프로그램 서버는 온-프레미스에 두고 다른 응용 프로그램 서버는 Azure에 둘 수도 없습니다. 이것이 바로 논의를 시작할 부분입니다. 또한 SAP 시스템의 DBMS 구성 요소와 SAP 응용 프로그램 서버 계층을 두 개의 서로 다른 Azure 지역에 배포하는 것도 지원되지 않습니다. 예: 미국 서부의 DBMS 및 미국 중부의 SAP 응용 프로그램 계층 이러한 구성을 지원하지 않는 이유는 SAP NetWeaver 아키텍처의 대기 시간 민감도 때문입니다.

그러나 작년에 관련 작업을 거치면서 데이터 센터 파트너가 Azure 지역에 대한 공동 위치를 개발했습니다. 이러한 공동 위치는 대개 Azure 지역 내의 실제 Azure 데이터 센터와 매우 가깝습니다. Azure에 대한 Express 경로를 사용해서 공동 위치에 짧은 거리를 유지하면서 자산을 연결하면 대기 시간이 2ms보다 적어질 수 있습니다. 이러한 경우 공동 위치에 DBMS 계층(SAN/NAS 저장소 포함)을 배치하고 Azure에 SAP 응용 프로그램 계층을 배치할 수 있습니다. 2015년 12월을 기준으로 이와 같은 배포는 더 이상 진행되지 않고 있습니다. 하지만 SAP 이외의 응용 프로그램 배포를 사용하는 다른 고객들은 이러한 접근 방식을 이미 사용하고 있습니다.

### SAP 시스템의 오프라인 백업

선택한 SAP 구성(2계층 또는 3계층)에 따라, 백업이 필요할 수도 있습니다. 데이터베이스의 백업 외에 VM 자체의 내용도 있습니다. DBMS 관련 백업은 데이터베이스 메서드로 수행됩니다. 여러 다른 데이터베이스에 대한 자세한 설명은 [DBMS 가이드][dbms-guide]에서 찾을 수 있습니다. SAP 데이터는 이 섹션에 설명되는 오프라인 방식으로(데이터베이스 콘텐츠도 포함) 또는 다음 섹션에 설명되는 온라인 방식으로 백업될 수 있습니다.

오프라인 백업에서는 기본적으로 Azure 포털을 통해 VM 종료해야 하고 VM에 연결된 모든 VHD 외의 기본 VM 디스크 복사본이 있어야 합니다. 이 경우 VM 및 관련 디스크의 지정 시간 이미지가 보존됩니다. '백업'을 다른 Azure 저장소 계정으로 복사하는 것이 좋습니다. 따라서 이 문서의 [Azure 저장소 계정 간에 디스크 복사][planning-guide-5.4.2] 챕터에 설명된 절차가 적용됩니다. Azure 포털을 사용한 종료 외에도, <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>에 설명된 것처럼 Powershell 또는 CLI를 통해 종료할 수도 있습니다.

해당 상태가 복원되면 기본 VM 뿐만 아니라 기본 VM의 원래 디스크, 탑재된 VHD가 삭제되고, 저장된 VHD를 원래 저장소 계정으로 다시 복사한 다음 시스템이 다시 배포됩니다. 이 문서에서는 Powershell에서 이 프로세스를 스크립팅하는 방법의 예를 보여 줍니다. <http://www.westerndevs.com/azure-snapshots/>

위에 설명된 대로 VM 백업을 복원하여 새 하드웨어 키를 만든 후에는 새 SAP 라이선스를 설치해야 합니다.

### SAP 시스템의 온라인 백업

[DBMS 가이드][dbms-guide]에 설명된 대로 DBMS의 백업은 DBMS 관련 방법을 사용하여 수행합니다.

SAP 시스템 내의 다른 VM은 Azure 가상 컴퓨터 백업 기능을 사용하여 백업할 수 있습니다. Azure 가상 컴퓨터 백업은 2015년 초반에 도입된 이후 Azure에서 전체 VM을 백업하는 표준 방법이 되었습니다. Azure 백업은 Azure에 백업을 저장하고 VM을 다시 복원할 수 있도록 합니다.

> [AZURE.NOTE] 
2015년 12월을 기준으로 VM 백업은 SAP 라이선스에 사용되는 고유한 VM ID를 보존하지 않습니다. 즉, 복원된 VM은 저장된 이전 VM을 대신하는 것이 아니라 새 VM으로 간주되므로 VM 백업에서 복원하려면 새 SAP 라이선스 키를 설치해야 합니다. 2016년 1월을 기준으로 Azure VM 백업은 Azure Resource Manager를 통해 배포되는 VM을 지원하지 않습니다.

> ![Windows][Logo_Windows] Windows
>
> 이론적으로 DBMS 시스템에서 Windows VSS(Volume Shadow Copy Service - <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>)를 지원하는 경우 데이터베이스를 실행하는 VM을 SQL Server 등 일관성 있는 방법으로 백업할 수 있습니다. 그러나 데이터베이스의 Azure VM 백업 기반의 특정 시간 복원은 가능하지 않습니다. 따라서 Azure VM 백업을 사용하지 않고 DBMS 기능을 사용하여 데이터베이스 백업을 수행하는 것이 좋습니다.
>
> Azure 가상 컴퓨터 백업에 익숙해지려면 여기에서 시작하세요. <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>
>
> Azure VM에 설치된 Microsoft Data Protection Manager와 Azure 백업을 함께 사용하여 데이터베이스 백업/복원할 수도 있습니다. 자세한 내용은 여기 <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>를 참조하세요.


> ![Linux][Logo_Linux] Linux
> 
> Linux에는 Windows VSS와 동일한 기능이 없습니다. 따라서 파일 일치 백업만 가능하고 응용 프로그램 일치 백업은 가능하지 않습니다. SAP DBMS 백업은 DBMS 기능을 사용해서 수행해야 합니다. SAP 관련 데이터가 포함된 파일 시스템은 여기에 설명된 것과 같이 tar 등의 기능을 사용하여 저장할 수 있습니다. <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>


### 프로덕션 SAP 지형에 대한 DR 사이트로 사용되는 Azure

2014년 중순부터 Hyper-V, System Center 및 Azure와 관련한 다양한 구성 요소가 확장되면서 Azure를 Hyper-V를 기준으로 온-프레미스에서 실행되는 VM에 대한 DR 사이트로 사용할 수 있게 되었습니다.

이 솔루션을 배포하는 방법을 자세하게 설명하는 블로그는 다음에 설명되어 있습니다. <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## 요약
Azure의 SAP 시스템 고가용성의 핵심 사항은 다음과 같습니다.

* 현재 온-프레미스 배포의 경우와 정확히 동일한 방식으로 SAP 단일 실패 지점을 보호할 수는 없습니다. 그 이유는 타사 소프트웨어를 사용하지 않을 경우 아직 Azure에서 공유 디스크 클러스터를 구축할 수 없기 때문입니다.
* DBMS 계층의 경우 공유 디스크 클러스터 기술에 의존하지 않는 DBMS 기능을 사용해야 합니다. 자세한 내용은 [DBMS 가이드][dbms-guide]를 참조하세요.
* Azure 인프라의 장애 도메인 문제 또는 호스트 유지 관리의 영향을 최소화하려면 Azure 가용성 집합을 사용해야 합니다.
	* SAP 응용 프로그램 계층에 대한 가용성 집합을 하나 유지하는 것이 좋습니다.
	* SAP DBMS 계층에 대한 별도의 가용성 집합을 유지하는 것이 좋습니다.
	* 다른 SAP 시스템의 VM에 대해 동일한 가용성 집합을 적용하는 것은 바람직하지 않습니다.
* SAP DBMS 계층의 백업 목적을 보려면 [DBMS 가이드][dbms-guide]를 참조하세요.
* 간단한 대화 상자 인스턴스를 재배포하는 것이 더 빠르므로 SAP 대화 상자 인스턴스를 백업하는 것은 거의 의미가 없습니다.
* SAP 시스템의 전체 디렉터리를 포함하는 VM과 다양한 인스턴스의 모든 프로필을 백업하는 것은 도움이 되며 Windows 백업(또는 Linux의 tar)을 사용하여 수행해야 합니다. Windows Server 2008(R2) 및 Windows Server 2012(R2) 간에는 차이가 있으며 좀 더 최신의 Windows Server 릴리스를 사용하여 백업하는 것이 더 쉬우므로 Windows Server 2012(R2)를 Windows 게스트 운영 체제로 실행하는 것이 좋습니다.

<!---HONumber=AcomDC_0907_2016-->