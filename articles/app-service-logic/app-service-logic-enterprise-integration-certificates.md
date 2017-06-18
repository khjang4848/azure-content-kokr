<properties 
	pageTitle="엔터프라이즈 통합 팩에서 인증서 사용 | Microsoft Azure 앱 서비스" 
	description="엔터프라이즈 통합 팩 및 논리 앱에서 인증서를 사용하는 방법 알아보기" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/06/2016" 
	ms.author="deonhe"/>

# 인증서 및 엔터프라이즈 통합 팩에 대해 알아보기

## 개요
엔터프라이즈 통합에서는 인증서를 사용하여 B2B 통신을 보호합니다. 엔터프라이즈 통합 앱에서 두 가지 종류의 인증서를 사용할 수 있습니다.
- 공용 인증서는 CA(인증 기관)에서 구입해야 합니다
- 개인 인증서는 직접 발행할 수 있습니다. 이러한 인증서를 자체 서명된 인증서라고도 합니다.


## 인증서란?
인증서는 전자 통신에서 참가자의 ID를 확인하고 전자 통신을 보호하는 데 사용하는 디지털 문서입니다.

## 인증서를 사용하는 이유
경우에 따라 B2B 통신을 기밀로 유지해야 합니다. 엔터프라이즈 통합에서는 인증서를 사용하여 다음 두 가지 방법으로 이러한 통신을 보호합니다.
- 메시지의 콘텐츠를 암호화하여
- 메시지에 디지털로 서명하여

## 인증서 업로드 방법

### 공용 인증서
B2B 기능이 포함된 논리 앱에서 **공용 인증서**를 사용하려면 먼저 통합 계정에 업로드해야 합니다. 다른 한편 **자체 서명된 인증서**를 사용하려면 먼저 [주요 자격 증명 모음](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")을 업로드해야 합니다.

인증서를 업로드한 후에 [규약](./app-service-logic-enterprise-integration-agreements.md)에서 해당 속성을 정의하는 경우 B2B 메시지를 보호하는 데 사용할 수 있습니다.

Azure Portal에 로그인한 후에 통합 계정에 공용 인증서를 업로드하는 자세한 단계는 다음과 같습니다.
1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 인증서를 추가할 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **인증서** 타일을 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. 열린 인증서 블레이드에서 **추가** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 인증서의 **이름**을 입력하고 인증서 종류를 선택(이 예제에서는 공용 인증서 형식을 사용함)한 다음 **인증서** 텍스트 상자의 오른쪽에 있는 폴더 아이콘을 선택합니다. 파일을 찾아 볼 수 있는 파일 선택이 열리면 통합 계정에 업로드하려는 인증서 파일을 선택합니다. 인증서를 선택한 후에 파일 선택기에서 **확인**을 선택합니다. 이 작업은 인증서의 유효성을 검사하고 통합 계정에 업로드합니다. 마지막으로, 다시 **인증서 블레이드 추가**로 돌아와서 **확인** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)
7. 1분 이내에 인증서 업로드가 완료되었음을 나타내는 알림이 표시됩니다.
8. **인증서 타일**을 선택합니다. 페이지를 새로 고친 후에 새로 추가된 인증서가 표시되어야 합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### 개인 인증서

또한 다음 단계를 수행하여 통합 계정에 개인 인증서를 업로드할 수 있습니다.

1. [개인 키를 주요 자격 증명 모음에 업로드](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")

	> [AZURE.TIP] Logic Apps 서비스에 Key Valut에서 작업을 수행할 수 있는 권한을 부여해야 합니다. 이 PowerShell 명령을 사용하여 Logic Apps 서비스 주체에 대한 액세스 권한을 부여할 수 있습니다. `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

2. 개인 인증서 만들기
3. 통합 계정으로 개인 인증서 업로드

위의 단계를 완료한 후에 개인 인증서를 사용하여 규약을 만들 수 있습니다.

Azure Portal에 로그인한 후에 통합 계정에 개인 인증서를 업로드하는 자세한 단계는 다음과 같습니다.
   
1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 인증서를 추가할 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **인증서** 타일을 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)
5. 열린 인증서 블레이드에서 **추가** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)
6. 인증서의 **이름**을 입력하고 인증서 종류를 선택(이 예제에서는 공용 인증서 형식을 사용함)한 다음 **인증서** 텍스트 상자의 오른쪽에 있는 폴더 아이콘을 선택합니다. 파일을 찾아 볼 수 있는 파일 선택이 열리면 통합 계정에 업로드하려는 인증서 파일을 선택합니다. 인증서를 선택한 후에 파일 선택기에서 **확인**을 선택합니다. 이 작업은 인증서의 유효성을 검사하고 통합 계정에 업로드합니다. 마지막으로, 다시 **인증서 블레이드 추가**로 돌아와서 **확인** 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)
7. 1분 이내에 인증서 업로드가 완료되었음을 나타내는 알림이 표시됩니다.
8. **인증서 타일**을 선택합니다. 새로 추가된 인증서가 표시되어야 합니다. ![](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

인증서를 업로드한 후에 [규약](./app-service-logic-enterprise-integration-agreements.md)에서 해당 속성을 정의하는 경우 B2B 메시지를 보호하는 데 사용할 수 있습니다.


## 다음 단계
- [B2B 기능을 사용하는 논리 앱 만들기](./app-service-logic-enterprise-integration-b2b.md)
- [B2B 규약 만들기](./app-service-logic-enterprise-integration-agreements.md)
- [주요 자격 증명 모음에 대해 자세히 알아보기](../key-vault/key-vault-get-started.md "주요 자격 증명 모음에 대해 알아보기")

<!---HONumber=AcomDC_0914_2016-->