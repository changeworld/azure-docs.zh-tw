---
title: 使用現有的 Azure Load Balancer Azure CLI 設定虛擬機器擴展集
description: 瞭解如何使用 Azure CLI 來設定具有現有 Azure Load Balancer 的虛擬機器擴展集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518204"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>使用 Azure CLI 來設定現有 Azure Load Balancer 的虛擬機器擴展集

在本文中，您將瞭解如何使用現有的 Azure Load Balancer 來設定虛擬機器擴展集。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件 

- 您需要在將部署虛擬機器擴展集的訂用帳戶中有現有的標準 sku 負載平衡器。

- 您需要虛擬機器擴展集的 Azure 虛擬網路。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 的版本2.0.28 版或更新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有的負載平衡器部署虛擬機器擴展集

將括弧中的值取代為您設定中的資源名稱。

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

下列範例會使用下列方式部署虛擬機器擴展集：

- 名為 **>myvmss** 的虛擬機器擴展集
- Azure Load Balancer 名為 **myLoadBalancer**
- 名為 **myBackendPool** 的負載平衡器後端集區
- 名為 **myVnet** 的 Azure 虛擬網路
- 名為 **>mysubnet** 的子網
- 名為 **myResourceGroup** 的資源群組
- 虛擬機器擴展集的 Ubuntu Server 映射

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> 建立擴展集之後，就無法針對負載平衡器健康情況探查所使用的負載平衡規則，修改後端埠。 若要變更通訊埠，您可以藉由更新 Azure 虛擬機器擴展集來移除健康情況探查、更新埠，然後再次設定健康情況探查。

## <a name="next-steps"></a>後續步驟

在本文中，您已部署具有現有 Azure Load Balancer 的虛擬機器擴展集。  若要深入瞭解虛擬機器擴展集與負載平衡器，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)
                                