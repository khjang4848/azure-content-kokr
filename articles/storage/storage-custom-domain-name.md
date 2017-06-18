<properties
	pageTitle="Blob 저장소 끝점에 대한 도메인 이름 구성 | Microsoft Azure"
	description="Azure 클래식 포털에서 Azure 저장소 계정에 대해 사용자 지정 사용자 도메인을 Blob 저장소 끝점에 매핑하는 방법을 알아봅니다."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="tamram"/>


# Blob 저장소 끝점에 대한 사용자 지정 도메인 이름 구성

## 개요

Azure 저장소 계정에서 Blob 데이터에 액세스할 수 있도록 사용자 지정 도메인을 구성할 수 있습니다. Blob 저장소의 기본 끝점은 `<storage-account-name>.blob.core.windows.net`입니다. **www.contoso.com**과 같은 사용자 지정 도메인 및 하위 도메인을 저장소 계정의 Blob 끝점에 매핑하면 사용자도 해당 도메인을 사용하여 저장소 계정의 Blob 데이터에 액세스할 수 있습니다.

>[AZURE.IMPORTANT] Azure Storage는 아직 사용자 지정 도메인으로 HTTPS를 지원하지 않습니다. 이 기능에 관심을 가진 고객을 위해 향후 릴리스에서 사용할 수 있도록 할 예정입니다.

저장소 계정의 Blob 끝점으로 사용자 지정 도메인을 가리키는 방법은 두 가지입니다. 가장 간단한 방법은 사용자 지정 도메인 및 하위 도메인을 Blob 끝점에 매핑하는 CNAME 레코드를 만드는 것입니다. CNAME 레코드는 원본 도메인을 대상 도메인에 매핑하는 DNS 기능입니다. 이 경우에 원본 도메인은 사용자 지정 도메인 및 하위 도메인이며, 하위 도메인은 항상 필요합니다. 대상 도메인은 Blob 서비스 끝점입니다.

