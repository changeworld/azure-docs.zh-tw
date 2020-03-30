---
title: 啟用和創建大型檔共用 - Azure 檔
description: 在本文中，您將瞭解如何啟用和創建大型檔共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c683e9847864de4e3409fb6dbd533497a5ae3cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061004"
---
# <a name="enable-and-create-large-file-shares"></a>啟用並創建大型檔共用

在存儲帳戶上啟用大型檔共用時，檔共用可以擴展到 100 TiB。 您可以在現有檔共用的現有存儲帳戶上啟用此縮放。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 如果要使用 Azure CLI，[請安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果要使用 Azure PowerShell，[請安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

目前，您只能在啟用大型檔共用的帳戶上使用本地冗余存儲 （LRS） 或區域冗余存儲 （ZRS）。 不能使用地理區域冗余存儲 （GZRS）、異地冗余存儲 （GRS） 或讀取存取異地冗余存儲 （RA-GRS）。
在帳戶上啟用大型檔共用是一個不可逆的過程。 啟用後，您將無法將您的帳戶轉換為 GZRS、GRS 或 RA-GRS。

## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶

### <a name="portal"></a>入口網站

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在 Azure 門戶中，選擇 **"所有服務**"。 
1. 在資源清單中，輸入**存儲帳戶**。 當您輸入時，清單會根據您輸入的文字進行篩選。 選取 [儲存體帳戶] ****。
1. 在顯示的 **"存儲帳戶**"視窗中，選擇"**添加**"。
1. 選擇用於創建存儲帳戶的訂閱。
1. 在 [資源群組]**** 欄位下方，選取 [新建]****。 為新的資源群組輸入名稱。

    ![示範如何在入口網站中建立資源群組的螢幕擷取畫面](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 接下來，輸入儲存體帳戶的名稱。 此名稱在整個 Azure 中必須是唯一的。 名稱的長度也必須為 3 到 24 個字元，並且只能有數位和小寫字母。
1. 選擇存儲帳戶的位置，並確保它是[大型檔共用受支援的複製之一](storage-files-planning.md#regional-availability)。
1. 將複製設置為**本地冗余存儲**或**區域冗余存儲**。
1. 將這些欄位保留為其預設值：

   |欄位  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |效能     |標準         |
   |帳戶類型     |StorageV2 (一般用途 v2)         |
   |存取層     |經常性存取         |

1. 選擇 **"高級**"，然後選擇 **"大型檔共用**右側的**啟用**"選項按鈕。
1. 選取 [檢閱 + 建立]****，以檢閱您的儲存體帳戶設定並建立帳戶。

    ![在 Azure 門戶中新存儲帳戶上使用"已啟用"選項按鈕的螢幕截圖](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 選取 [建立]****。

### <a name="cli"></a>CLI

首先，[安裝最新版本的 Azure CLI，](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)以便可以啟用大型檔共用。

要創建啟用大型檔共用的存儲帳戶，請使用以下命令。 用`<yourStorageAccountName>`您的資訊`<yourResourceGroup>`替換`<yourDesiredRegion>`和 。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先，[安裝最新版本的 PowerShell，](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)以便您可以啟用大型檔共用。

要創建啟用大型檔共用的存儲帳戶，請使用以下命令。 用`<yourStorageAccountName>`您的資訊`<yourResourceGroup>`替換`<yourDesiredRegion>`和 。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>在現有帳戶上啟用大型檔共用

您還可以在現有帳戶上啟用大型檔共用。 如果啟用大型檔共用，將無法轉換為 GZRS、GRS 或 RA-GRS。 在此存儲帳戶上啟用大型檔共用是不可逆的。

### <a name="portal"></a>入口網站

1. 打開[Azure 門戶](https://portal.azure.com)，然後轉到要啟用大型檔共用的存儲帳戶。
1. 打開存儲帳戶並選擇 **"配置**"。
1. 在**大型檔共用**上選擇**啟用**，然後選擇 **"保存**"。
1. 選擇 **"概覽"** 並選擇 **"刷新**"。

![在 Azure 門戶中在現有存儲帳戶上選擇"已啟用"選項按鈕](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

您現在已啟用存儲帳戶上的大型檔共用。 接下來，必須更新現有共用的配額，以利用增加的容量和規模。

如果您收到錯誤訊息"大型檔共用尚不可用，則您所在的區域可能正在完成其推出。 如果您迫切需要大型檔共用，請聯繫支援人員。

### <a name="cli"></a>CLI

要在現有帳戶上啟用大型檔共用，請使用以下命令。 替換`<yourStorageAccountName>`並`<yourResourceGroup>`隨您的資訊。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

要在現有帳戶上啟用大型檔共用，請使用以下命令。 替換`<yourStorageAccountName>`並`<yourResourceGroup>`隨您的資訊。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>創建大型檔共用

在存儲帳戶上啟用大型檔共用後，可以在該帳戶中創建具有較高配額的檔共用。 

### <a name="portal"></a>入口網站

創建大型檔共用與創建標準檔共用幾乎相同。 主要區別是，您可以設置最多 100 TiB 的配額。

1. 從存儲帳戶中，選擇 **"檔共用**"。
1. 選擇 **= 檔共用**。
1. 輸入檔共用的名稱。 您還可以設置您希望的配額大小，最多 100 TiB。 然後選擇 **"創建**"。 

![顯示名稱和配額框的 Azure 門戶 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

要創建大型檔共用，請使用以下命令。 用`<yourStorageAccountName>`您的資訊`<yourStorageAccountKey>`替換`<yourFileShareName>`和 。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

要創建大型檔共用，請使用以下命令。 用`<YourStorageAccountName>`您的資訊`<YourStorageAccountKey>`替換`<YourStorageAccountFileShareName>`和 。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>擴展現有檔共用

在存儲帳戶上啟用大型檔共用後，還可以將該帳戶中的現有檔共用擴展到更高的配額。 

### <a name="portal"></a>入口網站

1. 從存儲帳戶中，選擇 **"檔共用**"。
1. 按右鍵檔共用，然後選擇 **"配額**"。
1. 輸入所需的新大小，然後選擇 **"確定**"。

![具有現有檔共用配額的 Azure 門戶 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

要將配額設置為最大大小，請使用以下命令。 用`<yourStorageAccountName>`您的資訊`<yourStorageAccountKey>`替換`<yourFileShareName>`和 。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

要將配額設置為最大大小，請使用以下命令。 用`<YourStorageAccountName>`您的資訊`<YourStorageAccountKey>`替換`<YourStorageAccountFileShareName>`和 。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>後續步驟

* [在 Windows 上連接和裝載檔共用](storage-how-to-use-files-windows.md)
* [在 Linux 上連接和裝載檔共用](storage-how-to-use-files-linux.md)
* [在 macOS 上連接和裝載檔共用](storage-how-to-use-files-mac.md)
