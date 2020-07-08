---
title: 啟用和建立大型檔案共用-Azure 檔案儲存體
description: 在本文中，您將瞭解如何啟用和建立大型檔案共用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 427d936353b47e951f8faaf90483691bab856767
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510738"
---
# <a name="enable-and-create-large-file-shares"></a>啟用並建立大型檔案共用

當您在儲存體帳戶上啟用大型檔案共用時，您的檔案共用可以相應增加至 100 TiB。 您可以針對現有的檔案共用，在現有的儲存體帳戶上啟用此調整。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
- 如果您想要使用 Azure CLI，請[安裝最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
- 如果您想要使用 Azure PowerShell，請[安裝最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="restrictions"></a>限制

目前，您只能在已啟用大型檔案共用的帳戶上使用本機-多餘儲存體（LRS）或區域冗余儲存體（ZRS）。 您不能使用異地區域冗余儲存體（切換）、異地多餘儲存體（GRS）、讀取權限異地多餘儲存體（RA-GRS）或讀取權限異地區域冗余儲存體（RA-切換）。

在帳戶上啟用大型檔案共用是無法復原的程式。 啟用之後，您將無法將帳戶轉換成切換、GRS、RA-GRS 或 RA-切換。

## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，選取 [所有服務]。 
1. 在資源清單中，輸入**儲存體帳戶**。 當您輸入時，清單會根據您輸入的文字進行篩選。 選取 [**儲存體帳戶**]。
1. 在出現的 [**儲存體帳戶**] 視窗中，選取 [**新增**]。
1. 選取您將用來建立儲存體帳戶的訂用帳戶。
1. 在 [資源群組]**** 欄位下方，選取 [新建]****。 為新的資源群組輸入名稱。

    ![示範如何在入口網站中建立資源群組的螢幕擷取畫面](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 接下來，輸入儲存體帳戶的名稱。 此名稱在整個 Azure 中必須是唯一的。 名稱的長度也必須是3到24個字元，而且只能包含數位和小寫字母。
1. 選取儲存體帳戶的位置。
1. 將複寫設定為**本機多餘的儲存體**或**區域多餘的儲存體**。
1. 將這些欄位保留為預設值：

   |欄位  |值  |
   |---------|---------|
   |部署模型     |Resource Manager         |
   |效能     |標準         |
   |帳戶類型     |StorageV2 (一般用途 v2)         |
   |存取層     |經常性存取         |

1. 選取 [ **Advanced**]，然後選取 [**大型檔案共用**] 右邊的 [**已啟用**] 選項按鈕。
1. 選取 [檢閱 + 建立]  ，以檢閱您的儲存體帳戶設定並建立帳戶。

    ![在 Azure 入口網站的新儲存體帳戶上具有 [已啟用] 選項按鈕的螢幕擷取畫面](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. 選取 [建立]。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，[安裝最新版本的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ，讓您可以啟用大型檔案共用。

若要建立已啟用大型檔案共用的儲存體帳戶，請使用下列命令。 `<yourStorageAccountName>`將、 `<yourResourceGroup>` 和取代 `<yourDesiredRegion>` 為您的資訊。

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，[安裝最新版的 PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) ，讓您可以啟用大型檔案共用。

若要建立已啟用大型檔案共用的儲存體帳戶，請使用下列命令。 `<yourStorageAccountName>`將、 `<yourResourceGroup>` 和取代 `<yourDesiredRegion>` 為您的資訊。

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>在現有的帳戶上啟用大型檔案共用

您也可以在現有的帳戶上啟用大型檔案共用。 如果您啟用大型檔案共用，您將無法轉換成切換、GRS、RA-GRS 或 RA-切換。 在此儲存體帳戶上啟用大型檔案共用無法復原。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 開啟 [ [Azure 入口網站](https://portal.azure.com)]，然後移至您要啟用大型檔案共用的儲存體帳戶。
1. 開啟儲存體帳戶，**然後選取 [** 設定]。
1. 選取 [在**大型檔案共用**上**啟用**]，然後選取 [**儲存**]。
1. 選取 **[總覽**]，然後選取 [重新整理 **]。**

![在 Azure 入口網站的現有儲存體帳戶上選取 [已啟用] 選項按鈕](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

您現在已在儲存體帳戶上啟用大型檔案共用。 接下來，您必須[更新現有共用的配額](#expand-existing-file-shares)，以利用增加的容量和規模。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要在現有的帳戶上啟用大型檔案共用，請使用下列命令。 `<yourStorageAccountName>`將和取代 `<yourResourceGroup>` 為您的資訊。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

您現在已在儲存體帳戶上啟用大型檔案共用。 接下來，您必須[更新現有共用的配額](#expand-existing-file-shares)，以利用增加的容量和規模。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要在現有的帳戶上啟用大型檔案共用，請使用下列命令。 `<yourStorageAccountName>`將和取代 `<yourResourceGroup>` 為您的資訊。

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

您現在已在儲存體帳戶上啟用大型檔案共用。 接下來，您必須[更新現有共用的配額](#expand-existing-file-shares)，以利用增加的容量和規模。

---

## <a name="create-a-large-file-share"></a>建立大型檔案共用

在您的儲存體帳戶上啟用大型檔案共用之後，您可以在具有較高配額的帳戶中建立檔案共用。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

建立大型檔案共用幾乎等同于建立標準檔案共用。 主要差異在於您可以將配額設定為最多 100 TiB。

1. 從您的儲存體帳戶中，選取 [檔案**共用**]。
1. 選取 [ **+ 檔案共用**]。
1. 輸入檔案共用的名稱。 您也可以設定您想要的配額大小，最高可達 100 TiB。 然後選取 [建立]。 

![顯示 [名稱] 和 [配額] 方塊的 [Azure 入口網站] UI](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要建立大型檔案共用，請使用下列命令。 `<yourStorageAccountName>`將、 `<yourStorageAccountKey>` 和取代 `<yourFileShareName>` 為您的資訊。

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要建立大型檔案共用，請使用下列命令。 `<YourStorageAccountName>`將、 `<YourStorageAccountKey>` 和取代 `<YourStorageAccountFileShareName>` 為您的資訊。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>展開現有的檔案共用

在您的儲存體帳戶上啟用大型檔案共用之後，您也可以將該帳戶中的現有檔案共用擴充為較高的配額。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 從您的儲存體帳戶中，選取 [檔案**共用**]。
1. 以滑鼠右鍵按一下您的檔案共用，然後選取 [**配額**]。
1. 輸入您想要的新大小，然後選取 **[確定]**。

![具有現有檔案共用配額的 Azure 入口網站 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要將配額設定為大小上限，請使用下列命令。 `<yourStorageAccountName>`將、 `<yourStorageAccountKey>` 和取代 `<yourFileShareName>` 為您的資訊。

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要將配額設定為大小上限，請使用下列命令。 `<YourStorageAccountName>`將、 `<YourStorageAccountKey>` 和取代 `<YourStorageAccountFileShareName>` 為您的資訊。

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>後續步驟

* [連線並在 Windows 上掛接檔案共用](storage-how-to-use-files-windows.md)
* [連線並在 Linux 上掛接檔案共用](storage-how-to-use-files-linux.md)
* [連線並在 macOS 上掛接檔案共用](storage-how-to-use-files-mac.md)
