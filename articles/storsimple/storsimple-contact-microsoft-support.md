<properties 
   pageTitle="Microsoft 지원 문의 | Microsoft Azure"
   description="StorSimple 장치에서 지원 요청을 만들고 지원 세션을 시작하는 방법을 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# Microsoft 지원에 문의

Microsoft Azure StorSimple 솔루션에 문제가 발생하는 경우 기술 지원을 위해 서비스 요청을 할 수 있습니다. 지원 엔지니어와 함께 온라인 세션에서 StorSimple 장치에 지원 세션을 시작해야 할 수도 있습니다. 이 문서에서는 다음을 안내합니다.

- 지원 요청을 만드는 방법
- StorSimple 장치의 Windows PowerShell 인터페이스에서 지원 세션을 시작하는 방법

지원 요청을 만들기 전에 [StorSimple 8000 시리즈 지원 SLA 및 정보](https://msdn.microsoft.com/library/mt433077.aspx)를 검토하십시오.

## 지원 요청 만들기

지원 요청을 만들려면 다음 단계를 수행합니다.

#### 지원 요청을 만들려면

1. [Azure 클래식 포털](https://manage.windowsazure.com/)의 오른쪽 위에서 계정 이름을 클릭한 다음 **Microsoft 지원에 문의**를 클릭합니다.

	![ManagementPortal을 통한 MS 지원 문의](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. 새 Azure 포털(portal.azure.com)로 리디렉션됩니다. **새 지원 요청** 타일을 클릭합니다.

	![새 포털을 통한 MS 지원 문의](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    화면의 오른쪽에 **새 지원 요청** 창이 나타납니다.

	![새 지원 요청 창](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. **기본 사항** 대화 상자에서 다음을 완료합니다.
	1. **문제점 유형** 드롭다운 목록에서 **기술**을 선택합니다.
	2. 드롭다운 목록에서 **구독**을 선택합니다.
	3. **서비스** 드롭다운 목록에서 **StorSimple**을 선택합니다.
	4. 드롭다운 목록에서 **지원 계획**을 선택합니다. 기술 지원을 사용하도록 설정하기 위해 유료 지원 계획이 필요합니다.

4. **Next**를 클릭합니다. **문제** 대화 상자가 나타납니다.

	![새 지원 요청 창](./media/storsimple-contact-microsoft-support/Ibiza5a.png)

5. **문제** 대화 상자에서 다음을 완료합니다.

    1.  드롭다운 목록에서 **심각도** 수준을 선택합니다.
    2.  드롭다운 목록에서 **문제 유형**을 선택합니다.
    3.  드롭다운 목록에서 **범주**를 선택합니다.
    4.  **세부 정보** 상자에 문제를 간략히 설명합니다.
    5.  **시간 프레임** 상자에서 가장 최근에 문제가 발생한 날짜, 시간 및 표준 시간대를 지정합니다.
    6.  **파일 업로드**에서 폴더 아이콘을 클릭하여 지원 패키지를 찾아봅니다.
    7.  **진단 정보 공유** 확인란을 선택합니다.

6. **Next**를 클릭합니다. **연락처 정보** 대화 상자가 나타납니다.

	![새 지원 요청 창](./media/storsimple-contact-microsoft-support/Ibiza6a.png)

7. 연락처 정보를 입력하고 연락 방법(전화 또는 전자 메일)을 선택합니다.

8. **향후 지원 요청에 대한 연락처 변경 내용 저장** 확인란을 선택합니다.

9. **만들기**를 클릭합니다.

요청을 제출한 후에 지원 엔지니어가 요청을 계속 진행하기 위해 가능한 한 빨리 연락할 것입니다.

## StorSimple용 Windows PowerShell에서 지원 세션 시작

StorSimple 장치에서 발생할 수 있는 문제를 해결하려면 Microsoft 기술 지원 서비스 팀과 연계해야 합니다. Microsoft 기술 지원 서비스 팀은 장치에 로그온하기 위해 지원 세션을 사용해야 합니다.

지원 세션을 시작하려면 다음 단계를 수행합니다.

#### 지원 세션을 시작하려면

1. 직렬 콘솔을 사용하여 직접 또는 원격 컴퓨터에서 텔넷 세션을 통해 장치에 액세스합니다. 이렇게 하려면 [PuTTY를 사용하여 장치 직렬 콘솔에 연결](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)의 단계를 따르세요.

2. 열린 세션에서 **Enter** 키를 눌러 명령 프롬프트를 엽니다.

3. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.

4. 프롬프트에 다음 암호를 입력합니다.

	`Password1`

5. 프롬프트에 다음 명령을 입력합니다.

	`Enable-HcsSupportAccess`

6. 암호화된 문자열을 표시합니다. 메모장과 같은 텍스트 편집기에 이 문자열을 복사합니다.

7. 이 문자열을 저장하고 Microsoft 기술 지원 서비스에 전자 메일 메시지로 보냅니다.

> [AZURE.IMPORTANT] `Disable-HcsSupportAccess`를 실행하여 지원 액세스를 비활성화할 수 있습니다. StorSimple 장치는 세션이 시작된 8시간 후에 지원 액세스를 비활성화하려고 합니다. 지원 세션을 시작한 후에 StorSimple 장치 자격 증명을 변경하는 것이 좋습니다.

<!---HONumber=AcomDC_0921_2016-->