---
title: 使用 CLI 部署 Azure 位置 Vm
description: 瞭解如何使用 CLI 來部署 Azure 現成的 Vm，以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 71e0f2e87fc71deef0bdc4dd48425cdc9dd99dc8
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200765"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>使用 Azure CLI 部署現成的 Vm

使用 [Azure](../spot-vms.md) 現成的 vm 可讓您以大幅節省的成本來利用未使用的容量。 Azure 基礎結構會在任何時間點恢復容量，以找出 Vm。 因此，找出 Vm 很適合用來處理中斷的工作負載，例如批次處理作業、開發/測試環境、大型計算工作負載等。

現成 Vm 的定價是根據區域和 SKU 的變數。 如需詳細資訊，請參閱 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 

您可以選擇針對 VM 設定您願意支付的最高價格（每小時）。 您可以使用最多5個小數位數，將位置 VM 的最大價格設定為美元 (USD) 。 例如，值的 `0.98765` 最大價格為每小時 $0.98765 美元。 如果您將最大價格設定為 `-1` ，將不會根據價格來收回 VM。 如果有可用的容量和配額，則 VM 的價格將是標準 VM 的目前價格或價格的價格。 如需設定最大價格的詳細資訊，請參閱 [找出 vm-定價](../spot-vms.md#pricing)。

使用 Azure CLI 建立具有位置的 VM 的程式，與 [快速入門文章](./quick-create-cli.md)中所述的步驟相同。 只需新增 '--priority 點 ' 參數、將設定 `--eviction-policy` 為 [解除配置] (這是預設) 或 `Delete` ，並且提供最大價格或 `-1` 。 


## <a name="install-azure-cli"></a>安裝 Azure CLI

若要建立現成的 Vm，您必須執行 Azure CLI 2.0.74 版版或更新版本。 執行 **az --version** 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>建立點 VM

此範例說明如何部署將不會根據價格收回的 Linux 點 VM。 收回原則設定為解除配置 VM，以便稍後可以重新開機。 如果您想要在 VM 收回時刪除 VM 和基礎磁片，請將設定 `--eviction-policy` 為 `Delete` 。

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



建立 VM 之後，您可以查詢以查看資源群組中所有 Vm 的最大計費價格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>模擬收回

您可以 [模擬](/rest/api/compute/virtualmachines/simulateeviction) 點 VM 的收回，以測試您的應用程式將 repond 到突然收回的程度。 

以您的資訊取代下列內容： 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**後續步驟**

您也可以使用 [Azure PowerShell](../windows/spot-powershell.md)、 [入口網站](../spot-portal.md)或 [範本](spot-template.md)來建立點 VM。

使用 [Azure 零售價格 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 查詢目前的定價資訊，以取得有關找出定價的資訊。 `meterName`和 `skuName` 都會包含 `Spot` 。

如果您遇到錯誤，請參閱 [錯誤碼](../error-codes-spot.md)。