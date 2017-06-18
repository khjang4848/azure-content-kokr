<properties
   pageTitle="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드 | Microsoft Azure"
   description="Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드"
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


# Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드

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
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
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

이 가이드는 Microsoft Azure에서의 SAP 소프트웨어 구현 및 배포에 대한 설명서의 일부입니다. 이 가이드를 읽기 전에 [계획 및 구현 가이드][planning-guide]를 읽어보세요. 이 문서에서는 Azure IaaS(서비스 제공 인프라) 기능을 사용하는 Microsoft Azure VM(가상 컴퓨터)에서 SAP와 함께 다양한 RDBMS(관계형 데이터베이스 관리 시스템) 및 관련 제품을 배포하는 방법에 대해 설명합니다.

이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## 일반적인 고려 사항
이 챕터에서는 Azure VM에서 SAP 관계형 DBMS 시스템을 실행할 때의 고려 사항을 소개합니다. 이 챕터에는 특정 DBMS 시스템에 대한 참조가 거의 없습니다. 대신 이 챕터를 마친 후 이 문서 내에서 특정 DBMS 시스템을 다룹니다.

### 사전 정의
문서 전체에서 다음과 같은 용어를 사용 합니다.

* IaaS: 서비스 제공 인프라
* PaaS: Platform as a Service
* SaaS: Software as a Service
* SAP 구성 요소: ECC, BW, Solution Manager 또는 EP 등의 개별 SAP 응용 프로그램입니다. SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 응용 프로그램을 기반으로 사용할 수 있습니다.
* SAP 환경: 하나 이상의 SAP 구성 요소가 논리적으로 그룹화되어 개발, QAS, 학습, DR 또는 프로덕션과 같은 비즈니스 기능을 수행합니다.
* SAP 배경: 고객 IT 환경의 전체 SAP 자산입니다. SAP 배경에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
* SAP 시스템: 예를 들어 SAP ERP 개발 시스템, SAP BW 테스트 시스템, SAP CRM 프로덕션 시스템 등의 응용 프로그램 계층과 DBMS 계층의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층을 나눌 수 없습니다. 즉, SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됩니다. 그러나 Azure 또는 온-프레미스에는 SAP 배경의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다.
* 클라우드 전용 배포: Azure 구독이 사이트 간 연결 또는 Express 경로 연결을 통해 온-프레미스 네트워크 인프라에 연결되지 않는 배포입니다. 공통 Azure 설명서에서는 이러한 종류의 배포를 '클라우드 전용' 배포라고도 합니다. 이 방법으로 배포된 가상 컴퓨터는 인터넷과 Azure의 VM에 할당된 공용 인터넷 끝점을 통해 액세스됩니다. 이러한 유형의 배포에서는 온-프레미스 AD(Active Directory) 및 DNS가 Azure로 확장되지 않습니다. 따라서 VM은 온-프레미스 Active Directory에 속하지 않습니다. 참고: 이 문서에서 클라우드 전용 배포는 온-프레미스에서 Active Directory 또는 이름 확인을 공용 클라우드로 확장하지 않고 Azure에서 단독으로 실행 중인 전체 SAP 배경으로 정의됩니다. Azure에서 호스트되는 SAP 시스템과 온-프레미스에 상주하는 리소스 간에 SAP STMS 또는 기타 온-프레미스 리소스를 사용해야 하는 프로덕션 SAP 시스템 또는 구성에 대해서는 클라우드 전용 구성이 지원되지 않습니다.
* 프레미스 간: VM이 온-프레미스 데이터 센터와 Azure 간에 사이트-사이트, 다중 사이트 또는 Express 경로 방식으로 연결되는 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 프레미스 간 시나리오라고도 합니다. 연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이렇게 확장된 VM은 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이 기능은 Azure의 SAP 자산 배포를 위한 가장 일반적인 시나리오가 될 것입니다. 자세한 내용은 [이][vpn-gateway-cross-premises-options] 문서 및 [이][vpn-gateway-site-to-site-create] 문서를 참조하세요.

> [AZURE.NOTE] 프로덕션 SAP 시스템의 경우 SAP 시스템을 실행 중인 Azure 가상 컴퓨터가 온-프레미스 도메인의 멤버인 SAP 시스템의 프레미스 간 배포가 지원됩니다. 일부 또는 전체 SAP 배경을 Azure로 배포하는 데 프레미스 간 구성이 지원됩니다. Azure에서 전체 SAP 배경을 실행하려고 해도 이러한 VM이 온-프레미스 도메인 및 ADS에 속해야 합니다. 이전 버전의 문서에서 하이브리드-IT 시나리오에 대해 설명했습니다. '하이브리드'란 온-프레미스와 Azure 간에 프레미스 간 연결을 사용한다는 사실을 기반으로 합니다. 이 경우 '하이브리드'는 Azure의 VM이 온-프레미스 Active Directory의 일부임을 의미하기도 합니다.

일부 Microsoft 문서에서는 특히 DBMS HA 구성의 경우 프레미스 간 시나리오를 약간 다르게 설명합니다. SAP 관련 문서의 경우 프레미스 간 시나리오가 사이트 간 또는 개인(Express 경로) 방식으로 연결되고 SAP 배경이 온-프레미스와 Azure 간에 분산된다고 요약되어 있습니다.

### 리소스
다음 가이드는 Azure의 SAP 배포 항목에 대해 사용할 수 있습니다.

* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 계획 및 구현 가이드][planning-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 배포 가이드][deployment-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - DBMS 배포 가이드(이 문서)][dbms-guide]
* [Windows VM(가상 컴퓨터)에서 SAP NetWeaver - 고가용성 배포 가이드][ha-guide]

다음 SAP 정보는 Azure의 SAP 항목과 관련이 있습니다.

| Note 번호 | 제목
|------------|--------
| [1928533] | Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형
| [2015553] | Microsoft Azure의 SAP: 지원 필수 조건
| [1999351] | SAP용 고급 Azure 모니터링 문제 해결
| [2178632] | Microsoft Azure의 SAP용 주요 모니터링 메트릭
| [1409604] | Windows에서의 가상화: 향상된 모니터링
| [2191498] | Azure와 Linux의 SAP: 향상된 모니터링
| [2039619] | Oracle Database를 사용하는 Microsoft Azure의 SAP 응용 프로그램: 지원 제품 및 버전
| [2233094] | DB6: Linux, UNIX, 및 Windows용 IBM DB2를 사용하는 SAP 응용 프로그램 - 추가 정보
| [2243692] | Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제
| [1984787] | SUSE LINUX Enterprise Server 12: 설치 참고
| [2002167] | Red Hat Enterprise Linux 7.x: 설치 및 업그레이드

Linux용 SAP 정보를 모두 포함하는 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)를 읽어 보세요.

Microsoft Azure 아키텍처 및 Microsoft Azure 가상 컴퓨터 배포와 작동에 대한 실무 지식이 있어야 합니다. 자세한 내용은 여기 <https://azure.microsoft.com/documentation/>에서 찾을 수 있습니다.
 
> [AZURE.NOTE] Microsoft Azure Platform의 Microsoft Azure PaaS(Platform as a Service)에 대해서는 다루지 **않습니다**. 이 문서에서는 온-프레미스 환경에서 DBMS(데이터베이스 관리 시스템)를 실행하는 것처럼 Microsoft Azure 가상 컴퓨터(IaaS)에서 DBMS를 실행하는 방법에 대해 설명합니다. 이러한 두 환경에서의 데이터베이스 기능은 매우 다르므로 서로 혼합하지 않아야 합니다. 참고 항목: <https://azure.microsoft.com/services/sql-database/>

여기에서는 IaaS에 대해 설명하고 있으므로 Windows, Linux 및 DBMS 설치와 구성은 기본적으로 온-프레미스에 설치할 완전 컴퓨터 또는 가상 컴퓨터와 동일합니다. 그러나 IaaS를 사용하는 경우 일부 아키텍처 및 시스템 관리 구현이 달라집니다. 이 문서의 목적은 IaaS를 사용할 때 대비해야 하는 특정 아키텍처 및 시스템 관리의 차이점을 설명하는 것입니다.

일반적으로 이 문서에서 다루는 차이점의 전반적인 영역은 다음과 같습니다.

* 적절한 데이터 파일 레이아웃을 사용하고 워크로드에 대한 충분한 IOPS를 얻기 위한 SAP 시스템의 적절한 VM/VHD 레이아웃 계획
* IaaS 사용 시 네트워킹 고려 사항
* 데이터베이스 레이아웃을 최적화하기 위해 사용할 특정 데이터베이스 기능
* IaaS 사용 시 백업 및 복원 고려 사항
* 배포에 다양한 이미지 형식 사용
* Azure IaaS의 고가용성

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 배포의 구조
이 챕터를 진행하려면 [배포 가이드][deployment-guide]의 [이][deployment-guide-3] 챕터에 설명된 내용을 이해해야 합니다. 이 챕터를 읽기 전에 다양한 VM 시리즈와 그 차이점 및 Azure 표준과 프리미엄 저장소의 차이점에 대한 정보를 이해해야 합니다.

2015년 3월까지 운영 체제를 포함한 Azure VHD 크기가 127GB로 제한되었습니다. 2015년 3월, 이 제한이 없어졌습니다(자세한 내용은 <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/> 참조). 이후 운영 체제를 포함하는 VHD는 다른 모든 VHD와 같은 크기를 가질 수 있습니다. 그렇지만 운영 체제, DBMS 및 최종 SAP 이진 파일이 데이터베이스 파일과 구분되는 배포 구조를 사용하는 것이 더 좋습니다. 따라서 Azure 가상 컴퓨터에서 실행 중인 SAP 시스템에 운영 체제와 함께 기본 VM(또는 VHD), DBMS(데이터베이스 관리 시스템) 실행 파일 및 SAP 실행 파일이 설치되어 있어야 합니다. DBMS 데이터 및 로그 파일은 Azure 저장소(표준 또는 프리미엄 저장소)에서 별도의 VHD 파일에 저장되며 원래 Azure 운영 체제 이미지 VM에 논리 디스크로 연결됩니다.

Azure 표준 저장소를 활용하는지 또는 프리미엄 저장소를 활용하는지(예: DS 시리즈 또는 GS 시리즈 VM 사용)에 따라 [여기][virtual-machines-sizes]에서 설명하는 Azure의 할당량이 달라집니다. Azure VHD를 계획할 때 다음에 대한 최적의 할당량 균형을 찾아야 합니다.

* 데이터 파일 수
* 파일에 포함된 VHD 수
* 단일 VHD당 IOPS 할당량
* VHD당 데이터 처리량
* VM 크기당 가능한 추가 VHD 수
* VM에서 제공할 수 있는 전체 저장소 처리량
 
Azure는 VHD 드라이브당 IOPS 할당량을 적용합니다. 이러한 할당량은 VHD가 Azure 표준 저장소에서 호스트되는지 또는 프리미엄 저장소에서 호스트되는지에 따라 달라집니다. I/O 대기 시간도 두 저장소에서 서로 다르며 프리미엄 저장소를 사용할 때 더 나은 I/O 대기 시간 요소를 제공합니다. 각 VM 유형에는 연결할 수 있는 VHD 수가 제한되어 있습니다. 또한 특정 VM 유형만 Azure 프리미엄 저장소를 활용할 수 있습니다. 즉, 특정 VM 유형을 결정할 때 고려해야 할 사항에 CPU 및 메모리 요구 사항뿐 아니라 IOPS, 대기 시간 및 디스크 처리량 요구 사항도 포함되며, 보통 VHD 수 또는 프리미엄 저장소 디스크의 형식에 따라 달라집니다. 특히 프리미엄 저장소를 사용할 경우 VHD의 크기는 각 VHD에서 필요한 IOPS 수 및 처리량을 반영해야 할 수 있습니다.

전체 IOPS 속도, 탑재된 VHD 수 및 VM 크기는 모두 관련되어 있으므로 SAP 시스템의 Azure 구성이 온-프레미스 배포와 다를 수 있습니다. LUN당 IOPS 제한은 일반적으로 온-프레미스 배포에서 구성할 수 있습니다. Azure 저장소를 사용할 경우 이러한 제한이 고정되는 반면 프리미엄 저장소의 경우 디스크 형식에 따라 달라집니다. 따라서 온-프레미스 배포를 사용할 경우 SAP 및 DBMS와 같은 특수 실행 파일용 볼륨 또는 임시 데이터베이스나 테이블 공간용 특수 볼륨을 위한 다양한 볼륨을 사용하는 데이터베이스 서버의 고객 구성을 보게 됩니다. 이러한 온-프레미스 시스템을 Azure로 이동하는 경우 IOPS를 전혀 수행하지 않거나 적게 수행하는 실행 파일 또는 데이터베이스에 대해 VHD를 사용하여 IOPS 대역폭이 낭비될 수 있습니다. 따라서 Azure VM에서는 가능하면 DBMS 및 SAP 실행 파일을 OS 디스크에 설치하는 것이 좋습니다.

데이터베이스 파일 및 로그 파일의 배치와 사용되는 Azure 저장소 형식은 IOPS, 대기 시간 및 처리량 요구 사항에 따라 정의되어야 합니다. 트랜잭션 로그에 대해 충분한 IOPS를 사용하려면 트랜잭션 로그 파일을 위한 VHD를 여러 개 활용하거나 더 큰 프리미엄 저장소 디스크를 사용해야 할 수 있습니다. 이 경우 트랜잭션 로그를 포함할 VHD와 함께 소프트웨어 RAID(예: Windows용 Windows 저장소 풀 또는 Linux용 MDADM 및 LVM(논리 볼륨 관리자))를 빌드합니다.

___

> ![Windows][Logo_Windows] Windows
>
> Azure VM의 드라이브 D:\\는 Azure 계산 노드의 일부 로컬 디스크에서 지원하는 비지속형 드라이브입니다. 비지속형이기 때문에 VM을 다시 부팅하면 D:\\ 드라이브의 변경 내용이 손실됩니다. "변경 내용"이란 저장된 파일, 생성된 디렉터리, 설치된 응용 프로그램 등을 의미합니다.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM은 Azure 계산 노드의 논리 디스크에서 지원하는 비지속형 드라이브의 /mnt/resource에 자동으로 탑재됩니다. 비지속형이기 때문에 VM을 다시 부팅하면 /mnt/resource의 변경 내용이 손실됩니다. 변경 내용이란 저장된 파일, 생성된 디렉터리, 설치된 응용 프로그램 등을 의미합니다.

___

Azure VM 시리즈에 따라 계산 노드의 논리 디스크에 다음과 같이 범주로 분류할 수 있는 다양한 성능이 표시됩니다.

* A0-A7: 매우 제한된 성능. Windows 페이지 파일 이외에 어떤 것에 대해서도 사용할 수 없음
* A8-A11: 매우 양호한 성능(수 만개의 IOPS 및 1GB/초 이상의 처리량)
* D 시리즈: 매우 양호한 성능(수 만개의 IOPS 및 1GB/초 이상의 처리량)
* DS 시리즈: 매우 양호한 성능(수 만개의 IOPS 및 1GB/초 이상의 처리량)
* G 시리즈: 매우 양호한 성능(수 만개의 IOPS 및 1GB/초 이상의 처리량)
* GS 시리즈: 매우 양호한 성능(수 만개의 IOPS 및 1GB/초 이상의 처리량)

위의 문은 SAP에서 인증된 VM 유형에 적용됩니다. tempdb 또는 임시 테이블 공간과 같이 일부 DBMS 기능에서 활용하는 데 필요한 뛰어난 IOPS 및 처리량을 제공하는 VM 시리즈입니다.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 및 VHD용 캐싱
포털을 통해 이러한 디스크/VHD를 생성하거나 업로드된 VHD를 VM에 탑재할 때 VM과 Azure 저장소에 있는 VHD 간의 I/O 트래픽을 캐시할지 여부를 선택할 수 있습니다. Azure 표준 및 프리미엄 저장소에서는 이 유형의 캐시에 대해 두 가지 기술을 사용합니다. 두 경우 모두 캐시 자체가 VM의 임시 디스크(Windows의 D:\\ 또는 Linux의 /mnt/resource)에서 사용하는 것과 동일한 드라이브에서 지원하는 디스크입니다.
 
