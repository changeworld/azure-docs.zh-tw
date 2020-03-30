---
title: 使用現有 Azure 負載等化器配置虛擬機器規模集 - Azure CLI
description: 瞭解如何使用現有 Azure 負載等化器配置虛擬機器規模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349741"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>使用 Azure CLI 配置具有現有 Azure 負載等化器的虛擬機器規模集

在本文中，您將瞭解如何使用現有的 Azure 負載等化器配置虛擬機器規模集。 

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。
- 將部署虛擬機器規模集的訂閱中的現有標準 sKU 負載等化器。
- 虛擬機器縮放集的 Azure 虛擬網路。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果選擇在本地使用 CLI，則本文要求您安裝 Azure CLI 版本 2.0.28 或更高版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure-cli"></a>登入 Azure CLI

登錄到 Azure。

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用現有負載等化器部署虛擬機器規模集

將括弧中的值替換為配置中資源的名稱。

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

下面的示例部署虛擬機器規模集，該集具有：

- 名為**myVMSS 的**虛擬機器規模集
- Azure 負載等化器名為**myLoad 平衡器**
- 負載等化器後端池名為 **"我的後端池**"
- 名為**myVnet 的**Azure 虛擬網路
- 名為 **"我的子網"的子網**
- 名為 **"我的資源組"的資源組**
- 虛擬機器規模集的 Ubuntu 伺服器映射

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
> 創建比例集後，無法為負載等化器的運行狀況探測使用的負載平衡規則修改後端埠。 要更改埠，可以通過更新 Azure 虛擬機器縮放集、更新埠然後再次配置運行狀況探測來刪除運行狀況探測。

## <a name="next-steps"></a>後續步驟

在本文中，您部署了一個虛擬機器縮放集與現有的 Azure 負載等化器。  要瞭解有關虛擬機器縮放集和負載等化器的更多詳細資訊，請參閱：

- [什麼是 Azure Load Balancer？](load-balancer-overview.md)
- [什麼是虛擬機器擴展集？](../virtual-machine-scale-sets/overview.md)
                                