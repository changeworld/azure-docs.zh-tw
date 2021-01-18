---
title: 快速入門：使用 Azure PowerShell 建立共用查詢
description: 在本快速入門中，您會遵循相關步驟，以使用 Azure PowerShell 建立 Resource Graph 共用查詢。
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d7efc02cad3aaa67c639a319f1a7bb455d6e04b0
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128072"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Resource Graph 共用查詢

本文說明如何使用 [Az. Az.resourcegraph](/powershell/module/az.resourcegraph) PowerShell 模組來建立 Azure Resource Graph 共用查詢。

## <a name="prerequisites"></a>先決條件：

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az.ResourceGraph** PowerShell 模組處於預覽狀態，因此必須使用 `Install-Module` Cmdlet 個別安裝。

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- 如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>建立 Resource Graph 共用查詢

在 `Az.ResourceGraph` PowerShell 模組已新增至您選擇的環境後，現在可以建立 Resource Graph 共用查詢。 共用查詢是 Azure Resource Manager 物件，您可以在 Azure Resource Graph Explorer 中授與權限或執行。 此查詢會摘要列出依 _位置_ 分組的所有資源計數。

1. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)建立資源群組，以儲存 Azure Resource Graph 共用查詢。 此資源群組的名稱為 `resource-graph-queries`，位置為 `westus2`。

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. 使用 `Az.ResourceGraph` PowerShell 模組和 [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) Cmdlet 來建立 Azure Resource Graph 共用查詢：

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. 列出新資源群組中的共用查詢。 [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) Cmdlet 會傳回值陣列。

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. 若只要取得單一共用查詢結果，請使用 `Get-AzResourceGraphQuery` 及其 `Name` 參數。

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>清除資源

如果您想要從 Azure 環境中移除 Resource Graph 共用查詢和資源群組，則可以使用下列命令：

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/cli/azure/group#az_group_delete)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure PowerShell 建立 Resource Graph 共用查詢。 若要深入了解 Resource Graph 語言，請繼續前往查詢語言詳細資料頁面。

> [!div class="nextstepaction"]
> [取得有關查詢語言的詳細資訊](./concepts/query-language.md)