Azure 표준 저장소의 경우 가능한 캐시 유형은 다음과 같습니다.

* 캐싱 없음
* 읽기 캐싱
* 읽기 및 쓰기 캐싱

일관성 있고 결정적 성능을 얻으려면 Azure 표준 저장소에서 **DBMS 관련 데이터 파일, 로그 파일 및 테이블 공간을 포함하는 모든 VHD의 캐싱을 '없음'**으로 설정해야 합니다. VM 캐싱은 기본값으로 유지할 수 있습니다.

Azure 프리미엄 저장소의 경우 다음과 같은 캐싱 옵션이 있습니다.

* 캐싱 없음
* 읽기 캐싱

Azure 프리미엄 저장소에 대한 권장 사항은 SAP 데이터베이스의 **데이터 파일 읽기 캐싱**을 활용하고 **로그 파일의 VHD에 대한 캐싱 없음**을 선택하는 것입니다.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>소프트웨어 RAID
위에서 설명한 것처럼 데이터베이스 파일에 필요한 IOPS 수를 구성 가능한 VHD 수 및 Azure VM이 VHD 또는 프리미엄 저장소 디스크 형식당 제공할 최대 IOPS에 맞게 배분해야 합니다. VHD에 대한 IOPS 부하를 처리하는 가장 쉬운 방법은 여러 VHD에 소프트웨어 RAID를 빌드하는 것입니다. 그런 다음 SAP DBMS의 여러 데이터 파일을 소프트웨어 RAID에서 얻은 LUN에 배치합니다. 3가지 프리미엄 저장소 디스크 중 2개는 표준 저장소 기반의 VHD보다 더 높은 IOPS 할당량을 제공하므로 요구 사항에 따라 프리미엄 저장소 사용도 고려해야 합니다. 또한 Azure 프리미엄 저장소는 훨씬 더 향상된 I/O 대기 시간을 제공합니다.

다른 DBMS 시스템의 트랜잭션 로그에도 동일하게 적용됩니다. DBMS 시스템은 한 번에 한 파일에만 쓰므로 Tlog 파일을 더 추가해도 도움이 되지 않습니다. VHD 기반의 단일 표준 저장소에서 제공하는 것보다 더 높은 IOPS 속도가 필요한 경우 여러 표준 저장소 VHD를 스트라이프하거나 해당 IOPS 속도보다 더 큰 프리미엄 저장소 디스크 형식을 사용하여 트랜잭션 로그에 I/O를 쓰기 위한 대기 시간을 줄일 수 있습니다.
 
Azure 배포에서 소프트웨어 RAID를 사용해야 하는 상황은 다음과 같습니다.

* 트랜잭션 로그/다시 실행 로그에서 Azure에서 단일 VHD에 제공하는 것보다 더 많은 IOPS를 필요로 하는 경우. 위에서 언급한 대로 소프트웨어 RAID를 사용하여 여러 VHD에 LUN을 빌드하여 이 문제를 해결할 수 있습니다.
* SAP 데이터베이스의 여러 데이터 파일에 I/O 워크로드가 균등하지 않게 배분된 경우. 이러한 경우 한 데이터 파일이 자주 할당량에 도달하고, 다른 데이터 파일은 단일 VHD의 IOPS 할당량에 근접하지도 않을 가능성이 있습니다. 이러한 경우 가장 간단한 해결책은 소프트웨어 RAID를 사용하여 여러 VHD에 하나의 LUN을 빌드하는 것입니다.
* 데이터 파일당 정확한 I/O 워크로드는 알 수 없으며 DBMS에 대한 전체 IOPS 워크로드만 대략적으로 알 수 있습니다. 소프트웨어 RAID를 사용하여 하나의 LUN을 빌드하는 것이 가장 간단합니다. 이 LUN 뒤의 다중 VHD 할당량의 합이 알려진 IOPS 속도를 충족해야 합니다.

___

> ![Windows][Logo_Windows] Windows
>
> 이전 Windows 버전의 Windows 스트라이프보다 Windows Server 2012 이상 저장소 공간이 더 효율적이므로 이 공간을 사용하는 것이 좋습니다. Windows Server 2012를 운영 체제로 사용하는 경우 PowerShell 명령을 사용하여 Windows 저장소 풀과 저장소 공간을 만들어야 할 수도 있습니다. PowerShell 명령은 <https://technet.microsoft.com/library/jj851254.aspx>에서 확인할 수 있습니다.

> 
> ![Linux][Logo_Linux] Linux
>
> Linux에서 소프트웨어 RAID를 빌드하는 경우 MDADM 및 LVM(논리 볼륨 관리자)만 지원됩니다. 자세한 내용은 다음 문서를 읽어보세요.
>
> * [Linux에서 소프트웨어 RAID 구성][virtual-machines-linux-configure-raid](MDADM용)
> * [Azure에서 Linux VM에 LVM 구성][virtual-machines-linux-configure-lvm]


___

Azure 프리미엄 저장소와 함께 사용할 수 있는 VM 시리즈 활용에 대한 고려 사항은 일반적으로 다음과 같습니다.

* SAN/NAS 장치에서 제공하는 것과 가까운 I/O 대기 시간에 대한 요구
* Azure 표준 저장소에서 제공할 수 있는 것보다 더 나은 I/O 대기 시간 요소에 대한 요구
* 특정 VM 유형에 대해 여러 표준 저장소 VHD를 사용하여 얻을 수 있는 것보다 더 높은 VM당 IOPS

기본 Azure 저장소는 각 VHD를 3개 이상의 저장소 노드에 복제하므로 간단한 RAID 0 스트라이프를 사용할 수 있습니다. RAID5 또는 RAID1을 구현할 필요가 없습니다.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage
Microsoft Azure 저장소는 최소 3개의 개별 저장소 노드에 기본 VM(OS 포함) 및 VHD 또는 BLOB을 저장합니다. 저장소 계정을 만들 때는 다음과 같은 보호를 선택할 수 있습니다.

![Azure 저장소 계정에 대해 지역에서 복제 사용][dbms-guide-figure-100]

Azure 저장소 로컬 복제(로컬 중복)는 인프라 장애로 인한 데이터 손실로부터 보호할 수 있는 수준을 제공하지만 대부분의 고객은 경제적 부담이 커서 구입하지 못합니다. 위와 같이 서로 다른 4개의 옵션이 있으며 5번째는 처음 3개 옵션 중 하나의 변형입니다. 자세히 살펴보면 다음과 같이 구분할 수 있습니다.

* **프리미엄 LRS(로컬 중복 저장소)**: Azure 프리미엄 저장소는 I/O 사용량이 많은 워크로드를 실행하는 가상 컴퓨터에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. Azure 지역의 동일한 Azure 데이터 센터 내에 3개의 데이터 복제본이 있습니다. 복사본이 여러 오류 도메인 및 업그레이드 도메인에 있습니다(각 도메인의 개념은 [계획 가이드][planning-guide]의 [이][planning-guide-3.2] 챕터 참조). 저장소 노드 오류 또는 디스크 오류로 인해 서비스에서 제공하는 데이터가 복제되는 경우 새 복제본이 자동으로 생성됩니다.
* **LRS(로컬 중복 저장소)**: 이 경우 Azure 지역의 동일한 Azure 데이터 센터 내에 데이터의 복제본이 3개 있습니다. 복사본이 여러 오류 도메인 및 업그레이드 도메인에 있습니다(각 도메인의 개념은 [계획 가이드][planning-guide]의 [이][planning-guide-3.2] 챕터 참조). 저장소 노드 오류 또는 디스크 오류로 인해 서비스에서 제공하는 데이터가 복제되는 경우 새 복제본이 자동으로 생성됩니다.
* **GRS(지역 중복 저장소)**: 이 경우 다른 Azure 지역에 있는 3개의 추가 데이터 복제본을 공급하는 비동기 복제가 있습니다. 이러한 경우 대부분 동일한 지역(예: 북유럽 및 유럽 서부)에 있습니다. 이렇게 하면 추가 복제본이 3개가 되므로 총 6개의 복제본이 있습니다. 변형은 지역에서 복제된 Azure 지역의 데이터를 읽기 용도(읽기-액세스 지역 중복)로 사용할 수 있는 위치가 추가된 것입니다.
* **ZRS(영역 중복 저장소)**: 이 경우 동일한 Azure 지역에 3개의 데이터 복제본이 있습니다. [계획 가이드][planning-guide]의 [이][planning-guide-3.1] 챕터에 설명된 대로 Azure 지역은 근접해 있는 여러 데이터 센터일 수 있습니다. LRS의 경우 복제본은 하나의 Azure 지역을 구성하는 여러 데이터 센터에 분산됩니다.

자세한 내용은 [여기][storage-redundancy]를 참조하세요.
 
> [AZURE.NOTE] DBMS 배포의 경우 지역 중복 저장소의 사용은 권장되지 않습니다.
>
> Azure 저장소 지역에서 복제는 비동기적입니다. 잠금 단계에서는 단일 VM에 탑재된 개별 VHD 복제가 동기화되지 않습니다. 따라서 여러 VHD에 분산되거나 다중 VHD 기반의 소프트웨어 RAID에 대해 배포된 DBMS 파일을 복제하는 데 적합하지 않습니다. DBMS 소프트웨어를 사용하려면 영구 디스크 저장소가 여러 LUN 및 기본 디스크/VHD/스핀들 간에 정확하게 동기화되어야 합니다. DBMS 소프트웨어는 다양한 메커니즘을 사용하여 I/O 쓰기 작업의 순서를 지정하고 이러한 작업이 몇 밀리초라도 다른 경우 DBMS는 복제에서 지정한 디스크 저장소가 손상된 것으로 보고합니다. 따라서 지역 복제된 여러 VHD에 분산된 데이터베이스 구성을 원하는 경우 데이터베이스 도구 및 기능을 사용하여 이러한 복제를 수행해야 합니다. 이 작업을 수행하기 위해 Azure 저장소 지역에서 복제를 사용해서는 안 됩니다.
>
> 문제는 예제 시스템을 사용하여 설명하는 것이 가장 간단합니다. DBMS 데이터 파일을 포함하는 8개의 VHD와 트랜잭션 로그 파일을 포함하는 하나의 VHD가 있는 Azure에 SAP 시스템을 업로드했다고 가정합니다. 이러한 9개의 각 VHD에는 데이터가 데이터 파일에 기록되는지 트랜잭션 로그 파일에 기록되는지에 관계없이 DBMS에 따라 일관된 방법으로 데이터가 기록됩니다.
>
> 데이터를 올바르게 지역 복제하고 일관성 있는 데이터베이스 이미지를 유지하려면 9개의 모든 VHD 콘텐츠가 서로 다른 9개 VHD에 대해 실행된 I/O 작업 순서와 동일하게 지역 복제되어야 합니다. 그러나 Azure 저장소 지역에서 복제는 VHD 간 종속성 선언을 허용하지 않습니다. 즉, Microsoft Azure 저장소 지역에서 복제는 이러한 서로 다른 9개 VHD의 콘텐츠가 서로 관련되어 있으며 데이터 변경 내용이 9개 VHD 모두에서 발생한 I/O 작업 순서대로 복제할 때만 일관되게 적용된다는 사실을 알지 못합니다.
>
> 이 시나리오에서는 지역 복제된 이미지가 일관성 있는 데이터베이스 이미지를 제공하지 않을 가능성이 높을 뿐 아니라, 성능에 큰 영향을 줄 수 있는 지역 중복 저장소의 성능이 저하됩니다. 한 마디로 말해 DBMS 형식 워크로드에 대해서는 이러한 유형의 저장소 중복을 사용하지 마세요.
 
#### VHD를 Azure 가상 컴퓨터 서비스 저장소 계정에 매핑
Azure 저장소 계정은 관리 구성 요소일 뿐 아니라 제한의 대상이 됩니다. 단, 제한은 Azure 표준 저장소 계정인지 또는 Azure 프리미엄 저장소 계정인지에 따라 달라집니다. 정확한 기능 및 제한은 [여기][storage-scalability-targets]에 나열되어 있습니다.
 
Azure 표준 저장소의 경우 저장소 계정당 IOPS의 제한이 있습니다([이 문서][storage-scalability-targets]의 '총 요청 속도'를 포함하는 행). 또한 2015년 7월 현재 초기 제한은 Azure 구독당 100개의 저장소 계정으로 설정되어 있습니다. 따라서 Azure 표준 저장소를 사용할 때 여러 저장소 계정 간 VM의 IOPS 균형을 조정해야 합니다. 반면 단일 VM은 가능한 경우 하나의 저장소 계정을 사용하는 것이 이상적입니다. 따라서 Azure 표준 저장소에서 호스트되는 각 VHD가 해당 할당량 한도에 도달할 수 있는 DBMS 배포의 경우 Azure 표준 저장소를 사용하는 Azure 저장소 계정당 30-40개의 VHD만 배포해야 합니다. 반면 Azure 프리미엄 저장소를 활용하고 대용량 데이터베이스를 저장하려는 경우 IOPS에 대해 신경 쓰지 않아도 됩니다. 하지만 Azure 프리미엄 저장소 계정은 데이터 볼륨 면에서 Azure 표준 저장소 계정보다 훨씬 더 제한적입니다. 결과적으로, 데이터 볼륨 제한에 도달할 때까지만 Azure 프리미엄 저장소 계정 내에 제한된 수의 VHD를 배포할 수 있습니다. 결국 Azure 저장소 계정은 IOPS 및/또는 용량이 제한된 "가상 SAN"이라고 할 수 있습니다. 결과적으로, 작업은 온-프레미스 배포에서처럼 여러 '가상 SAN 장치' 또는 Azure 저장소 계정에 대해 여러 SAP 시스템의 VHD 레이아웃을 정의합니다.
 
Azure 표준 저장소의 경우 여러 저장소 계정의 저장소를 단일 VM에 두는 것은 권장되지 않습니다.

반면 Azure VM의 DS 또는 GS 시리즈를 사용할 경우 Azure 표준 저장소 계정 및 프리미엄 저장소 계정에서 VHD를 탑재할 수 있습니다. VHD에서 지원되는 표준 저장소에 백업을 작성하고 프리미엄 저장소에 DBMS 데이터 및 로그 파일이 있는 경우 이렇게 다른 유형의 저장소를 활용할 수 있습니다.

고객 배포 및 테스트에 따르면 데이터베이스 데이터 파일 및 로그 파일을 포함하는 30-40개의 VHD를 단일 Azure 표준 저장소 계정에 프로비전해야 적절한 성능을 제공할 수 있습니다. 앞서 언급했듯이 Azure 프리미엄 저장소 계정의 제한은 IOPS가 아니라 포함할 수 있는 데이터 용량과 관련이 있습니다.

SAN 장치 온-프레미스처럼 공유를 위해서는 모니터링을 수행하여 Azure 저장소 계정의 병목을 검색해야 합니다. SAP용 Azure 모니터링 확장 및 Azure 포털은 현재 사용 중이며 차선의 IO 성능을 제공할 수 있는 Azure 저장소 계정을 검색할 수 있는 도구입니다. 이 상황이 검색되면 사용 중인 VM을 다른 Azure 저장소 계정으로 이동하는 것이 좋습니다. SAP 호스트 모니터링 기능의 활성화 방법에 대한 자세한 내용은 [배포 가이드][deployment-guide]를 참조하세요.

Azure 표준 저장소 및 Azure 표준 저장소 계정에 대한 모범 사례를 요약한 다른 문서는 여기(<https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>)를 참조하세요.
 
#### 배포된 DBMS VM을 Azure 표준 저장소에서 Azure 프리미엄 저장소로 이동
고객이 배포된 VM을 Azure 표준 저장소에서 Azure 프리미엄 저장소로 이동을 원하는 경우가 있습니다. 이 작업은 데이터를 물리적으로 이동하지 않으면 불가능합니다. 다음과 같은 방법을 사용할 수 있습니다.

