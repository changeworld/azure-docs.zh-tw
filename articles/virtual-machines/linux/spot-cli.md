---
title: 使用 CLI 部署 Azure Spot VM
description: 瞭解如何使用 CLI 部署 Azure Spot VM 以節省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5b6a07bfbcf56f3ca78fa4991e7741a3d44c25b9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544347"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>使用 Azure CLI 部署 Spot VM

使用[Azure Spot VM](spot-vms.md)使您能夠利用我們未使用的容量,從而顯著節省成本。 在 Azure 需要返回容量的任何時間點,Azure 基礎結構將驅逐 Spot VM。 因此,Spot VM 非常適合處理批次處理作業、開發/測試環境、大型計算工作負載等中斷的工作負載。

現貨 VM 的定價基於區域和 SKU 是可變的。 有關詳細資訊,請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 

您可以選擇為 VM 設置您願意每小時支付的最高價格。 Spot VM 的最高價格可以用美元 (USD) 設置,最多使用 5 個小數位。 例如,該值`0.98765`將是每小時 0.98765 美元的最高價格。 如果將最高價格設置為`-1`,則 VM 不會根據價格被逐出。 VM 的價格將是 Spot 的當前價格或標準 VM 的價格,只要容量和配額可用,標準 VM 的價格就更少了。 有關設置最高價格的詳細資訊,請參閱[現貨 VM - 定價](spot-vms.md#pricing)。

使用 Azure CLI 使用 Spot 創建 VM 的過程與[快速入門文章中](/azure/virtual-machines/linux/quick-create-cli)詳述的過程相同。 只需新增「-優先順序點」 參數並提供最高價格`-1`或 。


## <a name="install-azure-cli"></a>安裝 Azure CLI

要創建 Spot VM,需要運行 Azure CLI 版本 2.0.74 或更高版本。 執行 **az --version** 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>建立 Spot VM

此示例演示如何部署不會根據價格逐出的 Linux Spot VM。 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

創建 VM 後,可以查詢以查看資源組中所有 VM 的最大計費價格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**後續步驟**

您還可以使用[Azure PowerShell](../windows/spot-powershell.md)或[範本](spot-template.md)創建 Spot VM。

如果遇到錯誤,請參閱[錯誤代碼](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
