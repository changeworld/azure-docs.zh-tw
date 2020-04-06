---
title: 使用 Azure AD 網域服務授權透過 SMB 存取檔案資料
description: 瞭解如何透過 Azure 活動目錄網域服務透過伺服器訊息區塊 (SMB) 為 Azure 檔案啟用基於身份的身份驗證。 然後,加入網域的 Windows 虛擬機器 (VM) 可以使用 Azure AD 認證存取 Azure 檔共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666842"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>在 Azure 檔案開啟 Azure 的目錄網域服務認證

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

有關 Azure 檔案分享的 Azure AD 認證概述,請參考[Azure 檔案透過 SMB 進行 Azure 的目錄身分驗證的概述](storage-files-active-directory-overview.md)。 本文重點介紹如何在 Azure 檔上啟用 Azure 活動目錄域服務 (Azure AD DS) 的身份驗證。

> [!NOTE]
> Azure 檔案支援使用 RC4-HMAC 加密的 Azure AD DS 進行 Kerberos 身份驗證。 AES Kerberos 加密尚不受支援。

## <a name="prerequisites"></a>Prerequisites

在 Azure 檔案分享啟用 Azure AD 過 SMB 之前,請確保已完成以下先決條件:

1.  **選取或建立 Azure AD 租用戶。**

    您可以針對「透過 SMB 進行 Azure AD 驗證」使用全新或現有租用戶。 租用戶和您想要存取的檔案共用必須與相同的訂用帳戶相關聯。

    若要建立新的 Azure AD 租用戶，您可以[新增 Azure AD 租用戶和 Azure AD 訂用帳戶](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有現有的 Azure AD 租戶,但希望建立新租戶以與 Azure 檔案共用一起使用,請參閱[創建 Azure 活動目錄租戶](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

1.  **在 Azure AD 租用戶上啟用 Azure AD Domain Services。**

    若要支援使用 Azure AD 認證進行驗證，您必須為 Azure AD 租用戶啟用 Azure AD Domain Services。 如果您不是 Azure AD 租用戶的系統管理員，請連絡系統管理員，並遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) 的逐步指導。

    Azure AD DS 部署通常需要大約 15 分鐘才能完成。 在繼續下一步之前,驗證 Azure ADDS 的運行狀況是否顯示 **「正在運行**」,並啟用了密碼哈希同步。

1.  **域將 Azure VM 與 Azure AD DS 聯接。**

    要使用來自 VM 的 Azure AD 認證存取檔分享,VM 必須已加入 Azure AD DS。 如需如何加入虛擬機器網域的詳細資訊，請參閱[將 Windows Server 虛擬機器加入受控網域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > Azure AD DS 驗證上具有 Azure 檔案分享,僅在 Windows 7 或 Windows 伺服器 2008 R2 上方作業系統版本的 Azure VM 支援。

1.  **選取或建立 Azure 檔案共用。**

    選取與 Azure AD 租用戶相同訂閱關聯的全新或現有檔案共用。 如需建立新檔案共用的資訊，請參閱 [在 Azure 檔案中建立檔案共用](storage-how-to-create-file-share.md)。
    為了獲得最佳性能,我們建議您的檔共用與計劃從其中訪問共用的 VM 位於同一區域。

1.  **藉由使用儲存體帳戶金鑰裝載 Azure 檔案共用，驗證 Azure 檔案連線。**

    若要驗證是否已正確設定虛擬機器和檔案共用，請嘗試使用儲存體帳戶金鑰裝載檔案共用。 如需詳細資料，請參閱[裝載 Azure 檔案共用並在 Windows 中存取共用](storage-how-to-use-files-windows.md)。

## <a name="overview-of-the-workflow"></a>工作流程概觀

在為 Azure 檔案共享啟用透過 SMB 進行 Azure AD DS 身份驗證之前,請驗證 Azure AD 和 Azure 儲存環境是否配置正確。 我們建議您演練[先決條件](#prerequisites),以確保已完成所有必需的步驟。

接下來,執行以下操作以授予使用 Azure AD 認證的 Azure 檔資源的許可權:

1. 為存儲帳戶通過 SMB 啟用 Azure AD DS 身份驗證,以便將儲存帳戶註冊到關聯的 Azure AD DS 部署。
2. 將共用的存取權限指派至 Azure AD 身分識別 (使用者、群組或服務主體)。
3. 透過 SMB 為目錄和檔案設定 NTFS 權限。
4. 從已加入網域的虛擬機器中裝載 Azure 檔案共用。

下圖說明了用於透過 SMB 對 Azure 檔啟用 Azure AD DS 身份驗證的端到端工作流。

![此圖顯示針對 Azure 檔案使用「透過 SMB 進行 Azure AD 驗證」的工作流程](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. 為您的帳戶啟用 Azure AD DS 認證

要透過 Azure 檔的 SMB 啟用 Azure AD DS 身份驗證,可以使用 Azure 門戶、Azure PowerShell 或 Azure CLI 在儲存帳戶上設定屬性。 將此屬性隱式設置"域聯接"存儲帳戶與關聯的 Azure AD DS 部署。 然後,對儲存帳戶中的所有新檔和現有檔共享啟用通過 SMB 的 Azure AD DS 身份驗證。

請記住,只有在成功地將 Azure AD DS 部署到 Azure AD 租戶後,才能透過 SMB 啟用 Azure AD DS 身份驗證。 有關詳細資訊,請參閱[先決條件](#prerequisites)。

### <a name="azure-portal"></a>Azure 入口網站

要使用[Azure 門戶](https://portal.azure.com)透過 SMB 啟用 Azure AD DS 身份驗證,請按照以下步驟操作:

1. 在 Azure 門戶中,轉到現有儲存帳戶,或[建立儲存帳戶](../common/storage-account-create.md)。
1. 在 [設定]**** 區段中，選取 [組態]****。
1. 在**使用基礎的檔案分享存取**下,將**Azure 的目錄網域服務 (AAD DS) 切換**切換到**啟用**。
1. 選取 [儲存]  。

下圖示範如何透過儲存帳戶的 SMB 啟用 Azure AD DS 身份驗證。

![在 Azure 門戶中透過 SMB 啟用 Azure AD DS 驗證](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

要使用 Azure PowerShell 透過 SMB 啟用 Azure AD DS 身份驗證,請安裝最新的 Az 模組(2.4 或更高版本)或 Az.Storage 模組(1.5 或更高)。 有關安裝 PowerShell 的詳細資訊,請參閱[使用 PowerShell 在 Windows 上安裝 Azure PowerShell。](https://docs.microsoft.com/powershell/azure/install-Az-ps)

要建立新的儲存帳戶,請呼叫[New-AzStorage 帳號](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0),然後將**啟用 AzureActiveDirectoryDomain 服務ForFile**參數設定為**true**。 在下面的範例中,請記住將占位符值替換為您自己的值。 ( 如果使用上一個預覽模組,則功能啟用的參數為**啟用 AzureFilesAad 集成為 SMB**。

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

要在現有儲存帳戶上啟用此功能,請使用以下命令:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

要使用 Azure CLI 透過 SMB 啟用 Azure AD 身份驗證,請安裝最新的 CLI 版本(版本 2.0.70 或更高版本)。 有關安裝 Azure CLI 的詳細資訊,請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

要建立新的儲存帳戶,請呼叫[az 儲存帳戶建立](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create),`--enable-files-aadds`並將屬性設定為**true**。 在下面的範例中,請記住將占位符值替換為您自己的值。 (如果使用上一個預覽模組,則功能啟用的參數為**file-ad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

要在現有儲存帳戶上啟用此功能,請使用以下命令:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

現在,您已成功通過 SMB 啟用 Azure AD DS 身份驗證,並分配了一個自定義角色,該角色提供使用 Azure AD 標識存取 Azure 檔共享的功能。 要給其他使用者存取存取權限,請按照[「分配權限」](#2-assign-access-permissions-to-an-identity)中的說明使用識別,[並在 SMB 部份上設定 NTFS 權限](#3-configure-ntfs-permissions-over-smb)。

## <a name="next-steps"></a>後續步驟

有關 Azure 檔以及如何在 SMB 上使用 Azure AD 的詳細資訊,請參閱以下資源:

- [基於 Azure 檔案的基礎的 SMB 存取的身分驗證支援的概述](storage-files-active-directory-overview.md)
- [常見問題集](storage-files-faq.md)
