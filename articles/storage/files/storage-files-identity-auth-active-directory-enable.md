---
title: 通過 SMB 為 Azure 檔啟用活動目錄身份驗證
description: 瞭解如何通過活動目錄通過 SMB 為 Azure 檔共用啟用基於身份的身份驗證。 然後，加入域的 Windows 虛擬機器 （VM） 可以使用 AD 憑據訪問 Azure 檔共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: rogarana
ms.openlocfilehash: deead728812a34c6f432f59666cd22ba79f5409e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281284"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>通過 SMB 為 Azure 檔共用啟用活動目錄身份驗證

[Azure 檔](storage-files-introduction.md) 通過兩種類型的域服務支援通過伺服器訊息區 （SMB） 進行基於身份的身份驗證：Azure 活動目錄域服務 （Azure AD DS） （GA） 和活動目錄 （AD） （預覽）。 本文重點介紹新引入的（預覽）支援利用 Active 目錄域服務對 Azure 檔共用進行身份驗證。 如果您有興趣為 Azure 檔共用啟用 Azure AD DS （GA） 身份驗證，請參閱[我們有關主題的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 檔僅共用支援針對一個域服務的身份驗證，即 Azure 活動目錄域服務 （Azure AD DS） 或活動目錄 （AD）。 
>
> 用於 Azure 檔共用身份驗證的 AD 標識必須同步到 Azure AD。 密碼雜湊同步是可選的。 
> 
> AD 身份驗證不支援對 AD 中創建的電腦帳戶進行身份驗證。 
> 
> AD 身份驗證只能針對存儲帳戶註冊的一個 AD 林進行支援。 預設情況下，您只能從單個 AD 林中訪問具有 AD 憑據的 Azure 檔共用。 如果需要從其他林訪問 Azure 檔共用，請確保配置了正確的林信任，有關詳細資訊，請參閱[常見問題解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。  
> 
> 對於 Azure 檔同步管理的 Azure 檔共用，支援用於 SMB 訪問的 AD 身份驗證和 ACL 持久性。
>
> Azure 檔支援使用 RC4-HMAC 加密的 AD 進行 Kerberos 身份驗證。 AES Kerberos 加密尚不受支援。

通過 SMB 為 Azure 檔共用啟用 AD 時，AD 域聯接的電腦可以使用現有的 AD 憑據裝載 Azure 檔共用。 此功能可以通過託管在預處理電腦或 Azure 中託管的 AD 環境啟用。

用於訪問 Azure 檔共用的 AD 標識必須同步到 Azure AD，以便通過基於角色的標準[存取控制 （RBAC）](../../role-based-access-control/overview.md)模型強制實施共用級別檔許可權。 將保留並強制執行從現有檔案伺服器結轉的檔/目錄上的[Windows 樣式 DACL。](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 此功能提供與企業 AD 域基礎架構的無縫集成。 當您使用 Azure 檔共用替換上置前檔案伺服器時，現有使用者可以通過單個登錄體驗從其當前用戶端存取 Azure 檔共用，而無需對正在使用的憑據進行任何更改。  
 
## <a name="prerequisites"></a>Prerequisites 

在為 Azure 檔共用啟用 AD 身份驗證之前，請確保已完成以下先決條件： 

- 選擇或創建 AD 環境並將其同步到 Azure AD。 

    您可以在新的或現有的 AD 環境中啟用該功能。 用於訪問的身份必須同步到 Azure AD。 Azure AD 租戶和要訪問的檔共用必須與同一訂閱關聯。 

    要設置 AD 域環境，請參閱[活動目錄域服務概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果 AD 尚未將 AD 同步到 Azure AD，請按照[Azure 活動目錄的混合標識指南進行操作](../../active-directory/hybrid/whatis-hybrid-identity.md)，以確定首選身份驗證方法和 Azure AD 連接設置選項。 

- 域將本地電腦或 Azure VM 連接到 AD（也稱為 AD DS）。 

    要使用電腦或 VM 中的 AD 憑據訪問檔共用，您的設備必須加入到 AD 的域。 有關如何加入 AD 的域的資訊，請參閱[將電腦加入域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 在[受支援的區域](#regional-availability)中選擇或創建 Azure 存儲帳戶。 

    確保包含檔共用的存儲帳戶尚未配置為 Azure AD DS 身份驗證。 如果在存儲帳戶上啟用 Azure 檔 Azure AD DS 身份驗證，則需要在更改為使用 AD 之前禁用該身份驗證。 這意味著需要在 Azure AD DS 環境中配置的現有 ACL 進行重新配置，以便進行適當的許可權實施。
    
    有關創建新檔共用的資訊，請參閱 [在 Azure 檔 中創建檔共用](storage-how-to-create-file-share.md)。
    
    為了獲得最佳性能，我們建議您將存儲帳戶部署在同一區域中，以便從哪個 VM 訪問共用。 

- 通過使用存儲帳戶金鑰安裝 Azure 檔共用來驗證連接。 

    要驗證您的設備和檔共用是否已正確配置，請嘗試使用存儲帳戶金鑰安裝檔共用。 有關詳細資訊，請參閱使用 [Windows 的 Azure 檔共用](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>區域可用性

Azure 檔 AD 身份驗證（預覽）[在大多數公共區域](https://azure.microsoft.com/global-infrastructure/regions/)都可用。

Azure 檔 AD 身份驗證不在：
- 美國西部
- 美國西部 2
- 美國東部
- 美國東部 2
- 西歐
- 北歐

## <a name="workflow-overview"></a>工作流程概觀

在為 Azure 檔共用啟用通過 SMB 進行 AD 身份驗證之前，我們建議您演練 [先決條件](#prerequisites) 並確保已完成所有步驟。 先決條件驗證 AD、Azure AD 和 Azure 存儲環境是否已正確配置。 

接下來，使用 AD 憑據授予對 Azure 檔資源的許可權： 

- 在存儲帳戶上啟用 Azure 檔 AD 身份驗證。  

- 將共用的存取權限分配給與目標 AD 標識同步的 Azure AD 標識（使用者、組或服務主體）。 

- 通過 SMB 配置目錄和檔的 ACL。 

- 從 AD 域聯接的 VM 裝載 Azure 檔共用。 

下圖說明瞭用於通過 SMB 為 Azure 檔共用啟用 Azure AD 身份驗證的端到端工作流。 

![檔 AD 工作流圖](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 檔共用的 SMB 上的 AD 身份驗證僅在運行在比 Windows 7 或 Windows Server 2008 R2 更新的作業系統版本的電腦上或 VM 上支援。 

## <a name="enable-ad-authentication-for-your-account"></a>為您的帳戶啟用 AD 身份驗證 

要通過 SMB 對 Azure 檔共用啟用 AD 身份驗證，需要先向 AD 註冊存儲帳戶，然後在存儲帳戶上設置所需的域屬性。 在存儲帳戶上啟用該功能時，它適用于帳戶中的所有新檔和現有檔共用。 用於`join-AzStorageAccountForAuth`啟用該功能。 您可以在下面的部分中找到端到端工作流的詳細說明。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` Cmdlet 將修改您的 AD 環境。 閱讀以下說明，以更好地瞭解它正在採取哪些措施來確保您具有執行命令的適當許可權，並確保應用的更改與合規性和安全性原則保持一致。 

Cmdlet`Join-AzStorageAccountForAuth`將代表指示的存儲帳戶執行等效的離線域聯接。 它將在 AD 域中創建一個帳戶，即[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)或服務[登錄帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 創建的 AD 帳戶表示 AD 域中的存儲帳戶。 如果 AD 帳戶是在強制密碼過期的 AD 組織單位 （OU） 下創建的，則必須在最大密碼期限之前更新密碼。 訪問 Azure 檔共用時，無法更新 AD 帳戶密碼將導致身份驗證失敗。 要瞭解如何更新密碼，請參閱[更新 AD 帳戶密碼](#update-ad-account-password)。

您可以使用以下腳本執行註冊並啟用該功能，也可以手動執行腳本將執行的操作。 這些操作在腳本之後的部分仲介紹。 您不需要同時執行這兩個操作。

### <a name="1-check-prerequisites"></a>1. 檢查先決條件
- [下載並解壓縮 AzFiles 混合模組](https://github.com/Azure-Samples/azure-files-samples/releases)
- 在具有 AD 憑據的域加入 AD 的設備中安裝並執行模組，該憑據具有在目標 AD 中創建服務登錄帳戶或電腦帳戶的許可權。
-  使用同步到 Azure AD 的 AD 憑據運行腳本。 AD 憑據必須具有存儲帳戶擁有者或參與者 RBAC 角色許可權。
- 請確保您的存儲帳戶位於[受支援的區域](#regional-availability)。

### <a name="2-domain-join-your-storage-account"></a>2. 功能變數名稱加入您的存儲帳戶
在 PowerShell 中執行預留位置之前，請記住在下面的參數中將預留位置值替換為您自己的預留位置值。

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

以下說明總結了執行`Join-AzStorageAccountForAuth`Cmdlet 時執行的所有操作。 如果您不希望使用以下命令，則可以手動執行這些步驟：

> [!NOTE]
> 如果您已成功執行上述`Join-AzStorageAccountForAuth`腳本，請訪問下一節"3"。 確認功能已啟用"。 您無需再次執行以下操作。

#### <a name="a-checking-environment"></a>a. 檢查環境

首先，它會檢查您的環境。 具體來說，它檢查是否安裝了[Active Directory PowerShell，](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps)以及是否使用管理員許可權執行 shell。 然後，它會檢查是否安裝了[Az.Storage 1.11.1 預覽模組](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)，如果沒有安裝該模組，則安裝該模組。 如果這些檢查通過，它將檢查您的 AD，以查看是否有電腦[帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（預設）[或服務登錄帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)已創建 SPN/UPN 為"cifs/您的存儲帳戶名稱-此處.file.core.windows.net"。 如果帳戶不存在，它將創建一個，如下 b 節所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 手動創建表示 AD 中的存儲帳戶的標識

要手動創建此帳戶，請使用`New-AzStorageAccountKey -KeyName kerb1`創建存儲帳戶的新 kerberos 金鑰。 然後，使用該 kerberos 金鑰作為帳戶的密碼。 此金鑰僅在設置期間使用，不能用於針對存儲帳戶的任何控制或資料平面操作。

擁有該金鑰後，在 OU 下創建服務或電腦帳戶。 使用以下規範：SPN："cIFs/您的存儲帳戶名稱-此處.file.core.windows.net"密碼：存儲帳戶的 Kerberos 金鑰。

如果 OU 強制密碼過期，則必須在最大密碼期限之前更新密碼，以防止在訪問 Azure 檔共用時出現身份驗證失敗。 有關詳細資訊，請參閱[更新 AD 帳戶密碼](#update-ad-account-password)。

保留新創建的帳戶的 SID，下一步將需要它。 您剛剛創建的表示存儲帳戶的 AD 標識不需要同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在存儲帳戶上啟用該功能

然後，該腳本將在存儲帳戶上啟用該功能。 要手動執行此設置，請提供以下命令中的域屬性的一些配置詳細資訊，然後運行它。 以下命令中所需的存儲帳戶 SID 是您在 AD 中創建的標識的 SID（上面的 b 節）。

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. 確認功能已啟用

您可以檢查以確認該功能是否已在存儲帳戶上啟用，您可以使用以下腳本：

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

您現在已成功在存儲帳戶上啟用該功能。 即使啟用了該功能，您仍需要完成其他操作才能正確使用該功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

現在，您已成功通過 SMB 啟用 AD 身份驗證，並分配了一個自訂角色，該角色提供對具有 AD 標識的 Azure 檔共用的訪問。 要授予其他使用者對檔共用的存取權限，請按照["分配存取權限"](#assign-access-permissions-to-an-identity)中的說明使用標識並在[SMB 部分上配置 NTFS 許可權](#configure-ntfs-permissions-over-smb)。

## <a name="update-ad-account-password"></a>更新 AD 帳戶密碼

如果您在強制密碼過期時間的 OU 下註冊了表示存儲帳戶的 AD 標識/帳戶，則必須在最大密碼期限之前輪換密碼。 無法更新 AD 帳戶的密碼將導致身份驗證無法訪問 Azure 檔共用。  

要觸發密碼輪換，可以從 AzFilesHybrid 模組運行`Update-AzStorageAccountADObjectPassword`該命令。 Cmdlet 執行類似于存儲帳戶金鑰輪換的操作。 它獲取存儲帳戶的第二個 Kerberos 金鑰，並用它來更新 AD 中註冊帳戶的密碼。 然後，它重新生成存儲帳戶的目標 Kerberos 金鑰，並更新 AD 中註冊帳戶的密碼。 您必須在 AD 域聯接的環境中運行此 Cmdlet。

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>後續步驟

有關 Azure 檔以及如何在 SMB 上使用 AD 的詳細資訊，請參閱以下資源：

- [基於 Azure 檔的基於身份的 SMB 訪問的身份驗證支援的概述](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
