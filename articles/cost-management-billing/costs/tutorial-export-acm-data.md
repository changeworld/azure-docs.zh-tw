---
title: 教學課程 - 建立及管理 Azure 成本管理的匯出資料
description: 本文示範如何建立及管理匯出的 Azure 成本管理資料，以將其用於外部系統中。
author: bandersmsft
ms.author: banders
ms.date: 12/7/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 32989b4d5c595416f82fc9d3f1cec2eddec1d6ee
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929254"
---
# <a name="tutorial-create-and-manage-exported-data"></a>教學課程：建立和管理匯出的資料

若您已閱讀過成本分析教學課程，您便已對手動下載成本管理資料相當熟悉。 但是，您可以建立週期性工作，每天、每週或每個月自動將您的成本管理資料匯出至 Azure 儲存體。 匯出的資料格式為 CSV，其中包含所有由成本管理收集的資訊。 您接著可以在 Azure 儲存體中，搭配外部系統使用匯出的資料，並與您擁有的自訂資料合併。 您也可以在外部系統 (例如儀表板或其他財務系統) 中使用您的匯出資料。

觀看影片：[如何使用 Azure 成本管理制定匯出至儲存體的排程](https://www.youtube.com/watch?v=rWa_xI1aRzo)，為您的 Azure 成本資料建立匯出至 Azure 儲存體的排程。 若要觀看其他影片，請造訪[成本管理 YouTube 頻道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

本教學課程中的範例會引導您匯出成本管理資料，並驗證該資料已成功匯出。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

## <a name="prerequisites"></a>必要條件
資料匯出適用於各種不同的 Azure 帳戶類型，包括 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客戶和 [Microsoft 客戶合約](get-started-partners.md)客戶。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 針對依使用者和群組的資料匯出，每個訂用帳戶皆支援下列 Azure 權限或範圍。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者 - 可以建立、修改或刪除已排程的訂用帳戶匯出。
- 參與者 - 可以建立、修改或刪除他們自己的排程匯出。 可以修改其他人所建立的排程匯出名稱。
- 讀者 - 可以排程擁有其權限的匯出。

針對 Azure 儲存體帳戶：
- 需要寫入權限，才能變更設定的儲存體帳戶，無論匯出的權限為何。
- 您的 Azure 儲存體帳戶必須針對 Blob 或檔案儲存體進行設定。

如果您有新的訂用帳戶，您無法立即使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。

## <a name="create-a-daily-export"></a>建立每日匯出

### <a name="portal"></a>[入口網站](#tab/azure-portal)

若要建立或檢視資料匯出，或是排程匯出，請在 Azure 入口網站開啟所需的範圍，然後在功能表中選取 [成本分析]。 例如，瀏覽至 [訂用帳戶]、從清單中選取訂用帳戶，然後在功能表中選取 [成本分析]。 在成本分析頁面頂端，選取 [設定]，然後選取 [匯出]。

> [!NOTE]
> - 除了訂用帳戶，您也可以在資源群組、管理群組、部門和註冊上建立匯出。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。
>- 當您在計費帳戶範圍或客戶的租用戶上以合作夥伴身分登入時，您可以將資料匯出至與合作夥伴儲存體帳戶連結的 Azure 儲存體帳戶。 不過，您必須在 CSP 租用戶中擁有有效的訂用帳戶。

1. 選取 [新增]，然後輸入匯出的名稱。
1. 針對 **計量**，請選取：
    - **實際成本 (使用量和購買)** - 選擇匯出標準使用量和購買
    - **分攤成本 (使用量和採購)** - 選取以匯出 Azure 保留等購買的分攤成本
1. 針 **匯出類型**，請選取：
    - **每日匯出當月迄今成本** - 每天提供新的匯出檔案，提供當月迄今成本資訊。 系統會從先前的每日匯出彙總最新資料。
    - **每週匯出過去七天內成本** - 從匯出的開始日期起算，每週匯出過去七天內的成本。
    - **每月匯出上個月的成本** - 相較於您建立匯出的當月，提供您上個月的成本匯出。 在未來，排程會在每個新月份的第五天執行匯出，同時提供前幾個月的成本。
    - **一次性匯出** - 可讓您選擇要匯出至 Azure Blob 儲存體之歷程記錄資料的日期範圍。 從選擇的日期起算，您最多可以匯出 90 天的歷程記錄成本。 這項匯出會立即執行，您的儲存體帳戶在兩個小時內會提供此資訊。
        根據您的匯出類型，選擇開始日期，或選擇 **開始** 和 **結束** 日期。
1. 為您的 Azure 儲存體帳戶指定訂用帳戶，然後選取資源群組或建立新的資源群組。
1. 選取儲存體帳戶名稱，或建立新名稱。
1. 選取位置 (Azure 區域)。
1. 指定您希望匯出檔案移至的儲存體容器和目錄路徑。
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="新增匯出範例" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. 檢閱匯出詳細資料，然後選取 [建立]。

您新增的匯出會出現在匯出清單中。 根據預設會啟用新的匯出。 若您想要停用或刪除已排程的匯出，請選取清單中的任何項目，然後選取 [停用] 或 [刪除]。

剛開始可能會需要12 到 24 個小時，才會開始執行匯出。 但是，資料可能需要更久才會出現在匯出檔案中。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

從準備適用於 Azure CLI 的環境開始：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. 登入之後，若要查看目前的匯出，請使用 [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list) 命令：

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* 除了訂用帳戶，您也可以建立資源群組和管理群組的匯出。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。
   >* 當您在計費帳戶範圍或客戶的租用戶上以合作夥伴身分登入時，您可以將資料匯出至與合作夥伴儲存體帳戶連結的 Azure 儲存體帳戶。 不過，您必須在 CSP 租用戶中擁有有效的訂用帳戶。

1. 建立資源群組，或使用現有的資源群組。 若要建立資源群組，請使用 [az group create](/cli/azure/group#az_group_create) 命令：

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. 建立儲存體帳戶以接收匯出，或使用現有的儲存體帳戶。 若要建立儲存體帳戶，請使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令：

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. 執行 [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) 命令以建立匯出：

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   針對 **--type** 參數，您可以選擇 `ActualCost`、`AmortizedCost` 或 `Usage`。

   此範例會使用 `MonthToDate`。 匯出會針對您的當月至今成本，每天建立一個匯出檔案。 系統會從先前的本月每日匯出彙總最新資料。

1. 若要查看匯出作業的詳細資料，請使用 [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show) 命令：

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. 使用 [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update) 命令來更新匯出：

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   此範例會變更輸出目錄。

>[!NOTE]
>剛開始可能會需要12 到 24 個小時，才會開始執行匯出。 但是，資料可能需要更久才會出現在匯出檔案中。

您可以使用 [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete) 命令來刪除匯出：

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

從準備適用於 Azure PowerShell 的環境開始：

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

* > [!IMPORTANT]
  > **Az.CostManagement** PowerShell 模組處於預覽狀態，因此您必須使用 `Install-Module` Cmdlet 將其分開安裝。 此 PowerShell 模組正式推出後，便會成為未來 Az PowerShell 模組版本的一部分，且預設可從 Azure Cloud Shell 內使用。

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. 登入之後，若要查看目前的匯出，請使用 [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport) Cmdlet：

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* 除了訂用帳戶，您也可以建立資源群組和管理群組的匯出。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。
   >* 當您在計費帳戶範圍或客戶的租用戶上以合作夥伴身分登入時，您可以將資料匯出至與合作夥伴儲存體帳戶連結的 Azure 儲存體帳戶。 不過，您必須在 CSP 租用戶中擁有有效的訂用帳戶。

1. 建立資源群組，或使用現有的資源群組。 若要建立資源群組，請使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet：

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. 建立儲存體帳戶以接收匯出，或使用現有的儲存體帳戶。 若要建立儲存體帳戶，請使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) Cmdlet：

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. 執行 [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) Cmdlet 來建立匯出：

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   針對 **DefinitionType** 參數，您可以選擇 `ActualCost`、`AmortizedCost` 或 `Usage`。

   此範例會使用 `MonthToDate`。 匯出會針對您的當月至今成本，每天建立一個匯出檔案。 系統會從先前的本月每日匯出彙總最新資料。

1. 若要查看匯出作業的詳細資料，請使用 `Get-AzCostManagementExport` Cmdlet：

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. 使用 [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) Cmdlet 來更新匯出：

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   此範例會變更輸出目錄。

>[!NOTE]
>剛開始可能會需要12 到 24 個小時，才會開始執行匯出。 但是，資料可能需要更久才會出現在匯出檔案中。

您可以使用 [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) Cmdlet 來刪除匯出：

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>匯出排程

排程的匯出會受您最初建立匯出時的時間和星期幾所影響。 當您建立匯出的排程時，後續的每次匯出都會以同樣的頻率執行。 例如，頻率設定為每日的當月迄今成本每日匯出，就會在每一天執行匯出。 同樣地，如果是每週匯出，就會在每週已排定的同一天執行匯出。 匯出的確切發生時間無法保證，但匯出的資料可在執行匯出後四小時內取得。

每次匯出都會建立一個新檔案，因此不會覆寫較舊的匯出。

#### <a name="create-an-export-for-multiple-subscriptions"></a>建立多個訂用帳戶的匯出

如果您有 Enterprise 合約，則可以使用管理群組，將訂用帳戶成本資訊彙總在單一容器中。 接著，您可以匯出管理群組的成本管理資料。

不支援其他訂用帳戶類型的管理群組匯出。

1. 如果尚未建立管理群組，請建立一個群組，並將訂用帳戶指派給該群組。
1. 在成本分析中，將範圍設定為您的管理群組，然後選取 [選取此管理群組]。
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="顯示選取此管理群組選項的範例" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. 在範圍建立匯出，以取得管理群組中訂用帳戶的成本管理資料。
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="顯示建立新的匯出選項與管理群組範圍的範例":::

## <a name="verify-that-data-is-collected"></a>驗證已收集資料

您可以輕鬆驗證您的成本管理資料已收集完成，並使用 Azure 儲存體總管檢視匯出的 CSV 檔案。

在匯出清單中，選取儲存體帳戶名稱。 在儲存體帳戶頁面上，選取 [在總管中開啟]。 若您看見確認方塊，請選取 [是] 以在 Azure 儲存體總管中開啟檔案。

![顯示範例資訊和 [在總管中開啟] 連結的儲存體帳戶頁面](./media/tutorial-export-acm-data/storage-account-page.png)

在 [儲存體總管] 中，巡覽至您想要開啟的容器，然後選取對應到目前月份的資料夾。 隨即顯示 CSV 檔案清單。 選取其中一個，然後選取 [開啟]。

![[儲存體總管] 中顯示的範例訊息](./media/tutorial-export-acm-data/storage-explorer.png)

檔案隨即便會使用設為開啟 CSV 檔案副檔名的程式或應用程式開啟。 以下是 Excel 中的範例。

![Excel 中顯示的範例匯出 CSV 資料](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>下載匯出的 CSV 資料檔案

您也可以在 Azure 入口網站中下載匯出的 CSV 檔案。 下列步驟說明如何從成本分析尋找它。

1. 在 [成本分析] 中，選取 [設定]，然後選取 [匯出]。
1. 在匯出清單中，選取用於匯出的儲存體帳戶。
1. 在您的儲存體帳戶中，選取 [容器]。
1. 在容器清單中，選取容器。
1. 瀏覽目錄和儲存體 Blob，前往您想要的日期。
1. 選取 CSV 檔案，然後選取 [下載]。

[![匯出下載範例](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>檢視匯出執行歷程記錄

您可以在匯出清單頁面中選取個別的匯出，以檢視排程匯出的執行歷程記錄。 匯出清單頁面也可讓您快速存取，以檢視先前匯出的執行時間，以及下一次執行匯出的時間。 以下是顯示執行歷程記錄的範例。

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="顯示 [匯出] 窗格的螢幕擷取畫面。":::

選取匯出以檢視其執行歷程記錄。

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="顯示匯出執行歷程記錄的螢幕擷取畫面。":::

## <a name="access-exported-data-from-other-systems"></a>從其他系統存取匯出資料

匯出成本管理資料的其中一個目的是從外部系統存取資料。 您可能會使用儀表板系統或其他財務系統。 這類系統之間的差異相當大，因此顯示範例並不實用。  但是，您可以從 [Azure 儲存體簡介](../../storage/common/storage-introduction.md)中應用程式存取您的資料開始。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

前進到下一個教學課程，透過識別閒置及使用量過低的資源來最佳化和改善效率。

> [!div class="nextstepaction"]
> [檢閱並針對建議採取動作](tutorial-acm-opt-recommendations.md)