* 모든 VHD, 기본 VHD 및 데이터 VHD를 새 Azure 프리미엄 저장소 계정으로 간단하게 복사할 수 있습니다. 데이터 볼륨이 필요한 이유가 아니더라도 Azure 표준 저장소의 VHD 수를 선택해야 할 때가 많습니다. 하지만 대부분은 IOPS 때문에 VHD가 필요합니다. Azure 프리미엄 저장소로 이동한 후에는 VHD를 줄여 일부 IOPS 처리량을 달성할 수 있습니다. Azure 표준 저장소의 경우 명목상의 디스크 크기가 아니라 사용된 데이터에 대해 청구되므로 VHD 수는 비용 면에서 크게 중요하지 않습니다. 그러나 Azure 프리미엄 저장소는 명목상의 디스크 크기에 대해 청구됩니다. 따라서 대부분의 고객은 프리미엄 저장소의 Azure VHD 수를 필요한 IOPS 처리량을 얻는 데 필요한 수로 유지하려고 합니다. 그러므로 대부분 1:1 복사본 비율을 사용합니다.
* 탑재되지 않은 경우 SAP 데이터베이스의 데이터베이스 백업을 포함할 수 있는 단일 VHD를 탑재합니다. 백업 후 백업이 있는 VHD를 포함한 모든 VHD의 탑재를 해제하고 기본 VHD 및 백업이 있는 VHD를 Azure 프리미엄 저장소 계정으로 복사합니다. 그런 다음 기본 VHD를 기반으로 VM을 배포하고 백업이 있는 VHD를 탑재합니다. 이제 데이터베이스를 복원하는 데 사용되는 VM에 대한 빈 프리미엄 저장소 디스크를 추가로 만듭니다. 여기에서는 복원 프로세스 중 DBMS를 사용하여 데이터 및 로그 파일에 대한 경로를 변경할 수 있다고 가정합니다.
* 또는 이전 프로세스를 변형하여, 백업 VHD를 Azure 프리미엄 저장소로 복사하고 새로 배포 및 설치한 VM에 연결할 수 있습니다.
* 네 번째 방법은 데이터베이스의 데이터 파일 수를 변경해야 할 시기를 선택하는 것입니다. 이 경우 내보내기/가져오기를 사용하여 형식이 같은 SAP 시스템 복사를 수행합니다. Azure 프리미엄 저장소 계정에 복사된 VHD에 내보내기 파일을 저장하고 이를 가져오기 프로세스를 실행하는 데 사용할 VM에 연결합니다. 고객은 주로 이 방법을 사용하여 데이터 파일의 수를 줄입니다.

### Azure의 SAP용 VM 배포
Microsoft Azure는 VM 및 관련 디스크를 배포하기 위한 여러 가지 방법을 제공합니다. 따라서 배포 방법에 따라 VM 준비가 달라질 수 있으므로 차이점을 이해해야 합니다. 일반적으로 다음 챕터에서 설명하는 시나리오를 살펴봅니다.

#### Azure 마켓플레이스에서 VM 배포
Azure 마켓플레이스에서 Microsoft 또는 타사에서 제공한 이미지를 사용할 수 있습니다. Azure에서 VM을 배포한 후 온-프레미스 환경에서와 동일한 지침 및 도구를 사용하여 VM 내부에 SAP 소프트웨어를 설치합니다. Azure VM에 SAP 소프트웨어를 설치할 때는 SAP 설치 미디어를 Azure VHD에 업로드 및 저장하거나 필요한 SAP 설치 미디어를 모두 포함하는 '파일 서버'로 사용할 Azure VM을 만드는 것이 좋습니다.

#### 고객별 범용 이미지를 사용하여 VM 배포
OS 또는 DBMS 버전에 대한 특정 패치 요구 사항으로 인해 Azure 마켓플레이스에 제공된 이미지가 용도에 맞지 않을 수 있습니다. 따라서 '개인' OS/DBMS VM 이미지를 사용하는 VM을 만들어 이후 여러 번 배포할 수 있습니다. 복제를 위해 이러한 '개인' 이미지를 준비하려면 온-프레미스 VM에서 OS를 일반화해야 합니다. VM 일반화 방법에 대한 자세한 내용은 [배포 가이드][deployment-guide]를 참조하세요.

온-프레미스 VM(특히 2계층 시스템)에 SAP 콘텐츠를 이미 설치한 경우 Azure VM 배포 후 SAP Software Provisioning Manager에서 지원하는 인스턴스 이름 변경 절차를 통해 SAP 시스템 설정을 적용할 수 있습니다(SAP Note [1619720]). 그렇지 않은 경우 Azure VM 배포 후 나중에 SAP 소프트웨어를 설치할 수 있습니다.
 
SAP 응용 프로그램에서 사용하는 데이터베이스 콘텐츠를 기준으로 SAP 설치를 통해 콘텐츠를 새롭게 생성하거나 DBMS 데이터베이스 백업이 있는 VHD나 DBMS 기능을 통해 Microsoft Azure 저장소에 직접 백업하여 콘텐츠를 Azure로 가져올 수 있습니다. 이 경우 DBMS 데이터 및 로그 파일 온-프레미스를 사용하여 VHD를 준비한 다음 디스크로서 Azure로 가져올 수도 있습니다. 하지만 온-프레미스에서 Azure에 로드되는 DBMS 데이터 전송은 온-프레미스를 프로비전해야 하는 VHD 디스크에 대해 작동합니다.

#### 일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동
온-프레미스에서 Azure로 특정 SAP 시스템을 이동하려고 합니다(리프트 및 전환). 이 작업은 OS, SAP 이진 파일 및 결과적 DBMS 이진을 포함하는 VHD와 DBMS 데이터와 로그 파일이 있는 VHD를 Azure에 업로드하여 수행할 수 있습니다. 위의 시나리오 2와는 달리 Azure VM에서 호스트 이름, SAP SID 및 SAP 사용자 계정을 온-프레미스 환경에서 구성된 대로 유지합니다. 그러므로 이미지 일반화는 필요하지 않습니다. 이 경우는 SAP 배경의 일부가 온-프레미스에서 실행되고 일부는 Azure에서 실행되는 프레미스 간 시나리오에 주로 적용됩니다.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Azure VM에서 고가용성 및 재해 복구
Azure는 SAP 및 DBMS 배포에 사용할 다양한 구성 요소에 적용할 수 있는 다음과 같은 HA(고가용성) 및 DR(재해 복구) 기능을 제공합니다.

### Azure 노드에 배포된 VM
Azure 플랫폼은 배포된 VM을 위한 실시간 마이그레이션 등의 기능을 제공하지 않습니다. 즉, VM이 배포된 서버 클러스터에 유지 관리가 필요한 경우 VM을 중지했다가 다시 시작해야 합니다. Azure에서의 유지 관리는 서버 클러스터 내의 업그레이드 도메인을 통해 수행됩니다. 한 번에 하나의 업그레이드 도메인만 유지 관리됩니다. 이렇게 다시 시작하는 동안 VM이 종료될 때 서비스가 중단되었다가 유지 관리가 수행되고 VM이 다시 시작됩니다. 그러나 대부분의 DBMS 공급업체는 주 노드를 사용할 수 없는 경우 다른 노드에서 DBMS 서비스를 신속하게 다시 시작하는 고가용성 및 재해 복구 기능을 제공합니다. Azure 플랫폼에서는 VM, 저장소 및 기타 Azure 서비스를 업그레이드 도메인에 분산하는 기능을 제공하여 계획된 유지 관리를 수행합니다. 그렇지 않으면 인프라 장애가 소수의 VM 또는 서비스에만 영향을 줍니다. 신중하게 계획할 경우 온-프레미스 인프라와 비슷한 수준의 가용성을 얻을 수 있습니다.

Microsoft Azure 가용성 집합은 한 번에 한 노드만 종료되는 클러스터 내에서 VM 및 기타 서비스가 클러스터 내의 다른 오류 및 업그레이드 도메인에 분산되도록 하는 서비스 또는 VM의 논리적 그룹입니다(자세한 내용은 [이][virtual-machines-manage-availability] 문서 참조).

VM을 롤아웃하려는 경우 다음과 같이 구성해야 합니다.

![DBMS HA 구성에 대한 가용성 집합 정의][dbms-guide-figure-200]

DBMS 배포의 고가용성 구성을 생성하려는 경우(사용된 개별 DBMS HA 기능과 별개로) DBMS VM에 대해 다음을 수행해야 합니다.

