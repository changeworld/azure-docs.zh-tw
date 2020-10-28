---
title: 使用 Azure Resource Manager 範本部署 Azure Cache for Redis
description: 瞭解如何使用 Azure Resource Manager 範本 (ARM 範本) 部署 Azure Cache for Redis 資源。 針對常見案例提供範本。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735974"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>快速入門：使用 ARM 範本建立 Azure Cache for Redis

瞭解如何建立 Azure Resource Manager 範本 (ARM 範本) ，以部署 Azure Cache for Redis。 快取可以搭配現有的儲存體帳戶以保留診斷資料。 您將學習如何定義要部署哪些資源，以及如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。 目前對於訂用帳戶，同一區域中所有快取的診斷設定是共用的。 更新區域中的一個快取將會影響區域中的所有其他快取。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶** ：如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。
* **儲存體帳戶** ：若要建立一個帳戶，請參閱 [建立 Azure 儲存體帳戶](../storage/common/storage-account-create.md?tabs=azure-portal)。 儲存體帳戶會用於診斷資料。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-redis-cache/)。

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

範本中會定義下列資源：

* [Microsoft. Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

您也可以 [使用新進階層的 Resource Manager](cache-overview.md#service-tiers) 範本。

* [建立具有叢集的進階 Azure Cache for Redis](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [建立具有資料持續性的進階 Azure Cache for Redis](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [建立部署到虛擬網路中的 Premium Redis 快取](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

若要檢查最新的範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/) 並搜尋 _Azure Cache for Redis_ 。

## <a name="deploy-the-template"></a>部署範本

1. 選取以下影像來登入 Azure 並開啟範本。

    [![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. 選取或輸入下列值：

    * **訂用帳戶** ：選取用來建立資料共用和其他資源的 Azure 訂用帳戶。
    * **資源群組** ：選取 [新建] 以建立新資源群組或選取現有的資源群組。
    * **位置** ：選取資源群組的位置。 儲存體帳戶和 Redis 快取必須位於相同的區域中。 根據預設，Redis 快取會使用與資源群組相同的位置。 因此，請指定與儲存體帳戶相同的位置。
    * **Redis Cache name** ：輸入 Redis 快取的名稱。
    * **現有的診斷儲存體帳戶** ：輸入儲存體帳戶的資源識別碼。 語法是 `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`。

    其餘設定請使用預設值。
1. 選取 [我同意上方所述的條款及條件]，然後選取 [購買]。

## <a name="review-deployed-resources"></a>檢閱已部署的資源

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟您所建立的 Redis 快取。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組，這會刪除資源群組中的資源。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將學會如何建立部署 Azure Cache for Redis Azure Resource Manager 範本。 若要瞭解如何建立 Azure Resource Manager 範本，以使用 Azure Cache for Redis 部署 Azure Web 應用程式，請參閱 [使用範本建立 Web 應用程式和 Azure Cache for Redis](./cache-web-app-arm-with-redis-cache-provision.md)。
