---
title: 啟用對 Azure 檔案共用的 AD DS 驗證
description: 瞭解如何針對 Azure 檔案共用啟用透過 SMB 進行 Active Directory Domain Services 驗證。 接著，已加入網域的 Windows 虛擬機器便可使用 AD DS 認證來存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 4c374e62c0807269d1457bfe46d3df4260acd45c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510458"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>第一部：為您的 Azure 檔案共用啟用 AD DS 驗證 

啟用 Active Directory Domain Services （AD DS）驗證之前，請確定您已閱讀總覽一[文](storage-files-identity-auth-active-directory-enable.md)，以瞭解支援的案例和需求。

本文說明在您的儲存體帳戶上啟用 Active Directory Domain Services （AD DS）驗證所需的程式。 啟用此功能之後，您必須設定儲存體帳戶和 AD DS，才能使用 AD DS 認證來向您的 Azure 檔案共用進行驗證。 若要針對 Azure 檔案共用啟用透過 SMB 進行 AD DS 驗證，您需要向 AD DS 註冊您的儲存體帳戶，然後在儲存體帳戶上設定所需的網域屬性。

若要向 AD DS 註冊您的儲存體帳戶，請在您的 AD DS 中建立代表該帳戶的帳戶。 您可以將這個程式想成，如同建立一個代表您 AD DS 中內部部署 Windows 檔案伺服器的帳戶一樣。 在儲存體帳戶上啟用此功能時，它會套用至帳戶中所有新的和現有的檔案共用。

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>選項1（建議）：使用 AzFilesHybrid PowerShell 模組

AzFilesHybrid PowerShell 模組中的 Cmdlet 會進行必要的修改，並為您啟用此功能。 因為 Cmdlet 的某些部分會與您的內部部署 AD DS 互動，所以我們會說明 Cmdlet 的功能，讓您可以判斷變更是否符合您的合規性和安全性原則，並確保您擁有執行 Cmdlet 的適當許可權。 雖然我們建議使用 AzFilesHybrid 模組，但如果您無法這麼做，我們會提供步驟，讓您可以手動執行。

### <a name="download-azfileshybrid-module"></a>下載 AzFilesHybrid 模組

