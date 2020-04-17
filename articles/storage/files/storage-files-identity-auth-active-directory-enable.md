---
title: 以 SMB 為 Azure 檔案啟用活動目錄身分驗證
description: 瞭解如何透過活動目錄透過 SMB 為 Azure 檔共享啟用基於身份的身份驗證。 然後,加入網域的 Windows 虛擬機器 (VM) 可以使用 AD 認證訪問 Azure 檔案共用。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 8d1e1262c592f0120b191e18a5c16b97b887a6a2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536520"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>以 SMB 為 Azure 檔案分享啟用本地端目錄網域服務身份驗證

[Azure 檔案](storage-files-introduction.md) 透過兩種類型的網域服務支援透過伺服器訊息區塊 (SMB) 進行基於身份的身份驗證:Azure 活動目錄域服務 (Azure AD DS) 和本地活動目錄域服務 (AD DS)(預覽)。 本文重點介紹新引入的(預覽)支援利用 Active 目錄域服務對 Azure 檔共享進行身份驗證。 如果您有興趣為 Azure 檔案共享啟用 Azure AD DS (GA) 身份驗證,請參閱[我們有關主題的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 檔僅共用支援針對一個域服務的身份驗證,即 Azure 活動目錄域服務 (Azure AD DS) 或本地活動目錄域服務 (AD DS)。 
>
> 用於 Azure 檔案共享身份驗證的 AD DS 標識必須同步到 Azure AD。 密碼哈希同步是可選的。 
> 
> 本地 ADDS 身份驗證不支援對 AD DS 中創建的計算機帳戶進行身份驗證。 
> 
> 本地 ADDS 身份驗證只能針對儲存帳戶註冊的一個 AD 林進行支援。 預設情況下,您只能從單個林訪問具有 AD DS 認證的 Azure 檔共用。 如果需要從其他林訪問 Azure 檔共享,請確保配置了正確的林信任,有關詳細資訊,請參閱[常見問題解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。  
> 
> 對於 Azure 檔同步管理的 Azure 檔案共享,支援針對 SMB 訪問和 ACL 持久性的 AD DS 身份驗證。
>
> Azure 檔案支援使用 RC4-HMAC 加密的 AD 進行 Kerberos 身份驗證。 AES Kerberos 加密尚不受支援。

通過 SMB 為 Azure 檔案共享啟用 AD DS 時,AD DS 聯接的電腦可以使用現有的 AD 認證載入 Azure 檔案共用。 此功能可以通過託管在預處理計算機或 Azure 中託管的 AD DS 環境啟用。

用於造訪 Azure 檔案共享的標識必須同步到 Azure AD,以便透過[基於角色的存取控制 (RBAC)](../../role-based-access-control/overview.md)模型強制實施共用等級檔案許可權。 將保留並強制執行從現有檔案伺服器結轉的檔案/目錄上的[Windows 樣式 DACL。](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 此功能提供與企業 AD DS 環境的無縫整合。 當您使用 Azure 檔共享替換上置前檔案伺服器時,現有使用者可以通過單個登入體驗從其當前用戶端訪問 Azure 檔案共用,而無需對正在使用的認證進行任何更改。  

> [!NOTE]
> 為了説明您為某些常見用例設置 Azure 檔 AD 身份驗證,我們發布[了 兩個視頻](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos),並逐步指導:
> - 將本地檔案伺服器取代為 Azure 檔(包括在檔案和 AD 身份驗證的專用連結)
> - 使用 Azure 檔案為 Windows 虛擬桌面的設定檔容器(包括 AD 身份驗證與 FSLogix 設定的設定)

## <a name="prerequisites"></a>Prerequisites 

在 Azure 檔案分享啟用 AD DS 身份驗證之前,請確保已完成以下先決條件: 

- 選擇或建立 AD DS 環境[並將其同步到 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    您可以在新的或現有的本地 AD DS 環境中啟用該功能。 用於造訪的身份必須同步到 Azure AD。 Azure AD 租戶和要訪問的文件共享必須與同一訂閱關聯。 

    要設定 AD 網域環境,請參考[工作目錄網域服務 。](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 如果 AD 尚未將 AD 同步到 Azure AD,請按照[Azure AD 連接和 Azure AD 連接執行狀況安裝路線圖](../../active-directory/hybrid/how-to-connect-install-roadmap.md)中的指南來配置和設定 Azure AD 連接。 

- 域將本地電腦或 Azure VM 連接到本地 AD DS。 

    要使用電腦或 VM 中的 AD 認證存取檔分享,您的裝置必須加入 AD DS。 有關如何加入網域的資訊,請參閱[將電腦加入網域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 在[受支援的區域](#regional-availability)中選擇或創建 Azure 儲存帳戶。 

    確保包含檔案共用的儲存帳戶尚未配置為 Azure AD DS 身份驗證。 如果在儲存帳戶上啟用 Azure 檔 Azure AD DS 身份驗證,則需要在更改為使用本地 AD DS 之前禁用該身份驗證。 這意味著需要在 Azure AD DS 環境中配置的現有 ACL 進行重新配置,以便進行適當的許可權實施。
    
    有關建立新檔案共享的資訊,請參閱 [在 Azure 檔案 建立檔案共享](storage-how-to-create-file-share.md)。
    
    為了獲得最佳性能,我們建議您將存儲帳戶部署在同一區域中,以便從哪個 VM 訪問共用。 

- 通過使用存儲帳戶金鑰安裝 Azure 檔案共享來驗證連接。 

    要驗證您的設備和檔分享是否已正確配置,請嘗試使用存儲帳戶密鑰安裝檔案共用。 有關詳細資訊,請參閱使用 [Windows 的 Azure 檔案共享](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>區域可用性

Azure 檔案身份驗證與 AD DS(預覽)在大多數[公共區域](https://azure.microsoft.com/global-infrastructure/regions/)都可用。

具有本地 AD DS 的 Azure 檔身份驗證不在:
- 美國西部


## <a name="workflow-overview"></a>工作流程概觀

在為 Azure 檔共享啟用透過 SMB 進行 AD DS 身份驗證之前,我們建議您閱讀並完成 [先決條件](#prerequisites)部分。 先決條件驗證 AD、Azure AD 和 Azure 儲存環境是否已正確配置。 

如果您計劃在檔共享上啟用任何網路配置,我們建議您在啟用 ADDS 身份驗證之前評估[網路注意事項](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)並首先完成相關配置。

接下來,按照以下步驟設定用於 AD 身份驗證的 Azure 檔: 

1. 在儲存帳戶上啟用 Azure 檔案 AD DS 身份驗證。 

1. 將共用的存取許可權分配給與目標 AD 識別同步的 Azure AD 識別(使用者、組或服務主體)。 

1. 通過 SMB 配置目錄和檔的 ACL。 
 
1. 將 Azure 檔案共享載入加入 AD DS 的 VM。 

1. 在 AD DS 中更新儲存帳戶標識的密碼。

下圖說明了用於透過 SMB 為 Azure 檔案共享啟用 Azure AD 身份驗證的端到端工作流。 

![檔案 AD 工作流圖](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 檔案共享的透過 SMB 進行 ADDS 身份驗證僅在運行在比 Windows 7 或 Windows Server 2008 R2 更新的作業系統版本的電腦上或 VM 上支援。 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. 為您的帳戶啟用 AD 認證 

要透過 SMB 對 Azure 檔案共享啟用 AD DS 身份驗證,您需要首先在 AD DS 註冊儲存帳戶,然後在儲存帳戶上設置所需的域屬性。 在存儲帳戶上啟用該功能時,它適用於帳戶中的所有新檔和現有文件共用。 用於`join-AzStorageAccountForAuth`啟用該功能。 您可以在本節中的腳本中找到端到端工作流的詳細說明。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` cmdlet 將修改您的 AD 環境。 閱讀以下說明,以更好地瞭解它正在採取哪些措施來確保您具有執行命令的適當許可權,並確保應用的更改與合規性和安全策略保持一致。 

cmdlet`Join-AzStorageAccountForAuth`將代表指示的存儲帳戶執行等效的離線域聯接。 這個文稿使用 cmdlet 在 AD 網域建立帳號,即[電腦帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)(預設)[或服務登入帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 如果選擇手動執行此操作,則應選擇最適合您的環境的帳戶。

cmdlet 創建的 AD DS 帳戶表示 AD 網域中的儲存帳戶。 如果 AD DS 帳戶是在強制密碼過期的組織單位 (OU) 下創建的,則必須在最大密碼期限之前更新密碼。 訪問 Azure 檔共享時,未能更新帳戶密碼將導致身份驗證失敗。 要瞭解如何更新密碼,請參閱[更新 ADDS 帳號密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

您可以使用以下文稿執行註冊並啟用該功能,也可以手動執行腳本將執行的操作。 這些操作在腳本之後的部分中介紹。 您不需要同時執行這兩個操作。

### <a name="11-script-prerequisites"></a>1.1 文稿先決條件
- [下載並解壓縮 AzFiles 混合模組](https://github.com/Azure-Samples/azure-files-samples/releases)
- 使用 AD DS 認證在已連線到本地 ADDS 的網域的裝置中安裝和執行模組,該認證具有在目標 AD 中建立服務登錄帳戶或電腦帳戶的許可權。
-  使用同步到 Azure AD 的本地 AD DS 認證執行文稿。 本地 AD DS 認證必須具有儲存帳戶擁有者或參與者 RBAC 角色許可權。
- 請確定您的儲存帳戶位於[受支援的區域](#regional-availability)。

### <a name="12-domain-join-your-storage-account"></a>1.2 網域加入您的儲存帳戶
在 PowerShell 中執行占位符之前,請記住在下面的參數中將占位符值替換為您自己的占位符值。
> [!IMPORTANT]
> 域聯接 cmdlet 將創建一個 AD 帳戶來表示 AD 中的儲存帳戶(檔案共用)。 您可以選擇註冊為電腦帳戶或服務登入帳戶,有關詳細資訊,請參閱[常見問題解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。 對於計算機帳戶,AD 中默認密碼過期期限設置為 30 天。 同樣,服務登錄帳戶可能在 AD 域或組織單位 (OU) 上設置了默認密碼過期期限。
> 對於這兩種帳戶類型,我們強烈建議您檢查 AD 環境中設定的密碼過期期限,並計劃在最大密碼期限之前更新以下 AD 帳戶[AD 中的儲存帳戶標識密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。 訪問 Azure 檔案共享時,無法更新 AD 帳戶密碼將導致身份驗證失敗。 您可以考慮在[AD 中建立新的 AD 組織單位 (OU),](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps)並相應地禁用[電腦帳戶](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)或服務登錄帳戶上的密碼過期策略。 

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
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

以下說明總結了執行`Join-AzStorageAccountForAuth`cmdlet 時執行的所有操作。 如果您不希望使用以下命令,則可以手動執行這些步驟:

> [!NOTE]
> 如果您已成功執行上述`Join-AzStorageAccountForAuth`文稿,請轉到下一節"1.3 確認功能已啟用"。 您無需再次執行以下操作。

#### <a name="a-checking-environment"></a>a. 檢查環境

首先,腳本檢查您的環境。 具體而言,它檢查是否安裝了[Active Directory PowerShell,](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps)以及是否使用管理員許可權執行 shell。 然後,它會檢查是否安裝了[Az.Storage 1.11.1 預覽模組](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview),如果沒有安裝該模組,則安裝該模組。 如果這些檢查通過,它將檢查您的 AD DS 以查看是否有電腦[帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)(預設)[或服務登錄帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)已創建 SPN/UPN 為"cifs/您的儲存帳戶-名稱-此處.file.core.windows.net"。 如果帳戶不存在,它將創建一個,如下 b 節所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 手動建立表示 AD 的儲存帳戶的識別

要手動創建此帳戶,請使用`New-AzStorageAccountKey -KeyName kerb1`為您的儲存帳戶創建新的 Kerberos 密鑰。 然後,使用該 Kerberos 金鑰作為您的帳戶的密碼。 此密鑰僅在設置期間使用,不能用於針對存儲帳戶的任何控制或數據平面操作。

擁有該密鑰後,在 OU 下創建服務或計算機帳戶。 使用以下規範:SPN:"cIFs/您的儲存帳戶名稱-此處.file.core.windows.net"密碼:存儲帳戶的 Kerberos 密鑰。

如果 OU 強制密碼過期,則必須在最大密碼期限之前更新密碼,以防止在訪問 Azure 檔共享時出現身份驗證失敗。 有關詳細資訊[,請參閱 AD DS 中的儲存帳戶識別更新密碼](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

保留新創建的帳戶的 SID,下一步將需要它。 您建立的表示儲存帳戶的標識不需要同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在儲存帳戶上啟用此功能

然後,該腳本將在存儲帳戶上啟用該功能。 要手動執行此設置,請提供以下命令中的域屬性的一些配置詳細資訊,然後運行它。 以下命令中所需的儲存帳戶 SID 是您在[上一節中](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)AD DS 中建立的識別的 SID。

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 確認功能已開啟

您可以檢查以確認此功能是否已在儲存帳戶上啟用,您可以使用以下文稿:

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

您現在已成功在存儲帳戶上啟用該功能。 啟用該功能后,必須執行更多步驟才能使用該功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

您現在已成功透過 SMB 啟用 ADDS 身份驗證,並分配了一個自定義角色,該角色提供對具有 AD DS 標識的 Azure 檔共享的訪問。 要給其他使用者存取存取權限,請按照[「分配權限」](#2-assign-access-permissions-to-an-identity)中的說明使用識別,[並在 SMB 部份上設定 NTFS 權限](#3-configure-ntfs-permissions-over-smb)。

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. 在 AD DS 中更新儲存帳戶識別的密碼

如果您在強制密碼過期時間的 OU 下註冊了表示存儲帳戶的 AD DS 標識/ 帳戶,則必須在最大密碼期限之前輪換密碼。 無法更新 AD DS 帳戶的密碼將導致訪問 Azure 檔共享的身份驗證失敗。  

要觸發密碼輪換,可以從 AzFilesHybrid`Update-AzStorageAccountADObjectPassword`模組運行 該命令。 cmdlet 執行類似於存儲帳戶金鑰輪換的操作。 它獲取儲存帳戶的第二個 Kerberos 金鑰,並用它來更新 AD DS 中註冊帳戶的密碼。 然後,它重新生成存儲帳戶的目標 Kerberos 密鑰,並在 AD DS 中更新註冊帳戶的密碼。 您必須在本地 AD DS 網域聯接環境中執行此 cmdlet。

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>後續步驟

有關 Azure 檔以及如何在 SMB 上使用 AD 的詳細資訊,請參閱以下資源:

- [基於 Azure 檔案的基礎的 SMB 存取的身分驗證支援的概述](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
