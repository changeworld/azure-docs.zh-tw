---
title: 啟用 SMB 多重通道
description: 瞭解如何在 Azure premium 檔案共用上啟用 SMB 多重通道。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2444ec28a2618b638f78926e214de468f56c5e52
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "95995455"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>在 FileStorage 帳戶上啟用 SMB 多重通道 (預覽)  

Azure FileStorage 帳戶支援 (preview) 的 SMB 多重通道，這會藉由建立多個與 premium 檔案共用的網路連線，來提高 SMB 3.x 用戶端的效能。 本文提供在現有的儲存體帳戶上啟用 SMB 多重通道的逐步指引。 如需有關 Azure 檔案儲存體 SMB 多重通道的詳細資訊，請參閱 SMB 多重通道效能。

## <a name="limitations"></a>限制

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>區域可用性

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>必要條件

- [建立 FileStorage 帳戶](storage-how-to-create-premium-fileshare.md)。
- 如果您想要使用 Azure PowerShell 模組，請 [安裝此模組的3.0.1 預覽版本](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview)。

## <a name="getting-started"></a>開始使用

開啟 PowerShell 視窗並登入您的 Azure 訂用帳戶。 從該處，您可以使用下列命令來註冊 SMB 多重通道預覽。

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> 註冊最多可能需要一小時的時間。

### <a name="verify-that-feature-registration-is-complete"></a>確認功能註冊已完成

因為在您的儲存體帳戶上啟用此功能可能需要一小時的時間，您可以使用下列命令來驗證它是否已針對您的訂用帳戶註冊：

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>啟用 SMB 多重通道 
建立 FileStorage 帳戶之後，您可以依照指示，更新儲存體帳戶的 SMB 多重通道設定。

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 Azure 入口網站，然後流覽至您想要設定 SMB 多重通道的 FileStorage 儲存體帳戶。
1. 選取 [檔案 **服務**] 下的 [檔案 **共用**]，然後選取 [檔案 **共用設定**]。
1. 將 **SMB 多重** 通道切換至 **on** (或 **off** 以停用) ，然後選取 [ **儲存**]。

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="儲存體帳戶的螢幕擷取畫面，已開啟 smb 多重通道。":::

如果在 [檔案 **共用設定** ] 下看不到 SMB 多重通道選項，或在更新設定時發生無法更新設定錯誤，請確認您的訂用帳戶已註冊，而且您的帳戶位於支援的帳戶類型和複寫的其中一個 [支援區域](#regional-availability) 中。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 模組啟用 SMB 多重通道，您必須安裝此模組 [的3.0.1 預覽版本](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) 。

執行 `$resourceGroupName` 這些 PowerShell 命令之前，請先將變數和設定 `$storageAccountName` 至您的資源群組和儲存體帳戶。

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI 尚不支援設定 SMB 多重通道。 請參閱入口網站指示，以在儲存體帳戶上設定 SMB 多重通道。

---

> [!NOTE]
> SMB 多重通道設定設定的任何變更都會套用至儲存體帳戶下的所有檔案共用。 不過，您必須在用戶端上重新掛接共用，變更才會生效。


## <a name="next-steps"></a>後續步驟 

- 重新掛接您的檔案[共用](storage-how-to-use-files-windows.md)，以利用 SMB 多重通道。
- 針對[任何與 SMB 多重通道相關的問題進行疑難排解](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)。
- 若要深入瞭解增強功能，請參閱 [SMB 多重通道效能](storage-files-smb-multichannel-performance.md)
 - 若要深入瞭解 Windows SMB 多重通道功能，請參閱 [管理 SMB Mulitchannel](/azure-stack/hci/manage/manage-smb-multichannel)。