- [下載並解壓縮 AzFilesHybrid 模組](https://github.com/Azure-Samples/azure-files-samples/releases)（GA 模組： v 0.2.0 +）
- 在已加入內部部署 AD DS 的裝置中安裝並執行模組，其 AD DS 認證具有在目標 AD 中建立服務登入帳戶或電腦帳戶的許可權。
-  使用同步處理至您 Azure AD 的內部部署 AD DS 認證來執行腳本。 內部部署 AD DS 認證必須具有儲存體帳戶擁有者或參與者 RBAC 角色許可權。

### <a name="run-join-azstorageaccountforauth"></a>執行聯結-AzStorageAccountForAuth

此 `Join-AzStorageAccountForAuth` Cmdlet 會代表指定的儲存體帳戶執行離線網域聯結的對應項。 此腳本會使用 Cmdlet，在您的 AD 網域中建立[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)。 如果您因為任何原因而無法使用電腦帳戶，則可以改為變更腳本來建立[服務登入帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 如果您選擇手動執行命令，您應該選取最適合您環境的帳戶。

Cmdlet 所建立的 AD DS 帳戶代表儲存體帳戶。 如果 AD DS 帳戶是在強制執行密碼到期的組織單位（OU）下建立，您必須在密碼最長使用期限之前更新密碼。 無法在該日期之前更新帳戶密碼，會導致存取 Azure 檔案共用時發生驗證失敗。 若要瞭解如何更新密碼，請參閱[更新 AD DS 帳戶密碼](storage-files-identity-ad-ds-update-password.md)。

在 PowerShell 中執行之前，請使用您自己的參數取代預留位置值。
> [!IMPORTANT]
> 網域加入 Cmdlet 會建立 AD 帳戶來代表 AD 中的儲存體帳戶（檔案共用）。 您可以選擇將註冊為電腦帳戶或服務登入帳戶，請參閱[常見問題](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)以取得詳細資料。 針對電腦帳戶，AD 中的預設密碼到期時間設定為30天。 同樣地，服務登入帳戶可能會在 AD 網域或組織單位（OU）上設定預設密碼到期時間。
> 針對這兩種帳戶類型，建議您檢查 AD 環境中所設定的密碼到期期限，並在密碼最長使用期限之前，規劃更新 AD 帳戶的[儲存體帳戶身分識別密碼](storage-files-identity-ad-ds-update-password.md)。 您可以考慮[在 AD 中建立新的 Ad 組織單位（OU）](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) ，並據以停用[電腦帳戶](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)或服務登入帳戶的密碼到期原則。 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" # If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>選項2：手動執行啟用動作

如果您已 `Join-AzStorageAccountForAuth` 成功執行上述腳本，請移至[確認功能已啟用](#confirm-the-feature-is-enabled)一節。 您不需要執行下列手動步驟。

### <a name="checking-environment"></a>檢查環境

首先，您必須檢查環境的狀態。 具體來說，您必須檢查是否已安裝[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) ，以及是否使用系統管理員許可權來執行命令介面。 然後檢查是否已安裝[Az. Storage 2.0 模組](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)，如果不是，請安裝它。 完成這些檢查之後，請檢查您的 AD DS，查看是否有已使用 SPN/UPN 建立的[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（預設）或[服務登入帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)，做為「cifs/您的儲存體-帳戶-名稱-在這裡。檔案. .net」。 如果帳戶不存在，請依照下一節所述建立一個。

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>以手動方式建立代表 AD 中儲存體帳戶的身分識別

若要手動建立此帳戶，請為您的儲存體帳戶建立新的 Kerberos 金鑰。 然後，使用 Kerberos 金鑰作為您帳戶的密碼，並使用下列 PowerShell Cmdlet。 此金鑰只會在安裝期間使用，而且不能用於對儲存體帳戶進行任何控制或資料平面作業。 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

擁有該金鑰後，請在您的 OU 底下建立服務或電腦帳戶。 使用下列規格（請務必將範例文字取代為您的儲存體帳戶名稱）：

SPN：「cifs/您的儲存體-帳戶-名稱-在此為您的儲存體帳戶的 Kerberos 金鑰。

如果您的 OU 強制執行密碼逾期，您必須在密碼最長使用期限前更新密碼，以避免在存取 Azure 檔案共用時發生驗證失敗。 如需詳細資訊，請參閱[在 AD 中更新儲存體帳戶身分識別的密碼](storage-files-identity-ad-ds-update-password.md)。

保留新建立之身分識別的 SID，在下一個步驟中需要用到它。 您建立用來代表儲存體帳戶的身分識別不需要同步到 Azure AD。

### <a name="enable-the-feature-on-your-storage-account"></a>在您的儲存體帳戶上啟用此功能

現在您可以在儲存體帳戶上啟用此功能。 在下列命令中提供網域屬性的一些設定詳細資料，然後執行它。 下列命令所需的儲存體帳戶 SID，是您在[上一節](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)的 AD DS 中建立的身分識別 sid。

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>偵錯

您可以執行 AzStorageAccountAuth Cmdlet，在 AD 設定上執行一組已登入 AD 使用者的基本檢查。 AzFilesHybrid v0.1.2+ 版本支援此 Cmdlet。 如需在此 Cmdlet 中執行之檢查的詳細資訊，請參閱 Windows 的疑難排解指南中的[無法以 AD 認證掛接 Azure 檔案儲存體](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)。

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>確認已啟用此功能

您可以使用下列腳本，檢查以確認您的儲存體帳戶是否已啟用此功能：

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>後續步驟

您現在已成功在您的儲存體帳戶上啟用此功能。 若要使用此功能，您必須指派共用層級許可權。 繼續進行下一節。

[第二部分：將共用層級許可權指派給身分識別](storage-files-identity-ad-ds-assign-permissions.md)
