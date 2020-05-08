---
title: 針對 Azure 檔案儲存體啟用透過 SMB 的 Active Directory 驗證
description: 瞭解如何透過 Active Directory，為 Azure 檔案共用啟用以身分識別為基礎的驗證。 接著，已加入網域的 Windows 虛擬機器（Vm）就可以使用 AD 認證來存取 Azure 檔案共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: 6309219b31c22f1f1d090cc9de9931609e3423f7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792970"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>針對 Azure 檔案共用啟用透過 SMB 的內部部署 Active Directory Domain Services 驗證

[Azure 檔案儲存體](storage-files-introduction.md) 可透過兩種類型的網域服務，在伺服器訊息區（SMB）上支援以身分識別為基礎的驗證： Azure Active Directory Domain Services （Azure AD DS）和內部部署 Active Directory Domain Services （AD DS）（預覽）。 本文著重于新引進的（預覽）支援，以利用 Active Directory 網域服務向 Azure 檔案共用進行驗證。 如果您想要為 Azure 檔案共用啟用 Azure AD DS （GA）驗證，請參閱[主題中的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 檔案共用僅支援對一個網域服務進行驗證，不論是 Azure Active Directory 網域服務（Azure AD DS）或內部部署 Active Directory Domain Services （AD DS）。 
>
> 用於 Azure 檔案共用驗證的 AD DS 身分識別必須同步處理至 Azure AD。 密碼雜湊同步處理是選擇性的。 
> 
> 內部部署 AD DS 驗證不支援對 AD DS 中建立的電腦帳戶進行驗證。 
> 
> 只有一個 AD 樹系會向其註冊儲存體帳戶，才能支援內部部署 AD DS 驗證。 根據預設，您只能使用來自單一樹系的 AD DS 認證來存取 Azure 檔案共用。 如果您需要從不同的樹系存取 Azure 檔案共用，請確定您已設定適當的樹系信任，請參閱[常見問題](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)以取得詳細資料。  
> 
> Azure 檔案同步所管理的 Azure 檔案共用支援 SMB 存取和 ACL 持續性的 AD DS 驗證。
>
> Azure 檔案儲存體支援使用具有 RC4-HMAC 加密的 AD 進行 Kerberos 驗證。 尚不支援 AES Kerberos 加密。

當您啟用透過 SMB 的 Azure 檔案共用 AD DS 時，已加入 AD DS 的電腦可以使用現有的 AD 認證來掛接 Azure 檔案共用。 這項功能可以使用裝載于內部內部部署電腦或裝載于 Azure 中的 AD DS 環境來啟用。

用來存取 Azure 檔案共用的身分識別必須同步至 Azure AD，才能透過[角色型存取控制（RBAC）](../../role-based-access-control/overview.md)模型來強制執行共用層級檔案許可權。 在從現有檔案伺服器上執行的檔案/目錄上， [Windows 樣式的 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN)將會保留並強制執行。 這項功能可與您的企業 AD DS 環境緊密整合。 當您使用 Azure 檔案共用來取代內部部署檔案伺服器時，現有的使用者可以透過單一登入體驗，從其目前的用戶端存取 Azure 檔案共用，而不需要變更使用中的認證。  

> [!NOTE]
> 為了協助您為一些常見的使用案例設定 Azure 檔案儲存體 AD 驗證，我們發佈[兩個](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos)影片與逐步指導方針：
> - 以 Azure 檔案儲存體取代內部部署檔案伺服器（包括檔案和 AD 驗證的私人連結上的安裝程式）
> - 使用 Azure 檔案儲存體做為 Windows 虛擬桌面的設定檔容器（包括 AD 驗證和 FsLogix 設定上的安裝程式）

## <a name="prerequisites"></a>Prerequisites 

為 Azure 檔案共用啟用 AD DS 驗證之前，請確定您已完成下列必要條件： 

- 選取或建立您的 AD DS 環境，並[將其同步至 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    您可以在新的或現有的內部部署 AD DS 環境上啟用此功能。 用於存取的身分識別必須同步至 Azure AD。 Azure AD 租使用者和您要存取的檔案共用必須與相同的訂用帳戶相關聯。 

    若要設定 AD 網域環境，請參閱[Active Directory Domain Services 總覽](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果您尚未將 AD 同步至您的 Azure AD，請遵循[Azure AD Connect 和 Azure AD Connect Health 安裝藍圖](../../active-directory/hybrid/how-to-connect-install-roadmap.md)中的指引來設定和設定 Azure AD Connect。 

- 網域-將內部部署機器或 Azure VM 加入內部部署 AD DS。 

    若要從電腦或 VM 使用 AD 認證來存取檔案共用，您的裝置必須已加入網域，才能 AD DS。 如需如何加入網域的相關資訊，請參閱將[電腦加入網域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 選取或建立 Azure 儲存體帳戶。  為了達到最佳效能，我們建議您將儲存體帳戶部署在您打算用來存取共用的 VM 所在的相同區域中。

    請確定尚未設定包含檔案共用的儲存體帳戶進行 Azure AD DS 驗證。 如果 Azure 檔案儲存體在儲存體帳戶上啟用 Azure AD DS 驗證，則必須先將其停用，才能變更為使用內部部署 AD DS。 這表示在 Azure AD DS 環境中設定的現有 Acl 必須重新設定，才能強制執行適當的許可權。
    
    如需建立新檔案共用的相關資訊，請參閱 [在 Azure 檔案儲存體中建立檔案共用](storage-how-to-create-file-share.md)。

- 使用您的儲存體帳戶金鑰來掛接 Azure 檔案共用，以驗證連線能力。 

    若要確認已正確設定您的裝置和檔案共用，請嘗試使用您的儲存體帳戶金鑰來掛接檔案[共用](storage-how-to-use-files-windows.md)。 如果您在連接到 Azure 檔案儲存體時遇到問題，請參閱[我們針對 Windows 上的 Azure 檔案儲存體掛接錯誤所發行的疑難排解工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我們也會提供[指引](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)，以解決埠445遭到封鎖的案例。 

## <a name="regional-availability"></a>區域可用性

[所有公用區域和 Azure Gov 區域](https://azure.microsoft.com/global-infrastructure/locations/)都提供使用 AD DS 的 Azure 檔案儲存體驗證（預覽）。

## <a name="overview"></a>總覽

如果您打算在檔案共用上啟用任何網路設定，建議您先評估[網路考慮](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)，並先完成相關設定，再啟用 AD DS 驗證。

針對您的 Azure 檔案共用啟用 AD DS 驗證，可讓您使用內部內部部署 AD DS 認證向 Azure 檔案共用進行驗證。 此外，它可讓您更有效地管理您的許可權，以允許細微的存取控制。 若要這麼做，您必須將內部內部部署 AD DS 的身分識別，與 AD connect Azure AD 同步處理。 您可以使用同步處理到 Azure AD 的身分識別來控制共用層級存取，同時使用內部內部部署 AD DS 認證來管理檔案/共用層級存取。

接下來，請遵循下列步驟來設定 AD DS Authentication 的 Azure 檔案儲存體： 

1. [在您的儲存體帳戶上啟用 Azure 檔案儲存體 AD DS 驗證](#1-enable-ad-ds-authentication-for-your-account)

1. [將共用的存取權限指派給與目標 AD 身分識別同步的 Azure AD 身分識別（使用者、群組或服務主體）](#2-assign-access-permissions-to-an-identity)

1. [透過 SMB 設定目錄和檔案的 Acl](#3-configure-ntfs-permissions-over-smb)
 
1. [將 Azure 檔案共用掛接至已加入您的 AD DS 的 VM](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [在 AD DS 中更新儲存體帳戶身分識別的密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

下圖說明針對 Azure 檔案共用啟用透過 SMB 進行 Azure AD 驗證的端對端工作流程。 

![檔案 AD workflow 圖表](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> 只有在比 Windows 7 或 Windows Server 2008 R2 更新的作業系統版本上執行的電腦或 Vm，才支援透過 SMB 對 Azure 檔案共用進行 AD DS 驗證。 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1為您的帳戶啟用 AD DS 驗證 

若要針對 Azure 檔案共用啟用透過 SMB 進行 AD DS 驗證，您必須先向 AD DS 註冊您的儲存體帳戶，然後在儲存體帳戶上設定所需的網域屬性。 在儲存體帳戶上啟用此功能時，它會套用至帳戶中所有新的和現有的檔案共用。 下載 AzFilesHybrid Powershell 模組，並使用`join-AzStorageAccountForAuth`來啟用此功能。 您可以在本節內的腳本中找到端對端工作流程的詳細描述。 

> [!IMPORTANT]
> Cmdlet `Join-AzStorageAccountForAuth`會對您的 AD 環境進行修改。 閱讀下列說明，以進一步瞭解其用途，以確保您擁有執行命令的適當許可權，而且套用的變更會符合合規性和安全性原則。 

此`Join-AzStorageAccountForAuth` Cmdlet 會代表指定的儲存體帳戶執行離線網域聯結的對應項。 此腳本會使用指令程式，在您的 AD 網域中建立帳戶，可能是[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（預設）或[服務登入帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 如果您選擇手動執行此動作，您應該選取最適合您環境的帳戶。

此 Cmdlet 所建立的 AD DS 帳戶代表 AD 網域中的儲存體帳戶。 如果 AD DS 帳戶是在強制執行密碼到期的組織單位（OU）下建立，您必須在密碼最長使用期限之前更新密碼。 當存取 Azure 檔案共用時，無法更新帳戶密碼會導致驗證失敗。 若要瞭解如何更新密碼，請參閱[更新 AD DS 帳戶密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

您可以使用下列腳本來執行註冊並啟用功能; 或者，您也可以手動執行腳本所需的作業。 這些作業會在腳本後的一節中說明。 您不需要這麼做。

### <a name="11-script-prerequisites"></a>1.1 腳本必要條件
- [下載並解壓縮 AzFilesHybrid 模組](https://github.com/Azure-Samples/azure-files-samples/releases)
- 在已加入內部部署 AD DS 的裝置中安裝並執行模組，其 AD DS 認證具有在目標 AD 中建立服務登入帳戶或電腦帳戶的許可權。
-  使用同步處理至您 Azure AD 的內部部署 AD DS 認證來執行腳本。 內部部署 AD DS 認證必須具有儲存體帳戶擁有者或參與者 RBAC 角色許可權。
- 請確定您的儲存體帳戶在[支援的區域](#regional-availability)中。

### <a name="12-domain-join-your-storage-account"></a>1.2 網域加入您的儲存體帳戶
在 PowerShell 中執行之前，請記得使用您自己的參數來取代預留位置值。
> [!IMPORTANT]
> 網域加入 Cmdlet 會建立 AD 帳戶來代表 AD 中的儲存體帳戶（檔案共用）。 您可以選擇將註冊為電腦帳戶或服務登入帳戶，請參閱[常見問題](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)以取得詳細資料。 針對電腦帳戶，AD 中的預設密碼到期時間設定為30天。 同樣地，服務登入帳戶可能會在 AD 網域或組織單位（OU）上設定預設密碼到期時間。
> 對於這兩種帳戶類型，我們強烈建議您檢查 AD 環境中設定的密碼到期期限，並規劃在密碼最長使用期限之前，在 ad 帳戶的[ad 中更新儲存體帳戶身分識別的密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。 您可以考慮[在 AD 中建立新的 Ad 組織單位（OU）](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) ，並據以停用[電腦帳戶](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)或服務登入帳戶的密碼到期原則。 

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
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` # Default set to "ComputerAccount" if this parameter is not provided
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

下列描述摘要說明執行 Cmdlet 時所執行`Join-AzStorageAccountForAuth`的所有動作。 如果您不想要使用命令，您可以手動執行這些步驟：

> [!NOTE]
> 如果您已成功執行上述`Join-AzStorageAccountForAuth`腳本，請移至下一節「1.3 確認功能已啟用」。 您不需要再次執行下列作業。

#### <a name="a-checking-environment"></a>a. 檢查環境

首先，腳本會檢查您的環境。 具體來說，它會檢查是否已安裝[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) ，以及是否使用系統管理員許可權來執行命令介面。 然後，它會檢查是否已安裝[Az 1.11.1-preview 模組](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)，如果不是，則會進行安裝。 如果這些檢查通過，則會檢查您的 AD DS，以查看是否有已使用 SPN/UPN 建立的[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（預設）或[服務登入帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)，做為「cifs/您的儲存體-帳戶-名稱-在這裡. net.tcp」。 如果帳戶不存在，它會建立一個，如下列第 b 節所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 以手動方式建立代表 AD 中儲存體帳戶的身分識別

若要手動建立此帳戶，請使用`New-AzStorageAccountKey -KeyName kerb1`為您的儲存體帳戶建立新的 Kerberos 金鑰。 然後，使用該 Kerberos 金鑰做為帳戶的密碼。 此金鑰只會在設定期間使用，而且不能用於對儲存體帳戶進行任何控制或資料平面作業。

擁有該金鑰後，請在您的 OU 底下建立服務或電腦帳戶。 請使用下列規格： SPN： "cifs/您的儲存體-帳戶名稱-在此輸入您儲存體帳戶的 Kerberos 金鑰。

如果您的 OU 強制執行密碼逾期，您必須在密碼最長使用期限前更新密碼，以避免在存取 Azure 檔案共用時發生驗證失敗。 如需詳細資訊，請參閱[AD DS 儲存體帳戶身分識別的密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

保留新建立之帳戶的 SID，在下一個步驟中需要用到它。 您建立用來代表儲存體帳戶的身分識別不需要同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在您的儲存體帳戶上啟用此功能

接著，腳本會在您的儲存體帳戶上啟用此功能。 若要手動執行此安裝，請在下列命令中提供網域屬性的一些設定詳細資料，然後執行它。 下列命令所需的儲存體帳戶 SID，是您在[上一節](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)的 AD DS 中建立的身分識別 sid。

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 確認功能已啟用

您可以檢查以確認您的儲存體帳戶是否已啟用此功能，您可以使用下列腳本：

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

您現在已成功在您的儲存體帳戶上啟用此功能。 現在已啟用此功能，您必須執行更多步驟，才能使用此功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

您現在已成功啟用透過 SMB 的 AD DS 驗證，並已指派自訂角色，可讓您存取具有 AD DS 身分識別的 Azure 檔案共用。 若要將檔案共用的存取權授與其他使用者，請遵循[指派存取權限](#2-assign-access-permissions-to-an-identity)以使用身分識別和透過[SMB 設定 NTFS 許可權](#3-configure-ntfs-permissions-over-smb)一節中的指示。

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5在 AD DS 中更新儲存體帳戶身分識別的密碼

如果您已在強制執行密碼到期時間的 OU 下，註冊代表儲存體帳戶的 AD DS 身分識別/帳戶，您必須在密碼最長使用期限前輪替密碼。 無法更新 AD DS 帳戶的密碼會導致驗證失敗，因此無法存取 Azure 檔案共用。  

若要觸發密碼輪替，您可以從`Update-AzStorageAccountADObjectPassword` AzFilesHybrid 模組執行命令。 此 Cmdlet 會執行類似于儲存體帳戶金鑰輪替的動作。 它會取得儲存體帳戶的第二個 Kerberos 金鑰，並使用它來更新 AD DS 中已註冊之帳戶的密碼。 然後，它會重新產生儲存體帳戶的目標 Kerberos 金鑰，並在 AD DS 中更新已註冊帳戶的密碼。 您必須在已加入內部部署 AD DS 網域的環境中執行此 Cmdlet。

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>後續步驟

如需有關 Azure 檔案儲存體以及如何透過 SMB 使用 AD 的詳細資訊，請參閱下列資源：

- [瞭解 SMB 存取的 Azure 檔案儲存體身分識別型驗證支援](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