사용자 지정 도메인을 Blob 끝점에 매핑하는 프로세스로 인해 [Azure 클래식 포털](https://manage.windowsazure.com)에서 도메인을 등록하는 동안 도메인이 잠시 가동 중지될 수 있습니다. 사용자 지정 도메인에서 가동 중지 시간이 없어야 하는 SLA(서비스 수준 계약)가 설정된 응용 프로그램을 현재 지원하고 있다면 DNS 매핑이 진행되는 동안 사용자가 도메인에 액세스할 수 있도록 중간 등록 단계를 제공하는 데 Azure **asverify** 하위 도메인을 사용할 수 있습니다.

다음 표는 이름이 **mystorageaccount**인 저장소 계정의 Blob 데이터에 액세스하기 위한 샘플 URL을 보여 줍니다. 저장소 계정에 등록된 사용자 지정 도메인은 **www.contoso.com**입니다.

리소스 종류|URL 형식
---|---
저장소 계정|**기본 URL:** http://mystorageaccount.blob.core.windows.net<p>**사용자 지정 도메인 URL:** http://www.contoso.com</td>
Blob|**기본 URL:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**사용자 지정 도메인 URL:** http://www.contoso.com/mycontainer/myblob
루트 컨테이너|**기본 URL:** http://mystorageaccount.blob.core.windows.net/myblob 또는 http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**사용자 지정 도메인 URL:** http://www.contoso.com/myblob 또는 http://www.contoso.com/$root/myblob

## 저장소 계정의 사용자 지정 도메인 등록

사용자가 도메인을 잠시 사용할 수 없더라도 괜찮은 경우나 사용자 지정 도메인에서 현재 응용 프로그램을 호스트하지 않는 경우에 이 절차에 따라 사용자 지정 도메인을 등록하세요.

사용자 지정 도메인이 가동 중지 시간이 없어야 하는 응용 프로그램을 현재 지원하고 있다면 <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">중간 asverify 하위 도메인을 사용하여 저장소 계정의 사용자 지정 도메인 등록</a>에서 설명한 절차를 따르세요.

사용자 지정 도메인 이름을 구성하려면 도메인 등록 기관에서 새 CNAME 레코드를 만들어야 합니다. CNAME 레코드에서 도메인 이름의 별칭을 지정합니다. 이 경우에는 사용자 지정 도메인의 주소를 저장소 계정의 Blob 저장소 끝점에 매핑합니다.

각 등록 기관은 서로 유사하면서 약간 다른 방법으로 CNAME 레코드를 지정하지만 개념은 동일합니다. 대다수 기본 도메인 등록 패키지에서 DNS 구성을 제공하지 않으므로 CNAME 레코드를 만들려면 먼저 도메인 등록 패키지를 업그레이드해야 할 수 있습니다.

1.  [Azure 클래식 포털](https://manage.windowsazure.com)에서 **저장소** 탭으로 이동합니다.

2.  **저장소** 탭에서 사용자 지정 도메인을 매핑할 저장소 계정의 이름을 클릭합니다.

3.  **구성** 탭을 클릭합니다.

4.  화면 아래에 있는 **도메인 관리**를 클릭하여 **사용자 지정 도메인 관리** 대화 상자를 표시합니다. 대화 상자 위쪽에 있는 텍스트에서 CNAME 레코드를 만드는 방법에 대한 정보를 볼 수 있습니다. 이 절차에서는 **asverify** 하위 도메인을 참조하는 텍스트는 무시하십시오.

5.  DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.

6.  CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.

7.  새 CNAME 레코드를 만들어 **www** 또는 **photos**와 같은 하위 도메인 별칭을 지정합니다. 그런 다음 Blob 서비스 끝점인 호스트 이름을 **mystorageaccount.blob.core.windows.net** 형식으로 지정합니다. 여기에서 **mystorageaccount**는 저장소 계정의 이름입니다. 사용할 호스트 이름은 **사용자 지정 도메인 관리** 대화 상자의 텍스트 상자에 자동으로 입력됩니다.

8.  CNAME 레코드를 만든 후 **사용자 지정 도메인 관리** 대화 상자로 돌아와 **사용자 지정 도메인 이름** 필드에 하위 도메인을 포함하여 사용자 지정 도메인의 이름을 입력합니다. 예를 들어 도메인이 **contoso.com**이고 하위 도메인이 **www**인 경우 **www.contoso.com**을 입력합니다. 하위 도메인이 **photos**인 경우에는 **photos.contoso.com**을 입력합니다. 하위 도메인은 필수입니다.

9. **등록** 버튼을 클릭하여 사용자 지정 도메인을 등록합니다.

	등록이 올바르게 완료되면 **사용자 지정 도메인이 활성화되었습니다.**라는 메시지가 표시됩니다. 이제 적절한 사용 권한이 있는 사용자는 사용자 지정 도메인의 Blob 데이터를 볼 수 있습니다.

## 중간 asverify 하위 도메인을 사용하여 저장소 계정의 사용자 지정 도메인 등록

사용자 지정 도메인이 가동 중지 시간이 없어야 하는 SLA가 있는 응용 프로그램을 현재 지원하고 있는 경우 이 절차에 따라 사용자 지정 도메인을 등록하세요. asverify.<subdomain>.<customdomain>에서 asverify.<storageaccount>.blob.core.windows.net을 가리키는 CNAME을 만들어 Azure에 도메인을 미리 등록할 수 있습니다. 그런 다음 <subdomain>.<customdomain>에서 <storageaccount>.blob.core.windows.net을 가리키는 두 번째 CNAME을 만들 수 있습니다. 그러면 사용자 지정 도메인에 대한 지점 트래픽이 Blob 끝점으로 전송됩니다.

asverify 하위 도메인은 Azure에서 인식하는 특수한 하위 도메인입니다. 고유한 하위 도메인 앞에 **asverify**를 추가하면 도메인의 DNS 레코드를 수정하지 않아도 Azure에서 사용자 지정 도메인을 인식할 수 있습니다. 도메인의 DNS 레코드를 수정하면 가동 중지 시간 없이 Blob 끝점에 매핑됩니다.

1.  [Azure 클래식 포털](https://manage.windowsazure.com)에서 **저장소** 탭으로 이동합니다.

2.  **저장소** 탭에서 사용자 지정 도메인을 매핑할 저장소 계정의 이름을 클릭합니다.

3.  **구성** 탭을 클릭합니다.

4.  화면 아래에 있는 **도메인 관리**를 클릭하여 **사용자 지정 도메인 관리** 대화 상자를 표시합니다. 대화 상자의 위쪽에 있는 텍스트에서 **asverify** 하위 도메인을 사용하여 CNAME 레코드를 만드는 방법에 대한 정보를 볼 수 있습니다.

5.  DNS 등록 기관의 웹 사이트에 로그온한 다음 DNS 관리 페이지로 이동합니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.

6.  CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 합니다.

7.  새 CNAME 레코드를 만들어 asverify 하위 도메인을 포함한 하위 도메인 별칭을 지정합니다. 예를 들어 지정하는 하위 도메인은 **asverify.www** 또는 **asverify.photos**와 같은 형식이어야 합니다. 그런 다음 Blob 서비스 끝점인 호스트 이름을 **asverify.mystorageaccount.blob.core.windows.net** 형식으로 지정합니다. 여기에서 **mystorageaccount**는 저장소 계정의 이름입니다. 사용할 호스트 이름은 **사용자 지정 도메인 관리** 대화 상자의 텍스트 상자에 자동으로 입력됩니다.

8.  CNAME 레코드를 만든 후에 **사용자 지정 도메인 관리** 대화 상자로 돌아와 사용자 지정 도메인의 이름을 **사용자 지정 도메인 이름** 필드에 입력합니다. 예를 들어 도메인이 **contoso.com**이고 하위 도메인이 **www**인 경우 **www.contoso.com**을 입력합니다. 하위 도메인이 **photos**인 경우에는 **photos.contoso.com**을 입력합니다. 하위 도메인은 필수입니다.

9.	**고급: 'asverify' 하위 도메인을 사용하여 내 사용자 지정 도메인을 미리 등록합니다.** 확인란을 클릭합니다.

10. **등록** 버튼을 클릭하여 사용자 지정 도메인을 미리 등록합니다.

	미리 등록이 올바르게 완료되면 **사용자 지정 도메인이 활성화되었습니다.**라는 메시지가 표시됩니다.

11. 이제 사용자 지정 도메인이 Azure에서 확인되었지만 도메인에 대한 트래픽은 아직 저장소 계정으로 라우팅되지 않습니다. 프로세스를 완료하려면 DNS 등록 기관의 웹 사이트로 돌아가 하위 도메인을 Blob 서비스 끝점에 매핑한 CNAME 레코드를 하나 더 만듭니다. 예를 들어 하위 도메인을 **www** 또는 **photos**로 지정하고 호스트 이름을 **mystorageaccount.blob.core.windows.net**으로 지정합니다. 여기에서 **mystorageaccount**는 저장소 계정의 이름입니다. 이 단계에서 사용자 지정 도메인 등록이 완료됩니다.

12. 마지막으로 **asverify**를 사용하여 만든 CNAME 레코드는 중간 단계로만 필요하므로 삭제할 수 있습니다.

이제 적절한 사용 권한이 있는 사용자는 사용자 지정 도메인의 Blob 데이터를 볼 수 있습니다.

## 사용자 지정 도메인에서 Blob 서비스 끝점을 참조하는지 확인

사용자 지정 도메인이 Blob 서비스 끝점에 실제로 매핑되었는지 확인하려면 저장소 계정 내의 공용 컨테이너에 Blob을 만듭니다. 그런 다음 웹 브라우저에서 다음 형식의 URI를 사용하여 Blob에 액세스합니다.

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

예를 들어 다음 URI를 사용하여 **myforms** 컨테이너의 Blob에 매핑하는 **photos.contoso.com** 사용자 지정 하위 도메인을 통해 웹 양식에 액세스할 수 있습니다.

-   http://photos.contoso.com/myforms/applicationform.htm

## 저장소 계정에서 사용자 지정 도메인 등록 취소

사용자 지정 도메인의 등록을 취소하려면 다음 단계를 따르세요.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 탐색 창에서 **저장소**를 클릭합니다.

3. **저장소** 페이지에서 저장소 계정의 이름을 클릭하여 대시보드를 표시합니다.

5. 리본 메뉴에서 **도메인 관리**를 클릭합니다.

6. **사용자 지정 도메인 관리** 대화 상자에서 **등록 취소**를 클릭합니다.


## 추가 리소스

-   [사용자 지정 도메인을 CDN(콘텐츠 배달 네트워크) 끝점에 매핑하는 방법](../cdn/cdn-map-content-to-custom-domain.md)

<!---HONumber=AcomDC_0914_2016-->