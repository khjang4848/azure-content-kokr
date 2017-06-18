<properties
	pageTitle="클라우드 서비스 FAQ | Microsoft Azure"
	description="클라우드 서비스에 대한 질문과 대답입니다."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2016"
	ms.author="adegeo"/>

# 클라우드 서비스 FAQ
이 문서는 Microsoft Azure 클라우드 서비스에 대한 일부 자주 묻는 질문을 답변합니다. 또한 일반적인 Azure 가격 책정 및 지원 정보는 [Azure 지원 FAQ](http://go.microsoft.com/fwlink/?LinkID=185083)를 방문할 수 있습니다. 크기 정보는 [클라우드 서비스 VM 크기 페이지](cloud-services-sizes-specs.md)를 참조할 수도 있습니다.

## 인증서

### 어디에 내 인증서를 설치해야 하나요?

- 개인 키를 포함한 **내** 응용 프로그램 인증서(*.pfx, *.p12)

- **CA** 모든 중간 인증서를 이 저장소(정책 및 하위 CA)로 이동시킵니다.

- **루트** 루트 CA 저장소이므로 기본 루트 CA 인증서를 여기로 이동시켜야 합니다.

### 만료된 인증서를 제거할 수 없습니다.

Azure에서는 사용 중인 인증서를 제거할 수 없습니다. 인증서를 사용하는 배포를 삭제하거나 다른 또는 갱신된 인증서로 배포를 업데이트해야 합니다.

### 만료된 인증서 삭제

인증서를 사용하지 않으면 [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell cmdlet을 사용하여 인증서를 제거할 수 있습니다.

### 확장명이 Microsoft Azure 서비스 관리라는 인증서가 만료되었습니다.

이러한 인증서는 원격 데스크톱 확장과 같은 클라우드 서비스에 확장명을 추가할 때마다 생성됩니다. 이러한 인증서는 확장명의 개인 구성을 암호화하고 암호 해독하는 경우에만 사용됩니다. 이러한 인증서가 만료되는 경우 중요하지 않습니다. 만료 날짜를 확인하지 않습니다.

### 삭제한 인증서가 다시 나타납니다.

대개 Visual Studio와 같은 사용 중인 도구 때문에 계속 다시 나타납니다. 인증서를 사용하는 도구에 다시 연결할 때마다 다시 Azure에 업로드됩니다.

### 내 인증서가 자꾸 없어집니다.

가상 컴퓨터 인스턴스가 재활용되면 모든 로컬 변경 내용이 손실됩니다. [시작 태스크](cloud-services-startup-tasks.md)를 사용하여 역할이 시작될 때마다 가상 컴퓨터에 인증서를 설치합니다.

### 포털에서 내 관리 인증서를 찾을 수 없습니다.

[관리 인증서](..\azure-api-management-certs.md) Azure 클래식 포털에만 사용할 수 있습니다. 현재의 Azure 포털에서는 관리 인증서를 사용하지 않습니다.

### 어떻게 하면 관리 인증서를 사용하지 않도록 설정할 수 있습니까?

[관리 인증서](..\azure-api-management-certs.md)는 사용하지 않도록 설정할 수 없습니다. 더 이상 사용되게 하지 않으려면 Azure 클래식 포털을 통해 삭제합니다.

### 특정 IP 주소에 대한 SSL 인증서를 만들려면 어떻게 해야 합니까?

[인증서 만들기 자습서](cloud-services-certs-create.md)에 나오는 지시를 따릅니다. IP 주소를 DNS 이름으로 사용합니다.

## 문제 해결

### 여러 VIP 클라우드 서비스에서 IP를 예약할 수 없습니다.

우선, IP를 예약하려고 하는 가상 컴퓨터 인스턴스가 켜져 있는지 확인합니다. 다음으로, 스테이징 및 프로덕션 배포 모두에 대해 예약된 IP를 사용해야 합니다. 배포를 업그레이드하는 동안 설정을 변경하지 **않습니다**.

<!---HONumber=AcomDC_0914_2016-->