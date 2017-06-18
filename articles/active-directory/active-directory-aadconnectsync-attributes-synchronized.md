<properties
	pageTitle="Azure AD Connect 동기화: Azure Active Directory에 특성 동기화 | Microsoft Azure"
	description="Azure Active Directory에 동기화된 특성을 나열합니다."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="markvi;andkjell"/>


# Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성
이 항목에서는 Azure AD Connect 동기화에 의해 동기화되는 특성을 보여 줍니다. 특성은 관련된 Azure AD 앱으로 그룹화되었습니다.

## 동기화할 특성
일반적으로 *동기화할 최소 특성 목록*에 대해 궁금해 합니다. 기본 및 권장되는 접근 방법은 클라우드에서 전체 GAL(전체 주소 목록)을 생성하고 Office 365 워크로드의 모든 기능을 사용할 수 있도록 기본 특성을 유지하는 것입니다. 일부 경우에 이 예제(![잘못된 특성](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png))와 같이 이러한 특성은 중요한 데이터 또는 PII(개인 식별 정보) 데이터를 포함하므로 조직에서 클라우드와 동기화하지 않으려는 일부 특성이 있습니다.

이 경우 이 항목에서 특성의 목록으로 시작하고 중요한 데이터 또는 PII 데이터를 포함하고 동기화할 수 없는 이러한 특성을 식별합니다. 그런 다음 [Azure AD 앱 및 특성 필터링](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)을 사용하여 설치 중에 이러한 특성을 선택 취소합니다.

>[AZURE.WARNING] 특성을 선택 취소하는 경우 주의해야 하고 동기화가 불가능한 특성만 선택 취소해야 합니다. 다른 특성을 선택 취소하면 기능에 부정적인 영향을 미칠 수도 있습니다.

## Office 365 ProPlus

| 특성 이름| 사용자| 주석 |
| --- | :-: | --- |
| accountEnabled| X| 활성화된 계정을 정의합니다.|
| cn| X| |
| displayName| X| |
| objectSID| X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| pwdLastSet| X| 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| sourceAnchor| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| usageLocation| X| 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|

## Exchange Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| 도우미| X| X| | |
| authOrig| X| X| X| |
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| 이 특성은 현재 그룹에 사용되지 않습니다.|
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| maDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| 이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute2| X| X| X| 이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute3| X| X| X| 이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute4| X| X| X| 이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute5| X| X| X| 이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg IsOrganizational| | | X| |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userCertificate| X| X| | |
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |

## SharePoint Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| authOrig| X| X| X| |
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|
| wWWHomePage| X| X| | |

## Lync Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|
| wWWHomePage| X| X| | |

## Azure RMS

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| cn| X| | X| 일반 이름 또는 별칭입니다. 대부분 [mail] 값의 접두사입니다.|
| displayName| X| X| X| 이름을 나타내는 문자열로 보통 친숙한 이름(성명)으로 표시됩니다.|
| mail| X| X| X| 메일 주소 전체입니다.|
| member| | | X| |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| proxyAddresses| X| X| X| 기계적 속성입니다. Azure AD에서 사용됩니다. 사용자에 대한 모든 보조 메일 주소를 포함합니다.|
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다.|
| securityEnabled| | | X| groupType에서 파생됩니다.|
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | 이 UPN은 사용자용 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|

## Intune

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| C| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| securityEnabled| | | X| groupType에서 파생됩니다|
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|

## Dynamics CRM

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|

## 타사 응용 프로그램
이 그룹은 일반 워크로드 또는 응용 프로그램에 필요한 최소한의 특성으로 사용되는 특성 모음입니다. 다른 섹션에 나열되지 않은 워크로드 또는 타사 앱에 사용할 수 있습니다. 다음에 명시적으로 사용됩니다.

