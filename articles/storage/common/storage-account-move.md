---
title: 將 Azure 儲存體帳戶移至另一個區域 |Microsoft Docs
description: 說明如何將 Azure 儲存體帳戶移至另一個區域。
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 34f1c96d8336447b6ca2a4f55fefa9a061c38fa2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198495"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>將 Azure 儲存體帳戶移至另一個區域

若要移動儲存體帳戶，請在另一個區域中建立儲存體帳戶的複本。 然後，使用 AzCopy 或您選擇的其他工具，將您的資料移至該帳戶。

在本文中，您將學會如何：

> [!div class="checklist"]
> 
> * 匯出範本。
> * 藉由新增目的地區域和儲存體帳戶名稱來修改範本。
> * 部署範本以建立新的儲存體帳戶。
> * 設定新的儲存體帳戶。
> * 將資料移至新的儲存體帳戶。
> * 刪除來源區域中的資源。

## <a name="prerequisites"></a>先決條件

- 確定目的地區域中支援您的帳戶所使用的服務和功能。

- 針對預覽功能，請確定您的訂用帳戶已列入目的地區域的允許清單中。

<a id="prepare" />

## <a name="prepare"></a>準備

若要開始使用，請匯出，然後修改 Resource Manager 範本。 

### <a name="export-a-template"></a>匯出範本

此範本包含描述儲存體帳戶的設定。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站匯出範本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [**所有資源**]，然後選取您的儲存體帳戶。

3. 選取 >**設定**] [  >  **匯出範本**]。

4. 在 [**匯出範本**] 分頁中，選擇 [**下載**]。

5. 找出您從入口網站下載的 .zip 檔案，並將該檔案解壓縮至您選擇的資料夾。

   此 zip 檔案包含組成範本的 json 檔案，以及用來部署範本的腳本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 匯出範本：

1. 使用[disconnect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)命令登入您的 Azure 訂用帳戶，並遵循畫面上的指示：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 如果您的身分識別與多個訂用帳戶相關聯，請將您的使用中訂用帳戶設定為您要移動之儲存體帳戶的訂用帳戶。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 匯出來源儲存體帳戶的範本。 這些命令會將 json 範本儲存到您目前的目錄。

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

藉由變更儲存體帳戶名稱和區域來修改範本。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站部署範本：

1. 在 [Azure 入口網站中，選取 [**建立資源**]。

2. 在 [搜尋 Marketplace]**** 中，輸入**範本部署**，然後按 **ENTER**。

3. 選取 [**範本部署**]。

    ![Azure Resource Manager 範本程式庫](./media/storage-account-move/azure-resource-manager-template-library.png)

4. 選取 [建立]  。

5. 選取 [在編輯器中組建您自己的範本]****。

6. 選取 [**載入**檔案]，然後依照指示載入您在上一節中下載的**範本. json**檔案。

7. 在**範本. json**檔案中，設定儲存體帳戶名稱的預設值，以命名目標儲存體帳戶。 這個範例會將儲存體帳戶名稱的預設值設定為 `mytargetaccount` 。
    
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
    若要取得區域位置代碼，請參閱[Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)。  區域的程式碼是不含空格、**美國中部**  =  **centralus**的區功能變數名稱稱。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 部署範本：

1. 在**範本. json**檔案中，設定儲存體帳戶名稱的預設值，以命名目標儲存體帳戶。 這個範例會將儲存體帳戶名稱的預設值設定為 `mytargetaccount` 。
    
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

2. 將**範本. json**檔案中的**location**屬性編輯至目的地區域。 這個範例會將目的地區域設定為 `eastus` 。

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    您可以藉由執行[get-azlocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)命令取得區域碼。

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>移動

部署範本，以在目的地區域中建立新的儲存體帳戶。 

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 儲存**範本. json**檔案。

2. 輸入或選取屬性值：

- 訂用帳戶  ：選取 Azure 訂用帳戶。

- **資源群組**：選取 [新建]**** 並指定資源群組名稱。

- **位置**：選取 Azure 位置。

3. 按一下 [**我同意上方所述的條款及條件**] 核取方塊，然後按一下 [**選取購買**] 按鈕。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 取得您想要在其中部署目標公用 IP 與[get-azsubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)的訂用帳戶識別碼：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. 使用下列命令來部署您的範本：

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>設定新的儲存體帳戶

有些功能不會匯出至範本，因此您必須將它們新增至新的儲存體帳戶。 

下表列出這些功能，以及將它們新增至新儲存體帳戶的指導方針。

| 功能    | 指導方針    |
|--------|-----------|
| **生命週期管理原則** | [管理 Azure Blob 儲存體生命週期](../blobs/storage-lifecycle-management-concepts.md) |
| **靜態網站** | [在 Azure 儲存體中裝載靜態網站](../blobs/storage-blob-static-website-how-to.md) |
| **事件訂閱** | [回應 Blob 儲存體事件](../blobs/storage-blob-event-overview.md) |
| **警示** | [使用 Azure 監視器來建立、查看和管理活動記錄警示](../../azure-monitor/platform/alerts-activity-log.md) |
| **內容傳遞網路 (CDN)** | [使用 Azure CDN 透過 HTTPS 以自訂網域存取 Blob](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 如果您為來源儲存體帳戶設定 CDN，只要將現有 CDN 的來源變更為新帳戶的主要 blob 服務端點（或主要靜態網站端點）即可。 

### <a name="move-data-to-the-new-storage-account"></a>將資料移至新的儲存體帳戶

AzCopy 是將資料移到的慣用工具。 它已針對效能進行優化。  其中一個較快的方法是，資料會直接複製到存放伺服器之間，因此 AzCopy 不會使用您電腦的網路頻寬。 在命令列或自訂腳本中使用 AzCopy。 請參閱[開始使用 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

您也可以使用 Azure Data Factory 來移動您的資料。 它提供了直覺的使用者介面。 若要使用 Azure Data Factory，請參閱下列任何連結：。 

  - [使用 Azure Data Factory 在 Azure Blob 儲存體之間複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 來回複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [使用 Azure Data Factory 從 Azure File Storage 複製資料，或將資料複製到 Azure File Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [使用 Azure Data Factory 將資料複製到 Azure 資料表儲存體或從該處複製資料](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>捨棄或清除

部署之後，如果您想要從頭開始，您可以刪除目標儲存體帳戶，並重複本文的[準備](#prepare)和[移動](#move)章節中所述的步驟。

若要認可變更並完成儲存體帳戶的移動，請刪除來源儲存體帳戶。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站移除儲存體帳戶：

1. 在 [Azure 入口網站中，展開左側的功能表以開啟服務的功能表，然後選擇 [**儲存體帳戶**] 以顯示您的儲存體帳戶清單。

2. 找出要刪除的目標儲存體帳戶，然後以滑鼠右鍵按一下清單右側的 [**更多**] 按鈕（**...**）。

3. 選取 [**刪除**]，然後確認。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要移除資源群組及其相關聯的資源，包括新的儲存體帳戶，請使用[new-azstorageaccount](/powershell/module/az.storage/remove-azstorageaccount)命令：

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure 儲存體帳戶從一個區域移至另一個區域，並清除來源資源。  若要深入瞭解如何在 Azure 中的區域和嚴重損壞修復之間移動資源，請參閱：


- [將資源移至新的資源群組或訂用帳戶](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [將 Azure VM 移動到另一個區域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