* 동일한 Azure 가상 네트워크에 VM을 추가합니다(<https://azure.microsoft.com/documentation/services/virtual-network/>).
* HA 구성의 VM은 동일한 서브넷에 있어야 합니다. 클라우드 전용 배포에서 서로 다른 서브넷 간의 이름 확인은 불가능하며 IP 확인만 가능합니다. 프레미스 간 배포에 대한 Express 경로 또는 사이트 간 연결을 사용하여 하나 이상의 서브넷이 있는 네트워크가 이미 구성되어 있습니다. 이름 확인은 온-프레미스 AD 정책 및 네트워크 인프라에 따라 수행됩니다. 
[설명]: <> (ARM에서 여전히 True인 경우 MSSedusch TODO 테스트)

#### IP 주소
복구 가능한 방법으로 HA 구성을 위한 VM을 설정하는 것이 좋습니다. IP 주소를 사용하여 HA 구성 내에서 HA 파트너를 확인하는 것은 Azure에서 고정 IP 주소를 사용하지 않는 한 안정적이지 않습니다. Azure에는 두 가지 "종료" 개념이 있습니다.

* Azure 포털 또는 Azure PowerShell cmdlet Stop-AzureRmVM을 통한 종료: 이 경우 가상 컴퓨터가 종료되고 할당이 취소됩니다. Azure 계정에 더 이상 이 VM에 대해 청구되지 않으므로 사용한 저장소에 대해서만 비용이 발생합니다. 그러나 네트워크 인터페이스의 개인 IP 주소가 고정이 아닌 경우 IP 주소가 해제되고 네트워크 인터페이스가 VM 다시 시작 후 다시 할당된 이전 IP 주소를 가져온다는 것을 보장할 수 없습니다. Azure 포털을 통해 또는 Stop-AzureRmVM을 호출하여 종료를 수행하면 자동으로 할당이 취소됩니다. 컴퓨터 할당을 취소하지 않으려면 Stop-AzureRmVM -StayProvisioned을 사용합니다.
* OS 수준에서 VM을 종료하는 경우 VM이 종료되고 할당이 취소되지 않습니다. 그러나 이 경우 종료 후에도 Azure 계정에 계속 VM에 대해 청구됩니다. 이러한 경우 중지된 VM에 대한 IP 주소 할당은 그대로 유지됩니다. VM을 종료하면 할당 취소가 자동으로 실행되지 않습니다.

프레미스 간 시나리오에서 DHCP 설정의 온-프레미스 정책이 다른 경우에도 기본적으로 종료 및 할당 취소는 VM에서의 IP 주소 할당 취소를 의미합니다.

* 단, [여기][virtual-networks-reserved-private-ip]의 설명에 따라 고정 IP 주소를 네트워크 인터페이스에 할당한 경우는 예외입니다.
* 이러한 경우 네트워크 인터페이스가 삭제되지 않는 한 IP 주소가 고정 상태로 유지됩니다.

> [AZURE.IMPORTANT] 전체 배포를 단순하고 관리하기 편하게 유지하려면 Azure 내의 DBMS HA 또는 DR 구성에서 여러 관련 VM 간에 작동하는 이름 확인 방법으로 VM 파트너를 설정하는 것이 좋습니다.
 
## 호스트 모니터링 배포
Azure 가상 컴퓨터에서 SAP 응용 프로그램을 생산적으로 사용하려면 Azure 가상 컴퓨터를 실행 중인 물리적 호스트에서 호스트 모니터링 데이터를 가져오는 기능이 SAP에 필요합니다. SAPOSCOL 및 SAP HostAgent에서 이 기능을 사용하려면 특정 SAP HostAgent 패치 수준이 필요합니다. 정확한 패치 수준은 SAP Note [1409604]에 설명되어 있습니다.

SAPOSCOL 및 SAPHostAgent에 호스트 데이터를 제공하는 구성 요소 배포와 이러한 구성 요소의 수명 주기 관리에 대한 자세한 내용은 [배포 가이드][deployment-guide]를 참조하세요.

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Microsoft SQL Server에 대한 고유 정보

### SQL Server IaaS
Microsoft Azure부터 Windows Server 플랫폼에 빌드된 기존 SQL Server 응용 프로그램을 쉽게 Azure 가상 컴퓨터로 마이그레이션할 수 있습니다. 가상 컴퓨터에서 SQL Server를 사용하면 이러한 응용 프로그램을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 응용 프로그램에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure 가상 컴퓨터에서 SQL Server를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

> [AZURE.IMPORTANT] 여기에서는 Microsoft Azure Platform의 PaaS(Platform as a Service)인 Microsoft Azure SQL 데이터베이스에 대해서는 다루지 않습니다. 이 문서에서는 Azure의 IaaS(서비스 제공 인프라)를 활용하여 Azure 가상 컴퓨터에서 온-프레미스 배포에 대해 알려진 SQL Server 제품을 실행하는 방법에 대해 설명합니다. 이러한 두 환경에서의 데이터베이스 기능은 다르므로 서로 혼합하지 않아야 합니다. 참고 항목: <https://azure.microsoft.com/services/sql-database/>
 
계속하기 전에 [이][virtual-machines-sql-server-infrastructure-services] 문서를 검토하는 것이 좋습니다.

다음 섹션에서는 위 링크에 있는 설명서의 일부를 집계 및 설명합니다. SAP에 대한 정보도 언급되며 몇 가지 개념도 보다 자세히 설명합니다. 그러나 SQL Server 관련 문서를 읽기 전에 먼저 위의 문서를 통해 작업하는 것이 좋습니다.

계속하기 전에 다음과 같은 IaaS의 SQL Server 관련 정보를 참조하세요.

* **가상 컴퓨터 SLA**: Azure에서 실행 중인 가상 컴퓨터용 SLA는 <https://azure.microsoft.com/support/legal/sla/>에 있습니다.
* **SQL 버전 지원**: SAP 고객의 경우 Microsoft Azure 가상 컴퓨터에서 SQL Server 2008 R2 이상 버전을 지원합니다. 이전 버전은 지원되지 않습니다. 자세한 내용은 이 일반 [지원 설명](https://support.microsoft.com/kb/956893)을 참조하세요. 일반적으로 SQL Server 2008은 Microsoft에서도 지원됩니다. 그러나 SQL Server 2008 R2에 SAP용 중요 기능이 도입되어 있으므로 SQL Server 2008 R2 이상 릴리스를 사용해야 합니다. SQL Server 2012 및 2014에서는 IaaS 시나리오(Azure 저장소에 대한 직접 백업 등)와 더 밀접하게 통합되어 확장되었습니다. 따라서 이 문서는 SQL Server 2012 및 2014와 Azure용 최신 패치 수준으로 제한됩니다.
* **SQL 기능 지원**: 대부분의 SQL Server 기능이 Microsoft Azure 가상 컴퓨터에서 지원되지만 몇 가지 예외가 있습니다. **공유 디스크를 사용하는 SQL Server 장애 조치(failover) 클러스터링은 지원되지 않습니다**. 데이터베이스 미러링, AlwaysOn 가용성 그룹, 복제, 로그 전달 및 Service Broker와 같은 분산 기술은 단일 Azure 지역 내에서 지원됩니다. <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>에서 설명한 대로 다른 Azure 지역 간 SQL Server AlwaysOn도 항상 지원됩니다. 자세한 내용은 [지원 설명](https://support.microsoft.com/kb/956893)을 참조하세요. AlwaysOn 구성을 배포하는 방법에 대한 예가 [이][virtual-machines-workload-template-sql-alwayson] 문서에 나와 있습니다. 또한 [여기][virtual-machines-sql-server-infrastructure-services]에 설명된 모범 사례도 참조하세요.
* **SQL 성능**: Microsoft Azure에서 호스트되는 가상 컴퓨터는 다른 공용 클라우드 가상화 서비스보다 훨씬 뛰어난 성능을 제공하지만 개별 결과는 다를 수 있습니다. [이][virtual-machines-sql-server-performance-best-practices] 문서를 확인해 보세요.
* **Azure 마켓플레이스에서 이미지 사용**: 새 Microsoft Azure VM을 배포하는 가장 빠른 방법은 Azure 마켓플레이스의 이미지를 사용하는 것입니다. Azure 마켓플레이스에는 SQL Server를 포함한 이미지가 있습니다. SQL Server가 이미 설치된 이미지는 SAP NetWeaver 응용 프로그램에 즉시 사용할 수 없습니다. 그 이유는 이러한 이미지 내에 SAP NetWeaver 시스템에 필요한 데이터 정렬이 아닌 기본 SQL Server 데이터 정렬이 설치되어 있기 때문입니다. 이러한 이미지를 사용하려면 [Microsoft Azure 마켓플레이스에서 SQL Server 이미지 사용][dbms-guide-5.6] 챕터에서 설명하는 단계를 확인하세요.
* 자세한 내용은 [가격 책정 정보](https://azure.microsoft.com/pricing/)를 확인하세요. [SQL Server 2012 라이선스 가이드](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) 및 [SQL Server 2014 라이선스 가이드](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf)도 중요한 리소스입니다.
 
### Azure VM의 SAP 관련 SQL Server 설치에 대한 SQL Server 구성 지침

#### SAP 관련 SQL Server 배포용 VM/VHD 구조에 대한 권장 사항
일반적인 설명에 따라 SQL Server 실행 파일을 VM의 기본 VHD(드라이브 C:)에 있는 시스템 드라이브에 배치 또는 설치해야 합니다. 일반적으로 대부분의 SQL Server 시스템 데이터베이스는 SAP NetWeaver 워크로드에서 높은 수준으로 사용되지 않습니다. 따라서 SQL Server의 시스템 데이터베이스(master, msdb 및 model)는 C:\\ 드라이브에 남아 있을 수 있습니다. 단, tempdb는 예외적으로 일부 SAP ERP 및 모든 BW 워크로드에서 원본 VM보다 더 많은 데이터 볼륨 또는 I/O 작업 볼륨을 필요로 할 수 있습니다. 이러한 시스템의 경우 다음 단계를 수행해야 합니다.

* 주 tempdb 데이터 파일을 SAP 데이터베이스의 주 데이터 파일과 같은 논리 드라이브로 이동합니다.
* 추가 tempdb 데이터 파일을 SAP 사용자 데이터베이스의 데이터 파일을 포함하는 다른 논리 드라이브에 각각 추가합니다.
* tempdb 로그 파일을 사용자 데이터베이스의 로그 파일을 포함하는 논리 드라이브에 추가합니다.
* **로컬 SSD를 사용하는 VM 형식의 경우에만** 계산 노드에서 tempdb 데이터 및 로그 파일을 D:\\ 드라이브에 배치할 수 있습니다. 그러나 여러 tempdb 데이터 파일을 사용하도록 권장할 수 있습니다. D:\\ 드라이브 볼륨은 VM 유형에 따라 다릅니다.
 
이러한 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 적절한 tempdb 크기를 확인하려면 온-프레미스에서 실행 중인 기존 시스템에서 tempdb 크기를 확인합니다. 또한 이러한 구성은 시스템 드라이브에서 tempdb에 대해 제공할 수 있는 것보다 더 많은 IOPS 수를 허용합니다. 다시 말해, 온-프레미스에서 실행 중인 시스템은 tempdb에 대한 I/O 워크로드를 모니터링하여 tempdb에 필요한 IOPS 수를 파생할 수 있습니다.

SQL Server와 SAP 데이터베이스를 실행하고 tempdb 데이터 및 tempdb 로그 파일이 D:\\ 드라이브에 배치된 VM 구성은 다음과 같습니다.
 
![SAP용 Azure IaaS VM의 참조 구성][dbms-guide-figure-300]

D:\\ 드라이브의 크기는 VM 유형에 따라 달라집니다. D:\\ 드라이브가 너무 작은 경우 tempdb 크기 요구 사항에 따라 tempdb 데이터 및 로그 파일을 SAP 데이터베이스 데이터 및 로그 파일에 강제로 연결해야 할 수 있습니다.

#### VHD 형식 설정
SQL Server의 경우 SQL Server 서버 데이터 및 로그 파일을 포함하는 VHD의 NTFS 블록 크기는 64K여야 합니다. D:\\ 드라이브의 형식을 설정할 필요가 없습니다. 이 드라이브는 미리 포맷되어 있습니다.

데이터베이스를 복원 또는 생성해도 파일 콘텐츠를 비워 데이터 파일이 초기화되지 않도록 하려면 SQL Server 서비스가 실행 중인 사용자 컨텍스트에 특정 사용 권한이 있어야 합니다. 일반적으로 Windows 관리자 그룹의 사용자에게는 이러한 권한이 있습니다. SQL Server 서비스가 Windows 관리자가 아닌 사용자의 사용자 컨텍스트에서 실행되는 경우 해당 사용자에게 '볼륨 유지 관리 작업 수행' 사용자 권한을 할당해야 합니다. 자세한 내용은 Microsoft 기술 자료 문서 <https://support.microsoft.com/kb/2574695>를 참조하세요.
 
#### 데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 확장할 수 있습니다. 그러므로 아직 수행하지 않은 경우 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SQL Server 페이지 압축을 적용하는 것이 좋습니다.
 
Azure에 업로드하기 전에 데이터베이스 압축을 수행하는 이유는 두 가지입니다.

* 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터베이스 압축은 Azure 가상 컴퓨터에서도 온-프레미스에서와 마찬가지로 잘 작동합니다. 기존 SAP SQL Server 데이터베이스 압축 방법에 대한 자세한 내용은 <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>를 참조하세요.
  
### SQL Server 2014 - Azure 블로그 저장소에 데이터베이스 파일 직접 저장
SQL Server 2014에서는 VHD '래퍼' 없이 Azure Blob 저장소에 직접 데이터베이스 파일을 저장할 수 있습니다. 특히 표준 Azure 저장소 또는 더 작은 VM 유형을 사용할 경우 일부 더 작은 VM 유형에 탑재할 수 있는 VHD 수를 제한하여 IOPS 제한을 극복할 수 있습니다. 이 방법은 사용자 데이터베이스에는 적용되지만 SQL Server의 시스템 데이터베이스에는 사용할 수 없습니다. SQL Server의 데이터 및 로그 파일에도 사용할 수 있습니다. VHD로 '래핑'하지 않고 이 방법으로 SAP SQL Server 데이터베이스를 배포하려는 경우 다음 사항에 유의하세요.

* 사용된 저장소 계정이 SQL Server가 실행 중인 VM을 배포하는 데 사용된 것과 동일한 Azure 지역에 있어야 합니다.
* 앞 부분에서 설명한 여러 Azure 저장소 계정에 VHD 분산과 관련된 고려 사항이 이 배포 방법에도 적용됩니다. Azure 저장소 계정의 제한에 대한 I/O 작업 수를 의미합니다. 
[설명]: <> (MSSedusch TODO 하지만 네트워크 대역폭을 사용하고 저장소 대역폭을 사용하지 않습니다. 그렇죠?)

이 배포 형식에 대한 자세한 내용은 <https://msdn.microsoft.com/library/dn385720.aspx>를 참조하세요.
 
SQL Server 데이터 파일을 Azure 프리미엄 저장소에 직접 저장하려면 최소 <https://support.microsoft.com/kb/3063054>에서 설명한 SQL Server 2014 패치 릴리스가 필요합니다. Azure 표준 저장소에 SQL Server 데이터 파일을 저장하는 작업은 SQL Server 2014의 릴리스 버전에서 사용할 수 있습니다. 그러나 해당 패치에는 SQL Server 데이터 파일 및 백업을 위한 Azure Blob 저장소의 직접 사용을 더 안정적으로 만드는 수정 사항과 다른 시리즈의 수정 사항이 포함되어 있습니다. 따라서 일반적으로 이러한 패치를 사용하는 것이 좋습니다.

### SQL Server 2014 버퍼 풀 확장
SQL Server 2014에는 버퍼 풀 확장이라는 새로운 기능이 도입되었습니다. 이 기능은 메모리에 저장된 SQL Server의 버퍼 풀과 서버 또는 VM의 로컬 SSD에서 지원되는 보조 수준 캐시를 확장합니다. 따라서 '메모리 내'에서 더 큰 데이터 작업 집합을 유지할 수 있습니다. Azure 표준 저장소 액세스에 비해 Azure VM의 로컬 SSD에 저장된 버퍼 풀 확장에 대한 액세스는 많은 요소를 더 빠르게 만듭니다. 따라서 뛰어난 IOPS 및 처리량을 갖는 VM 유형의 로컬 D:\\ 드라이브를 활용할 경우 Azure 저장소에 대한 IOPS 부하를 줄이고 쿼리 응답 시간을 크게 향상시킬 수 있습니다. 이 이점은 프리미엄 저장소를 사용하지 않는 경우에 특히 적용됩니다. 프리미엄 저장소와 계산 노드에서 프리미엄 Azure 읽기 캐시를 사용하는 경우 데이터 파일에 대해 권장된 것처럼 큰 차이가 없습니다. 그 이유는 두 캐시(SQL Server 버퍼 풀 확장 및 프리미엄 저장소 읽기 캐시)가 계산 노드의 로컬 디스크를 사용하기 때문입니다. 이 기능에 대한 자세한 내용은 <https://msdn.microsoft.com/library/dn133176.aspx> 문서를 참조하세요.

### SQL Server에 대한 백업/복구 고려 사항
Azure에 SQL Server를 배포하는 경우 백업 방법을 검토해야 합니다. 생산성이 높은 시스템이 아니더라도 SQL Server에서 호스트하는 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure 저장소에는 이제 세 개의 이미지가 있으므로 저장소 작동 중단을 보완하는 측면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복구 계획 유지 관리가 중요한 이유는 특정 시점 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 예를 들어 백업을 복구하여 2계층 SAP 구성을 동일한 시스템의 3계층 시스템 설정으로 전송할 수 있습니다.

SQL Server를 Azure 저장소에 백업하는 방법은 세 가지가 있습니다.
 
1. SQL Server 2012 CU4 이상에서는 기본적으로 데이터베이스를 URL에 백업할 수 있습니다. 이 내용은 [SQL Server 2014의 새로운 기능 – 5부 - 백업/복원 향상](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx) 블로그에 자세히 나와 있습니다. [SQL Server 2012 SP1 CU4 이상][dbms-guide-5.5.1] 챕터를 참조하세요.
1. SQL 2012 CU4 이전의 SQL Server 릴리스는 리디렉션 기능을 사용하여 VHD에 백업하고 기본적으로 쓰기 스트림을 구성된 Azure 저장소 위치로 이동합니다. [SQL Server 2012 SP1 CU3 및 이전 버전][dbms-guide-5.5.2] 챕터를 참조하세요.
1. 마지막 방법은 기존의 디스크에 SQL Server 백업 명령을 VHD 디스크 장치에 백업하는 것입니다. 이는 온-프레미스 배포 패턴과 동일하며 이 문서에서 자세히 설명하지 않습니다.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 이상
이 기능을 사용하면 Azure BLOB 저장소에 직접 백업할 수 있습니다. 이 방법을 사용하지 않을 경우 VHD 및 IOPS 용량을 사용하는 다른 Azure VHD에 백업해야 합니다. 기본 개념은 다음과 같습니다.
 
 ![Microsoft Azure 저장소 BLOB에 SQL Server 2012 백업 사용][dbms-guide-figure-400]

이 경우 SQL Server 백업을 저장할 VHD가 필요하지 않습니다. 그러므로 할당되는 VHD 수가 적고 VHD IOPS의 전체 대역폭을 데이터 및 로그 파일에 사용할 수 있습니다. <https://msdn.microsoft.com/library/dn435916.aspx#limitations> 문서의 '제한' 섹션에 설명된 대로 최대 백업 크기는 1TB로 제한되어 있습니다. SQL Server 백업 압축을 사용해도 백업 크기가 1TB를 넘으면 이 문서의 [SQL Server 2012 SP1 CU3 및 이전 버전][dbms-guide-5.5.2] 챕터에서 설명한 기능을 사용해야 합니다.

Azure Blob 저장소 백업에서의 데이터베이스 복원을 설명하는 [관련 문서](https://msdn.microsoft.com/library/dn449492.aspx)에서는 백업 크기가 25GB를 넘을 경우 Azure BLOB 저장소에서 직접 복원하지 않도록 권장하고 있습니다. 이 문서에서는 기능 제한 때문이 아니라 단순히 성능 고려 사항을 기준으로 한 것입니다. 따라서 상황별로 서로 다른 조건이 적용될 수 있습니다.

이러한 형식의 백업 설정 및 활용 방법에 대한 설명서는 [이](https://msdn.microsoft.com/library/dn466438.aspx) 자습서에서 확인할 수 있습니다.
 
단계 순서의 예제는 [여기](https://msdn.microsoft.com/library/dn435916.aspx)를 참조하세요.

백업 자동화는 각 백업의 BLOB 이름을 다르게 지정하도록 하기 위해 중요합니다. 그렇지 않은 경우 백업이 덮어써지며 복원 체인이 끊어집니다.
 
3가지 백업 유형이 혼합되지 않게 하려면 백업에 사용되는 저장소 계정에 다른 컨테이너를 생성하는 것이 좋습니다. 컨테이너는 VM 전용 또는 VM과 백업 형식이 될 수 있습니다. 스키마는 다음과 같습니다.
 
 ![Microsoft Azure 저장소 BLOB에 SQL Server 2012 백업 사용 - 개별 저장소 계정의 다양한 컨테이너][dbms-guide-figure-500]

위의 예제에서 백업은 VM이 배포된 것과 동일한 저장소 계정으로 수행할 수 없습니다. 특히 백업을 위한 새 저장소 계정이 있어야 합니다. 저장소 계정 내에는 백업 형식 및 VM 이름의 매트릭스를 사용하여 생성된 여러 컨테이너가 있습니다. 이러한 구분을 사용하면 여러 VM의 백업을 쉽게 관리할 수 있습니다.

백업에 직접 쓰는 BLOB은 VM의 VHD 수에 추가되지 않습니다. 따라서 데이터 및 트랜잭션 로그 파일을 위해 특정 VM SKU에 탑재되는 최대 VHD 수를 최대화하고 저장소 컨테이너에 대해 백업을 실행할 수 있습니다.

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 및 이전 버전
Azure Storage에 대해 백업을 직접 수행하려면 먼저 [이](https://www.microsoft.com/download/details.aspx?id=40740) KBA 문서에 연결된 msi를 다운로드해야 합니다.
 
x64 설치 파일 및 설명서를 다운로드합니다. 이 파일은 ‘Microsoft SQL Server Backup to Microsoft Azure Tool’ 프로그램을 설치합니다. 제품 설명서를 자세히 읽어보세요. 이 도구는 기본적으로 다음과 같은 방식으로 작동합니다.

* SQL Server 측에서 SQL Server 백업에 대한 디스크 위치가 정의됩니다(D:\\ 드라이브 사용 안 함).
* 이 도구를 사용하면 다른 유형의 백업을 서로 다른 Azure 저장소 컨테이너에 저장할 수 있는 규칙을 정의할 수 있습니다.
* 규칙이 적용되면 이 도구가 VHD/디스크 중 하나에 대한 백업의 쓰기 스트림을 앞에서 정의한 Azure 저장소 위치로 리디렉션합니다.
* 이 도구는 SQL Server 백업에 대해 정의된 VHD/디스크에서 약간의 스텁 파일(몇 KB)을 남겨 둡니다. **이 파일은 Azure 저장소에서 다시 복원하는 데 필요하므로 저장소 위치에 남겨 두어야 합니다.**
	* 스텁 파일이 손실되고(예: 스텁 파일이 포함된 저장소 미디어 손실) Microsoft Azure 저장소 계정에 백업하는 옵션을 선택한 경우 저장소 컨테이너에서 다운로드하여 Microsoft Azure 저장소를 통해 스텁 파일을 복구할 수 있습니다. 그런 다음 암호화가 원래 규칙을 통해 사용된 경우 도구가 동일한 암호화 암호를 사용하여 검색 및 동일한 컨테이너에 업로드하도록 구성된 로컬 컴퓨터의 폴더에 스텁 파일을 배치합니다.

이는 위에서 설명한 것처럼 최신 릴리스의 SQL Server에 대한 스키마를 Azure 저장소 위치의 주소 지정을 직접 허용하지 않는 SQL Server 릴리스에도 배치할 수 있다는 의미입니다.
 
이 방법은 Azure 저장소에 대해 기본적으로 백업을 지원하는 최신 SQL Server 버전에서는 사용하지 않아야 합니다. 단, Azure로의 기본 백업 제한이 Azure로의 기본 백업 실행을 차단합니다.

#### SQL Server 데이터베이스를 백업하는 다른 방법
데이터베이스를 백업하는 다른 방법은 백업을 저장하는 데 사용하는 VM에 추가 VHD를 연결하는 것입니다. 이러한 경우 VHD가 모두 실행되고 있지 않은지 확인해야 합니다. 해당하는 경우 VHD의 탑재를 해제하고 '보관'한 다음 빈 VHD로 바꿔야 합니다. 패치를 다운한 경우 데이터베이스 파일이 있는 VHD에서 이러한 VHD를 별도의 Azure 저장소 계정에 보관해야 합니다.

두 번째 방법은 많은 VHD를 연결할 수 있는 대용량 VM을 사용하는 것입니다. 예: 32VHD가 있는 D14. 저장소 공간을 사용하여 사용되는 공유를 빌드할 수 있는 유연한 환경을 구성한 다음 다양한 DBMS 서버에 대한 백업 대상으로 만듭니다.
 
몇 가지 모범 사례가 [여기](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx)에 설명되어 있습니다.

#### 백업/복원에 대한 성능 고려 사항
완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개 CPU 스레드까지 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터 파일을 저장하는 데 사용하는 VHD 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(BLOB 또는 VHD) 수가 적을수록 처리량이 줄어듭니다.
* VM 크기가 작을수록 Azure 저장소에서 쓰고 읽는 저장소 처리량 할당량이 줄어듭니다. 백업이 Azure Blob에 직접 저장되는지 여부 또는 VHD에 저장되는지 여부에 관계없이 다시 Azure Blob에 저장됩니다.

Microsoft Azure 저장소 BLOB을 더 최신 버전의 백업 대상으로 사용할 경우 각 특정 백업에 대해 하나의 URL 대상만 지정할 수 있습니다.
 
그러나 이전 버전에서 ‘Microsoft SQL Server Backup to Microsoft Azure Tool’을 사용할 때는 둘 이상의 파일 대상을 정의할 수 있습니다. 둘 이상의 대상을 사용하여 백업을 확장하고 백업 처리량을 늘릴 수 있습니다. 그 결과 Azure 저장소 계정에도 여러 파일이 만들어집니다. 테스트에 따르면 여러 파일 대상을 사용하여 SQL Server 2012 SP1 CU4에 구현된 백업 확장으로 얻을 수 있는 처리량을 확실하게 얻을 수 있습니다. Azure로의 기본 백업 크기가 1TB로 제한되지도 않습니다.

그러나 처리량은 백업에 사용하는 Azure 저장소 계정의 위치에 따라 달라집니다. VM이 실행되지 않는 다른 지역에서 저장소 계정을 찾을 수 있습니다. 예를 들어 유럽 서부에서 VM 구성을 실행하고 백업에 사용할 저장소 계정을 북유럽에 둘 수 있습니다. 이렇게 하면 대상 저장소 및 VM이 동일한 지역 데이터 센터에서 실행될 수 있으므로 백업 처리량에 영향을 주고 150MB/초의 처리량을 생성할 가능성이 없습니다.

#### 백업 BLOB 관리
백업을 직접 관리해야 합니다. 잦은 트랜잭션 로그 백업의 실행으로 많은 blob이 생성되므로 이러한 blob의 관리를 통해 쉽게 Azure 포털의 작업 부하를 줄일 수 있습니다. 따라서 Azure 저장소 탐색기를 활용하는 것이 좋습니다. Azure 저장소 계정을 관리하는 데 도움이 되는 몇 가지 도구를 사용할 수 있습니다.

* Azure SDK가 설치된 Microsoft Visual Studio(<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage 탐색기(<https://azure.microsoft.com/downloads/>)
* 타사 도구

[설명]: <> (아직 ARM에서 지원되지 않음) 
[설명]: <> (### Azure VM 백업) 
[설명]: <> (SAP 시스템 내의 VM은 Azure 가상 컴퓨터 백업 기능을 사용하여 백업할 수 있습니다. Azure 가상 컴퓨터 백업은 2015년 초반에 도입된 이후 Azure에서 전체 VM을 백업하는 표준 방법이 되었습니다. Azure 백업은 백업을 Azure에 저장하며 VM의 복원을 허용합니다.) 
[설명]: <> (DBMS 시스템에서 Windows VSS(볼륨 섀도 복사본 서비스 - <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>)를 지원하는 경우 데이터베이스를 실행하는 VM을 SQL Server 등 일관성 있는 방법으로 백업할 수 있습니다. 따라서 Azure VM 백업을 사용하여 SAP 데이터베이스의 복원 가능한 백업을 가져올 수 있습니다. 그러나 데이터베이스의 Azure VM 백업 기반의 지정 시간 복원은 가능하지 않습니다. 따라서 Azure VM 백업을 사용하지 않고 DBMS 기능을 사용하여 데이터베이스 백업을 수행하는 것이 좋습니다.) 
[설명]: <> (Azure 가상 컴퓨터 백업 기능을 익히려면 여기 <https://azure.microsoft.com/documentation/services/backup/>에서 시작하세요.)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Microsoft Azure 마켓플레이스에서 SQL Server 이미지 사용
Microsoft는 Azure 마켓플레이스에서 이미 SQL Server를 포함하는 버전의 VM을 제공합니다. SQL Server 및 Windows 라이선스가 필요한 SAP 고객의 경우, 이미 SQL Server가 설치된 VM을 스핀업하여 라이선스에 대한 요구 사항을 충족시킬 수 있습니다. SAP에 대한 이러한 이미지를 사용하려면 다음 사항을 고려해야 합니다.

* 평가판이 아닌 SQL Server 버전은 Azure 마켓플레이스에서 배포된 'Windows 전용' VM보다 비용이 더 높습니다. 가격을 비교하려면 <https://azure.microsoft.com/pricing/details/virtual-machines/> 및 <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql> 문서를 참조하세요.
* SQL Server 2012처럼 SAP에서 지원하는 SQL Server 릴리스만 사용할 수 있습니다.
* Azure 마켓플레이스에서 제공되는 VM에 설치되는 SQL Server 인스턴스의 데이터 정렬은 SAP NetWeaver에서 SQL Server 인스턴스를 실행하는 데 필요한 데이터 정렬이 아닙니다. 다음 섹션의 지침을 사용하여 데이터 정렬을 변경할 수 있습니다.

#### Microsoft Windows/SQL Server VM의 SQL Server 데이터 정렬 변경
Azure 마켓플레이스의 SQL Server 이미지는 SAP NetWeaver 응용 프로그램에서 요구하는 데이터 정렬을 사용하도록 설정되어 있지 않으므로 배포 후 즉시 변경해야 합니다. SQL Server 2012의 경우 VM을 배포하자마자 다음 단계를 통해 이 작업을 수행할 수 있으며 관리자가 배포된 VM에 로그인할 수 있습니다.

* '관리자'로 Windows 명령 창을 엽니다.
* 디렉터리를 C:\\Program Files\\Microsoft SQL Server\\110\\Setup Bootstrap\\SQLServer2012로 변경합니다.
* Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL\_Latin1\_General\_Cp850\_BIN2 명령을 실행합니다.
	* `<local_admin_account_name`>은 갤러리를 통해 처음으로 VM을 배포할 때 관리자 계정으로 정의된 계정입니다.

이 프로세스는 몇 분밖에 안 걸립니다. 단계가 올바르게 수행되었는지 확인하려면 다음 단계를 수행하세요.

* SQL Server Management Studio를 엽니다.
* 쿼리 창을 엽니다.
* SQL Server master 데이터베이스에서 sp\_helpsort 명령을 실행합니다.

다음과 같은 결과가 나와야 합니다.

	Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

원하는 결과가 아닌 경우 SAP 배포를 중지하고 설치 명령이 원하는 대로 작동되지 않은 이유를 확인합니다. 위에서 언급한 것과 다른 SQL Server 코드 페이지를 사용하여 SAP NetWeaver 응용 프로그램을 SQL Server 인스턴스에 배포할 수는 **없습니다**.

### Azure의 SAP용 SQL Server 고가용성
이 문서 앞부분에서 언급했듯이 이전 SQL Server 고가용성 기능 활용에 필요한 공유 저장소를 만들 수는 없습니다. 이 기능은 사용자 데이터베이스(및 tempdb)용 공유 디스크를 사용하여 WSFC(Windows Server 장애 조치(failover) 클러스터)에 둘 이상의 SQL Server 인스턴스를 설치합니다. 이는 SAP에서도 지원되는 오래된 표준 고가용성 방법입니다. Azure에서는 공유 저장소를 지원하지 않으므로 공유 디스크 클러스터 구성의 SQL Server 고가용성 구성을 인식할 수 없습니다. 그러나 여러 가지 다른 고가용성 방법을 사용할 수 있으며 다음 섹션에서 설명합니다.

[설명]: <> (문서에서는 여전히 ASM을 참조) 
[설명]: <> (Azure의 SQL Server에 사용 가능한 다른 특정 고가용성 기술을 읽기 전에 [여기][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]에서 자세한 내용을 참조하세요.)

#### SQL Server 로그 전달
HA(고가용성) 방법 중 하나는 SQL Server 로그 전달입니다. HA 구성에 참여하는 VM에 이름 확인 작업이 있는 경우 아무 문제가 없으며 Azure 설정이 온-프레미스의 설정과 다르지 않습니다. IP 확인만 사용하는 것은 권장되지 않습니다. 로그 전달 및 로그 전달 관련 원칙 설정에 대한 내용은 이 설명서를 참조하세요.

<https://technet.microsoft.com/library/ms187103.aspx>
 
고가용성을 실현하기 위해서는 이러한 로그 전달 구성 내에 있는 VM을 동일한 Azure 가용성 집합 내에 있도록 배포해야 합니다.

#### 데이터베이스 미러링
SAP에서 지원하는 데이터베이스 미러링(SAP Note [965908] 참조)은 SAP 연결 문자열의 장애 조치(failover) 파트너 정의를 사용합니다. 프레미스 간 사례의 경우 두 VM이 동일한 도메인에 있고 두 개의 SQL Server 인스턴스를 실행 중인 사용자 컨텍스트도 도메인 사용자이며 관련된 두 개의 SQL Server 인스턴스에 대한 충분한 권한이 있다고 가정합니다. 따라서 Azure에서의 데이터베이스 미러링 설정은 일반적인 온-프레미스 설정/구성 간에 다르지 않습니다.

클라우드 전용 배포 시 가장 쉬운 방법은 Azure에서 이러한 DBMS VM(및 이상적인 전용 SAP VM)을 한 도메인 내에 배치할 수 있도록 다른 도메인을 설정하는 것입니다.

도메인을 사용할 수 없는 경우 <https://technet.microsoft.com/library/ms191477.aspx>에 설명된 대로 데이터베이스 미러링 끝점에 대한 인증서를 사용할 수도 있습니다.

Azure에서 데이터베이스 미러링을 설정하는 자습서는 <https://technet.microsoft.com/library/ms189852.aspx>에서 찾을 수 있습니다.

#### AlwaysOn
AlwaysOn은 SAP 온-프레미스에 대해 지원되므로(SAP Note [1772688] 참조) Azure에서 SAP와 함께 사용할 수 없습니다. Azure에서 공유 디스크를 만들 수는 없지만 여러 VM 간에 AlwaysOn WSFC(Windows Server 장애 조치(failover) 클러스터) 구성을 만들 수는 있습니다. 단, 클러스터 구성에서 공유 디스크를 쿼럼으로 사용할 수는 없습니다. 따라서 Azure에서 AlwaysOn WSFC 구성을 빌드할 수 있으며 단순히 공유 디스크를 사용하는 쿼럼 유형을 선택할 수 없습니다. 이러한 VM이 배포된 Azure 환경은 VM을 이름으로 확인해야 하며 VM이 동일한 도메인에 있어야 합니다. 이는 Azure 전용 및 프레미스 간 배포에만 적용됩니다. SQL Server 가용성 그룹 수신기(Azure 가용성 집합과 다름) 배포와 관련된 일부 특별 고려 사항이 있으며 Azure는 이 시점에서 온-프레미스처럼 AD/DNS 개체를 만들 수 없습니다. 따라서 Azure의 특정 동작을 극복하려면 몇 가지 다른 설치 단계가 필요합니다.

가용성 그룹 수신기를 사용하는 경우 몇 가지 고려 사항이 있습니다.

* 가용성 그룹 수신기는 Windows Server 2012 또는 Windows Server 2012 R2를 VM의 게스트 OS로 사용할 때만 사용할 수 있습니다. Windows Server 2012의 경우 <https://support.microsoft.com/kb/2854082> 패치가 적용되고 있는지 확인해야 합니다.
* Windows Server 2008 R2의 경우 이 패치가 없고 연결 문자열에서 장애 조치(failover) 파트너를 지정하여 데이터베이스 미러링과 같은 방법으로 AlwaysOn을 사용해야 합니다(SAP default.pfl 매개 변수 dbs/mss/server를 통해 수행 - SAP Note [965908] 참조).
* 가용성 그룹 수신기를 사용할 경우 데이터베이스 VM을 전용 부하 분산 장치에 연결해야 합니다. 클라우드 전용 배포의 이름을 확인하려면 SAP 시스템의 모든 VM(응용 프로그램 서버, DBMS 서버 및 (A)SCS 서버)이 동일한 가상 네트워크에 있어야 하며, SQL Server VM의 VM 이름을 확인하려면 SAP 응용 프로그램 계층에서 etc\\host 파일을 유지 관리해야 합니다. 두 VM이 동시에 종료되는 경우 Azure에서 새 IP 주소를 할당하지 않게 하려면 AlwaysOn 구성에서 이러한 VM의 네트워크 인터페이스에 대해 고정 IP 주소를 할당해야 합니다(고정 IP 주소 정의는 [이][virtual-networks-reserved-private-ip] 문서에서 설명합니다.) 
[설명]: <> (이전 블로그) 
[설명]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>)
* 현재 기능의 Azure는 클러스터가 만들어진 노드와 동일한 IP 주소를 클러스터 이름에 할당하므로 클러스터에 특정 IP 주소를 할당해야 하는 WSFC 클러스터를 구성할 때는 특별한 단계가 필요합니다. 즉, 클러스터에 다른 IP 주소를 할당하기 위해서는 수동 단계를 수행해야 합니다.
* 가용성 그룹 수신기는 가용성 그룹의 기본 및 보조 복제본을 실행 중인 VM에 할당된 TCP/IP 끝점을 사용하여 Azure에서 만들어집니다.
* 이러한 끝점은 ACL로 보호해야 할 수 있습니다.

[설명]: <> (TODO 이전 블로그) 
[설명]: <> (Azure에서 AlwaysOn 구성 설치에 대한 자세한 단계 및 필요성은 [여기][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]에서 사용할 수 있는 자습서를 살펴보는 것이 좋습니다.) 
[설명]: <> (Azure 갤러리를 통해 미리 구성된 AlwaysOn 설정 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>) 
[설명]: <> (가용성 그룹 수신기 생성은 [이][virtual-machines-windows-classic-ps-sql-int-listener] 자습서에 잘 설명되어 있습니다.) 
[설명]: <> (ACL을 사용한 네트워크 끝점 보호는 여기를 참조하세요.) 
[설명]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>) 
[설명]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>) 
[설명]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>) 
[설명]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>)

여러 Azure 지역에 SQL Server AlwaysOn 가용성 그룹을 배포할 수도 있습니다. 이 기능은 Azure VNet-VNet 연결을 활용합니다([자세한 내용][virtual-networks-configure-vnet-to-vnet-connection]). 
[설명]: <> (TODO 이전 블로그) 
[설명]: <> (이러한 시나리오의 SQL Server AlwaysOn 가용성 그룹의 설정은 여기 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>에 설명되어 있습니다.)

#### Azure의 SQL Server 고가용성 요약
Azure 저장소가 콘텐츠를 보호하는 경우 상시 대기 이미지를 고집할 이유가 줄어듭니다. 즉, 고가용성 시나리오는 다음과 같은 경우에 대해서만 방지해야 합니다.

* Azure의 서버 클러스터 유지 관리 또는 기타 이유로 인해 VM을 전체적으로 사용할 수 없는 경우
* SQL Server 인스턴스에 소프트웨어 문제가 발생한 경우
* 데이터가 삭제되고 지정 시간 복구가 필요하여 수동 오류로부터 보호해야 하는 경우

기술적인 면에서 처음 두 경우는 데이터베이스 미러링 또는 AlwaysOn을 통해 해결할 수 있으며 세 번째 경우는 로그 전달을 통해서만 가능합니다.

AlwaysOn의 경우 데이터베이스 미러링에 비해 더 복잡한 설정이 필요하지만 AlwaysOn의 이점이 있습니다. 이러한 이점은 다음과 같습니다.

*	읽기 가능한 보조 복제본
*	보조 복제본에서 백업
*	향상된 확장성
*	둘 이상의 보조 복제본

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Azure의 SAP용 일반 SQL Server 요약
이 가이드에는 많은 권장 사항이 있으며 Azure 배포를 계획하기 전에 두 번 이상 읽는 것이 좋습니다. 하지만 일반적으로 상위 10개의 일반 Azure DBMS 특정 사항을 따라야 합니다.

[설명]: <> (2.3 무엇보다 더 높은 처리량? 하나보다 많은 VHD?)
1. Azure에서 가장 많은 이점을 제공하는 SQL Server 2014와 같은 최신 DBMS 릴리스를 사용합니다. SQL Server의 경우 Azure 저장소 백업 기능을 포함하는 SQL Server 2012 SP1 CU4입니다. 그러나 SAP와 함께 최소 SQL Server 2014 SP1 CU1 또는 SQL Server 2012 SP2 및 최신 CU를 권장합니다.
1. 데이터 파일 레이아웃과 Azure 제한 사항 균형을 조정하도록 Azure에서 SAP 시스템 배경을 신중하게 계획합니다.
	* VHD가 너무 많으면 안 되지만 필요한 IOPS에 도달할 수 있을 만큼 충분해야 합니다.
	* IOPS는 Azure 저장소 계정당 제한되어 있으며 저장소 계정은 각 Azure 구독 내에서 제한됩니다([자세한 내용][azure-subscription-service-limits]).
	* 더 높은 처리량이 필요한 경우에만 VHD를 스트라이프합니다.
1. D:\\ 드라이브는 비영구적이며 Windows 재부팅 시 이 드라이브의 내용이 모두 손실되므로 계속 유지해야 하는 소프트웨어를 설치하거나 파일을 저장하지 마세요.
1. Azure 표준 저장소에 대해 Azure VHD 캐싱을 사용하지 마세요.
1. Azure 지역에서 복제된 저장소 계정을 사용하지 마세요. DBMS 워크로드에 대한 로컬 중복을 사용합니다.
1. DBMS 공급업체의 HA/DR 솔루션을 사용하여 데이터베이스 데이터를 복제합니다.
1. 항상 이름 확인을 사용하고 IP 주소를 사용하지 마세요.
1. 가능한 가장 높은 데이터베이스 압축을 사용합니다. SQL Server의 경우 페이지 압축입니다.
1. Azure 마켓플레이스의 SQL Server 이미지를 사용할 때는 주의하세요. SQL Server 이미지를 사용하는 경우 SAP NetWeaver 시스템을 설치하기 전에 인스턴스 데이터 정렬을 변경해야 합니다.
1. [배포 가이드][deployment-guide]의 설명에 따라 Azure용 SAP 호스트 모니터링을 설치 및 구성합니다.

## Windows의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 응용 프로그램을 쉽게 Azure 가상 컴퓨터로 마이그레이션할 수 있습니다. 가상 컴퓨터에서 SAP ASE를 사용하면 이러한 응용 프로그램을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 응용 프로그램에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure 가상 컴퓨터에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Azure 가상 컴퓨터용 SLA는 <https://azure.microsoft.com/support/legal/sla>에서 찾을 수 있습니다.

Microsoft Azure에서 호스트되는 가상 컴퓨터는 다른 공용 클라우드 가상화 서비스에 비해 훨씬 뛰어난 성능을 제공하지만 개별 결과는 다를 수 있습니다. 서로 다른 SAP 인증 VM SKU의 SAP 크기 조정 SAPS 번호는 별도 SAP Note [1928533]에서 제공됩니다.

이 문서의 처음 네 챕터에서 설명한 것처럼 SAP 응용 프로그램과 함께 SAP ASE를 배포할 때는 Azure 저장소 사용, SAP VM 또는 SAP 모니터링 배포와 관련된 설명 및 권장 사항이 적용됩니다.

### SAP ASE 버전 지원 
SAP는 현재 SAP ASE 버전 16.0을 SAP Business Suite 제품과 함께 사용하도록 지원합니다. SAP Business Suite 제품과 함께 사용할 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에서 SAP Service Marketplace를 통해서만 제공됩니다.

온-프레미스 설치와 마찬가지로 Sybase 웹 사이트에서 직접 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 업데이트를 다운로드하지 마세요. 온-프레미스 및 Azure 가상 컴퓨터에서 SAP Business Suite 제품을 사용하도록 지원되는 패치에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

* [1590719]
* [1973241]
 
SAP ASE에서의 SAP Business Suite 실행에 대한 일반 정보는 [SCN](https://scn.sap.com/community/ase)에서 찾을 수 있습니다.

### Azure VM의 SAP 관련 SAP ASE 설치에 대한 SAP ASE 구성 지침

#### SAP ASE 배포의 구조
일반적인 설명에 따라 SAP ASE 실행 파일을 VM의 기본 VHD에 있는 시스템 드라이브(드라이브 C:)에 배치 또는 설치해야 합니다. 일반적으로 대부분의 SAP ASE 시스템 및 도구 데이터베이스는 실제로 SAP NetWeaver 워크로드에서 사용되지 않습니다. 따라서 시스템 및 도구 데이터베이스(master, model, saptools, sybmgmtdb, sybsystemdb)가 C:\\ 드라이브에 남아 있을 수 있습니다.

단, SAP ASE에 의해 만들어진 모든 작업 테이블 및 임시 테이블을 포함하는 임시 데이터베이스는 예외입니다. 이 경우 일부 SAP ERP 및 모든 BW 워크로드에서 원본 VM의 기본 VHD(C: 드라이브)에 맞지 않는 더 큰 데이터 볼륨 또는 I/O 작업 볼륨이 필요할 수 있습니다.
 
시스템을 설치하는 데 사용되는 SAPInst/SWPM의 버전에 따라 데이터베이스에 다음 항목이 포함될 수 있습니다.

* SAP ASE를 설치할 때 만들어지는 단일 SAP ASE tempdb
* SAP 설치 루틴에 의해 만들어지는 SAP ASE 및 추가 saptempdb를 설치하여 만들어지는 SAP ASE tempdb
* ERP/BW 특정 tempdb 요구 사항에 맞게 SAP ASE 및 수동으로 만든 추가 tempdb를 설치하여 만들어지는 SAP ASE tempdb(예: SAP Note [1752266])

특정 ERP 또는 모든 BW 워크로드의 경우 성능을 위해 추가로 생성된 tempdb 장치(SWPM을 통해 또는 수동으로)를 C:가 아닌 다른 드라이브에 유지해야 합니다. 추가 tempdb가 없는 경우 새로 만드는 것이 좋습니다(SAP Note [1752266]).

이러한 시스템의 경우 추가로 생성된 tempdb에 대해 다음 단계를 수행해야 합니다.

* 첫 번째 tempdb 장치를 SAP 데이터베이스의 첫 번째 장치로 이동
* SAP 데이터베이스의 장치를 포함하는 각 VHD에 tempdb 장치 추가

이 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 참조로 온-프레미스에서 실행 중인 기존 시스템에서 tempdb 장치 크기를 확인할 수 있습니다. 또는 이러한 구성은 시스템 드라이브에서 tempdb에 대해 제공할 수 있는 것보다 더 많은 IOPS 수를 사용합니다. 다시 말하지만 온-프레미스에서 실행 중인 시스템에서 tempdb에 대해 I/O 워크로드를 모니터링할 수 있습니다.

SAP ASE 장치를 VM의 D:\\ 드라이브에 배치하지 마세요. 이는 tempdb에도 적용되며 tempdb에 저장된 개체가 임시 개체인 경우에도 마찬가지입니다.

#### 데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 확장할 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인해야 합니다.

아직 구현되지 않은 경우 Azure에 업로드하기 전에 압축을 수행해야 하는 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure 가상 컴퓨터에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인하는 방법은 SAP Note [1750510]을 참조하세요.

#### DBACockpit를 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. webdynpro를 사용하도록 설정하고 필요한 경우 생성하려면 SAP Note [1245200]을 참조하세요. 위의 Note 지침을 수행하면 http 및 https 연결에 사용할 포트와 함께 ICM(Internet Communication Manager)도 구성하게 됩니다. Http에 대한 기본 설정은 다음과 같습니다.

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

트랜잭션 DBACockpit에서 생성된 링크는 다음과 같습니다.

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

SAP 시스템을 호스트하는 Azure 가상 컴퓨터가 사이트 간, 다중 사이트 또는 Express 경로(프레미스 간 배포)를 통해 연결되었는지 여부 및 그 방법에 따라 ICM이 DBACockpit를 열려는 컴퓨터에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수에 따라 정규화된 호스트 이름을 확인하는 방법을 이해하고 필요한 경우 icm/host\_name\_full 매개 변수를 명시적으로 설정하려면 SAP Note [773830]을 참조하세요.

온-프레미스와 Azure 간에 프레미스 간 연결이 없는 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소 및 도메인 레이블을 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같습니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

DNS 이름에 대한 자세한 내용은 [여기][virtual-machines-azurerm-versus-azuresm]에서 확인할 수 있습니다.

SAP 프로필 매개 변수 icm/host\_name\_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit

> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure 포털의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [1558958]
* [1619967]
* [1882376]

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]
* [1814258]
* [1922555]
* [1956005]

#### SAP ASE에 대한 백업/복구 고려 사항
Azure에 SAP ASE를 배포하는 경우 백업 방법을 검토해야 합니다. 생산성이 높은 시스템이 아니더라도 SAP ASE에서 호스트하는 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure 저장소에는 이제 세 개의 이미지가 있으므로 저장소 작동 중단을 보완하는 측면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 예를 들어 백업을 복구하여 2계층 SAP 구성을 동일한 시스템의 3계층 시스템 설정으로 전송할 수 있습니다.

Azure에서의 데이터베이스 백업 및 복원은 온-프레미스와 동일한 방식으로 진행됩니다. 다음 SAP Note를 참조하세요.

* [1588316]
* [1585981]

여기에는 덤프 구성 생성 및 백업 예약에 대한 자세한 내용이 나와 있습니다. 전략 및 요구 사항에 따라 데이터베이스를 구성하고 디스크 덤프를 기존 VHD 중 하나에 기록하거나 백업을 위해 다른 VHD를 추가할 수 있습니다. 오류가 발생할 경우 데이터 손실의 위험을 줄이기 위해서는 데이터베이스 장치가 없는 VHD를 사용하는 것이 좋습니다.

데이터 및 LOB 압축 SAP ASE 외에도 백업 압축을 제공합니다. 데이터베이스 및 로그 덤프의 공간 사용량을 줄이려면 백업 압축을 사용하는 것이 좋습니다. 자세한 내용은 SAP Note [1588316]을 참조하세요. 백업 압축은 백업 또는 백업 덤프를 포함하는 VHD를 Azure 가상 컴퓨터에서 온-프레미스로 다운로드하려고 할 때 전송되는 데이터 양을 줄이는 데도 효과적입니다.

데이터베이스 또는 로그 덤프 대상으로 D:\\ 드라이브를 사용하지 마세요.

#### 백업/복원에 대한 성능 고려 사항
완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개 CPU 스레드까지 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 장치를 저장하는 데 사용하는 VHD 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, VHD) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 VHD에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 덤프를 작성하는 SAP ASE 수준에서 덤프 구성을 만듭니다.

탑재된 여러 VHD에 볼륨을 스트라이프하는 방법은 이 가이드의 앞부분에서 설명했습니다. 여러 디렉터리를 사용하여 SAP ASE 덤프를 구성하는 방법에 대한 자세한 내용은 [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp)에서 덤프 구성을 만드는 데 사용되는 저장 프로시저 sp\_config\_dump에 대한 설명서를 참조하세요.

### Azure VM을 사용한 재해 복구

#### SA Sybase Replication Server를 사용한 데이터 복제
SAP SRS(Sybase Replication Server)를 사용하면 SAP ASE에서 멀리 떨어진 위치에 데이터베이스 트랜잭션을 비동기적으로 전송하는 웜 대기 솔루션을 제공합니다.

SRS 설치 및 작동은 Azure 가상 컴퓨터 서비스에서 호스트되는 VM에서도 온-프레미스에서처럼 기능적으로 작동합니다.

SAP Replication Server를 통한 ASE HADR은 향후 릴리스에 도입될 예정입니다. 이 기능은 곧 Microsoft Azure Platform에 대해 테스트되고 릴리스될 예정입니다.

## Linux의 SAP ASE에 대한 고유 정보

Microsoft Azure부터 기존 SAP ASE 응용 프로그램을 쉽게 Azure 가상 컴퓨터로 마이그레이션할 수 있습니다. 가상 컴퓨터에서 SAP ASE를 사용하면 이러한 응용 프로그램을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 응용 프로그램에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure 가상 컴퓨터에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Azure VM을 배포하려면 공식 SLA를 알아야 합니다. <https://azure.microsoft.com/support/legal/sla>에서 확인할 수 있습니다.

SAP 크기 조정 정보 및 SAP 인증 VM SKU 목록은 SAP Note [1928533]을 참조하세요. Azure 가상 컴퓨터에 대한 추가 SAP 크기 조정 문서는 <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> 및 <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>를 참조하세요.

이 문서의 처음 네 챕터에서 설명한 것처럼 SAP 응용 프로그램과 함께 SAP ASE를 배포할 때는 Azure 저장소 사용, SAP VM 또는 SAP 모니터링 배포와 관련된 설명 및 권장 사항이 적용됩니다.

다음 두 SAP Note에는 Linux의 ASE 및 클라우드의 ASE에 대한 일반 정보가 포함되어 있습니다.

* [2134316]
* [1941500]

### SAP ASE 버전 지원 
SAP는 현재 SAP ASE 버전 16.0을 SAP Business Suite 제품과 함께 사용하도록 지원합니다. SAP Business Suite 제품과 함께 사용할 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에서 SAP Service Marketplace를 통해서만 제공됩니다.

온-프레미스 설치와 마찬가지로 Sybase 웹 사이트에서 직접 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 업데이트를 다운로드하지 마세요. 온-프레미스 및 Azure 가상 컴퓨터에서 SAP Business Suite 제품을 사용하도록 지원되는 패치에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

* [1590719]
* [1973241]
 
SAP ASE에서의 SAP Business Suite 실행에 대한 일반 정보는 [SCN](https://scn.sap.com/community/ase)에서 찾을 수 있습니다.

### Azure VM의 SAP 관련 SAP ASE 설치에 대한 SAP ASE 구성 지침

#### SAP ASE 배포의 구조
일반적인 설명에 따라 SAP ASE 실행 파일을 VM의 루트 파일 시스템(/sybase)에 배치 또는 설치해야 합니다. 일반적으로 대부분의 SAP ASE 시스템 및 도구 데이터베이스는 실제로 SAP NetWeaver 워크로드에서 사용되지 않습니다. 따라서 시스템 및 도구 데이터베이스(master, model, saptools, sybmgmtdb, sybsystemdb)가 루트 파일 시스템에 남아 있을 수 있습니다.

단, SAP ASE에서 만들어진 모든 작업 테이블 및 임시 테이블을 포함하는 임시 데이터베이스는 예외입니다. 이 경우 일부 SAP ERP 및 모든 BW 워크로드에서 원본 VM의 OS 디스크에 맞지 않는 더 큰 데이터 볼륨 또는 I/O 작업 볼륨이 필요할 수 있습니다.
 
시스템을 설치하는 데 사용되는 SAPInst/SWPM의 버전에 따라 데이터베이스에 다음 항목이 포함될 수 있습니다.

* SAP ASE를 설치할 때 만들어지는 단일 SAP ASE tempdb
* SAP 설치 루틴에 의해 만들어지는 SAP ASE 및 추가 saptempdb를 설치하여 만들어지는 SAP ASE tempdb
* ERP/BW 특정 tempdb 요구 사항에 맞게 SAP ASE 및 수동으로 만든 추가 tempdb를 설치하여 만들어지는 SAP ASE tempdb(예: SAP Note [1752266])

특정 ERP 또는 모든 BW 워크로드의 경우 성능을 위해 추가로 생성된 tempdb(SWPM을 통해 또는 수동으로)를 단일 Azure 데이터 디스크 또는 여러 Azure 데이터 디스크에 걸친 Linux RAID로 표현될 수 있는 별도의 파일 시스템에 유지해야 합니다. 추가 tempdb가 없는 경우 새로 만드는 것이 좋습니다(SAP Note [1752266]).

이러한 시스템의 경우 추가로 생성된 tempdb에 대해 다음 단계를 수행해야 합니다.

* SAP 데이터베이스의 첫 번째 파일 시스템으로 첫 번째 tempdb 디렉터리 이동
* SAP 데이터베이스의 파일 시스템을 포함하는 각 VHD에 tempdb 디렉터리 추가

이 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 참조로 온-프레미스에 실행 중인 기존 시스템에서 tempdb 디렉터리 크기를 확인할 수 있습니다. 또는 이러한 구성은 시스템 드라이브에서 tempdb에 대해 제공할 수 있는 것보다 더 많은 IOPS 수를 사용합니다. 다시 말하지만 온-프레미스에서 실행 중인 시스템에서 tempdb에 대해 I/O 워크로드를 모니터링할 수 있습니다.

SAP ASE 디렉터리를 VM의 /mnt 또는 /mnt/resource에 배치하지 마세요. 이는 tempdb에도 적용되며 tempdb에 저장된 개체가 임시 개체인 경우에도 마찬가지입니다. 왜냐하면 /mnt 또는 /mnt/resource가 비영구적인 기본 Azure VM 임시 공간이기 때문입니다. Azure VM의 임시 공간에 대한 자세한 내용은 [이 문서][virtual-machines-linux-how-to-attach-disk]에서 찾을 수 있습니다.

#### 데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 확장할 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인해야 합니다.

아직 구현되지 않은 경우 Azure에 업로드하기 전에 압축을 수행해야 하는 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure 가상 컴퓨터에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인하는 방법은 SAP Note [1750510]을 참조하세요. 또한 SAP Note [2121797]에서 데이터베이스 압축에 대한 자세한 내용을 참조하세요.

#### DBACockpit를 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. webdynpro를 사용하도록 설정하고 필요한 경우 생성하려면 SAP Note [1245200]을 참조하세요. 위의 Note 지침을 수행하면 http 및 https 연결에 사용할 포트와 함께 ICM(Internet Communication Manager)도 구성하게 됩니다. Http에 대한 기본 설정은 다음과 같습니다.

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

트랜잭션 DBACockpit에서 생성된 링크는 다음과 같습니다.

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

SAP 시스템을 호스트하는 Azure 가상 컴퓨터가 사이트 간, 다중 사이트 또는 Express 경로(프레미스 간 배포)를 통해 연결되었는지 여부 및 그 방법에 따라 ICM이 DBACockpit를 열려는 컴퓨터에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수에 따라 정규화된 호스트 이름을 확인하는 방법을 이해하고 필요한 경우 icm/host\_name\_full 매개 변수를 명시적으로 설정하려면 SAP Note [773830]을 참조하세요.

온-프레미스와 Azure 간에 프레미스 간 연결이 없는 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소 및 도메인 레이블을 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같습니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

DNS 이름에 대한 자세한 내용은 [여기][virtual-machines-azurerm-versus-azuresm]에서 확인할 수 있습니다.

SAP 프로필 매개 변수 icm/host\_name\_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure 포털의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [1558958]
* [1619967]
* [1882376]

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]
* [1814258]
* [1922555]
* [1956005]

#### SAP ASE에 대한 백업/복구 고려 사항
Azure에 SAP ASE를 배포하는 경우 백업 방법을 검토해야 합니다. 생산성이 높은 시스템이 아니더라도 SAP ASE에서 호스트하는 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure 저장소에는 이제 세 개의 이미지가 있으므로 저장소 작동 중단을 보완하는 측면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 예를 들어 백업을 복구하여 2계층 SAP 구성을 동일한 시스템의 3계층 시스템 설정으로 전송할 수 있습니다.

Azure에서의 데이터베이스 백업 및 복원은 온-프레미스와 동일한 방식으로 진행됩니다. 다음 SAP Note를 참조하세요.

* [1588316]
* [1585981]

여기에는 덤프 구성 생성 및 백업 예약에 대한 자세한 내용이 나와 있습니다. 전략 및 요구 사항에 따라 데이터베이스를 구성하고 디스크 덤프를 기존 VHD 중 하나에 기록하거나 백업을 위해 다른 VHD를 추가할 수 있습니다. 오류가 발생할 경우 데이터 손실의 위험을 줄이기 위해서는 데이터베이스 디렉터리/파일이 없는 VHD를 사용하는 것이 좋습니다.

데이터 및 LOB 압축 SAP ASE 외에도 백업 압축을 제공합니다. 데이터베이스 및 로그 덤프의 공간 사용량을 줄이려면 백업 압축을 사용하는 것이 좋습니다. 자세한 내용은 SAP Note [1588316]을 참조하세요. 백업 압축은 백업 또는 백업 덤프를 포함하는 VHD를 Azure 가상 컴퓨터에서 온-프레미스로 다운로드하려고 할 때 전송되는 데이터 양을 줄이는 데도 효과적입니다.

Azure VM 임시 공간 /mnt 또는 /mnt/resource를 데이터베이스 또는 로그 덤프 대상으로 사용하지 마세요.

#### 백업/복원에 대한 성능 고려 사항
완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개 CPU 스레드까지 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 장치를 저장하는 데 사용하는 VHD 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(Linux 소프트웨어 RAID, VHD) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 VHD에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 덤프를 작성하는 SAP ASE 수준에서 덤프 구성을 만듭니다.

탑재된 여러 VHD에 볼륨을 스트라이프하는 방법은 이 가이드의 앞부분에서 설명했습니다. 여러 디렉터리를 사용하여 SAP ASE 덤프를 구성하는 방법에 대한 자세한 내용은 [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp)에서 덤프 구성을 만드는 데 사용되는 저장 프로시저 sp\_config\_dump에 대한 설명서를 참조하세요.

### Azure VM을 사용한 재해 복구

#### SA Sybase Replication Server를 사용한 데이터 복제
SAP SRS(Sybase Replication Server)를 사용하면 SAP ASE에서 멀리 떨어진 위치에 데이터베이스 트랜잭션을 비동기적으로 전송하는 웜 대기 솔루션을 제공합니다.

SRS 설치 및 작동은 Azure 가상 컴퓨터 서비스에서 호스트되는 VM에서도 온-프레미스에서처럼 기능적으로 작동합니다.

SAP Replication Server를 통한 ASE HADR은 현재 지원되지 않습니다. 이 기능은 곧 Microsoft Azure Platform에 대해 테스트되고 릴리스될 예정입니다.

## Windows의 Oracle 데이터베이스에 대한 고유 정보
2013년 중반부터 Microsoft Windows Hyper-V 및 Azure에서 Oracle 소프트웨어가 지원됩니다. Oracle의 Windows Hyper-V 및 Azure 지원에 대한 자세한 내용은 <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces>를 참조하세요.

일반 지원에 따라 Oracle 데이터베이스를 활용하는 SAP 응용 프로그램의 특정 시나리오도 지원됩니다. 자세한 내용은 문서의 이 부분에 나와 있습니다.

### Oracle 지원 버전
Azure 가상 컴퓨터에서 Oracle의 SAP 실행을 위해 지원되는 Oracle 버전 및 해당 OS 버전에 대한 자세한 내용은 SAP Note [2039619]에서 찾을 수 있습니다.

Oracle에서의 SAP Business Suite 실행에 대한 일반 정보는 SCN <https://scn.sap.com/community/oracle>에서 찾을 수 있습니다.

### Azure VM의 SAP 설치에 대한 Oracle 구성 지침

#### 저장소 구성
NTFS로 포맷된 디스크를 사용하는 하나의 Oracle 인스턴스만 지원됩니다. 모든 데이터베이스 파일은 VHD 디스크 기반의 NTFS 파일 시스템에 저장되어야 합니다. 이러한 VHD는 Azure VM에 탑재되며 Azure 페이지 Blob 저장소(<https://msdn.microsoft.com/library/azure/ee691964.aspx>)를 기반으로 합니다. 모든 종류의 네트워크 드라이브 또는 Azure 파일 서비스와 같은 원격 공유:
 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
Oracle 데이터베이스 파일에 대해 지원되지 **않습니다**.

Azure 페이지 Blob 저장소 기반의 Azure VHD를 사용하는 경우 이 문서의 [VM 및 VHD용 캐싱][dbms-guide-2.1] 및 [Microsoft Azure Storage][dbms-guide-2.3] 챕터의 설명이 Oracle Database를 사용한 배포에도 적용됩니다.

이 문서 앞부분의 일반 정보에서 설명했듯이 Azure VHD에 대한 IOPS 처리량에 할당량이 존재합니다. 정확한 할당량은 사용되는 VM 유형에 따라 달라집니다. VM 형식과 해당 할당량 목록은 [여기][virtual-machines-sizes]에서 찾을 수 있습니다.

지원되는 Azure VM 형식을 식별하려면 SAP Note [1928533]을 참조하세요.

디스크당 현재 IOPS 할당량이 요구 사항을 충족하는 경우 탑재된 단일 Azure VHD에 모든 DB 파일을 저장할 수 있습니다.

더 많은 IOPS가 필요한 경우 Window 저장소 풀(Windows Server 2012 이상에서만 사용 가능) 또는 Windows 2008 R2용 Windows 스트라이프를 사용하여 탑재된 여러 VHD 디스크에 하나의 큰 논리적 장치를 만드는 것이 좋습니다. 또한 이 문서의 [소프트웨어 RAID][dbms-guide-2.2] 챕터를 참조하세요. 이 방법을 사용하면 디스크 공간 관리를 위한 관리 오버헤드를 간소화하고 탑재된 여러 VHD에 파일을 수동으로 배포하는 수고를 덜 수 있습니다.

#### 백업/복원
백업/복원 기능의 경우 SAP BR*Tools for Oracle은 표준 Windows Server 운영 체제 및 Hyper-V와 동일한 방법으로 지원됩니다. Oracle RMAN(Recovery Manager)에서도 디스크에 백업 및 디스크에서의 복원이 지원됩니다.

#### 고가용성
[설명]: <> (ASM 참조 링크) 
높은 가용성 및 재해 복구를 위해 Oracle Data Guard가 지원됩니다. 자세한 내용은 [이][virtual-machines-windows-classic-configure-oracle-data-guard] 설명서에서 찾을 수 있습니다.

#### 기타
Azure 가용성 집합 또는 SAP 모니터링과 같은 기타 일반적인 항목은 모두 이 문서의 처음 세 챕터에서 Oracle Database와 VM 배포에 대해 설명한 대로 적용됩니다.

## Windows의 SAP MaxDB 데이터베이스에 대한 고유 정보

### SAP MaxDB 버전 지원
SAP는 현재 SAP MaxDB 버전 7.9를 Azure의 SAP NetWeaver 기반 제품에 사용하도록 지원합니다. SAP NetWeaver 기반 제품과 함께 사용할 SAP MaxDB 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에서 SAP Service Marketplace를 통해서만 제공됩니다. SAP MaxDB에서의 SAP NetWeaver 실행에 대한 일반 정보는 <https://scn.sap.com/community/maxdb>에서 찾을 수 있습니다.

### SAP MaxDB DBMS에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure에서 지원되는 SAP MaxDB DBMS용 Microsoft Windows 버전을 찾으려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

최신 버전의 Microsoft Windows 운영 체제인 Microsoft Windows 2012 R2를 사용하는 것이 좋습니다.

### 사용 가능한 SAP MaxDB 설명서
SAP Note [767598]에서 업데이트된 SAP MaxDB 설명서 목록을 찾을 수 있습니다.
	
### Azure VM의 SAP 설치에 대한 SAP MaxDB 구성 지침

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>저장소 구성
SAP MaxDB에 대한 Azure Storage 모범 사례는 [RDBMS 배포의 구조][dbms-guide-2] 챕터에 설명된 일반 권장 사항을 따릅니다.

> [AZURE.IMPORTANT] 다른 데이터베이스처럼 SAP MaxDB에도 데이터 및 로그 파일이 있습니다. 그러나 SAP MaxDB 용어에서 올바른 용어는 "볼륨"("파일" 아님)입니다. 예를 들어 SAP MaxDB 데이터 볼륨 및 로그 볼륨이 있습니다. 운영 체제 디스크 볼륨과 혼동하지 마세요.

한마디로 다음을 수행해야 합니다.

* [Microsoft Azure Storage][dbms-guide-2.3] 챕터에 지정된 대로 SAP MaxDB 데이터 및 로그 볼륨(즉, 파일)을 유지하는 Azure 저장소 계정을 **LRS(로컬 중복 저장소)**로 설정합니다.
* 로그 볼륨(즉, 파일)에 대한 IO 경로에서 SAP MaxDB 데이터 볼륨(즉, 파일)에 대한 IO 경로를 구분합니다. 즉, SAP MaxDB 데이터 볼륨(즉, 파일)을 하나의 논리 드라이브에 설치하고 SAP MaxDB 로그 볼륨(즉, 파일)을 다른 논리 드라이브에 설치해야 합니다.
* [VM용 캐싱][dbms-guide-2.1] 챕터에 설명된 대로 SAP MaxDB 데이터 또는 로그 볼륨(즉, 파일)에 사용할지와 Azure 표준 또는 Azure 프리미엄 저장소에 사용할지 여부에 따라 각 Azure Blob에 적절한 파일 캐싱을 설정합니다.
* 디스크당 현재 IOPS 할당량이 요구 사항을 충족하는 경우 탑재된 단일 Azure VHD의 모든 데이터 볼륨을 저장하고 다른 탑재된 단일 Azure VHD에 모든 데이터베이스 로그 볼륨을 저장할 수 있습니다.
* 더 많은 IOPS 및/또는 공간이 필요한 경우 Microsoft Window 저장소 풀(Microsoft Windows Server 2012 이상에서만 사용 가능) 또는 Windows 2008 R2용 Microsoft Windows 스트라이프를 사용하여 탑재된 여러 VHD 디스크에 하나의 큰 논리적 장치를 만드는 것이 좋습니다. 또한 이 문서의 [소프트웨어 RAID][dbms-guide-2.2] 챕터를 참조하세요. 이 방법을 사용하면 디스크 공간 관리를 위한 관리 오버헤드를 간소화하고 탑재된 여러 VHD에 파일을 수동으로 배포하는 수고를 덜 수 있습니다.
* IOPS 요구 사항이 높은 경우 DS 시리즈와 GS 시리즈 VM에서 사용 가능한 Azure 프리미엄 저장소를 사용할 수 있습니다.

![SAP MaxDB DBMS에 대한 Azure IaaS VM의 참조 구성][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>백업 및 복원
Azure에 SAP MaxDB를 배포하는 경우 백업 방법을 검토해야 합니다. 생산성이 높은 시스템이 아니더라도 SAP MaxDB에서 호스트하는 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure 저장소에는 세 개의 이미지가 유지되므로 저장소 오류 및 더 중요한 작동 또는 관리 오류에 대한 시스템 보호 면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리 또는 수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 Azure의 백업을 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 예를 들어 백업을 복구하여 2계층 SAP 구성을 동일한 시스템의 3계층 시스템 설정으로 전송할 수 있습니다.

Azure에서의 데이터베이스 백업 및 복원 방법은 온-프레미스 시스템에서와 동일합니다. 그러므로 SAP Note [767598]에 나열된 SAP MaxDB 설명서 중 하나에서 설명한 표준 SAP MaxDB 백업/복원 도구를 사용할 수 있습니다.

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>백업 및 복원에 대한 성능 고려 사항
완전 배포에서처럼 백업 및 복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개 CPU 스레드까지 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 장치를 저장하는 데 사용하는 VHD 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, VHD) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 사용할 수 있으며 필요에 따라 혼합할 수 있습니다.

* 백업에 대해 별도의 볼륨을 지정합니다.
* 해당 스트라이프 디스크 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 VHD에 백업 대상 볼륨을 스트라이프합니다.
* 다음을 위한 별도의 전용 논리적 디스크 장치가 필요합니다.
    * SAP MaxDB 백업 볼륨(즉, 파일)
    * SAP MaxDB 데이터 볼륨(즉, 파일)
    * SAP MaxDB 로그 볼륨(즉, 파일)

탑재된 여러 VHD에 대한 볼륨 스트라이프는 이 문서 앞부분의 [소프트웨어 RAID][dbms-guide-2.2] 챕터에서 설명했습니다.

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>기타
Azure 가용성 집합 또는 SAP 모니터링과 같은 기타 일반적인 항목은 모두 이 문서의 처음 세 챕터에서 SAP MaxDB 데이터베이스와 VM 배포에 대해 설명한 대로 적용됩니다. 다른 SAP MaxDB 관련 설정은 Azure VM에 투명하며 SAP Note [767598]에 나열된 다른 문서 및 다음 SAP Note에서 설명됩니다.

* [826037]
* [1139904]
* [1173395]

## Windows의 SAP liveCache에 대한 고유 정보

### SAP liveCache 버전 지원
Azure 가상 컴퓨터에서 지원되는 SAP liveCache의 최소 버전은 **EhP 2 for SAP SCM 7.0** 이상에 대해 릴리스된 **liveCache 7.9.08.31** 및 **LCA 빌드 25**를 포함하는 **SAP LC/LCAPPS 10.0 SP 25**입니다.

### SAP liveCache DBMS에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure에서 지원되는 SAP liveCache용 Microsoft Windows 버전을 찾으려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

최신 버전의 Microsoft Windows 운영 체제인 Microsoft Windows 2012 R2를 사용하는 것이 좋습니다.

### Azure VM의 SAP 설치에 대한 SAP liveCache 구성 지침

#### 권장되는 Azure VM 유형
SAP liveCache는 많은 계산을 수행하는 응용 프로그램이므로 RAM과 CPU의 양 및 속도가 SAP liveCache 성능에 큰 영향을 줍니다.

SAP에서 지원하는 Azure VM 형식(SAP Note [1928533])의 경우, VM에 할당된 모든 가상 CPU 리소스가 하이퍼바이저의 전용 물리적 CPU 리소스에 의해 지원됩니다. 오버프로비저닝이 발생되지 않습니다(따라서 CPU 리소스에 대한 경쟁 없음).

마찬가지로, SAP에서 지원하는 모든 Azure VM 인스턴스 형식의 경우 VM 메모리가 모두 실제 메모리에 매핑되므로 예를 들어 오버프로비저닝(오버 커밋)이 사용되지 않습니다.

이러한 관점에서 볼 때, 새로운 D 시리즈 또는 DS 시리즈(Azure 프리미엄 저장소와 함께 사용) Azure VM 유형은 A 시리즈보다 60% 더 빠른 프로세서를 제공하므로 이 형식을 사용하는 것이 좋습니다. RAM 및 CPU 부하가 매우 높은 경우 G 시리즈와 GS 시리즈(Azure 프리미엄 저장소와 함께 사용) VM을 최신 Intel® Xeon® 프로세서 E5 v3 제품군과 함께 사용할 수 있으며 그 결과 D/DS 시리즈에 비해 2배 많은 메모리 및 4배 많은 SSD(반도체 드라이브)를 사용할 수 있습니다.

#### 저장소 구성
SAP liveCache는 SAP MaxDB 기술을 기반으로 하므로 [저장소 구성][dbms-guide-8.4.1] 챕터에 언급된 모든 SAP MaxDB에 대한 Azure Storage 모범 사례 권장 사항이 SAP liveCache에도 적용됩니다.

#### liveCache용 전용 Azure VM
SAP liveCache는 컴퓨팅 기능을 집중적으로 사용하므로 생산적인 사용을 위해 전용 Azure 가상 컴퓨터에 배포하는 것이 좋습니다.
 
![생산적인 사용 사례를 위한 liveCache용 전용 Azure VM][dbms-guide-figure-700]

#### 백업 및 복원
성능 고려 사항을 포함한 백업 및 복원은 관련 SAP MaxDB 챕터 [백업 및 복원][dbms-guide-8.4.2] 및 [백업 및 복원에 대한 성능 고려 사항][dbms-guide-8.4.3]에 설명되어 있습니다.

#### 기타
다른 모든 일반 항목은 관련 SAP MaxDB의 [이][dbms-guide-8.4.4] 챕터에 설명되어 있습니다.

## Windows의 SAP Content Server에 대한 고유 정보
SAP Content Server는 다양한 형식의 전자 문서와 같은 콘텐츠를 저장하기 위한 별도의 서버 기반 구성 요소입니다. SAP Content Server는 기술 개발을 통해 제공되며 SAP 응용 프로그램의 응용 프로그램 간에 사용됩니다. 별도의 시스템에 설치됩니다. 일반적인 콘텐츠는 기술 웨어하우스 설명서 또는 mySAP PLM 문서 관리 시스템의 기술 드로잉 및 교육 자료입니다.

### SAP Content Server 버전 지원
SAP에서 현재 다음을 지원합니다.

* **SAP Content Server** 버전 **6.50 이상**
* **SAP MaxDB 버전 7.9**
* **Microsoft IIS(인터넷 정보 서비스) 버전 8.0 이상**

SAP Content Server의 최신 버전을 사용하는 것이 좋습니다. 이 문서를 작성할 당시를 기준으로 하면 **6.50 SP4**와 **Microsoft IIS 8.5**가 최신 버전입니다.

SAP Content Server 및 Microsoft IIS에서 지원되는 최신 버전은 [SAP PAM(제품 가용성 매트릭스)][sap-pam]을 참조하세요.

### SAP Content Server에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure의 SAP Content Server에서 지원되는 Windows 버전을 확인하려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

Microsoft Windows의 최신 버전을 사용하는 것이 좋습니다. 이 문서를 작성할 당시를 기준으로 하면 **Windows Server 2012 R2**입니다.

### Azure VM의 SAP 설치에 대한 SAP Content Server 구성 지침

#### 저장소 구성 
SAP MaxDB 데이터베이스에 파일을 저장하도록 SAP Content Server를 구성한 경우 [저장소 구성][dbms-guide-8.4.1] 챕터에서 SAP MaxDB에 대해 언급된 모든 Azure Storage 모범 사례 권장 사항이 SAP Content Server 시나리오에도 적용됩니다.

파일 시스템에 파일을 저장하도록 SAP Content Server를 구성한 경우 전용 논리 드라이브를 사용하는 것이 좋습니다. 저장소 공간을 사용하면 [소프트웨어 RAID][dbms-guide-2.2] 챕터에 설명된 대로 논리 디스크 크기 및 IOPS 처리량이 늘어납니다.

#### SAP Content Server 위치
SAP Content Server는 SAP 시스템이 배포된 것과 동일한 Azure VNET 및 Azure 지역에 배포되어야 합니다. SAP Content Server 구성 요소는 전용 Azure VM 또는 SAP 시스템이 실행 중인 동일한 VM에 배포할 수 있습니다.
 
![SAP Content Server용 전용 Azure VM][dbms-guide-figure-800]

#### SAP Cache Server 위치
SAP Cache Server는 로컬에 있는 (캐시된) 문서에 대한 액세스를 제공하는 추가 서버 기반 구성 요소입니다. SAP Cache Server는 SAP Content Server 문서를 캐시합니다. 이 기능의 목적은 서로 다른 위치에서 문서를 두 번 이상 검색해야 하는 경우 네트워크 트래픽을 최적화하는 것입니다. 일반적으로 SAP Cache Server는 SAP Cache Server에 액세스하는 클라이언트와 물리적으로 가까워야 합니다.

다음 두 가지 옵션이 있습니다.

1. **클라이언트가 백 엔드 SAP 시스템인 경우** SAP Content Server에 액세스하도록 백 엔드 SAP 시스템이 구성된 경우 해당 SAP 시스템은 클라이언트입니다. SAP 시스템과 SAP Content Server는 모두 같은 Azure 지역, 즉 동일한 Azure 데이터 센터에 배포되므로 물리적으로 서로 가깝습니다. 따라서 전용 SAP Cache Server를 사용할 필요가 없습니다. SAP UI 클라이언트(SAP GUI 또는 웹 브라우저)는 SAP 시스템에 직접 액세스하고 SAP 시스템은 SAP Content Server에서 문서를 검색합니다.
1. **클라이언트가 온-프레미스 웹 브라우저인 경우** 웹 브라우저에서 직접 액세스하도록 SAP Content Server를 구성할 수 있습니다. 이 경우 온-프레미스에서 실행하는 웹 브라우저는 SAP Content Server의 클라이언트입니다. 온-프레미스 데이터 센터와 Azure 데이터 센터는 서로 다른 물리적 위치에 있습니다(이상적으로는 서로 가까워야 함). 온-프레미스 데이터 센터는 Azure 사이트 간 VPN 또는 Express 경로를 통해 Azure에 연결됩니다. 두 옵션 모두 Azure에 대한 보안 VPN 네트워크 연결을 제공하지만 사이트 간 네트워크 연결은 온-프레미스 데이터 센터와 Azure 데이터 센터 간의 네트워크 대역폭 및 대기 시간 SLA를 제공하지 않습니다. 문서에 대한 액세스 속도를 향상시키기 위해 다음 중 하나를 수행할 수 있습니다.
    1. SAP Cache Server를 온-프레미스에서 온-프레미스 웹 브라우저 가까이 설치합니다([이][dbms-guide-900-sap-cache-server-on-premises] 그림의 옵션).
    1. 빠른 속도와 짧은 대기 시간의 온-프레미스 데이터 센터와 Azure 데이터 센터 간 전용 네트워크 연결을 제공하는 Azure Express 경로를 구성합니다.
 
![SAP Cache Server 온-프레미스 설치 옵션][dbms-guide-figure-900] <a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### 백업/복원
SAP MaxDB 데이터베이스에 파일을 저장하도록 SAP Content Server를 구성하는 경우 SAP MaxDB [백업 및 복원][dbms-guide-8.4.2] 챕터 및 [백업 및 복원에 대한 성능 고려 사항][dbms-guide-8.4.3] 챕터에 설명된 백업/복원 절차 및 성능 고려 사항을 참조하세요.

파일 시스템에 파일을 저장하도록 SAP Content Server를 구성하는 경우 문서가 위치한 전체 파일 구조의 수동 백업/복원을 실행할 수 있습니다. SAP MaxDB 백업/복원과 마찬가지로, 백업 목적을 위한 전용 디스크 볼륨을 사용하는 것이 좋습니다.

#### 기타
다른 SAP Content Server 관련 설정은 Azure VM에 투명하며 다양한 문서 및 SAP Note에 설명되어 있습니다.

* <https://service.sap.com/contentserver>
* SAP Note [1619726]

## Windows의 LUW용 IBM DB2에 대한 고유 정보
Microsoft Azure를 사용하면 Linux, UNIX, Windows(LUW)용 IBM DB2에서 실행 중인 기존 SAP 응용 프로그램을 Azure 가상 컴퓨터로 쉽게 마이그레이션할 수 있습니다. LUW용 IBM DB2에서 SAP를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다. LUW용 IBM DB2에서의 SAP Business Suite 실행에 대한 일반 정보는 <https://scn.sap.com/community/db2-for-linux-unix-windows>의 SCN(SAP Community Network)을 참조하세요.

Azure에서 LUW용 DB2의 SAP에 대한 추가 정보 및 업데이트는 SAP Note [2233094]를 참조하세요.

### Linux, UNIX 및 Windows용 IBM DB2 버전 지원
Microsoft Azure 가상 컴퓨터 서비스에서 LUW용 IBM DB2의 SAP는 DB2 버전 10.5부터 지원됩니다.

지원되는 SAP 제품 및 Azure VM 형식에 대한 내용은 SAP Note [1928533]을 참조하세요.

### Azure VM의 SAP 설치에 대한 Linux, UNIX 및 Windows용 IBM DB2 구성 지침

#### 저장소 구성
모든 데이터베이스 파일은 VHD 디스크 기반의 NTFS 파일 시스템에 저장되어야 합니다. 이러한 VHD는 Azure VM에 탑재되며 Azure 페이지 Blob 저장소(<https://msdn.microsoft.com/library/azure/ee691964.aspx>)를 기반으로 합니다. 모든 종류의 네트워크 드라이브 또는 다음 Azure 파일 서비스 같은 원격 공유는 데이터베이스 파일에 대해 지원되지 **않습니다**.

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
Azure 페이지 Blob 저장소 기반의 Azure VHD를 사용하는 경우 이 문서의 [RDBMS 배포 구조][dbms-guide-2] 챕터의 설명이 LUW용 IBM DB2 데이터베이스를 사용한 배포에도 적용됩니다.

이 문서 앞부분의 일반 정보에서 설명했듯이 Azure VHD에 대한 IOPS 처리량에 할당량이 존재합니다. 정확한 할당량은 사용되는 VM 유형에 따라 달라집니다. VM 형식과 해당 할당량 목록은 [여기][virtual-machines-sizes]에서 찾을 수 있습니다.

디스크당 현재 IOPS 할당량이 충분한 경우 탑재된 단일 Azure VHD에 모든 데이터베이스 파일을 저장할 수 있습니다.

SAP 설치 가이드의 "데이터베이스 디렉터리의 데이터 보안 및 성능 고려 사항" 챕터에서도 성능 고려 사항을 참조할 수 있습니다.

또는 이 문서의 [소프트웨어 RAID][dbms-guide-2.2] 챕터에 설명된 대로 Window 저장소 풀(Windows Server 2012 이상에서만 사용 가능) 또는 Windows 2008 R2용 Windows 스트라이프를 사용하여 탑재된 여러 VHD 디스크에 하나의 큰 논리적 장치를 만들 수 있습니다. sapdata 및 saptmp 디렉터리에 대한 DB2 저장소 경로를 포함하는 디스크의 경우 물리적 디스크 섹터 크기를 512KB로 지정해야 합니다. Windows 저장소 풀을 사용하는 경우 “-LogicalSectorSizeDefault” 매개 변수를 사용하여 명령줄 인터페이스를 통해 수동으로 저장소 풀을 만들어야 합니다. 자세한 내용은 <https://technet.microsoft.com/library/hh848689.aspx>를 참조하세요.

#### 백업/복원
LUW용 IBM DB2의 백업/복원 기능은 표준 Windows Server 운영 체제 및 Hyper-V와 동일한 방법으로 지원됩니다.

유효한 데이터베이스 백업 전략이 있는지 확인해야 합니다.

완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 또한 백업 압축에서 사용하는 CPU 사용량은 최대 8개 CPU 스레드까지 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 장치를 저장하는 데 사용하는 VHD 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, VHD) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 VHD에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 백업을 작성합니다.

#### 고가용성 및 재해 복구
MSCS(Microsoft Cluster Server)는 지원되지 않습니다.

DB2 HADR(고가용성 재해 복구)은 지원됩니다. HA 구성의 가상 컴퓨터에 이름 확인 작업이 있는 경우 Azure 설정이 온-프레미스의 설정과 다르지 않습니다. IP 확인만 사용하는 것은 권장되지 않습니다.

Azure 저장소 지역에서 복제는 사용하지 마세요. 자세한 내용은 [Microsoft Azure Storage][dbms-guide-2.3] 및 [Azure VM에서 고가용성 및 재해 복구][dbms-guide-3] 챕터를 참조하세요.

#### 기타
Azure 가용성 집합 또는 SAP 모니터링과 같은 기타 일반적인 항목은 모두 이 문서의 처음 세 챕터에서 LUW용 IBM DB2와 VM 배포에 대해 설명한 대로 적용됩니다.

또한 [Azure의 SAP용 일반 SQL Server 요약][dbms-guide-5.8] 챕터를 참조하세요.

<!---HONumber=AcomDC_0907_2016-->
