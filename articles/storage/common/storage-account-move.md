---
title: 將 Azure 儲存帳戶移動到其他區域 |微軟文件
description: 演示如何將 Azure 儲存帳戶移動到其他區域。
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459027"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>將 Azure 儲存帳戶移到其他區域

要移動存儲帳戶,請在另一個區域創建存儲帳戶的副本。 然後,使用 AzCopy 或您選擇的其他工具將資料移動到該帳戶。

在本文中，您將學會如何：

> [!div class="checklist"]
> 
> * 匯出範本。
> * 通過添加目標區域和存儲帳戶名稱來修改範本。
> * 部署範本以創建新的儲存帳戶。
> * 配置新的存儲帳戶。
> * 將數據移至新的儲存帳戶。
> * 刪除來源區域中的資源。

## <a name="prerequisites"></a>Prerequisites

- 確保目標區域支援您的帳戶使用的服務和功能。

- 對於預覽功能,請確保訂閱已為目標區域列入白名單。

<a id="prepare" />

## <a name="prepare"></a>準備

要開始,請匯出,然後修改資源管理器範本。 

### <a name="export-a-template"></a>匯出範本

此範本包含描述存儲帳戶的設置。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

要使用 Azure 門戶匯出樣本,請使用以下功能:

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選擇**所有資源**,然後選擇您的儲存帳戶。

3. 選擇>**設定** > **匯出樣本**。

4. 在 **「匯出範本」** 邊欄選項卡中選擇 **「下載**」 。

5. 找到從門戶下載的 .zip 檔案,然後解壓縮該檔案到您選擇的資料夾。

   此 zip 檔包含 .json 檔,這些檔包含用於部署範本的範本和文稿。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 匯出樣本::

1. 使用[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入 Azure 訂閱,並按照螢幕上的說明操作:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 如果標識與多個訂閱關聯,則將活動訂閱設置為要移動的存儲帳戶的訂閱。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 匯出源存儲帳戶的範本。 這些命令將 json 範本保存到當前目錄。

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

要使用 Azure 門戶部署樣本,請使用以下功能:

1. 在 Azure 門戶中,選擇 **「創建資源**」。。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選擇**樣本部署**。

    ![Azure Resource Manager 範本程式庫](./media/storage-account-move/azure-resource-manager-template-library.png)

4. 選取 [建立]  。

5. 選取 [在編輯器中組建您自己的範本]****。

6. 選擇 **「載入檔**」,然後按照說明載入您在最後一節中下載的**template.json**檔。

7. 在**樣本.json**檔中,通過設置存儲帳戶名稱的預設值來命名目標存儲帳戶。 這個範例儲存帳號名稱的預設值設定到`mytargetaccount`。
    
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
    要取得區域位置代碼,請參考[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的代碼是沒有空白的區域名稱,**美國** = **中部中心**。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

要使用 PowerShell 部署樣本,請使用:

1. 在**樣本.json**檔中,通過設置存儲帳戶名稱的預設值來命名目標存儲帳戶。 這個範例儲存帳號名稱的預設值設定到`mytargetaccount`。
    
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

2. 將**樣本.json**檔中**的位置**屬性編輯到目標區域。 這個範例將目標區域設定為`eastus`。

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    您可以透過執行[取得 AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)指令來取得區域代碼。

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>移動

部署範本以在目標區域中創建新存儲帳戶。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 保存**範本.json**檔。

2. 輸入或選擇屬性值:

- **訂閱**:選擇 Azure 訂閱。

- **資源群組**：選取 [新建]**** 並指定資源群組名稱。

- **位置**:選擇 Azure 位置。

3. 按下「**我同意上述條款和條件**」複選框,然後按下 **「選擇購買**」按鈕。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 取得要使用[取得 Az 訂閱](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)部署目標公共 IP 的訂閱 ID :

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 使用這些指令部署樣本:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>設定新的儲存帳戶

某些功能不會匯出到範本,因此您必須將它們添加到新的儲存帳戶。 

下表列出了這些功能以及將其添加到新存儲帳戶的指導。

| 功能    | 指引    |
|--------|-----------|
| **生命週期管理原則** | [管理 Azure Blob 儲存體生命週期](../blobs/storage-lifecycle-management-concepts.md) |
| **靜態網站** | [在 Azure 儲存中託管靜態網站](../blobs/storage-blob-static-website-how-to.md) |
| **事件訂閱** | [回應 Blob 儲存體事件](../blobs/storage-blob-event-overview.md) |
| **警示** | [使用 Azure 監視器建立、檢視並管理活動紀錄警示](../../azure-monitor/platform/alerts-activity-log.md) |
| **內容傳遞網路 (CDN)** | [使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 如果為源存儲帳戶設置了 CDN,只需將現有 CDN 的源更改為新帳戶的主 Blob 服務終結點(或主靜態網站終結點)。 

### <a name="move-data-to-the-new-storage-account"></a>將資料移至新的儲存帳戶

以下是一些將數據移過來的方法。

:heavy_check_mark:Azure**儲存資源管理員**

  它易於使用,適用於小型數據集。 您可以複製容器和檔共享,然後將其貼上到目標帳戶中。

  請參考[Azure 資源管理員](https://azure.microsoft.com/features/storage-explorer/)。

:heavy_check_mark:**阿茲比**

  這是首選方法。 它針對性能進行了優化。  速度更快的一種方法是數據直接在存儲伺服器之間複製,因此 AzCopy 不使用計算機的網路頻寬。 在命令列或作為自訂文稿的一部分使用 AzCopy。

  請參考[AzCopy 入門](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark:Azure**數據工廠** 

  僅當需要當前版本的 AzCopy 不支援的功能時,才使用此工具。 例如,在 AzCopy 的當前版本中,不能在具有分層命名空間的帳戶之間複製 Blob。 此外,AzCopy 不會保留檔案存取控制列表或檔時間戳(例如:創建和修改時間戳)。 

  請參考以下連結:
  - [使用 Azure 資料工廠將資料複製到或從 Azure Blob 儲存中複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [使用 Azure Data Factory 從 Azure File Storage 複製資料，或將資料複製到 Azure File Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [使用 Azure Data Factory 將資料複製到 Azure 資料表儲存體或從該處複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>丟棄或清除

部署後,如果要重新開始,可以刪除目標存儲帳戶,並重複本文的[「準備](#prepare)和[移動](#move)」部分中描述的步驟。

要提交更改並完成儲存帳戶的移動,請刪除源存儲帳戶。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

要使用 Azure 門戶刪除儲存帳戶,請執行以下設定:

1. 在 Azure 入口中,展開左側的功能表以開啟服務選單,然後選擇 **「儲存帳戶**」以顯示儲存帳戶的清單。

2. 找到要刪除的目標存儲帳戶,然後右鍵按一下清單右側的 **「更多**」 按鈕 (**...)。**

3. 選擇 **「刪除**」並確認。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

要刪除資源群組及其關聯資源(包括新的儲存帳戶),請使用[「刪除-AzStorageAccount」](/powershell/module/az.storage/remove-azstorageaccount)指令:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>後續步驟

在本教學中,您將 Azure 儲存帳戶從一個區域移動到另一個區域並清理了源資源。  要瞭解有關在 Azure 中在區域和災難恢復之間移動資源的詳細資訊,請參閱:


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
