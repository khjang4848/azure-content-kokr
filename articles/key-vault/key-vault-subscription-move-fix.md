<properties
	pageTitle="구독 이동 후에 주요 자격 증명 모음 테넌트 ID 변경 | Microsoft Azure"
	description="다른 테넌트에 구독을 이동한 후에 주요 자격 증명 모음에 대한 테넌트 ID를 전환하는 방법을 알아봅니다."
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# 구독 이동 후에 주요 자격 증명 모음 테넌트 ID 변경
### Q: 내 구독이 테넌트 A에서 테넌트 B로 이동했습니다. 내 기존 주요 자격 증명 모음에 대한 테넌트 ID를 변경하고 테넌트 B에서 주체에 대한 올바른 ACL을 설정하려면 어떻게 해야 하나요?

구독에 새 주요 자격 증명 모음을 만들 때 해당 구독에 대한 기본 Azure Active Directory 테넌트 ID에 자동으로 연결됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. Azure 구독을 테넌트 A에서 테넌트 B로 이동할 때 기존 주요 자격 증명 모음에는 테넌트 B의 주체(사용자 및 응용 프로그램)가 액세스할 수 없게 됩니다. 이 문제를 해결하려면 다음을 수행합니다.

- 이 구독에 있는 모든 기존 주요 자격 증명 모음과 연결된 테넌트 ID를 테넌트 B로 변경합니다.
- 모든 기존 액세스 정책 항목을 제거합니다.
- 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

예를 들어 이 구독에서 주요 자격 증명 모음 'myvault'가 테넌트 A에서 테넌트 B로 이동된 경우 이 주요 자격 증명 모음에 대한 테넌트 ID를 변경하고 오래된 액세스 정책을 제거하는 방법은 다음과 같습니다.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

이동 전에 이 자격 증명 모음이 테넌트 A였기 때문에 **(Get-AzureRmContext).Tenant.TenantId**가 테넌트 B인 반면 **$vault.Properties.TenantId**의 원래 값이 테넌트 A입니다.

이제 자격 증명 모음이 올바른 테넌트 ID와 연결되고 오래된 액세스 정책 항목이 삭제되므로 새 액세스 정책 항목을 [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx)로 설정합니다.

## 다음 단계

- Key Vault에 대한 질문이 있으면 [Azure Key Vault 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)을 방문하세요.

<!---HONumber=AcomDC_0921_2016-->