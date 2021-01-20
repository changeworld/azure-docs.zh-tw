---
title: '建立 NFS 共用-Azure 檔案儲存體 (preview) '
description: 瞭解如何建立可使用網路檔案系統通訊協定來裝載的 Azure 檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602659"
---
# <a name="how-to-create-an-nfs-share"></a>如何建立 NFS 磁碟區

Azure 檔案共用是在雲端中完全受控的檔案共用。 您可以使用伺服器訊息區通訊協定或網路檔案系統 (NFS) 通訊協定來存取它們。 本文說明如何建立使用 NFS 通訊協定的檔案共用。 如需這兩種通訊協定的詳細資訊，請參閱 [Azure 檔案共用通訊協定](storage-files-compare-protocols.md)。

## <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>區域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>必要條件

- 建立 [FileStorage 帳戶](storage-how-to-create-premium-fileshare.md)。

    > [!IMPORTANT]
    > NFS 共用只能從信任的網路存取。 與您的 NFS 共用的連線必須來自下列其中一個來源：

    - [建立私人端點](storage-files-networking-endpoints.md#create-a-private-endpoint) (建議的) 或[限制對公用端點的存取](storage-files-networking-endpoints.md#restrict-public-endpoint-access)。
    - [在 Linux 上設定點對站 (P2S) VPN，以搭配 Azure 檔案儲存體使用](storage-files-configure-p2s-vpn-linux.md)。
    - [設定與 Azure 檔案儲存體搭配使用的站對站 VPN](storage-files-configure-s2s-vpn.md)。
    - 設定 [ExpressRoute](../../expressroute/expressroute-introduction.md)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="register-the-nfs-41-protocol"></a>註冊 NFS 4.1 通訊協定

如果您使用的是 Azure PowerShell 模組或 Azure CLI，請使用下列命令來註冊您的功能：

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>確認功能註冊

註冊核准最多可能需要一小時的時間。 若要確認註冊是否已完成，請使用下列命令：

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>確認儲存體帳戶種類

目前，只有 FileStorage 帳戶可以建立 NFS 共用。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要確認您擁有的儲存體帳戶種類，請在 Azure 入口網站中流覽至該帳戶。 然後，從您的儲存體帳戶選取 [ **屬性**]。 從 [屬性] 分頁中，檢查 [ **帳戶類型**] 下的值，此值應該是 **FileStorage**。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要確認您有 FileStorage 帳戶，您可以使用下列命令：

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

輸出應該是 **FileStorage**，如果不是，則您的儲存體帳戶是不正確的類型。 若要建立 **FileStorage** 帳戶，請參閱 [如何建立 Azure premium 檔案共用](storage-how-to-create-premium-fileshare.md)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要確認您有 FileStorage 帳戶，您可以使用下列命令：

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

輸出應包含 **"kind"： "FileStorage"**，如果沒有，則您的儲存體帳戶是不正確的類型。 若要建立 **FileStorage** 帳戶，請參閱 [如何建立 Azure premium 檔案共用](storage-how-to-create-premium-fileshare.md)。

---
## <a name="create-an-nfs-share"></a>建立 NFS 磁碟區

# <a name="portal"></a>[入口網站](#tab/azure-portal)

現在您已建立 FileStorage 帳戶並設定網路功能，接下來您可以建立 NFS 檔案共用。 此程式類似于建立 SMB 共用，您可以在建立共用時選取 **NFS** 而非 **SMB** 。

1. 瀏覽至您的儲存體帳戶，並選取 [檔案共用]。
1. 選取 [ **+ 檔案共用** ] 以建立新的檔案共用。
1. 命名您的檔案共用，選取布建的容量。
1. 針對 **通訊協定** ，請選取 [ **NFS (預覽])**。
1. 針對 **根 Squash** 進行選取。

    - Root squash (預設) 存取遠端超級使用者 (根) 對應到 UID (65534) 和 GID (65534) 。
    - 沒有根 squash-遠端超級使用者 (根) 以 root 身分接收存取權。
    - 所有 squash-所有使用者存取都對應至 UID (65534) 和 GID (65534) 。
    
1. 選取 [建立]。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="檔案共用建立分頁的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 確定已安裝 .NET framework。 請參閱 [下載 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
 
1. 使用下列命令確認已安裝的 PowerShell 版本 `5.1` 或更高版本。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升級您的 PowerShell 版本，請參閱 [升級現有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. 安裝最新版本的 PowershellGet 模組。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. 關閉，然後重新開啟 PowerShell 主控台。

1. 安裝 **Az. Storage** preview module 版本 **2.5.2-preview**。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   如需如何安裝 PowerShell 模組的詳細資訊，請參閱 [安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. 若要使用 Azure PowerShell 模組建立 premium 檔案共用，請使用 [AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) Cmdlet。

> [!NOTE]
> 布建的共用大小是由共用配額指定，檔案共用會依布建的大小計費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立 premium 檔案共用，請使用 [az storage share create](/cli/azure/storage/share-rm) 命令。

> [!NOTE]
> 布建的共用大小是由共用配額指定，檔案共用會依布建的大小計費。 如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>後續步驟

現在您已建立 NFS 共用，您必須將它裝載在 Linux 用戶端上，才能使用它。 如需詳細資訊，請參閱 [如何掛接 NFS 共用](storage-files-how-to-mount-nfs-shares.md)。

如果您遇到任何問題，請參閱 [疑難排解 AZURE NFS 檔案共用](storage-troubleshooting-files-nfs.md)。
