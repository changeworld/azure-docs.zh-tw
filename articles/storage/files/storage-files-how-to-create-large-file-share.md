---
title: 開啟與建立大型檔案分享 - Azure 檔案
description: 在本文中,您將瞭解如何啟用和創建大型文件共用。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537673"
---
# <a name="enable-and-create-large-file-shares"></a>開啟並建立大型檔案分享

在儲存帳戶上啟用大型檔共用時,檔共用可以擴展到 100 TiB。 您可以在現有檔案共用的現有存儲帳戶上啟用此縮放。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 如果要使用 Azure CLI,[請安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果要使用 Azure PowerShell,[請安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

目前,您只能在啟用大型檔共享的帳戶上使用本地冗餘存儲 (LRS) 或區域冗餘存儲 (ZRS)。 不能使用地理區域冗餘存儲 (GZRS)、異地冗餘存儲 (GRS) 或讀取存取異地冗餘記憶體 (RA-GRS)。
在帳戶上啟用大型文件共用是一個不可逆的過程。 啟用後,您將無法將您的帳戶轉換為 GZRS、GRS 或 RA-GRS。

## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶

### <a name="portal"></a>入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 門戶中,選擇 **"所有服務**"。 
1. 在資源清單中,輸入**儲存帳戶**。 當您輸入時，清單會根據您輸入的文字進行篩選。 選擇**儲存帳號**。
1. 在顯示的 **「儲存帳戶**」視窗中,選擇「**添加**」 。
1. 選擇用於創建存儲帳戶的訂閱。
1. 在 [資源群組]**** 欄位下方，選取 [新建]****。 為新的資源群組輸入名稱。

    ![示範如何在入口網站中建立資源群組的螢幕擷取畫面](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 接下來，輸入儲存體帳戶的名稱。 此名稱在整個 Azure 中必須是唯一的。 名稱的長度也必須為 3 到 24 個字元,並且只能有數位和小寫字母。
1. 選取儲存體帳戶的位置。
1. 將複製設定為**本地冗餘記憶體**或**區域冗餘儲存**。
1. 將這些欄位保留為預設值:

   |欄位  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |效能     |標準         |
   |帳戶類型     |StorageV2 (一般用途 v2)         |
   |存取層     |經常性存取         |

1. 選擇 **「進階**」,然後選擇 **「大型檔案共用**右側的**啟用**」選項按鈕。
1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。

    ![在 Azure 門戶中新儲存帳戶上使用「已啟用」選項按鈕的螢幕截圖](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 選取 [建立]  。

### <a name="cli"></a>CLI

首先,[安裝最新版本的 Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)以便可以啟用大型檔案共用。

要建立啟用大型檔共用的儲存帳戶,請使用以下命令。 使用`<yourStorageAccountName>`您的資訊`<yourResourceGroup>``<yourDesiredRegion>`取代與 。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

首先,[安裝最新版本的 PowerShell,](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)以便您可以啟用大型檔案共用。

要建立啟用大型檔共用的儲存帳戶,請使用以下命令。 使用`<yourStorageAccountName>`您的資訊`<yourResourceGroup>``<yourDesiredRegion>`取代與 。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>在現有帳戶上啟用大型檔案分享

您還可以在現有帳戶上啟用大型文件共用。 如果啟用大型檔案共用,將無法轉換為 GZRS、GRS 或 RA-GRS。 在此存儲帳戶上啟用大型檔共享是不可逆的。

### <a name="portal"></a>入口網站

1. 打開[Azure 門戶](https://portal.azure.com),然後轉到要啟用大型檔案共用的存儲帳戶。
1. 開啟存儲帳戶並選擇 **「設定」**。
1. 在**大型檔案共用**上選擇**啟用**,然後選擇 **"儲存**"。
1. 選擇 **「概覽」** 並選擇 **「刷新**」 。

![在 Azure 門戶中在現有儲存帳戶上選擇「已啟用」選項按鈕](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

您現在已啟用存儲帳戶上的大型文件共用。 接下來,必須更新現有共用的配額,以利用增加的容量和規模。

如果您收到錯誤消息"大型文件共用尚不可用,則您所在的區域可能正在完成其推出。 如果您迫切需要大型文件共用,請聯繫支援人員。

### <a name="cli"></a>CLI

要在現有帳戶上啟用大型檔共用,請使用以下命令。 替換`<yourStorageAccountName>`並`<yourResourceGroup>`隨您的資訊。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

要在現有帳戶上啟用大型檔共用,請使用以下命令。 替換`<yourStorageAccountName>`並`<yourResourceGroup>`隨您的資訊。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>建立大型檔案分享

在存儲帳戶上啟用大型檔共用后,可以在該帳戶中創建具有較高配額的檔共用。 

### <a name="portal"></a>入口網站

創建大型檔案共用與創建標準檔共享幾乎相同。 主要區別是,您可以設置最多 100 TiB 的配額。

1. 從存儲帳戶中,選擇 **「檔案共用**」。。
1. 選擇 **= 檔案共享**。
1. 輸入檔案共享的名稱。 您還可以設置您希望的配額大小,最多 100 TiB。 然後選取 [建立]  。 

![顯示名稱和配額框的 Azure 門戶 UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

要建立大型文件共用,請使用以下命令。 使用`<yourStorageAccountName>`您的資訊`<yourStorageAccountKey>``<yourFileShareName>`取代與 。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

要建立大型文件共用,請使用以下命令。 使用`<YourStorageAccountName>`您的資訊`<YourStorageAccountKey>``<YourStorageAccountFileShareName>`取代與 。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>延伸現有檔案分享

在存儲帳戶上啟用大型檔共用后,還可以將該帳戶中的現有檔共用擴展到更高的配額。 

### <a name="portal"></a>入口網站

1. 從存儲帳戶中,選擇 **「檔案共用**」。。
1. 右鍵按一下檔共用,然後選擇 **「配額**」。。
1. 輸入所需的新大小,然後選擇 **"確定**"。

![具有現有檔案共享配額的 Azure 門戶 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

要將配額設置為最大大小,請使用以下命令。 使用`<yourStorageAccountName>`您的資訊`<yourStorageAccountKey>``<yourFileShareName>`取代與 。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

要將配額設置為最大大小,請使用以下命令。 使用`<YourStorageAccountName>`您的資訊`<YourStorageAccountKey>``<YourStorageAccountFileShareName>`取代與 。

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

* [在 Windows 上連接和載入檔案分享](storage-how-to-use-files-windows.md)
* [Linux 連接與載入檔案分享](storage-how-to-use-files-linux.md)
* [在 macOS 上連接和載入檔案分享](storage-how-to-use-files-mac.md)
