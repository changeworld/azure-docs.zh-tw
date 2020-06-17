---
title: 快速入門 - 使用 PowerShell 建立 Azure Databricks 工作區
description: 此快速入門說明如何使用 PowerShell 來建立 Azure Databricks 工作區。
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485685"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>快速入門：使用 PowerShell 建立 Azure Databricks 工作區

此快速入門說明如何使用 PowerShell 來建立 Azure Databricks 工作區。 您可以使用 PowerShell，以互動方式或在指令碼中建立和管理 Azure 資源。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 雖然 Az.Databricks PowerShell 模組處於預覽狀態，但您仍必須使用下列命令，將其與 Az PowerShell 模組分開安裝：`Install-Module -Name Az.Databricks -AllowPrerelease`。
> 在正式推出 Az.Databricks PowerShell 模組後，其會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式提供。

如果這是您第一次使用 Azure Databricks，您必須註冊 **Microsoft.Databricks** 資源提供者。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶識別碼。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會在**美國西部 2** 區域建立名為 **myresourcegroup** 的資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>建立 Azure Databricks 工作區

在此節中，您會使用 PowerShell 建立 Azure Databricks 工作區。

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

提供下列值：

|       **屬性**       |                                                                                **說明**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 名稱                     | 提供您 Databricks 工作區的名稱                                                                                                                                   |
| resourceGroupName        | 指定現有的資源群組名稱                                                                                                                                        |
| Location                 | 選取 [美國西部 2]。 如需其他可用的區域，請參閱[依區域提供的 Azure 服務](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | 指定您是要建立新的受控資源群組，還是使用現有資源群組。                                                                                        |
| SKU                      | 選擇 [標準]、[進階] 或 [試用]。 如需這些價格區間的詳細資訊，請參閱 [Databricks 定價](https://azure.microsoft.com/pricing/details/databricks/) |

工作區建立需要幾分鐘的時間。 此程序完成後，使用者帳戶便會自動新增為工作區中的管理使用者。

當工作區部署失敗時，工作區仍會以失敗狀態建立。 刪除失敗的工作區，並建立可解決部署錯誤的新工作區。 當您刪除失敗的工作區時，也會一併刪除受控資源群組和任何已成功部署的資源。

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>判斷 Databricks 工作區的佈建狀態

若要判斷 Databricks 工作區是否已成功佈建，您可以使用 `Get-AzDatabricksWorkspace` Cmdlet。

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>清除資源

如果其他快速入門或教學課程不需要在本快速入門中建立的資源，您可以執行下列範例加以刪除。

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本快速入門範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

若只要刪除在本快速入門中建立的伺服器，而不刪除資源群組，請使用 `Remove-AzDatabricksWorkspace` Cmdlet。

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 Databricks 中建立 Spark 叢集](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
