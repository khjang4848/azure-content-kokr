<properties
	pageTitle="Azure Multi-Factor Authentication FAQ"
	description="Azure multi-factor Authentication과 관련된 질문과 대답 목록을 제공합니다. Multi-Factor Authentication은 사용자 이름 및 암호 이상을 요구하여 사용자 ID를 확인하는 방법입니다. 이 기능은 사용자 로그인 및 트랜잭션에 대한 보안의 추가 계층을 제공합니다."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="kgremban"/>

# Azure Multi-Factor Authentication FAQ


이 FAQ에서는 청구 모델 및 유용성에 대한 질문을 포함하여 Azure Multi-factor Authentication 및 Multi-Factor Authentication 서비스 사용에 대한 일반적인 질문에 대한 답변을 제공합니다.

## 일반

**Q: Azure Multi-Factor Authentication에 대한 도움을 받으려면 어떻게 해야 하나요?**

- [Microsoft 지원 기술 자료 검색](https://www.microsoft.com/ko-KR/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)

  기술 자료에서 일반적인 기술 문제에 대한 해결 방법을 검색합니다.

- [Microsoft Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

  커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)에서 직접 원하는 질문을 할 수 있습니다.

- [암호 재설정](mailto:phonefactorsupport@microsoft.com)

  기존 PhoneFactor 고객이며 암호를 재설정에 대해 질문이 있거나 도움이 필요한 경우 [암호 재설정](mailto:phonefactorsupport@microsoft.com) 링크를 사용하여 지원 사례를 개설하세요.

- [Azure Multi-Factor Authentication 서버(PhoneFactor) 지원](https://support.microsoft.com/oas/default.aspx?prid=14947)

  이 링크를 사용하여 Microsoft 기술 지원 엔지니어에게 문의합니다. 전자 메일, 온라인 제출 또는 전화 지원이 포함될 수 있는 지원 옵션을 확인하는 데 도움이 되는 몇 가지 질문이 요구됩니다.



**Q: Azure Multi-Factor Authentication 서버는 사용자 데이터를 어떻게 처리하나요?**

Multi-Factor Authentication 서버를 사용하면 사용자의 데이터가 온-프레미스 서버에만 저장됩니다. 영구 사용자 데이터는 클라우드에 저장되지 않습니다. 사용자가 2단계 인증을 수행하는 경우 Multi-factor Authentication 서버는 인증을 위해 Azure Multi-factor Authentication 클라우드 서비스에 데이터를 보냅니다. 인증 요청이 클라우드 서비스에 전송되면 다음 목록에 포함된 필드의 데이터가 요청 및 로그에 전송되어 고객의 인증 및 사용 보고서에서 사용할 수 있게 됩니다. 일부 필드는 선택 사항이며 Multi-factor Authentication 서버에서 구성할 수 있습니다. Multi-factor Authentication 서버에서 Multi-factor Authentication 클라우드 서비스로의 통신에는 포트 443 아웃바운드을 통해 SSL(Secure Sockets Layer) 또는 TLS(전송 계층 보안)가 사용됩니다. 2단계 인증 로그에 포함된 데이터 필드는 다음과 같습니다.

- **고유 ID**(사용자 이름 또는 온-프레미스 Multi-Factor Authentication 서버 ID)
- **이름과 성**(선택 사항)
- **전자 메일 주소**(선택 사항)
- **전화 번호**(음성 통화 또는 SMS 인증을 수행할 때)
- **장치 토큰**(모바일 앱 인증을 수행할 때)
- **인증 모드**
- **인증 결과**
- **Multi-Factor Authentication 서버 이름**
- **Multi-Factor Authentication 서버 IP**
- **클라이언트 IP**(사용 가능한 경우)



이러한 필드 외에도 인증 결과(성공 또는 거부) 및 모든 거부 사유는 인증 데이터와 함께 저장되어 인증 및 사용 보고서에서 사용할 수 있습니다.




## 결제

**Q: 사용자를 인증하는 데 사용되는 전화 통화 또는 문자 메시지 요금은 조직에 청구되나요?**

모든 비용은 서비스의 사용자 단위 또는 인증 단위 비용으로 산정됩니다. 조직에는 Azure Multi-Factor Authentication을 통해 사용자에게 전달된 개별 전화 통화 또는 문자 메시지에 대한 요금이 부과되지 않습니다. 전화 서비스 통신사는 전화 명의자가 전화 또는 문자 메시지를 받을 때 로밍 관련 또는 기타 비용을 부과할 수 있습니다.

**Q: Azure Multi-Factor Authentication에 대한 요금은 조직에 어떻게 청구되나요?**

Azure Multi-Factor Authentication은 사용자 및 인증 청구 옵션당 독립 실행형 서비스로 사용 가능하거나 Azure Active Directory Premium, Enterprise Mobility Suite 또는 Enterprise Cloud Suite와 함께 제공됩니다. 독립 실행형 서비스는 조직의 Azure 약정 금액에 대해 월별로 또는 Microsoft 기업 계약, Microsoft Open License 프로그램, Microsoft CSP(클라우드 솔루션 공급자) 프로그램 또는 Direct를 통해 사용할 수 있는 사용자당 연간 라이선스로 청구되는 소비를 기반으로 사용할 수 있습니다.

>[AZURE.IMPORTANT]
오스트레일리아 지역은 사업 소재지가 오스트레일리아나 뉴질랜드로 되어 있는 고객만 사용할 수 있습니다.

청구 모델 | 가격
------------- | ------------- |
사용자 당 소비(Azure 약정 금액)| 1\.40$/월(무제한 인증)
인증 단위 소비(Azure 약정 금액)|$1.40에 10건 인증
사용자당 연간 라이선스(직접)|1\.40$/월(무제한 인증)
사용자당 연간 라이선스(볼륨 라이선스)|[기업 계약 담당자](https://www.microsoft.com/ko-KR/licensing/licensing-programs/enterprise.aspx)에 문의합니다.

**Q: 사용자 단위 청구 모델은 Multi-Factor Authentication을 사용하도록 구성된 사용자 수를 기반으로 청구되나요? 아니면 확인을 수행하는 사용자의 수에 따라 요금이 청구되나요?**

대금 청구는 Multi-Factor Authentication을 사용하도록 구성된 사용자 수를 기준으로 합니다.

**Q:Multi-Factor Authentication은 어떤 방식으로 청구됩니까?**

조직에서 사용자 단위 또는 인증 단위 소비 청구 및 사용 모델을 사용하는 경우 조직의 관리자는 Azure 클래식 포털에서 Multi-Factor Authentication 공급자를 만들 때 사용 형식을 선택합니다. 가상 컴퓨터, 웹 사이트 등과 같은 조직의 Azure 구독에 대해 청구되는 리소스입니다. 라이선스 모델을 사용하는 경우 Azure Multi-Factor Authentication 라이센스를 구입한 다음 Office 365 및 기타 구독 제품과 마찬가지로 사용자에게 할당합니다.

**Q: 관리자용 Azure Multi-Factor Authentication의 무료 버전이 있나요?**

Azure Multi-Factor Authentication 공급자가 해당하는 Azure Active Directory 인스턴스에 연결되지 않았을 때 Azure 관리자용 Multi-Factor Authentication으로 알려진 Azure Multi-Factor Authentication 기능 일부가 Azure 전역 관리자 그룹의 구성원에게 무료로 제공됩니다. Multi-Factor Authentication 공급자를 사용하여 Multi-Factor Authentication을 사용하도록 구성된 디렉터리의 모든 관리자 및 사용자를 Azure Multi-Factor Authentication의 정식 버전으로 업그레이드합니다.

**Q:Office 365 사용자용 Azure Multi-Factor Authentication의 무료 버전이 있나요?**

Azure Multi-Factor Authentication 공급자가 해당하는 Azure Active Directory 인스턴스에 연결되지 않았을 때 Office 365용 Multi-Factor Authentication으로 알려진 Azure Multi-Factor Authentication 기능 일부가 Office 365 라이선스가 할당된 사용자에게 무료로 제공됩니다. Multi-Factor Authentication 공급자를 사용하여 Multi-Factor Authentication을 사용하도록 구성된 디렉터리의 모든 관리자 및 사용자를 Azure Multi-Factor Authentication의 정식 버전으로 업그레이드합니다.

**Q: 조직에서 사용자당 청구 모델과 인증당 청구 모델 간을 전환할 수 있습니까?**

조직에서는 리소스를 만들 때 청구 모델을 선택합니다. 리소스가 프로비전된 후에는 요금 청구 모델을 변경할 수 없습니다. 그러나 새로운 Multi-Factor Authentication 리소스를 생성하여 원래 리소스를 대체할 수 있습니다. 단, 사용자 설정 및 구성 옵션은 새로운 리소스로 이전될 수 없습니다.

**Q: 조직에서 언제든지 소비 대금 청구 및 라이선스 모델 간을 전환할 수 있습니까?**

조직에서는 언제든지 Azure Multi-Factor Authentication, Azure Active Directory Premium, Enterprise Mobility Suite 및 Enterprise Cloud Suite 라이선스를 구입할 수 있습니다. 라이선스가 사용자당 Azure Multi-Factor Authentication 공급자를 이미 가지고 있는 디렉터리에 추가되면 사용 기반 청구는 소유된 라이선스의 수로 감소됩니다. Multi-Factor Authentication을 사용하도록 구성된 모든 사용자에게 라이선스가 할당되면 관리자는 Azure Multi-Factor Authentication 공급자를 삭제할 수 있습니다. 조직은 라이선스 모델을 사용하여 인증당 소비 청구를 혼합할 수 없습니다. 인증당 Multi-Factor Authentication 공급자가 디렉터리에 연결되면 조직은 소유한 라이선스에 관계없이 모든 Multi-Factor Authentication 확인 요청에 대한 요금이 청구됩니다.

**Q: 내 조직은 ID를 사용하고 동기화하여 Azure Multi-Factor Authentication을 사용해야 합니까?**

조직에서 사용량 기반 청구 모델을 사용하는 경우 Azure Active Directory가 필요하지 않습니다. 디렉터리에 Multi-Factor Authentication 공급자를 연결하는 것은 선택 사항입니다. 조직이 디렉터리에 연결되어 있지 않은 경우 Azure Multi-factor Authentication 서버 또는 Azure Multi-factor Authentication SDK 온-프레미스를 배포할 수 있습니다. 라이선스를 구입하고 디렉터리의 사용자에게 할당할 때 라이선스가 디렉터리에 추가되기 때문에 라이선스 모델에 대해 Azure Active Directory가 필요합니다.


## 유용성

**Q: 사용자가 전화로 응답을 받지 못하거나 사용자가 전화를 사용할 수 없는 경우 어떻게 해야 하나요?**

이전에 백업 휴대폰을 구성한 경우 로그인 페이지에서 확인 메시지가 표시될 때 해당 휴대폰을 선택하여 다시 시도합니다. 사용자에게 다른 방법이 구성되어 있지 않으면 사용자는 조직의 관리자에게 문의하고 사용자의 기본 전화(휴대폰 또는 사무실 전화)에 할당된 번호를 업데이트하도록 요청해야 합니다.


**Q: 사용자가 더 이상 액세스할 수 없는 계정에 대한 문의할 경우 관리자는 어떻게 해야 하나요?**

관리자는 사용자가 등록 프로세스를 다시 진행하도록 하여 사용자 계정을 재설정할 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](multi-factor-authentication-manage-users-and-devices.md)에 대해 자세히 알아보세요.

**Q: 앱 암호를 사용하는 사용자의 전화를 분실하거나 도난당하는 경우 관리자는 어떻게 해야 하나요?**

관리자는 모든 사용자 앱 암호를 삭제하여 무단 액세스를 방지할 수 있습니다. 사용자에게 교체용 장치가 있는 경우 앱 암호를 다시 만들 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](multi-factor-authentication-manage-users-and-devices.md)에 대해 자세히 알아보세요.

**Q: 사용자가 비브라우저 앱에 로그인할 수 없으면 어떻게 하나요?**

- Multi-Factor Authentication을 사용하도록 구성된 사용자는 일부 비브라우저 앱에 로그인하기 위해 앱 암호가 필요합니다.
- 사용자는 로그인 정보를 지우고(삭제), 앱을 다시 시작하고, 자신의 사용자 이름 및 앱 암호를 사용하여 로그인해야 합니다.

앱 암호 만들기에 대한 자세한 내용 및 기타 [앱 암호에 대한 도움말](multi-factor-authentication-end-user-app-passwords.md)을 확인하세요.


>[AZURE.NOTE] Office 2013 클라이언트에 대한 최신 인증
>
> Office 2013 클라이언트(Outlook 포함)는 새 인증 프로토콜을 지원합니다. Office 2013을 Multi-Factor Authentication을 지원하도록 구성할 수 있습니다. Office 2013을 구성한 후에 Office 2013 클라이언트에 대해 앱 암호가 필요합니다. 자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.

**Q: 문자 메시지가 수신되지 않거나 양방향 문자 메시지에 응답했으나 확인 제한 시간이 초과되면 어떻게 해야 하나요?**

Azure Multi-Factor Authentication 서비스는 SMS 집계를 통해 문자 메시지를 보냅니다. 사용되는 집계, 대상 국가, 사용자의 휴대폰 통신사 및 신호 강도를 비롯한 다양한 요인이 문자 메시지의 전달 및 수신 안정성에 영향을 줄 수 있습니다. 이로 인해 양방향 SMS에서 문자 메시지의 전달 및 SMS 응답의 수신이 보장되지 않습니다. 가능하면 양방향 SMS보다는 단방향 SMS를 사용하는 것이 좋습니다. 단방향 SMS는 보다 안정적이며 다른 국가에서 전송된 문자 메시지에 응답할 때 발생하는 전역 SMS 요금이 부과되지 않도록 합니다.

미국 및 캐나다 같은 일부 국가 또는 지역에서는 좀 더 확실한 문자 메시지 확인 기능이 보장됩니다. Azure Multi-Factor Authentication을 사용할 때 문자 메시지가 안정적으로 수신되지 않는 경우 대신 모바일 앱 또는 휴대폰 전화 방법을 선택하는 것이 좋습니다. 모바일 앱 인증 방법의 경우 사용자가 셀룰러 및 Wi-Fi 연결 둘 다에 대해 모바일 앱 알림을 받을 수 있으므로 유용합니다. 또한 장치에 신호가 전혀 없는 경우에도 모바일 앱 암호가 표시됩니다. [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 및 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073) 장치의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

**Q: Azure Multi-factor Authentication 서버에서 하드웨어 토큰을 사용할 수 있나요?**

Azure Multi-factor Authentication 서버를 사용하는 경우 타사 OATH(공개 인증), TOTP(시간 기반, 일회용 암호) 토큰을 가져온 후 Multi-Factor Authentication에 사용할 수 있습니다. Gemalto가 토큰에 대해 생성한 구형 PSKC(이식 가능한 대칭 키 컨테이너) 형식의 타사 OATH TOTP 토큰과, CSV 형식의 토큰에 대한 가져오기를 지원합니다. CSV 형식의 토큰을 가져올 때는 해당 CSV 파일에 일련 번호, Base32 형식의 암호 키, 시간 간격(일반적으로 30초)이 포함되어 있어야 합니다.

Azure Multi-factor Authentication 서버에 가져올 수 있는 CSV 파일에 추가할 수 있는 경우 OATH TOTP 토큰에 해당하는 ActiveIdentity 토큰을 사용할 수 있습니다. OATH 토큰은 ADFS(Active Directory 페더레이션 서비스), RADIUS(원격 인증 전화 접속 사용자 서비스)(클라이언트 시스템에서 액세스 챌린지 응답을 처리할 수 있는 경우), IIS(인터넷 정보 서버) 폼 기반 인증에서 사용할 수 있습니다.

**Q: Azure Multi-factor Authentication 서버를 사용하여 터미널 서비스 보안을 유지할 수 있나요?**

예. 그렇지만 Windows Server 2012 R2 이상 버전을 사용하는 경우 RD 게이트웨이(원격 데스크톱 게이트웨이)를 사용해야만 이 작업을 수행할 수 있습니다.

Windows Server 2012 R2의 보안 변경 때문에 Azure Multi-factor Authentication 서버가 Windows Server 2012 및 이전 버전에서 LSA(로컬 보안 기관) 보안 패키지에 연결하는 방식이 변경되었습니다. Windows 2012 이전의 터미널 서비스 버전의 경우 [Windows 인증으로 응용 프로그램 보호](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)를 간단히 수행할 수 있습니다. Windows Server 2012 R2를 사용하는 경우 RD 게이트웨이가 필요합니다.

**Q: 호출자 ID를 설정한 후에 익명 발신자의 Multi-factor Authentication 호출을 받게 되는 이유는 무엇인가요?**

경우에 따라 Multi-Factor Authentication 호출이 공용 전화망을 통해 이루어진 경우 발신자 번호를 지원하지 않는 통신 회사를 통해 라우팅됩니다. 이 때문에 항상 Multi-Factor Authentication에서 호출자 ID를 보내더라도 이 ID가 보장되지 않습니다.


## 오류

**Q: 사용자가 모바일 앱 알림을 사용하여 인증할 때 "활성화된 계정에 대한 인증 요청이 아닙니다." 오류 메시지가 표시되는 경우 어떻게 해야 하나요?**

다음 절차를 따릅니다.

1. [Azure 포털 프로필](https://account.activedirectory.windowsazure.com/profile/)로 이동한 후 조직 계정으로 로그인합니다.
2. 필요한 경우 **기타 확인 옵션**을 클릭하고 계정 확인에 사용할 다른 옵션을 클릭합니다.
3. **추가 보안 확인**을 클릭합니다.
4. 모바일 앱에서 기존 계정을 제거합니다.
5. **구성**을 클릭하고 지침에 따라 모바일 앱을 다시 구성합니다.




**Q: 사용자가 비브라우저 응용 프로그램을 사용하여 로그인하려고 할 때 0x800434D4L 오류 메시지가 표시되면 어떻게 해야 하나요?**

현재, 사용자는 사용자의 브라우저를 통해 액세스할 수 있는 응용 프로그램 및 서비스에서만 추가 보안 확인을 사용할 수 있습니다. Windows PowerShell과 같은 로컬 컴퓨터에 설치된 비브라우저 응용 프로그램(*리치 클라이언트 응용 프로그램*이라고도 함)은 추가 보안 확인에 필요한 계정에서 작동되지 않습니다. 이 경우 응용 프로그램에서 0x800434D4L 오류가 발생할 수 있습니다.

이 문제를 해결하려면 관리 관련 작업용 사용자 계정과 비관리 관련 작업용 사용자 계정을 따로 두어야 합니다. 나중에 관리 계정과 비관리 계정 간의 사서함을 연결하면 비관리 계정을 사용하여 Outlook에 로그인할 수 있습니다. 이에 대한 자세한 내용은 [관리자에게 사용자의 사서함 내용을 열고 보는 기능 제공](http://help.outlook.com/141/gg709759.aspx?sl=1) 방법을 참조하세요.

<!---HONumber=AcomDC_0921_2016-->