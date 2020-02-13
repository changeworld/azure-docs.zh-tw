---
title: 使用 CLI 部署 Azure 位置 Vm （預覽）
description: 瞭解如何使用 CLI 來部署 Azure 點 Vm，以節省成本。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163087"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>預覽：使用 Azure CLI 部署點 Vm

使用[Azure 點 vm](spot-vms.md)可讓您以可觀的成本節約，利用我們未使用的容量。 Azure 基礎結構會在任何時間點回復，以找出虛擬機器的功能。 因此，針對可處理中斷的工作負載（例如批次處理作業、開發/測試環境、大型計算工作負載等），找出 Vm 很棒。

點 Vm 的定價是以區域和 SKU 為依據的變數。 如需詳細資訊，請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定價。 

您可以選擇為 VM 設定您願意支付的最大價格（每小時）。 您可以使用最多5個小數位數，以美元（USD）來設定點 VM 的最大價格。 例如，`0.98765`的值是每小時 $0.98765 美元的最大價格。 如果您將最大價格設定為 `-1`，將不會根據價格來收回 VM。 VM 的價格將會是標準 VM 的目前價格或價格（這是較少的），只要有可用的容量和配額。 如需設定最大價格的詳細資訊，請參閱[找出 vm-定價](spot-vms.md#pricing)。

使用 Azure CLI 來建立具有位置之 VM 的程式，與[快速入門文章](/azure/virtual-machines/linux/quick-create-cli)中所述的步驟相同。 只要新增 [--priority 位置] 參數，並提供最大價格或 `-1`。

> [!IMPORTANT]
> 點實例目前處於公開預覽狀態。
> 此預覽版本不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>



## <a name="install-azure-cli"></a>安裝 Azure CLI

若要建立點 Vm，您必須執行 Azure CLI 版2.0.74 或更新版本。 執行 **az --version** 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登入 Azure。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>建立點 VM

此範例示範如何部署不會根據價格來收回的 Linux 點 VM。 

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

建立 VM 之後，您可以查詢以查看資源群組中所有 Vm 的最大計費價格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**後續步驟**

您也可以使用[Azure PowerShell](../windows/spot-powershell.md)或[範本](spot-template.md)來建立點 VM。

如果您遇到錯誤，請參閱[錯誤碼](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
