---
title: 將 Azure 存儲帳戶移動到其他區域 |微軟文檔
description: 演示如何將 Azure 存儲帳戶移動到其他區域。
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838710"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>將 Azure 存儲帳戶移動到其他區域

要移動存儲帳戶，請在另一個區域創建存儲帳戶的副本。 然後，使用 AzCopy 或您選擇的其他工具將資料移動到該帳戶。

在本文中，您將學會如何：

> [!div class="checklist"]
> 
> * 匯出範本。
> * 通過添加目的地區域和存儲帳戶名稱來修改範本。
> * 部署範本以創建新的存儲帳戶。
> * 配置新的存儲帳戶。
> * 將資料移動到新的存儲帳戶。
> * 刪除源區域中的資源。

## <a name="prerequisites"></a>Prerequisites

- 確保目的地區域支援您的帳戶使用的服務和功能。

- 對於預覽功能，請確保訂閱已為目的地區域列入白名單。

<a id="prepare" />

## <a name="prepare"></a>準備

要開始，請匯出，然後修改資源管理器範本。 

### <a name="export-a-template"></a>匯出範本

此範本包含描述存儲帳戶的設置。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

要使用 Azure 門戶匯出範本，請使用以下功能：

1. 登錄到 Azure[門戶](https://portal.azure.com)。

2. 選擇**所有資源**，然後選擇您的存儲帳戶。

3. 選擇>**設置** > **匯出範本**。

4. 在 **"匯出範本"** 邊欄選項卡中選擇 **"下載**"。

5. 找到從門戶下載的 .ZIP 檔案，然後解壓縮該檔到您選擇的資料夾。

   此 ZIP 檔案包含 .json 檔，這些檔包含用於部署範本的範本和腳本。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要使用 PowerShell 匯出範本：：

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登錄到 Azure 訂閱，並按照螢幕上的說明操作：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 如果標識與多個訂閱關聯，則將活動訂閱設置為要移動的存儲帳戶的訂閱。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 匯出源存儲帳戶的範本。 這些命令將 json 範本保存到目前的目錄。

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>修改範本 

通過更改存儲帳戶名稱和地區來修改範本。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

要使用 Azure 門戶部署範本，請使用以下功能：

1. 在 Azure 門戶中，選擇 **"創建資源**"。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選擇**範本部署**。

    ![Azure Resource Manager 範本程式庫](./media/storage-account-move/azure-resource-manager-template-library.png)

4. 選取 [建立]****。

5. 選取 [在編輯器中組建您自己的範本]****。

6. 選擇 **"載入檔**"，然後按照說明載入您在最後一節中下載的**template.json**檔。

7. 在**範本.json**檔中，通過設置存儲帳戶名稱的預設值來命名目標存儲帳戶。 本示例將存儲帳戶名稱的預設值設置到`mytargetaccount`。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    要獲取區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空格的區功能變數名稱稱，**美國** = **中部中心**。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要使用 PowerShell 部署範本，請使用：

1. 在**範本.json**檔中，通過設置存儲帳戶名稱的預設值來命名目標存儲帳戶。 本示例將存儲帳戶名稱的預設值設置到`mytargetaccount`。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. 將**範本.json**檔中**的位置**屬性編輯到目的地區域。 本示例將目標地區設定為`eastus`。

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    您可以通過運行[獲取-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)命令來獲取區功能變數代碼。

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>移動

部署範本以在目的地區域中創建新存儲帳戶。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 保存**範本.json**檔。

2. 輸入或選擇屬性值：

- **訂閱**：選擇 Azure 訂閱。

- **資源群組**：選取 [新建]**** 並指定資源群組名稱。

- **位置**：選擇 Azure 位置。

3. 按一下"**我同意上述條款及條件**"核取方塊，然後按一下 **"選擇購買**"按鈕。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

1. 獲取要使用[獲取 Az 訂閱](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)部署目標公共 IP 的訂閱 ID ：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 使用這些命令部署範本：

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>配置新的存儲帳戶

某些功能不會匯出到範本，因此您必須將它們添加到新的存儲帳戶。 

下表列出了這些功能以及將其添加到新存儲帳戶的指導。

| 功能    | 指引    |
|--------|-----------|
| **生命週期管理原則** | [管理 Azure Blob 儲存體生命週期](../blobs/storage-lifecycle-management-concepts.md) |
| **靜態網站** | [在 Azure 存儲中託管靜態網站](../blobs/storage-blob-static-website-how-to.md) |
| **事件訂閱** | [回應 Blob 儲存體事件](../blobs/storage-blob-event-overview.md) |
| **警示** | [使用 Azure 監視器創建、查看和管理活動日誌警報](../../azure-monitor/platform/alerts-activity-log.md) |
| **內容傳遞網路 (CDN)** | [使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 如果為源存儲帳戶設置了 CDN，只需將現有 CDN 的源更改為新帳戶的主 Blob 服務終結點（或主靜態網站終結點）。 

### <a name="move-data-to-the-new-storage-account"></a>將資料移動到新的存儲帳戶

以下是一些將資料移過來的方法。

：heavy_check_mark：Azure**存儲資源管理器**

  它便於使用，適用于小型資料集。 您可以複製容器和檔共用，然後將其粘貼到目標帳戶中。

  請參閱[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)。

：heavy_check_mark：**阿茲比**

  這是首選方法。 它針對性能進行了優化。  速度更快的一種方法是資料直接在存儲伺服器之間複製，因此 AzCopy 不使用電腦的網路頻寬。 在命令列或作為自訂腳本的一部分使用 AzCopy。

  請參閱[使用 AzCopy 入門](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

：heavy_check_mark：Azure**資料工廠** 

  僅當需要當前版本的 AzCopy 不支援的功能時，才使用此工具。 例如，在 AzCopy 的當前版本中，不能在具有階層命名空間的帳戶之間複製 Blob。 此外，AzCopy 不會保留檔存取控制清單或檔時間戳記（例如：創建和修改時間戳記）。 

  請參閱以下連結：
  - [使用 Azure 資料工廠將資料複製到或從 Azure Blob 存儲中複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [使用 Azure Data Factory 從 Azure File Storage 複製資料，或將資料複製到 Azure File Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [使用 Azure Data Factory 將資料複製到 Azure 資料表儲存體或從該處複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>丟棄或清理

部署後，如果要重新開始，可以刪除目標存儲帳戶，並重複本文的["準備](#prepare)和[移動](#move)"部分中描述的步驟。

要提交更改並完成存儲帳戶的移動，請刪除源存儲帳戶。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

要使用 Azure 門戶刪除存儲帳戶，請執行以下設置：

1. 在 Azure 門戶中，展開左側的功能表以打開服務功能表，然後選擇 **"存儲帳戶**"以顯示存儲帳戶的清單。

2. 找到要刪除的目標存儲帳戶，然後按右鍵清單右側的 **"更多**" 按鈕 （**...）。**

3. 選擇 **"刪除**"並確認。

# <a name="powershell"></a>[電源外殼](#tab/azure-powershell)

要刪除資源組及其關聯資源（包括新的存儲帳戶），請使用["刪除-AzStorageAccount"](/powershell/module/az.resources/remove-azstorageaccount)命令：

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>後續步驟

在本教程中，您將 Azure 存儲帳戶從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災害復原之間移動資源的詳細資訊，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移至其他區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