- Yammer(User만 사용됨)
- [SharePoint와 같은 리소스에서 제공하는 하이브리드 B2B 조직 간 공동 작업 시나리오](http://go.microsoft.com/fwlink/?LinkId=747036)

이 그룹은 Azure AD Directory가 Office 365, Dynamics, 또는 Intune을 지원하지 않을 때 사용되는 특성의 집합입니다. 코어 특성의 작은 집합이 있습니다.

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| cn| X| | X| |
| displayName| X| X| X| |
| givenName| X| X| | |
| mail| X| | X| |
| managedBy| | | X| |
| mailNickName| X| X| X| |
| member| | | X| |
| objectSID| X| | | 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다.|
| sn| X| X| | |
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|

## Windows 10
Windows 10 도메인에 가입된 컴퓨터(장치)는 일부 특성을 Azure AD에 동기화합니다. 이 시나리오에 대한 자세한 내용은 [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)을 참조하세요. 이 특성은 항상 동기화되며 Windows 10은 선택 취소할 수 있는 앱으로 표시되지 않습니다. Windows 10 도메인에 가입된 컴퓨터는 특성 userCertificate를 채우는 것으로 식별됩니다.

| 특성 이름| 장치| 주석 |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| 도메인에 가입된 컴퓨터에 대해 하드 코딩된 값입니다. |
| displayName | X| |
| ms-DS-CreatorSID | X| registeredOwnerReference라고도 합니다.|
| objectGUID | X| deviceID라고도 합니다.|
| objectSID | X| onPremisesSecurityIdentifier라고도 합니다.|
| operatingSystem | X| deviceOSType이라고도 합니다.|
| operatingSystemVersion | X| deviceOSVersion이라고도 합니다.|
| userCertificate | X| |

이 특성은 선택한 다른 앱 외에 **사용자**에 대해서도 있습니다.

| 특성 이름| 사용자| 주석 |
| --- | :-: | --- |
| domainFQDN| X| dnsDomainName이라고도 합니다. 예를 들어 contoso.com입니다.|
| domainNetBios| X| netBiosName이라고도 합니다. 예를 들어 CONTOSO입니다.|

## Exchange 하이브리드 쓰기 저장
이 특성들은 **Exchange 하이브리드**를 사용하도록 설정할 때 Azure AD에서 온-프레미스 Active Directory로 다시 기록됩니다. Exchange 버전에 따라 더 적은 특성을 동기화 할 수 있습니다.

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| msDS-ExternalDirectoryObjectID| X| | | Azure AD의 cloudAnchor에서 파생됩니다. 이 특성은 Exchange 2016에서 새로운 내용입니다.|
| msExchArchiveStatus| X| | | 온라인 보관: 고객의 메일을 보관합니다.|
| msExchBlockedSendersHash| X| | | 필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다.|
| msExchSafeRecipientsHash| X| | | 필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다.|
| msExchSafeSendersHash| X| | | 필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다.|
| msExchUCVoiceMailSettings| X| | | UM(통합 메시징)-온라인 음성 메일 사용: Microsoft Lync Server Intergration 사용자가 온라인 서비스에 음성 메일이 있는지 Lync Server 온-프레미스에 나타내기 위해서 사용합니다.|
| msExchUserHoldPolicies| X| | | 소송 보류: 어떤 사용자가 소송을 보류 중인지 확인하기 위해 클라우드 서비스를 사용합니다.|
| proxyAddresses| X| X| X| Exchange Online의 x500 주소만 삽입됩니다.|

## 장치 쓰기 저장
Active Directory에 장치 개체를 만듭니다. 이러한 개체는 Azure AD에 가입된 장치이거나 도메인에 가입된 Windows 10 컴퓨터일 수 있습니다.

| 특성 이름| 장치| 주석 |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| displayName | X| |
| dn | X| |
| msDS-CloudAnchor | X| |
| msDS-DeviceID | X| |
| msDS-DeviceObjectVersion | X| |
| msDS-DeviceOSType | X| |
| msDS-DeviceOSVersion | X| |
| msDS-DevicePhysicalIDs | X| |
| msDS-KeyCredentialLink | X| Windows Server 2016 AD 스키마에서만 |
| msDS-IsCompliant | X| |
| msDS-IsEnabled | X| |
| msDS-IsManaged | X| |
| msDS-RegisteredOwner | X| |


## 참고 사항

- 대체 ID를 사용하면, 온-프레미스 특성 userPrincipalName은 Azure AD 특성 onPremisesUserPrinciparlName과 동기화됩니다. 대체 ID 특성(예: 메일)은 Azure AD 특성 userPrincipalName과 동기화됩니다.
- 위의 목록에서 개체 형식 **User**는 개체 형식 **iNetOrgPerson**에도 적용됩니다.

## 다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0914_2016-->