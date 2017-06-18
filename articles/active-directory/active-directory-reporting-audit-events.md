<properties
   pageTitle="Azure Active Directory 감사 보고서 이벤트 | Microsoft Azure"
   description="Azure Active Directory에서 확인하고 다운로드할 수 있는 감사된 이벤트"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# Azure Active Directory 감사 보고서 이벤트

*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure Active Directory 감사 보고서를 통해 고객은 자신의 Azure Active Directory에서 발생한 권한 있는 작업을 식별할 수 있습니다. 권한 있는 작업에는 권한 상승 변경(예: 역할 만들기 또는 암호 재설정), 정책 구성 변경(예: 암호 정책) 또는 디렉터리 구성 변경(예: 도메인 페더레이션 설정 변경)이 포함됩니다. 보고서는 이벤트 이름의 감사 레코드, 작업을 수행한 행위자, 변경으로 인한 영향을 받은 대상 리소스, 날짜 및 시간(UTC)을 제공합니다. 고객은 [감사 로그 보기](active-directory-reporting-azure-portal.md)에서 설명한 대로 [Azure 포털](https://portal.azure.com/)을 통해 Azure Active Directory의 감사 이벤트 목록을 검색할 수 있습니다.


## 감사 보고서 이벤트 목록
<!--- audit event descriptions should be in the past tense --->

이벤트 | 이벤트 설명
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**사용자 이벤트** |
사용자 추가 | 디렉터리에 사용자를 추가합니다.
사용자 삭제 | 디렉터리에서 사용자를 삭제합니다.
라이선스 속성 설정 | 디렉터리에 있는 사용자의 라이선스 속성을 설정합니다.
사용자 암호 재설정 | 디렉터리의 사용자에 대한 암호를 다시 설정합니다.
사용자 암호 변경 | 디렉터리에 있는 사용자의 암호를 변경합니다.
사용자 라이선스 변경 | 디렉터리의 사용자에게 할당된 라이선스를 변경합니다. 업데이트된 라이선스를 보려면 아래의 [사용자 업데이트](#update-user-attributes) 속성을 살펴보세요.
사용자 업데이트 | 디렉터리의 사용자를 업데이트합니다. 업데이트할 수 있는 특성은 [아래에서 참조](#update-user-attributes)합니다.
사용자 암호 강제 변경 설정 | 로그인 시 사용자가 강제로 암호를 변경하도록 하는 속성을 설정합니다.
사용자 자격 증명 업데이트 | 사용자 암호 변경  
**그룹 이벤트** |
그룹 추가 | 디렉터리에 그룹 생성됨.
그룹 업데이트 | 디렉터리에 그룹 업데이트됨. 업데이트된 그룹 속성을 보려면 아래 섹션의 [그룹 속성 감사됨](#update-group-attributes)을 참조하세요.
그룹 삭제 | 디렉터리에서 그룹 삭제됨.
그룹에 멤버 추가 | 디렉터리에서 그룹에 멤버 추가됨.
그룹에서 멤버 제거 | 디렉터리의 그룹에서 멤버 제거됨.
CreateGroupSettings | 생성된 그룹 설정
UpdateGroupSettings | 그룹 설정을 업데이트했습니다. 업데이트된 그룹 설정을 보려면 아래 섹션의 [그룹 속성 감사됨](#update-group-attributes)을 참조하세요.
DeleteGroupSettings | 삭제된 그룹 설정
SetGroupLicense | 그룹 라이선스를 설정합니다.
SetGroupManagedBy | 사용자에서 관리될 그룹 설정
AddGroupMember | 그룹에 추가된 멤버
RemoveGroupMember | 그룹에서 멤버 제거
AddGroupOwner | 그룹에 추가된 소유자
RemoveGroupOwner | 그룹에서 제거된 소유자
**응용 프로그램 이벤트** |
서비스 주체 추가 | 디렉터리에 서비스 주체를 추가합니다.
서비스 주체 제거 | 디렉터리에서 서비스 주체를 제거합니다.
서비스 주체 자격 증명 추가 | 서비스 주체에 자격 증명을 추가합니다.
서비스 주체 자격 증명 제거 | 서비스 주체에서 자격 증명을 제거합니다.
위임 항목 추가 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)를 만들었습니다.
위임 항목 설정 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)를 업데이트했습니다.
위임 항목 제거 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)를 삭제했습니다.
**역할 이벤트** |
역할에 역할 멤버 추가 | 디렉터리 역할에 사용자를 추가합니다.
역할에서 역할 멤버 제거 | 디렉터리 역할에서 사용자를 제거합니다.
회사 연락처 정보 설정 | 회사 수준 연락처 기본 설정을 지정합니다. 여기에는 Microsoft 온라인 서비스에 대한 기술 관련 알림 및 마케팅을 위한 메일 주소가 포함됩니다.
위임 항목 추가 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 만들었습니다.
위임 항목 설정 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 업데이트했습니다.
위임 항목 제거 | 디렉터리에서 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity)를 삭제했습니다.
AddSevicePrincipalOwner | 서비스 주체에 소유자를 추가했습니다.
RemoveSevicePrincipalOwner | 서비스 주체에서 소유자를 제거했습니다.
AddApplication | 응용 프로그램을 추가합니다.
UpdateApplication | 응용 프로그램을 업데이트합니다. 업데이트된 앱 설정을 보려면 아래 섹션의 [응용 프로그램 속성 감사됨](#update-application-attributes)을 참조하세요.
DeleteApplication | 응용 프로그램을 삭제합니다.
RestoreApplication|응용 프로그램을 복원합니다.
AddApplicationOwner | 	응용 프로그램에 소유자를 추가합니다.
RemoveApplicationOwner | 	응용 프로그램에서 소유자를 제거합니다.
**역할 이벤트** |
역할에 역할 멤버 추가 | 디렉터리 역할에 사용자를 추가합니다.
역할에서 역할 멤버 제거 | 디렉터리 역할에서 사용자를 제거합니다.
AddRoleDefinition | 역할 정의를 추가했습니다.
UpdateRoleDefinition | 역할 정의를 업데이트했습니다. 업데이트된 역할 설정을 보려면 아래 섹션의 [역할 정의 속성 감사됨](#update-role-definition-attributes)을 참조하세요.
DeleteRoleDefinition | 역할 정의를 삭제했습니다.
AddRoleAssignmentToRoleDefinition | 역할 정의에 역할 할당을 추가했습니다.
RemoveRoleAssignmentFromRoleDefinition | 역할 정의에서 역할 할당을 제거했습니다.
AddRoleFromTemplate | 템플릿에서 역할을 추가했습니다.
UpdateRole | 역할을 업데이트했습니다.
AddRoleScopeMemberToRole | 역할에 범위가 지정된 멤버를 추가했습니다.
RemoveRoleScopedMemberFromRole | 역할에서 범위가 지정된 멤버를 제거했습니다.
**장치 이벤트(모든 새 이벤트)** |
AddDevice | 장치를 추가했습니다.
UpdateDevice | 장치를 업데이트했습니다. 업데이트된 장치 속성을 보려면 아래 섹션의 [장치 속성 감사됨](#update-device-attributes)을 참조하세요.
DeleteDevice | 장치를 삭제했습니다.
AddDeviceConfiguration | 장치 구성을 추가했습니다.
UpdateDeviceConfiguration | 장치 구성을 업데이트했습니다. 업데이트된 장치 구성 속성을 보려면 아래 섹션의 [장치 구성 속성 감사됨](#update-device-configuration-attributes)을 참조하세요.
DeleteDeviceConfiguration | 장치 구성을 삭제했습니다.
AddRegisteredOwner | 장치에 등록된 소유자를 추가했습니다.
AddRegisteredUsers | 장치에 등록된 사용자를 추가했습니다.
RemoveRegisteredOwner | 장치에서 등록된 소유자를 제거합니다.
RemoveRegisteredUsers | 장치에서 등록된 사용자를 제거합니다.
RemoveDeviceCredentials | 장치 자격 증명을 제거합니다.
**B2B 이벤트** |
배치 초대가 업로드되었습니다. | 관리자는 초대를 포함하는 파일을 업로드하여 파트너 사용자에게 보냅니다.
배치 초대가 처리되었습니다. | 파트너 사용자에 보내는 초대를 포함하는 파일이 처리되었습니다.
외부 사용자를 초대합니다. | 외부 사용자를 디렉터리에 초대했습니다.
외부 사용자 초대를 충전합니다. | 외부 사용자가 자신의 초대를 디렉터리에 충전했습니다.
외부 사용자를 그룹에 추가합니다. | 외부 사용자가 디렉터리의 그룹에 멤버 자격을 할당 받았습니다.
외부 사용자를 응용 프로그램에 할당합니다. | 외부 사용자는 응용 프로그램에 대한 직접 액세스에 할당되었습니다.
바이럴 테넌트 만들기입니다. | 새 테넌트는 초대 상환에 의해 Azure AD에서 만들어졌습니다.
바이럴 사용자 만들기입니다. | 사용자는 초대 상환에 의해 Azure AD의 기존 테넌트에서 만들어졌습니다.
**관리 장치(모든 새 이벤트)** |
AddAdministrativeUnit | 관리 장치를 추가합니다.
UpdateAdministrativeUnit| 	관리 장치를 업데이트합니다. 업데이트된 관리 장치 속성을 보려면 아래 섹션의 [관리 장치 속성 감사됨](#update-administrative-unit-attributes)을 참조하세요.
DeleteAdministrativeUnit| 	관리 장치를 삭제합니다.
AddMemberToAdministrativeUnit| 	관리 장치에 멤버를 추가합니다.
RemoveMemberFromAdministrativeUnit| 	관리 장치에서 멤버를 제거합니다.
**Directory 이벤트** |
회사에 파트너 추가 | 디렉터리에 파트너를 추가합니다.
회사에서 파트너 제거 | 디렉터리에서 파트너를 제거합니다.
DemotePartner | 파트너를 강등합니다.
회사에 도메인 추가 | 디렉터리에 도메인을 추가합니다.
회사에서 도메인 제거 | 디렉터리에서 도메인을 제거합니다.
도메인 업데이트 | 디렉터리에서 도메인을 업데이트합니다. 업데이트된 도메인 속성을 보려면 아래 섹션의 [도메인 속성 감사됨](#update-domain-attributes)을 참조하세요.
도메인 인증 설정 | 회사의 기본 도메인 설정을 변경합니다.
회사 연락처 정보 설정 | 회사 수준 연락처 기본 설정을 지정합니다. 여기에는 Microsoft 온라인 서비스에 대한 기술 관련 알림 및 마케팅을 위한 메일 주소가 포함됩니다.
도메인에 페더레이션 설정 지정 | 도메인의 페더레이션 설정을 업데이트합니다.
도메인 확인 | 디렉터리의 도메인을 확인합니다.
메일로 확인된 도메인 확인 | 메일 확인을 사용하여 디렉터리의 도메인을 확인합니다.
회사에 DirSyncEnabled 플래그 설정 | Azure AD Sync에 디렉터리를 사용하도록 하는 속성을 설정합니다.
암호 정책 설정 | 사용자 암호의 길이 및 문자 제약 조건을 설정합니다.
회사 정보 설정 | 회사 수준 정보를 업데이트합니다. 자세한 내용은 [Get-msolcompanyinformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet을 참조하세요.
SetCompanyAllowedDataLocation | 	회사에 허용된 데이터 위치를 설정합니다.
SetCompanyDirSyncEnabled | DirSyncEnabled 플래그를 설정합니다.
SetCompanyDirSyncFeature | 	DirSync 기능을 설정합니다.
SetCompanyInformation | 회사 정보를 설정합니다.
SetCompanyMultiNationalEnabled | 	회사에 다국적 기능 활성화를 설정합니다.
SetDirectoryFeatureOnTenant | 	테넌트에 디렉터리 기능을 설정합니다.
SetTenantLicenseProperties | 테넌트 라이선스 속성을 설정합니다.
CreateCompanySettings | 회사 설정 만들기
UpdateCompanySettings | 회사 설정을 업데이트합니다. 업데이트된 회사 속성을 보려면 아래 섹션의 [회사 속성 감사됨](#update-company-attributes)을 참조하세요.
DeleteCompanySettings | 회사 설정 삭제
SetAccidentalDeletionThreshold | 	실수로 인한 삭제 임계값을 설정합니다.
SetRightsManagementProperties | 권한 관리 속성을 설정합니다.
PurgeRightsManagementProperties | 권한 관리 속성을 제거합니다.
UpdateExternalSecrets | 외부 암호 업데이트
**정책 이벤트(모든 새 이벤트)** |
AddPolicy | 	정책을 추가합니다.
UpdatePolicy |	정책을 업데이트합니다.
DeletePolicy |	정책을 삭제합니다.
AddDefaultPolicyApplication |	응용 프로그램에 정책을 추가합니다.
AddDefaultPolicyServicePrincipal |	서비스 주체에 정책을 추가합니다.
RemoveDefaultPolicyApplication |	응용 프로그램에서 정책을 제거합니다.
RemoveDefaultPolicyServicePrincipal |	서비스 주체에서 정책을 제거합니다.
RemovePolicyCredentials |	정책 자격 증명을 제거합니다.

## 감사 보고서 보존
Azure AD 감사 보고서의 이벤트는 180일 동안 보존됩니다. 보고서에 보존하는 방법에 대한 자세한 내용은 [Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md)을 참조하세요.

더 긴 보존 기간 동안 감사 이벤트를 저장하려는 고객은 Reporting API를 사용하여 별도의 데이터 저장소에 정기적으로 감사 이벤트를 저장할 수 있습니다. 자세한 내용은 [Reporting API 시작](active-directory-reporting-api-getting-started.md)을 참조하세요.

## 각 감사 이벤트에 포함된 속성

속성 | 설명
------------- | --------------------------------------------------------------
날짜 및 시간 | 감사 이벤트가 발생한 날짜 및 시간
행위자 | 작업을 수행한 사용자 또는 서비스 주체
작업 | 수행된 작업
대상 | 작업이 수행된 사용자 또는 서비스 주체


## "업데이트 사용자" 특성
"사용자 업데이트" 감사 이벤트에는 업데이트된 사용자 특성에 대한 추가 정보가 포함됩니다. 특성마다 이전 값과 새 값이 모두 포함됩니다.

특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | 사용자가 로그인할 수 있습니다.
AssignedLicense | 사용자에게 할당된 모든 라이선스입니다.
AssignedPlan | 사용자에게 할당된 라이선스에서 발생한 서비스 계획입니다.
LicenseAssignmentDetail | 사용자에게 할당된 라이선스의 세부 정보입니다. 예를 들어, 그룹 기반 라이선스가 관련되어 있으면 라이선스를 부여한 그룹이 포함됩니다.
모바일 | 사용자의 휴대폰입니다.
OtherMail | 사용자의 암호 확인용 메일입니다.
OtherMobile | 사용자의 대체 휴대폰입니다.
StrongAuthenticationMethod | Multi-Factor Authentication(예: 모바일 앱으로부터의 음성 통화, SMS 또는 확인 코드)을 위해 사용자가 구성한 검증 방법의 목록입니다.
StrongAuthenticationRequirement | 이 사용자에 대한 Multi-Factor Authentication의 강제 적용, 사용 또는 사용 안 함의 여부입니다.
StrongAuthenticationUserDetails | Multi-Factor Authentication 및 암호 재설정 확인에 사용된 사용자 전화 번호, 대체 전화 번호, 메일 주소입니다.
StrongAuthenticationPhoneAppDetail | 2FA 로그인을 수행하도록 등록된 휴대폰 앱의 세부 정보
TelephoneNumber | 사용자의 전화 번호입니다.
AlternativeSecurityId | 개체에 대한 대체 보안 ID입니다.
CreationType |사용자의 생성 방법입니다(초대 또는 바이럴로).
InviteTicket |사용자에 대한 초대 티켓의 목록입니다.
InviteReplyUrl |초대 수락 시 회신할 url의 목록입니다.
InviteResources |사용자가 초대된 리소스의 목록입니다.
LastDirSyncTime |권한이 있는 (고객, 온-프레미스) 디렉터리의 동기화로 인해 개체가 업데이트된 마지막 시간입니다.
MSExchRemoteRecipientType |MSO 받는 사람 형식에 매핑합니다. 받는 사람 형식은 [MSO 받는 사람 형식] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx을(를) 참조하세요.
PreferredDataLocation |사용자, 그룹, 연락처, 공용 폴더 또는 장치의 데이터에 대한 기본 설정된 위치입니다.
ProxyAddresses |외부 메일 시스템에서 Exchange Server 받는 사람 개체가 인식되는 주소입니다.
StsRefreshTokensValidFrom |새로 고침 토큰 해지 정보를 전달합니다. 이 시간 전에 발급된 STS 새로 고침 토큰은 만료된 것으로 간주됩니다.
UserPrincipalName |사용자에 대한 인터넷 스타일 로그온 이름인 UPN입니다.
UserState |사용자 PendingApproval/PendingAcceptance/Accepted/PendingVerification의 상태입니다.
UserStateChangedOn |UserState에 마지막으로 변경된 타임스탬프입니다. 수명 주기 워크플로를 트리거하는 데 사용됩니다.
UserType |사용자의 유형입니다. 멤버(0), 게스트(1), 바이럴(2)입니다.

## "그룹 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
분류 | 통합 그룹(HBI, MBI 등)에 대한 분류입니다.
설명 | 개체에 대한 알기 쉬운 설명이 포함된 구입니다.  
DisplayName |개체에 대한 표시 이름
DirSyncEnabled |동기화가 권한이 있는 (고객, 온-프레미스) 디렉터리에서 발생하는지 여부를 나타냅니다.
GroupLicenseAssignment |그룹의 라이선스 할당
GroupType |그룹 유형, 통합(0)
IsMembershipRuleLocked |MembershipRule 속성이 셀프 서비스 그룹 관리 서비스에서 설정되었으며 사용자가 변경할 수 없음을 나타냅니다. GroupType이 GroupType.DynamicMembership을 포함하는 그룹에만 적용
IsPublic |그룹이 공개/개인인지를 나타내는 플래그입니다.
LastDirSyncTime |권한이 있는 (고객, 온-프레미스) 디렉터리의 동기화의 결과로 개체가 업데이트된 마지막 시간입니다.
Mail |기본 전자 메일 주소
MailEnabled |이 그룹에 전자 메일 기능이 있는지 여부를 나타냅니다.
MailNickname |주소록 개체에 대한 Moniker입니다. 일반적으로 전자 메일 이름 앞 부분은 "@" 기호입니다.   
MembershipRule |셀프 서비스 그룹 관리 서비스에서 이 그룹에 속해야 하는 멤버를 결정하는 데 사용되는 조건을 표현하는 문자열입니다. IsMembershipRuleLocked도 참조하세요. GroupType이 GroupType.DynamicMembership을 포함하는 그룹에만 적용 가능합니다.
MembershipRuleProcessingState| 이 그룹에 대한 멤버 자격 처리 상태를 정의하는 셀프 서비스 그룹 관리 서비스에서 정의되는 열거형 값입니다. GroupType이 GroupType.DynamicMembership을 포함하는 그룹에만 적용 가능합니다.
ProxyAddresses |외부 메일 시스템에서 Exchange Server 받는 사람 개체가 인식되는 주소입니다.
RenewedDateTime |그룹이 가장 최근에 갱신된 때의 타임스탬프 레코드입니다.   
SecurityEnabled |그룹의 멤버 자격이 권한 부여 결정에 영향을 줄 수 있는지 여부를 나타냅니다.
WellKnownObject |미리 정의된 집합 중 하나로 지정하여 디렉터리 개체를 표시합니다.

## "장치 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | 보안 주체가 인증할 수 있는지 여부를 나타냅니다.
CloudAccountEnabled | 보안 주체가 인증할 수 있는지 여부를 나타냅니다. 장치가 온-프레미스에서 마스터되는 경우 InTune으로 작성됩니다.
CloudDeviceOSType | OS 기준(예: Windows RT, iOS)의 장치 유형입니다. 클라우드 서비스(예: Intune)로 설정된 경우 이 특성은 DeviceOSType에 대한 디렉터리에서 권한이 있게 됩니다.
CloudDeviceOSVersion | OS의 버전입니다. 클라우드 서비스(예: Intune)로 설정된 경우 이 특성은 DeviceOSVersion에 대한 디렉터리에서 권한이 있게 됩니다.
CloudDisplayName | DisplayName LDAP 특성의 값입니다. 클라우드 서비스(예: Intune)로 설정된 경우 이 특성은 DisplayName에 대한 디렉터리에서 권한이 생깁니다.
CloudCreated |클라우드 서비스에서 개체가 만들어졌는지 여부를 나타냅니다.
CompliantUntil | 어떤 시간까지 장치가 규정 준수로 간주됩니다.
DeviceMetadata |장치에 대한 사용자 지정 메타데이터
DeviceObjectVersion | 이 특성은 장치의 스키마 버전 확인에 사용됩니다.
DeviceOSType |OS 기준(예: Windows RT, iOS)의 장치 유형입니다. 등록 서비스에 의해 작성되었으며 MDM 관리 서비스 또는 STS light 관리 서비스에 의해 업데이트될 예정이었습니다.
DeviceOSVersion |OS의 버전입니다. 등록 서비스에 의해 작성되었으며 MDM 관리 서비스 또는 STS light 관리 서비스에 의해 업데이트될 예정이었습니다.
DevicePhysicalIds |물리적 장치의 식별자를 저장하기 위한 다중값 특성입니다. BIOS ID, TPM 지문, 하드웨어 특정 ID 등을 포함할 수 있습니다.
DirSyncEnabled | 동기화가 권한이 있는 (고객, 온-프레미스) 디렉터리에서 발생하는지 여부를 나타냅니다.
DisplayName |개체에 대한 표시 이름
IsCompliant | 이 특성은 장치의 모바일 장치 관리 상태를 관리하는 데 사용됩니다.
IsManaged |이 특성은 클라우드 MDM에서 관리되는 장치를 나타내는 데 사용됩니다.
LastDirSyncTime |권한이 있는 (고객, 온-프레미스) 디렉터리의 동기화로 인해 개체가 업데이트된 마지막 시간입니다.

## "장치 구성 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |제거를 위한 것으로 간주되기 전에 장치가 비활성화될 수 있는 최대 일 수입니다.
RegistrationQuota |단일 사용자에 대해 허용되는 장치 등록 수를 제한하는 데 사용되는 정책입니다.

## "서비스 주체 구성 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |보안 주체가 인증할 수 있는지 여부를 나타냅니다.
AppPrincipalId | 보안 주체에 대한 외부, 응용 프로그램 정의 ID입니다.
DisplayName |개체에 대한 표시 이름
ServicePrincipalName | 이름이 응용 프로그램 클래스 값을 지정하고 기관이 서비스 주체에 대한 식별자를 지정하는 하나 이상의 호스트 이름[:포트] 또는 “이름"을 포함하는 “이름/기관"을 포함하는 서비스 주체 이름입니다.

## "앱 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |서비스 주체에 할당된 주소(리디렉션 URL)의 집합입니다.
AppId |앱의 응용 프로그램 ID   
AppIdentifierUri | 응용 프로그램을 식별하는 응용 프로그램 URI입니다. 일반적으로 응용 프로그램 액세스 URL입니다.
AppLogoUrl |CDN에 저장된 응용 프로그램 로고 이미지에 대한 url입니다.
AvailableToOtherTenants | 응용 프로그램이 다중 테넌트 응용 프로그램(예: 다른 테넌트에서 사용할 수 있음)이면 True입니다.
DisplayName | 응용 프로그램 이름에 대한 표시 이름
Entitlement |응용 프로그램 권한 부여의 목록입니다.
ExternalUserAccountDelegationsAllowed |리소스 응용 프로그램이 신뢰할 수 있는 것인지 여부와 외부 사용자 계정에 대한 위임 항목을 만들 수 있는지 여부를 나타내는 플래그입니다.
GroupMembershipClaims |그룹 멤버 자격 클레임 정책입니다.
PublicClient | 클라이언트가 보안을 유지할 수 없는 경우 True입니다(예: OAuth2.0에서 기밀이 아닌 클라이언트).
RecordConsentConditions | 계약 조건: None(0), SilentConsentForPartnerManagedApp(1)에 정의된 대로 승인 조건의 유형입니다. 이 값은 Graph API 스키마에 노출되고 테넌트 관리자에 의해 설정/변경될 수 있습니다.
RequiredResourceAccess |RequiredResourceAccess 속성 값의 XML 콘텐츠입니다.
WebApp |True인 경우 이 응용 프로그램이 웹앱임을 나타냅니다.
WwwHomepage |기본 웹 페이지입니다.

## "역할 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |서비스 주체에 할당된 주소(리디렉션 URL)의 집합입니다.
BelongsToFirstLoginObjectSet |True인 경우 이 개체가 새 테넌트의 첫 번째 관리자의 로그인을 활성화하는 데 필요한 개체 집합에 속함을 나타냅니다.
Builtin |시스템이 개체의 수명을 소유하는지 여부를 나타냅니다.
설명 | 개체에 대한 알기 쉬운 설명이 포함된 구입니다.  
DisplayName |개체에 대한 표시 이름
MailNickname | 주소록 개체에 대한 Moniker입니다. 일반적으로 전자 메일 이름 앞 부분은 "@" 기호입니다.
RoleDisabled |액세스 검사를 위해 역할을 무시할지 여부를 나타냅니다.
RoleTemplateId | 역할 템플릿의 ID입니다.
ServiceInfo |MOAC 및/또는 다른 서비스 인스턴트(동일하거나 다른 서비스 형식의)에서 사용할 수 있는 서비스 관련 프로비전 정보입니다.
TaskSetScopeReference |Role 또는 RoleTemplate과 연결된 TaskSet 및 Scope의 집합을 식별합니다.
ValidationError |해결할 개체 관리자 작업에서 속성 또는 링크와 관련된 일시적이지 않은 서비스 특정 오류를 설명하는 페더레이션된 서비스에 의해 게시된 정보입니다.
WellKnownObject |미리 정의된 집합 중 하나로 지정하여 디렉터리 개체를 표시합니다.

## "역할 정의 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes |이 RoleDefinition을 보안 주체에 할당하는 경우 참조될 수 있는 권한 부여 범위의 컬렉션입니다.
DisplayName |개체에 대한 표시 이름
GrantedPermissions |이 RoleDefinition에 의해 부여된 권한입니다.


## “관리 장치 업데이트” 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
설명 |	이 속성은 관리 장치의 설명을 변경할 때 업데이트됩니다.
DisplayName |	이 속성은 관리 장치의 이름을 변경할 때 업데이트됩니다.

## "회사 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation | 회사의 사용자가 프로비전되도록 허용되는 위치입니다.
AuthorizedServiceInstance | 계획이 배포될 수 있는 서비스 인스턴스의 이름입니다.
DirSyncEnabled |동기화가 권한이 있는 (고객, 온-프레미스) 디렉터리에서 발생하는지 여부를 나타냅니다.
DirSyncStatus |이 테넌트 컨텍스트에서 주소록 개체의 동기화가 권한이 있는 (고객, 온-프레미스) 디렉터리에서 발생하는지 여부를 나타냅니다. Company 개체에 대한 DirSyncEnabled 속성의 확장입니다.
DirSyncFeatures |테넌트에 대한 활성화 및 비활성화된 dirsync 기능 집합을 추적하는 비트 플래그입니다.
DirectoryFeatures |활성화/비활성화 디렉터리 기능입니다.
DirSyncConfiguration |현재 테넌트에 관련된 모든 DirSync 구성을 포함합니다.
DisplayName |개체에 대한 표시 이름
IsMnc |회사가 다국적 회사 기능에 대해 활성화된 경우 부울 플래그는 "true"로 설정됩니다.
ObjectSettings | 개체의 범위에 적용할 수 있는 설정의 컬렉션입니다.
PartnerCommerceUrl |파트너의 상거래 사이트에 대한 URL입니다.
PartnerHelpUrl |파트너의 도움말 사이트에 대한 URL입니다.
PartnerSupportEmail | 파트너의 지원 전자 메일에 대한 URL입니다.
PartnerSupportTelephone |파트너의 지원 전화에 대한 URL입니다.
PartnerSupportUrl |파트너의 지원 사이트에 대한 URL입니다.
StrongAuthenticationDetails |StrongAuthentication과 관련된 세부 정보입니다.
StrongAuthenticationPolicy |회사에 대한 강력한 인증 정책입니다.
TechnicalNotificationMail |회사에 관련된 기술 문제를 알리기 위한 전자 메일 주소입니다.
TelephoneNumber |ITU Recommendation E.123을 준수하는 전화 번호입니다.
TenantType |테넌트의 형식입니다. 이 값이 지정되지 않은 경우 테넌트는 Company입니다. 그렇지 않은 경우 가능한 값은 MicrosoftSupport (0), SyndicatePartner (1), BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5)입니다.
VerifiedDomain |DNS 도메인 이름 집합은 회사에 바인딩됩니다.

## "도메인 업데이트" 특성
특성 | 설명
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Capabilities |있는 경우 도메인의 기능을 설명하는 비트 플래그입니다.
Default |도메인이 기본값인지 여부를 나타냅니다(예: 관리자가 MOAC에 새 사용자를 만들 때 기본 UserPrincipalName 접미사).
Initial |도메인이 OCP에 의해 할당된 회사에 대한 초기 도메인인지 여부를 나타냅니다. 초기 도메인은 Microsoft Online 도메인의 고유한 하위 도메인입니다(예: contoso3.microsoftonline.com).
LiveType |있는 경우 해당 Windows Live 네임스페이스의 형식입니다.
Name | 끝점에 대한 식별자입니다.
PasswordNotificationWindowDays |사용자에게 알리는 암호가 만료되기 전의 일 수입니다.
PasswordValidityPeriodDays | 암호를 변경해야 하기 전에 유효한 일 수입니다.

감사 레코드는 많은 규정 준수 규칙의 필수 컨트롤입니다. Azure Active Directory 감사 보고서를 사용하여 규정 준수 규칙을 충족하려는 고객은 보고서 세부 정보를 설명하는 데 도움이 되도록 내보낸 고객 감사 보고서의 복사본과 함께 이 도움말 항목의 복사본을 제출하는 것이 좋습니다. Azure에서 현재 충족하는 규정 준수 규칙을 알려고 하는 감사자는 직접 Microsoft Azure 보안 센터의 [규정 준수 페이지](https://azure.microsoft.com/support/trust-center/compliance/)로 안내하세요.

<!---HONumber=AcomDC_0921_2016-->